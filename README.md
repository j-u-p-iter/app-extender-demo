# app.utils.extendApp

## Содержание
+ [Описание](https://github.com/j-u-p-iter/apress-application/blob/master/docs/utils/extend_app.md#Описание)
+ [Интерфейс](https://github.com/j-u-p-iter/apress-application/blob/master/docs/utils/extend_app.md#Интерфейс)
+ [Применение](https://github.com/j-u-p-iter/apress-application/blob/master/docs/utils/extend_app.md#Применение)
+ [Режим мягкого мёрджа](https://github.com/j-u-p-iter/apress-application/blob/master/docs/utils/extend_app.md#Режим-мягкого-мёрджа)

## Описание
Утилита для расширения единого кросс-проектного app-конфига.

```javascript
app.utils.extendApp.
`````

## Интерфейс.

### Может принимать два разных набора типов данных:

a) Путь к перезаписываемой части конфига и контент для данной части передаются в виде двух аргументов

1. `path: required`.

+ **Тип данных:** Строка.
+ **Описание:** Путь к расширяемой части конфига.

2. `content: required`

+ **Тип данных:** Объект.
+ **Описание:** Объект, содержащий пары ключ-значение для расширения конфига по пути `path`.

3. `isMergeSoft: optional`

+ **Тип данных:** Boolean.
+ **Описание:** Позволяет задать мягкий режим мёрджа, когда конфиг будет расширен только в случае, если в конфиге уже не присутстуют аналогичные значения.

Пример:

```javascript
app.utils.extendApp(path, content, isMergeSoft?);
````


b) Путь к перезаписываемой части конфига и контент для данной части передаются в составе единого объекта.

1. `configPart: required`.

+ **Тип данных:** Объект. Ключ - путь к перезаписываемой части конфига. Значение ключа - контент для записи по пути, указанному в ключе.
+ **Описание:** Объект для расширения существующего конфига.

2. `isMergeSoft: optional`

+ **Тип данных:** Boolean.
+ **Описание:** Позволяет задать мягкий режим мёрджа, когда конфиг будет расширен только в случае, если в конфиге уже не присутстуют аналогичные значения.

Пример:

```javascript
app.utils.extendApp(configPart, isMergeSoft?);
````

## Применение

Зачастую требуется расширить существующий проектный конфиг из гема или из определённой части проекта,
будь-то СК, кабинет и пр.

Допустим, мы имеем основной проектный конфиг:

```javascript
// Основной проектный конфиг
app = app || {
  config: {
    someKey: 'someValue',
    oneMoreKey: 'oneMoreValue',
    api: {
      someURL: 'http://someUrl.ru',
      oneMoreURL: 'http://oneMoreUrl.ru'
    }
  }
};
````

На сайте компании появляется необходимость расширить данный конфиг значениями,
которые известны только на СК.

Например, нам нужно добавить
````javascript
  companyKey: 'companyValue'
  oneMoreCompanyKey: 'oneMoreCompanyValue',
````

а также
````javascript
  api.companyURL: 'companyUrl'
  api.oneMoreCompanyURL: 'oneMoreCompanyUrl'
````

в наш конфиr.

Как можно поступить, и как мы сейчас и поступаем? Можно точечно задать нужные значения:

````javascript
app.config.companyKey = 'companyValue';
app.config.oneMoreCompanyKey = 'oneMoreCompanyValue';

app.config.api.companyURL = 'companyUrl';
app.config.api.oneMoreCompanyURL = 'oneMoreCompanyUrl';
````

Из недостатков - нам приходится дублировать app.config и app.config.api каждый раз, когда нам требуется добавить в
соответствующий неймспейс нужное значение. Это неудобно и при большом количестве подобных операций оборачивается ещё большим неудобством.

Почему мы не можем просто написать так:

`````javascript
app.config = {
  companyKey: 'companyValue',
  oneMoreCompanyKey: 'oneMoreCompanyValue'
}
`````

Потому что это перезапишет существующий app.config. И это, конечно же, некорректно.

Данная утилита позволяет расширять конфиг без дублей, при этом не перезаписывая существующий конфиг, а лишь добавляя новые значения в него:

```javascript
// Можно расширить каждый неймспейс по отдельности
app.utils.extendApp('app.config': {
  companyKey: 'companyValue',
  oneMoreCompanyKey: 'oneMoreCompanyValue'
});

app.utils.extendApp('app.config.api': {
  companyURL: 'companyUrl',
  oneMoreCompanyURL: 'oneMoreCompanyUrl'
});


// А можно расширить весь конфиг за один раз
app.utils.extendApp({
  'app.config': {
    companyKey: 'companyValue',
    oneMoreCompanyKey: 'oneMoreCompanyValue'
  },
  'app.config.api': {
    companyURL: 'companyUrl',
    oneMoreCompanyURL: 'oneMoreCompanyUrl'
  }
});
```````

## Режим мягкого мёрджа

Внимание! По дефолту, если в конфиге уже присутствуют значения, которые мы пытаемся присвоить, утилита перезапишет эти значения.

Если необходимо добавлять значения в конфиг только в том случае, если таковых ещё нет в данном конфиге - нужно передать в утилиту
дополнительный агрумент типа boolean - isMergeSoft.


```javascript
// В случае, когда в утилиту передаются путь к перезаписываемой части конфига и контент для данной части в виде двух аргументов,
// параметр мягкого мёрджа передаётся третьим аргументом:
app.utils.extendApp('app.config': {
  companyKey: 'companyValue',
  oneMoreCompanyKey: 'oneMoreCompanyValue'
}, true);

app.utils.extendApp('app.config.api': {
  companyURL: 'companyUrl',
  oneMoreCompanyURL: 'oneMoreCompanyUrl'
}, true);


// В случае, когда путь и контент передаются в составе единого объекта, параметр мягкого мёрджа передаётся вторым аргументом:
app.utils.extendApp({
  'app.config': {
    companyKey: 'companyValue',
    oneMoreCompanyKey: 'oneMoreCompanyValue'
  },
  'app.config.api': {
    companyURL: 'companyUrl',
    oneMoreCompanyURL: 'oneMoreCompanyUrl'
  }
}, true);
```````
