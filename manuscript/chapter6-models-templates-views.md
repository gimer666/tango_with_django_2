# Модели, шаблоны и представления {#chapter-mtv}
Теперь когда мы настроили модели и заполнили базу данных некоторыми тестовыми данными, можно объединить наши знания о моделях, шаблонах и представлениях для выдачи динамического содержимого. В этой главе мы рассмотрим как отобразить категории на главной странице и затем создадим отдельные страницы для категорий, на которых будут показаны связанные с ними список ссылок.

## Основная последовательность действий: Создание страниц с изменяемыми данными
Чтобы создать страницу с изменяемыми данными в Django, необходимо выполнить последовательность действий из пяти основных этапов.

1.  В файле `views.py` импортировать модели, которые Вы хотите использовать.
2.  В функции представления вызвать модель, чтобы получить данные, необходимые для отображения.
3.  Затем передать результаты из Вашей модели в контекст шаблона.
4.  Создайте/измените шаблон таким образом, чтобы он выдавал данные из контекста.
5.  Если Вы ещё этого не сделали, сопоставьте URL Вашему представлению.

Эти шаги показывают как нам нужно работать с фреймворком Django, чтобы связать вместе модели, представления и шаблоны.

## Показываем категории на главной странице Rango
Одним из требований к главной странице было отображение первых пяти категорий по рангу. Для выполнения этого требования мы осуществим каждый из вышеприведенных этапов.

### Импортируем требуемые модели
Во-первых, нам нужно осуществить первый шаг. Откройте файл `rango/views.py` и в начало файла, после других импортов, импортируйте модель `Category` из файла Rango `models.py`.

{lang="python",linenos=off}
	# Импорт модели Category
	from rango.models import Category

### Изменяем представление Index
В этом разделе мы реализуем шаги 2 и 3, где нам нужно изменить функцию представления `index()`. Помните, что функция `index()` отвечает за представление главной страницы. Измените `index()` следующим образом:

{lang="python",linenos=off}
	def index(request):
	    # Осуществляем запрос к базе данных для получения списка ВСЕХ категорий, хранящихся в ней на текущий момент.
	    # Упорядочиваем категории по количеству лайков в порядке убывания.
	    # Извлекаем только первые 5 - или все, если их число меньше 5.
	    # Помещаем список в наш словарь контекста, который будет передан механизму шаблонов.	    
	    
	    category_list = Category.objects.order_by('-likes')[:5]
	    context_dict = {'categories': category_list}
	    
	    # Формируем ответ для клиента по шаблону и отправляем обратно!
	    return render(request, 'rango/index.html', context_dict)

Здесь выражение `Category.objects.order_by('-likes')[:5]` использует модель `Category` для получения первых пяти категорий. Вы видите, что в нём используется метод `order_by()` для сортировки по количеству лайков - `likes` - в порядке убывания. Знак `-` в `-likes` означает, что мы хотим, чтобы они были отсортированы в порядке убывания (если мы уберём `-`, то результат запроса будет возвращен в порядке возрастания). Поскольку возвращается список объектов `Category`, мы использовали операторы списка языка Python для извлечения первых пяти объектов из списка (`[:5]`) для формирования подмножества объектов `Category`.

Когда запрос выполнен, мы передаём ссылку на список (хранящийся в виде переменной `category_list`) в словарь, `context_dict`. Этот словарь затем передается как часть контекста в движок шаблона при вызове `render()`.

W> ###Замечание
W> Чтобы вышеприведенный фрагмент кода работал как надо, Вам нужно выполнить упражнения из предыдущей части, где Вам нужно было добавить поле `likes` в модель `Category`.

### Изменяем шаблон Index
После обновления представления мы можем осуществить четвертый шаг и обновить шаблон `rango/index.html`, расположенный в каталоге `templates` Вашего проекта. Измените HTML-код, чтобы он выглядел так, как показано ниже.

{lang="html",linenos=off}
	<!DOCTYPE html>
	{% load staticfiles %}
	<html>
	<head>
	    <title>Rango</title>
	</head>
	
	<body>
	    <h1>Rango says...</h1>
	    <div>
			hey there partner! <br/>
			<strong>{{ boldmessage }}</strong>
	    </div>
	
	    <div>
	    {% if categories %}
	    <ul>
	        {% for category in categories %}
	            <li>{{ category.name }}</li>
	        {% endfor %}
	    </ul>
	    {% else %}
	        <strong>There are no categories present.</strong>
	    {% endif %}	
	    </div>
	    
	    <div>
	        <a href="/rango/about/">About Rango</a><br />
	        <img src="{% static "images/rango.jpg" %}" alt="Picture of Rango" /> 
	    </div>
	</body>
	</html>

Здесь мы использовали язык шаблонов Django для представления данных, используя управляющик операторы `if` и `for`. Внутри тега `<body>` страницы мы проверяем содержит ли `categories` - название переменной контекста с нашим списком категорий - какаие-либо категории (`{% if categories %}`).

Если да - то мы приступаем к созданию неупорядоченного списка (внутри тегов `<ul>`). Затем используется цикл `for` (`{% for category in categories %}`) для итераций по списку результатов и выводится каждое название категории (`{{ category.name }})` внутри пары тегов `<li>` для создания элемента списка.

Если категорий не существует, то отображается сообщение о том, что категории отсутствуют.

Как видно из примера в языке шаблонов Django все команды заключаются в теги `{%` и `%}`, а на переменные ссылаются с помощью скобок {{ и }}.

Если Вы теперь посетите домашнюю страницу Rango по адресу `<http://127.0.0.1:8000/rango/>`, Вы должны увидеть список категорий под заголовком страницы как показано на [рисунке ниже](#figch6-rango-categories-index).

{id="fig-ch6-rango-categories-index"}
![Домашняя страница Rango - теперь генерирующаяся динамически - отображающая список категорий.](images/ch6-rango-categories-index.png)

## Создание страницы с подробной информацией о категории
В соответствии с [ТЗ приложения Rango](#overview-design-brief-label), нам также необходимо показать список страниц, которые связаны с каждой категорией. При этом нам придется решить несколько проблем. Необходимо создать новое представление, которое должно принимать параметры. Нам также необходимо создать шаблоны URL и URL строки для кодирования названий категорий.

### URL настройка и сопоставление
Сначала решим задачу, связанную с URL. Одним из способов решения этой проблемы является использование уникального идентификатора (ID) для каждой категории в URL. Например, мы можем создать URLы вида `/rango/category/1/` или `/rango/category/2/`, где числа соответствуют категориям с уникальными идентификаторами 1 и 2 соответственно. Тем не менее, невозможно определить, что это за категория, только по идентификатору.

Вместо этого мы могли бы использовать название категории как часть URL. Например, мы можем представить, что URL `/rango/category/python/` выдаст нам список страниц, связанных с Python. Это простой, читаемый и понятный URL. Если мы хотим использовать этот метод, то необходимо обрабатывать категории, которые будут состоять из нескольких слов, например, 'Other Frameworks', и т.д..

T> ### Используйте человекопонятные URLs
T> Разработка человекопонятных и читаемых URL-адресов - это важный аспект веб проектирования. Смотри [статью на Википедии о человекопонятных URLах](http://en.wikipedia.org/wiki/Clean_URL) для более подробной информации.

Чтобы решить эту проблему мы будем использовать функцию `slugify`, предоставляемую Django (<http://stackoverflow.com/questions/837828/how-do-i-create-a-slug-in-django>).
### Добавления поля Slug в таблицу категорий
Для того, чтобы создать читабельные URL-адреса, нам нужно добавить поле `slug` в модель `Category`. Сначала нам необходимо импортировать функцию `slugify` из Django, которая заменит пробелы на дефисы - например, выражение `"how do i create a slug in django"` превращается в `"how-do-i-create-a-slug-in-django"`.

W> ### Небезопасные URL-адреса
W> Хотя Вы можете использовать пробелы в URL адресах, это считается не безопасным. Прочитайте IETF памятку по URLам, чтобы узнать больше.

Затем нам надо переопределить метод `save()` модели `Category`, в котором мы вызовем метод `slugify` и обновим поле `slug` с помощью него. Обратите внимание, что каждый раз при изменении названия категории, slug также изменится. Измените Вашу модель как показано ниже и не забудьте импортировать метод slugify.

{lang="python",linenos=off}
	from django.template.defaultfilters import slugify
	...
	class Category(models.Model):
	    name = models.CharField(max_length=128, unique=True)
	    views = models.IntegerField(default=0)
	    likes = models.IntegerField(default=0)
	    slug = models.SlugField()
	    
	    def save(self, *args, **kwargs):
	        self.slug = slugify(self.name)
	        super(Category, self).save(*args, **kwargs)
	        
	    class Meta:
	        verbose_name_plural = 'categories'
	    
	    def __str__(self):
	        return self.name

Теперь, когда модель была обновлена, изменения должны быть переданы в базу данных. Но поскольку данные уже существуют в базе данных, нам нужно учитывать последствия изменений. В частности, для всех существующих названий категорий мы хотим преобразовать их, используя функцию `slugify` (что осуществляется при первоначальном сохранении записи). Когда мы обновляем модели с помощью инструмента миграции, он добавляет поле `slug` и предоставляет возможность заполнения поля значением по умолчанию. Конечно, нам нужно определенное значение для каждой записи - поэтому сначала нам нужно выполнить миграцию, а затем повторно запустить скрипт для заполнения базы. Это связано с тем, что скрипт будет явно вызывать метод `save()` для каждой записи, запуская реализованный выше метод 'save()' и, таким образом, обновлять поле slug соответствующим образом для каждой записи.

Чтобы выполнить миграцию введите следующие команды (как подробно описано в разделе [Основные последовательности действий из главы Модели и базы данных](#section-models-databases-workflow)).

{lang="text",linenos=off}
	$ python manage.py makemigrations rango
	$ python manage.py migrate

Поскольку мы не задали значение по умолчанию для slug и у нас уже существуют данные в модели, команда `migrate` предоставит Вам два варианта. Выберите вариант, в котором предлагается задать значение по умолчанию и введите пустую строку илил две кавычки (т. е. ''). Снова запустите скрипт для заполнения, который обновит поля slug.

{lang="text",linenos=off}
	$ python populate_rango.py

Теперь запустите сервер для разработки с помощью команды `$ python manage.py runserver`, и просмотрите данные в моделях с помощью интерфейса администратора по адресу `http://127.0.0.1:8000/admin/`.

Если Вы захотите добавить новую категорию через интерфейс администратора, то столкнетесь с одной или двумя проблемами!

1. Допустим мы хотим добавить категорию `Python User Groups`. Если вы сделаете это и попытаетесь сохранить запись, Django не позволит вам сохранить её пока Вы не заполните также поле slug. Хотя мы можем набрать в нём `python-user-groups`, ручной ввод может приводить к генерации небезопасных URL-адресов. Поэтому лучше генерировать slug автоматически.

2. Следующая проблема возникает, если одна категория называется `Django`, а другая - `django`. Поскольку `slugify()` преобразует всё к нижнему регистру символов будет невозможно определить какая категория соответствует URL `django`.

Для решения первой проблемы, мы можем или обновить нашу модель, чтобы поле slug можно было оставлять пустым, например:

{lang="python",linenos=off}
	slug = models.SlugField(blank=True) 

**или** мы можем изменить интерфейс администратора так, чтобы он автоматически заполнял поле slug при вводе названия категории. Для этого обновите `rango/admin.py`, добавив следующий код.

{lang="python",linenos=off}
	from django.contrib import admin
	from rango.models import Category, Page
	...
	# Добавляем этот класс для изменения интерфейса администратора
	class CategoryAdmin(admin.ModelAdmin):
	    prepopulated_fields = {'slug':('name',)}
	
	# Обновляем регистр, чтобы она включала этот измененный интерфейс
	admin.site.register(Category, CategoryAdmin)
	...

Опробуйте измененный интерфейс администратора и добавьте новую категорию.

Теперь для решения второй проблемы, мы можем сделать поле slug также уникальным, добавив ограничение к полю slug.

{lang="python",linenos=off}
	slug = models.SlugField(unique=True)

После добавления этого ограничения, мы можем использовать поле slug для уникальной идентификации каждой категории. Мы могли бы добавить ограничение уникальности ранее, но если бы мы выполнили миграцию и использовали бы пустую строку в качестве значения по умолчанию, то получили бы ошибку. Это было бы связано с тем, что нарушилось бы условие уникальности. Мы могли бы удалить базу данных и воссоздать её заново - но такое не всегда выполнимо.

W> ### Проблемы при миграциях
W> Всегда лучше заранее спроектировать свою базу данных и стараться не изменять её. Создание сценария для заполнения позволяет Вам легко воссоздать свою базу данных, если нужно удалить её.
W>
W> Иногда лучше просто удалить базу данных и воссоздать всё с нуля, чем пытаться понять из-за чего возник конфликт. Хорошим упражнением является написание сценария для вывода данных из базы, чтобы любые сделанные Вами изменения могли быть сохранены в файл, который можно будет просмотреть в случае необходимости.

### Последовательность действий для создания страницы с категориями
Чтобы страницы с категориями были доступы по адресу `/rango/category/<category-name-slug>/` нам нужно внести ряд изменений и предпринять следующие шаги:

1. Импортировать модель `Page` в `rango/views.py`.
2. Создать новое представление в `rango/views.py` под названием `show_category()`. Представление `show_category()` будет принимать дополнительный параметр - `category_name_url`, в котором будет хранится зашифрованное название категории.
	- Нам понадобятся вспомогательные функции для кодирования и декодирования `category_name_url`.
3.  Создайте новый шаблон `templates/rango/category.html`.
4.  Обновите `urlpatterns` приложения Rango, сопоставив новому представлению `show_category` URL шаблон в `rango/urls.py`.

Нам также потребуется обновить представление `index()` и шаблон `index.html`, чтобы создать ссылки на представление страницы с категориями.

### Представление Category
В `rango/views.py`, нам сначала нужно импортировать модель `Page`. Для этого мы должны добавить следующую команду импорта в начале файла.

{lang="python",linenos=off}
	from rango.models import Page

Затем мы можем добавить наше новое представление, `show_category()`.

{lang="python",linenos=off}
	def show_category(request, category_name_slug):
	    # Создаем словарь контекста, который мы можем передать механизму обработки шаблонов.
	    # механизму обработки шаблонов.
	    context_dict = {}
	    
	    try:
	        # Можем ли мы найти название категории с дефисами для заданного названия?
	        # Если нет, метод .get() вызывает исключение DoesNotExist.
	        # Таким образом, метод .get() возвращает экземпляр модели или вызывает исключение.
	        category = Category.objects.get(slug=category_name_slug)
	        
	        # Получить все связанные страницы.
	        # Заметьте, что фильтр возвращает список страниц-объектов или пустой список
	        pages = Page.objects.filter(category=category)
	        
	        # Добавить наш список результатов (назвав его pages) к контексту шаблона.
	        context_dict['pages'] = pages
	        # Мы также добавим объект категории 
	        # из базы данных в словарь контекста.
	        # Мы будем использовать это информацию в шаблоне, чтобы проверить, что категория существует.		
	        context_dict['category'] = category
	    except Category.DoesNotExist:
	        # Мы попадаем сюда, если не нашли указанной категории.
	        # Ничего делать не надо - 
	        # шаблон отобразить сообщение "Нет такой категории" вместо нас.
	        context_dict['category'] = None
	        context_dict['pages'] = None
	    
	    # Возврщаем ответ на запрос клиенту.
	    return render(request, 'rango/category.html', context_dict)

При создании нашего нового представления мы использовали те же основные этапы, что и для нашего представления  `index()`. Сначала мы определили словарь контекста, затем попытались извлечь данные из модели и добавить соответствующие данные в словарь контекста. Мы определили категорию по значению, передаваемому в виде параметра `category_name_slug` в функцию представления `show_category()`. Если категория существует в модели `Category`, мы можем затем извлечь соответствующие страницы и добавить их к словарю контекста --  `context_dict`.

### Шаблон Category
Теперь давайте создадим наш шаблон для нового представления. 
Now let's create our template for the new view. В каталоге `<workspace>/tango_with_django_project/templates/rango/` создайте `category.html`. В новый файл добавьте следующий код.

{lang="html",linenos=on}
	<!DOCTYPE html>
	<html>
	<head>
	    <title>Rango</title>
	</head>
	<body>
	    <div>
	    {% if category %}
	        <h1>{{ category.name }}</h1>
	        {% if pages %}
	            <ul>
	            {% for page in pages %}
	                <li><a href="{{ page.url }}">{{ page.title }}</a></li>
	            {% endfor %}
	            </ul>
	        {% else %}
	            <strong>No pages currently in category.</strong>
	        {% endif %}
	    {% else %}
	        The specified category does not exist!
	    {% endif %}
	    </div>
	</body>
	</html>

Пример HTML кода опять показывает, как мы используем данные, передаваемые в шаблон с помощью его контекста через теги `{{ }}`. Мі получаем доступ к объектам `category` и `pages` и их полям, например, `category.name` и `page.url`.

Если `category` существует, то мы проверяем то мы проверяем есть ли в ней страницы. Если есть, то мы перебираем страницы, используя теги шаблона `{% for page in pages %}`. Для каждой страницы в списке `pages` мы показываем их атрибуты `title` и `url`. Они отображаются в виде неупорядоченного HTML списка (обозначаемого тегами `<ul>`). Если вы не слишком знакомы с HTML, то просмотрите [учебное пособие по HTML от W3Schools.com](http://www.w3schools.com/html/), чтобы узнать больше о различных тегах.

I> ### Замечание, связанное с условными тегами шаблона
I> Условный тег шаблона Django - `{% if %}` - это на самом деле удобный способ определения существования объекта в контексте шаблона. Всегда проверяйте существовование объекта, чтобы избежать ошибок.
I>
I> Размещение условных проверок в Ваших шаблонах - таких как `{% if category %}` в вышеприведенном примере - также имеет семантический смысл. Результат условной проверки непосредственно влияет на то, как страница, созданная на основе шаблона, будет представлена пользователю. Помните, что особенности, связанные с внешним видом страниц, Ваших Django приложений должны быть инкапсулированы в шаблоны.

### URL сопоставление с параметрами
Теперь давайте рассмотрим как передать значение параметра `category_name_url` в функцию `show_category()`. Для этого необходимо модифицировать файл Rango `urls.py` и обновить кортеж `urlpatterns` следующим образом.

{lang="python",linenos=off}
	urlpatterns = [
	    path('', views.index, name='index'),
	    path('about/', views.about, name='about'),
	    path('category/<slug:category_name_slug>/', 
			views.show_category, name='show_category'),
	]

Мы добавили новый путь, который содержит параметр `<slug:category_name_slug>`. Это указывает Django что мы хотим проверить является ли строка правильным URL-адресом и присвоить её переменной `category_name_slug`. Вы наверное заметили, что это же название переменной мы передаём в представление `show_category`. Вы также можете извлекать другие переменные, такие как строки и целые числа, более подробную информацию смотри в [документации Django по URL путям](https://docs.djangoproject.com/en/2.0/ref/urls/). Если вам нужно разобрать более сложные выражения, вы можете использовать `re_path()` вместо `path()`, которая позволяет вам сравнивать всевозможные виды регулярных (и нерегулярных) выражений. К счастью, в Django уже реализованы выражения для самых популярных шаблонов.

<!-->
Мы добавили довольно сложную строку, которая будет вызывать `view.show_category()`, когда произойдет совпадение с URL шаблоном `r'^category/(?P<category_name_slug>[\w\-]+)/$'`.

Здесь нужно отметить две вещи. Сначала мы добавили название параметра в URL шаблон, т. е. `<category_name_slug>`, к которому мы сможем получить доступ в нашем представлении позже. При создании параметризованного URL-адреса необходимо убедиться, что параметры, включенные в URL-адрес, объявлены в соответствующем представлении.

Следующее, что следует отметить, это то, что регулярное выражение `[\w\-]+)` будет искать любую последовательность алфавитно-цифровых символов, например, `a-z`, `A-Z`, или `0-9`, обозначаемые буквой `\w` и любые дефисы (-), обозначаемые буквой `\-`, и каких символов в последовательности может быть сколько угодно, что обозначается `[ ]+` в выражении.

URL шаблон будет соответствовать последовательности алфавитно-цифровых символов и дефисов, которые находятся между `rango/category/` и завершающим `/`. Эта последовательность будет сохранена в параметре `category_name_slug` и передана в `views.show_category()`. Например, 
для URL `rango/category/python-books/` значение в `category_name_slug` будет равно `python-books`. Но если URL был равен `rango/category/££££-$$$$$/`, то последовательность символов между `rango/category/` и завершающим `/` не будет соответствовать регулярному выражению и возникнет ошибка `404 not found`, поскольку не будет найдено подходящего URL шаблона.
-->

Все фукнкции представления, определенные как часть приложений Django, *должны* принимать хотя бы один параметр. Он обычно называется `request` - и обеспечивает доступ к информации, связанной с заданным HTTP запросом, сделанным пользователем. При использовании URLов с параметрами, Вы должны передавать дополнительные именованные параметры в набор аргументов для заданного представления. Именно поэтому наше представление `show_category()` было определено как `def show_category(request, category_name_slug)`.

<!--
Здесь важна не позиция дополнительных параметров, а 
*название*, которое должно соответствовать названию, определенному в URL шаблоне.
Обратите внимание, что название `category_name_slug`, заданное в URL шаблоне, соответствует 
параметру `category_name_slug`, определенному для нашего представления.
Значение `category_name_slug` в нашем представлении будет равно `python-books` или любому другому значению, указанному в этой части URL.
-->
		
I> ### Проблема с использованием регулярных выражений
I> "Некоторые люди, сталкиваясь с проблемой, думают *'Я знаю как её решить, я буду использовать регулярные выражения.'* В настоящее время в этим связано две проблемы."
I> [Jamie Zawinski](http://blog.codinghorror.com/regular-expressions-now-you-have-two-problems/)
I>
I> Использование Django метода `path()` позволяет Вам в большинстве случаев избежать проблем с регулярными выражениями - но если  необходимо использовать их - эта [шпаргалка](http://cheatography.com/davechild/cheat-sheets/regular-expressions/) может быть Вам полезна [cheat sheet].

### Изменяем шаблон Index
Our new view is set up and ready to go - but we need to do one more thing. Our index page template needs to be updated so that it links to the category pages that are listed. We can update the `index.html` template to now include a link to the category page via the slug.

{lang="html",linenos=off}
	<!DOCTYPE html>
	{% load staticfiles %}
	<html>
	    <head>
	        <title>Rango</title>
	    </head>
	    
	    <body>
			hey there partner! <br />
			<strong>{{ boldmessage }}</strong>
	        
	        <div>
	            hey there partner!
	        </div>
	        
	        <div>
	        {% if categories %}
	        <ul>
	            {% for category in categories %}
	            <!-- Following line changed to add an HTML hyperlink -->
	            <li>
	            <a href="/rango/category/{{ category.slug }}">{{ category.name }}</a>
	            </li>
	            {% endfor %}
	        </ul>
	        {% else %}
	            <strong>There are no categories present.</strong>
	        {% endif %}
	        </div>
	        
	        <div>
	            <a href="/rango/about/">About Rango</a><br />
	            <img src="{% static "images/rango.jpg" %}" alt="Picture of Rango" /> 
	        </div>
	    </body>
	</html>

Again, we used the HTML tag `<ul>` to define an unordered list. Within the list, we create a series of list elements (`<li>`), each of which in turn contains a HTML hyperlink (`<a>`). The hyperlink has an `href` attribute, which we use to specify the target URL defined by `/rango/category/{{ category.slug }}` which, for example, would turn into `/rango/category/python-books/` for the category `Python Books`.

### Пример работы
Let's try everything out now by visiting the Rango homepage. You should see up to five categories on the index page. The categories should now be links. Clicking on `Django` should then take you to the `Django` category page, as shown in the [figure below](#fig-ch6-rango-links). If you see a list of links like `Official Django Tutorial`, then you've successfully set up the new page. 

What happens when you visit a category that does not exist? Try navigating a category which doesn't exist, like `/rango/category/computers/`. Do this by typing the address manually into your browser's address bar. You should see a message telling you that the specified category does not exist.

{id="fig-ch6-rango-links"}
![The links to Django pages. Note the mouse is hovering over the first link -- you can see the corresponding URL for that link at the bottom left of the Google Chrome window.](images/ch6-rango-links.png)

X> ## Упражнения
X> Закрепите то, чему Вы научились в этой главе, пытаясь выполнить следующие упражнения.
X> 
X> * Update the population script to add some value to the `views` count for each page.
X> * Modify the index page to also include the top 5 most viewed pages.
X> * Include a heading for the "Most Liked Categories" and "Most Viewed Pages".
X> * Include a link back to the index page from the category page.
X> * Undertake [part three of official Django tutorial](https://docs.djangoproject.com/en/2.0/intro/tutorial03/) if you have not done so already to reinforce what you've learnt here.

{id="fig-ch6-exercises"}
![The index page after you complete the exercises, showing the most liked categories and most viewed pages.](images/ch6-exercises.png)

T> ### Подсказки к упражнениям
T> * When updating the population script, you'll essentially follow the same process as you went through in the [previous chapter's](#chapter-models-databases) exercises. You will need to update the data structures for each page, and also update the code that makes use of them.
T>      * Update the three data structures containing pages for each category -- `python_pages`, `django_pages` and `other_pages`. Each page has a `title` and `url` -- they all now need a count of how many `views` they see, too.
T>      * Look at how the `add_page()` function is defined in your population script. Does it allow for you to pass in a `views` count? Do you need to change anything in this function?
T>      * Finally, update the line where the `add_page()` function is *called*. If you called the views count in the data structures `views`, and the dictionary that represents a page is called `p` in the context of where `add_page()` is called, how can you pass the views count into the function?
T> * Remember to re-run the population script so that the views are updated.
T>      * You will need to edit both the `index` view and the `index.html` template to put the most viewed (i.e. popular pages) on the index page.
T>      * Instead of accessing the `Category` model, you will have to ask the `Page` model for the most viewed pages.
T>      * Remember to pass the list of pages through to the context.
T>      * If you are not sure about the HTML template code to use, you can draw inspiration from the `category.html` template code as the markup is essentially the same.

T> ### Model Tips
T> For more tips on working with models you can take a look through the following blog posts:
T> 
T> 1. [Best Practices when working with models](http://steelkiwi.com/blog/best-practices-working-django-models-python/) by Kostantin Moiseenko. In this post you will find a series of tips and tricks when working with models.
T> 2. [How to make your Django Models DRYer](https://medium.com/@raiderrobert/make-your-django-models-dryer-4b8d0f3453dd#.ozrdt3rsm) by Robert Roskam. In this post you can see how you can use the `property` method of a class to reduce the amount of code needed when accessing related models.
