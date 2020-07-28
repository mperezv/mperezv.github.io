---
layout: post
title:  "Cómo añadir una barra de navegación a tu aplicación Rails con Tailwind"
date:   2020-07-27 23:48 +0200
categories: jekyll update
tags: rails tailwind
---
 
Esta semana estoy estudiando las vistas de Ruby on Rails y la parte de front en general. Ahora mismo tengo una aplicación que es un desastre a nivel de front. Tener tiene pero es un HTML casi a pelo, vaya. 

En este contexto he decidido empezar por ponerle una barra de navegación con [Tailwind](https://tailwindcss.com/) a mi proyecto.


## Tailwind: instalación y configuración

Tailwind es un _framework_ de CSS (como [Bootstrap](https://getbootstrap.com)) "altamente customizable, de bajo nivel y que te da todos los bloques que necesitas para construir diseños a medida sin ningún molesto estilo terco contra el que tienes que luchar para sobreescribirlo". 

Funciona de una manera similar a Bootstrap, con clases de CSS que puedes añadir a tu código y unos ficheros SCSS que puedes sobreescribir para personalizar esas clases. 

### Instalación

Podemos instalar Tailwind con npm o con yarn.

```bash
# Utilizando npm
npm install tailwindcss

# Utilizando Yarn
yarn add tailwindcss
```

Ahora necesitaremos un fichero de configuración que se llame `tailwind.config.js` en la raíz de nuestro proyecto. Podemos crearlo de dos maneras:

```bash
# Con la ruta
./node_modules/.bin/tailwind init

# Con npx
npx tailwindcss init
```

Si quisiéramos darle otro nombre al fichero de configuración, le tenemos que pasar al comando el nuevo nombre como parámetro.

```bash
npx tailwindcss init tailwindcss-configuración.js
```

En el caso de que utilicemos un nombre de fichero personalizado, necesitaremos especificarlo cuando se incluya Tailwind como plugin en tu configuración PostCSS también:

```javascript
// postcss.config.js
module.exports = {
  plugins: [
    require('tailwindcss')('./tailwindcss-configuración.js'),
  ],
}
```

También en la raíz de nuestro proyecto debería haber un fichero llamado `postcss.config.js` al que le añadiremos `require('tailwindcss')` y `require('autoprefixer')` para que taliwind se construya como plugin en tu proyecto:

```javascript
//postcss.config.js

module.exports = {
  plugins: [
    require('autoprefixer'),    
    require('postcss-import'),
    require('tailwindcss'),
    require('postcss-flexbugs-fixes'),
    require('postcss-preset-env')({
      autoprefixer: {
        flexbox: 'no-2009'
      },
      stage: 3
    })
  ]
}
```

### Configuración

Para la configuración de Tailwind crearemos una hoja de estilos en `app/javascript/src` con:

```bash
touch app/javascript/src/application.scss
```

Como estamos utilizando `postcss-import` (está en el fichero `postcss.config.js`), la documentación nos indica que tenemos que utilizar los siguientes _imports_ en el fichero que acabamos de crear.

```scss
// app/javascript/src/application.scss

@import "tailwindcss/base";
@import "tailwindcss/components";
@import "tailwindcss/utilities";
```

También tendremos que añadir el import de este fichero en `app/javascript/packs/application.js`:

```javascript
import '../src/application.scss'
```

Por último, tendremos que indicar al _layout_ de la aplicación que nos cargue esas hojas de estilos y no las que tiene enlazadas. Esto lo haremos añadiendo las siguientes etiquetas a `app/views/layouts/applicacion.html.erb`.

```
<%= stylesheet_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>

```

## Crear y añadir una barra de navegación

Hay dos conceptos que tendremos que tener claros a la hora de hacer una barra de navegación: **_layouts_** y **vistas parciales**.

Las _layouts_ o plantillas son las páginas de erb que se cargarán como plantilla en nuestras vistas. Para cargar la plantilla que toque, Rails busca primero un fichero en `app/views/layouts` con el mismo nombre base que el controlador. Es decir, si tenemos un `ArticlesController` buscará un `articles.html.erb`. Si no hay una plantilla específica, cargará `app/views/layouts/application.html.erb` o `app/views/layouts/application.builder`.

Si quisiéramos declarar una plantilla específica para toda la aplicación con un nombre diferente al por defecto, tendríamos que sobreescribirlo desde el `ApplicationController` con la sentencia `layout "nuestra_plantilla"`.

Estas plantillas tienen nuestro código y una, o varias, etiquetas `<%= yield %>` que son los espacios que se rellenarán con cada vista.

Para no tener que reescribir la barra de navegación en todas nuestras páginas, la añadiremos a nuestra _layout_ `application.html.erb`.

Podríamos o bien escribir directamente nuestro código sobre el `yield`, o (más ordenado) añadir una vista parcial con el código de nuestra barra de navegación. 

Las vistas parciales son archivos con código erb cuyo nombre empieza por un guión bajo y que cargaremos en el resto de vistas con el método `render` y el nombre de la vista parcial sin el guión bajo. 

Así, para crear nuestra barra de navegación, crearemos un directorio `application` o `shared` en `app/views` y, dentro, un fichero que se llame `_navbar.html.erb` en el cual podríamos poner, por ejemplo, el siguiente código:


```
    <nav class="flex items-center justify-between flex-wrap bg-teal-300 p-6">

      <%= link_to image_tag('logo.png'), welcome_index_path %>

      <div class="w-full block flex-grow lg:flex lg:items-center lg:w-auto">
        <div class="text-sm lg:flex-grow">
          <%= link_to 'Nuevo artículo', new_article_path, class: 'block mt-4 lg:inline-block lg:mt-0 text-white hover:text-white mr-4' %>
          <%= link_to 'Blog', controller: 'articles', class: 'block mt-4 lg:inline-block lg:mt-0 text-white hover:text-white' %>
        </div>
      </div>
    </nav>
```

En nuestra _layout_ añadiremos la siguiente línea:

`<%= render "application/navbar" %>`

¡Y ya está! Ahora se cargará nuestra barra de navegación en todas las páginas de nuestra aplicación. 