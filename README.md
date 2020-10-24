Desarrollo de Módulos y Themes en Drupal 8
===

>Creación de tres contenedores en Docker con una imagen de *Drupal* ***8.9.7***
>preparada para el desarrollo de módulos y themes (incluye XDebug).

[![version][version-badge]][changelog]
[![Licencia][license-badge]][license]
[![Donate][donate-badge]][donate-url]

## Instalación

* ### Requerimientos

  La configuración de los volumenes en Docker en el docker-compose.yml está
  pensada para ser usada principalmente bajo Windows 10, por lo que será
  necesario tener instalado Docker bajo este sistema operativo.

  >Se recomienda el uso de **VSCode** con la extensión de *Docker* instalada.
  >Esto facilitará el uso de las tareas preconfiguradas incluídas en este
  >repositorio.

* ### Instalación manual

  * Bajar este repositorio y colocarlo en la carpeta en la que deseamos
    trabajar.
  * Abrir el proyecto con VSCode.
  * Ejecutar la tarea *"Ejecutar TODO"*

  #### Consideraciones sobre la instalación:
  La primera vez que se ejecuta el *docker-compose.yml* se generan las imágenes
  necesarias y se crean los tres contenedores (Drupal, MySql y PHPMyAdmin).

  La imagen de MySql contiene un script que genera la base de datos y su
  contenido por defecto por lo que deberemos esperar a que en nuestra consola
  se muestre un mensaje como el siguiente:

  ![Primer paso](https://res.cloudinary.com/onovas/image/upload/v1603466946/GitHub/Docker%20-%20D8%20Dev/drupal_mysql_1_sxuzzs.png)
  Esto nos indica que se están creando las tablas e insertando los datos en las mismas.

  ![Segundo paso](https://res.cloudinary.com/onovas/image/upload/v1603466964/GitHub/Docker%20-%20D8%20Dev/drupal_mysql_2_v4mzei.png)
  Una vez se muestre este otro mensaje en nuestra consola ya estaremos listos
  para ejecutar nuestras peticiones a los contenedores.

  >Debemos tener en cuenta que, según las características de nuestra máquina,
  >el proceso completo puede demorarse hasta 30min en la primera ejecución.
  >
  >En ejecuciones posteriores, la puesta en marcha de los contenedores es
  >prácticamente inmediata.

  #### Accediendo a nuestros contenedores:
  1. El acceso a Drupal se efectúa en la url **localhost:8080**
     |Usuario|Contraseña|
     |-|-|
     |admin|password|
     >El contenedor de Drupal también tiene expuesto el puerto 9000 para poder
     >usar XDebug.

  2. El acceso a PHPMyAdmin se efectúa en la url **localhost:8888**
     |Usuario|Contraseña|
     |-|-|
     |admin|password|

  3. Por su parte, MySQL no tiene ningún puerto expuesto a nuestra máquina
     local.

  #### Volúmenes para la persistencia de datos:
  El contenedor de Drupal tiene asociados tres volúmenes:
  * El primero apunta a la carpeta: */modules/custom*
  * El segundo apunta a la carpeta: */themes/custom*
  * El tercero apunta a la carptes: */libraries*

  El contenedor de MySql tiene asociado un único volúmen para garantizar la
  persistencia de los datos en la BBDD.

## Autor(es)
- Óscar Novás - [OscarNovas.com][mi-web]

## Créditos
Aquí puedes comprobar la lista de [contribuyentes][contributors]
a este repositorio.

## Histórico de cambios
Aquí puedes comprobar la lista de [cambios][changelog] efectuados hasta el
momento.

---
⌨️ con ❤️ por [Óscar Novás][mi-web] 😊

[mi-web]: https://oscarnovas.com "for developers"

[version]: v0.0.1
[version-badge]: https://img.shields.io/badge/version-0.0.1-blue.svg

[license]: LICENSE.md
[license-badge]: https://img.shields.io/github/license/oscarnovasf/docker-d8-dev "Leer la licencia"

[changelog]: CHANGELOG.md "Histórico de cambios"
[contributors]: https://github.com/oscarnovasf/docker-d8-dev/contributors "Ver contribuyentes"

[donate-badge]: https://img.shields.io/badge/Donate-PayPal-green.svg
[donate-url]: https://paypal.me/oscarnovasf "Haz una donación"
