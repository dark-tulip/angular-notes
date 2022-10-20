Preferences - tslint - disable


Создание компоненты
```
ng generate component modal
ng g c modal --skipTests
```
global styles are defined on
```
styles.css
```
```html
app-root component, is the “home page” of the application,
export - для того чтобы они были видны в других компонентах
@Component -  это декоратор
selector - куда вставлять компонент
templateUrl - какой html шаблон


{{}} - интерполяция вставка переменной из ts компоненты (подстановка ts|js)
interpaltion: ['{{', '}}']
{{ user | json }} - this is a pipe, same as JSON.stringify(user)
<pre> </pre> - сохраняет различные пробелы (особенно json)


bindings - связка шаблона и компонента
когда мы передаем значение в атрибут, нужно воспользоваться концепцией binding
[src]="imageUrl" байндим атрибут в квадратные скобки, понимает что это значение динамической переменной
мы можем байндить любые атрибуты и передавать им динамику


инициализация элементов в lifecycle cookie 
добавление интерфейсов OnInit
ngOnInit() {
  // вызывается когда стартует компонент
}


entryComponents: the set of components to compile when this NgModule is defined, so that they can be dynamically loaded into the view

[] - get and set
() - отслеживать setting
[()]  и то и другое


Обновление таблицы
@ViewChild(MatTable, {static: true}) table: MatTable<User>;
this.table.renderRows();

Подписка на событие
dialogSubscription: Subscription;

if (this.dialogSubscription) {
  console.log('unsubscribed from event');
  this.dialogSubscription.unsubscribe();
}

this.dialogSubscription = ll.afterClosed().subscribe(x => {
      console.dir(x);
      // Тут можно загрузить данные
});

При уничтожении компоненты

  ngOnDestroy(): void {
    this.dialogSubscription.unsubscribe();
  }

Пройтись по enum
public phoneTypes = Object.values(PhoneType);


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

Two way binding
ngModel works with Forms module
<input type='text' [(ngModel)]="title">

Директивы - вспомогательные инструменты
Забайндить - сделать динамической  
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


Hide component
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

* значит что данная директива меняет html
```

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
  
  
### Добавление иконок
index.html
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
  
<div class="example-button-row">
  <button mat-button color="primary" (click)="openModal()">Primary</button>
  <button><mat-icon>keyboard_arrow_left</mat-icon></button>
  <button><mat-icon>keyboard_arrow_right</mat-icon></button>
</div>

### Пагинация

```ts
// AppComponent
  dataSource;
  currentPage = 1;
  itemsPerPage = 5;
  filterValue = '';

  constructor(private testController: TestController,
              public userService: UserService,
              public matDialog: MatDialog) {
    this.dataColumns = userService.dataColumns;
    this.dataSource = userService.getUsers();
  }
  
  applyFilter() {
    console.log('Filter by substr: ', this.filterValue);
    this.currentPage = 1;
    this.dataSource = this.userService.getUsers(this.currentPage, this.itemsPerPage, this.filterValue);
  }

  pageDown() {
    this.currentPage--;
    this.dataSource = this.userService.getUsers(this.currentPage, this.itemsPerPage, this.filterValue);
    console.log('pageDown this.currentPage', this.currentPage);
  }

  pageUp() {
    this.currentPage++;
    this.dataSource = this.userService.getUsers(this.currentPage, this.itemsPerPage, this.filterValue);
    console.log('pageUp this.currentPage', this.currentPage);
  }

  getTotalPages() {
    const pages = Math.ceil(this.userService.getTotalSize(this.filterValue) / this.itemsPerPage);
    console.log('pages', pages);
    return pages;
  }
```  

```
// UserService
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
<mat-form-field appearance="standard">
  <mat-label>Filter</mat-label>
  <input matInput [(ngModel)]="filterValue" (ngModelChange)="applyFilter()" placeholder="Search columns">
</mat-form-field>

<div class="example-button-row">
  <button mat-button color="primary" (click)="openModal()">Primary</button>
  <button mat-button (click)="pageDown()" [disabled]="currentPage == 1"><mat-icon>keyboard_arrow_left</mat-icon></button>
  <button mat-button (click)="pageUp()"   [disabled]="currentPage == getTotalPages()"><mat-icon>keyboard_arrow_right</mat-icon></button>
</div>
```
