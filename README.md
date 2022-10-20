# Apuntes-Curso-Angular

- Apuntes extraidos del curso de angular de Fernando Herrera organizados en relización a las secciones del curso y las aplicaciones desarroyadas en el mismo
---

## 01_Bases
- **Instalación de Angular** `npm install -g @angular/cli` instalará angular CLI en modo global.
- [Documentación](https://angular.io/cli) relativa a angular/cli
- Angular se compone de los siguientes bloques fundamentales:
     - **Componentes**  :  se compone de HTML y lógica en typescript
     -  **Rutas**: Mostrar diferentes componetes basados en rutas URL del navegador web
     -  **Directivas**: Directivas de componentes directivas estructurales y directivas de atributos
     -  **Servicio** : Lugares centrales para el manejo de información
     -  **Módulos**: Nos permite agrupar los anteriores bloques anteriores incluso otros módulos
- Creación de un proyecto con Angular
     - `ng new nombreProyecto`
     - `ng serve -o` -> arranca el proyecto y lo ejecuta en el navegador
- **Enlace de una via** entre la lógica y la vista html. para ello basta con indicar en el html el nombre de la propiedad entre doble llave `{{titulo}}`
- Entre estas llaves no solo se puede poner propiedades, sino tambien expresiones javascript
- Podemos vincular un **evento** a un elemento del DOM e indicarle un método a realizar
    - `<button (click)="acumular(1)">+1</button>`
- Crear un componente de manera automática:
    - `ng generate componente <carpeta>/nombreComponente`
- Directiva estructural **NgFor**
```html
<ul>
  <li *ngFor="let heroe of heroes; let i = index">
    {{ i + 1 }} - {{ heroe }}
  </li>
</ul>
```
- Directiva estructural **NgIf**   **<ng-template>**
```html
<div class="d-block" *ngIf="heroeBorrado; else noBorrado">
    <span >Heroe Borrado: </span>
    <span>{{heroeBorrado}}</span>
</div>
<ng-template  #noBorrado>
    <span class="d-block">Na ha borrado ningún heroe</span>
</ng-template>
```
- Observese la creación de una referencia local `#noBorrado`

### Comunicación entre vista y controlador - [(ngModel)] - {{atributo}}
- Además de la comunicación mediante binding por interpolación `{{}}` entre vista y controlador, también existe el *two way data binding* en este caso mediante la directiva ngModel `[(ngModel)]`
- Una de las opciones que tenemos para realizar esta comunicación es a través de formularios. (se tratarán en detalle más adelante)
- Para cosas sencillas se puede utilizar el FormsModule
- Importarmos formsModule en el modulo donde se encuetra nuestro componente
- `import { FormsModule } from '@angular/forms';`
- En la vista ya podemos hacer uso de la directiva ngSubmit para tomar el control del formulario cuando se presiona el botón submit
```html
<Form (ngSubmit)="metodoSubmit()">
    <Input type="text" name="nombre" [(ngModel)]="nuevo.nombre">
    <button type="submit">Agregar</button>
</Form>
```
- La directiva `[(ngModel)]` vincula en dos direcciones el contenido de la vista con un atributo del controlador. Importante incluir en el input el atributo `name="loquesea"` para que funcione correctamente ngModel

### Comunicación entre componente padre e hijo - @Input()
- Una forma de pasar información del componente padre a un componente hijo es mediante el uso del `@Input` 
- En el componente hijo creamos `@Input() nuevo!: Personaje;`
- y en la vista del componente padre. en el `<app-hijo [nuevo]="personajeInicial"></app-hijo>`
- en el controlador del padre tendremos ese atributo que será el que le pasemos al hijo
```javascript
public personajeInicial: Personaje = {
    nombre: 'santos',
    poder: 500,
  };
```

### Comunicación entre componente hijo y padre - @Output - EventEmitter
- Para esto tenemos que crear en el componente hijo la "salida" `@Output()`
- La crearemos de la siguiente manera `@Output() comunicaInsercion = new EventEmitter<Personaje>();`
- Luego, en la vista, bien como evento de un botón, submit de un formulario, etc... En el control del evento haremos uso de ese atributo creado de tipo eventEmitter
- En la parte del genérico indicaremos el tipo que se va a emitir. En este caso un objeto de tipo `Personaje`
```Typescript
agregar() {
    this.comunicaInsercion.emit({
      nombre: this.nuevo.nombre,
      poder: this.nuevo.poder,
    });
```
- Observese el uso de la instancia eventEmitter, su metodo `emit` será el encargado de emitir al componente padre el objeto `Personaje`
- El componente padre lo recibirá como argumento en su propio método de la siguiente forma:
```html
<div class="col-4">
    <app-agregar
      [nuevo]="personajeInicial"
      (comunicaInsercion)="recibeInsercion($event)"
    ></app-agregar>
</div>
```
- donde `$event` será el objeto de tipo `Personaje`
### Servicios
- su función es la de manejar la información y metodos para interactuar con fuentes de información externas
- Este punto de encuentro de información puede ser accesible bien desde los componentes de un módulo en concreto o bien desde toda la aplicación.
    - Para esto puede ser proveido en el propio módulo indicándose en el aparatado `providers`
    - ...O puede ser proveido de manera global indicandolo en el decorador del servicio 
```javascript
@Injectable({
  providedIn: 'root',
})
```
- Para hacer uso de un servicion concreto hay que inyectarlo en el componente que lo requiera **(Inyección de dependencias)**
- `constructor(private service: Service){}`
---
## 02_Gifs App
- Esta aplicación está diseñada gráficamente en base a **bootstrap**
- Incorporamos bootstrap a nuestra aplicaióna  través del CDN del própio bootstrap insertandolo en nuestro archivo `index.html`
```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-Zenh87qX5JnK2Jl0vWa8Ck2rdkQ2Bzep5IDxbcnCeuOxjzrPF/et3URy9Bv1WTRi" crossorigin="anonymous">
```
- **Tip**: A la hora de devolver un array con desde algún servicio, es conveniente devolverlo mediante el Spread Operator para romper la relación entre el array devuelto y el original. La función de esto es para evitar que se pueda manipular el array original del servicion fuera de este.
```javascript
get getArrayPrivado(){
    return [...this.arrayPrivado]
}
```
### @ViewChild (aplicado a elementos del DOM)
- Este decorador sireve para vincular un elemento del DOM y el controlador.
- En la vista:
```html
<Input #textoEntrada type="text" (keyUp.enter)="busqueda()">
```
- Vinculandolo en el controlador
```javascript
@ViewChild('textoEntrada ') busqueda!: ElementRef;
```
- Utilizándolo en el método busqueda
```javascript
busqueda() {
    const terminoBusqueda = this.busqueda.nativeElement.value;
}
```
### Comunicación con una API - Angular HTTP
- Para esta aplicación se hace uso del API [Giphy](https://developers.giphy.com/)
- Para la comunicación de la aplicación con el API utilizamos el módulo de **Angular HTTP**
- Lo importamos en nuestro `app.module.ts`
```javascript
import { HttpClientModule } from '@angular/common/http';
```
- En el servicion donde vamos a realizar el trabajo de comunicación con la API inyectamos el servicio http
```javascript
import { HttpClient, HttpParams } from '@angular/common/http';
.
.
constructor(private http: HttpClient) {}
```
- Ahora ya podemos hacer uso del objeto http el cual nos proporciona todo lo necesario para trabajar con el protocolo HTTP
- Existen muchas ventajas para trabajar en base a http Angular, entre otras, el uso de los Observables 
- Ejemplo petición http.get con **params**
```javascript
busquedaApi(terminoBusqueda: string) {
    const params = new HttpParams()
      .set('api_key', this.apiKey)
      .set('q', terminoBusqueda)
      .set('limit', 12);

    this.http
      .get<Gifs>(this.urlGiphy, { params })
      .pipe(map((res) => res.data))
      .subscribe((resp) => {
        this._arrayResp = resp;
      });
  }
```