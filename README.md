- # Effector tutorial

https://effector.dev/ "Сайт эффектора"

#

# Наименование в effector

> Domain - это пространство имен. Относительно инициализированного домена можно создавать store, effect, event. Так же Домен может на события и эффекты.
>
> `const domain = createDomen()`
>
> Пример:
>
> `const $store = domain.createStore(dafaultState: any);` defaultDtate - значение по умолчанию для хранилища
>
> `const effectFx = domain.createEffect(name: string | handler: () => {});` name - наименование эффекта, handler - функция, срабатывающая во время вызова эффекта
>
> `const event = domain.createEvent(name: string)` name - наименование эвента

> Store - Основное хранилище (Название хранилища принято начинать со знака "$")
>
> `const $store = createStore<type of store>()`

> Effect - Это контейнер для эффектов, может быть ассинхнонным (Название эффекта принято заканчивать суффиксом "Fx")
>
> `const someEffectFx = createEffect(function)`

> Event - Событие/функция, которое(-ая) может произойти и повлиять на хранилище, на событие можно подписаться
>
> `const someEvent = createEvent<type of event | React.SyntheticEvent>()`

#

# Store и его методы

- ### `on`

  > Основной и чаще всего используемый метод это `.on`
  >
  > Обновляет состояние стора с помощью обработчика при срабатывании триггера
  >
  > `$store.on(триггер, (состояние $store, переданный в триггер аргумент) => изменённое состояние store)`

  Пример:

  ```typescript
  const someEvent = createEvent<string>();
  const $store = createStore<string[]>().on(someEvent, (state, result) => [
    ...state,
    result,
  ]);
  ```

* ### `map`

  > Создаёт производный store на основе данных исходного

  Пример:

  ```typescript
  const $secondStore = $store.map((item) => "new store item " + item);
  ```

* ### `reset`

  > Сбрасывает состояние store к исходному значению при срабатывании триггера
  >
  > `$store.reset(триггер)`

  Пример:

  `$store.reset(someEvent)`

* ### `watch`

  > Вызывает функцию при каждом обновлении store, в первый раз вызывается при инициализации store
  >
  > `$store.watch((состояние $store) => возвращаемое значение)`

  Пример:

  ```typescript
  $store.watch((state) => state);
  ```

#

# Effect, его методы и свойства

- ### `map` - Метод

  > Создает производное событие на основе данных эффекта
  >
  > `secondEffect = someEffect.map((некоторый параметр из основного эффекта) => возвращаемое значение)`
  >
  > Можно комбинировать с методом `watch` для отслеживания изменений определённого параметра

- ### `use`- Метод

  > Вызывает вложенную функцию функцию при вызове эффекта
  >
  > `someEffectFx.use((params) => {})`

- ### `watch`- Метод
  > Вызывает вложенную функцию при каждом изменении срабатывании эффекта
  >
  > `someEffect.watch((params) => {console.log(вызван эффект)})`

* ### `prepend` - https://effector.dev/ru/docs/api/effector/effect/#prepend - Метод

  > Не понял

* ### `doneData` - Свойство

  > Возвращает успешно полученные данные (Аналогично `.resolve(res => json(res))`)

* ### `failData` - Свойство

  > Возвращает ошибку, если она возникла во время срабатывания эффекта (Аналогично `.reject(error => json(error))`)

* ### `pending` - Свойство
  > Возвращает булевое значение, во время работы эффекта

#

# Event методы

- ### `map` - Метод

  > Создает производное событие на основе данных эффекта
  >
  > То есть создаётся event привязанные на effect
  >
  > `const secondEvent = someEvent.map(({result}) => result)`

* ### `prepend` - Метод

  > Создаёт событие-триггер, происходящее перед вызовом основного события
  >
  > `const eventTrigger = someEvent.prepend(result => {value: result})` Вызывать в данном случае нужно будет не someEvent, а eventTrigger.

- ### `watch` - Метод

  > Вызывает функцию при каждом срабатывании эвента.
  >
  > `someEvent.watch((params) => {console.log("вызван эвент")})`

  Пример:

  ```typescript
   const sayHi = createEvent()

   const stop = sayHi.watch(name => {console.log(Привет, ${name}!)})

   sayHi('Боб')

   // => Привет, Боб!
  ```

#

# Методы еффектора

- ### `combine`

  > Комбинирует несколько store'ов, event'ов и effect'ов в один store
  >
  > `const $combineStore = combine({key: value})`

  Пример:

  > `const $combineStore = combine({loading: someEffect.pending, data: $store})`

- ### `restore`

  > Создаёт новое хранилище не основе эвента или эффекта.
  >
  > `const $restoreStore = restore<some type>(event | effect, defaultState)` - defaultState - значение по умолчанию, после обработки эффекта в хранилище загружается возвращаемые данные из эффекта

  Пример:

  > `const $error = resotre<Error>(someEffectFx.failData, null)`

- ### `sample`

  > Метод для связывания данных, события по триггеру
  >
  > `sample({clock?, source?, filter?, fn?, target?}): target`

  Пример:

  ```typescript
  sample({ clock: trigger, source: $store, fn: () => {}, target: effectFx });
  ```

  > При срабатывание триггера clock, данные из source идут в функцию fn, при её наличии, и отправляются в target на финальную обработку/действие.

- ### `forward`

  > Метод для создания связи между элементами.
  >
  > `forward({from: source | [s1, s2, s3, ...], to: target | [t1, t2, t2, ...]})`
  >
  > from - первый ключ метода forward, который принимает значение и, в последствии передаёт эти значения как аргумент в метод, определённый в ключ to.

  Пример:

  ```typescript
   forward({
    from: state
    to: someEffectFx
    });
  ```

#

# Effector-react

#

# Gate и его методы

> Компнонет для условного рендеринга,
> использовать можно как отдельный компонент или же как хук.

> `const SomeGate = createGate<{props}>()` props - параметры передеваемые как аргументы в компонент

- ### `state`

  > Возвращает значение переданное в gate как пропс

- ### `open`

  > Возвращает event на момент монтирования компонента

- ### `close`

  > Возвращает event на момент размонтирования компонента

Пример:

```typescript
const fetchUserByIdFx = createEffect((id: number) => axios.get("/id").data)

const $user = createStore<TUser | null>(null).on(` ` fetchUserByIdFx.doneData,` `(state, result) => result)

const UserGate = createGate<{id: number}>()

forward({
    from: UserGate.state,
    to: fetchUserByIdFx
    })

const [state, dispatch] = useReducer((state) => state + 1, 0);

return (
  <main className="home">
    <div className="home__hello">
      <h1>Hello, dear character!</h1>
        <button onClick={() => dispatch()}>increament</button>
        <PostGate id={state} />
        <Post />
    </div>
  </main>
  );
```

#

# createComponent()

> Создание компонента на основе store'а

> `const Component = createComponent($store, (props, значение store'а) => (<></>))`

#

# useStore()

> Хук для использования данных store'а в компонентах React
>
> `const data = useStore($store)`
