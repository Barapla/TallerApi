# TallerApi

# Iniciar un proyecto de nodejs express TS desde 0

Crear carpeta y entrar

- mkdir node_project
- cd node_project

Crear archivo package.json

- npm init -y

Instalar dependencias

- npm install -D typescript           
- npm install -D tslint
- npm install -S express
- npm install -D @types/express
    
    npm install -D typescript
        typescript: Esta dependencia instala el compilador de TypeScript, que es necesario para transpilar el código TypeScript a JavaScript. Se instala como una dependencia de desarrollo (-D) porque solo es necesario durante el proceso de desarrollo.

    npm install -D tslint
        tslint: TSLint es una herramienta de análisis estático para TypeScript que ayuda a mantener un código consistente y de alta calidad. Proporciona reglas configurables para el estilo de código y la detección de errores potenciales. Al igual que TypeScript, se instala como una dependencia de desarrollo (-D) ya que solo se necesita durante el desarrollo.

    npm install -S express
        express: Express es un popular framework para aplicaciones web en Node.js. Este comando instala Express como una dependencia de producción (-S o --save) ya que es necesario en tiempo de ejecución para que la aplicación funcione.

    npm install -D @types/express
        @types/express: Estas son las definiciones de tipos TypeScript para Express. Como TypeScript es un lenguaje de tipado estático, estas definiciones de tipos proporcionan información sobre la estructura y el comportamiento de Express, permitiendo un desarrollo más preciso y seguro en TypeScript. Se instala como una dependencia de desarrollo (-D) ya que solo es necesario durante la fase de desarrollo.

Creamos el tsconfig.json

- tsc --init
(esta es la configuracion basica)
```json
{
  "compilerOptions": {
    "module": "commonjs",
    "esModuleInterop": true,
    "target": "es6",
    "moduleResolution": "node",
    "sourceMap": true,
    "outDir": "dist"
  },
  "lib": ["es2015"]
}
```

Creamos nuestro tslint.json

- ./node_modules/.bin/tslint --init

Actualizamos el package.json
```json
  {
    "main": "dist/app.js",
    "scripts": {
      "start": "tsc && node dist/app.js",
      "test": "echo \"Error: no test specified\" && exit 1"
    }
  }
```

Creamos la carpeta "src"

- mkdir src

Dentro de esta carpeta un archivo main.ts o app.ts o index.ts (este es tu punto de entrada)

- touch app.ts

Dentro de este archivo escribimos

```javascript
//importar express
import express from 'express';

// Crear una nueva instancia de express
const app = express();

// Definir el puerto
const port = 3000;

// Crear una ruta
app.get('/', (req, res) => {
  res.send('Hola mundo!');
});

// Iniciar el servidor
app.listen(port, () => {
   console.log(`Servidor en el pueto: ${port}`);
});

```

Actualizar el package.json
```json
  "scripts": {
    "start": "tsc && node dist/app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```


# Pasos adicionales

Instalar y configurar nodemon

- npm install -D nodemon

Crear a nivel raiz el archivo nodemon.json

- touch nodemon.json
  
Escribir dentro de
```json
{
    "watch": ["src"], // esta es nuestra carpeta que contiene todos nuestros archivos
    "ext": ".ts,.js", // este son las extensiones que estara munitoreando
    "ignore": [], // si existe algo que ignore lo agregamos aqui
    "exec": "npx ts-node ./src/app.ts" 
}
```


Actualizamos nuestro script en el package.json
```json
  "scripts": {
    "dev": "npx nodemon",
    "start": "tsc && node dist/app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

# Un poco de arquitectura para Node

Leer esta documentacion

https://www.linkedin.com/pulse/clean-architecture-en-node-y-express-odannys-de-la-cruz/?originalSubdomain=es

# Recomendacion utilizar prisma

Documentacion

https://www.prisma.io/

Instalar Prisma CLI
(si estas usando unix agrega "sudo" antes del comando porque requieres permisos)
- npm install -g @prisma/cli

Inicializar el proyecto de Prisma

- npx prisma init

Esto nos generara un carpeta "prisma" que contendra "schema.prisma" y un archivo ".env" (para que funcione este archivo ocupamos instalar una dependencia)

- npm install dotenv

En el archivo ".env" estara dentro nuestra variable de "DATABASE_URL"
Esta contendra la cadena de conexion hacia nuestra base de datos, necesitamos poner la nuestra

``` javascript
// schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql" //gestor de base de datos
  url      = env("DATABASE_URL")
}

model User {
  id    Int     @id @default(autoincrement())
  name  String
  email String  @unique
  posts Post[]
}

model Post {
  id    Int     @id @default(autoincrement())
  title String
  body  String
  userId Int
  user  User    @relation(fields: [userId], references: [id])
}

```

Generar el Prisma Client

- npx prisma generate

```javascript
// Importa el Prisma Client
import { PrismaClient } from '@prisma/client';

// Crea una instancia del Prisma Client
const prisma = new PrismaClient();

// Usa el Prisma Client para interactuar con la base de datos
const users = await prisma.user.findMany();
```

# Prisma Migrate:

Inicializar migraciones:

- npx prisma migrate dev --name init

Esto creara una carpeta llamada migrations y ejecutara la migracion inicial

Editar los modelos del archivo "schema.prisma"

Crear migración:

Una vez hechos los cambios en los modelos, ejecuta el comando de migracion

- npx prisma migrate dev --name nombre_de_la_migracion

Aplicar migracion

- npx prisma migrate deploy

# Prisma Introspect:

Generar los modelos del "schema.prisma" desde una base de datos existente:

- npx prisma introspect

Aplicar los cambios en el modelo

- npx prisma generate
