# 🚀 Refactorización de API Hogar Universitario a Clean Architecture

Este proyecto documenta la migración integral de la API del Hogar Universitario desde un modelo acoplado hacia **Arquitectura Limpia (Clean Architecture)**. El objetivo principal es separar las reglas de negocio de los detalles técnicos como la base de datos (SQL Server), frameworks (Express) y servicios externos (Cloudinary, Firebase, SIAE).

---

## 📑 Índice de Módulos Refactorizados
* [Limpieza](#-módulo-de-limpieza)
* [Autenticación](#-módulo-auth)
* [Estudiantes](#-módulo-de-estudiantes)
* [Dormitorios](#-módulo-de-dormitorios)
* [Cultos](#-módulo-de-cultos)
* [Reportes](#-módulo-de-reportes)
* [Amonestaciones](#-módulo-de-amonestaciones)
* [Asistencia](#-módulo-de-asistencia-cultos)
* [Usuarios](#-módulo-de-usuarios)
* [Configuración](#-módulo-de-configuración)
* [Firmas Digitales](#-módulo-de-firmas-digitales)

---

## 🧹 Módulo de Limpieza

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Limpieza.js` | Entidad con lógica de cálculo de puntaje total aislada. |
| **Aplicación** | `RegistrarLimpieza.js` | Orquestador de fotos (Cloudinary), persistencia y avisos push. |
| **Infraestructura** | `LimpiezaRepositorySql.js` | Abstracción total de consultas MSSQL del controlador HTTP. |

### 🔍 Análisis de Refactorización
* **Problema:** Acoplamiento fuerte entre Express y SQL, dificultando cambios en la lógica de evaluación.
* **Solución Clean:** Se separó la configuración de Express en `express.js` y se delegó la lógica de negocio al Caso de Uso.

---

## 🔐 Módulo Auth

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Usuario.js` | Modelo de datos independiente de la estructura de la tabla. |
| **Aplicación** | `LoginUsuario.js` | Lógica de seguridad (bcrypt) y acceso externo (axios) desacoplada. |
| **Infraestructura** | `UsuarioRepositorySql.js` | Manejo de transacciones complejas para registros multi-tabla. |

### 🔍 Análisis de Refactorización
* **Problema:** El manejo de contraseñas y llamadas a APIs externas saturaban las rutas de Express.
* **Solución Clean:** Se implementó el patrón **Repository** para centralizar el acceso a `dormi.Usuarios` y tablas de roles.

---

## 👨‍🎓 Módulo de Estudiantes

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Estudiante.js` | Representación pura del estudiante como entidad de negocio. |
| **Aplicación** | `ObtenerFotoEstudiante.js` | Tratamiento de datos binarios fuera del controlador. |
| **Infraestructura** | `EstudianteRepositorySql.js` | Desacoplamiento de la base de datos externa (SIAE). |

### 🔍 Análisis de Refactorización
* **Problema:** Mezcla de orígenes de datos locales y externos (IDS-APP) en un mismo archivo de rutas.
* **Solución Clean:** Se encapsuló la obtención de fotos binarias, permitiendo que la API sea agnóstica al origen del archivo.

---

## 🏠 Módulo de Dormitorios

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Dormitorio.js` | Definición de jerarquías (Edificio > Pasillo > Cuarto). |
| **Aplicación** | `ObtenerMapaOcupacion.js` | Orquestador de la visualización en tiempo real del edificio. |
| **Infraestructura** | `DormitorioRepositorySql.js` | Encapsulamiento de JOINs complejos para mapas de ocupación. |

### 🔍 Análisis de Refactorización
* **Problema:** Lógica de relaciones físicas expuesta en las rutas y dificultad para agrupar datos.
* **Solución Clean:** Se crearon modelos de infraestructura física, permitiendo validar capacidades de cuartos desde el dominio.

---

## ⛪ Módulo de Cultos

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `TipoCulto.js` | Estandarización de eventos religiosos en la lógica del sistema. |
| **Infraestructura** | `CultoRepositorySql.js` | Catálogo centralizado y escalable para futuros módulos. |

### 🔍 Análisis de Refactorización
* **Problema:** Consulta directa a catálogos en rutas, limitando la escalabilidad del sistema de asistencia.
* **Solución Clean:** Repositorio minimalista que actúa como puente único hacia la tabla `Cat_TipoCulto`.

---

## 📋 Módulo de Reportes

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Reporte.js` | Reglas de estado inicial según el tipo de usuario reportante. |
| **Aplicación** | `CrearReporte.js` | Orquestación de acumulaciones y disparador de notificaciones. |
| **Infraestructura** | `ReporteRepositorySql.js` | Transaccionalidad SQL y paginación de datos eficiente. |

### 🔍 Análisis de Refactorización
* **Problema:** Falta de atomicidad en la creación de reportes y amonestaciones automáticas.
* **Solución Clean:** Uso de **Transacciones SQL** para asegurar que el reporte y la sanción se guarden en conjunto.

---

## ⚠️ Módulo de Amonestaciones

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Amonestacion.js` | Estandarización de sanciones y fechas de registro del sistema. |
| **Aplicación** | `RegistrarAmonestacion.js` | Centralización de persistencia y comunicación asíncrona. |
| **Infraestructura** | `AmonestacionRepositorySql.js` | Microservicio de catálogos y JOINs de niveles optimizados. |

### 🔍 Análisis de Refactorización
* **Problema:** Duplicidad de consultas de catálogos y falta de abstracción en el envío de alertas.
* **Solución Clean:** Desacoplamiento de notificaciones push, permitiendo registros manuales o automáticos (SISTEMA).

---

## 🚫 Módulo de Asistencia (Cultos)

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Asistencia.js` | Entidad estándar para registros por ID o Nombre de culto. |
| **Aplicación** | `ReportarInasistenciaMasiva.js` | Orquestador de persistencia en lote y avisos personalizados. |
| **Infraestructura** | `AsistenciaRepositorySql.js` | Transaccionalidad atómica y lógica de límites dinámica. |

### 🔍 Análisis de Refactorización
* **Problema:** Procesamiento masivo ineficiente e inconsistencia en reglas de límites de faltas.
* **Solución Clean:** Encapsulamiento de límites (2 vs 3 faltas) en el Repositorio bajo una transacción segura.
---

## 👥 Módulo de Usuarios

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `UsuarioAdmin.js` | Validación centralizada de roles y privilegios de acceso. |
| **Aplicación** | `CambiarRolUsuario.js` | Responsable único de validar estados previos al cambio de rol. |
| **Infraestructura** | `UsuarioAdminRepositorySql.js` | Limpieza atómica de privilegios (Monitor > Estudiante). |

### 🔍 Análisis de Refactorización
* **Problema:** Riesgo de inconsistencia al actualizar roles sin limpiar asignaciones previas.
* **Solución Clean:** Lógica transaccional que asegura la limpieza de datos en `Estudiantes` al degradar un rol de Monitor.

---

## ⚙️ Módulo de Configuración

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Aplicación** | `CerrarSemestreActual.js` | Validación de seguridad para operaciones de alto impacto. |
| **Infraestructura** | `ConfiguracionRepositorySql.js` | Unidad de trabajo para cierre, apertura y vaciado de cuartos. |

### 🔍 Análisis de Refactorización
* **Problema:** Operaciones críticas (vaciado de cuartos) expuestas directamente en rutas HTTP.
* **Solución Clean:** Implementación de integridad referencial forzada mediante transacciones en el Repositorio.

---

## ✍️ Módulo de Firmas Digitales

| Capa | Componente | Mejora Aplicada |
| :--- | :--- | :--- |
| **Dominio** | `Firma.js` | Validación estática de tipos de documentos firmables. |
| **Aplicación** | `RegistrarFirmaDigital.js` | Orquestación de validación y persistencia polimórfica. |
| **Infraestructura** | `FirmaRepositorySql.js` | Resolución dinámica de tablas y optimización de Base64. |

### 🔍 Análisis de Refactorización
* **Problema:** Lógica condicional compleja en rutas y riesgo de truncamiento en datos Base64.
* **Solución Clean:** Repositorio capaz de resolver el destino de la firma sin exponer nombres de tablas SQL.

---
