# Добавляем функциональные возможности: поиск с помощью стороннего API {#chapter-bing}
Теперь когда наше приложение Rango хорошо смотрится и большинство основных функциональных возможностей реализовано, мы можем реализовать некоторым более продвинутые функции. В этой главе мы подсоединим Rango к поисковому API, чтобы пользователи могли также искать страницы, а не только просматривать категории. Основная цель этой главы - показать вам, как Вы можете подключаться и использовать другие веб-сервисы - и как интегрировать их в свое собственное приложение.

Поисковое API, которое мы будем использовать - это API поисковой системы Microsoft Bing, но Вы можете также легко подключить API, предоставляемые [Webhose](https://webhose.io/) или [Yandex](https://yandex.com/support/search/robots/search-api.html) вместо него.

Чтобы использовать API поисковой системы Bing нам нужно написать [обертку](https://en.wikipedia.org/wiki/Adapter_pattern), которая позволит нам посылать запросы и получать результаты из API - и возвращать их обратно в более удобном формате. Но прежде чем мы сможем это сделать, нам сначала нужно настроить учетную запись Azure для использования поискового API Bing.

## API поисковой системы Bing
[API поисковой системы Bing](https://docs.microsoft.com/en-gb/rest/api/cognitiveservices/bing-web-api-v7-reference) позволяет Вам встраивать результаты поиска поисковой системы Bing в Ваше собственное приложение. С помощью простого интерфейса Вы можете запросить результаты поиска от Bing серверов в XML или JSON форме. Возвращаемые данные могут быть обработаны XML или JSON анализатором, а результат затем выведен как часть шаблона Вашего приложения.

Хотя Bing API может обрабатывать запросы различного вида, в этом учебном пособии мы сосредоточимся только на поиске, а также обработке JSON ответов. Для использования Bing Search API Вам надо зарегистрироваться, чтобы получить *API ключ*. В настоящее время ключ позволяет осуществлять подписчикам до 3000 запросов в месяц, что более чем достаточно для наших целей.

I> ### Программный Интерфейс Приложения (API)
I> [Программный Интерфейс Приложения](http://en.wikipedia.org/wiki/Application_programming_interface) определяет способ взаимодействия программных компонентов друг с другом. Для веб-приложений API является набор HTTP-запросов вместе с определением структур ответных сообщений от сервера, которые может возвращать каждый запрос. Любой более-менее полезный сервис, который может быть предложен через Интернет, может иметь свой собственный API - мы не ограничены только веб-поиском. Для получения дополнительной информации о веб API, [ознакомтесь с отличным руководством по API от Луиса Рея (Luis Rei)](http://blog.luisrei.com/articles/rest.html).

### Регистрируемся для получения ключа Bing API
Для получения Bing API ключа, Вы должны сначала зарегистрировать учетную запись Microsoft Azure. Учетная запись открывает доступ к широкому спектру услуг Microsoft. Если у Вас уже есть учетная запись Microsoft, Вам не нужно регистрироваться, а достаточно просто войти в неё. В противном случае, Вы можете войти в Интернет и создать бесплатную учетную запись Microsoft по адресу [`https://account.windowsazure.com`](https://account.windowsazure.com).

Когда ваша учетная запись будет создана, войдите в систему и перейдите на портал.

В левой части страницы вы должны увидеть список, а в верхней части списка - ссылку `+ Create a resource`. Нажмите на неё и затем выберите `AI + Machine Learning`. Пролистайте вниз и выберите опцию `Bing Search v7`, следуйте инструкциям и зарегистрируйтесь для получения услуги. Убедитесь, что вы выбрали ценовой пакет `F0 Free`, который позволяет Вам совершать 3000 запросов в месяц - этого более чем достаточно для наших целей.


{id="fig-bing-search"}
![API сервисы поисковой системы Bing - зарегистрируйтесь для получения 3000 транзакций в месяц бесплатно.
](images/ch14-bing-search-api.png)

Как только Вы зарегистрировались и добавили Bing Search v7 API, выберите сервис и нажмите на вкладку `Keys`. Здесь Вы сможете получить доступ к двум ключам, которыми Вы не должны делиться ни с кем.


{id="fig-bing-explore"}
![Информационная страница учетной записи. На этом снимке экрана, *Primary Account Key* (Первичный ключ учетной записи) скрыт специально. Вы тоже должны держать в тайне Ваш ключ!
](images/ch14-bing-account.png)

Предполагая, что у Вас всё получилось, скопируйте Ваш API ключ. Нам он понадобиться, когда мы будем создавать запросы как часть процесса аутентификации.

## Добавляем функцию поиска
Ниже приведен код, который мы можем использовать для создания запросов, посылаемых службе поисковой системы Bing. Создайте файл `rango/bing_search.py` и импортируйте следующий код. Вы заметите, что нам нужно импортировать пакет под названием `requests`, чтобы мы могли осуществлять запросы по сети. Но, чтобы использовать этот пакет, Вам нужно сначала запустить  `pip install requests`, чтобы установить его в нашем виртуальном окружении.

{lang="python",linenos=on}
	import json
	import requests

	# Добавьте свой ключ учетной записи Microsoft в файл с названием bing.key
	def read_bing_key():
		"""
		считывает ключ BING API из файла 'bing.key'
		возвращает: строку, которая равна или None, т. е., ключ не найден или ключу
		не забудьте поместить bing.key в Ваш файл .gitignore, чтобы избежать его добавления в репозиторий.
	
		Смотри Антипатерны Python - it is an awesome resource to improve your python code
		Here we using "with" when opening documents
		http://docs.quantifiedcode.com/python-anti-patterns/maintainability/not_using_with_to_open_files.html
		"""
	
		bing_api_key = None
		try:
			with open('bing.key','r') as f:
				bing_api_key = f.readline().strip()
			
		except:
			raise IOError('bing.key file not found')
		
		if not bing_api_key:
			raise KeyError('Bing key not found')	
	
		return bing_api_key

	def run_query(search_terms):
		"""
		See the Microsoft's documentation on other parameters that you can set.
		https://docs.microsoft.com/en-gb/rest/api/cognitiveservices/bing-web-api-v7-reference
		"""
		bing_key = read_bing_key()
		search_url = 'https://api.cognitive.microsoft.com/bing/v7.0/search'
		headers = {"Ocp-Apim-Subscription-Key" : bing_key}
		params  = {"q": search_terms, "textDecorations":True, "textFormat":"HTML"}
		response = requests.get(search_url, headers=headers, params=params)
		response.raise_for_status()
		search_results = response.json()
		results = []
		for result in search_results["webPages"]["value"]:
			results.append({
				'title': result['name'],
				'link': result['url'],
				'summary': result['snippet']})
		return results

In the module(s) above, we have implemented two functions: one to retrieve your Bing API key from a local file, and another to issue a query to the Bing search engine. Below, we discuss how both of the functions work.

### `read_bing_key()` - Reading the Bing Key {#section-bing-adding-key}
The `read_bing_key()` function reads in your key from a file called `bing.key`, located in your Django project's root directory (i.e. `<workspace>/tango_with_django/`). We have created this function because if you are putting your code into a public repository on GitHub for example, you should take some precautions to avoid sharing your API Key publicly. 

From the Azure website, take a copy of your *Account key* and save it into `<workspace>/tango_with_django/bing.key`. The key should be the only contents of the file - nothing else should exist within it. This file should NOT be committed to your GitHub repository. To make sure that you do not accidentally commit it, update your repository's `.gitignore` file to exclude any files with a `.key` extension, by adding the line `*.key`. This way, your key file will only be stored locally and you will not end up with someone using your query quota (or worse).
	
T> ### Keys and Rings
T>
T> Keep them secret, keep them safe!


### `run_query()` - Executing the Query
The `run_query()` function takes a query as a string, and returns the top ten results from Bing in a list that contains a dictionary of the result items (including the `title`, a `link`, and a `summary`). 

To summarise though, the logic of the `run_query()` function can be broadly split into six main tasks.

* First, the function prepares for connecting to Bing by preparing the URL that we'll be requesting.
* The function then prepares authentication, making use of your Bing API key. This is obtained by calling `read_bing_key()`, which in turn pulls your Account key from the `bing.key` file you created earlier.
* The response is then parsed into a Python dictionary object using the `json` Python package.
* We loop through each of the returned results, populating a `results` dictionary. For each result, we take the `title` of the page, the `link` or URL and a short `summary` of each returned result.
* The list of dictionaries is then returned by the function.

<!-->
Notice that results are passed from Bing's servers as JSON. This is because they by default return the results using JSON. 

Also, note that if an error occurs when attempting to connect to Bing's servers, the error is printed to the terminal via the `print` statement within the `except` block.
-->

I> ###Bing it on!
I> There are many different parameters that the Bing Search API can handle which we don't cover here. 
I> If you want to know more about the API check out the [Bing Search API Documentation](https://docs.microsoft.com/en-gb/rest/api/cognitiveservices/bing-web-api-v7-reference).

X> ### Exercises
X> Extend your `bing_search.py` module so that it can be run independently, i.e. running `python bing_search.py` from your terminal or Command Prompt. Specifically, you should implement functionality that:
X> 
X> - prompts the the user to enter a query, i.e. use `raw_input()`; and
X> - issues the query via `run_query()`, and prints the results.
X>
X> Update the `run_query()` method so that it handles network errors gracefully.


T> ### Подсказка
T> Add the following code, so that when you run `python bing_search.py` it calls the `main()` function:
T> 	
T> {lang="python",linenos=off}
T>		def main():
T>		    #insert your code here
T>		
T>		if __name__ == '__main__':
T>		    main()
T>
T> When you run the module explicitly via `python bing_search.py`, the `bing_search` module is treated as the `__main__` module, and thus triggers `main()`. However, when the module is imported by another module, then `__name__` will not equal `__main__`, and thus the `main()` function not be called. This way you can `import` it with your application without having to call `main()`.


## Добавляем поиск в Rango
Now that we have successfully implemented the search functionality module, we need to integrate it into our Rango app. There are two main steps that we need to complete for this to work.

-  We must first create a `search.html` template that extends from our `base.html` template. The `search.html` template will include a HTML `<form>` to capture the user's query as well as template code to present any results.
- We then create a view to handle the rendering of the `search.html` template for us, as well as calling the `run_query()` function we defined above.

### Добавляем шаблон для поиска
Давайте сначала создадим шаблон `rango/search.html`. Добавьте следующую HTML разметку, код шаблонов Django и Bootstrap классы.

{lang="html",linenos=on}
	{% extends 'rango/base.html' %}
	{% load staticfiles %}
	
	{% block title %} Search {% endblock %}
	
	{% block body_block %}
	<div>
	    <h1>Search with Rango</h1>
	    <br/>
	    <form class="form-inline" id="user_form" 
	          method="post" action="{% url 'rango:search' %}">
	        {% csrf_token %}
	        <div class="form-group">
	            <input class="form-control" type="text" size="50" 
	                   name="query" value="" id="query" />
	        </div>
	        <button class="btn btn-primary" type="submit" name="submit"
	                value="Search">Search</button>
	    </form>
	    
	    <div>
	        {% if result_list %}
	        <h3>Results</h3>
	        <!-- Display search results in an ordered list -->
	        <div class="list-group">
	        {% for result in result_list %}
	            <div class="list-group-item">
	                <h4 class="list-group-item-heading">
	                    <a href="{{ result.link }}">{{ result.title|safe|escape}}</a>
	                    </h4>
	                    <p class="list-group-item-text">{{ result.summary|safe|escape }}</p>
	            </div>
	        {% endfor %}
	        </div>
	        {% endif %}
	    </div>
	</div>	
	{% endblock %}

Вышеприведенный код шаблона выполняет две основные задачи.

- In all scenarios, the template presents a search box and a search buttons within a HTML `<form>` for users to enter and submit their search queries.
- If a `results_list` object is passed to the template's context when being rendered, the template then iterates through the object displaying the results contained within.
	
To style the HTML, we have made use of Bootstrap [jumbotron](https://getbootstrap.com/docs/4.2/components/jumbotron/), [list groups](https://getbootstrap.com/docs/4.2/components/list-group/), and [forms](https://getbootstrap.com/docs/4.2/components/forms/).

To render the title and summary correctly, we have used the `safe` and `escape` tags to inform the template that the `result.title` and `result.summary` should be rendered as is (i.e. as HTML).

In the view code, in the next subsection, we will only pass through the results to the template, when the user issues a query. Initially, there will be no results to show.

### Добавляем представление
With our search template added, we can then add the view that prompts the rendering of our template. Add the following `search()` view to Rango's `views.py` module.

{lang="python",linenos=off}	
	def search(request):
	    result_list = []
	    
	    if request.method == 'POST':
	        query = request.POST['query'].strip()
	        if query:
	            # Run our Bing function to get the results list!
	            result_list = run_query(query)
	    
	    return render(request, 'rango/search.html', {'result_list': result_list})
	
By now, the code should be pretty self explanatory to you. The only major addition is the calling of the `run_query()` function we defined earlier in this chapter. To call it, we are required to also import the `bing_search.py` module, too. Ensure that before you run the script that you add the following `import` statement at the top of the `views.py` module.

{lang="python",linenos=off}
	from rango.bing_search import run_query, read_bing_key

You'll also need to ensure you do the following, too.

- Add a mapping between your `search()` view and the `/rango/search/` URL calling it `name='search'` by adding in `path('search/', views.search, name='search'),` to `rango/urls.py`.
- Also, update the `base.html` navigation bar to include a link to the search page. Remember to use the `url` template tag to reference the link.
- You will need a copy of the `bing.key` in your project's root directory (`<workspace>/tango_with_django_project`, alongside `manage.py`).

Once you have put in the URL mapping and added a link to the search page, you should now be able issue queries to the Bing Search API and have the results shown within the Rango app (as shown in the figure below).

{id="fig-bing-python-search"}
![Searching for "Python for Noobs".](images/ch14-bing-python-search.png)

X> ### Дополнительное упражнение
X>
X> You may notice that when you issue a query, the query disappears when the results are shown. This is not very user friendly. Update the view and template so that the user's query is displayed within the search box.
X>
X> Within the view, you will need to put the `query` into the context dictionary. Within the template, you will need to show the query text in the search box.