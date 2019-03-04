# Использование Bootstrap в приложении Rango {#chapter-bootstrap}
В этой главе мы стилизуем приложение Rango c помощью инструментария *Twitter Bootstrap 4*. Bootstrap - это самый популярный HTML, CSS, JS фреймворк, который мы можем использовать для стилизации нашего приложения. Инструментарий позволяет разрабатывать и стилизовать адаптивные веб-приложения и довольно прост в использовании, когда Вы с ним познакомитесь.

I> ### Каскадные таблицы стилей
I> Если Вы не знакомы с CSS, ознакомтесь с [кратким курсом по CSS](#chapter-css). Там представлено краткое руководство по основам работы с каскадными таблицами стилей.

Теперь перейдите на [сайт Bootstrap 4.0](http://getbootstrap.com/) - там приведен образец кода и примеры различных элементов, показано как стилизовать их, добавив соответствующие теги стилей и т. д. На сайте Bootstrap также приводится множество [образцовых макетов](http://getbootstrap.com/examples/), на основе которых мы можем спроектировать свой.

Мы считаем, что [стиль dashboard](http://getbootstrap.com/examples/dashboard/) лучше всего удовлетворяет нашим потребностям с учетом макета Rango, т. к. он содержит верхнее меню, боковую панель (которую мы будем использовать для отображения категорий) и панель для основного содержимого.

Загрузите и сохраните исходный код HTML шаблона Dashboard в файл с названием `bootstrap-base.html`, выбрав в качестве места для хранения Ваш каталог `templates/rango`.

Прежде чем мы сможем использовать шаблон, нам нужно изменить HTML так, чтобы мы могли использовать его в нашем приложении. Изменения, которые мы осуществили приведены ниже вместе с обновленным HTML кодом (чтобы облегчить Вам задачу, копия HTML кода находится в нашем [github репозитории](https://raw.githubusercontent.com/leifos/tango_with_django_2/master/code/tango_with_django_project/templates/rango/bootstrap-base.html)).

- Были заменены все ссылки вида `../../` на `http://getbootstrap.com/docs/4.2/`, поскольку мы используем версию 4.2.
- Была заменена ссылка на `dashboard.css` с относительной на абсолютную:
	- `http://getbootstrap.com/docs/4.2/examples/dashboard/dashboard.css`
- Удалена форма поиска из верхней навигационной панели.
- Удалено всё несущественное содержимое из HTML и заменено на:
	- `{% block body_block %}{% endblock %}`
- Элементу title присвоено:
	- `<title>
           Rango - {% block title %}How to Tango with Django!{% endblock %}
       </title>`
- Изменено `project name` на `Rango`.
- Добавлены ссылки на главную страницу, страницу входа в систему, регистрации и т. д. в верхнюю навигационную панель.
- Добавлен боковой блок, т. е., `{% block side_block %}{% endblock %}`
- Добавлен блок `{% load staticfiles %}`  и `{% load rango_template_tags %}` после тега `DOCTYPE`.



## Новый базовый шаблон

W> ### Копирование и вставка кода
W> В введении мы говорили, что копировать и вставлять код нельзя - но код приведенный здесь является исключением.
W> Однако, если Вы будете напрямую вырезать и вставлять код, то в итоге добавите дополнительный текст, который Вам не нужен. Поэтому перейдите на нашу страницу GitHub и возьмите [базовый шаблон](https://raw.githubusercontent.com/leifos/tango_with_django_2/master/code/tango_with_django_project/templates/rango/bootstrap-base.html) показанный ниже оттуда.
W> 
W> Кроме того, если Вы не понимаете, что делают конкретные классы Bootstrap, обратитесь к [Bootstrap документации](http://getbootstrap.com/css/).


{lang="html",linenos=off}
	<!DOCTYPE html>
	{% load staticfiles %}
	{% load rango_template_tags %}
	<html lang="en">
	  <head>
	    <meta charset="utf-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
	    <meta name="description" content="">
	    <meta name="author" content="Mark Otto, Jacob Thornton, and Bootstrap contributors">
	    <meta name="generator" content="Jekyll v3.8.5">
	    <link rel="icon" href="{% static 'images/favicon.ico' %}">
	    <title>
	      Rango - {% block title %}How to Tango with Django!{% endblock %}
	    </title>

	    <!-- Bootstrap core CSS -->
		<!-- Bootstrap core CSS -->
		<link href="https://getbootstrap.com/docs/4.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">
	
	    <!-- Custom styles for this template -->
	    <link href="https://getbootstrap.com/docs/4.2/examples/dashboard/dashboard.css" rel="stylesheet">

	  </head>

	  <body>
		  <header>
	    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark p-0">
	  <a class="navbar-brand p-2" href="{% url 'rango:index' %}">Rango</a>
	  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
	        <span class="navbar-toggler-icon"></span>
	      </button>
  
	<div class="collapse navbar-collapse" id="navbarCollapse">
	  <ul class="navbar-nav mr-auto">
	    <li class="nav-item"><a class="nav-link" href="{% url 'rango:index' %}">Home</a></li>
	    <li class="nav-item "><a class="nav-link" href="{% url 'rango:about' %}">About</a></li>
		{% if user.is_authenticated %}
	    <li class="nav-item "><a class="nav-link" href="{% url 'rango:restricted' %}">Restricted</a></li>
	    <li class="nav-item"><a class="nav-link" href="{% url 'rango:add_category' %}">Add Category</a></li>
	    <li class="nav-item"><a class="nav-link" href="{% url 'auth_logout' %}?next=/rango/">Logout</a></li>
		{% else %}
	    <li class="nav-item"><a class="nav-link" href="{% url 'registration_register' %}">Register Here</a></li>
	    <li class="nav-item "><a class="nav-link" href="{% url 'auth_login' %}">Login</a></li>
		{% endif %}
	
	  </ul>
	</div>
	</nav>

	</header>


	    <div class="container-fluid">
	      <div class="row">
			  <nav class="col-md-2 d-none d-md-block bg-light sidebar">
			       <div class="sidebar-sticky">
		
		        {% block sidebar_block %}
		            {% get_category_list category %}
		        {% endblock %}
			
			 </div>
		 </nav>
		 
	 <main role="main" class="col-md-9  ml-sm-auto col-lg-10 px-4">		
		 {% block body_block %}{% endblock %}
	 
	  <!-- FOOTER -->
	  <footer>
	    <p class="float-right"><a href="#">Back to top</a></p>
	    <p>&copy; 2019 Tango With Django 2 &middot; <a href="#">Privacy</a> &middot; <a href="#">Terms</a></p>
	  </footer>
	</main>


	    <!-- Bootstrap core JavaScript -->
	    <!-- Placed at the end of the document so the pages load faster -->
	 <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
	       <script>window.jQuery || document.write('<script src="https://getbootstrap.com/docs/4.2/assets/js/vendor/jquery-slim.min.js"><\/script>')</script><script src="https://getbootstrap.com/docs/4.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-zDnhMsjVZfS3hiP7oCBRmfjkQC4fzxVxFhBx8Hkz2aZX8gEvA/jsP3eXRCvzTofP" crossorigin="anonymous"></script>
	         <script src="https://cdnjs.cloudflare.com/ajax/libs/feather-icons/4.9.0/feather.min.js"></script>
        
	         <script src="https://getbootstrap.com/docs/4.2/examples/dashboard/dashboard.js"></script>
		</body>
	</html>

Как только Вы настроите шаблон, можете загрузить [Rango Favicon](https://raw.githubusercontent.com/leifos/tango_with_django_2/master/code/tango_with_django_project/static/images/favicon.ico) и сохранить её в `static/images/`.

Если Вы внимательно посмотрите на измененный HTML код макета Dashboard, то заметите, что большая часть структуры создается тегами `<div>`. По сути страница разбита на две части - верхнюю навигационную панель, которая находится внутри тегов `<header>` и основную панель с содержимым, которая обозначена тегом `<main ... >`. Внутри основной панели с содержимым есть `<div>`, в котором находятся два других divа для `sidebar_block` и `body block`.
	
При создании вышеприведенного кода шаблона предполагалось, что Вы прочитали главу Аутентификация пользователя и использовали пакет Django Regisration Redux. Если нет, то Вам нужно будет обновить шаблон и удалить/изменить ссылки для регистрации, входа в систему и т.д. в панели навигации в хедере.

Также обратите внимание, что шаблон HTML содержит ссылки на внешние сайты для получения необходимых файлов `css` и `js`. Поэтому Вам нужно быть подключенным к Интернету для загрузки стилей при запуске приложения.

I> ### Работаете Оффлайн?
I> Вместо того, чтобы добавлять внешние ссылки на файлы `css` и `js`, Вы можете загрузить все необходимые файлы и сохранить их в своём каталоге для статических файлов. Если Вы сделаете это, то просто обновите базовый шаблон добавив ссылки на статические файлы, хранящиеся локально.

## Быстрое изменение стиля
Чтобы придать приложению Rango нужный нам внешний вид, мы можем заменить содержимое существующего файла `base.html` на HTML код из шаблона `base_bootstrap.html`. Возможно Вы захотите сначала скопировать в другой файл ранее введенный код из `base.html`, а затем вставить в него код из `base_bootstrap.html`.

Теперь перезагрузите ваше приложение. Смотрится хорошо! Ну или лучше, чем было.

Вы должны заметить, что ваше приложение уже смотрится примерно в сто раз лучше. Ниже мы привели несколько снимков экрана страницы about до и после добавления стилей.

Посетите разные страницы. Поскольку все они наследуются от базового шаблона, они будут выглядеть намного лучше, но не идеально! В оставшейся части этой главы мы рассмотрим ряд изменений, сделанных в шаблонах, и будем использовать различные Bootstrap классы для улучшения внешнего вида приложения Rango.

<!--
## Page Headers
Now that we have the `base.html` all set up and ready to go, we can do a
really quick face lift to Rango by going through the Bootstrap
components and selecting the ones that suit the pages.

Let's start by updating all our templates by adding in the class `page-header` to the `<h1>` title tag at the top of each page. For example the `about.html` would be updated as follows.


{lang="html",linenos=off}
	{% extends 'rango/base.html' %}
	{% load staticfiles %}
	{% block title_block %}
		About
	{% endblock %}
	
	{% block body_block %}
		<div>
		<h1 class="page-header">About Page</h1>			
			This tutorial has been put together by: leifos and maxwelld90
		</div>	
		<img src="{% static "images/rango.jpg" %}" alt="Picture of Rango" /> 
	{% endblock %}

This doesn't visually appear to change the look and feel, but it informs the toolkit what is the title text, and if we change the theme then it will be styled appropriately.

-->

![Снимок экрана страницы About до добавления нового стиля.](images/ch12-about-nostyling.png)

![Снимок экрана страницы About после применения Bootstrap стилизации.](images/ch12-about-bootstrap.png)


### Категории в боковой панели
Категории в боковой панели пока никак не стилизованы. Если мы посмотрим на исходный HTML код для [страницы Dashboard](https://getbootstrap.com/docs/4.2/examples/dashboard/), то заметим, что несколько классов были добавлены к тегам `<ul>` and `<li>`, чтобы указать, что они являются nav-items и nav-links соответственно. Поэтому обновите шаблон, как показано ниже.

{lang="html",linenos=off}
	<ul class="nav flex-column">
	{% if cats %}
	    {% for c in cats %}
	    {% if c == act_cat %}
	        <li  class="nav-item">
	              <a  class="nav-link active" href="{% url 'rango:show_category' c.slug %}">
					  <span data-feather="archive"></span>
					  {{ c.name }}</a>
	        </li>
	    {% else  %}
	        <li class="nav-item">
	            <a  class="nav-link" href="{% url 'rango:show_category' c.slug %}">
					<span data-feather="archive"></span>{{ c.name }}</a>
	        </li>
	    {% endif %}
	{% endfor %}
	{% else %}
	    <li class="nav-item">No Categories Yet!</li>
	{% endif %}
	</ul>

Кроме того вместо того, чтобы использовать `<strong>` для выбранной в настоящий момент категории, мы добавили класс `active` к активной категории. Мы также добавили `feather-icon`, используя тег `<span. ... >`. Здесь мы выбрали иконку `archive`, но Вы можете выбрать любую поправившуюся Вам на странице Feather Icons (https://feathericons.com/).

### Главная страница
Для главной страницы неплохо было бы показать самые популярные категорри и страницы в двух отдельных столбцах, а заголовок расположить вверху.

Глядя на примеры с сайта Bootstrap, мы видим, что в примере [Jumbotron](https://getbootstrap.com/docs/4.2/examples/jumbotron/) есть четко выделенный элемент хедера (т. е., jumbotron), в который мы можем поместить наш заголовок. И поэтому мы можем обновить index.html следующим образом:
 
 {lang="html",linenos=off}
 	<div class="jumbotron p-4">
 	<div class="container">
  		<h1 class="jumbotron-heading">Rango says...</h1>
 		<div>
 			<h2 class="h2">
 				{% if user.is_authenticated %}
 		 	   		Howdy {{ user.username }}!
 			   {% else %}
 	    	   		Hey there partner!
 			   {% endif %}
 			</h2>
         	<strong>{{ boldmessage }}</strong>
     	</div>
 	</div>
 	</div>
	
Возможно Вы заметили, что после `jumbotron` мы добавили `p-4`. Класс `p-4` определяет отступы (https://getbootstrap.com/docs/4.2/utilities/spacing/) вокруг `jumbotron`. Попробуйте изменить класс на  `p-6` или `p-1`. Вы также можете управлять отступами сверху, снизу, слева и справа с помощью классов `pt`, `pb`, `pr` и `pl`.
  
X> ### Упражнение
X> - Обновите все другие шаблоны шаблоны, чтобы заголовок страницы находился внутри `jumbotron`.
X> Это сделает внешний вид приложения единообразным.
  
Затем, чтобы создать две колонки, мы воспользуемся примером [Альбом](https://getbootstrap.com/docs/4.2/examples/album/). Хотя в нём три колонки, называемые карточками, нам нужно только две. Большинство, если не все CSS-фреймворки используют [сетку](https://getbootstrap.com/docs/4.2/layout/grid/), состоящую из 12 блоков. Если Вы просмотрите исходный HTML код для страницы Альбом, то увидите, что внутри строки расположен `<div>`, который определяет размер карточки `<div class="col-md-4">`, после которого следует `<div class="card mb-4 shadow-sm">`. Эти классы задают длину каждой карточки в 4 условные единицы относительно ширины экрана (а 4 на 3 равно 12). Поскольку нам нужны две карточки (одна для самых популярных страниц, а вторая - для самых популярных категорий), мы можем изменить 4 на 6 (т. к. 2 на 6 равно 12). Таким образом, обновите `index.html` добавив в него следующий HTML код.

{lang="html",linenos=off}
	 <div class="container">
	 <div class="row">
	 <div class="col-md-6">
	 <div class="card mb-6">
	 <div class="card-body">
	 	<h2 >Most Liked Categories</h2>
	    <p class="card-text">
	 	{% if categories %}
	 		<ul>
	 	  	{% for category in categories %}
	 	  	<li>
	 	  	<a href="{% url 'rango:show_category' category.slug %}">{{ category.name }}</a>
	 	  	</li>
	 	  	{% endfor %}
	 	  	</ul>
	 	  	{% else %}
	 	  	<strong>There are no categories present.</strong>
	 	  	{% endif %}
	 	</p>
	</div>
	</div>
	</div>
	<div class="col-md-6">
	<div class="card mb-6">
	<div class="card-body">
		<h2>Most Viewed Pages</h2>
	    <p class="card-text">
	 	{% if pages %}
	 	<ul>
	 		{% for page in pages %}
	 		<li>
	 		<a href="{{ page.url }}">{{ page.title }}</a>
	 		</li>
	 		{% endfor %}
	 		</ul>
	 		{% else %}
	 		<strong>There are no pages present.</strong>
	 		{% endif %}
	        </p>
	</div>
	</div>
	</div>
	</div>
	</div>
 
После того, как Вы обновили шаблон, перезагрузите страницу - теперь она должна выглядеть намного лучше, но то как отображаются элементы списка выглядит ужасно.

Давайте используем [стили для списков, предоставляемые Bootstrap](https://getbootstrap.com/docs/4.2/components/list-group/), чтобы улучшить их внешний вид. Мы можем сделать это довольно легко, изменив элементы `<ul>` на `<ul class="list-group">` и `<li>` - на `<li class="list-group-item">`. Перезагрузите страницу, смотрится лучше, не правда ли?

![Снимок экрана главной страницы с Jumbotron и колонками.](images/ch12-styled-index.png)

### Страница входа в систему
Теперь давайте рассмотрим страницу входа в систему. На сайте Bootstrap видно, что они уже создали [хорошую форму для входа в систему](https://getbootstrap.com/docs/4.2/examples/signin/). Если просмотреть код страницы, то Вы заметите множество классов, которые нужно добавить для стилизации базовой формы для входа в систему. Обновите `body_block` в шаблоне `login.html` следующим образом:

{lang="html",linenos=off}
	{% block body_block %}
	<link href="https://getbootstrap.com/docs/4.0/examples/signin/signin.css"
	    rel="stylesheet">
	<div class="jumbotron p-4">
	    <h1 class="jumbotron-heading">Login</h1>
	</div>
	<div class="container">
		
	<form class="form-signin" role="form" method="post" action=".">
	    {% csrf_token %}
	    <h2 class="form-signin-heading">Please sign in</h2>
	    <label for="inputUsername" class="sr-only">Username</label>
	    <input type="text" name="username" id="id_username" class="form-control" 
	           placeholder="Username" required autofocus>
	    <label for="inputPassword" class="sr-only">Password</label>
	    <input type="password" name="password" id="id_password" class="form-control"
	           placeholder="Password" required>
	    <button class="btn btn-lg btn-primary btn-block" type="submit" 
	            value="Submit" />Sign in</button>
	</form>

	</div>
	{% endblock %}

Кроме добавления ссылки на файл `signin.css` Bootstrap и ряда изменений в классах, связанных с элементами, мы удалили код, который автоматически генерирует форму для входа в систему, т. е. `form.as_p`. Вместо этого, мы использовали её элементы и что важно `id` создаваемых элементов и связали их с элементами, которые используются в Bootstrap форме. Чтобы узнать значения `id` мы запустили приложение Rango, перешли на страницу и затем открыли исходный код, чтобы увидеть, какой HTML-код был создан с помощью тега шаблона `form.as_p`.

Кнопке были присвоены классы `btn` и `btn-primary`. Если Вы обратитесь к [Bootstrap разделу по кнопкам](https://getbootstrap.com/docs/4.2/components/buttons/), то увидите, что существует множество различных цветов, размеров и стилей, которые могут быть назначены кнопкам, смотри 

![Снимок экрана страницы входа в систему с измененной нами Bootstrap стилизацией.](images/ch12-styled-login.png)

### Другие шаблоны, использующие формы
Вы можете проделать аналогичные изменения с шаблонами `add_cagegory.html` и `add_page.html`. Шаблон `add_page.html` мы можем модифицировать следующим образом:

{lang="html",linenos=off}
	{% extends "rango/base.html" %}
	{% block title %}Add Page{% endblock %}
	
	{% block body_block %}
		<div class="jumbotron p-4">
			<div class="container">
		 		<h1 class="jumbotron-heading">Add Page to {{category.name}}</h1>
			</div>
		</div>

		<div class="container">
			<div class="row">
		       <form role="form" id="page_form" method="post" 
		             action="/rango/category/{{category.slug}}/add_page/">
		       {% csrf_token %}
		       {% for hidden in form.hidden_fields %}
		           {{ hidden }}
		       {% endfor %}
		       {% for field in form.visible_fields %}
		           {{ field.errors }}
		           {{ field.help_text }}<br/>
		           {{ field }}<br/>
				   <div class="p-2"></div>
		       {% endfor %}
		       <br/>
		       <button class="btn btn-primary"
		               type="submit" name="submit">
		           Add Page
		       </button>
			   <div class="p-5"></div>
		       </form>
		   </div>
		</div>
	{% endblock %}

X> ### Упражнение 
X> - Создайте подобный шаблон для страницы Добавить категорию под названием `add_category.html`.

### Шаблон страницы для регистрации
Для `registration_form.html`, мы можем изменить форму следующим образом:

{lang="python",linenos=off}
    {% extends "rango/base.html" %}
    {% block body_block %}

	<div class="jumbotron p-4">
		<div class="container">
	 		<h1 class="jumbotron-heading">Register Here</h1>
		
	</div>
	</div>

	<div class="container">
		<div class="row">

			<div class="form-group" >
		    <form role="form"  method="post" action=".">
		        {% csrf_token %}
		        <div class="form-group" >
		        <p class="required"><label class="required" for="id_username">
		            Username:</label>
		            <input class="form-control" id="id_username" maxlength="30" 
		                 name="username" type="text" />
		            <span class="helptext">
		            Required. 30 characters or fewer. Letters, digits and @/./+/-/_ only.
		            </span>
		        </p>
		        <p class="required"><label class="required" for="id_email">
		            E-mail:</label>
		            <input class="form-control" id="id_email" name="email" 
		                 type="email" />
		        </p>
		        <p class="required"><label class="required" for="id_password1">
		            Password:</label>
		            <input class="form-control" id="id_password1" name="password1"
		                type="password" />
		        </p>
		        <p class="required">
		            <label class="required" for="id_password2">
		                Password confirmation:</label>
		            <input class="form-control" id="id_password2" name="password2" 
		                 type="password" />
		            <span class="helptext">
		                 Enter the same password as before, for verification.
		            </span>
		        </p>
		        </div>
		        <button type="submit" class="btn btn-primary">Submit</button>
		    </form>
		</div>
		
	</div>
	</div>
	
    {% endblock %}

Опять мы вручную преобразовали форму, созданную тегом шаблона `{{ form.as_p }}` и добавили различные Bootstrap классы.

W> ### Bootstrap, HTML и Django  - решение на скорую руку
W> Предложенный нами вариант - не лучшее решение проблемы - но достаточное для решения поставленной задачи.
W> Код будет выглядеть намного лучше и понятнее, если мы сможем указать Django, что при создании HTML кода для формы нужно подставить соответствующие классы. Но как это сделать Вы должны выяснить сами :-)

### Что дальше?
В этой главе мы описали как быстро стилизовать Ваше приложение Django с помощью Bootstrap. Bootstrap обладает большими возможностями и позволяет относительно легко менять темы - перейдите на [сайт StartBootstrap](http://startbootstrap.com/), где приведен целый ряд бесплатных тем. В качестве альтернативы вы можете использовать другой CSS фреймворк, например: [Zurb](http://zurb.com), [Gridism](http://cobyism.com/gridism/), [Pure](https://purecss.io) или [GroundWorkd](https://groundworkcss.github.io/groundwork/). Теперь, когда у вас есть представление о том, как работать с шаблонами и настраивать их для работы с адаптивным CSS фреймворком, мы можем вернуться к работе над самим приложением и добавить в него дополнительные функциональные возможности, объединяющие приложение в единое целое.

![Снимок экрана страницы регистрации с измененной нами Bootstrap стилизацией.](images/ch12-styled-register.png)

X> ### Упражнение, связанное с использованием другого фреймворка
X> Хотя в этом учебном пособии используется Bootstrap, в качестве дополнительного, необязательного упражнения, Вы можете стилизовать приложение Rango, используя один из других адаптивных CSS фреймворков. Если Вы создадите свой собственный стиль для приложения, дайте нам знать, чтобы мы могли привести ссылку на него и показать другим как Вы улучшили внешний вид приложения Rango!
