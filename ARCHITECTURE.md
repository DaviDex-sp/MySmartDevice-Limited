# 🏗️ Arquitectura Técnica — MySmartDevice

## 📖 Visión General

MySmartDevice implementa una **Arquitectura N-Tier Limpia (Clean Architecture)** con separación estricta de responsabilidades entre presentación, lógica de negocio y acceso a datos.

---

## 🎯 Principios Arquitectónicos

### 1. Separación de Capas
```
┌──────────────────────────────────────────────────────┐
│   PRESENTACIÓN (Razor Pages)        ← Responde a eventos del usuario
├──────────────────────────────────────────────────────┤
│   LÓGICA DE NEGOCIO (Services)      ← Reglas, validaciones, orquestación
├──────────────────────────────────────────────────────┤
│   ACCESO A DATOS (EF Core)          ← Persistencia, queries
├──────────────────────────────────────────────────────┤
│   BASE DE DATOS (MySQL)             ← Almacenamiento
└──────────────────────────────────────────────────────┘
```

### 2. Inyección de Dependencias Centralizada

Todas las dependencias se resuelven en **Program.cs** según su tiempo de vida:

```csharp
// Scoped — Un contexto por solicitud HTTP
services.AddScoped<IUsuarioService, UsuarioService>();
services.AddScoped<IDispositivoService, DispositivoService>();

// Singleton — Una instancia para toda la aplicación
services.AddSingleton<IMqttPublisherService, MqttPublisherService>();

// Hosted Services — Workers de larga duración
services.AddHostedService<MqttDomoticaService>();
```

### 3. DTOs y Mapeo de Datos

Nunca se exponen entidades de BD directamente. Todo fluye mediante DTOs:

```
Dispositivo (BD) → DispositivoDTO → JSON Response
Request JSON → UsuarioDTO → Usuario (BD)
```

---

## 📂 Estructura de Carpetas

```
ProyectoMSD/
├── Controllers/
│   └── Api/
│       └── MqttComandosController.cs      ← REST API para MQTT
├── Filters/
│   └── NotificacionNavbarFilter.cs        ← Filtro global (IPageFilter)
├── Helpers/
│   └── TelefonoHelper.cs                  ← Utilidades de formato
├── Hubs/
│   └── DispositivoHub.cs                  ← SignalR WebSocket Hub
├── Interfaces/                            ← Contratos (inyección de DI)
│   ├── IConfiguracionService.cs
│   ├── IDashboardService.cs
│   ├── IDispositivoService.cs
│   ├── IEspacioService.cs
│   ├── IMqttPublisherService.cs
│   ├── INotificacionService.cs
│   ├── IPropiedadService.cs
│   ├── ISoporteService.cs
│   └── IUsuarioService.cs
├── Modelos/
│   ├── AppDbContext.cs                    ← DbContext (EF Core)
│   ├── Usuario.cs
│   ├── Dispositivo.cs
│   ├── Espacio.cs
│   └── ...
├── Services/                              ← Lógica de negocio
│   ├── UsuarioService.cs                  ← Auth, hashing PBKDF2
│   ├── DispositivoService.cs              ← CRUD dispositivos
│   ├── MqttPublisherService.cs            ← Singleton pool MQTT
│   ├── MqttDomoticaService.cs             ← IHostedService MQTT
│   ├── NotificacionService.cs             ← Email + UI alerts
│   ├── DashboardService.cs                ← Agregación de datos
│   └── ...
├── Pages/                                 ← Presentación (Razor Pages)
│   ├── Dashboard/
│   ├── Dispositivos/
│   ├── Espacios/
│   ├── Usuarios/
│   └── ...
├── wwwroot/
│   ├── css/
│   │   ├── design-system.css              ← Variables de diseño
│   │   ├── common-styles.css              ← Componentes reutilizables
│   │   └── pages/                         ← Estilos por módulo
│   └── js/
├── Program.cs                             ← Configuración DI y middleware
└── appsettings.json                       ← Configuración (dev/prod)
```

---

## 🔗 Flujos de Datos Principales

### 1️⃣ Flujo: Control de Dispositivo (Usuario → Dispositivo)

```
1. Usuario hace clic en UI (Dashboard/Dispositivos)
   ↓
2. JavaScript llama: POST /api/mqttcomandos
   {"dispositivoId": 5, "comando": "ENCENDER"}
   ↓
3. MqttComandosController recibe request
   - Valida autorización
   ↓
4. Llama a IMqttPublisherService.PublicarComando()
   ↓
5. MqttPublisherService (Singleton)
   - Obtiene conexión del pool
   - Publica a tema: "dispositivos/5/comandos"
   ↓
6. Dispositivo IoT físico
   - Recibe comando en MQTT
   - Ejecuta acción
   - Publica confirmación
   ↓
7. MqttDomoticaService (Hosted Service)
   - Suscriptor permanente
   - Recibe confirmación en "dispositivos/5/estado"
   ↓
8. Actualiza BD via DispositivoService
   ↓
9. Notifica clientes via SignalR (DispositivoHub)
   ↓
10. Dashboard se actualiza en tiempo real
    ✅ Usuario ve cambio instantáneamente
```

### 2️⃣ Flujo: Telemetría en Tiempo Real (Dispositivo → Usuario)

```
Dispositivo IoT publica: "dispositivos/5/sensores"
↓
MqttDomoticaService suscriptor recibe payload
↓
Procesa datos (validación, transformación)
↓
Guarda en BD via DashboardService.ActualizarMetricas()
↓
Notifica via DispositivoHub.SendAsync("ActualizacionTelemetria", datos)
↓
Clientes conectados a SignalR reciben evento
↓
JavaScript actualiza gráficos con Chart.js
↓
✅ Dashboard muestra métricas en vivo
```

### 3️⃣ Flujo: Notificaciones Multi-Canal

```
Evento en sistema (ej: sensor crítico)
↓
DispositivoService.ProcesarAlerta(alerta)
↓
NotificacionService.CrearNotificacion()
├─ Guarda en BD
├─ Envía Email via Gmail API
└─ Publica evento SignalR a navbar
↓
Usuario recibe:
├─ Email instantáneo
└─ Banner en UI
```

---

## 🧩 Patrones de Implementación

### Patrón Service Layer

```csharp
// Interfaz (contrato)
public interface IDispositivoService
{
    Task<DispositivoDTO> ObtenerAsync(int id);
    Task<List<DispositivoDTO>> ListarAsync();
    Task<int> CrearAsync(CrearDispositivoDTO dto);
}

// Implementación
public class DispositivoService : IDispositivoService
{
    private readonly AppDbContext _context;
    private readonly IMqttPublisherService _mqtt;
    
    public async Task<DispositivoDTO> ObtenerAsync(int id)
    {
        var dispositivo = await _context.Dispositivos.FindAsync(id);
        return new DispositivoDTO
        {
            Id = dispositivo.Id,
            Nombre = dispositivo.Nombre,
            Estado = dispositivo.Estado
        };
    }
}
```

### Patrón Hosted Service (Workers)

```csharp
public class MqttDomoticaService : BackgroundService
{
    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        while (!stoppingToken.IsCancellationRequested)
        {
            // Suscriptor MQTT permanente
            await _mqttClient.SubscribeAsync(...);
            // Procesa mensajes indefinidamente
        }
    }
}
```

---

## 🔒 Seguridad en Capas

### Capa de Presentación
- ✅ Validación HTML5 en cliente
- ✅ CSRF tokens en formularios
- ✅ Authorization attributes en Razor Pages

### Capa de Servicios
- ✅ Validación de datos de entrada
- ✅ Verificación de permisos (ClaimsPrincipal)
- ✅ Hashing de contraseñas (PBKDF2)
- ✅ Logs de auditoría

### Capa de Datos
- ✅ Queries parametrizadas (EF Core)
- ✅ Row-level security (BD)
- ✅ Encriptación de campos sensibles

### Comunicación Externa
- ✅ MQTT con autenticación
- ✅ SignalR sobre WSS (websockets seguros)
- ✅ OAuth 2.0 con Google

---

## 🚀 Escalabilidad

### Horizontal (Múltiples servidores)
- ✅ Session storage en Redis (facilita)
- ✅ SignalR backplane (para múltiples instancias)
- ✅ BD centralizada (MySQL cloud)

### Vertical (Optimización en una instancia)
- ✅ Conexión MQTT reutilizable (Singleton)
- ✅ Pool de conexiones a BD
- ✅ Caching de datos frecuentes

---

## 🎯 Decisiones Arquitectónicas

### ✅ ¿Por qué ASP.NET Core 9?
- Performance (framework más rápido .NET)
- C# moderno y expresivo
- Dependency Injection nativo
- Razor Pages para UI empresarial
- Ecosystem completo (Entity Framework, SignalR, etc.)

### ✅ ¿Por qué Razor Pages y no MVC?
- Simplicidad para aplicaciones CRUD
- Acoplamiento de lógica y vista más manejable
- Arquitectura de página cohesiva
- Ideal para dashboard + CRUD

### ✅ ¿Por qué SignalR + MQTT?
- MQTT: Protocolo estándar IoT, bajo consumo de red
- SignalR: Tiempo real web, abstrae WebSocket/Long-Polling
- Complementarios: MQTT para IoT ↔ SignalR para web

### ✅ ¿Por qué EF Core + Pomelo?
- ORM de alto nivel, type-safe
- Pomelo: Soporte nativo MySQL con Connection Pooling
- Migraciones automáticas
- LINQ para queries complejas

---

## ⏱️ Tiempos de Vida (DI)

| Servicio | Tiempo | Razón |
|---|---|---|
| `AppDbContext` | Scoped | Vinculado al contexto HTTP |
| `IUsuarioService` | Scoped | Accede a DbContext |
| `IDispositivoService` | Scoped | Accede a DbContext |
| `IMqttPublisherService` | Singleton | Pool persistente (no cambia por request) |
| `MqttDomoticaService` | AddHostedService | Ejecuta independientemente de requests |
| `NotificacionNavbarFilter` | Scoped | Aplica a cada request |

---

*Documento de arquitectura — Código disponible bajo solicitud*
