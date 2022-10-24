#### Отключить линтер
Preferences - tslint - disable
#### Aliases
```ts
type Login: string  // кастомный тип
const login: Login = 'admin'

type ID = string | number  // или строка или число
const id1 = 1234
const id2 = '12345'
```
#### Interfaces
```
interface Rect {
  readonly id: string   // модификатор доступа, только для чтения
  color?: string   // необязательный
}
// Константы нельзя reassign-ить
const rect: Rect = {
  id: '1234'
}
// указание типа
const rect2: <Rect>{}
const rect3: {} as Rect;
```

#### Создание компоненты
```typescript
ng generate component modal
ng g c modal --skipTests
```

```html
app-root component, is the “home page” of the application,
export - для того чтобы они были видны в других компонентах
@Component -  это декоратор
selector - куда вставлять компонент
templateUrl - какой html шаблон
```
```
{{}} - интерполяция вставка переменной из ts компоненты (подстановка ts|js)
interpaltion: ['{{', '}}']
{{ user | json }} - this is a pipe, same as JSON.stringify(user)
<pre> </pre> - сохраняет различные пробелы (особенно json)
```
```
bindings - связка шаблона и компонента
когда мы передаем значение в атрибут, нужно воспользоваться концепцией binding
[src]="imageUrl" байндим атрибут в квадратные скобки, понимает что это значение динамической переменной
мы можем байндить любые атрибуты и передавать им динамику
```
```
инициализация элементов в lifecycle cookie 
добавление интерфейсов OnInit
ngOnInit() {
  // вызывается когда стартует компонент
}
```
#### entryComponents: the set of components to compile when this NgModule is defined, so that they can be dynamically loaded into the view
```typescript
[] - get and set
() - отслеживать setting
[()]  и то и другое
```

#### Обновление таблицы
```typescript
@ViewChild(MatTable, {static: true}) table: MatTable<User>;
this.table.renderRows();
```
#### Подписка на событие
```typescript
dialogSubscription: Subscription;

if (this.dialogSubscription) {
  console.log('unsubscribed from event');
  this.dialogSubscription.unsubscribe();
}

this.dialogSubscription = ll.afterClosed().subscribe(x => {
      console.dir(x);
      // Тут можно загрузить данные
});
```
#### При уничтожении компоненты
```typescript
  ngOnDestroy(): void {
    this.dialogSubscription.unsubscribe();
  }
```
#### Пройтись по enum
```typescript
public phoneTypes = Object.values(PhoneType);

enum Membership {
  Simple,
  Standart,
  Premium
}

console.log(Membership.Simple)  // 0
console.log(Membership[2])      // Premium - this is reversed enum
```

$event - этот элемент нативныый
<input type="text" (input)="inputHandler($event)">
inputHandler(event: any) {
  const value = event.target.value;
  this.title = value;
}


<input type="text" #myInput (input)="inputHandler(myInput.value)">
nputHandler(value) {
  this.title = value;
}

#### Two way binding
ngModel works with Forms module
```
<input type='text' [(ngModel)]="title">
```
Директивы - вспомогательные инструменты
Забайндить - сделать динамической  
```typescript
[ngStyle]="{
  color: title.length < 5 ? 'black' : 'blue';
}"
<p
  [class.blue]="textColor === 'blue'"
  [class.green]="textColor === 'green'"
  [class.red]="textColor === 'red'"
>{{ text }}</p>

<button (click)="textColor='blue'">set blue</button>
<button (click)="textColor='red'">set red</button>
<button (click)="textColor='green'">set green</button>
```

#### Hide component
```typescript
export class AppComponent {
  toggle = true;
  toggleCards() {
    this.toggle = !this.toggle;
  }
}

<button (click)="toggleCards()"></button>
<div *ngIf="toggle; else noCards">
  <app-card></app-card>
  <app-card></app-card>
  <app-card></app-card>
</div>
<ng-template #noCards>
  <p>Cards are hidden</p>
</ng-template>
```
`*` значит что данная директива меняет html
```typescript
  applyFilter(event: KeyboardEvent) {
    const filterValue = (event.target as HTMLInputElement).value;
    console.log('Event', filterValue);
    this.dataSource = this.userService.dataSource.filter(x => x.username.includes(filterValue));
  }
  
  <mat-form-field appearance="standard">
  <mat-label>Filter</mat-label>
  <input matInput (keyup)="applyFilter($event)" placeholder="Search columns" #input>
</mat-form-field>

  filterBy(filterBy: string) {
    const result = this.dataSource.filter(x => x.username.includes(filterBy)
      || x.surname.includes(filterBy)
      || x.fathersName.includes(filterBy)
    );
    console.log('Filter by: ', result);
    return result;
  }
```  
  
### Добавление иконок

```html title="index.html"
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
  
<div class="example-button-row">
  <button mat-button color="primary" (click)="openModal()">Primary</button>
  <button><mat-icon>keyboard_arrow_left</mat-icon></button>
  <button><mat-icon>keyboard_arrow_right</mat-icon></button>
</div>
```
### Пагинация

```typescript
export class AppComponent implements OnInit, AfterViewInit, OnDestroy {

  dialogSubscription: Subscription;

  dataSource: MatTableDataSource<User>;

  currentPage = 1;
  itemsPerPage = 5;
  filterValue = '';
  totalItems = 0;
  totalPages = 0;
  sortByColumn = '';
  sortDirection = '';

  @ViewChild(MatPaginator) paginator: MatPaginator;

  constructor(private testController: TestController,
              public userService: UserService,
              public matDialog: MatDialog) {
  }

  ngOnDestroy(): void {
    this.dialogSubscription.unsubscribe();
  }

  ngOnInit(): void {
    this.refreshDataSource();
  }

  ngAfterViewInit(): void {

  }

  deleteRow(user: User) {
    this.userService.deleteUser(user);
    if (this.dataSource.data.length <= 1) {
      this.pageDown();
    }
    this.refreshDataSource();
  }


  /**
   * Если передали пользователя - user edit mode
   * Иначе - new user mode
   * @param user
   */
  openUser(user?: User) {

    const userDraft = Object.assign(new User(), user);

    const dialogConfig = new MatDialogConfig();
    dialogConfig.id = 'modal-component';
    dialogConfig.height = '900px';
    dialogConfig.width = '1200px';
    dialogConfig.data = userDraft;

    let openedModal = this.matDialog.open(ModalComponent, dialogConfig);

    if (this.dialogSubscription) {
      this.dialogSubscription.unsubscribe();
    }

    this.dialogSubscription = openedModal.beforeClosed().subscribe(editedUser => {

      if (editedUser?.id) {  // update
        this.userService.updateUser(editedUser);
        this.refreshDataSource();

      } else if (editedUser && Object.keys(editedUser).length) {  // add new user
        this.userService.addUser(editedUser);
        this.refreshDataSource();

      } else {
        console.log('Adding user cancelled, empty data');
      }
    });
  }

  applyFilter() {
    console.log('Filter by substr: ', this.filterValue);
    this.currentPage = 1;
    this.refreshDataSource();
  }

  pageDown() {
    this.currentPage--;
    this.refreshDataSource();
    console.log('pageDown this.currentPage', this.currentPage);
  }

  pageUp() {
    this.currentPage++;
    this.refreshDataSource();
    console.log('pageUp this.currentPage', this.currentPage);
  }

  refreshDataSource() {
    console.log('refresh data src');
    this.dataSource = new MatTableDataSource<User>(
      this.userService.getUsers(this.currentPage,
        this.itemsPerPage,
        this.filterValue,
        this.sortByColumn,
        this.sortDirection
      )
    );
    this.totalItems = this.userService.getTotalSize(this.filterValue);
    this.totalPages = Math.ceil(this.totalItems / this.itemsPerPage);
  }

  resetFilter() {
    this.filterValue = '';
    this.currentPage = 1;
    this.refreshDataSource();
  }

  sortData(event: Sort) {
    console.log('active', event.active, 'direction', event.direction);
    this.sortByColumn = event.active;
    this.sortDirection = event.direction;
    this.refreshDataSource();
    console.dir(this.dataSource.data);
  }

  helloResult: string = null;
  complexObjectResult: ComplexObject = null;
  hello = 'Hello';

  async sendComplexObject() {
    const complexObject: ComplexObject = {
      id: 42,
      title: 'test',
      description: 'Big object',
      date: new Date(),
      phoneNumber: {
        phoneType: PhoneType.MOBILE,
        number: '77074655454',
        isRequired: true
      },
    };
    console.log(complexObject);
    this.complexObjectResult = await this.testController.sendComplexObject(complexObject);
  }

  async say() {
    this.helloResult = await this.testController.sayHello(this.hello);
  }
}
```  

```typescript [title="UserService.ts"]
getUsers(page?: number, itemsPerPage?: number, filterValue?: string) {
    if (!filterValue) {
      filterValue = '';
    }
    if (!page) {
      page = 1;
    }
    if (!itemsPerPage) {
      itemsPerPage = 5;
    }
    return this.dataSource
      .filter(x => x.username.includes(filterValue))
      .slice((page - 1) * itemsPerPage, page * itemsPerPage);
  }

  getTotalSize(filterValue?: string) {
    return this.dataSource
      .filter(x => x.username.includes(filterValue)).length;
  }
```

```html
<section>
    <div class="example-button-row">
      <button mat-button color="primary" class="add-btn" (click)="openUser()">Добавить</button>

      <button mat-button (click)="pageDown()" [disabled]="currentPage <= 1">
        <mat-icon>keyboard_arrow_left</mat-icon>
      </button>
      <button mat-button (click)="pageUp()" [disabled]="currentPage >= this.totalPages">
        <mat-icon>keyboard_arrow_right</mat-icon>
      </button>

      <mat-form-field appearance="standard">
        <mat-label>Поиск</mat-label>
        <input matInput [(ngModel)]="filterValue" (ngModelChange)="applyFilter()" placeholder="Поиск">
        <button *ngIf="filterValue" matSuffix mat-icon-button aria-label="Clear" (click)="resetFilter()">
          <mat-icon>close</mat-icon>
        </button>
      </mat-form-field>
    </div>
  </section>
```
#### Comparator
```typescript
type CompareFn = <T>(a: User, b: User, sortDirection: string, sortByColumn: string) => number;

/**
 * Сортировка пользователя по полям
 * @param a - user1
 * @param b - user2
 * @param sortDirection
 * @param sortByColumn
 */
const sortUsers: CompareFn = (a: User, b: User, sortDirection: string, sortByColumn: string) => {
  const isAsc = sortDirection === 'asc';
  switch (sortByColumn) {
    case 'surname':
      return compare(a.surname, b.surname, isAsc);
    case 'character':
      return compare(a.character, b.character, isAsc);
    case 'totalAccountBalance':
      return compare(a.totalAccountBalance, b.totalAccountBalance, isAsc);
    case 'minBalance':
      return compare(a.minBalance, b.minBalance, isAsc);
    case 'maxBalance':
      return compare(a.maxBalance, b.maxBalance, isAsc);
    case 'age':
      return compare(a.age, b.age, isAsc);
    default:
      return 0;
  }
};
```
