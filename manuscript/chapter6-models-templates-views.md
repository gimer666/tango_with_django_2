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

Чтобы решить эту проблему мы будем использовать функцию `slugify`, предоставляемую Django. 

<!-->
<http://stackoverflow.com/questions/837828/how-do-i-create-a-slug-in-django>
-->
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
To implement the category pages so that they can be accessed via `/rango/category/<category-name-slug>/` we need to make a number of changes and undertake the following steps:

1. Import the `Page` model into `rango/views.py`.
2. Create a new view in `rango/views.py` called `show_category()`. The `show_category()` view will take an additional parameter, `category_name_url` which will store the encoded category name.
	- We will need helper functions to encode and decode the `category_name_url`.
3.  Create a new template, `templates/rango/category.html`.
4.  Update Rango's `urlpatterns` to map the new `category` view to a URL pattern in `rango/urls.py`.

We'll also need to update the `index()` view and `index.html` template to provide links to the category page view.

### Представление Category
In `rango/views.py`, we first need to import the `Page` model. This means we must add the following import statement at the top of the file.

{lang="python",linenos=off}
	from rango.models import Page

Next, we can add our new view, `show_category()`.

{lang="python",linenos=off}
	def show_category(request, category_name_slug):
	    # Create a context dictionary which we can pass 
	    # to the template rendering engine.
	    context_dict = {}
	    
	    try:
	        # Can we find a category name slug with the given name?
	        # If we can't, the .get() method raises a DoesNotExist exception.
	        # So the .get() method returns one model instance or raises an exception.
	        category = Category.objects.get(slug=category_name_slug)
	        
	        # Retrieve all of the associated pages.
	        # Note that filter() will return a list of page objects or an empty list
	        pages = Page.objects.filter(category=category)
	        
	        # Adds our results list to the template context under name pages.
	        context_dict['pages'] = pages
	        # We also add the category object from 
	        # the database to the context dictionary.
	        # We'll use this in the template to verify that the category exists.
	        context_dict['category'] = category
	    except Category.DoesNotExist:
	        # We get here if we didn't find the specified category.
	        # Don't do anything - 
	        # the template will display the "no category" message for us.
	        context_dict['category'] = None
	        context_dict['pages'] = None
	    
	    # Go render the response and return it to the client.
	    return render(request, 'rango/category.html', context_dict)

Our new view follows the same basic steps as our `index()` view. We first define a context dictionary and then attempt to extract the data from the models, and add the relevant data to the context dictionary. We determine which category by using the value passed as parameter `category_name_slug` to the `show_category()` view function. If the category slug is found in the `Category` model, we can then pull out the associated pages, and add this to the context dictionary, `context_dict`.

### Шаблон Category
Now let's create our template for the new view. In `<workspace>/tango_with_django_project/templates/rango/` directory, create `category.html`. In the new file, add the following code.

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

The HTML code example again demonstrates how we utilise the data passed to the template via its context through the tags `{{ }}`. We access the `category` and `pages` objects, and their fields e.g. `category.name` and `page.url`.

If the `category` exists, then we check to see if there are any pages in the category. If so, we iterate through the pages using the `{% for page in pages %}` template tags. For each page in the `pages` list, we present their `title` and `url` attributes. This is displayed in an unordered HTML list (denoted by the `<ul>` tags). If you are not too familiar with HTML then check out the [HTML Tutorial by W3Schools.com](http://www.w3schools.com/html/) to learn more about the different tags.

I> ### Note on Conditional Template Tags
I> The Django template conditional tag - `{% if %}` - is a really neat way of determining the existence of an object within the template's context. Make sure you check the existence of an object to avoid errors.
I>
I> Placing conditional checks in your templates - like `{% if category %}` in the example above - also makes sense semantically. The outcome of the conditional check directly affects the way in which the rendered page is presented to the user. Remember, presentational aspects of your Django appls should be encapsulated within templates.

### URL сопоставление с параметрами
Now let's have a look at how we actually pass the value of the `category_name_url` parameter to the `show_category()` function. To do so, we need to modify Rango's `urls.py` file and update the `urlpatterns` tuple as follows.

{lang="python",linenos=off}
	urlpatterns = [
	    path('', views.index, name='index'),
	    path('about/', views.about, name='about'),
	    path('category/<slug:category_name_slug>/', 
			views.show_category, name='show_category'),
	]


We have added a new path which contains a parameter `<slug:category_name_slug>`. This indicates to django that we want to match a string which is a slug, and to assign it to `category_name_slug`. You will notice that this variable name is what we pass through to the view `show_category`. You can also extract out other variables like strings and integers, see the [Django documentation on URL paths](https://docs.djangoproject.com/en/2.0/ref/urls/) for more details. If you need to parse more complicated expressions you can use `re_path()` instead of `path()` which will allow you to match all sorts of regular (and iregular) expressions. Luckily for us Django provides matches for the most common patterns.
<!-->
We have added in a rather complex entry that will invoke `view.show_category()` when the URL pattern `r'^category/(?P<category_name_slug>[\w\-]+)/$'` is matched. 

There are two things to note here. First we have added a parameter name with in the URL pattern, i.e. `<category_name_slug>`, which we will be able to access in our view later on. When you create a parameterised URL you need to ensure that the parameters that you include in the URL are declared in the corresponding view.
The next thing to note is that the regular expression `[\w\-]+)` will look for any sequence of alphanumeric characters e.g. `a-z`, `A-Z`, or `0-9` denoted by `\w` and any hyphens (-) denoted by `\-`, and we can match as many of these as we like denoted by the `[ ]+` expression.

The URL pattern will match a sequence of alphanumeric characters and hyphens which are between the `rango/category/` and the trailing `/`. This sequence will be stored in the parameter `category_name_slug` and passed to `views.show_category()`. For example, the URL `rango/category/python-books/` would result in the `category_name_slug` having the value, `python-books`. However, if the URL was `rango/category/££££-$$$$$/` then the sequence of characters between `rango/category/` and the trailing `/` would not match the regular expression, and a `404 not found` error would result because there would be no matching URL pattern.
-->

All view functions defined as part of a Django applications *must* take at least one parameter. This is typically called `request` - and provides access to information related to the given HTTP request made by the user. When parameterising URLs, you supply additional named parameters to the signature for the given view.  That is why our `show_category()` view was defined as `def show_category(request, category_name_slug)`.

<!--
It's not the position of the additional parameters that matters, it's
the *name* that must match anything defined within the URL pattern.
 Note how `category_name_slug` defined in the URL pattern matches the
 `category_name_slug` parameter defined for our view. 
		Using  `category_name_slug` in our view will give `python-books`, or whatever value was supplied as that part of the URL.
-->
		
I> ###Regex Hell
I> "Some people, when confronted with a problem, think *'I know, I'll use regular expressions.'* Now they have two problems."
I> [Jamie Zawinski](http://blog.codinghorror.com/regular-expressions-now-you-have-two-problems/)
I>
I> Django's `path()` method means you can generally avoid Regex Hell - but if you need to use a regular expression this [cheat sheet](http://cheatography.com/davechild/cheat-sheets/regular-expressions/) is really useful.

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
X> Reinforce what you've learnt in this chapter by trying out the following exercises.
X> 
X> * Update the population script to add some value to the `views` count for each page.
X> * Modify the index page to also include the top 5 most viewed pages.
X> * Include a heading for the "Most Liked Categories" and "Most Viewed Pages".
X> * Include a link back to the index page from the category page.
X> * Undertake [part three of official Django tutorial](https://docs.djangoproject.com/en/2.0/intro/tutorial03/) if you have not done so already to reinforce what you've learnt here.

{id="fig-ch6-exercises"}
![The index page after you complete the exercises, showing the most liked categories and most viewed pages.](images/ch6-exercises.png)

T> ### Hints
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
