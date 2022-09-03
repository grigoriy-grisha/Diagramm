# Диаграмма


Читается диаграмма снизу-вверх

В самом внизу описаны состояния Блокировки мест и Выбора мест:


## SelectPlaceState Abstract

Абстрактный класс выбора мест. Имеет виртуальный метод select и несколько базовых вспомогательных методов.
От него наследуются классы SeatSelectPlaceState и CompartmentSelectPlaceState

### CompartmentSelectPlaceState

Класс реализует логику выбора мест в купе

### SeatSelectPlaceState

Класс реализует логику выбора мест в сидячем вагоне

## DisableState Abstract

Абстрактный класс блокировки мест. имеет виртуальный метод disable. И несколько вспомогательных методов.
От него наследуются классы: 
- AllCompartmentDisableState
- AllowMultiBookingCompartmentDisableState
- CompartmentDisableState
- AllowMultiBookingDisableState
- SeatDisableState
- StaticDisableState
