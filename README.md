# doc-api-js


Para crear una API con Express que realice consultas a SQL y exponga un endpoint, puedes seguir estos pasos:

### 1. Configura tu proyecto
1. **Crea un proyecto con npm**:
   ```bash
   mkdir my-express-api
   cd my-express-api
   npm init -y
   ```
2. **Instala las dependencias necesarias**:
   ```bash
   npm install express mysql2
   ```

### 2. Escribe el código de tu API
Aquí hay un ejemplo de una API básica que se conecta a una base de datos MySQL y expone un endpoint para obtener datos:

#### Código (`app.js`):
```javascript
const express = require('express');
const mysql = require('mysql2');

const app = express();
const PORT = 3000;

// Configuración de la conexión a la base de datos
const db = mysql.createConnection({
    host: 'localhost',      // Cambia esto por el host de tu base de datos
    user: 'root',           // Cambia esto por tu usuario
    password: 'password',   // Cambia esto por tu contraseña
    database: 'mi_base_datos' // Cambia esto por tu base de datos
});

// Conexión a la base de datos
db.connect((err) => {
    if (err) {
        console.error('Error al conectar a la base de datos:', err);
        return;
    }
    console.log('Conectado a la base de datos MySQL');
});

// Endpoint para obtener datos
app.get('/api/usuarios', (req, res) => {
    const query = 'SELECT * FROM usuarios'; // Cambia esto por tu consulta
    db.query(query, (err, results) => {
        if (err) {
            console.error('Error al ejecutar la consulta:', err);
            res.status(500).send('Error en el servidor');
            return;
        }
        res.json(results); // Envía los resultados como respuesta en JSON
    });
});

// Iniciar el servidor
app.listen(PORT, () => {
    console.log(`Servidor corriendo en http://localhost:${PORT}`);
});
```

### 3. Crea la tabla de ejemplo
Asegúrate de tener una tabla en tu base de datos llamada `usuarios`. Puedes usar la siguiente consulta SQL como ejemplo:

```sql
CREATE TABLE usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    correo VARCHAR(100)
);

INSERT INTO usuarios (nombre, correo)
VALUES
('Juan Pérez', 'juan@example.com'),
('Ana López', 'ana@example.com');
```

### 4. Prueba la API
1. Ejecuta el servidor:
   ```bash
   node app.js
   ```
2. Abre tu navegador o usa una herramienta como [Postman](https://www.postman.com/) o `curl` para probar el endpoint:
   ```bash
   curl http://localhost:3000/api/usuarios
   ```

Deberías ver una respuesta como esta:
```json
[
    { "id": 1, "nombre": "Juan Pérez", "correo": "juan@example.com" },
    { "id": 2, "nombre": "Ana López", "correo": "ana@example.com" }
]
```

### Opciones adicionales
- **Variables de entorno**: Usa paquetes como `dotenv` para gestionar las credenciales.
- **Middleware**: Usa middleware como `body-parser` para manejar datos en el cuerpo de las solicitudes.
- **Seguridad**: Implementa medidas como CORS y sanitización de consultas para evitar vulnerabilidades como inyecciones SQL.













---

---

---

---


## Si estás usando SQL Server en Azure y tienes problemas para conectarte

Guía paso a paso para configurar tu API con **SQL Server** en lugar de MySQL y resolver posibles problemas de conexión.




### 1. **Configura tu SQL Server en Azure**
1. **Asegúrate de que tu servidor permita conexiones externas:**
   - En el portal de Azure, ve a tu servidor SQL y selecciona **Cortafuegos y redes virtuales**.
   - Asegúrate de que la opción "Permitir acceso a los servicios de Azure" esté habilitada.
   - Agrega la dirección IP pública de tu máquina local (o red) para que pueda conectarse.

2. **Obtén la cadena de conexión:**
   - Ve a tu base de datos en Azure.
   - En la sección de **Cadenas de conexión**, selecciona **ADO.NET** para obtener el formato adecuado.

---

### 2. **Instala las dependencias necesarias**
Usaremos `mssql` para conectar con SQL Server:
```bash
npm install express mssql
```

---

### 3. **Escribe tu código para la API**
Crea un archivo llamado `app.js` con el siguiente código:

#### Código (`app.js`):
```javascript
const express = require('express');
const sql = require('mssql');

const app = express();
const PORT = 3000;

// Configuración de la base de datos
const dbConfig = {
    user: 'tu_usuario',        // Cambia por tu usuario
    password: 'tu_contraseña', // Cambia por tu contraseña
    server: 'tu_servidor.database.windows.net', // Cambia por tu servidor
    database: 'tu_base_datos', // Cambia por tu base de datos
    options: {
        encrypt: true,         // Requiere SSL en Azure
        enableArithAbort: true
    }
};

// Endpoint para obtener datos
app.get('/api/usuarios', async (req, res) => {
    try {
        // Conectar a la base de datos
        const pool = await sql.connect(dbConfig);

        // Realizar consulta
        const result = await pool.request().query('SELECT * FROM usuarios'); // Cambia la consulta según tu tabla

        res.json(result.recordset); // Devuelve los resultados en formato JSON
    } catch (err) {
        console.error('Error al consultar SQL Server:', err);
        res.status(500).send('Error en el servidor');
    }
});

// Iniciar el servidor
app.listen(PORT, () => {
    console.log(`Servidor corriendo en http://localhost:${PORT}`);
});
```

---

### 4. **Prueba la conexión**
1. Ejecuta el servidor:
   ```bash
   node app.js
   ```
2. Accede al endpoint:
   - Usa Postman o un navegador para probar `http://localhost:3000/api/usuarios`.

Si la conexión es exitosa, deberías ver los datos de la tabla `usuarios`.

---

### 5. **Posibles errores y soluciones**

#### **Error de conexión: ECONNREFUSED**
- Asegúrate de que tu servidor SQL en Azure permite conexiones desde tu IP pública.
- Verifica que las credenciales (usuario, contraseña, servidor y base de datos) sean correctas.

#### **Error de tiempo de espera (Timeout)**
- Asegúrate de que el firewall en Azure esté configurado para permitir tu IP.
- Verifica que la opción `encrypt: true` esté habilitada, ya que Azure requiere conexiones seguras.

#### **Error en el nombre del servidor**
- El formato del servidor debe ser algo como `mi-servidor.database.windows.net`. Verifica en el portal de Azure.

---

### 6. **Recomendaciones adicionales**
- **Variables de entorno**: Usa un archivo `.env` para manejar la configuración del servidor:
   1. Instala `dotenv`:
      ```bash
      npm install dotenv
      ```
   2. Configura tu archivo `.env`:
      ```
      DB_USER=tu_usuario
      DB_PASSWORD=tu_contraseña
      DB_SERVER=tu_servidor.database.windows.net
      DB_DATABASE=tu_base_datos
      ```
   3. Modifica tu código para usar estas variables:
      ```javascript
      require('dotenv').config();

      const dbConfig = {
          user: process.env.DB_USER,
          password: process.env.DB_PASSWORD,
          server: process.env.DB_SERVER,
          database: process.env.DB_DATABASE,
          options: {
              encrypt: true,
              enableArithAbort: true
          }
      };
      ```

Con esta configuración, deberías poder conectar tu API a SQL Server en Azure sin problemas.
