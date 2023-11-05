+++
title = "Despliegue Automatizado con GitHub Actions y FTP"
date = 2023-11-05
updated = 2023-11-05
description = "Aprenda cómo configurar fácilmente un sistema de despliegue automatizado para su aplicación utilizando GitHub Actions y la transferencia de archivos FTP, ideal para repositorios alojados en GitHub con acceso a herramientas de gestión cPanel."

[taxonomies]
tags = ["Despliegue", "GitHub", "Automatización"]
+++

Automatizar su proceso de despliegue puede simplificar significativamente su flujo de trabajo. Esta guía lo guiará a través de los pasos para establecer un sistema de despliegue automatizado para sus repositorios alojados en GitHub, especialmente cuando su proveedor de alojamiento ofrece acceso a cPanel.

## 1. Configurar una Cuenta FTP en el Sitio Web de su Proveedor de Alojamiento
Para comenzar, es crucial crear una cuenta FTP en el sitio web de su proveedor de alojamiento. Especifique el directorio como "public_html" ya que es donde desea desplegar su aplicación. Dependiendo del marco o la biblioteca que esté utilizando, es posible que deba desplegar el contenido de diferentes carpetas en la carpeta "public_html" en el servidor.

Por ejemplo, si está utilizando Zola, su proyecto se construirá en la carpeta "public", y deberá desplegar el contenido de esta carpeta en el servidor. Profundizaremos en esto en más detalle en breve.

## 2. Utilizar GitHub Actions para Configurar el Sistema de Despliegue
### 2.1. Agregar Secretos del Repositorio
Antes de configurar el sistema de despliegue, necesitamos agregar las variables necesarias para la carga de archivos FTP en el repositorio. Siga estos pasos:

- Vaya a su repositorio y haga clic en "Configuración".
- En "Secretos y Variables" > "Acciones", haga clic en "Nuevo Secreto de Repositorio".
- Agregue tres secretos: FTP_PASSWORD, FTP_SERVER y FTP_USERNAME.

### 2.2. Configurar la Acción Automatizada
En su repositorio elegido, vaya a "Acciones" > "Nuevo Flujo de Trabajo" > "Configurar un flujo de trabajo usted mismo". Esto abrirá un editor interactivo en su ventana. Recomendamos cambiar el nombre de su archivo *.yml a algo como "deploy.yml" para mayor claridad. Luego, pegue el siguiente código en el editor:

```yml
name: Publicar sitio web en cPanel
on:
  push:
    branches:
    - main

jobs:
  FTP-Deploy-Action:
    name: Acción de Despliegue FTP
    runs-on: ubuntu-latest
    steps:
    - name: Comprobar el código
      uses: actions/checkout@v2.1.0
      with:
        fetch-depth: 2
    - name: Acción de Despliegue FTP
      uses: SamKirkland/FTP-Deploy-Action@4.3.0
      with:
        server: ${{ secrets.FTP_SERVER }}
        username: ${{ secrets.FTP_USERNAME }}
        password: ${{ secrets.FTP_PASSWORD }}
        protocol: ftp
        port: 21
        local-dir: public/
```

Tenga en cuenta lo siguiente:

1. Este archivo YAML tomará el contenido del directorio de salida de su aplicación y lo cargará en el servidor a través de FTP. Para Zola, el proyecto se construye en el directorio "public/". Sin embargo, para su aplicación, esta ruta podría ser diferente.
2. Dependiendo de si su rama principal se llama "main" o "master", es posible que deba ajustar la sección "branches" en el archivo YAML anterior.

## 3. Hacer un Push a la Rama Principal
Una vez que haya configurado el sistema de despliegue, todo lo que necesita hacer es realizar un push de nuevos cambios a su rama principal. Si todo está configurado correctamente, el archivo YAML debería desencadenar una acción que cargue su aplicación en el servidor que la aloja, haciendo que su proceso de despliegue sea sin complicaciones y automatizado.
