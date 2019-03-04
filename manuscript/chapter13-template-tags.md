# Теги шаблонов

## Выдаем список категорий на каждой странице

Хотелось бы иметь возможность показывать различные категории, которые пользователь может просматривать в боковой панели на каждой странице. С учетом того, что мы изучили, можно было бы поступить следующим образом:

-   В шаблоне `base.html` добавить код, отображающий список категорий, если он был передан ему.
-   Затем в каждом представлении мы могли обращаться к объекту Category, получать все категории и передавать их в словарь контекста.

Но это довольно примитивное решение. Оно требует большого числа операций вырезания и вставки кода. Кроме того, возникнет проблема с отображением категорий на страницах, обслуживаемых пакетом django-registration-redux. Из-за этого необходим другой подход, использующий `теги шаблонов`, которые добавляются в шаблон и запрашивают необходимые данные.

### Использование тегов шаблонов

Создайте каталог `rango/templatetags`, , а в нем два файла - один назовите `__init__.py`, и оставьте его пустым, а второй -- `rango_extras.py`, в который добавьте следующий код:

{lang="python",linenos=off}
	from django import template
	from rango.models import Category

	register = template.Library()

	@register.inclusion_tag('rango/cats.html')
	def get_category_list():
    	return {'cats': Category.objects.all()}

Как видите, мы создали метод под названием `get_category_list()`, который возвращает список категорий, и связали его с шаблоном `rango/cats.html`. Теперь создайте шаблон `rango/cats.html` в каталоге с шаблонами и вставьте в него следующий код:

{lang="html",linenos=off}
	<ul class="nav nav-sidebar">
	{% if cats %}
		{% for c in cats %}             
			<li><a href="{% url 'category'  c.slug %}">{{ c.name }}</a></li>
		{% endfor %}      
	{% else %}
		<li> <strong >There are no category present.</strong></li>	
	{% endif %}  
	</ul> 
	
Теперь в Вашем базовом шаблоне `base.html` Вы можете обратиться к созданному тегу шаблона, сначала загрузив его с помощью команды `{% load rango\_extras %}`, а затем разместив его на странице в виде `{% get\_category\_list %}`, т. е.:  

{lang="html",linenos=off}      
	<div class="col-sm-3 col-md-2 sidebar">          
		{% block side_block %}         
		{% get_category_list %}         
		{% endblock %}      
	</div>   
	

I> Перезагрузите сервер, чтобы зарегистрировать новые теги
I>
I> Вам нужно будет перезапускать Ваш сервер каждый раз при модификации тегов шаблонов. В противном случае они не будут зарегистрированы в системе и Вы будете искать причину почему Ваш код не работает.

### Теги шаблонов с параметрами

Now lets extend this so that when we visit a category page, it highlights which category we are in. To do this we need to paramterise the templatetag. So update the method in `rango\_extras.py` to be: 

{lang="python",linenos=off}
       def get_category_list(cat=None):         
	   return {'cats': Category.objects.all(), 'act_cat': cat}  


This lets us pass through the category we are on. We can now update the `base.html` to pass through the category, if it exists.  

{lang="html",linenos=off}

	<div class="col-sm-3 col-md-2 sidebar">          
	{% block side_block %}         
	{% get_category_list category %}         
	{% endblock %}      
	</div>   
	
Now update the`cats.html` template:   

{lang="html",linenos=off}
	{% for c in cats %}
		{% if c == act_cat %} 
			<li  class="active" > 
		{% else  %} 
			 <li>
		{% endif %}
			<a href="{% url 'category'  c.slug %}">{{ c.name }}</a></li>
	{% endfor %}


Here we check to see if the category being displayed is the same as the category being passed through (i.e.`act\_cat`), if so, we assign the`active`class to it from Bootstrap (http://getbootstrap.com/components/#nav).   Restart the development web server, and now visit the pages. We have passed through the `category` variable. When you view a category page, the template has access to the `category` variable, and so provides a value to the template tag `get\_category\_list()`. This is then used in the `cats.html` template to select which category to highlight as active.
