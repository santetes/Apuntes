 <!-- title: Apuntes Angular -->

- [Apuntes-Curso-Angular](#apuntes-curso-angular)
  - [01_Bases](#01_bases)
    - [Comunicación entre vista y controlador - [(ngModel)] - {{atributo}}](#comunicación-entre-vista-y-controlador---ngmodel---atributo)
    - [Comunicación entre componente padre e hijo - @Input()](#comunicación-entre-componente-padre-e-hijo---input)
    - [Comunicación entre componente hijo y padre - @Output - EventEmitter](#comunicación-entre-componente-hijo-y-padre---output---eventemitter)
    - [Servicios](#servicios)
  - [02_Gifs App](#02_gifs-app)
    - [@ViewChild (aplicado a elementos del DOM)](#viewchild-aplicado-a-elementos-del-dom)
    - [Comunicación con una API - Angular HTTP](#comunicación-con-una-api---angular-http)
    - [Tipado de una respuesta](#tipado-de-una-respuesta)
    - [Persistencia de Información - LocalStorage](#persistencia-de-información---localstorage)
    - [Animate.css](#animatecss)
  - [03_PaisesApp](#03_paisesapp)
    - [Rutas](#rutas)

# Apuntes-Curso-Angular

-   Apuntes extraidos del curso de angular de Fernando Herrera organizados en relización a las secciones del curso y las aplicaciones desarroyadas en el mismo

---

## 01_Bases

-   **Instalación de Angular** `npm install -g @angular/cli` instalará angular CLI en modo global.
-   [Documentación](https://angular.io/cli) relativa a angular/cli
-   Angular se compone de los siguientes bloques fundamentales:
    -   **Componentes** : se compone de HTML y lógica en typescript
    -   **Rutas**: Mostrar diferentes componetes basados en rutas URL del navegador web
    -   **Directivas**: Directivas de componentes directivas estructurales y directivas de atributos
    -   **Servicio** : Lugares centrales para el manejo de información
    -   **Módulos**: Nos permite agrupar los anteriores bloques anteriores incluso otros módulos
-   Creación de un proyecto con Angular
    -   `ng new nombreProyecto`
    -   `ng serve -o` -> arranca el proyecto y lo ejecuta en el navegador
-   **Enlace de una via** entre la lógica y la vista html. para ello basta con indicar en el html el nombre de la propiedad entre doble llave `{{titulo}}`
-   Entre estas llaves no solo se puede poner propiedades, sino tambien expresiones javascript
-   Podemos vincular un **evento** a un elemento del DOM e indicarle un método a realizar
    -   `<button (click)="acumular(1)">+1</button>`
-   Crear un componente de manera automática:
    -   `ng generate componente <carpeta>/nombreComponente`
-   Directiva estructural **NgFor**

```html
<ul>
    <li *ngFor="let heroe of heroes; let i = index">
        {{ i + 1 }} - {{ heroe }}
    </li>
</ul>
```

-   Directiva estructural **NgIf** **<ng-template>**

```html
<div class="d-block" *ngIf="heroeBorrado; else noBorrado">
    <span>Heroe Borrado: </span>
    <span>{{heroeBorrado}}</span>
</div>
<ng-template #noBorrado>
    <span class="d-block">Na ha borrado ningún heroe</span>
</ng-template>
```

-   Observese la creación de una referencia local `#noBorrado`

### Comunicación entre vista y controlador - [(ngModel)] - {{atributo}}

-   Además de la comunicación mediante binding por interpolación `{{}}` entre vista y controlador, también existe el _two way data binding_ en este caso mediante la directiva ngModel `[(ngModel)]`
-   Una de las opciones que tenemos para realizar esta comunicación es a través de formularios. (se tratarán en detalle más adelante)
-   Para cosas sencillas se puede utilizar el FormsModule
-   Importarmos formsModule en el modulo donde se encuetra nuestro componente
-   `import { FormsModule } from '@angular/forms';`
-   En la vista ya podemos hacer uso de la directiva `ngSubmit` para tomar el control del formulario cuando se presiona el botón submit

```html
<form (ngSubmit)="metodoSubmit()">
    <input type="text" name="nombre" [(ngModel)]="nuevo.nombre" />
    <button type="submit">Agregar</button>
</form>
```

-   La directiva `[(ngModel)]` vincula en dos direcciones el contenido de la vista con un atributo del controlador. Importante incluir en el input el atributo `name="loquesea"` para que funcione correctamente ngModel

### Comunicación entre componente padre e hijo - @Input()

-   Una forma de pasar información del componente padre a un componente hijo es mediante el uso del `@Input`
-   En el componente hijo creamos `@Input() nuevo!: Personaje;`
-   y en la vista del componente padre. en el `<app-hijo [nuevo]="personajeInicial"></app-hijo>`
-   en el controlador del padre tendremos ese atributo que será el que le pasemos al hijo

```javascript
public personajeInicial: Personaje = {
    nombre: 'santos',
    poder: 500,
  };
```

### Comunicación entre componente hijo y padre - @Output - EventEmitter

-   Para esto tenemos que crear en el componente hijo la "salida" `@Output()`
-   La crearemos de la siguiente manera `@Output() comunicaInsercion = new EventEmitter<Personaje>();`
-   Luego, en la vista, bien como evento de un botón, submit de un formulario, etc... En el control del evento haremos uso de ese atributo creado de tipo eventEmitter
-   En la parte del genérico indicaremos el tipo que se va a emitir. En este caso un objeto de tipo `Personaje`

```Typescript
agregar() {
    this.comunicaInsercion.emit({
      nombre: this.nuevo.nombre,
      poder: this.nuevo.poder,
    });
```

-   Observese el uso de la instancia eventEmitter, su metodo `emit` será el encargado de emitir al componente padre el objeto `Personaje`
-   El componente padre lo recibirá como argumento en su propio método de la siguiente forma:

```html
<div class="col-4">
    <app-agregar
        [nuevo]="personajeInicial"
        (comunicaInsercion)="recibeInsercion($event)"
    ></app-agregar>
</div>
```

-   donde `$event` será el objeto de tipo `Personaje`

### Servicios

-   su función es la de manejar la información y metodos para interactuar con fuentes de información externas
-   Este punto de encuentro de información puede ser accesible bien desde los componentes de un módulo en concreto o bien desde toda la aplicación.
    -   Para esto puede ser proveido en el propio módulo indicándose en el aparatado `providers`
    -   ...O puede ser proveido de manera global indicandolo en el decorador del servicio

```javascript
@Injectable({
  providedIn: 'root',
})
```

-   Para hacer uso de un servicion concreto hay que inyectarlo en el componente que lo requiera **(Inyección de dependencias)**
-   `constructor(private service: Service){}`

---

## 02_Gifs App

-   Esta aplicación está diseñada gráficamente en base a **bootstrap**
-   Incorporamos bootstrap a nuestra aplicaióna través del CDN del própio bootstrap insertandolo en nuestro archivo `index.html`

```html
<link
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css"
    rel="stylesheet"
    integrity="sha384-Zenh87qX5JnK2Jl0vWa8Ck2rdkQ2Bzep5IDxbcnCeuOxjzrPF/et3URy9Bv1WTRi"
    crossorigin="anonymous"
/>
```

-   **Tip**: A la hora de devolver un array con desde algún servicio, es conveniente devolverlo mediante el Spread Operator para romper la relación entre el array devuelto y el original. La función de esto es para evitar que se pueda manipular el array original del servicion fuera de este.

```javascript
get getArrayPrivado(){
    return [...this.arrayPrivado]
}
```

### @ViewChild (aplicado a elementos del DOM)

-   Este decorador sireve para vincular un elemento del DOM y el controlador.
-   En la vista:

```html
<input #textoEntrada type="text" (keyUp.enter)="busqueda()" />
```

-   Vinculandolo en el controlador

```javascript
@ViewChild('textoEntrada ') busqueda!: ElementRef;
```

-   Utilizándolo en el método busqueda

```javascript
busqueda() {
    const terminoBusqueda = this.busqueda.nativeElement.value;
}
```

### Comunicación con una API - Angular HTTP

-   Para esta aplicación se hace uso del API [Giphy](https://developers.giphy.com/)
-   Para la comunicación de la aplicación con el API utilizamos el módulo de **Angular HTTP**
-   Lo importamos en nuestro `app.module.ts`

```javascript
import { HttpClientModule } from '@angular/common/http'
```

-   En el servicion donde vamos a realizar el trabajo de comunicación con la API inyectamos el servicio http

```javascript
import { HttpClient, HttpParams } from '@angular/common/http';
.
.
constructor(private http: HttpClient) {}
```

-   Ahora ya podemos hacer uso del objeto http el cual nos proporciona todo lo necesario para trabajar con el protocolo HTTP
-   Existen muchas ventajas para trabajar en base a http Angular, entre otras, el uso de los Observables
-   Ejemplo petición http.get con **params**
    -   Params nos permite configurar una petición de este tipo:
    -   https://api.giphy.com/v1/gifs/search?api_key=1TpDBYvXGJfoUHuwM20R3wKIOR0cgtEb&q=car&limit=10
    -   Observese en esta petición los parámetros "api_key", "q", "limit"

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

### Tipado de una respuesta

-   Es conveniente realizar este tipado para poder ir viendo todos las propiedades de un objeto de tipo respuestas
-   La herramienta que se usará para esta función es la página [QuickType](https://quicktype.io/)
-   Tan solo hay que copiar la respuesta de una petición en esa página y automáticamente tendremos la configuración de una interface con el tipado

### Persistencia de Información - LocalStorage

-   Es un método que tenemos para almacenar información en nuestro navegador y hacerla persistente

```javascript
localStorage.setItem('clave', JSON.stringify(this._infoLocalStorage);
```

-   Recuperar información del localStorage

```javascript
this._infoLocalStorage = JSON.parse(
    localStorage.getItem('arrayBusquedas') || '[]'
)
```

-   Observese el uso del objeto global `JSON` para convertir un objeto en string y un string en objeto (localStorage sólo almacena strings)
-   **Tip**: para manejar la información en el localStorage, array de strings y demás es conveniente manejarla en minúsculas y sin espacios antes y después del string. Para que a la hora de buscar, coincidencias, etc... no nos surja ningún error `this.almacenaEnLocalStorage(terminoBusqueda.trim().toLowerCase());`
-   Luego, si en la vista queremos que aparezcan esos strings bien capitalizados, únicamente tenemos que pasarlos por el pipe `{item | Tittlecase}` Se verá detenidamente los Pipes más adelante

### Animate.css

-   Es una libreria que nos permite mejorar la visualización de los elementos en la vista que aparecen y desaparecen bruscamente.
-   Para ello utilizaremos la libreria [Animate.css](https://animate.style/)
-   Hay varias formas de insertarla en nuestro proyecto, una de ellas es mediante el CDN en el `index.html`

```html
<link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css"
/>
```

-   Luego, únicamente tenemos que incluir en elemento que queramos realizar la mejora de la visualización la clase:
-   `class="animate__animated animate__fadeIn"`
-   Reivisar la documentación de la página oficial para ver más efectos

---

## 03_PaisesApp

-   En esta aplicación estaremos consumiendo la Api [RestCoutries](https://restcountries.com/)
-   También haremos uso de **BootStrap** para realizar la maquetación gráfica

### Rutas

-   La configuración de las rutas se define dentro del archivo `app-routing.module.ts`
-   Ejemplo de la configuración del archivo en la aplicación Paises_App

```typescript
import { NgModule } from '@angular/core'
import { RouterModule, Routes } from '@angular/router'
import { PorPaisComponent } from './pais/pages/por-pais/por-pais.component'
import { PorRegionComponent } from './pais/pages/por-region/por-region.component'
import { PorCapitalComponent } from './pais/pages/por-capital/por-capital.component'
import { VerPaisComponent } from './pais/pages/ver-pais/ver-pais.component'

const routes: Routes = [
    {
        path: '',
        component: PorPaisComponent,
        pathMatch: 'full',
    },
    {
        path: 'region',
        component: PorRegionComponent,
    },
    {
        path: 'capital',
        component: PorCapitalComponent,
    },
    {
        path: 'pais/:id',
        component: VerPaisComponent,
    },
    {
        path: '**',
        redirectTo: '',
    },
]

@NgModule({
    imports: [RouterModule.forRoot(routes)],
    exports: [RouterModule],
})
export class AppRoutingModule {}
```

-   Este archivo `app-routing.module` tenemos que importarlo dentro de nuestro módulo principal de nuestra aplicación; `app.module.ts`
-   Luego, para poder ir visualizando cada uno de los componentes (páginas) que se encuentran enrutadas, tendremos que indicar en la vista contenedora de nuestras páginas la etiqueta que habilita esta visualizacion `<router-outlet></router-outlet>`.
    -   Esta directiva podemos utilizarla porque en el módulo donde se encutra la vista tenemos importado el `app-routing.module` o en su defecto el `RouterModule`
-   En la parte vista que se encarga de seleccionar la vista a visualizar, en el elemento html correspondiente, deberemos indicar el atributo `routerLink="path_donde_navegar"` Este atributo lo podemos utilizar porque tenemos importado en nuestra aplicación el `RouterModule` en el módulo donde pertenezca la vista (si es en el app.module estará incluido en el `app-routing.module`)

```html
<li
    routerLink="region"
    routerLinkActive="active"
    class="list-group-item list-group-item-action"
>
    Buscar Región
</li>
```

-   Con el `routerLinkActive` indicamos al elemento html la clase que deberemos añadir cuando la ruta actual se encuentre activada (`active` es una clase propia de bootstrap)
-   En el caso de que la ruta indicada sea un string vacío `''` (página inicial mostrada), tenemos que incluir una atributo especial para que detecte correctamente cuando ese path se encuentra activo `[routerLinkActiveOptions]="{ exact: true }"`
-   Un caso similar lo vemos en la configuración de nuestras rutas en `app-routing.module`, donde tenemos que indicarle `pathMatch: 'full'`

```typescript
{
    path: '',
    component: PorPaisComponent,
    pathMatch: 'full',
},
```

-   En esta sección tanmbién se hace uso del retorno de `Observables` en las peticiones del servicio. Será el metodo que llama a este servicio el que tendrá que gestionar el `subscribe`

```typescript
return this.http.get(url)
```

prueba
