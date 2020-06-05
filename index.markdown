---
layout: default
title: magalhaescarlos
---

# Testando temas do jekyll

Lorem ipsum

## H2

```js

function hw(){
    console.log(`Hello world!`);
}
```

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>