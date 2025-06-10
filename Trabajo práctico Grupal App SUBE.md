```table-of-contents
```
## 1. Definición de Ideas o Requisitos según el estándar IEEE 830
### 1.1 Requisitos Funcionales

1. Registro de usuario: El sistema debe permitir a un usuario registrarse con sus datos personales (nombre, DNI, correo electrónico, contraseña).    
2. Asociación de tarjeta SUBE: El sistema debe permitir asociar una o más tarjetas SUBE al perfil del usuario mediante el número de tarjeta.      
3. Consulta de saldo: El sistema debe permitir visualizar el saldo actualizado de cada tarjeta SUBE asociada.  
4. Consulta de últimos movimientos: El sistema debe mostrar al usuario los últimos movimientos de la tarjeta SUBE (viajes realizados, recargas).  
5. Recarga de saldo: El sistema debe permitir al usuario realizar recargas virtuales a su tarjeta SUBE mediante medios de pago digitales (tarjeta de crédito/débito, billeteras virtuales).  
6. Notificaciones: El sistema debe enviar alertas por bajo saldo y confirmación de recargas o vinculaciones de tarjeta.  
7. Gestión de múltiples tarjetas: El usuario debe poder gestionar varias tarjetas desde una misma cuenta (por ejemplo, familiares).  

### 1.2 Requisitos No Funcionales

1. Disponibilidad: La aplicación debe estar disponible 24/7 con un 99% de uptime mensual.      
2. Seguridad: La app debe cifrar las contraseñas y los datos personales sensibles de los usuarios.  
3. Usabilidad: La interfaz debe ser intuitiva, compatible con dispositivos móviles y accesible para usuarios con dificultades visuales.  
4. Escalabilidad: El sistema debe permitir la incorporación de nuevas funcionalidades sin comprometer el rendimiento.  
5. Compatibilidad: La aplicación debe ser funcional en Android, iOS y versión web.  
6. Rendimiento: Las operaciones principales (consultas de saldo, recargas) deben ejecutarse en menos de 2 segundos en condiciones normales de red.