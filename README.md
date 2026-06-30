# App-UndSurf
App para gestión de pozos en mina subterránea y superficie. Util para transmitir los datos en forma ágil del Geologo que planifica el plan de perforación a los perforistas y luego compartir estos datos a otros profesionales como el ingeniero geotécnico o el ingeniero en minas para sus visitas a terreno.

---

## 📄 README.md Versión en Español - Castellano (and in the English language below)

```markdown
<p align="center">
  <img src="assets/Logo-UndSurf-1.png" alt="Logo Undsurf" width="200"/>
</p>
# Undsurf - Sistema de Gestión de Datos de Perforación Minera

**Undsurf** es una aplicación integral de gestión operativa diseñada para agilizar el flujo de comunicación entre geólogos, perforistas e ingenieros de minas en minas subterráneas y a cielo abierto.

Reemplaza los formularios en papel por un flujo de trabajo digital en tiempo real. El Geólogo planifica el programa de perforación, el Perforista ejecuta y registra los avances, y otros profesionales (Ingeniero Geotécnico, Ingeniero de Minas) acceden a los datos validados al instante para la toma de decisiones en terreno.

## 🚀 Características Principales

- **Control de Acceso por Roles (RBAC):** Paneles y permisos personalizados para Geólogos (Administradores), Perforistas e Ingenieros Geotécnicos.
- **Gestión Centralizada de Datos:** Carga y administración de datos de perforación mediante archivos CSV, con control estricto sobre los identificadores críticos.
- **Mapeo Visual de Riesgos:** Carga de plantas y secciones (PDF, JPG, PNG) para marcar visualmente riesgos de infraestructura y peligros subterráneos.
- **Autenticación Biométrica:** Inicio de sesión seguro mediante contraseña (primera vez) y reconocimiento de huella dactilar (sesiones posteriores).
- **Buscador Avanzado:** Filtros múltiples por `HOLE_ID`, Ubicación, Mina, Perforista, Geólogo, Ingeniero Geotécnico, Fecha y Estado de Riesgo.
- **Registro de Auditoría Completo:** Cada modificación realizada por cualquier usuario se registra con una marca de tiempo precisa (Fecha y Hora).

## 👥 Roles de Usuario y Permisos

| Función / Acción | **Geólogo (Admin)** | **Perforista** | **Ing. Geotécnico** |
| :--- | :---: | :---: | :---: |
| **Cargar CSV inicial y Planos Visuales** | ✅ Sí | ❌ No | ❌ No |
| **Editar `HOLE_ID` (Ilimitado)** | ✅ Sí | ❌ Limitado (Máx. 3 edits)* | ❌ No |
| **Modificar Avance de Perforación** | ❌ No | ✅ Sí | ❌ No |
| **Registrar Observaciones (Veta/Estéril)** | ❌ No | ✅ Sí | ❌ No |
| **Registrar Problemas de Perforación** | ❌ No | ✅ Sí | ❌ No |
| **Subir CSVs Geotécnicos y Riesgos Coloreados** | ✅ Sí | ❌ No | ✅ Sí (Solo Lectura + Añadir Riesgos) |
| **Subir Datos Finales (Survey, Geotecnia, Litología)** | ✅ Sí | ✅ Limitado | ❌ No |
| **Acceso (Solo Lectura)** | ✅ Sí | ✅ Sí | ✅ Sí |

> **\*Nota para Perforistas:** Solo pueden editar el `HOLE_ID` hasta 3 veces sin aprobación del administrador. Si superan este límite, deben solicitar permiso al Geólogo.

## 🔐 Reglas de Negocio Críticas (Gestión de HOLE_ID)

Para garantizar la integridad de los datos, **Undsurf** aplica reglas de validación estrictas para la columna `HOLE_ID`:

1.  **Unicidad:** El sistema valida que cada nuevo `HOLE_ID` no exista ya en la base de datos. Si existe, se muestra una alerta: *"Este HOLE_ID ya existe en la base de datos"*.
2.  **Formato:** El `HOLE_ID` solo acepta **letras MAYÚSCULAS**.
3.  **Bloqueo:** Una vez que un pozo comienza su ejecución con un `HOLE_ID` específico, este se **bloquea**.
    - El Perforista no puede modificarlo sin solicitar permiso.
    - Solo el **Geólogo (Admin)** puede desbloquearlo y editarlo.
4.  **Estado Final:** Una vez que un pozo se marca como **"Terminado"**, el `HOLE_ID` queda bloqueado de forma permanente. No se permiten más ediciones.

## 📂 Carga de Datos y Gestión de Archivos

La aplicación maneja múltiples fuentes de datos según la fase de perforación:

- **Configuración Inicial (Geólogo):**
  - CSV principal de perforación.
  - Imágenes de Planta/Sección (PDF, JPG, JPEG, PNG, TIFF, RAW) con indicadores de riesgo visuales.
- **Durante la Perforación (Perforista):**
  - Actualizaciones de la columna de Avance.
  - Observaciones (Intersecciones de Veta / Estéril).
  - Reportes de Problemas (ej. pérdida de retorno de agua por fallas).
- **Proyecto Final (Geólogo/Perforista):**
  - CSV de Survey (datos de desviación).
  - CSV Geotécnico (opcional).
  - CSV Litológico (opcional).

## 🔍 Búsqueda y Descubrimiento

Los usuarios pueden localizar rápidamente pozos específicos usando la barra de búsqueda dedicada, filtrando por:

- `HOLE_ID`
- Ubicación (Locación)
- Mina
- Nombre del Perforista
- Nombre del Geólogo
- Nombre del Ingeniero Geotécnico
- Rango de Fechas
- Nivel de Riesgo

## 🛡️ Seguridad y Autenticación

- **Primer Inicio de Sesión:** Credenciales de Usuario/Contraseña.
- **Inicios Posteriores:** Autenticación biométrica (Huella dactilar) para un acceso más rápido y seguro.
- **Recuperación de Contraseña:** Totalmente soportada a través del correo electrónico registrado.

## 🗄️ Modelo de Datos Conceptual (Relaciones)

A continuación se muestra el diagrama de relaciones conceptuales para las tablas principales de la base de datos y las integraciones CSV:

```mermaid
erDiagram
    USERS ||--o{ DRILLING_DATA : gestiona
    USERS {
        int user_id PK
        string name
        string role "Geologo, Perforista, Geotecnico"
        string email
        string biometric_key
    }
    
    DRILLING_DATA ||--o{ HOLE_PROGRESS : registra
    DRILLING_DATA {
        string HOLE_ID PK "Unico, Mayusculas"
        string location
        string mine
        float target_depth
        string status "Activo, Terminado"
    }
    
    DRILLING_DATA ||--o{ OBSERVATIONS : contiene
    OBSERVATIONS {
        int obs_id PK
        string HOLE_ID FK
        string type "Veta, Esteril"
        float depth
        string created_by
        datetime timestamp
    }
    
    DRILLING_DATA ||--o{ PROBLEM_LOGS : contiene
    PROBLEM_LOGS {
        int problem_id PK
        string HOLE_ID FK
        string description "Perdida de agua, Falla, etc."
        string status
        datetime timestamp
    }
    
    DRILLING_DATA ||--o{ GEOTECH_DATA : referencia
    GEOTECH_DATA {
        int geo_id PK
        string HOLE_ID FK
        blob csv_file
        string uploaded_by
        datetime timestamp
    }
    
    DRILLING_DATA ||--o{ SURVEY_DATA : referencia
    SURVEY_DATA {
        int survey_id PK
        string HOLE_ID FK
        blob csv_file
        float deviation
    }
    
    DRILLING_DATA ||--o{ LITHOLOGY_DATA : referencia
    LITHOLOGY_DATA {
        int lith_id PK
        string HOLE_ID FK
        blob csv_file
    }
    
    DRILLING_DATA ||--o{ RISKS : marca
    RISKS {
        int risk_id PK
        string HOLE_ID FK
        string risk_type
        string color_code
        string coordinates
    }
    
    USERS ||--o{ AUDIT_LOGS : genera
    AUDIT_LOGS {
        int log_id PK
        int user_id FK
        string action "Editar HOLE_ID, Actualizar Avance, etc."
        string field_changed
        datetime timestamp
    }
# Undsurf - Mine Drilling Data Management System

**Undsurf** is a comprehensive operational management application designed to streamline the communication flow between geologists, drillers, and mining engineers in underground and surface mines.

It replaces traditional paper-based forms with a digital, real-time workflow. The Geologist plans the drilling schedule, the Driller executes and logs progress, and other professionals (Geotechnical Engineers, Mining Engineers) access validated data instantly for on-site decision-making.

## 🚀 Core Features

- **Role-Based Access Control (RBAC):** Customized dashboards and permissions for Geologists (Admins), Drillers, and Geotechnical Engineers.
- **Centralized Data Management:** Upload and manage drilling data via CSV files, with strict control over critical identifiers.
- **Visual Risk Mapping:** Upload plant layouts and section views (PDF, JPG, PNG) to visually mark infrastructure risks and underground hazards.
- **Biometric Authentication:** Secure login using passwords (first time) and fingerprint recognition (subsequent sessions).
- **Advanced Search Engine:** Multi-filter search by `HOLE_ID`, Location, Mine, Driller, Geologist, Geotechnical Engineer, Date, and Risk Status.
- **Complete Audit Trail:** Every modification made by any user is logged with a precise timestamp (Date & Time).

## 👥 User Roles & Permissions

| Feature / Action | **Geologist (Admin)** | **Driller** | **Geotechnical Engineer** |
| :--- | :---: | :---: | :---: |
| **Upload initial CSV & Visual Plans** | ✅ Yes | ❌ No | ❌ No |
| **Edit `HOLE_ID` (Unlimited)** | ✅ Yes | ❌ Limited (Max 3 edits)* | ❌ No |
| **Modify Drilling Progress (Advance)** | ❌ No | ✅ Yes | ❌ No |
| **Log Observations (Vein/Waste depths)** | ❌ No | ✅ Yes | ❌ No |
| **Log Drilling Problems (Water loss, Faults)** | ❌ No | ✅ Yes | ❌ No |
| **Add Geotechnical CSVs & Color-Coded Risks** | ✅ Yes | ❌ No | ✅ Yes (Read-Only + Add Risks) |
| **Upload Final Data (Survey, Geotech, Lithology)** | ✅ Yes | ✅ Limited | ❌ No |
| **Access (Read-Only View)** | ✅ Yes | ✅ Yes | ✅ Yes |

> **\*Note for Drillers:** You can only edit the `HOLE_ID` up to 3 times without admin approval. If you exceed this limit, you must request permission from the Geologist.

## 🔐 Critical Business Rules (HOLE_ID Management)

To ensure data integrity, **Undsurf** enforces strict validation rules for the `HOLE_ID` column:

1.  **Uniqueness:** The system validates that every new `HOLE_ID` does not already exist in the database. If it does, an alert is displayed: *"This HOLE_ID already exists in the database"*.
2.  **Format:** The `HOLE_ID` only accepts **UPPERCASE letters**.
3.  **Locked State:** Once a drill hole begins execution with a specific `HOLE_ID`, it becomes **locked**. 
    - The Driller cannot modify it without requesting permission.
    - Only the **Geologist (Admin)** can unlock and edit it.
4.  **Final State:** Once a drill hole is marked as **"Finished"**, the `HOLE_ID` is permanently locked. No further edits are allowed.

## 📂 Data Uploads & File Management

The application handles multiple data sources depending on the drilling phase:

- **Initial Setup (Geologist):**
  - Main drilling CSV.
  - Plant/Section images (PDF, JPG, JPEG, PNG, TIFF, RAW) with visual risk indicators.
- **During Drilling (Driller):**
  - Updates to the Advance column.
  - Observations (Vein / Sterile intersections).
  - Problem Reports (e.g., lost water return due to faults).
- **Final Project (Geologist/Driller):**
  - Survey CSV (deviation data).
  - Geotechnical CSV (optional).
  - Lithological CSV (optional).

## 🔍 Search & Discovery

Users can quickly locate specific drill holes using the dedicated search bar, filtering by:

- `HOLE_ID`
- Location
- Mine
- Driller Name
- Geologist Name
- Geotechnical Engineer Name
- Date Range
- Risk Level

## 🛡️ Security & Authentication

- **First Login:** User/Password credentials.
- **Subsequent Logins:** Biometric authentication (Fingerprint) for faster and more secure access.
- **Password Recovery:** Fully supported via registered email address.

## 🗄️ Conceptual Data Model (Relationships)

Below is the conceptual relationship diagram for the core database tables and CSV integrations:

```mermaid
erDiagram
    USERS ||--o{ DRILLING_DATA : manages
    USERS {
        int user_id PK
        string name
        string role "Geologist, Driller, Geotech"
        string email
        string biometric_key
    }
    
    DRILLING_DATA ||--o{ HOLE_PROGRESS : tracks
    DRILLING_DATA {
        string HOLE_ID PK "Unique, Uppercase"
        string location
        string mine
        float target_depth
        string status "Active, Finished"
    }
    
    DRILLING_DATA ||--o{ OBSERVATIONS : contains
    OBSERVATIONS {
        int obs_id PK
        string HOLE_ID FK
        string type "Vein, Sterile"
        float depth
        string created_by
        datetime timestamp
    }
    
    DRILLING_DATA ||--o{ PROBLEM_LOGS : contains
    PROBLEM_LOGS {
        int problem_id PK
        string HOLE_ID FK
        string description "Water loss, Fault, etc."
        string status
        datetime timestamp
    }
    
    DRILLING_DATA ||--o{ GEOTECH_DATA : references
    GEOTECH_DATA {
        int geo_id PK
        string HOLE_ID FK
        blob csv_file
        string uploaded_by
        datetime timestamp
    }
    
    DRILLING_DATA ||--o{ SURVEY_DATA : references
    SURVEY_DATA {
        int survey_id PK
        string HOLE_ID FK
        blob csv_file
        float deviation
    }
    
    DRILLING_DATA ||--o{ LITHOLOGY_DATA : references
    LITHOLOGY_DATA {
        int lith_id PK
        string HOLE_ID FK
        blob csv_file
    }
    
    DRILLING_DATA ||--o{ RISKS : marks
    RISKS {
        int risk_id PK
        string HOLE_ID FK
        string risk_type
        string color_code
        string coordinates
    }
    
    USERS ||--o{ AUDIT_LOGS : generates
    AUDIT_LOGS {
        int log_id PK
        int user_id FK
        string action "Edit HOLE_ID, Update Progress, etc."
        string field_changed
        datetime timestamp
    }
