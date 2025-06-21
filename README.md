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


