# Деплой Vue.js сайта на Netlify

Это краткая выжимка из документации — [](https://docs.netlify.com/cli/get-started/)

## Установка и настройка Netlify CLI

Устанавливаем Netlify CLI глобально:

```bash
npm install netlify-cli --global
```

Можно попробовать установить локально (`npm install netlify-cli --save-dev`), но у меня таким способ не работала команда `netlify`. Можно, конечно, запускать как `./node_modules/.bin/netlify`, но хочется красоты.

Получаем токен:

```bash
netlify login
```

Откроется браузер, где будет нужно авторизоваться. Возможно сначала будет нужно войти в свой аккаунт, тогда нужно будет запустить команду еще раз.

По дефолту Netlify собирает данные, какие команды Netlify CLI выполняются. Отключаем телеметрию:

```bash
netlify --telemetry-disable
```

## Публикуем новый сайт

Создаем новый сайт на Netlify. Для этого в корне проекта выполняем команду:

```bash
netlify init
```

Выбираем пункт:

```bash
...
+  Create & configure a new site
```

Далее:

- Выбираем команду, где опубликовать сайт
- Задаем имя сайта — для имени `sitename` адрес будет `sitename.netlify.app`
- Коннектимся к гитхабу
- Команда для сборки: `npm run build`
- Какую папку деплоить: `build`
- `netlify.toml` полезно создать, чтобы настроить редиректы для SPA, например.
