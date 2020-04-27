---
layout: post
title: Простой Crud на nestjs
categories: 
- nestjs
---

Разбираюсь с модной в наших краях темой Nestjs
Делаем простое CRUD-приложение со swagger

---
## Общие сведения о nestjs

Ссылку давать особого смысла нет, она нагугливается моментально. 
Переписывать wiki тож смылса нет.

Мне nestjs понравился в первую очередь тем, что синтаксис очень похож на angular, что хорошо. В отличии от hapi.js,
у которого свои конструкции.

Есть мнение, что nest гораздо производительнее, чем альтернативы, и у него внутри всем известный express.
Хотя есть возможность подключить что-то альтернативное. Пока не буду заморачиваться.

## Каракас приложения

Начинаем приложение стандартно, создаем новую папку и устанвливаем nest cli
>npm i -g @nestjs/cli

После установки можно прверить версию, командой 
>nest --version   в моем случае - 7.1.2 

Далее, создаем каркас приложения командой
>nest new <название>

В ходе работы система спрашивае, какой manager будем использовать, я выбрал npm
Ставится очень долго. 
После окончания установки каркаса приложения делаем первый коммит и запускаем в режиме разработки **npm run start:dev**
Получаем "hello world", каркас приложения установлен.

## Создаем моудль 
Для начала создадим модуль **user**, сервис для него и контроллер.

>nest generate module user

>nest generate service seervice/user

>nest generate controller controller/user

Прописываем необходимые зависимости в user.module.ts 
>providers: [UserService]

>controllers: [UserController] 

## Маршрут
### GET

Создаем простейший маршрут для проверки работы.
привожу только чать контроллера **controller/user.controller.ts**

####Javascript　

```javascript
import { Get, HttpCode } from '@nestjs/common';
import { User } from 'src/user/user.entity';
import { UserService } from 'src/service/user/user.service';

@Controller('user')
export class UserController {

  constructor(private userService: UserService) { }

  @HttpCode(200)
  @Get()
  index(): Promise<User[]> {

    return this.userService.findAll();
  }
```



