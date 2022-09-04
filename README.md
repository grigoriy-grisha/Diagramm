# Диаграмма


Читается диаграмма снизу-вверх

В самом внизу описаны состояния Блокировки мест и Выбора мест:


## `SelectPlaceState<Abstract>`

Абстрактный класс выбора мест. Имеет виртуальный метод select и несколько базовых вспомогательных методов.
От него наследуются классы SeatSelectPlaceState и CompartmentSelectPlaceState

### `CompartmentSelectPlaceState`

Класс реализует логику выбора мест в купе

### `SeatSelectPlaceState`

Класс реализует логику выбора мест в сидячем вагоне

## `DisableStat<Abstract>F`

Абстрактный класс блокировки мест. имеет виртуальный метод disable. И несколько вспомогательных методов.
От него наследуются классы: 
- AllCompartmentDisableState
- AllowMultiBookingCompartmentDisableState
- CompartmentDisableState
- AllowMultiBookingDisableState
- SeatDisableState
- StaticDisableState

### `StaticDisableState`

Состояние блокирования мест для статической схемы, блокирует все не выранные места.

### `SeatDisableState`

Состояние блокирования мест для сидячих вагонов. Блокирует все, места, которые не рядом.
Постепенно их разблокировывает, пока пользователь не выберет 4 места. И так в обратной порядке, при снятии блокировки.

### `AllowMultiBookingDisableState` 

Состояние блокировки для мест в вагонах в режиме Мультибукинг. То есть, выбор мест без какого-либо порядка. Потом, при отправке данных для каждого места, будет создана отдельая услуга/заказ. Блокировка мест происходит после выбора всех мест.

### `CompartmentDisableState` 

Состояние блокировки мест в купе-вагонах. Тут происходит блокировка мест по гендеру. Если мы выбрали какое-то место в купе, то блокируем все остальные.

### `AllowMultiBookingCompartmentDisableState`

Состояние блокировки для мест в вагонах в режиме Мультибукинг. То есть, выбор мест без какого-либо порядка. Блокировка происходит только по гендеру и если все места выбраны.

### `AllCompartmentDisableState`

Состояние блокировки мест в купе-вагонах и купе-переговорочных в режимк "выбора всего купе". Блокирует не полные купе. Блокирует купе с другими гендерами. Блокирует другие купе, если уже есть выбранное купе.

## `SchemeModel`

Класс, который содержит состояние схемы:

- Все места
- Выбранные места
- Все купе
- Выбраные купе
- Изначальный гендер (Если в заказе только женщины, то изначальный гендер "Женский")
- Текущий гендер (То, что выбрал пользователь в выпадашке выбора гендера)
- В каком режиме работет схема
- Доступна ли возможность "Мультибукинга"
- Класс состояния выбора мест
- Класс состояния блокировки мест

### Связь с состояниями

Вышеописанные состояния связаны со SchemeModel по типу связи "Ассоциация", через методы **`setSelectPlaceState`** и **`setDisableState`**.
Это означает, что мы можем заменить логику выбора мест просто установив другое состояние, без потери уже накомпленного внутреннего состояния в SchemeModel

### Api 

Схема предоставляет методы по доступу к данным внутри схемы (Поиск, фильтрация, просто получение), изменению внутреннего состояния, проверки, выбора/удаления мест и купе, установка или сброс гендера и тд. 

При выборе места. Метод **`selectPlace`**, схема вызывает методы **`select`** у состояния выбора, а затем вызывает метод **`disable`**.

Так же есть возможность вызвать метода **`disable`** у состояния блокировки мест принудительно, вызвав метод **`forceDisable`**. Это бывает нужно для того, чтобы
при монтировании схемы сразу заблоировать места, например, по гендеру или в статической схеме все не выбранные места

## `TrainDom`

На примерно одном уровне лежит класс  **`TrainDom`**. Он предоставляет методы для взаимодействия с DOM схемы.

- Поиск эелеентов/элемента
- Получение отформатированных классов и id элементов

## `SchemeContext`

 Cвязан с классми **`SchemeModel`** и **`TrainDom`** по типу связи "агрегация".
 Класс реализует связку Model и DOM. Имеет методы, которые использует одномеренно методы **`SchemeModel`** и **`TrainDom`**.

 Класс является BLoC. То есть, предоставляет возмоность подписаться коду представления на изменения модели. Через поля  **`placesState$`** и  **`forceUpdate$`**,     которые являются **`Subject`** 
 
 ## `PlaceRenderer`
 
 Класс отображения состояния модели на DOM. Он в тупую пробегается по всех местам и проставляет нужные классы
 
 ## `CompartmentRenderer`
 
 Класс отображения состояния модели на DOM. Отображает гендеры в купе. 
 
 ## `BaseSchemeFacade<Abstract>`
 
 Весь сбор функционала схемы осуществляется в классах-фасадах.
 
 Базовый абстрактный класс фасада связан  по типу связи "Композиция" с классами **`PlaceRenderer`** и **`CompartmentRenderer`**
 И принимает в конструктор **`SchemeContext`**, а значит свзяан "агрегацией" с ним.
 
 Все Фасады:
 
 - AllCompartmentFacade
 - BaseSchemeFacade
 - CompartmentFacade
 - CoupeСonferenceRoomFacade
 - SeatFacade
 - StaticFacade
 
 Все классы-фасады должны его наследовать.
 
 ## `StaticFacade`
 
 Фасад стаической схемы, из функционала не делает практически ничего, только отображает схемы и использует компонент для показа описаний иконок.
  Использует компоненты:
 - показа описания иконок `IconsDescriptionComponent`
 
 ## `SeatFacade` 
 
 Фасад схемы выбора сидячих/плацкартных мест. Использует компоненты:
 - показа описания иконок `IconsDescriptionComponent`
 - подсказок для мест `PlaceDescriptionComponent`
 - выбора места `SelectPlaceComponent`
 - выбора тарифа `SelectTariffComponent`

## `CoupeСonferenceRoomFacade`
Фасад схемы выбора мест в вагонах с купе-переговорочными. Использует компоненты:
 - показа описания иконок `IconsDescriptionComponent`
 - подсказок для мест `PlaceDescriptionComponent`
 - выбора мест купе-переговорочной `SelectCoupeConferenceRoomComponent`
 - ховер для отображения доступности выбора всех мест `HoverAllCompartmentComponent`
 
## `CompartmentFacade`
Фасад схемы выбора мест в купе вагонах. Использует компоненты:
 - показа описания иконок `IconsDescriptionComponent`
 - подсказок для мест `PlaceDescriptionComponent`
 - выбора места `SelectPlaceComponent`
 - выбора тарифа `SelectTariffComponent`
 - выбора гендера для купе `SelectGenderComponent`

## `AllCompartmentFacade`
Фасад схемы выбора всех мест в купе вагонах. Использует компоненты:
 - показа описания иконок `IconsDescriptionComponent`
 - подсказок для мест `PlaceDescriptionComponent`
 - предоставляет возможность при нажатии на одно место в купе выбрать все остальные места, ожидая каждое действие (выбар тарифа, выбор гендера) `SelectAllCompartment`


