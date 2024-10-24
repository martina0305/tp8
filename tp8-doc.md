## Control de Acceso Roto (Broken Access Control)
* OWASP 2021 A01 *
### Prueba de concepto:

```javascript
app.post('/payment-method', (req, res) => {
    const { method } = req.body;
    const userId = req.session.user?.id;
    db.run('UPDATE users SET paymentMethod = ? WHERE id = ?', [method, userId]);
});
```
### Forma de explotación: 
Un atacante podría interceptar la solicitud y manipular el userId para cambiar el método de pago de otro usuario. Esto permite la modificación no autorizada de información sensible, lo que podría derivar en robo de fondos y comprometer la privacidad e integridad de los datos de los usuarios.
### Consecuencias:
La explotación de esta vulnerabilidad podría resultar en el robo de fondos si el atacante agrega un método de pago que controla. Además, compromete la privacidad de los datos y la integridad de la información, permitiendo la modificación no autorizada de detalles sensibles de otros usuarios.
### Por qué es malo:
Esta vulnerabilidad viola el principio de mínimo privilegio, comprometiendo la seguridad de los datos financieros. Además, puede llevar a pérdidas monetarias para los usuarios y la empresa, fraudes financieros, y posibles incumplimientos de regulaciones de protección de datos, dañando gravemente la reputación de la organización.
### Recomendaciones:
Implementar controles de autorización sólidos, validar adecuadamente las entradas de los usuarios, y utilizar tokens de sesión seguros. También es esencial establecer un sistema de monitoreo y realizar pruebas de seguridad regulares para detectar y prevenir accesos indebidos.

## Cross-Site Scripting (XSS)  
* OWASP 2021: A03 *

### Prueba de Concepto:  
```html
   app.get('/', (req, res) => {
       res.send(
           `<!DOCTYPE html>
           <html lang="es">
           <head>
               <meta charset="UTF-8">
               <meta name="viewport" content="width=device-width, initial-scale=1.0">
               <title>Verificar Diversión</title>
               <script>
                   async function checkFun() {
                       const response = await fetch('/check-fun', {
                           method: 'POST',
                           headers: {
                               'Content-Type': 'application/json'
                           },
                           body: JSON.stringify({ feedback: '¡Me estoy divirtiendo!' })
                       });
                       const data = await response.json();
                       alert(data.message); // Muestra un mensaje con el resultado
                   }
               </script>
           </head>
           <body>
               <h1>Verificación de Diversión</h1>
               <button onclick="checkFun()">¿Te estás divirtiendo?</button>
           </body>
           </html>`
       );
   });
```
### Forma de explotación:  
   Si el servidor devuelve datos sin una sanitización adecuada en la ruta ⁠ /check-fun ⁠, un atacante podría interceptar la respuesta, inyectar un script malicioso y hacer que el cliente lo ejecute en su navegador al recibir el mensaje a través del ⁠ alert⁠.

### Consecuencias:  
   - Ejecución de scripts maliciosos en el navegador del usuario.
   - Robo de cookies, tokens de sesión o información sensible.
   - Realización de acciones no autorizadas en nombre del usuario.

### Por qué es malo:  
   - Permite a un atacante ejecutar código arbitrario en el navegador del usuario.
   - Compromete la integridad y confidencialidad de los datos del usuario.
   - Da acceso al atacante a realizar acciones maliciosas en el sitio web, como realizar compras o acceder a información privada.

### Recomendaciones:  
   - Escapar y sanitizar cualquier dato enviado desde el servidor al cliente.
   - Implementar Content Security Policy (CSP) para mitigar la ejecución de scripts no confiables.
   - Evitar el uso de métodos como ⁠ innerHTML ⁠ o ⁠ eval ⁠ que puedan ejecutar código dinámico.
   - Validar y filtrar la entrada del usuario antes de procesarla.