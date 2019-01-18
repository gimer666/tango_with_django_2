# Модели и базы данных {#chapter-models-databases}
Обычно веб-приложениям требуется серверная часть для хранения динамического содержимого, которое появляется на веб-сайте. Для Rango, нам нужно хранить страницы и категории, а также другую информацию. Чаще всего для этого применяют реляционные базы данных, использующие *язык структурированных запросов (SQL)*. Django, однако, предоставляет удобный способ доступа к данным, хранящимся в базе данных, с помощью [*объектно-реляционного отображения (ORM)*](https://en.wikipedia.org/wiki/Object-relational_mapping). По сути, данные, хранящиеся в таблице базы данных, инкапсулированы с помощью Django *моделей*. Модель - это Python объект, который описывает данные таблицы базы данных. Вместо непосредственной работы с базой через SQL, Django предоставляет методы, которые позволяют вам манипулировать данными через соответствующую модель на языке Python.

В этой главе рассказывается об основах работы с данными, используя Django и его ORM. Вы узнаете, что очень просто добавлять, изменять и удалять данные в базе данных, использующейся в Вашем приложении, и насколько просто передавать данные из базы в браузеры Ваших пользователей.

## Требования приложения Rango {#section-models-databases-requirements}
Прежде чем начать, давайте рассмотрим требования к данным для приложения Rango, которое мы разрабатываем. Полный список требований к приложению был [подробно описан ранее](#overview-er), но чтобы освежить их в памяти, давайте быстро резюмируем требования нашего клиента.

* Rango является по сути *каталогом с веб страницами* - сайтом, содержащим ссылки на другие веб сайты.
* Существует множество различных *категорий с веб страницами* и каждая категория сожержит множество ссылок. [Мы предположили в введении](#overview-er), что это связь один-ко-многим. Смотри [диаграмму сущность-связь ниже](#fig-rango-erd-repeat).
* Категория имеет название, число посещений и количество лайков.
* Страница ссылается на категорию, имеет заголовок, URL и число просмотров.

{id="fig-rango-erd-repeat"}
![Диаграмма сущность-связь для двух основных сущностей Rango.](images/rango-erd.png)

## Сообщаем Django о Вашей базе данных {#section-models-database-telling}
Прежде чем мы сможем создать какие-либо модели, необходимо настроить базу данных в Django. Django автоматически создает переменную `DATABASES` в Вашем модуле `settings.py`, когда Вы начинаете новый проект. Она выглядит примерно следующим образом.

{lang="python",linenos=off}
	DATABASES = {
	    'default': {
	        'ENGINE': 'django.db.backends.sqlite3',
	        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
	    }
	}

Мы можем оставить все как есть для нашего приложения Rango. В качестве базы данных `по умолчанию` выбрана легковесный движок баз данных, [SQLite](https://www.sqlite.org/) (смотри ключ `ENGINE`). Ключ `NAME` для этой базы данных - это путь к файлу базы данных, которым по умолчанию является `db.sqlite3` в корне вашего проекта Django.

> ### Совет для тех, кто использует Git
> Если вы используете Git, у вас может возникнуть желание добавить файл базы данных в репозиторий. Это не очень хорошая идея, потому что, если вы работаете над своим приложением с другими людьми, они могут изменить базу данных, и это вызовет бесконечные конфликты.
>
> Вместо этого добавьте `db.sqlite3` в файл `.gitignore`, чтобы он не добавлялся в репозиторий при `git commit` и `git push`. Вы также можете добавить в `.gitignore` другие файлы, например, `*.pyc` и файлы, генерируемые используемой операционной системой.

> ### Использование других баз данных
> Фреймворк баз данных Django был создан для обслуживания различных баз данных, таких как [PostgresSQL](http://www.postgresql.org/), [MySQL](https://www.mysql.com/) и [Microsoft's SQL Server](https://en.wikipedia.org/wiki/Microsoft_SQL_Server). Для других движков баз данных, существуют другие ключи, например, `USER`, `PASSWORD`, `HOST` и `PORT` позволяющие Вам настроить базу данных в Django.
>
> Хотя в этой книге мы не расскажем как использовать другие движки баз данных, существуют онлайн руководства, в которых показано как это сделать. Хорошей отправной точкой является [официальная документация Django](https://docs.djangoproject.com/en/2.0/ref/databases/#storage-engines).
>
> Обратите внимание, что SQLite достаточно для демонстрации функциональности Django ORM. Когда Ваше приложение станет популярным и будет использоваться тысячами пользователей, Вы можете [перейти на более надёжную базу данных](http://www.sqlite.org/whentouse.html).

## Создание моделей
После настройки Вашей базы данных в `settings.py`, давайте создадим две первые модели данных для приложения Rango. Модели для приложения Django хранятся в соответствующем модуле `models.py`. Это означает, что для Rango модели хранятся в `rango/models.py`.

Для самих моделей мы создадим два класса - по одному классу на модель. Обе должны [наследоваться](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) от базового класса `Model`, `django.db.models.Model`. Два класса на языке Python будут определять модели, представляющие *категории* и *страницы*. Определите модель `Category` и `Page` следующим образом.

{lang="python",linenos=off}
	class Category(models.Model):
	    name = models.CharField(max_length=128, unique=True)
	    
	    def __str__(self):  
	        return self.name
	
	
	class Page(models.Model):
	    category = models.ForeignKey(Category, on_delete=models.CASCADE)
	    title = models.CharField(max_length=128)
	    url = models.URLField()
	    views = models.IntegerField(default=0)
	
	    def __str__(self):  
	        return self.title

> ### Проверьте наличие команд `import`
> В начале модуля `models.py`, должна быть строка `from django.db import models`. Если она отсутствует - добавьте её.

> ### `__str__()` или `__unicode__()`?
> Методы `__str__()` и `__unicode__()` в Python генерируют строковое представление класса (аналогично методу `toString()` в Java).
> В Python 2.x в методе `__str__()` строки представляются в формате ASCII. Если Вы хотите иметь [поддержку Unicode](https://docs.python.org/2/howto/unicode.html), то Вам нужно также реализовать метод `__unicode__()`.
>
> В Python 3.x строки по умолчанию являются Unicode - поэтому вам нужно только реализовать метод `__str __ ()`.

Когда вы определяете модель, вам нужно указать список полей и связанные с ними типы, а также любые обязательные или дополнительные параметры. По умолчанию все модели имеют целочисленное поле с автоматическим приращением, называемое id, которое назначается автоматически и является первичным ключом.

Django предоставляет [большой набор встроенных типов полей](https://docs.djangoproject.com/es/2.0/ref/models/fields/#model-field-types). Некоторые из наиболее часто используемых подробно описаны ниже.

* `CharField`, поле для хранения символьных данных (например, строк). Укажите `max_length`, чтобы определить максимальное количество символов, которое может хранится в поле.
* `URLField`, очень похож на `CharField`, но предназначен для хранения URL ресурсов. Вы также можете указать параметр `max_length`.
* `IntegerField`, хранит целые числа.
* `DateField`, которое хранит Python объект `datetime.date`.

> ### Другие типы полей
> Просмотрите [Django документацию по полям модели](https://docs.djangoproject.com/es/2.0/ref/models/fields/#model-field-types), чтобы получить полный список полей Django, которые Вы можете использовать, а также информацию об обязательных и необязательных параметрах существующие у каждого.

Для каждого поля Вы можете указать атрибут `unique`. Если он равен `True`, то значение поля должно быть уникально в таблице базы данных, которая связана с соответствующей моделью. Например, взгляните на нашу модель `Category`, определенную выше. Поле `name` имеет атрибут `unique`, таким образом название каждой категории должно быть уникальным. Это означает, что Вы можете использовать это поле как первичный ключ.

Вы также можете указать дополнительные атрибуты для каждого поля, например, указать значение по умолчанию - `default='value'`, указать может ли значение поля быть пустым (или [`NULL`](https://en.wikipedia.org/wiki/Nullable_type)) (`null=True`) или нет (`null=False`).

Django предоставляет три типа полей для определения связей между моделями в Вашей базе данных. А именно:

* `ForeignKey`, тип поля, которое позволяет нам создавать [связь один-ко-многим](https://en.wikipedia.org/wiki/One-to-many_(data_model));
* `OneToOneField`, тип поля, которое позволяет нам определять строгую [связь один-к-одному](https://en.wikipedia.org/wiki/One-to-one_(data_model)); and
* `ManyToManyField`, тип поля, которое позволяет нам определить [связь много-ко-многим](https://en.wikipedia.org/wiki/Many-to-many_(data_model)).

Из наших вышеприведенных примеров для моделей, поле `category` в модели `Page` имеет тип `ForeignKey`. Это позволяет нам создавать связь один-комногим с моделью/таблицей `Category`, которая указана в качестве аргумента конструктору поля. При указании внешнего ключа нам также нужно указать Django как обрабатывать ситуации, когда к которой пренадлежит страница удаляется. `ONCASCADE` указывает Django удалять страницы, связанные с категорией, если категория удаляется. Тем не менее существуют другие настройки, которые позволяют Django по-другому обрабатывать эту ситуацию. Смотри [Django документацию по внешним ключам](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.ForeignKey) для получения более подробной информации.

Наконец, хорошей практикой является реализация метода `__str__()`. Если этот метод не реализован, когда вы захотите `вывести` объект, он будет отображаться как `<Category: Category object>`. Эта информация бесполезна при отладке или доступе к объекту - вместо этого приведенный выше код выведет, например, `<Category: Python>` для категории Python. Эта информация также полезна при использовании интерфейса администратора, поскольку Django будет отображать строковое представление объекта.

## Создание и миграция базы данных
После того как наши модели определены в `models.py`, теперь мы можем с помощью магии Django создать таблицы в используемой базе данных. Django предоставляет то, что называется [*инструментом миграций*](https://en.wikipedia.org/wiki/Data_migration) для настройки и обновления базы данных, отражающим любые изменения в Ваших моделях. Например, если вам нужно добавить новое поле, вы можете использовать инструменты миграции для обновления информации в базе данных.

### Настройка базы данных
Прежде всего база данных должна быть *инициализирована*. Это означает создание базы данных и всех связанных с ней таблиц, чтобы затем можно было хранить в них данные. Для этого Вы должны открыть терминал или командную строку и перейти в корневой каталог Вашего проекта, где хранится файл `manage.py`. Выполните следующую команду, но помните, что *вывод может отличаться от того, что показан ниже.*

{lang="text",linenos=off}
	$ python manage.py migrate
	
	Operations to perform:
  		Apply all migrations: admin, auth, contenttypes, sessions
	Running migrations:
		Applying contenttypes.0001_initial... OK
		Applying auth.0001_initial... OK
		Applying admin.0001_initial... OK
		Applying admin.0002_logentry_remove_auto_add... OK
		Applying contenttypes.0002_remove_content_type_name... OK
		Applying auth.0002_alter_permission_name_max_length... OK
		Applying auth.0003_alter_user_email_max_length... OK
		Applying auth.0004_alter_user_username_opts... OK
		Applying auth.0005_alter_user_last_login_null... OK
		Applying auth.0006_require_contenttypes_0002... OK
		Applying auth.0007_alter_validators_add_error_messages... OK
		Applying auth.0008_alter_user_username_max_length... OK
		Applying auth.0009_alter_user_last_name_max_length... OK
		Applying sessions.0001_initial... OK

Все приложения, установленные в вашем Django проекте (просмотрите `INSTALLED_APPS` в `settings.py`) обновят свои представления в базе данных при выполнении этой команды. После выполнения этой команды вы должны увидеть файл `db.sqlite3` в корневом каталоге Django проекта.

Теперь создайте суперпользователя для управления базой данных. Запустите следующую команду.

{lang="text",linenos=off}
	$ python manage.py createsuperuser

Учетная запись суперпользователя будет использоваться для доступа к интерфейсу администратора Django позднее в этой главе. Введите имя пользователя для учетной записи, адрес электронной почты и пароль. После этого скрипт должен успешно завершиться. Обязательно запишите имя пользователя и пароль для вашей учетной записи суперпользователя.

### Создание и обновление моделей/таблиц
Всякий раз когда Вы вносите изменени в модели Вашего приложения, Вам необходимо *зарегистрировать* изменения с помощью команды `makemigrations` в `manage.py`. Если рассматривать в качестве примера приложение `rango`, то нам надо запустить следующую команду из корневого каталога нашего Django проекта.

{lang="text",linenos=off}
	$ python manage.py makemigrations rango
	
	Migrations for 'rango':
		rango/migrations/0001_initial.py
		- Create model Category
		- Create model Page

После выполнения этой команды, перейдите в каталог `rango/migrations` и убедитесь, что был создан скрипт на языке Python. Он называется `0001_initial.py` и содержит всю необходимую информацию для создания схемы базы данных этой конкретной миграции.

> ### Просмотр SQL команд
> Если Вы хотите просмотреть SQL команды, которые Django ORM создало для движка базы данных данной миграции, Вы можете выполнить следующую команду.
>
> {lang="text",linenos=off}
>     $ python manage.py sqlmigrate rango 0001
>
> В этом примере `rango` - это название Вашего приложения, а `0001` - это миграция, SQL код которой Вы хотите просмотреть. Это позволит Вам лучше понять что имено происходит на уровне базы данных, например, какие таблицы создаются. В этих файлах могут создаваться сложные схемы баз данных, в том числе отношение много-ко-многим, для которых создаются дополнительные таблицы.

После того как вы создали миграции для своего приложения, Вам нужно зафиксировать их в базе данных. Сделайте это, выполнив команду `migrate`.

{lang="text",linenos=off}
	$ python manage.py migrate
	
	Operations to perform:
		Apply all migrations: admin, auth, contenttypes, rango, sessions
	Running migrations:
		Applying rango.0001_initial... OK

Такой ответ командной строки подтверждает, что в Вашей базе данных были созданы необходимые таблицы и можно двигаться дальше.

Возможно Вы также заметили, что в нашей модели `Category` в настоящий момент не хватает некоторых полей, которые [были определены в ТЗ для приложения Rango](#section-models-databases-requirements). **Не волнуйтесь, мы добавим их позднее, чтобы напомнить Вам как осуществляется процесс миграций.**

## Django модели и командная оболочка Django
Прежде чем перейти к демонстрации интерфейса администратора, стоит отметить, что Вы можете взаимодействовать с Django моделями из командной оболочки - очень полезный инструмент для отладки. Мы покажем, как создать экземпляр ``Category``, используя его.

Чтобы получить доступ к командной оболочке, необходимо опять вызвать ``manage.py`` из корневого каталога Вашего Django проекта. Выполните следующую команду.

``$ python manage.py shell``

Она запустит экземпляр интерпретатора и загрузит в него настройки Вашего проекта. После этого Вы можете взаимодействовать с моделями, как показано в следующем листинге сессии терминала. Чтобы узнать, что делает каждая команда, прочитайте встроенные комментарии. Обратите внимание, что есть небольшие отличия между тем, что возвращает Django 1.9 и Django 1.10 -- они показаны ниже вместе с комментариями.

{lang="python",linenos=off}

	# Импортируем модель Category из приложения Rango
	>>> from rango.models import Category
	
	# Показать все текущие категории
	>>> print(Category.objects.all())
	# Поскольку не было определено ни одной категории мы получаем пустой QuerySet объект.
	<QuerySet []>  
	
	# Создаем объект новой категории и сохраняем его в базу данных.
	>>> c = Category(name="Test")
	>>> c.save()
	
	# Теперь опять выведем список всех сохраненных объектов категорий
	>>> print(Category.objects.all())
	# Теперь в базе данных сохранена категория под названием 'Test'.
	<QuerySet [<Category: Test>] 
	
	# Выходим из командной оболочки Django.
	>>> quit()

В примере мы сначала импортируем модель, с которой мы хотим работать. Затем мы выводим на экран все существующие категории. Поскольку рассматриваемая таблица `Category` пуста, мы получаем пустой список. Затем мы создаем и сохраняем категорию и опять выводим на экран все категории. Этот второй `print` должен показать только что созданную новую категорию ``Category``. Обратите внимание, что название `Test` появилось во втором `print` - это отработал метод `__str__()`!

> ### Прочитайте официальное пособие
> Вышеприведенный пример очень простой образец тех действий над базой данных, которые Вы можете осуществлять в командной оболочке Django. Если Вы ещё этого не сделали, пора полностью прочитать [вторую часть официального учебного пособия по Django, чтобы узнать больше о взаимодействии с моделями](https://docs.djangoproject.com/en/2.0/intro/tutorial02/). Также ознакомьтесь с [официальной документацией Django, чтобы узнать список доступных команд](https://docs.djangoproject.com/en/2.0/ref/django-admin/#available-commands) для работы с моделями.

## Настраиваем интерфейс администратора
Одной из главных особенностей Django является встроенный веб-интерфейс администрирования (или *администратора*), который позволяет Вам просматривать, редактировать и удалять данные, представленные в виде экземпляров модели (из соответствующих таблиц базы данных). В этом разделе мы настроим интерфейс администратора, чтобы Вы могли работать с двумя моделями Rango, созданными ранее.

Настроить все относительно просто. В модуле `settings.py` Вашего проекта, Вы заметите, что одним из преустановленных приложений (в списке `INSTALLED_APPS`) является `django.contrib.admin`. Кроме того в модуле `urls.py` Вашего проекта есть `url шаблон`, соответствующий `admin/`.

Настроек по умолчанию достаточно для начала работы. Запустите сервер разработки Django как обычно с помощью следующий команды.

{lang="text",linenos=off}
	$ python manage.py runserver

Перейдите в браузере по адресу `http://127.0.0.1:8000/admin/`. Вы увидите интерфейс для входа в систему. Войдите в систему, используя учетные данные, которые вы создали ранее с помощью команды `$ python manage.py createsuperuser`. Затем вы увидите интерфейс [похожий на тот, что показан ниже](#fig-ch5-admin-first).

{id="fig-ch5-admin-first"}
![Интерфейс администратора Django без моделей Rango.](images/ch5-admin-first.png)

Хотя всё выглядит не плохо, нам не хватает моделей `Category` и `Page`, которые были определены для приложения Rango. Чтобы добавить эти модели нам нужно помочь Django.

Для этого откройте файл `rango/admin.py`. Команда `include` для загрузки необходимых моделей уже существует, поэтому измените модуль, *зарегистрировав* в нём каждый класс, который вы хотите добавить. В приведенном ниже примере регистрируются классы `Category` и `Page` в интерфейсе администратора.

{lang="python",linenos=off}
	from django.contrib import admin
	from rango.models import Category, Page
	
	admin.site.register(Category)
	admin.site.register(Page)

Чтобы добавить другие классы, которые могут быть созданы в будущем, просто ещё раз вызовите метод `admin.site.register()`.

После сохранения этих изменений либо перезагрузите страницу интерфейса администратора или перезапустите сервер для разработки Django и снова перейдите по адресу `http://127.0.0.1:8000/admin/`. Теперь Вы увидите модели `Category` и `Page`, [как показано ниже](#fig-ch5-admin-second).

{id="fig-ch5-admin-second"}
![Интерфейс администратора Django с моделями Rango.](images/ch5-admin-second.png)

Попробуйте нажать на ссылку `Categorys` в разделе `Rango`. Там Вы должны увидеть категорию `Test` которую мы создали ранее через командную оболочку Django.

> ### Экспериментируем с интерфейсом администратора
> Вы будете использовать интерфейс администратора для проверки хранения данных при разработке приложения Rango. Поэкспериментируйте с ним и посмотрите, как все это работает. Интерфейс интуитивно понятен и прост.
>
> Удалите категорию `Test`, которая была создана ранее. Мы скоро заполним базу данных тестовыми данными.

> ### Управление учетными записями пользователей
> Интерфейс адинистратора Django - это то место, где Вы можете управлять учетными записями пользователей через раздел Аутентификация и Авторизация. Здесь вы можете создавать, изменять и удалять учетные записи пользователей, а также менять уровни привелегий.

> ### Правильное написание единственного и множественного числа для моделей
>  Обратите внимание на ошибку в названии модели в интерфейсе администратора (`Categorys`, а не `Categories`). Эту ошибку можно исправить, добавив вложенный класс `Meta` в определение Вашей модели с атрибутом `verbose_name_plural`. Просмотрите, например, на модифицированную версию модели `Category`, показанную ниже и [официальную документацию Django по моделям](https://docs.djangoproject.com/en/2.0/topics/db/models/#meta-options) для получения дополнительной информации о том, что может храниться в классе `Meta`.
>
> {lang="python",linenos=off}
> 	class Category(models.Model):
> 	    name = models.CharField(max_length=128, unique=True)
> 	
> 	    class Meta:
> 	        verbose_name_plural = 'Categories'
> 	        
> 	    def __str__(self):
> 	        return self.name

> ### Расширяем `admin.py`
> Следует отметить, что приведенный выше пример настройки модуля ``admin.py`` для Вашего Rango приложения является самым простым рабочим примером из доступных. Вы можете настроить интерфейс администратора различными способами. Для этого ознакомтесь с [официальной документацией Django, посвященной интерфейсу администратора](https://docs.djangoproject.com/en/1.9/ref/contrib/admin/).

## Создание скрипта для заполнения базы данных {#section-models-population}
Ввод тестовых данных в базу данных как правило утомительное занятие. Многие разработчики добавляют некоторые фиктивные тестовые данных, случайно нажимая на клавиши, например `wTFzmN00bz7`. Вместо этого лучше написать скрипт для автоматического заполнения базы данных реалистичными и достоверными данными. Например, когда Вы декомнтрируете или тестируете своё приложение, у Вас будут реалистичные данные в базе данных. Кроме того, если Вы развертываете приложение или делитесь им с соавторами, то Вам/им не придется заново осуществлять процесс ввода тестовых данных. Таким образом, хорошей практикой является создание того, что мы называем *скритпом для заполнения* базы данных.

Чтобы создать скрипт для заполнения базы данных Rango, начнем с создания нового модуля Python в корневом каталоге нашего Django проекта (например, ``<workspace>/tango_with_django_project/``). Создадим файл ``populate_rango.py`` и добавим следующий код.

{lang="python",linenos=on}

	import os
	os.environ.setdefault('DJANGO_SETTINGS_MODULE',
	                      'tango_with_django_project.settings')
	
	import django
	django.setup()
	from rango.models import Category, Page
	
	def populate():
	    # Во-первых, мы создадим списки словарей, содержащих страницы, которые мы хотим добавить в каждую категорию.	    
	    # Затем мы создадим словарь словарей для наших категорий.
	    # Это может показаться немного запутанным, но это позволяет нам перебирать
	    # каждую структуру данных и добавлять данные в наши модели.
	    
	    python_pages = [
	        {"title": "Official Python Tutorial",
	         "url":"http://docs.python.org/2/tutorial/"},
	        {"title":"How to Think like a Computer Scientist",
	         "url":"http://www.greenteapress.com/thinkpython/"},
	        {"title":"Learn Python in 10 Minutes",
	         "url":"http://www.korokithakis.net/tutorials/python/"} ]
	    
	    django_pages = [
	        {"title":"Official Django Tutorial",
	         "url":"https://docs.djangoproject.com/en/1.9/intro/tutorial01/"},
	        {"title":"Django Rocks",
	         "url":"http://www.djangorocks.com/"},
	        {"title":"How to Tango with Django",
	         "url":"http://www.tangowithdjango.com/"} ]
	    
	    other_pages = [
	        {"title":"Bottle",
	         "url":"http://bottlepy.org/docs/dev/"},
	        {"title":"Flask",
	         "url":"http://flask.pocoo.org"} ]
	    
	    cats = {"Python": {"pages": python_pages},
	            "Django": {"pages": django_pages},
	            "Other Frameworks": {"pages": other_pages} }
	    
	    # Если Вы хотите добавить больше категорий или страниц,
	    # включите их в словари выше.
	    
	    # Показанный ниже код перебирает словарь cats, затем добавляет каждую категорию 
	    # и соответствующие страницы для этой категории.	    
	    # Если Вы используете Python 2.x, то используйте cats.iteritems()
	    # смотри http://docs.quantifiedcode.com/python-anti-patterns/readability/
	    # для получения дополнительной информации о том, как правильно перебирать словарь.
	    
	    for cat, cat_data in cats.items():
	        c = add_cat(cat)
	        for p in cat_data["pages"]:
	            add_page(c, p["title"], p["url"])
	    
	    # Print out the categories we have added.
	    for c in Category.objects.all():
	        for p in Page.objects.filter(category=c):
	            print("- {0} - {1}".format(str(c), str(p)))
	
	def add_page(cat, title, url, views=0):
	    p = Page.objects.get_or_create(category=cat, title=title)[0]
	    p.url=url
	    p.views=views
	    p.save()
	    return p
	
	def add_cat(name):
	    c = Category.objects.get_or_create(name=name)[0]
	    c.save()
	    return c
	
	# Код начинает выполняться отсюда!
	if __name__ == '__main__':
	    print("Starting Rango population script...")
	    populate()

> ### Важно понимать этот код!
> **Не нужно просто копировать и вставлять код.** Добавьте код к Вашему новому модулю и затем построчно пройдитесь по нему, чтобы понять что происходит.
> 
> Мы написали пояснения к этому коду ниже, чтобы Вы могли закрепить знаения, поулченные из нашего кода!
>
> Обратите внимание на то, что когда Вы видите номера строк в коде - это означает, что мы приведил листинг всего файла, а не фрагмента. Это также затрудняет простое копирование и вставку кода!

Хотя код кажется большим по существу происходит последовательность вызовов двух небольших функций `add_page()` и `add_cat()`, определенных в конце модуля. Читая код мы видим, что выполнение начинается в *нижней части* модуля - смотри строки 75 и 76. Это связано с тем, что выше мы определяем функции; они не выполнются пока мы не вызовем их. Когда интерпретатор доходит до [`if __name__ == '__main__'`](http://stackoverflow.com/a/419185), мы вызываем функцию `populate()`.

> ### Что означает `__name__ == '__main__'`?
> Трюк с `__name__ == '__main__'` позволяет использовать Python модуль как многократко используемый модуль или отдельный Python скрипт. Под многократно используемым модулем понимается такой, который можно импортировать в другие модули (например, с помощью команды `import`), тогда как отдельный Python скрипт будет выполняться из терминала/командной строки, если ввести `python module.py`.
>
> Таким образом код внутри условного оператора `if __name__ == '__main__'` будет выполняться только тогда, когда модуль запускается как отдельный Python скрипт. При импорте модуля этот код, но тем не менее Вам будут полностью доступны любые классы или функции.

> ### Импортируем модели
> При импорте моделей Django, убедитесь, что Вы импортировали настройки своего проекта, импортировав `django` и определили переменную среды `DJANGO_SETTINGS_MODULE`, присвоив ей значение файла настроек Вашего проекта, как показано в строках 1-6 выше. Затем Вы вызываете ``django.setup()`` для импорта настроек Вашего Django проекта.
>
> Если Вы не выполните этот важный шаг, то **получите исключение при попытке импортировать ваши модели.  Это связано с тем, что необходимая инфраструктура Django еще не инициализирована.** Именно поэтому мы импортируем `Category` и `Page` *после* загрузки настроек в строке 8.

Цикл `for` в строках 51-54 отвечает за многократный вызов функций `add_cat()` и `add_page()`. Эти функции в свою очередь отвечают за создание новых категорий и страниц. `populate()` управляет созданием категорий. Например, ссылка на новую категорию хранится в локальной переменной `c` - смотри строку 52 выше. Она сохраняется поскольку для `Page` требуется ссылка на `Category`. После того как `add_cat()` и `add_page()` вызываются в `populate()`, функция завершается циклическим перебором всех новых объектов `Category` и связанных с ними `Page`, отображая их имена в терминале.

> ### Создаём экземпляры моделей
> Выше в скрипте для заполнения базы мы используем преимущество метода `get_or_create()` при создании моделей. Поскольку мы не хотим создавать дубликаты одной и той же записи, мы можем использовать `get_or_create()`, чтобы проверить существует ли запись в базе данных. Если не существует, метод создаст её. Если существует, то возвращается  If it does, then a reference to the specific model instance is returned.
> 
> This helper method can remove a lot of repetitive code for us. Rather than doing this laborious check ourselves, we can make use of code that does exactly this for us.
>
> The `get_or_create()` method returns a tuple of `(object, created)`. The first element `object` is a reference to the model instance that the `get_or_create()` method creates if the database entry was not found. The entry is created using the parameters you pass to the method - just like `category`, `title`, `url` and `views` in the example above. If the entry already exists in the database, the method simply returns the model instance corresponding to the entry. `created` is a boolean value; `True` is returned if `get_or_create()` had to create a model instance.
>
> This explanation therefore means that the `[0]` at the end of our call to the `get_or_create()` returns the object reference only. Like most other programming language data structures, Python tuples use [zero-based numbering](http://en.wikipedia.org/wiki/Zero-based_numbering).
> 
> You can check out the [official Django documentation](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#get-or-create) for more information on the handy `get_or_create()` method.

When saved, you can then run your new populations script by changing the present working directory in a terminal to the Django project's root. It's then a simple case of executing the command ``$ python populate_rango.py``. You should then see output similar to that shown below -- the order in which categories are added may vary depending upon how your computer is set up.

{lang="text",linenos=off}

	$ python populate_rango.py
	
	Starting Rango population script...
	- Python - Official Python Tutorial
	- Python - How to Think like a Computer Scientist
	- Python - Learn Python in 10 Minutes
	- Django - Official Django Tutorial
	- Django - Django Rocks
	- Django - How to Tango with Django
	- Other Frameworks - Bottle
	- Other Frameworks - Flask

Next, verify that the population script actually populated the database. Restart the Django development server, navigate to the admin interface (at `http://127.0.0.1:8000/admin/`) and check that you have some new categories and pages. Do you see all the pages if you click `Pages`, like in the figure shown below?

{id="fig-admin-populated"}
![The Django admin interface, showing the `Page` model populated with the new population script. Success!](images/ch5-admin-populated.png)

While creating a population script may take time, you will save yourself time in the long run. When deploying your app elsewhere, running the population script after setting everything up means you can start demonstrating your app straight away. You'll also find it very handy when it comes to [unit testing your code](#chapter-testing).

## Workflow: Model Setup {#section-models-databases-workflow}
Now that we've covered the core principles of dealing with Django's ORM, now is a good time to summarise the processes involved in setting everything up. We've split the core tasks into separate sections for you. Check this section out when you need to quickly refresh your mind of the different steps.

### Настраиваем Вашу базу данных
With a new Django project, you should first [tell Django about the database you intend to use](##section-models-database-telling) (i.e. configure `DATABASES` in `settings.py`). You can also register any models in the `admin.py` module of your app to make them accessible via the admin interface.

### Добавление модели
The workflow for adding models can be broken down into five steps.

1. First, create your new model(s) in your Django application's `models.py` file.
2. Update `admin.py` to include and register your new model(s).
3. Perform the migration `$ python manage.py makemigrations <app_name>`.
4. Apply the changes `$ python manage.py migrate`. This will create the necessary infrastructure within the database for your new model(s).
5. Create/edit your population script for your new model(s).

There will be times when you will have to delete your database -- sometimes it's easier to just start afresh. When you want to do this, do the the following. Note that for this tutorial, you are using a SQLite database -- Django does support a [variety of other database engines](https://docs.djangoproject.com/en/2.0/ref/databases/).

1. If you're running it, stop your Django development server.
2. For an SQLite database, delete the `db.sqlite3` file in your Django project's directory. It'll be in the same directory as the `manage.py` file.
3. If you have changed your app's models, you'll want to run the `$ python manage.py makemigrations <app_name>` command, replacing `<app_name>` with the name of your Django app (i.e. `rango`). Skip this if your models have not changed.
4. Run the `$ python manage.py migrate` to create a new database file (if you are running SQLite), and migrate database tables to the database.
5. Create a new admin account with the `$ python manage.py createsuperuser` command.
6. Finally, run your population script again to insert credible test data into your new database.

X> ### Упражнения
X> Now that you've completed this chapter, try out these exercises to reinforce and practice what you have learnt. **Once again, note that the following chapters will have expected you to have completed these exercises! If you're stuck, there are some hints to help you complete the exercises below.**
X>
X> * Update the `Category` model to include the additional attributes `views` and `likes` where the `default` values for each are both zero (`0`).
X> * Make the migrations for your app, and then migrate your database to commit the changes.
X> * Next update your population script so that the `Python` category has `128` views and `64` likes, the `Django` category has `64` views and `32` likes, and the `Other Frameworks` category has `32` views and `16` likes.
X> * Delete and recreate your database, populating it with your updated population script.
X> * Complete parts [two](https://docs.djangoproject.com/en/2.0/intro/tutorial02/) and [seven](https://docs.djangoproject.com/en/2.0/intro/tutorial07/) of the official Django tutorial. These sections will reinforce what you've learnt on handling databases in Django, and show you additional techniques to customising the Django admin interface.
X> * Customise the admin interface. Change it in such a way so that when you view the `Page` model, the table displays the `category`, the `name` of the page and the `url` - just [like in the screenshot shown below](#fig-admin-completed). You will need to complete the previous exercises or at least go through the official Django Tutorial to complete this exercise.


{id="fig-admin-completed"}
![The updated admin interface `Page` view, complete with columns for category and URL.](images/ch5-admin-completed.png)

T> ### Подсказки к упражнениям
T> If you require some help or inspiration to complete these exercises done, here are some hints.
T> 
T> * Modify the `Category` model by adding two `IntegerField`s: `views` and `likes`.
T> * In your population script, you can then modify the `add_cat()` function to take the values of the  `views` and `likes`.
T>      * You'll need to add two parameters to the definition of `add_cat()` so that `views` and `likes` values can be passed to the function, as well as a `name` for the category.
T>      * You can then use these parameters to set the `views` and `likes` fields within the new `Category` model instance you create within the `add_cat()` function. The model instance is assigned to variable `c` in the population script, as defined earlier in this chapter. As an example, you can access the `likes` field using the notation `c.likes`. Don't forget to `save()` the instance!
T>      * You then need to update the `cats` dictionary in the `populate()` function of your population script. Look at the dictionary. Each [key/value pairing](https://www.tutorialspoint.com/python/python_dictionary.htm) represents the *name* of the category as the key, and an additional dictionary containing additional information relating to the category as the *value*. You'll want to modify this dictionary to include `views` and `likes` for each category.
T>      * The final step involves you modifying how you call the `add_cat()` function. You now have three parameters to pass (`name`, `views` and `likes`); your code currently provides only the `name`. You need to add the additional two fields to the function call. If you aren't sure how the `for` loop works over dictionaries, check out [this online Python tutorial](https://www.tutorialspoint.com/python/python_dictionary.htm). From here, you can figure out how to access the `views` and `likes` values from your dictionary.
T> * After your population script has been updated, you can move on to customising the admin interface. You will need to edit `rango/admin.py` and create a `PageAdmin` class that inherits from `admin.ModelAdmin`. 
T>      * Within your new `PageAdmin` class, add `list_display = ('title', 'category', 'url')`.
T>      * Finally, register the `PageAdmin` class with Django's admin interface. You should modify the line `admin.site.register(Page)`. Change it to `admin.site.register(Page, PageAdmin)` in Rango's `admin.py` file.
T>		* If you get really stuck look at our [code on github](https://github.com/leifos/tango_with_django_2/)!

> ### Тесты
>
> Like in the last chapter we have written tests to check if you have completed the chapter and the exercises. To check your work so far, [download the `tests.py` script](https://github.com/leifos/tango_with_django_2/blob/master/code/tango_with_django_project/rango/tests.py) from our [GitHub repository](https://github.com/leifos/tango_with_django_2/), and save it within your `rango` app directory.
>
> Un-comment the Chapter 5 related tests.
> To run the tests, issue the following command in the terminal or Command Prompt.
>
> {lang="text",linenos=off}
>     $ python manage.py test rango
>
> If you are interested in learning about automated testing, now is a good time to check out the [chapter on testing](#chapter-testing). The chapter runs through some of the basics on how you can write tests to automatically check the integrity of your code.
