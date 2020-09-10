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
 - Доступ к дочернему через @VievChild();
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
export class ParentComponent {
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

Дочерний компонент интересен нам сейчас в следующем плане:
**child.component.ts**

{% highlight js %}
{% raw %}
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-child',
  templateUrl: './child.component.html',
  styleUrls: ['./child.component.css']
})
export class ChildComponent {
  user = {};
  
  setUser() {
    this.user = { name: 'UserName' };
  }
}
{% endraw %}
{% endhighlight %}

Основной момент - в **parent.component.html** добавлена переменная **#appUser** через которую будем получать доступ к внутреннему миру дочернего компонента.
Теперь можно в родительском компоненте на кнопку добавить вызов функции дочернего компонента.
><button (click)='**appUser**.setUser()'>Set User</button>

При нажатии на кнопку произойдет вызов метода дочернего компонента **setUser()** и отобразится user.name в родительском компоненте.
><p>{{appUser.user.name}}</p>

Это простой способ взаимодействия, но доступа из родительского компонента в дочерний нет, в этом заключается основное ограничение. 

## Доступ к дочернему через @VievChild()
Немного изменим содержимое родительского компоненита

**parent.component.ts**

{% highlight js %}
{% raw %}
import { Component, ViewChild } from '@angular/core';
import { ChildComponent } from './child/child.component';

@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html',
  styleUrls: ['./parent.component.css']
})
export class ParentComponent {
  @ViewChild(ChildComponent, {static: false})
  private childComponent: ChildComponent;
  
setUser(){
    this.childComponent.setUser();
  }
}
{% endraw %}
{% endhighlight %}

Шаблон тоже слегка изменим
**parent.component.html**

{% highlight js %}
{% raw %}
<button (click)='setUser()'>Set User</button>
<p>{{childComponent && childComponent.user.name}}</p>
<app-child></app-child>
{% endraw %}
{% endhighlight %}

Дочерний компонент оставляем без изменений.

Такой способ дает прямой доступ через @ViewChild от родительского копомпонента к дочернему, вызывается напрямую метод **this.childComponent.setUser();**

## Взаимодействие через общий сервис.
Основная идея - создать общий сервис и отправлять Observable данные из дочернего компонента. Родитель будет подписан на прослушивание, чтобы получать последние изменения данных.

**user.service.ts**

{% highlight js %}
{% raw %}
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class UserService {
  private userSource = new Subject();
  userSource$ = this.userSource.asObservable();
  
  notifyUser(user) {
    this.userSource.next(user);
  }
}
{% endraw %}
{% endhighlight %}


Родительский компонент изменим следующим образом

**parent.component.ts**
{% highlight js %}
{% raw %}
import { Component } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html',
  styleUrls: ['./parent.component.css']
})
export class ParentComponent {
  user = {};
  
  constructor(private userService: UserService) {
    userService.userSource$.subscribe(
      user => {
        this.user = user;
      });
  }
}
{% endraw %}
{% endhighlight %}

Шаблон тоже изменим, потому что родительский компопнент будет получать данные автоматически.
**parent.component.html**

{% highlight js %}
{% raw %}
<app-child></app-child>
<p>{{user.name}}</p>
{% endraw %}
{% endhighlight %}

Также, необходимо доработать и дочерний компонент.

**child.component.ts**

{% highlight js %}
{% raw %}
import { Component } from '@angular/core';
import { UserService } from '../user.service';

@Component({
  selector: 'app-child',
  templateUrl: './child.component.html',
  styleUrls: ['./child.component.css']
})
export class ChildComponent {
  constructor(private userService: UserService) { }
  
  setUser() {
    this.userService.notifyUser({ name: 'UserName' });
  }
}
{% endraw %}
{% endhighlight %}


В шаблоне дочернего компонента добавим вызов метода **setUser**

**child.component.html**

{% highlight js %}
{% raw %}
<button (click)='setUser()'>Set User</button>
{% endraw %}
{% endhighlight %}

При нажатии на кнопку в дочернем компоненте - родительский получает через сервис изменение значения переменной user.name и выводит его.
