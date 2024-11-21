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
