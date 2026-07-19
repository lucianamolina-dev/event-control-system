# Qntrol App

[![Platform](https://img.shields.io/badge/Platform-Android_Native-green?style=for-the-badge&logo=android)](https://developer.android.com/)
[![Language](https://img.shields.io/badge/Language-Java-orange?style=for-the-badge&logo=openjdk)](https://openjdk.org/)
[![Database](https://img.shields.io/badge/Database-Firebase_Firestore-FFCA28?style=for-the-badge&logo=firebase)](https://firebase.google.com/)
[![Auth](https://img.shields.io/badge/Auth-Firebase_Authentication-DD2C00?style=for-the-badge&logo=firebase)](https://firebase.google.com/)
[![Camera](https://img.shields.io/badge/Camera-Jetpack_CameraX-4285F4?style=for-the-badge&logo=android)](https://developer.android.com/jetpack/androidx/releases/camera)
[![Scanner](https://img.shields.io/badge/Scanner-Google_ML_Kit-34A853?style=for-the-badge&logo=google)](https://developers.google.com/ml-kit)
[![Website](https://img.shields.io/badge/Website-qntrol.es-blue?style=for-the-badge&logo=googlechrome)](https://qntrol.es)

**Qntrol** es una aplicación móvil nativa para Android diseñada específicamente para optimizar y agilizar el control de accesos, validación de entradas y monitorización de aforo en tiempo real para eventos de cualquier escala. 

Construida con un enfoque robusto y una experiencia de usuario moderna y fluida, la aplicación permite a los organizadores y personal de staff escanear códigos QR de alta velocidad, gestionar listas de invitados de forma interactiva y mantener un registro de auditoría local detallado y seguro.

---

## Plataforma Web (qntrol.es)

Para poder utilizar la aplicación móvil de manera efectiva, es fundamental conocer y utilizar la plataforma web oficial: **[qntrol.es](https://qntrol.es)**. 

La web de **Qntrol** es el panel de administración centralizado donde se gestionan los eventos, se configuran las entradas, se importan o añaden las listas de invitados y se visualizan las estadísticas de aforo. La app móvil Android sirve como herramienta operativa en el terreno de juego para el escaneo y validación en tiempo real de los asistentes, sincronizando la información instantáneamente con la base de datos de la plataforma web.

---

## Características Principales

*   **Autenticación Segura y Persistente:** Acceso integrado mediante credenciales de correo/contraseña y soporte para **Google Sign-In**. Implementa persistencia de sesión automática para que el staff no tenga que loguearse repetidamente durante el evento.
*   **Sincronización en Tiempo Real:** Monitorización continua y bidireccional del estado del aforo (asistentes ingresados vs. capacidad total) utilizando base de datos reactiva para reflejar de inmediato los cambios en todos los dispositivos de control activos.
*   **Escáner QR de Alta Velocidad (CameraX + ML Kit):** Motor de escaneo optimizado que procesa imágenes directamente en el dispositivo (Edge AI). Captura códigos de barras y códigos QR en condiciones de iluminación variable de forma instantánea.
*   **Modos de Validación Flexibles:**
    *   *Modo Automático:* Procesa el acceso y cierra la tarjeta informativa con los detalles del asiento y sector del asistente de forma automática tras 4 segundos, permitiendo flujos de entrada masivos rápidos.
    *   *Modo Manual:* Mantiene en pantalla la información del asiento y sección hasta que el operador decida cerrarla, ideal para guiar a los invitados de forma personalizada.
*   **Búsqueda Inteligente con Normalización de Texto:** Buscador en tiempo real que localiza titulares e invitados por nombre, apellido o correo electrónico. Normaliza el texto automáticamente (ignora mayúsculas, minúsculas y tildes) para garantizar resultados precisos bajo cualquier circunstancia.
*   **Registro de Auditoría Local (Logs):** Guarda de forma local y segura un historial detallado de accesos por usuario (dispositivo utilizado, método de entrada, fecha, hora e invitado aceptado) con políticas de autorentención de datos de hasta 30 días o un límite de 200 entradas.
*   **Personalización y Accesibilidad:**
    *   Soporte nativo multilenguaje (Español e Inglés).
    *   Soporte completo para cambios de interfaz visual (**Tema Claro y Tema Oscuro**).
    *   Diseño limpio basado en directrices de **Material Design 3**.

---

## Galería de Pantallas (Screenshots)

A continuación se muestran capturas de pantalla reales del funcionamiento e interfaz de usuario de la aplicación móvil:

### 1. Control de Acceso y Configuración
> Pantalla de inicio de sesión segura (con soporte para Google Auth) y panel de ajustes para alternar el tema visual y el idioma de la aplicación.

| Inicio de Sesión | Ajustes e Idioma |
| :---: | :---: |
| <img src="docs/screenshots/login.png" width="220" alt="Login Screen"> | <img src="docs/screenshots/settings.png" width="220" alt="Settings Screen"> |

---

### 2. Gestión de Eventos y Búsqueda de Invitados
> Listado global de los eventos activos creados desde la web y pantalla de búsqueda interactiva de invitados con normalización de texto.

| Panel de Eventos | Búsqueda de Invitados |
| :---: | :---: |
| <img src="docs/screenshots/events.png" width="220" alt="Events Dashboard"> | <img src="docs/screenshots/search_people.png" width="220" alt="Guest Search Screen"> |

---

### 3. Escaneo de Códigos QR
> Interfaz optimizada utilizando CameraX y Google ML Kit para capturar y decodificar códigos QR al instante.

| Escáner de Entrada QR |
| :---: |
| <img src="docs/screenshots/scanner.png" width="220" alt="QR Scanner"> |

---

## Arquitectura Conceptual del Sistema

La aplicación está diseñada bajo el patrón de arquitectura por componentes de Android, garantizando la separación de responsabilidades y la inyección eficiente de servicios en la nube.

```mermaid
graph TD
    subgraph Cliente Android (Java Nativo)
        UI[Material Design 3 UI] --> Controller[Activities / Event Handlers]
        Controller --> Camera[Jetpack CameraX]
        Camera --> MLKit[Google ML Kit Barcode Analyzer]
        Controller --> Prefs[SharedPreferences / Local Logs]
    end

    subgraph Firebase Cloud Services
        AuthService[Firebase Authentication] <--> Controller
        Firestore[Cloud Firestore DB] <--> Controller
    end

    MLKit -->|Token QR Detectado| Controller
    Controller -->|Validación en Tiempo Real| Firestore
```

### Flujo de Datos para la Validación del Acceso:
1. **Detección:** El analizador de imagen de **CameraX** entrega frames continuos a **Google ML Kit** a nivel de hardware.
2. **Extracción:** ML Kit identifica un código QR y extrae su valor de texto de forma local.
3. **Consulta Firestore:** La aplicación realiza una búsqueda de correspondencia reactiva en la subcolección de invitados asociada al usuario autenticado.
4. **Validación:**
   * Si el código no existe en los registros: Se genera una respuesta visual de error.
   * Si ya fue escaneado anteriormente: Se indica que el invitado ya ingresó con fecha/hora previa.
   * Si es válido y está pendiente: Se actualizan los campos correspondientes de forma transaccional, incrementando el aforo de manera inmediata en todos los terminales vinculados.
5. **Auditoría:** Se genera un registro local en `SharedPreferences` que concatena el dispositivo móvil físico que autorizó la entrada.

---

## Diseño del Modelo de Datos (Firestore)

El almacenamiento se organiza a través de colecciones y subcolecciones optimizadas para lecturas rápidas y actualizaciones atómicas en tiempo real:

```yaml
usuarios/ (Colección raíz)
  └── {email_usuario}/ (Documento por administrador de eventos)
        └── eventos/ (Subcolección de eventos)
              └── {id_evento}/ (Documento del evento)
                    ├── nombreEvento: "Graduación Promoción 2026"
                    ├── fecha: "2026-07-24"
                    ├── hora: "19:00"
                    └── invitados/ (Subcolección de asistentes)
                          └── {id_invitado_o_ticket}/ (Documento por titular/ticket)
                                ├── nombre: "María Gómez"
                                ├── email: "maria@example.com"
                                ├── asientoDetalle:
                                │     ├── sectionName: "Platea A"
                                │     ├── row: "12"
                                │     └── number: "4"
                                └── personas: [ (Lista de acompañantes / sub-tickets)
                                      {
                                        "nombre": "María Gómez",
                                        "escaneado": true,
                                        "fechaEscaneo": Timestamp(...),
                                        "qrCode": "QR_MARIA_GOMEZ"
                                      },
                                      {
                                        "nombre": "Acompañante 1",
                                        "escaneado": false,
                                        "fechaEscaneo": null,
                                        "qrCode": "QR_ACOMPANANTE_1"
                                      }
                                    ]
```

---

## Pila Tecnológica (Tech Stack)

*   **Lenguaje:** Java (SDK 8 compatible)
*   **Diseño UI:** Android Material Design 3, XML Layouts adaptativos.
*   **Android Jetpack:** `CameraX` (Lifecycle, View, Camera2) y `ConstraintLayout` para interfaces fluidas de alto rendimiento.
*   **Cloud Backend:** Firebase Auth (Email/Pass y Google Authentication provider), Cloud Firestore.
*   **Procesamiento de Imagen:** Google ML Kit Barcode Scanning API (versión empaquetada e independiente).
*   **Almacenamiento Local:** Android Shared Preferences con compresión lógica para el sistema de Logs de Auditoría.

---

## Confidencialidad y Seguridad del Código

> [vanilla]
> El código fuente completo y los archivos de configuración sensibles de Firebase (como llaves API y archivos de servicio) no se distribuyen de forma pública en este repositorio por motivos de seguridad empresarial y protección de la propiedad intelectual. Esta documentación sirve como presentación técnica del diseño arquitectónico, flujo de experiencia de usuario y capacidades tecnológicas del desarrollador para procesos de selección y portafolio profesional.
