---
layout: post
title: Взаимодействие между компонентами
categories: 
- angular
---

Закрепляю материал.
Взаимодействие между компонентами.

---
## Общие сведения о взаимодействии

В angular компоненты типа родительский-дочерний могут взаимодействовать по крайней мере тремя способами:
 - Родитель взаимодействует с дочерним через локальную переменную;
 - Доступ к дочернему черз @VievChild();
 - Взаимодействие через сервис.
 
 
## Взаимодействие через локальную переменную.

Самый простой способ.
Идея заключается в том, что при добавлении шаблона дочернего компонента добавляется еще переменная к шаблону компонента,
через которую родительский компоенент может получить доступ к дочернему.

Для простоты допустим, у нас есть **parent** и **child**

Родительский компонент состоит из:
**parent.component.ts**

{% highlight js %}
{% raw %}
import { Component } from '@angular/core';

@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html',
  styleUrls: ['./parent.component.css']
})
export class AppComponent {
  user = {};
}
{% endraw %}
{% endhighlight %}

**parent.component.html**

{% highlight js %}
{% raw %}
<app-child #appUser></app-child>
<button (click)='appUser.setUser()'>Set User</button>
<p>{{appUser.user.name}}</p>
{% endraw %}
{% endhighlight %}

 
