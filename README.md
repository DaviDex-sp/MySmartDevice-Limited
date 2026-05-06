# 🏠 MySmartDevice — Portfolio Profesional

> Plataforma IoT empresarial para automatización de hogares inteligentes

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![.NET](https://img.shields.io/badge/.NET-9.0-512BD4?logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![Azure](https://img.shields.io/badge/Azure-App%20Service-0078D4?logo=microsoft-azure&logoColor=white)](https://azure.microsoft.com/)
[![SignalR](https://img.shields.io/badge/SignalR-WebSockets-informational)](https://learn.microsoft.com/aspnet/core/signalr/)
[![MQTT](https://img.shields.io/badge/MQTT-HiveMQ-FFA500?logo=mqtt&logoColor=white)](https://www.hivemq.com/)

---

## 📖 Acerca de Este Proyecto

**MySmartDevice** es una solución integral de domótica desarrollada con arquitectura moderna y principios de ingeniería de software empresarial. Esta es una **demostración de portafolio profesional** que muestra capacidades, arquitectura y decisiones técnicas.

### ✨ Resumen Rápido
- 🏗️ **Arquitectura N-Tier Limpia** — Separación estricta de capas
- ⚡ **Comunicación IoT en Tiempo Real** — MQTT + SignalR WebSockets
- 🔐 **Autenticación Multi-Método** — Local + Google OAuth 2.0
- 📊 **Dashboard Interactivo** — Métricas en vivo con Chart.js
- 🚀 **CI/CD Automatizado** — GitHub Actions → Azure App Service
- 📱 **Responsive Design** — Optimizado para móvil, tablet y desktop

---

## 🎯 Stack Tecnológico

| Capa | Tecnología | Versión |
|---|---|---|
| **Backend** | ASP.NET Core | 9.0 |
| **UI Framework** | Razor Pages | ASP.NET Core 9 |
| **ORM** | Entity Framework Core + Pomelo | 9.0 |
| **Base de Datos** | MySQL 8.0 (Cloud) | 8.0 |
| **Tiempo Real** | SignalR (WebSockets) | ASP.NET Core 9 |
| **IoT Protocol** | MQTT vía MQTTnet | 4.3.3 |
| **Cloud** | Azure App Service | — |
| **CI/CD** | GitHub Actions | — |
| **Frontend** | HTML5, CSS3, JavaScript | ES6+ |

---

## 🏛️ Arquitectura del Sistema

```
┌─────────────────────────────────────────────────────────────┐
│   Razor Pages (Presentación)         ← Solo orquestación    │
├────────────────────┬────────────────────────────────────────┤
│                    │                                         │
│   Services Layer   │   REST API + SignalR Hub              │
│  (Lógica Negocio)  │   (Comunicación)                       │
│                    │                                         │
├────────────────────┴────────────────────────────────────────┤
│   AppDbContext (Entity Framework Core + Pomelo)             │
├────────────────────────────────────────────────────────────┤
│   MySQL Database (Cloud)                                    │
├────────────────────────────────────────────────────────────┤
│   MQTT (HiveMQ) ← IoT Devices                              │
└────────────────────────────────────────────────────────────┘
```

### Patrones Implementados
- ✅ **Dependency Injection** — Contenedor DI nativo de .NET
- ✅ **Service Layer Pattern** — Centralización de lógica
- ✅ **Repository Pattern (Implícito)** — EF Core como repository
- ✅ **DTO Pattern** — Transferencia segura de datos
- ✅ **Hosted Services** — Workers de larga duración (MQTT)
- ✅ **Hub Pattern** — SignalR para eventos en tiempo real

---

## 📊 Capacidades Principales

### 🎛️ Dashboard
- Métricas en tiempo real de todos los dispositivos
- Gráficos interactivos con Chart.js
- Visualización de históricos (7/30 días)
- Widgets personalizables
- Responsive para móvil/tablet/desktop

### 🔌 Control de Dispositivos
- CRUD completo de dispositivos IoT
- Editor gráfico para configuración
- Control remoto mediante MQTT
- Historial de comandos
- Estados sincronizados en tiempo real
- Categorización (luz, termostato, cerradura, cámara, etc.)

### 🏠 Gestión de Espacios
- Organización jerárquica de dispositivos
- Definición de habitaciones/áreas
- Control por espacio
- Vistas especializadas por ubicación

### 👥 Gestión de Usuarios
- Autenticación local (PBKDF2) + OAuth Google
- Sistema de roles (Admin, Propietario, Usuario, Invitado)
- Multi-tenancy
- Gestión de permisos

### 🔔 Sistema de Notificaciones
- Alertas por email (Gmail API)
- Notificaciones en tiempo real (UI)
- Centro de notificaciones con historial
- Filtros por severidad
- Preferencias personalizables

### ⚙️ Configuraciones
- Personalización de preferencias
- Tema claro/oscuro
- Zona horaria e idioma
- Integración de servicios
- Backup y restauración

### 📞 Sistema de Soporte
- Creación de tickets
- Asignación a agentes
- Priorización (Baja, Media, Alta, Crítica)
- Comentarios y seguimiento
- Base de conocimiento (FAQ)

---

## 🔐 Seguridad Implementada

### Autenticación
- ✅ Hashing PBKDF2 con salt seguro
- ✅ Session timeout de 30 minutos
- ✅ Google OAuth 2.0 integrado
- ✅ CSRF tokens en formularios

### Autorización
- ✅ Role-Based Access Control (RBAC)
- ✅ Data ownership (usuario solo ve sus datos)
- ✅ Authorization attributes en Razor Pages

### Comunicación
- ✅ HTTPS obligatorio
- ✅ WSS (WebSocket Secure) para SignalR
- ✅ MQTT autenticado en HiveMQ
- ✅ Secretos en Azure Key Vault (nunca hardcodeados)

### Prevención de Ataques
- ✅ SQL Injection — Queries parametrizadas (EF Core)
- ✅ XSS — Encoding automático en Razor
- ✅ CSRF — AntiForgery tokens
- ✅ Rate Limiting — Límite de intentos de login

---

## 📡 Flujos de Comunicación

### Control de Dispositivo (Usuario → Dispositivo)
```
Usuario hace clic en UI
    ↓
POST /api/mqttcomandos
    ↓
MqttPublisherService (Singleton)
    ↓
MQTT (HiveMQ)
    ↓
Dispositivo IoT ejecuta acción
    ↓
Confirmación via MQTT
    ↓
MqttDomoticaService (Hosted Service) procesa
    ↓
Actualiza BD
    ↓
Notifica via SignalR
    ↓
✅ Dashboard actualizado en tiempo real
```

### Telemetría en Tiempo Real (Dispositivo → Usuario)
```
Dispositivo publica en MQTT
    ↓
MqttDomoticaService suscriptor
    ↓
Procesa y guarda en BD
    ↓
SignalR Hub notifica a clientes
    ↓
Chart.js actualiza gráficos
    ↓
✅ Métricas en vivo en Dashboard
```

---

## 🚀 Despliegue y DevOps

### CI/CD Pipeline
```yaml
Push a master
    ↓
[Build Job]
  - Restaura NuGet
  - Compila .NET 9
  - Publica artefacto
    ↓
[Deploy Job]
  - Autentica en Azure (OIDC)
  - Despliega App Service
  - Inyecta variables de entorno
    ↓
✅ Aplicación en vivo
```

**Automatizado en cada commit a `master`**

---

## 📚 Documentación Técnica Completa

Esta es una demostración pública del proyecto. Para acceso a la documentación técnica completa:

- 📖 **[PORTFOLIO.md](./PORTFOLIO.md)** — Resumen ejecutivo para profesionales
- 🏗️ **[ARCHITECTURE.md](./ARCHITECTURE.md)** — Arquitectura detallada, diagramas y flujos
- ✨ **[FEATURES.md](./FEATURES.md)** — Descripción completa de cada módulo

---

## 🔒 Nota sobre el Código Fuente

Este repositorio contiene **documentación y demostración de capacidades**. El código fuente completo se mantiene de forma privada.

**Para solicitar acceso al código privado o discutir el proyecto:**

📧 **Email:** daviddiazgomez@gmail.com  
🔗 **LinkedIn:** [linkedin.com/in/daviddiazgomez/](https://www.linkedin.com/in/daviddiazgomez/)  
🌐 **Portfolio:** [daviddiazgomez.dev](https://daviddiazgomez.dev)  

---

## 🎓 Aprendizajes y Mejores Prácticas

✅ Arquitectura limpia y escalable  
✅ Patrones de diseño empresariales  
✅ Comunicación asincrónica IoT  
✅ Autenticación y autorización  
✅ DevOps y CI/CD  
✅ Cloud computing (Azure)  
✅ Bases de datos relacionales  
✅ Desarrollo frontend moderno  
✅ Seguridad en capas  
✅ Performance y escalabilidad  

---

## 📄 Licencia

Este proyecto está licenciado bajo la licencia MIT — ver el archivo [LICENSE](./LICENSE) para más detalles.

---

## 👤 Autor

**David Díaz**  
🔗 [@DaviDex-sp](https://github.com/DaviDex-sp)  
📧 daviddiazgomez@gmail.com  

---

*Plataforma IoT desarrollada con ASP.NET Core 9, Entity Framework, SignalR y MQTT*
