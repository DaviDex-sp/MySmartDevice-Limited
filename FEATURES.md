# ✨ Features y Capacidades — MySmartDevice

## 📊 Dashboard

### Descripción
Panel central que agrega telemetría en tiempo real de todos los dispositivos del usuario. Proporciona una vista holística del estado del hogar inteligente.

### Características
- ✅ **Métricas en Vivo** — Temperatura, humedad, consumo energético (actualizadas por WebSocket)
- ✅ **Gráficos Interactivos** — Chart.js con históricos de 7/30 días
- ✅ **Widgets Personalizables** — Usuario selecciona qué monitorizar
- ✅ **KPIs Dashboard** — Consumo total, dispositivos activos, alertas pendientes
- ✅ **Responsive Design** — Optimizado para móvil, tablet, desktop

### Arquitectura Técnica
- Service: `DashboardService` (agregación de datos)
- Hub: `DispositivoHub` (broadcasting de actualizaciones)
- Frontend: `Chart.js` + JavaScript vanilla
- Actualización: SignalR en tiempo real

---

## 🔌 Gestión de Dispositivos

### Descripción
CRUD completo para dispositivos IoT. Permite crear, configurar, actualizar y eliminar dispositivos desde la interfaz web.

### Características
- ✅ **CRUD Completo** — Crear, leer, actualizar, eliminar dispositivos
- ✅ **Editor Gráfico** — Interfaz visual para configurar componentes del dispositivo
- ✅ **Control Remoto** — Encender/apagar, ajustar valores mediante MQTT
- ✅ **Historial de Comandos** — Registro de todas las órdenes enviadas
- ✅ **Estados en Tiempo Real** — Refleja el estado actual del dispositivo físico
- ✅ **Categorización** — Luz, termostato, cerradura, cámara, etc.
- ✅ **Búsqueda y Filtrado** — Buscar por nombre, ubicación, tipo

### Flujo de Control
```
Usuario selecciona dispositivo
   ↓
Hace clic en "ENCENDER"
   ↓
JavaScript → POST /api/mqttcomandos
   ↓
MqttPublisherService publica a MQTT
   ↓
Dispositivo físico recibe comando
   ↓
Dispositivo ejecuta acción y confirma
   ↓
MqttDomoticaService recibe confirmación
   ↓
Actualiza BD y notifica UI via SignalR
   ↓
✅ Dashboard se actualiza al instante
```

---

## 🏠 Gestión de Espacios

### Descripción
Organiza los dispositivos de forma jerárquica dentro de la propiedad. Cada espacio puede contener múltiples dispositivos.

### Características
- ✅ **Crear Espacios** — Definir habitaciones o áreas (salón, cocina, dormitorio)
- ✅ **Asignar Dispositivos** — Vincular dispositivos a espacios
- ✅ **Vistas por Espacio** — Dashboard específico por habitación
- ✅ **Control por Área** — Controlar todos los dispositivos de un espacio simultáneamente
- ✅ **Jerarquía** — Soporta espacios anidados (piso → zona → habitación)
- ✅ **Edición y Eliminación** — Gestión completa del árbol de espacios

---

## 👥 Gestión de Usuarios

### Descripción
Administración completa de usuarios del sistema. Soporte para múltiples roles y niveles de acceso.

### Características
- ✅ **Registro de Usuarios** — Crear nuevas cuentas
- ✅ **Roles y Permisos** — Admin, usuario estándar, invitado
- ✅ **Autenticación Local** — Usuario/contraseña
- ✅ **OAuth Externo** — Iniciar sesión con Google
- ✅ **Gestión de Contraseñas** — Cambio, reset, recuperación
- ✅ **Activación de Cuenta** — Verificación por email
- ✅ **Bloqueo de Cuenta** — Desactivar usuarios
- ✅ **Auditoría** — Registro de login/logout

### Roles Soportados
- **Admin** — Control total del sistema
- **Propietario** — Control de su propiedad
- **Usuario** — Control limitado a dispositivos asignados
- **Invitado** — Solo lectura

---

## 🔔 Sistema de Notificaciones

### Descripción
Sistema multi-canal de alertas para eventos importantes en el sistema.

### Canales
1. **Email** — Notificaciones críticas vía Gmail API
2. **UI Banner** — Alertas en tiempo real en el navbar
3. **Centro de Notificaciones** — Historial centralizado

### Tipos de Notificaciones
- ⚠️ **Alertas de Sensores** — Temperatura fuera de rango, movimiento detectado
- 🔋 **Alertas de Batería** — Dispositivo con batería baja
- 🔐 **Alertas de Seguridad** — Puerta no cerrada, ventana abierta
- ℹ️ **Notificaciones Informativas** — Cambios de configuración, actualizaciones

### Características
- ✅ **Notificaciones en Tiempo Real** — Vía SignalR WebSocket
- ✅ **Filtros de Severidad** — Crítica, alta, media, baja
- ✅ **Gestión de Preferencias** — Usuario elige qué notificaciones recibir
- ✅ **Historial** — Guardar historial de 30 días
- ✅ **Marcar como Leída** — Interfaz para gestionar notificaciones

---

## ⚙️ Configuraciones del Sistema

### Descripción
Panel de configuración para ajustes globales de la aplicación.

### Opciones Configurables
- ✅ **Preferencias de Notificaciones** — Canales, frecuencia, severidad
- ✅ **Configuración Regional** — Idioma, zona horaria, formato de fecha
- ✅ **Integración de Servicios** — Conexiones a APIs externas
- ✅ **Temas de UI** — Claro/oscuro, colores personalizados
- ✅ **Seguridad** — Timeout de sesión, política de contraseñas
- ✅ **Backup y Restauración** — Exportar/importar configuración

---

## 👤 Gestión de Perfil

### Descripción
Panel personal para que el usuario gestione su perfil y cuenta.

### Características
- ✅ **Información Personal** — Nombre, email, avatar
- ✅ **Cambio de Contraseña** — Con validación de contraseña actual
- ✅ **Recuperación de Cuenta** — Proceso de reset seguro
- ✅ **Vinculación de OAuth** — Conectar/desconectar Google
- ✅ **Dispositivos Conectados** — Historial de sesiones
- ✅ **Tokens de Acceso** — Generar tokens API (si aplica)
- ✅ **Preferencias** — Configuraciones personales

---

## 🎫 Sistema de Soporte (Tickets)

### Descripción
Sistema de gestión de tickets para soporte técnico y reportes de problemas.

### Características
- ✅ **Crear Tickets** — Reportar problemas con categorización
- ✅ **Estados de Ticket** — Abierto, en progreso, resuelto, cerrado
- ✅ **Asignación** — Admin asigna a agentes de soporte
- ✅ **Prioridad** — Baja, media, alta, crítica
- ✅ **Comentarios** — Comunicación entre usuario y soporte
- ✅ **Adjuntos** — Subir imágenes/archivos de evidencia
- ✅ **SLA Tracking** — Tiempo de respuesta y resolución
- ✅ **Base de Conocimiento** — Artículos de FAQ

---

## 🌍 Gestión de Ubicaciones

### Descripción
Gestión de ubicaciones geográficas para propiedades distribuidas.

### Características
- ✅ **Crear Ubicaciones** — Dirección, coordenadas GPS
- ✅ **Múltiples Propiedades** — Usuario puede tener casas en diferentes ciudades
- ✅ **Mapeo** — Integración con Google Maps (visualización de ubicación)
- ✅ **Control por Zona** — Acciones masivas en ubicación
- ✅ **Información Climática** — OpenWeather API (temperatura exterior)

---

## 🔒 Seguridad Implementada

### Autenticación
- ✅ **Local Auth** — Usuario/contraseña con hashing PBKDF2
- ✅ **OAuth 2.0** — Autenticación con Google
- ✅ **Session Timeout** — 30 minutos de inactividad
- ✅ **CSRF Protection** — Tokens en formularios

### Autorización
- ✅ **Role-Based Access Control (RBAC)** — Roles: Admin, Propietario, Usuario, Invitado
- ✅ **Authorization Attributes** — `[Authorize(Roles="Admin")]` en Razor Pages
- ✅ **Data Ownership** — Usuario solo ve sus propias propiedades/dispositivos

### Comunicación
- ✅ **HTTPS Obligatorio** — Todas las conexiones encriptadas
- ✅ **WSS (WebSocket Secure)** — SignalR sobre encriptación
- ✅ **MQTT Autenticado** — Credenciales en HiveMQ
- ✅ **OAuth Secrets** — Almacenados en Azure Key Vault

### Prevención de Ataques
- ✅ **SQL Injection** — EF Core parameteriza queries
- ✅ **XSS** — Encoding automático en Razor
- ✅ **CSRF** — AntiForgery tokens
- ✅ **Rate Limiting** — Límite de intentos de login

---

## 📱 Responsive Design

### Breakpoints CSS
- 📱 **Mobile** — < 768px
- 📱 **Tablet** — 768px - 1024px
- 🖥️ **Desktop** — > 1024px

### Características
- ✅ Navegación adaptativa (hamburger menu en móvil)
- ✅ Gráficos responsive
- ✅ Touch-friendly buttons
- ✅ Optimización de imágenes

---

## ⚡ Performance

### Optimizaciones Implementadas
- ✅ **Lazy Loading** — Carga de imágenes bajo demanda
- ✅ **Connection Pooling** — Reutilización de conexiones
- ✅ **Caching** — Redis (opcional)
- ✅ **Bundle & Minify** — CSS/JS comprimidos
- ✅ **CDN** — Distribución de assets estáticos
- ✅ **Async/Await** — Operaciones no-bloqueantes

---

## 🚀 Escalabilidad

### Soporta
- ✅ Múltiples usuarios simultáneos
- ✅ Cientos de dispositivos IoT
- ✅ Despliegue en múltiples servidores
- ✅ Base de datos centralizada (MySQL cloud)
- ✅ Broker MQTT cloud (HiveMQ)

---

*Todas las características están conectadas y funcionan en tiempo real mediante arquitectura de eventos.*
