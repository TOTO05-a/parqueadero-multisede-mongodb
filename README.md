# Parqueaderos Multisede · MongoDB

Base de datos MongoDB para un sistema de parqueaderos con varias sedes: modelo con validación, datos de prueba, agregaciones analíticas, roles RBAC y transacciones.

![MongoDB](https://img.shields.io/badge/MongoDB-47A248?logo=mongodb&logoColor=white)
![mongosh](https://img.shields.io/badge/mongosh-scripts-4EA94B)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)

## Qué resuelve

*Campus Parking* administra parqueaderos en varias ciudades con hojas de cálculo locales. Este proyecto modela esa operación en MongoDB: usuarios con distintos niveles de acceso, vehículos, sedes con zonas (cupos, tarifas y tipos de vehículo permitidos) y el histórico de parqueos con tiempo y costo.

## Funcionalidades

- **Modelo híbrido** de cinco colecciones (`usuarios`, `vehiculos`, `sedes`, `zonas`, `parqueos`): referencias por `ObjectId`, campos *snapshot* para conservar histórico y embebidos solo cuando pertenecen a una única entidad.
- **Validación `$jsonSchema`** con `enum` y reglas de negocio, e **índices** únicos (documento, correo, placa, nombre de sede).
- **Datos de prueba** coherentes (sedes, zonas, usuarios, vehículos y parqueos activos y finalizados).
- **8 agregaciones** analíticas: ocupación, ingresos por sede, uso por cliente, frecuencia por tipo de vehículo, historial por usuario y reporte general multisede.
- **RBAC**: roles personalizados para administrador, empleado de sede y cliente, con script de pruebas de permisos.
- **Transacciones** con sesiones (`registrarIngreso` y `registrarSalida`) con `commitTransaction()` y `abortTransaction()` ante errores.

## Stack

MongoDB · mongosh · JavaScript · Docker Compose (instancia local). El driver `mongodb` de Node aparece solo en `docker/package.json`.

## Estructura

```text
├── db_config/db_config.js          # base, colecciones, $jsonSchema e índices
├── test_dataset/                   # datos de prueba (+ script de pruebas de validación)
├── aggregations/aggregations.js    # 8 agregaciones
├── roles/
│   ├── roles.js                    # roles y usuarios RBAC
│   └── pruebas_roles.js            # pruebas de permisos por rol
├── roles_exam/roles_exam.js        # roles del examen
├── transactions/transactions.js    # ingreso y salida transaccionales
└── docker/                         # docker-compose.yml y package.json
```

## Requisitos

- MongoDB (local o en Docker) y `mongosh`.
- Docker, solo si usas el `docker-compose.yml` incluido.

## Instalación y ejecución

1. Levanta MongoDB (el compose lo expone en el puerto `27018`):

   ```bash
   docker compose -f docker/docker-compose.yml up -d
   ```

2. Ejecuta los scripts en orden con `mongosh` (ajusta la cadena de conexión a tu instancia):

   ```bash
   mongosh "mongodb://localhost:27018" --file db_config/db_config.js
   mongosh "mongodb://localhost:27018" --file test_dataset/test_dataset.js
   mongosh "mongodb://localhost:27018" --file aggregations/aggregations.js
   mongosh "mongodb://localhost:27018" --file roles/roles.js
   ```

   Si el contenedor requiere autenticación, añade `-u <usuario> -p` (mongosh pedirá la contraseña) y `--authenticationDatabase admin`.
3. Las transacciones requieren un *replica set*. `transactions/transactions.js` define `registrarIngreso` y `registrarSalida`; cárgalo desde una sesión de `mongosh` o desde Node con el driver.

## Variables de entorno

No usa `.env`. La conexión y las credenciales se pasan por la cadena de `mongosh` o se definen en `docker/docker-compose.yml`. Las contraseñas de ejemplo son de demostración local: cámbialas si despliegas fuera de tu máquina.

## Autor

**Eduardo Antonio Cruz Bolaños** — [@TOTO05-a](https://github.com/TOTO05-a)
