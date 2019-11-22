# Архитектура ts документа

Код должен быть максимально читаемым и понятным. Для этого следует придерживаться определенных правил при работе с ts документами. 

## Последовательность спецификаторов доступа

В общем виде последовательность доступов выглядит так:
*  `Static` - свойства и методы принадлежат только своему классу, и не принадлежат объекту класса;
*  `Public` - свойства и методы используются во view;
*  `Protected` - свойства и методы используются у ребенка/родителя;
*  `Private` - внутреннее свойства и методы, которые используются в рамках текущей области видимости.

### Исключения

Методы фреймворка описываем без доступов. 

Например:
```ts
ngOnInit() {
  if (this.keys.length !== this.header.length) {
    console.error(`Кол-во ключей ${this.keys.length} не совпадает с кол-вом заголовков ${this.header.length}`);
    console.error(this.keys, this.header);
  }

  this.sortTH();
}
```

## Объявление методов и свойств

При объявлении св-ва и методов нужно придерживаться следующей последовательности:
*  `@Input()`;
*  `@Output()`;
*  `@ViewChild`;
*  Свойства класса;
*  Абстрактные свойства класса;
*  `constructor(){}`;
*  Методы фреймворка. Например `ngOnInit() {}`;
*  Методы класса;
*  Абстрактные методы класса.

## Отступы и перенос строк
* Горизонтальный отступ в проекте - **2 пробела**.
* Вертикальный отступ используется, чтобы разделить логические блоки/элементы внутри одной разметки.
* Максимальное допустимое кол-во переносов строк - **1 перенос**.
* Перенос строки в начале открытия блока требуется.
* Перенос строки в конце блока требуется ситуативно.
* Строки с единой логикой можно не разделять новой строкой.

## Комментирование:
1. При объявление метода или св-ва нужно оставлять комментарии формата JSDoc. 
2. Остальные операции комментируются по желанию. Но как показывает практика обильное кол-во комментариев увеличивает понимание кода для себя или нового разработчика в будущем.

```ts
/** Инициализация данных */
private initData(): void {
  // Запускаем загрузчик
  this.loadingPageService.isLoading$$.next(true);

  // Загрузилась текущая компания
  const currentCompany$ = this.partnerService.companyService.currentCompany$$.asObservable();

  // Новости загружены
  const getNewsReady$ = this.dashboardNewsService.getNewsReady$$.asObservable();

  // Загрузились документы
  const docCard$ = this.partnerService.getCompanyDocsCard();

  // Ждем ответ от всех потоков
  combineLatest(currentCompany$, getNewsReady$, docCard$)
    .pipe(
      filter((data) => {
        return !data.some((param) => param === null);
      }),
      take(1)
    )
    .subscribe(() => {
      // Выключаем загрузчик
      this.loadingPageService.isLoading$$.next(false);
    });
}
```

# CodeStyle
* В конструкциях, где предусмотрены фигурные скобки они ставятся обязательно. Фигурная скобка ставится на той же строке где и начинался оператор.

## IfElse

Допускается перенос строки на новую строчку в конце блока если внутри не было переносов строк. 

Общий синтаксис ifelse выглядит так:
```ts
    if (direction === 'next' && this.param.currentPage < this.pages) {
      this.onCurrentPageChange.emit(this.param.currentPage + 1);

      this.a = 0;
    } else if (direction === 'prev' && this.param.currentPage > 1) {
      this.onCurrentPageChange.emit(this.param.currentPage - 1);

    } else if (direction === 'first' && this.param.currentPage !== 1) {
      this.onCurrentPageChange.emit(1);

    } else {
      let page = +direction;
    }

    if (th.orderBy) {
      th.orderBy = th.orderBy === EOrderBy.asc ? EOrderBy.desc : EOrderBy.asc;
    } else {
      th.orderBy = EOrderBy.asc;
    }
```

Для незначительной логики допустимо использовать "\*\*? :\*\*"
```ts
th.orderBy = th.orderBy === EOrderBy.asc ? EOrderBy.desc : EOrderBy.asc;
```

Если логическое условие слишком длинное допускается их объявление в виде переменных/констант. Также данный случай предпочтителен для более сложной логики.
```ts
    // Описание первого условия
    const checkStepPage = val.param.previousValue.stepPage !== this.param.stepPage;

    // Описание второго условия
    const checkTotalItems = val.param.previousValue.totalItems !== this.param.totalItems;

    // Нужно ли сделать перерасчет страниц
    if (checkStepPage || checkTotalItems) {
      this.initPages();
    }
```

##  Стрелочные ф-ии

* Круглые скобки ставятся обязательно;.
* В простой логике фигурные скобки можно опустить;

```ts
this.isLeftDrag = this.isLeftDrag.map((drag, index) => index < i);

this.header.map((header, i) => {
  const th = thArray[i];

  header.width = parseInt(getComputedStyle(th).width);
  
  return header;
});
```