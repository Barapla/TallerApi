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

(Windows) Instalamos TSlint
- npm install -g typescript

Creamos nuestro tslint.json

- ./node_modules/.bin/tslint --init
- (Windows) npx tslint --init

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
    "exec": "npx ts-node ./src/index.ts" 
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

# Estructura del Proyecto

Para la estructura que llevaremos a cabo es la siguiente:
```
src/
├── controllers/
├── core/
│ ├── config/
│ ├── dto/
│ ├── mappers/
│ ├── middlewares/
│ └── utils/
├── routes/
├── services/
└── index.ts
```

## Descripción de Directorios

- **controllers**: Contiene los controladores que manejan las solicitudes entrantes y devuelven respuestas al cliente.

- **core**: Directorio principal para archivos que forman la base del proyecto.
  - **config**: Contiene archivos de configuración para diferentes entornos y aspectos del proyecto.
  - **dto** (Data Transfer Objects): Define cómo los datos serán enviados sobre la red.
  - **mappers**: Utilizados para mapear de una forma de datos a otra, por ejemplo de entidades de base de datos a DTOs.
  - **middlewares**: Middleware de Express que se utiliza para manejar solicitudes, realizar acciones antes de que lleguen a los controladores, o manejar errores.
  - **utils**: Funciones de utilidad generales usadas en todo el proyecto.

- **routes**: Define las rutas del servidor que mapean las solicitudes a los controladores correspondientes.

- **services**: Contiene la lógica de negocio y la lógica para interactuar con la base de datos.

- **index.ts**: Punto de entrada del proyecto donde se configura y se lanza el servidor.

## Notas Adicionales

Asegúrese de documentar cada parte de su código a medida que su proyecto se expande. Mantener un código bien documentado es esencial para la escalabilidad y el mantenimiento a largo plazo.



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

# Configuracion DB

- En nuestra carpeta core/config creamos un archivo database.ts

```
import { PrismaClient } from '@prisma/client';
export const prismaClient = new PrismaClient();
```

# Estandarizar respuestas

- En nuestra carpeta core/dto creamos un archivo TResult.ts

```
export interface TResult<T> {
    data: T;
    message: string[];
    success: boolean;
}
```

- En nuestra carpeta core/mappers creamos un archivo tresult.mappers.ts

```
import { TResult } from "../dto/TResult";

export const createTResult = <T>(data: T, message: string[] = [], ): TResult<T> => {
    const hasMessage = message.length > 0;
    const success = !hasMessage;

    return {
        data,
        message: hasMessage ? message : ["Success"],
        success
    }

}
```

# Creacion DTO

- En nuestra carpeta core/dto creamos un archivo developer.dto.ts

```
export interface DeveloperDto {
    uuid: string;
    name: string;
    description: string;
    founded: Date;
}

export interface DeveloperCreateDto {
    name: string;
    description: string;
    founded: Date;
}

export interface DeveloperUpdateDto {
    uuid: string;
    name: string;
    description: string;
    founded: Date;
}
```

- En nuestra carpeta core/mappers creamos un archivo llamado developer.mappers.ts

```
import { Developer } from "@prisma/client";
import { DeveloperDto } from "../dto/developer.dto";

export const DeveloperEntityToDto = (developer: Developer): DeveloperDto => {
    return {
        uuid: developer.uuid,
        name: developer.name,
        description: developer.description || "",
        founded: developer.founded || new Date() // Add conditional check to handle null value and assign default value
    };
}  

export const DevelopersEntityToDto = (developers: Developer[]): DeveloperDto[] => {
    return developers.map(developer => DeveloperEntityToDto(developer));
}
```

# Creacion de Servicio

- En nuestra carpeta de services creamos un archivo llamado developer.service.ts

```
import { prismaClient } from "../core/config/database";
import { DeveloperCreateDto, DeveloperUpdateDto } from "../core/dto/developer.dto";


export const getAll = async () => {
    try {
      const developers = await prismaClient.developer.findMany();
  
      return developers;
    } catch (err) {
      throw new Error("Error getting developers");
    }
}

export const getByUuid = async (uuid: string) => {
  try {
    const developer = await prismaClient.developer.findUnique({
      where: {
        uuid,
      },
    });

    return developer;
  } catch (err) {
    throw new Error("Error getting developer");
  }
}

export const register = async (developer: DeveloperCreateDto) => {
  try {
    const newDeveloper = await prismaClient.developer.create({
      data: {
        ...developer,
      },
    });

    return newDeveloper;
  } catch (err) {
    console.log(err);
    throw new Error("Error creating developer");
  }
};

export const update = async (developer: DeveloperUpdateDto) => {
  try {
    const updateDeveloper = await prismaClient.developer.update({
      where: {
        uuid: developer.uuid,
      },
      data: {
        ...developer,
      },
    });
    return updateDeveloper;
  } catch (err) {
    console.log(err);
    throw new Error("Error updating developer");
  }
}

export const deleteByUuid = async (uuid: string) => {
  try {
    await prismaClient.developer.delete({
      where: {
        uuid,
      },
    });
  } catch (err) {
    throw new Error("Error deleting developer");
  }
}
```

# Crear rutas

- En la carpeta routes creamos un archivo llamado developer.route.ts

```
import express from "express";
import { createDeveloper, deleteDeveloper, getDeveloper, getDevelopers, updateDeveloper } from "../controllers/developer.controller";

const router = express.Router();

router.get("/", getDevelopers);
router.get("/:uuid", getDeveloper);
router.post("/", createDeveloper);
router.put("/:uuid", updateDeveloper);
router.delete("/:uuid", deleteDeveloper);


export default router;
```

# Importar rutas

- En nuestro archivo index.ts importamos las rutas

```
//importar express
import express from 'express';

import developerRoute from './routes/developer.route';

// Crear una nueva instancia de express
const app = express();

// Definir el puerto
const port = 3000;

app.use([express.json()])


app.use("/developers", developerRoute);

// Crear una ruta
app.get('/', (req, res) => {
  res.send('Hola mundo!');
});

// Iniciar el servidor
app.listen(port, () => {
   console.log(`Servidor en el pueto: ${port}`);
});
```

# Swagger

## Instalamos las dependencias necesarias

- npm install swagger-jsdoc

- npm install swagger-ui-express

- npm install yamljs

- npm i --save-dev @types/yamljs

- npm i --save-dev @types/swagger-ui-express

## Swagger.yaml

Creamos un archivo Swagger.yaml en la raiz y seguimos la siguiente estructura 

```
openapi: 3.0.0
info:
  title: API de Ejemplo
  version: 1.0.0
  description: Una API de ejemplo para demostrar Swagger en Node.js
servers:
  - url: 'http://localhost:3000/'
paths:
  /developers:
    get:
      tags:
        - developers
      summary: Get all developers
      operationId: getDevelopers
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Developer'
    post:
      tags:
        - developers
      summary: Create a developer
      operationId: createDeveloper
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DeveloperCreateDto'
      responses:
        '201':
          description: Created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Developer'
  /developers/{uuid}:
    get:
      tags:
        - developers
      summary: Get developer by uuid
      operationId: getDeveloper
      parameters:
        - name: uuid
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Developer'
    put:
      tags:
        - developers
      summary: Update developer by uuid
      operationId: updateDeveloper
      parameters:
        - name: uuid
          in: path
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DeveloperUpdateDto'
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Developer'
    delete:
      tags:
        - developers
      summary: Delete game by uuid
      operationId: deleteGame
      parameters:
        - name: uuid
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '204':
          description: No Content
components:
  schemas:
    Developer:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        name:
          type: string
        description:
          type: string
    DeveloperCreateDto:
      type: object
      required:
        - name
        - description
      properties:
        name:
          type: string
        description:
          type: string
    DeveloperUpdateDto:
      type: object
      properties:
        name:
          type: string
        description:
          type: string
```

### Explicacion estructura

#### 1.- Versión de OpenAPI:

```
openapi: 3.0.0
```

Esto especifica la versión de la especificación OpenAPI que se está utilizando para este documento. OpenAPI 3.0.0 introduce muchas mejoras sobre versiones anteriores como Swagger 2.0.

#### 2.- Información General:

```
info:
  title: API de Ejemplo
  version: 1.0.0
  description: Una API de ejemplo para demostrar Swagger en Node.js
```

- **title**: El nombre de la API.
- **version**: La versión de la API.
- **description**: Una breve descripción de lo que hace la API.

#### 3.- Servidores:

```
servers:
  - url: 'http://localhost:3000/'
```

Esto define una lista de servidores donde la API está disponible. En este caso, la API se encuentra disponible en http://localhost:3000/.


#### 4.- Rutas:

Dentro de paths, defines las rutas individuales (endpoints) que la API expone y los métodos HTTP disponibles para esas rutas.

```
/developers:
  get:
    ...
  post:
    ...
/developers/{uuid}:
  get:
    ...
  put:
    ...
  delete:
    ...
```

- Cada operación (`get`, `post`, `put`, `delete`) dentro de una ruta puede tener varias propiedades:
  - **tags**: Ayuda a agrupar operaciones por recursos o cualquier otro cualificador.
  - **summary**: Un breve resumen de lo que hace la operación.
  - **operationId**: Un identificador único para la operación.
  - **security**: Define los esquemas de seguridad que se aplican a la operación.
  - **parameters**: Parámetros necesarios para la operación. En el caso de los endpoints que usan `{uuid}`, esto indica un parámetro en la ruta.
  - **requestBody**: Define el cuerpo de la solicitud para métodos que envían datos (como `post` y `put`).
  - **responses**: Define las respuestas que pueden ser devueltas por la operación.



#### 5.- Componentes:

```
components:
  schemas:
    Developer:
      ...
    DeveloperCreateDto:
      ...
    DeveloperUpdateDto:
      ...
```

Aquí defines los schemas que son utilizados en diferentes partes de tu API. Estos esquemas son modelos de datos que describen la estructura de los objetos JSON utilizados en las solicitudes y respuestas.Por ejemplo, Developer puede representar cómo se ve un desarrollador en tu sistema, mientras que DeveloperCreateDto podría ser un objeto utilizado específicamente para crear un nuevo desarrollador.

## Implementacion

En nuestro archivo index.ts agregamos las dependencias y lo configuramos para que lea el swagger

```
//importar express
import express from 'express';
import swaggerUi from "swagger-ui-express";
import YAML from "yamljs";

import developerRoute from './routes/developer.route';


// Crear una nueva instancia de express
const app = express();

// Definir el puerto
const port = 3000;

app.use([express.json()])

app.use(
  "/swagger",
  swaggerUi.serve,
  (req: express.Request, res: express.Response, next: express.NextFunction) => {
    const swaggerDocument = YAML.load("./swagger.yaml");
    const swaggerUiHandler = swaggerUi.setup(swaggerDocument);
    swaggerUiHandler(req, res, next);
  }
);

app.use("/developers", developerRoute);

// Crear una ruta
app.get('/', (req, res) => {
  res.send('Hola mundo!');
});

// Iniciar el servidor
app.listen(port, () => {
   console.log(`Servidor en el pueto: ${port}`);
});
```

# Auth

Para el siguiente paso vamos a necesitar una tabla de Usuarios para crear un modulo de registro, inicio de sesion y authorizacion

## Configuracion variables de entorno

Primero en la carpeta core/utils creamos un archivo llamado config.ts

```
export const getConfig = (key: string) => {
  const value = process.env[key];

  if (value === undefined) {
    throw new Error(`Missing environment variable: ${key}`);
  }
  return value;
};
```

## APP_SECRET

En la carpeta core/config creamos nuestro archivo config.ts

```
import dotenv from 'dotenv';
import { getConfig } from '../utils/config';
dotenv.config();

export const secretKey = getConfig('APP_SECRET');
```

