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

Создаем заготовку для сущности user/user.entity.ts, потом подключим TypeORM.

{% highlight js %}
{% raw %}
export interface User {
    id: number;
    login: string;
    password: string;
    email: string;
    phone: string;
    description: string;    
}
{% endraw %}
{% endhighlight %}

Прописываем необходимые зависимости в user.module.ts 

{% highlight js %}
{% raw %}
import { Module } from '@nestjs/common';
import { UserService } from 'src/service/user/user.service';
import { UserController } from 'src/controller/user/user.controller';
import { User } from './user.entity'

@Module({
  imports: [
    TypeOrmModule.forFeature([User])
  ],
  providers: [UserService],
  controllers: [UserController]
})
export class UserModule {}
{% endraw %}
{% endhighlight %}

## Маршрут
### GET

Создаем простейший маршрут для проверки работы.
привожу только чать контроллера **controller/user.controller.ts**

{% highlight js %}
{% raw %}
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
{% endraw %}
{% endhighlight %}

## Установка swagger

Выполним 

>npm i --save @nest/swagger swagger-ui-express

Добавим в main.ts поддержку swagger (const options и const document)

{% highlight js %}
{% raw %}
import { NestFactory } from '@nestjs/core';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  const options = new DocumentBuilder()
  .setTitle('Пользователи')
  .setDescription('API пользователей')
  .setVersion('0.0.1')
  .addTag('user')
  .build()

  const document = SwaggerModule.createDocument(app, options);
  SwaggerModule.setup('api', app, document);
  
  
  await app.listen(3000);
}
bootstrap();
{% endraw %}
{% endhighlight %}

В контроллере **controller/user.controller.ts** добавляем ApiTags

{% highlight javascript %}
{% raw %}
import { ApiTags } from '@nestjs/swagger';

@ApiTags('user')
@Controller('user')
export class UserController {

  constructor(private userService: UserService) { }

  @HttpCode(200)
  @Get()
  index(): Promise<User[]> {
    return this.userService.findAll();
  }
}
{% endraw %}
{% endhighlight %}

в файле **nest-cli.json** необходимо добавить раздел "plugins"

{% highlight javascript %}
{% raw %}
{
  "collection": "@nestjs/schematics",
  "sourceRoot": "src",
  "compilerOptions": {
    "plugins": ["@nestjs/swagger/plugin"]
  }
}

{% endraw %}
{% endhighlight %}

Подготовка сваггера завершена

## Установка TypeORM и sqlite3

Почему sqlite? Это простая база данных, не требует каких либо движений по установке драйверов, поддерживается андроидом.
TypeOrm - объектно-реляционный маппер, который поддерживает кучу баз данных, обеспечивает интерфейс между базой данных и Node, иногда делает мир лучше, но это не точно. 

Ставим все это добро одной командой 

>npm i - -save @nest/typeorm typeorm sqlite3

В моем случае поставились следующие версии:

 @nest/typeorm  - 0.2.24
 
 typeorm        - 7.0.0  
 
 sqlite3        - 4.1.1  

