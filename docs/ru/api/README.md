---
sidebar: auto
---

# Справочник API

## Vuex.Store

```js
import Vuex from 'vuex';

const store = new Vuex.Store({ ...options });
```

## Опции конструктора Vuex.Store

### state

* тип: `Object | Function`

  Корневой объект состояния хранилища Vuex. [Подробнее](../guide/state.md)

  Если вы передаёте функцию, возвращающую объект, то возвращаемый объект будет использован в качестве корневого состояния. Это может быть полезным, если вы хотите повторно использовать объект состояния, особенно при повторном использовании модулей. [Подробнее](../guide/modules.md#повторное-испоnьзование-модуnей)

### mutations

* тип: `{ [type: string]: Function }`

  Регистрирует доступные для хранилища мутации. Обработчики мутаций первым аргументом всегда получают `state` (при использовании модулей это будет локальный state модуля). Вторым аргументом передаётся "нагрузка" (`payload`), если она есть.

  [Подробнее](../guide/mutations.md)

### actions

* тип: `{ [type: string]: Function }`

  Регистрирует действия хранилища. В функции-обработчики передаётся объект `context`, со следующими свойствами:

  ```js
  {
    state,       // то же, что и `store.state`, или локальный state при использовании модулей
    rootState,   // то же, что и `store.state`, только при использовании модулей
    commit,      // то же, что и `store.commit`
    dispatch,    // то же, что и `store.dispatch`
    getters,     // то же, что и `store.getters`
    rootGetters; // то же, что и `store.getters`, только в модулях
  }
  ```

  И также получает вторым аргументом `payload` если нагрузка была передана.

  [Подробнее](../guide/actions.md)

### getters

* тип: `{ [key: string]: Function }`

  Регистрирует геттеры, используемые в хранилище. Геттер-функции при вызове получают следующие аргументы:

  ```
  state,     // при использовании модулей — локальный state модуля
  getters    // то же, что и store.getters
  ```

  При определении в модуле

  ```
  state,       // при использовании модулей — локальный state модуля
  getters,     // локальные геттеры текущего модуля
  rootState,   // глобальный state
  rootGetters  // все геттеры
  ```

  Зарегистрированные геттеры далее доступны в `store.getters`.

  [Подробнее](../guide/getters.md)

### modules

* тип: `Object`

  Объект, содержащий подмодули для помещения в хранилище, в формате:

  ```js
  {
    key: {
      state,
      namespaced?,
      mutations?,
      actions?,
      getters?,
      modules?
    },
    ...
  }
  ```

  Каждый модуль может содержать `state` и `mutations`, как и корневое хранилище. Состояние модуля будет прикреплено к корневому, по указанному ключу. Мутации и геттеры модуля получают при вызове первым аргументом только локальное состояние, а не корневое. При вызове действий `context.state` аналогичным образом указывает на локальное состояние модуля.

  [Подробнее](../guide/modules.md)

### plugins

* тип: `Array<Function>`

  Массив функций-плагинов, которые будут применены к хранилищу. Плагины попросту получают хранилище в качестве единственного аргумента, и могут как отслеживать мутации (для сохранения исходящих данных, логирования или отладки) или инициировать их (для обработки входящих данных, например, веб-сокетов или наблюдателей).

  [Подробнее](../guide/plugins.md)

### strict

* тип: `Boolean`
* по умолчанию: `false`

  Заставляет хранилище Vuex использовать strict mode. В strict mode любые изменения состояния, происходящие за пределами обработчиков мутаций, будут выбрасывать ошибки.

  [Подробнее](../guide/strict.md)

## Свойства экземпляра Vuex.Store

### state

* тип: `Object`

  Корневое состояние. Только для чтения.

### getters

* тип: `Object`

  Зарегистрированные геттеры. Только для чтения.

## Методы экземпляра Vuex.Store

### commit

* `commit(type: string, payload?: any, options?: Object)`
* `commit(mutation: Object, options?: Object)`

Запускает мутацию. `options` может содержать опцию `root: true` что разрешает совершать корневые (root) мутации [в модулях со своим пространством имён](../guide/modules.md#пространства-имён). [Подробнее](../guide/mutations.md)

### dispatch

* `dispatch(type: string, payload?: any, options?: Object)`
* `dispatch(action: Object, options?: Object)`

Инициирует действие. `options` может содержать опцию `root: true` что разрешает совершать корневые (root) действия [в модулях со своим пространством имён](../guide/modules.md#пространства-имён). Возвращает Promise который разрешает все обработчики инициируемых действий. [Подробнее](../guide/actions.md)

### replaceState

* `replaceState(state: Object)`

Позволяет заменить корневое состояние хранилища. Используйте только для гидрации состояния / функциональности "машины времени".

### watch

* `watch(fn: Function, callback: Function, options?: Object): Function`

Реактивно отслеживает возвращаемое значение `fn`, и вызывает коллбэк в случае изменений. `fn` получает состояние хранилища первым аргументом, и геттеры вторым аргументом. Принимает опциональный объект с настройками, с такими же параметрами как и у метода Vue `vm.$watch`.

Для прекращения наблюдения, необходимо вызвать возвращаемую функцию обработчик.

### subscribe

* `subscribe(handler: Function): Function`

Подписывается на мутации хранилища. Обработчик `handler` вызывается после каждой мутации и получает в качестве параметров дескриптор мутации и состояние после мутации:

```js
store.subscribe((mutation, state) => {
  console.log(mutation.type);
  console.log(mutation.payload);
});
```

Для остановки слежения необходимо вызвать возвращаемую функцию.

Чаще всего используется в плагинах. [Подробнее](../guide/plugins.md)

### subscribeAction

* `subscribeAction(handler: Function): Function`

> Добавлено в версии 2.5.0

Подписывается на действие хранилища. Обработчик `handler` вызывается после каждого действия и получает в качестве параметров дескриптов действия и текущее состояние хранилища:

```js
store.subscribeAction((action, state) => {
  console.log(action.type);
  console.log(action.payload);
});
```

Для остановки слежения необходимо вызвать возвращаемую функцию.

Чаще всего используется в плагинах. [Подробнее](../guide/plugins.md)

### registerModule

* `registerModule(path: string | Array<string>, module: Module, options?: Object)`

Регистрирует динамический модуль. [Подробнее](../guide/modules.md#динамическая-регистрация-модуnей)

`options` может иметь опцию `preserveState: true`, что позволяет сохранить предыдущее состояние. Полезно с рендерингом на стороне сервера.

### unregisterModule

* `unregisterModule(path: string | Array<string>)`

Разрегистрирует динамический модуль. [Подробнее](../guide/modules.md#динамическая-регистрация-модуnей)

### hotUpdate

* `hotUpdate(newOptions: Object)`

Осуществляет горячую замену действий и мутаций. [Подробнее](../guide/hot-reload.md)

## Вспомогательные функции для компонентов

### mapState

* `mapState(namespace?: string, map: Array<string> | Object<string | function>): Object`

Создаёт проксирующие вычисляемые свойства компонента, возвращающие поддерево state'а хранилища Vuex [Подробнее](../guide/state.md#вспомогатеnьная-функция-mapstate)

Первый аргумент опционально может быть строкой с указанным namespace. [Подробнее](../guide/modules.md#подкnючение-с-помощью-вспомогатеnьных-функций-к-пространству-имён)

Второй аргумент вместо объекта может быть функцией. `function(state: any)`

### mapGetters

* `mapGetters(namespace?: string, map: Array<string> | Object<string>): Object`

Создаёт проксирующие вычисляемые свойства компонента, проксирующие доступ к геттерам. [Подробнее](../guide/getters.md#вспомогатеnьная-функция-mapgetters)

Первый аргумент опционально может быть строкой с указанным namespace. [Подробнее](../guide/modules.md#подкnючение-с-помощью-вспомогатеnьных-функций-к-пространству-имён)

### mapActions

* `mapActions(namespace?: string, map: Array<string> | Object<string | function>): Object`

Создаёт проксирующие методы компонента, позволяющие диспетчеризировать действия. [Подробнее](../guide/actions.md#диспетчеризация-действий-в-компонентах)

Первый аргумент опционально может быть строкой с указанным namespace. [Подробнее](../guide/modules.md#подкnючение-с-помощью-вспомогатеnьных-функций-к-пространству-имён)

Второй аргумент вместо объекта может быть функцией. `function(dispatch: function, ...args: any[])`

### mapMutations

* `mapMutations(namespace?: string, map: Array<string> | Object<string | function>): Object`

Создаёт проксирующие методы компонента, позволяющие инициировать мутации. [Подробнее](../guide/mutations.md#вызов-мутаций-в-компонентах)

Первый аргумент опционально может быть строкой с указанным namespace. [Подробнее](../guide/modules.md#подкnючение-с-помощью-вспомогатеnьных-функций-к-пространству-имён)

Второй аргумент вместо объекта может быть функцией. `function(commit: function, ...args: any[])`

### createNamespacedHelpers

* `createNamespacedHelpers(namespace: string): Object`

Создаёт вспомогательные функции для связывания с компонентами для указанного пространства имён. Возвращаемый объект содержит `mapState`, `mapGetters`, `mapActions` и `mapMutations`, которые связаны с указанным пространством имён. [Подробнее](../guide/modules.md#подкnючение-с-помощью-вспомогатеnьных-функций-к-пространству-имён)
