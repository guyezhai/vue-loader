# Из чего состоит компонент Vue

Файл `*.vue` это собственный формат Vue, использующий HTML-подобный синтаксис для описания компонента. Каждый `*.vue` файл состоит из трёх главных секций: `<template>`, `<script>` и `<style>`, а также опциональных пользовательских блоков:

``` html
<template>
  <div class="example">{{ msg }}</div>
</template>

<script>
export default {
  data () {
    return {
      msg: 'Hello world!'
    }
  }
}
</script>

<style>
.example {
  color: red;
}
</style>

<custom1>
  Это может быть, например, документация для компонента
</custom1>
```

`vue-loader` парсит файл, извлекает каждую из секций, обрабатывает их при необходимости другими loader'ами, и в итоге собирает всё обратно в модуль CommonJS где `module.exports` будет объектом настроек компонента Vue.js.

`vue-loader` предоставляет возможность использовать нестандартные языки, например пре-процессоры CSS и языки шаблонов компилируемые в HTML просто указав атрибут `lang` для секции файла. Например, вы можете использовать SASS для стилей вашего компонента:

``` html
<style lang="sass">
  /* используем SASS! */
</style>
```

Подробнее на странице [использования пре-процессоров](../configurations/pre-processors.md).

### Секции файла

#### `<template>`

- Язык по умолчанию: `html`.

- Каждый `*.vue` файл может содержать максимум один блок `<template>`.

- Содержимое будет извлечено как строка и использовано как опция `template` для скомпилированного компонента Vue.

#### `<script>`

- Язык по умолчанию: `js` (ES2015 поддерживается автоматически при обнаружении `babel-loader` или `buble-loader`).

- Каждый `*.vue` файл может содержать максимум один блок `<script>`.

- Скрипт исполняется в окружении CommonJS (как обычный `.js` модуль, собранный с помощью Webpack), что позволяет вам использовать `require()` для подключения других зависимостей. А вместе с поддержкой ES2015, можно использовать также синтаксис `import` и `export`.

- Скрипт должен экспортировать объект с настройками компонента Vue.js. Экспортирование расширенного конструктора, созданного через `Vue.extend()`, также поддерживается, но просто объект предпочтительнее.

#### `<style>`

- Язык по умолчанию: `css`.

- Может быть несколько тегов `<style>` в одном `*.vue` файле.

- Тег `<style>` может иметь `scoped` или `module` атрибуты (подробнее — [Scoped CSS](../features/scoped-css.md) и [CSS модули](../features/css-modules.md)) для возможности инкапсулировать стили в текущий компонент. Несколько тегов `<style>` с разными режимами инкапсуляции могут быть использованы в одном компоненте.

- По умолчанию, содержимое будет извлечено и динамически вставлено в тег `<head>` документа обычным тегом `<style>` с помощью `style-loader`. Также возможно [настроить Webpack чтобы извлекать стили всех компонентов в один CSS-файл](../configurations/extract-css.md).

### Пользовательские блоки

> Доступно только в версиях vue-loader 10.2.0+

Дополнительные пользовательские блоки могут быть добавлены в `*.vue` файл для любых потребностей проекта, например блок `<docs>`. `vue-loader` будет использовать имя тега для определения какими загрузчиками Webpack требуется обрабатывать содержимое этой секции. Загрузчики Webpack должны быть указаны в секции `loaders` настроек `vue-loader`.

Подробнее в разделе [пользовательских блоков](../configurations/custom-blocks.md).

### Импорт содержимого из других файлов

Если вы предпочитаете разделять `*.vue` компоненты на несколько файлов, вы можете использовать атрибут `src` для импорта содержимого другого файла в секцию, например:

``` html
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

Имейте ввиду, что импорт через `src` следует тем же правилам определения пути к файлу, как и вызовы `require()` в CommonJS, что означает необходимость начинать относительные пути с `./`, и также возможность подключать ресурсы непосредственно из установленных NPM-пакетов, например:

``` html
<!-- импорт файла из установленного NPM пакета "todomvc-app-css" -->
<style src="todomvc-app-css/index.css">
```

`src` импорт также поддерживает пользовательские блоки, например:

``` html
<unit-test src="./unit-test.js">
</unit-test>
```

### Подсветка синтаксиса

На данный момент есть поддержка подсвестки синтаксиса для [Sublime Text](https://github.com/vuejs/vue-syntax-highlight), [Atom](https://atom.io/packages/language-vue), [Vim](https://github.com/posva/vim-vue), [Visual Studio Code](https://marketplace.visualstudio.com/items/liuji-jim.vue), [Brackets](https://github.com/pandao/brackets-vue), и [продуктов JetBrains](https://plugins.jetbrains.com/plugin/8057) (WebStorm, PhpStorm, и т.п.). Будем рады видеть разработки и для других редакторов и IDE! Если вы не используете никаких пре-процессоров в компонентах Vue, вы также можете использовать синтаксис HTML в вашем редакторе для `*.vue` файлов.

### Комментарии

Внутри каждой секции вы можете использовать синтаксис комментариев, который соответствует используемому языку (HTML, CSS, JavaScript, Pug, и т.д.). Для комментариев на уровне секций, используйте синтаксис комментариев HTML: `<!-- ваш комментарий -->`