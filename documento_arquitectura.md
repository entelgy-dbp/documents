Arquitectura de Software del Framework de Integración

Historial de las Revisiones

Las siguientes tablas describen la historia de modificación del documento para propósitos de seguimiento.

Únicamente los cambios que produzcan una nueva versión deberán ser mostrados en estas tablas.

# Resumen Ejecutivo

El Framework de Integración Compartamos establece un modelo estandarizado para diseñar, construir, operar y gobernar microservicios e integraciones en la organización. Su arquitectura combina lineamientos corporativos, componentes reutilizables, arquetipos productivos y una plataforma DevSecOps para asegurar consistencia, calidad y eficiencia en el ciclo de vida del software.

La arquitectura se organiza en tres grandes bloques:

- 1. Lineamientos, Estándares y Guías

Define el conjunto de reglas y principios que todos los servicios deben cumplir para garantizar homogeneidad y resiliencia:

Dominio: uso obligatorio de DDD, BIAN, Arquitectura Hexagonal y Clean Code.

Seguridad: autenticación y autorización vía JWT/OAuth2, secretos en GSM/KMS, mTLS mediante Service Mesh.

Observabilidad: trazabilidad con OpenTelemetry, logs JSON con MDC, métricas Micrometer/Prometheus.

Calidad & Testing: unit tests, integration tests con Testcontainers, contract testing, y gates de CI/CD (SAST/SCA/SBOM).

Resiliencia: Circuit Breaker, Retries, Timeouts, DLQ, patrones SAGA/Outbox, Idempotencia.

Contratos API: uso de OpenAPI/AsyncAPI, versionado, y linters corporativos.

Estos lineamientos son obligatorios, auditables y heredados automáticamente por cada arquetipo del framework.

- 2. Arquitectura del Framework de Integración

Es el núcleo funcional del framework y provee capas reutilizables, arquetipos y módulos preconfigurados que reducen el tiempo de desarrollo.

Incluye:

Initializer / Generador

CLI o portal (Backstage) que crea proyectos con estructura estándar y configuraciones listas.

Integration BOM

Controla versiones corporativas de Quarkus, Camel, Java 21, librerías commons y starters.

Arquetipos

Plantillas listas para usar:

REST API

Camel EIP/Integraciones

DB (SQL/NoSQL)

Kafka / PubSub

RabbitMQ
Todos integran seguridad, trazabilidad y resiliencia desde el inicio.

Librerías Commons

DTO core, error-envelope, headers estándar, mappers y utilitarios.

Starters

Configuración plug-and-play para logging, seguridad, trazabilidad, resiliencia, calidad, etc.

Gobierno & Conocimiento

Portal con quickstarts, ADRs, versionado, documentación, KPIs de adopción.

Este bloque define el “cómo se construye” y “qué se incluye” dentro de cada microservicio o integración.

- 3. Plataforma DevSecOps

Proporciona el entorno técnico para construir, validar, firmar y desplegar los servicios:

BOM corporativo

Dockerfile multi-stage

Helm charts base

Golden Images (UBI/JDK21)

Pipelines CI/CD (Azure DevOps) con SBOM, firma, escaneo, SAST/SCA, despliegues automatizados.

Es el motor operacional que garantiza que lo generado por el framework sea seguro, estable y compliant.

Conclusión

La arquitectura del Framework de Integración unifica:

Cómo se diseña (dominio + estándares)

Cómo se construye (arquetipos + commons + starters)

Cómo se despliega y opera (DevSecOps + observabilidad + resiliencia)

Cómo se gobierna (ADRs, documentación, versionado, KPIs)

El resultado es un ecosistema que acelera el desarrollo, reduce riesgos, mejora la calidad y habilita una integración consistente en toda la organización.

# 1. Vista General de la Arquitectura del Framework

La arquitectura del Framework de Integración se organiza en tres grandes bloques que trabajan de forma coordinada:

Lineamientos, estándares y guías (columna izquierda del diagrama).

Framework de Integración (arquetipos, librerías y starters).

Plataforma DevSecOps corporativa (BOM, imágenes, CI/CD).

Esta organización busca garantizar que todos los servicios de integración:

Compartan una base arquitectónica común (dominio, seguridad, observabilidad, resiliencia, calidad).

Se creen de manera estandarizada y repetible a partir de arquetipos.

Se desplieguen y operen sobre una plataforma homogénea y gobernada.

## 1.1. Lineamientos, estándares y guías

Este bloque resume los principios y reglas que todos los componentes del Framework de Integración deben cumplir. Define cómo deben construirse los servicios, qué características mínimas deben incorporar (seguridad, resiliencia, observabilidad, calidad, entregabilidad) y cómo se verifican dichas condiciones a través de código, arquetipos, configuraciones y pipelines.

Los detalles completos se encuentran en el documento corporativo “Lineamientos y Estándares del Framework de Integración”, que actúa como la referencia oficial.
A continuación se presenta un resumen alineado a los once principios rectores del framework:

Reutilización orientada a capacidades: uso obligatorio de starters y commons del framework, dependencia exclusiva de BOMs corporativos y arquetipos según el tipo de servicio.

Seguridad por diseño (Zero Trust): OAuth2/JWT, gestión centralizada de secretos (GSM/KMS), mTLS, golden images, SAST/SCA y SBOM en pipeline.

Arquitectura Hexagonal + DDD + BIAN: separación estricta en capas, pureza del dominio, puertos y adaptadores, bounded contexts basados en BIAN.

Clean Code y estándares corporativos: SOLID, linting unificado, documentación obligatoria con diagramas.

Resiliencia e idempotencia: timeouts, retries con jitter, circuit breakers, bulkheads y estrategias SAGA/Outbox.

Trazabilidad y auditoría end-to-end: trazas W3C, logs JSON con contexto completo y eventos de auditoría.

Observabilidad por defecto: health/readiness probes, métricas RED/USE, logs estructurados y trazas OpenTelemetry.

Entregabilidad reproducible: Docker multistage, Helm base, configuración externa, promoción por versión.

DevSecOps nativo: pipeline estándar con calidad, seguridad, firma y despliegue continuo.

Diseño de APIs estables: versionado explícito, OpenAPI/AsyncAPI, errores estándar, backward compatibility.

Datos, consistencia y gobernanza: idempotencia, outbox, retención segura, registro en catálogo corporativo y política de actualización del framework.

## 1.2. Framework de Integración (arquetipos, librerías y starters)

El segundo bloque corresponde al núcleo del framework, donde los lineamientos anteriores se concretan en artefactos reutilizables que los equipos consumen directamente.

Sus principales componentes son:

Initializer / Generador de proyectos

Punto de entrada para los equipos (CLI o portal tipo Backstage), que permite crear nuevos servicios de integración a partir de plantillas predefinidas.
Desde este generador se seleccionan:

Tipo de arquetipo (REST, Camel, DB, EDA Kafka, EDA RabbitMQ, etc.).

Capacidades de resiliencia, observabilidad y seguridad que se habilitarán desde el inicio.

Integraciones opcionales (bases de datos, mensajería, etc.).

Integration BOM (BOM de Integración)

Módulo que define las versiones alineadas de:

Quarkus,

Apache Camel,

Librerías core del framework,

Starters y módulos commons.

Evita “versiones a dedo” y permite upgrades coordinados y controlados por el equipo de plataforma.

Arquetipos de servicio

REST (platform-http) para APIs síncronas.

Camel (REST DSL / EIPs) para integraciones y orquestaciones complejas.

DB (Panache/Flyway) para servicios con persistencia SQL/NoSQL.

EDA – Kafka / PubSub para publicación/consumo de eventos.

EDA – RabbitMQ para patrones orientados a colas y routing keys.

Todos los arquetipos:

Nacen ya alineados a DDD + Hexagonal.

Incluyen wiring básico de seguridad, observabilidad y resiliencia vía starters.

Heredan las dependencias gestionadas por el Integration BOM.

Librerías Commons

Error-envelope estándar para respuestas de error homogeneizadas.

DTOs core para patrones de entrada/salida comunes.

Headers corporativos (traceId, correlationId, businessId, etc.).

Mappers y utilitarios compartidos.

Abstracciones de dominio (UseCase, Repository, Events) para reforzar DDD.

Starters

Starter de logging (configuración de logs JSON, MDC, patrones).

Starter de seguridad (JWT/OAuth2, filtros, integración con Mesh).

Starter de observabilidad: tracing/health/metrics (OpenTelemetry, health checks, métricas).

Starter de resiliencia (Fault Tolerance, RoutePolicies, DLQ, idempotencia).

Starter de quality (dependencias de test, configuraciones estándar).

Gobierno y conocimiento

Portal de arquitectura (por ejemplo, Backstage) con quickstarts, ejemplos, catálogos de patrones y documentación.

Registro de ADRs/RFCs que justifican decisiones de diseño del framework.

Esquema de versionado SemVer para librerías y arquetipos.

KPIs de adopción y calidad (porcentaje de servicios en framework, cobertura de tests, hallazgos de seguridad, etc.).

Documentación curada (arquitectura, estándares, guías de uso, ejemplos de referencia).

Este bloque responde a la pregunta: “¿Con qué artefactos concretos trabaja el equipo cuando usa el framework?”.

## 1.3. Plataforma DevSecOps corporativa

El tercer bloque es la capa de plataforma que soporta la construcción, despliegue y operación de los servicios generados por el framework.

Incluye:

BOM corporativo

Define las versiones aprobadas a nivel organización para JDK, frameworks, librerías clave y herramientas de seguridad.

Dockerfile multi-stage

Plantillas base optimizadas para:

Separar fases de build y runtime.

Reducir tamaño de imagen.

Alinear con las Golden Images corporativas (por ejemplo, UBI + JDK 21).

Helm chart base

Define la estructura estándar de despliegue en Kubernetes/GKE/OpenShift:

ConfigMap/Secrets.

Probes de health.

Recursos y límites.

Anotaciones para observabilidad, mesh, etc.

Golden Images

Imágenes base endurecidas y mantenidas por la organización (sistemas operativos, runtimes, agentes de seguridad).

Pipelines CI/CD (Azure DevOps u otra herramienta corporativa)

Compilación y empaquetado de los servicios generados por los arquetipos.

Ejecución de tests unitarios, de integración y de contrato.

Integración con herramientas de calidad y seguridad.

Generación de SBOM y firma de imágenes.

Despliegue automatizado a los entornos del banco.

En conjunto, este bloque asegura que todo lo que produce el Framework de Integración:

Se construye y despliega de forma segura y repetible.

Cumple los controles de seguridad y cumplimiento exigidos por la organización.

Opera dentro de un ecosistema observable y gobernado.

# 2. Componentes del Framework

Esta sección describe cada uno de los componentes que conforman el Framework de Integración. Para cada bloque se presenta su rol, responsabilidades, uso y la relación con los lineamientos corporativos.

La organización sigue la misma estructura del diagrama general:

Bloque central del Framework (Inicializador, BOM, arquetipos, commons, starters, gobierno).

Bloques laterales de lineamientos (Dominio, Seguridad, Observabilidad, Calidad, Resiliencia, API Contracts).

Plataforma DevSecOps (runtime + pipelines + estándares técnicos).

## 2.1. Initializer / Generador

Rol

Generar automáticamente proyectos listos para desarrollo, siguiendo estándares corporativos.

Responsabilidades

Crear la estructura base del servicio (DDD + arquitectura hexagonal).

Aplicar lineamientos de nombres, paquetes y convenciones.

Incluir dependencias controladas mediante Integration BOM.

Activar opcionalmente capacidades (seguridad, resiliencia, EDA, DB, Camel).

Inyectar Dockerfile y chart Helm corporativos.

Usuarios

Squads de desarrollo.

Equipo de Plataforma para evolucionar templates.

Lineamientos que hereda

Dominio (DDD/Hexagonal), seguridad, observabilidad, resiliencia, testing, contratos API, naming.

## 2.2. Integration BOM (Bill of Materials)

Rol

Controlar versiones, dependencias y compatibilidades de todo el ecosistema Quarkus + Camel + librerías Commons + Starters.

Responsabilidades

Fijar versiones corporativas de Quarkus, Camel y Java 21.

Publicar el catálogo de librerías aprobadas.

Garantizar reproducibilidad y evitar “dependency drift”.

Alinear las versiones con la plataforma DevSecOps y el BOM corporativo.

Usuarios

Todos los arquetipos.

Equipo de Plataforma para auditoría y mantenimiento.

Lineamientos que hereda

Seguridad, cumplimiento, control de dependencias, estabilidad operacional.

## 2.3. Arquetipos base

El framework provee un conjunto de arquetipos especializados, cada uno alineado por defecto a estándares corporativos.

Arquetipo REST (Quarkus - platform-http)

Rol

Construir APIs REST estandarizadas con OpenAPI, versionado y trazabilidad corporativa.

Responsabilidades

Exponer controladores REST.

Incluir validaciones, DTOs y error-envelope corporativo.

Aplicar seguridad: JWT, OAuth2, roles.

Activar logs JSON + MDC + traceId/correlationId.

Usuarios

Squads que construyen APIs sin orquestación compleja.

Lineamientos heredados

Observabilidad

Seguridad

API Contracts

Testing unitario.

Arquetipo Camel (REST DSL / EIPs)

Rol

Crear integraciones basadas en Camel para flujos EIP, orquestaciones, transformaciones y consumo de sistemas externos.

Responsabilidades

Activar Camel Quarkus.

Definir rutas estándar con policies de resiliencia.

Conectarse con sistemas internos vía REST, SOAP, DB o eventos.

Implementar headers estándar: traceId, correlationId, businessId.

Usuarios

Squads integradores y casos de uso multicanal.

Lineamientos heredados

Resiliencia (Circuit Breaker, Retry, Timeout)

Observabilidad

Seguridad

DDD.

Arquetipo DB (SQL/NoSQL – Panache/Flyway)

Rol

Facilitar el desarrollo de microservicios centrados en persistencia y consulta.

Responsabilidades

Configurar Panache con Java 21 reactivo.

Integrar Flyway/Liquibase para versionado de esquema.

Exponer repositorios siguiendo DDD.

Lineamientos heredados

Dominio

Testing

Seguridad

Observabilidad.

Arquetipo EDA (Kafka / PubSub)

Rol

Construir productores y consumidores de eventos corporativos.

Responsabilidades

Crear topics estándar, DLQ, retry topics.

Incluir policies de idempotencia.

Integrar mapeadores y headers para trazabilidad.

Publicar y consumir mensajes bajo AsyncAPI.

Lineamientos heredados

Resiliencia

API Contracts

Observabilidad

Seguridad.

Arquetipo RabbitMQ

Rol

Integrar con colas corporativas where Pub/Sub o Kafka no aplican.

Responsabilidades

Configurar exchanges, queues y DLQ.

Aplicar reintentos escalonados.

Alinear formato de eventos.

Lineamientos heredados

Resiliencia

Observabilidad

Seguridad.

## 2.4. Librerías “Commons”

Rol

Centralizar los componentes transversales que todos los servicios necesitan, no dependen de Quarkus-Camel.

Incluye

Conjunto de librerías reutilizables que encapsulan contratos, utilitarios y abstracciones de dominio, por ejemplo:

Error envelope y modelo de errores comunes

Representación estándar de errores técnicos y funcionales (códigos, mensajes, detalle, traceId/correlationId).

Soporte para formato alineado a RFC 7807 (opcional).

DTOs y contratos core

DTOs corporativos de uso transversal (p. ej. respuesta estándar, paginación, mensajes de auditoría).

Patrones comunes de entrada/salida (request/response base, envolturas para listas, etc.).

Headers corporativos y constantes

Definición centralizada de nombres de headers (X-Trace-Id, X-Correlation-Id, X-Business-Id, canal, usuario, cliente, etc.).

Enums y helpers para manejo de IDs de trazabilidad y metadatos.

Mappers y utilitarios compartidos

Conversores genéricos (domain ⇄ DTO, error ⇄ error-envelope).

Utilitarios de fechas, formateo, validaciones básicas, construcción de AuditEvent, etc.

Abstracciones de dominio y aplicación (DDD)

Interfaces base para UseCase, CommandHandler, DomainEvent, Repository, Specification, etc.

Contratos que refuerzan la separación domain / application / infrastructure y estandarizan la forma de modelar casos de uso.

Responsabilidades

Evitar duplicación de código.

Mantener consistencia entre servicios.

Proveer bases para pruebas unitarias.

Usuarios

Squads de desarrollo

Todos los servicios creados por el Initializer.

Lineamientos heredados

Dominio

Seguridad

Observabilidad

Resiliencia mínima.

## 2.5. Starters

Componentes preconfigurados que inyectan capacidades clave al servicio sin configuraciones manuales adicionales, implementados con Quarkus – Camel.

Starters (mínimo obligatorio en nuevos servicios)

Logging Starter

Configuración de logs JSON, MDC y categorías estándar (TECH, BUSINESS, AUDIT).

Integración con traceId/correlationId/businessId.

Convenciones de logger (nombres de categorías, formatos, niveles por defecto).

Exception / Error Starter

Mappers globales de excepciones a error-envelope estándar.

Estructura unificada de códigos de error técnicos/funcionales.

Soporte para trazabilidad en errores (traceId, correlationId) y respuesta segura (sin detalles sensibles).

Observability Starter

Integración con OpenTelemetry (traces), Micrometer/Prometheus (métricas) y SmallRye Health (health/readiness).

Configuración por defecto de endpoints /q/metrics, /q/health, /q/health/ready.

Tags y convenciones básicas de métricas técnicas y, opcionalmente, de negocio.

Resilience Starter

Integración con MicroProfile Fault Tolerance y políticas de resiliencia preconfiguradas.

Anotaciones y helpers (p. ej. @ResilientHttpOutbound, @ResilientKafkaOutbound, políticas de retries con backoff+jitter, circuit breaker, bulkhead).

Soporte para DLQ/Retry Topic en rutas Camel (cuando aplique).

Security Starter

Configuración estándar de OAuth2/JWT (OIDC), validación de tokens, roles/scopes.

Filtros para validación de headers mínimos (canal, usuario, cliente, app-id).

Integración con Mesh/mTLS y políticas de seguridad recomendadas.

Starters aplicables al arquetipo Camel

El arquetipo Camel reutiliza los mismos starters transversales del Framework (logging, exception, observability, resilience, security, quality).

Adicionalmente, se recomienda definir un integration-camel-starter que encapsule:

dependencias Camel estándar aprobadas,

base de RouteBuilder y convenciones de IDs de ruta,

políticas de error handling y DLQ,

métricas y trazabilidad por ruta.

Opcionalmente, puede complementarse con un messaging-starter (Kafka/PubSub) cuando la mayoría de integraciones sean event-driven.

Otros Starters recomendados

Quality / Testing Starter

Dependencias y configuración base de testing: JUnit 5, RestAssured, Testcontainers, Mockito, etc.

Configuración de Jacoco/cobertura y reglas mínimas esperadas.

Integración básica con Sonar (nombres de reports, ubicación de archivos).

Messaging Starter (Kafka / PubSub) (opcional, según estrategia)

Configuración estándar de productores/consumidores Kafka o PubSub.

Convenciones de topics, DLQ, headers de trazabilidad, idempotencia.

Persistence Starter (si se estandariza DB)

Configuración base de datasources, pools, migraciones.

Convenciones de naming de esquemas/tablas y políticas de timeouts.

## 2.6. Gobierno & Conocimiento

Rol

Asegurar que el framework evoluciona y se adopta correctamente.

Incluye

Portal Backstage (Quickstarts, catálogos, plantillas).

ADRs (Architecture Decision Records).

RFCs de cambios mayores.

Versionado SemVer del framework.

KPIs de adopción.

Repositorio de documentación (arquitectura, estándares, guías, ejemplos).

Responsabilidades

Mantener la gobernanza técnica.

Estandarizar mejores prácticas.

Asegurar consistencia en squads y dominios.

## 2.7. Plataforma DevSecOps (bloque inferior)

Rol

Proveer el ecosistema técnico donde los servicios se construyen, validan y despliegan.

Componentes incluidos

BOM corporativo: estándar global de dependencias.

Dockerfile multi-stage (build + runtime).

Golden Images (UBI + JDK21).

Helm chart base para Kubernetes/Anthos.

Pipelines CI/CD (Azure DevOps) con:

SBOM (CycloneDX)

Firma de contenedor (Cosign)

Scan de imagen (Trivy)

SAST/SCA (Veracode, Prisma Cloud)

Deploy automatizado.

Usuarios

Equipo DevOps + squads que despliegan servicios.

Lineamientos heredados

Seguridad

Cumplimiento

Resiliencia

Operación.

# 3. Forma de uso del Framework de Integración

La forma de uso del Framework define cómo los equipos de desarrollo (squads) interactúan con los arquetipos, librerías y lineamientos, desde la creación de un nuevo servicio hasta su despliegue en producción. El objetivo es garantizar que todos los servicios nacen y evolucionan de manera estandarizada, gobernada y alineada a la arquitectura corporativa.

El proceso se organiza en cinco etapas principales, totalmente integradas con las capacidades del framework y la plataforma DevSecOps.

## 3.1. Generación del proyecto (Initializer)

El uso del framework inicia con el Initializer, una herramienta CLI/portal (p. ej., Backstage) que permite a los equipos crear un nuevo servicio mediante plantillas preconfiguradas.

El desarrollador selecciona:

Tipo de servicio o integración:

REST, Camel, EDA Kafka, EDA RabbitMQ, DB SQL/NoSQL, etc.

Capacidades iniciales:

Seguridad JWT/OAuth2

Observabilidad (OpenTelemetry, logs JSON, métricas)

Resiliencia (Fault Tolerance, Circuit Breaker, Retry, Timeout)

SAGA/Outbox (si aplica)

Opciones de conectividad:

DB, Pub/Sub, Kafka, API externas.

La herramienta genera un proyecto completo que contiene:

Estructura en capas (DDD + Hexagonal).

Dependencias alineadas al Integration BOM.

Starters configurados (logging, tracing, metrics, security, resilience).

Dockerfile y chart Helm base.

Pruebas unitarias mínimas incluidas.

Resultado: el equipo obtiene un repositorio listo para comenzar a desarrollar, totalmente consistente con los estándares corporativos.

## 3.2. Desarrollo del dominio y casos de uso

Una vez creado el proyecto, el equipo desarrolla la funcionalidad en torno al dominio, siguiendo los lineamientos establecidos:

Diseño sobre Bounded Contexts BIAN/DDD.

Casos de uso en la capa de aplicación.

Repositorios, entidades y eventos en la capa de dominio.

Adaptadores (REST, mensajería, base de datos) en la capa de infraestructura.

Durante el desarrollo, los desarrolladores consumen:

DTOs comunes del framework.

Utilitarios para trazabilidad, manejo de errores y mapeo.

Headers estándar (traceId, correlationId, businessId).

Policies de resiliencia listas para uso.

Librerías de integración (Camel, Kafka, Database, Pub/Sub, RabbitMQ).

El framework garantiza que todas las implementaciones:

Sean consistentes con la arquitectura hexagonal.

Cumplan estándares de calidad y seguridad desde el primer commit.

Incluyan observabilidad por defecto.

## 3.3. Integración con sistemas internos y externos

El framework proporciona componentes listos para uso que facilitan la integración con el ecosistema corporativo:

Adaptadores REST basados en platform-http (Quarkus).

Rutas Camel configuradas bajo estándares corporativos (EIPs, transformaciones, filtros, validaciones).

Conectores EDA para:

Kafka (producers/consumers, DLQ, retry topics, idempotencia).

Pub/Sub (Google).

RabbitMQ (colas, routing keys, DLQ).

Acceso a bases de datos SQL/NoSQL con Panache reactivo.

Seguridad corporativa integrada (JWT, roles, policies, Mesh).

Secretos recuperados automáticamente vía GSM/KMS.

El framework ofrece starters especializados para habilitar fácilmente cada integración y evitar configuraciones manuales.

## 3.4. Testing, calidad y pipelines CI/CD

Los servicios generados por el framework incluyen integración automática con la plataforma DevSecOps corporativa:

Testing incluido:

Pruebas unitarias mínimas preconfiguradas.

Pruebas de integración con Testcontainers.

Configuración para contract testing con OpenAPI/AsyncAPI.

Mock servers para pruebas aisladas.

Pipelines CI/CD corporativos:

Análisis estático y de dependencias (SAST/SCA).

Generación de SBOM (CycloneDX).

Scan de contenedores (Trivy).

Firma de contenedores (Cosign).

Despliegue automático mediante Helm Charts.

Integración con Golden Images de runtime.

Esto garantiza confianza en los cambios y trazabilidad completa desde el código hasta producción.

## 3.5. Operación, monitoreo y resiliencia

Una vez desplegado, el servicio opera bajo los lineamientos del framework:

Logs JSON enriquecidos con trazabilidad completa.

Trazas distribuidas con OpenTelemetry.

Métricas técnicas y de negocio para dashboards Prometheus/Grafana.

Health checks /q/health integrados con Kubernetes.

Políticas de resiliencia activas:

Circuit Breaker,

Retries controlados,

Timeouts,

Idempotencia,

Manejo de DLQ,

Patrones SAGA/Outbox.

Herramientas de observabilidad corporativa procesan:

Tiempos de respuesta,

Errores,

Flujos críticos,

KPIs de servicios,

Causas raíz para incidentes.

La arquitectura asegura que cada servicio es:

Trazable,

Resiliente,

Observado,

Seguro,

Gobernado.

Resumen del uso: