# 🏠 MySmartDevice — Portfolio Profesional

> Plataforma IoT empresarial para automatización de hogares inteligentes

---

## 📖 Resumen Ejecutivo

**MySmartDevice** es una solución integral de domótica desarrollada con arquitectura moderna y principios de ingeniería de software de alto nivel. La plataforma permite a usuarios finales monitorizar y controlar dispositivos IoT desde una interfaz web centralizada, con comunicación en tiempo real y escalabilidad empresarial.

### 📊 Capacidades Principales

✅ **Dashboard en Tiempo Real** — Métricas en vivo con visualizaciones interactivas (Chart.js)  
✅ **Control de Dispositivos IoT** — CRUD, editor gráfico, despacho de comandos MQTT  
✅ **Autenticación Multi-Método** — Local (PBKDF2) + Google OAuth 2.0  
✅ **Comunicación Bidireccional** — SignalR (WebSockets) + MQTT (HiveMQ)  
✅ **Sistema de Notificaciones** — Email (Gmail API) + UI en tiempo real  
✅ **Gestión de Espacios** — Organización jerárquica de dispositivos por ubicación  
✅ **Multi-Tenancy** — Soporte para múltiples usuarios y propiedades  
✅ **CI/CD Automatizado** — GitHub Actions → Azure App Service  

---

## 🛠️ Stack Tecnológico

| Capa | Tecnología | Versión |
|---|---|---|
| **Backend** | ASP.NET Core | 9.0 |
| **Framework UI** | Razor Pages | ASP.NET Core 9 |
| **ORM** | Entity Framework Core + Pomelo | 9.0 |
| **Base de Datos** | MySQL 8.0 (Cloud) | 8.0 |
| **Tiempo Real** | SignalR (WebSockets) | ASP.NET Core 9 |
| **IoT** | MQTT vía MQTTnet | 4.3.3 |
| **Autenticación** | Cookies + OAuth 2.0 | ASP.NET Core 9 |
| **Email** | Gmail API | v1 |
| **Nube** | Azure App Service | — |
| **CI/CD** | GitHub Actions | — |
| **Frontend** | HTML5, CSS3, JavaScript | ES6+ |

---

## 🏗️ Arquitectura N-Tier Limpia

El proyecto implementa una **arquitectura en capas estricta** que prohíbe la inyección directa de datos en las vistas:

```
┌──────────────────────────────────────────────────────────┐
│   Razor Pages (Presentación)        ← Solo orquestación
├──────────────────────────────────────────────────────────┤
│   Services (Lógica de Negocio)      ← Reglas de negocio
├──────────────────────────────────────────────────────────┤
│   Data Access (EF Core + Pomelo)    ← Persistencia
├──────────────────────────────────────────────────────────┤
│   MySQL Database                    ← Datos
└──────────────────────────────────────────────────────────┘
```

### Patrones Implementados

- ✅ **Dependency Injection** — Contenedor DI nativo de .NET
- ✅ **Repository Pattern** — Abstracción de acceso a datos
- ✅ **Service Layer Pattern** — Centralización de lógica
- ✅ **DTO Pattern** — Transferencia segura de datos
- ✅ **Hosted Services** — Workers de larga duración (MQTT)
- ✅ **Middleware Pipeline** — Procesamiento de solicitudes
- ✅ **Global Filters** — Aplicación transversal de lógica

---

## 🔐 Seguridad Implementada

### Autenticación
- **Hashing PBKDF2** — Contraseñas con salt seguro
- **Session Timeout** — Expiración de 30 minutos
- **Google OAuth 2.0** — Integración con proveedores externos
- **Cookie Segura** — HttpOnly + Secure flags

### Gestión de Secretos
- **Desarrollo** — `dotnet user-secrets`
- **Producción** — Azure Key Vault + Variables de entorno
- **Zero Hardcoding** — Nunca en código fuente

### Comunicación
- **MQTT con Autenticación** — Credenciales en HiveMQ Cloud
- **SignalR sobre WSS** — Conexiones encriptadas
- **API REST Validada** — Autorizacion por roles

---

## 💡 Flujos de Comunicación

### 1️⃣ Control de Dispositivos
```
Usuario (UI) → REST API → MqttPublisherService → MQTT (HiveMQ) → Dispositivo Físico
```

### 2️⃣ Telemetría en Tiempo Real
```
Dispositivo → MQTT (HiveMQ) → MqttDomoticaService → SignalR Hub → Dashboard (UI)
```

### 3️⃣ Notificaciones
```
Evento en Sistema → NotificacionService → Gmail API → Email Usuario
                 → SignalR Hub → Navbar UI
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

**Automático en cada commit a `master`**

---

## 📦 Módulos del Sistema

| Módulo | Responsabilidad | Características |
|---|---|---|
| **Dashboard** | Visualización central | Métricas en vivo, gráficos, KPIs |
| **Dispositivos** | Gestión IoT | CRUD, editor visual, comandos MQTT |
| **Espacios** | Organización física | Habitaciones, ubicaciones |
| **Usuarios** | Gestión de acceso | Roles, permisos, multi-tenancy |
| **Notificaciones** | Alertas | Email + UI en tiempo real |
| **Configuraciones** | Settings globales | Parámetros de sistema |
| **Soporte** | Help Desk | Tickets, resolución de issues |

---

## 💎 Características Técnicas Destacadas

### ✨ Inyección de Dependencias Avanzada
- **Scoped Services** — Vinculados al contexto HTTP
- **Singleton Services** — Pool de conexión MQTT persistente
- **Hosted Services** — Worker MQTT de larga duración
- **Service Filters** — Aplicación global de lógica transversal

### ✨ Comunicación IoT Escalable
- **MQTTnet v4.3.3** — Cliente MQTT robusto
- **HiveMQ Cloud** — Broker MQTT cloud-native
- **Pool Persistente** — Conexión reutilizable
- **Reintento Automático** — Resilencia ante fallos de red

### ✨ Tiempo Real con SignalR
- **WebSockets** — Comunicación bidireccional
- **Hub Pattern** — Orquestación de eventos
- **Broadcast** — Múltiples clientes conectados
- **Telemetría en Vivo** — Sin latencia perceptible

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

---

## 📞 Contacto

👤 **David Díaz** (@DaviDex-sp)  
📧 [daviddiazgomez@gmail.com](mailto:daviddiazgomez@gmail.com)  
🔗 [LinkedIn](https://www.linkedin.com/in/daviddiazgomez/)  
🌐 [Portfolio](https://daviddiazgomez.dev)  

---

### 📖 Documentación Técnica Completa

- [Arquitectura Detallada](./ARCHITECTURE.md) — Diagramas, flujos, decisiones técnicas
- [Features y Módulos](./FEATURES.md) — Descripción completa de capacidades

---

*Para acceso al código fuente privado, por favor contacta directamente.*
