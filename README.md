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
```
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

```
