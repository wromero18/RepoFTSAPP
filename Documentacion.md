✅ 1. Explicación del funcionamiento del sistema
FTSApp es un sistema completo para la gestión segura de archivos vía web, dividido en tres capas principales:

Frontend (UI web): una interfaz amigable en HTML, CSS y JS puro que permite al usuario subir, listar, descargar, renombrar y eliminar archivos. Se comunica con el backend mediante fetch usando HTTPS.

Servidor web (web-server.js): un servidor en Node.js con Express que:

Sirve la UI web sobre HTTPS.

Expone una API REST (/api/files) para interactuar con los archivos.

Utiliza multer para manejar archivos subidos temporalmente.

Se conecta con un servidor de archivos seguro mediante un cliente personalizado TLS (tls-client.js), y envía comandos como LIST, GET, PUT, DELETE, y RENAME.

Servidor seguro de archivos (secure-server.js): recibe comandos desde el servidor web vía una conexión TLS. Se encarga de:

Listar archivos almacenados localmente.

Recibir archivos y guardarlos.

Enviar archivos al cliente.

Renombrar o eliminar archivos solicitados.

Todo el sistema opera sobre protocolos cifrados (HTTPS y TLS) para garantizar privacidad y seguridad en las transferencias.

✅ 2. Instrucciones para ejecutar el servidor y conectarse como cliente
Paso 1: Instalar dependencias
npm install
Paso 2: Generar certificados TLS autofirmados
mkdir certs
openssl req -x509 -newkey rsa:2048 -nodes -keyout certs/server-key.pem -out certs/server-cert.pem -days 365
Paso 3: Iniciar los servidores (en terminales separadas)
a) Servidor seguro de archivos
node secure-server.js
b) Servidor web con interfaz UI
node web-server.js
Paso 4: Acceder a la interfaz como cliente
Abrir en el navegador:
https://localhost:3000
El navegador puede advertir sobre el certificado autofirmado. Aceptar para continuar.

✅ 3. Ejemplos de uso y pruebas realizadas
Se realizaron pruebas completas desde la interfaz web:

 Subida de archivos:
Se subió un archivo .txt mediante el formulario.

Resultado: archivo transferido vía POST /api/files al servidor seguro y listado correctamente en la tabla.

 Visualización:
Al recargar la página, el archivo seguía listado.

Resultado: prueba exitosa de GET /api/files usando el comando LIST.

 Descarga:
Se hizo clic en “Descargar”.

Resultado: el archivo se descargó correctamente desde el backend vía GET /api/files/:filename → GET.

 Renombrar:
Se hizo clic en “Renombrar” y se asignó un nuevo nombre.

Resultado: la tabla se actualizó y el archivo fue renombrado en disco.

 Eliminación:
Se probó la opción “Eliminar”.

Resultado: archivo eliminado correctamente vía DELETE /api/files/:filename.

 Casos adicionales:
Se intentó subir sin seleccionar archivo → la app mostró alerta.

Se probó renombrar sin escribir nombre → acción cancelada.

Se verificó que los archivos temporales subidos se eliminaron correctamente (fs.unlink).

Tambien se realizo la Prueba de ver que pasa si Apagamos el servidor TLS y ver qué errores aparecen al operar.
(es decir, simular una falla del backend para observar el manejo de errores.)

Pasos realizados:
1. Inicio normal:

Se arrancaron secure-server.js y web-server.js.

Se accedió a la UI normalmente desde https://localhost:3000.

Se confirmó que la lista de archivos se mostraba correctamente (GET /api/files funcionando).

2. Simulación de fallo:

Se cerró manualmente el proceso del secure-server.js, dejando solo el web-server.js corriendo.

Sin recargar la página, se intentaron las siguientes acciones desde la UI:

Volver a listar archivos

Subir un archivo nuevo

Descargar un archivo existente

3. Observaciones desde la interfaz:

Al listar archivos: mensaje en tabla → “Error al cargar archivos. Intente de nuevo.”

Al subir archivo: apareció alerta con mensaje de error personalizado.

Al descargar archivo: ventana no se abrió o devolvió error de red.

El navegador no se bloqueó; la UI permaneció operativa, pero sin poder conectarse al backend.

Resultado esperado:
El sistema detecta la desconexión del servidor TLS y muestra mensajes de error claros al usuario.

Las acciones fallidas no afectan la estabilidad del navegador ni del web server.

Se validó que el manejo de errores en web-server.js con try/catch y respuestas res.status(500) funciona correctamente.
   



