---
layout: post
title: Категории блога.
categories: 
- other
---

Github pages из каробки предоставляет функционал блога, которого в принципе достаточен, чтобы начать размещать статьи.
Так как мой бложик будет сочетать в себе статьи по разным тематикам, то логично было бы иметь удобный способ навигации, 
чтобы можно было посмотреть все статьи нужной тематики. А не рыться на главной странице.

---

Все статьи лежат в папке _post общим списком, он же отображается на главной странице.
В начале я хотел насоздавать в папке _post еще папок, раскидать контент по тематикам, а в шапке сайта сделать меню.
Но эту идею реализовать не удалось.

Для формирования списка контента в GH (далее будем так называть Github pages) есть понятие категорий.
Вот эти самые категории и позволяют фильтровать контент. Еще они являются частью url, например если мы завели категорию delphi, то url статьи блога, отнесенной к этой категории, будет примерно такой:
>https://cybermyv.github.io/delphi/2019/11/28/My-delphi-app.html


Из каробки этот функционал не работает, в сети огромное количество инструкция, как это все прикрутить к сайту. Но покопаться все-таки пришлось, потому что это ни разу не очевидный случай. 

Мне хотелось сделать все максимально просто.

## 1 - В корне сайта создаем каталог _categories в который кладем файл index.html

{% highlight html %}
{% raw %}
<!DOCTYPE html>
    <body>
      <main class="page-content" aria-label="Content">
      <div class="wrapper">
        <h3>Categories</h3>

          {{ content }}

        {%- for post in site.posts -%}
        {{ post.title }}
        {%- endfor -%}

      </div>
    </main>
    </body>
</html>
{% endraw %}
{% endhighlight %}

## 1.1 - В каталоге _categories создаем файлы категорий, например delphi.md. Соответствнно на каждую категорию нужен свой файл.

{% highlight html %}
{% raw %}
---
tag: delphi
permalink: "/categories/delphi"
---
{% endraw %}
{% endhighlight %}


## 2 - В каталоге _layouts создаем файл categories.html 

{% highlight html %}
{% raw %}
---
title: Categories
layout: page
---
<!DOCTYPE html>
<body>
<main class="page-content" aria-label="Content">
    <div class="wrapper">
        <div class="home">
    {% assign category = page.title|downcase %}
    {% for post in site.posts %}
        {% if post.categories contains {{category}}  %}
            <li><a href="{{post.url}}">{{ post.title }}</a></li>
        {% endif %}
    {% endfor %}
    {%- if site.posts.size > 0 -%}
    <h4 class="post-list-heading">{{ page.list_title }}</h4>
    <ul class="post-list">
        {%- for post in paginator.posts -%}
        <li>
            {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
            <span class="post-meta">{{ post.date | date: date_format }}</span>
            <h5>
                <a class="post-link" href="{{ post.url | relative_url }}">
                    {{ post.title | escape }}
                    {{ post.category }}
                </a>
            </h5>
        </li>
        {%- endfor -%}
    </ul>
    {%- endif -%}
</div>
{% endraw %}
{% endhighlight %}

## 3 - Слегка правим корневой index.html, добавляем в него вывод всек категорий 

{% highlight html %}
{% raw %}
---
layout: default
title: Home
---
{%- for category in site.categories -%}
  <a href="categories/{{ category.title | downcase}}.html">{{ category.title }}</a> |&nbsp
  {%- endfor -%}
<div class="posts">
  {% for post in site.posts %}
    <article class="post">
      <h1><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h1>
      <div class="entry">
        {{ post.excerpt }}
      </div>
      <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">Read More</a>
    </article>
  {% endfor %}
</div>
{% endraw %}
{% endhighlight %}

## 4 - Добавляем в _config.yml поддержку категорий и комментим строку permalink

{% highlight html %}
{% raw %}
# 
#permalink: /:categories/:title/
#

collections:
  categories:
    output: true
defaults:
  -
    scope:
      path: ""
      type: categories
    values:
      layout: "categories"
{% endraw %}
{% endhighlight %}
 
## 5 - в заголовки постов добавляем категории

{% highlight html %}
{% raw %}
---
layout: post
title: Категории блога
categories: 
- delphi
---
{% endraw %}
{% endhighlight %}

Категорий может быть несколько, размещать с новой строки. 
Если для статьи не нужно указывать категорию, то ставим пустые квадратные скобки.

{% highlight html %}
{% raw %}
---
layout: post
title: Категории блога
categories: []
---
{% endraw %}
{% endhighlight %}

Теперь список категорий отображается на главной странице, если выбрать категорию, то откроется новая страница, на которой отобразатся списком статьи с выбранными категориями.

## Итого

- Удалось разобраться с категориями.
- С блогом работать можно.
- Статья по настройке категорий написана.

-- Можно попробовать завести категорию на русском, посмотреть, что из этого получится. 

В целом, я доволен результатом.
