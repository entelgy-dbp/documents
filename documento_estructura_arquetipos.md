# Definición de la Estructura de Arquetipos
Historial de las Revisiones
Las siguientes tablas describen la historia de modificación del documento para propósitos de seguimiento.
Únicamente los cambios que produzcan una nueva versión deberán ser mostrados en estas tablas.
## Resumen Ejecutivo
El Framework de Integración Compartamos define un conjunto de arquetipos corporativos para la generación de microservicios basados en Java 21, Quarkus y Apache Camel 4, alineados con los principios:
Secure by default
Observable by default
Resilient by default
Este documento establece la estructura estándar de proyecto para dichos arquetipos, abarcando organización de carpetas, capas lógicas, aplicación de puertos hexagonales, responsabilidades por capa, configuración técnica y lineamientos DevSecOps.
El objetivo es garantizar que todas las soluciones construidas por los equipos de desarrollo sean consistentes, escalables, mantenibles y gobernables, alineadas a un modelo operativo corporativo único.
Alcances principales del documento
### 1. Estructura y capas obligatorias
La estructura formal del proyecto generada por los arquetipos incluye:
Capas definidas según DDD + Arquitectura Hexagonal:
domain/
application/
infrastructure/
Modelado estándar de Ports (Hexagonal):
application/port/in → Casos de uso expuestos al exterior
application/port/out → Dependencias externas requeridas por la aplicación
Adaptadores de entrada/salida implementados exclusivamente en infrastructure/
Infraestructura técnica estandarizada:
infrastructure/rest/ → Controladores REST
infrastructure/proxy/ → Integración con sistemas externos
infrastructure/repository/ → Persistencia (Redis, DB, colas, etc.)
infrastructure/adapters/ → Otros adaptadores (Kafka, schedulers, MQ)
Carpeta opcional cross/ solo para utilidades locales del microservicio, nunca para elementos incluidos en los starters.
Carpeta config/ para configuraciones del runtime (JSON, CORS, Camel, seguridad, registros, mappers, etc.)
Carpetas de despliegue y operaciones:
k8s/, helm/, docker/, ops/
Resultado final:
Todos los microservicios generados comparten la misma anatomía arquitectónica y topología de carpetas, permitiendo estandarización, auditoría técnica y mantenibilidad.
### 2. Uso obligatorio de BOMs corporativos
Los arquetipos consumen los BOMs corporativos:
corporate-bom
integration-bom
Estos controlan las versiones de:
Quarkus
Apache Camel
Persistencia
Seguridad
Logging
Observabilidad
Testing
Framework interno y starters
Esto elimina la gestión individual de versiones y asegura:
Compatibilidad técnica
Calidad certificada
Seguridad reforzada
Ejecución homogénea en todos los entornos (on-prem, GCP, AKS, AWS EKS)
### 3. Lineamientos de arquitectura
El documento alinea los arquetipos con:
DDD (Domain Driven Design)
Arquitectura Hexagonal
Clean Architecture
Principios SOLID
Diseño por contratos
Decisiones clave:
Dominio puro (domain/)
Sin puertos de entrada o salida
Sin frameworks
Sin HTTP, SOAP, persistencia
Zero coupling con infraestructura
Application como core orquestador
Define casos de uso
Expone puertos de entrada (port/in)
Declara puertos de salida (port/out)
No invoca infraestructura directamente (solo a través de interfaces)
Infrastructure como implementador técnico
Adaptadores REST / Camel / Kafka
Integraciones externas (REST, SOAP, MQ, DB, Redis)
Resiliencia (timeouts, retries, circuit breakers)
Seguridad, trazabilidad y observabilidad aplicadas donde corresponde
Esta convención materializa la arquitectura hexagonal dentro del ecosistema Quarkus + Camel.
### 4. Seguridad integrada
Los arquetipos incorporan:
JWT / OAuth2
Validación de cabeceras mínimas
Roles / authorities
Integración con security-starter
Controles para entornos on-prem y cloud (GCP, AKS, OpenShift)
No se generan endpoints sin autenticación ni se permite exponer APIs sin políticas de autorización
### 5. Observabilidad estándar (Observability by default)
Todo microservicio nace con:
Logs estructurados JSON
traceId, correlationId, businessId
Micrometer Metrics → Prometheus
Health checks → SmallRye Health
Trazas OTEL exportables a Grafana/Tempo
Los filtros y mappers se entregan ya configurados a través de starters corporativos.
### 6. Resiliencia corporativa (Resilient by default)
La resiliencia se aplica exclusivamente a nivel de adaptadores (infrastructure/) mediante:
resilience-starter
Policies globales
@ResilientHttpOutbound
Retries
Timeouts
Circuit breakers
Bulkhead
Fallbacks
Ninguna lógica de resiliencia se define en dominio ni aplicación.
### 7. Plantilla DevSecOps de referencia
El documento proporciona un modelo operativo estándar para:
Manifiestos Kubernetes
Charts Helm
Integración con GKE on-prem/GDC
pipeline corporativo:
Build
Testing
Análisis estático (SAST)
SCA
Container scan
SBOM
Deploy controlado
Incluye ejemplos y políticas OPA/Conftest para despliegue seguro.
### 8. Criterios para un arquetipo “Done”
Un arquetipo se considera listo cuando:
Genera un proyecto funcional sin modificaciones manuales
Compila inmediatamente
Expone salud, métricas, trazabilidad y seguridad desde el primer minuto
Aplica resiliencia en adaptadores externos
Está alineado 100% a estructura corporativa
Trae documentación mínima:
README estándar
Ejemplo de casos de uso
Contratos REST válidos
## Conclusión
Este documento convierte los lineamientos del Framework de Integración Compartamos en una guía executable y gobernable, asegurando que cada microservicio generado:
Nace con la arquitectura correcta
Consume BOMs corporativos aprobados
Es seguro desde el día cero
Es observable sin configuración adicional
Aplicará resiliencia en todos los puntos de integración
Está listo para producción en un ambiente bancario regulado y de misión crítica
El resultado final es un ecosistema homogéneo, auditado y mantenible, donde la innovación se acelera y la complejidad técnica se controla mediante estándares de arquitectura y DevSecOps completamente unificados
### 1. Objetivo del documento
El propósito de este documento es definir la estructura estándar de los arquetipos de proyecto del Framework de Integración Compartamos, estableciendo con claridad la relación entre ambos niveles:
Nivel 1 — Framework de Integración (Core)
Es el conjunto de módulos transversales reutilizables (starters, commons, BOMs y lineamientos) que proporcionan:
Seguridad, resiliencia y observabilidad by default.
Estándares de trazabilidad, logging, métricas y manejo de errores.
Dependencias gobernadas mediante corporate-bom e integration-bom.
Componentes comunes reutilizables (TraceFilter, HttpClient resiliente, filtros de seguridad, etc.).
Nivel 2 — Arquetipos de Proyecto
Son las plantillas base usadas por los equipos para generar nuevos microservicios.
Cada arquetipo:
Usa los starters y BOMs del Framework (nivel 1).
Genera un proyecto con estructura hexagonal coherente.
Garantiza que el microservicio resultante nace con:
Seguridad habilitada (Secure by default)
Observabilidad integrada (Observable by default)
Resiliencia en puntos de integración (Resilient by default)
Calidad mínima para DevSecOps
### 2. Alcance y relación con otros documentos
Este documento define específicamente cómo deben estructurarse los arquetipos del nivel 2, incluyendo:
Estructura de carpetas y paquetes basada en DDD + Arquitectura Hexagonal.
Uso de los BOM corporativos y dependencias aprobadas del Framework.
Organización de las capas lógicas:
domain
application
infrastructure
cross (no contiene transversales del framework)
Manejo y versionamiento de DTOs y contratos API.
Integración nativa de resiliencia, seguridad, observabilidad y lineamientos DevSecOps.
Este documento debe utilizarse para:
Diseñar y mantener los arquetipos oficiales del Framework de Integración.
Asegurar que todos los microservicios derivados a partir de los arquetipos cumplen consistentemente con los principios rectores del framework:
Secure by default
Observable by default
Resilient by default
Se relaciona directamente con:
Documento de Arquitectura de Software del Framework de Integración
Define la visión global, componentes, patrones (DDD, Hexagonal, EDA, BIAN, etc.).
Documento de Lineamientos y Estándares del Framework de Integración
En especial la sección:
### 2.1 Principios rectores del framework
### 2.2 Lineamientos de modelado de dominio
### 2.3 Lineamientos de seguridad
### 2.4 Lineamientos de observabilidad
### 2.5–2.9 resiliencia, calidad, contratos y uso de arquetipos
Este documento baja esos lineamientos al nivel práctico de proyecto/arquetipo (carpetas, clases y POM).
### 3. Principios y objetivos del arquetipo
Los arquetipos del framework deben:
Reflejar los principios rectores del framework
Secure by default: seguridad habilitada desde la plantilla (JWT/OIDC, headers obligatorios, no secretos en código).
Observable by default: logging JSON, trazas con OpenTelemetry, métricas con Micrometer, health checks.
Resilient by default: integración con componentes de resiliencia (timeouts, retries, circuit breakers, DLQ).
Basarse en DDD + Hexagonal + Clean Architecture
Capas claras: Domain, Application, Infrastructure, Interfaces.
Separación estricta entre modelo de dominio y modelo expuesto (DTOs).
Adaptadores en infrastructure, puertos en domain.
Fomentar reutilización y estandarización
Uso obligatorio de BOM corporativo e Integration BOM.
Uso de módulos starters comunes (observability, security, resilience).
Reducción de “inventos” por equipo.
Ser compatible con DevSecOps y GKE on-prem
Estructura que facilite pipelines estándar.
Manifests Kubernetes, Helm charts y políticas (OPA/Conftest) preintegradas.
### 4. Tipos de arquetipo contemplados
El Framework de Integración puede definir distintos arquetipos especializados, todos basados en esta estructura general:
Arquetipo REST/API
Microservicios que exponen endpoints REST síncronos.
Arquetipo de Integración Camel
Servicios que centralizan orquestación, enrutamiento, transformación o conectividad hacia colas, ESB, timers.
Arquetipo EDA (Event-Driven / Kafka, PubSub)
Servicios productores/consumidores de eventos.
Este documento define la base común de estructura de proyecto.
Cada arquetipo podrá especializarse agregando módulos específicos (por ejemplo, route/ más intensivo en el arquetipo Camel).
### 5. Estructura estándar del proyecto generado
### 5. Estructura estándar del proyecto generado
Los arquetipos del Framework de Integración Compartamos generan proyectos con una estructura homogénea y alineada a la arquitectura hexagonal, asegurando que todos los microservicios nacen con los componentes transversales del Framework (seguridad, resiliencia, observabilidad, calidad y DevSecOps).
Antes de detallar la estructura, es importante diferenciar:
### 5.0 Niveles de estructura
Nivel 1 — Framework de Integración (startes, commons, BOMs)
Este nivel no lo generan los squads, lo mantiene el equipo responsable de dar soporte y mantenimiento al framework, contiene:
integration-framework/
├── bom/
│   ├── corporate-bom/
│   └── integration-bom/
├── starters/
│   ├── exception-starter/
│   ├── resilience-starter/
│   ├── security-starter/
│   ├── logging-starter/
│   └── observability-starter/
├── commons/
│   ├── core-utils/
│   ├── trace/
│   └── error/
└── samples/
└── ms-campaign-demo/
Este nivel define los artefactos reutilizables que el arquetipo consumirá.
Nivel 2 — Proyecto generado a partir de un arquetipo
Este es el proyecto que genera el equipo con:
mvn archetype:generate ...
Y cuya estructura se estandariza aquí.
### 5.1 Raíz del proyecto (nivel 2 – microservicio generado)
process-or-experience-<subdominio>/
├── .gitignore
├── README.md
├── pom.xml
├── src/
├── k8s/
├── helm/
├── ops/
└── docker/
Regla fundamental:
Todo proyecto generado DEBE mantener esta estructura. El contenido técnico transversal (seguridad, resiliencia, observabilidad) viene desde los starters del Framework (nivel 1), pero la estructura del proyecto es estándar para todos los microservicios (nivel 2).
### 5.1.1 .gitignore
Objetivo
Definir los archivos que no deben versionarse en el repositorio del microservicio.
Reglas
El .gitignore del arquetipo debe venir preconfigurado para excluir:
Artefactos de build:
/target/, .quarkus/, *.class
Directorios de IDE:
.idea/, .vscode/, .project, .settings/
Archivos temporales:
*.log, *.tmp, *.DS_Store
Archivos generados por herramientas locales de infraestructura:
Artefactos dentro de /k8s/ o /helm/ que no sean parte del chart oficial
Nunca deben versionarse credenciales, llaves privadas, tokens o archivos con secretos.
### 5.1.2 README.md
Objetivo
Proveer un documento de entrada para cualquier desarrollador o miembro de operaciones.
Contenido mínimo obligatorio
Prerrequisitos del proyecto generado:
Java 21
Maven
(Opcional) Quarkus CLI
Cómo ejecutar en local:
mvn quarkus:dev
Cómo probar los endpoints iniciales
Ejemplo de curl o Postman
Cómo empaquetar:
mvn package
Qué carpetas no deben modificarse sin alineación con plataforma, por ejemplo:
cross/ (no contiene componentes del framework)
config/
ops/ (scripts y políticas de DevSecOps)
Enlace a:
Lineamientos del Framework
Repositorio del Framework (nivel 1)
Documentación de arquitectura
### 5.1.3 pom.xml (proyecto generado)
Objetivo
Definir el descriptor Maven del microservicio, alineado de forma estricta con los BOMs corporativos y los starters del Framework (nivel 1).
Reglas principales
### 1. El POM del microservicio NO declara versiones de dependencias clave
Todas las versiones deben provenir de:
corporate-bom (Quarkus, Camel, logging, testing, seguridad base)
integration-bom (startes, librerías compartidas, trace, error-handling, resilience)
Esto garantiza:
Homogeneidad en todas las dependencias
Seguridad (no librerías vulnerables)
Facilita parcheo centralizado por el equipo de plataforma
### 2. El proyecto generado SÍ declara dependencias funcionales
Ejemplo:
<dependency>
<groupId>com.compartamos.integration</groupId>
<artifactId>resilience-starter</artifactId>
</dependency>
Pero NO pone <version>.
### 3. El POM del microservicio es normativo
Los equipos no deben modificar:
Versiones de Quarkus
Versiones de Camel
Versiones de starters
Reglas de seguridad
Configuración de compilación o plugins heredados
Cualquier cambio estructural debe gestionarse por el equipo de Framework (nivel 1).
### 4. Plugins de calidad
Deben estar preconfigurados en el BOM o en el arquetipo:
Checkstyle / Spotless
Surefire y Failsafe
SBOM y análisis de dependencias
Escaneo de vulnerabilidades de contenedores
Resultado: separación clara de responsabilidades
### 6. Estructura de carpetas Java (src/main/java)
La estructura estándar generada por los arquetipos refleja los principios del Framework de Integración: DDD, Arquitectura Hexagonal, separación explícita entre dominio, aplicación, adaptadores e infraestructura técnica, y agnosticismo total del dominio respecto a frameworks e infraestructura.
Esta estructura incorpora explícitamente la convención corporativa de ports en la capa de aplicación, alineada a Arquitectura Hexagonal y Clean Architecture
### 6.1 Vista general
La siguiente estructura de carpetas es la forma estándar generada por los arquetipos del Framework de Integración.
Refleja los principios de:
DDD (Domain-Driven Design)
Arquitectura Hexagonal
Clean Architecture
Separación explícita entre dominio, aplicación, puertos y adaptadores
Dominio completamente agnóstico a frameworks e infraestructura
En esta estructura, el dominio permanece puro, mientras que las dependencias externas se encapsulan mediante ports definidos en application/port e implementados exclusivamente en infrastructure.
src/main/java/com/compartamos/process/<recurso>/
├── domain/
│   ├── model/            # Entidades, Value Objects, Agregados
│   ├── events/           # Eventos de dominio (si aplica)
│   └── services/         # Servicios de dominio (reglas puras)
│
├── application/
│   ├── usecase/          # Casos de uso del sistema (orquestación)
│   ├── service/          # Servicios de aplicación (opcionales)
│   ├── dto/              # DTO internos de aplicación
│   └── port/
│       ├── in/           # Input Ports (casos de uso expuestos)
│       └── out/          # Output Ports (dependencias externas)
│
├── infrastructure/
│   ├── rest/             # Adaptadores REST (entrada)
│   │   └── dto/          # DTO expuestos vía API REST
│   ├── proxy/            # Adaptadores hacia sistemas externos
│   │   └── <sistema>/dto/# DTO de integración con sistemas externos
│   ├── repository/       # Implementaciones de puertos de persistencia
│   ├── adapters/         # Otros adaptadores técnicos (Kafka, File, Schedulers)
│   └── bean/             # Configuraciones técnicas (Factores, Conexión, etc.)
│
├── config/               # Configuraciones del runtime (CORS, Jackson, Camel)
│
├── route/                # Opcional (solo si existe Camel)
│                         # Contiene RouteBuilders sin lógica de negocio
│
└── cross/                # Componentes transversales propios
# (solo si NO son parte del framework)
Notas:
<recurso> debe ser un nombre de negocio simple y en minúsculas:
campaign, customer, payments, credit, loan, etc.
Todos los puertos (in/out) residen en application/port.
El dominio no contiene puertos ni depende de infraestructura.
Infrastructure es la única capa que implementa puertos de salida (port/out), y la única que interactúa con el mundo externo (REST, SOAP, DB, Redis, MQ, gRPC).
Controllers REST, rutas Camel, schedulers, consumers de eventos invocan exclusivamente puertos de entrada (port/in). Nunca acceden a casos de uso concretos ni adaptadores técnicos.
cross/ debe estar vacía salvo que exista una necesidad real de componentes transversales no incluidos en los starters del Framework.
### 6.2 Puertos (Ports) — Principio central Hexagonal
Los proyectos generados por los arquetipos del Framework deben modelar explícitamente los puertos (ports) de entrada y salida del sistema, como contrato formal de integración entre capas.
Los ports son interfaces que definen:
Qué casos de uso expone la aplicación (puertos de entrada/ inbound ports).
Qué servicios externos necesita el dominio/aplicación para cumplir dichos casos de uso (puertos de salida/ outbound ports).
Regla principal:
Todos los puertos (inbound y outbound) se encuentran exclusivamente en la capa application/port/.
Esto preserva un dominio completamente limpio, agnóstico a frameworks y a infraestructura. Se distinguen dos tipos:
Inbound Ports (puertos de entrada)
Outbound Ports (puertos de salida)
En el enfoque Hexagonal del Framework:
application/port/in/ define lo que la aplicación ofrece al exterior
application/port/out/ define lo que la aplicación necesita del exterior
### 6.2.1 Inbound Ports (puertos de entrada)
Los inbound ports representan los casos de uso que el microservicio expone al exterior: operaciones del negocio, procesos y servicios internos.
Ubicación:
src/main/java/com/compartamos/process/xxx/application/port/in
Responsabilidades:
Ser el punto de entrada oficial a la lógica de negocio
Definir las operaciones que pueden ser invocadas desde:
Controladores REST
Rutas Camel
Consumers de eventos
Procesos batch u orquestadores externos
Expresar la intención de negocio (consultar, registrar, validar, evaluar, procesar, recuperar)
Ejemplo:
public interface ConsultarScoreUseCase {
ScoreData consultar(String tipoDocumento, String numeroDocumento);
}
Reglas:
No dependen de Quarkus, Camel, Redis, HTTP, JPA, etc.
No contienen detalles técnicos
Pueden usar:
modelos de dominio
DTO internos de aplicación
Son invocados exclusivamente por adaptadores de entrada en infrastructure
### 6.2.2 Outbound Ports (puertos de salida)
Los outbound ports representan las dependencias externas necesarias para ejecutar los casos de uso, tales como:
Sistemas externos por REST/SOAP
Repositorios persistentes (Redis, BD, colas)
Proveedores de datos
Servicios corporativos
En términos Hexagonales, representan “lo que la aplicación necesita del exterior”.
Ubicación:
src/main/java/com/compartamos/process/xxx/application/port/out
Responsabilidades:
Declarar qué servicios externos necesita la aplicación:
Experian (consulta de score)
Bantotal (fecha del sistema, guía de procesos, autenticación)
SCO
Catálogo
Declarar intenciones de persistencia:
almacenar claims
obtener resultados procesados
recuperar configuraciones
Importante:
Si se declaran interfaces de repositorio puro en domain/repository, estas NO son puertos.
Los puertos de salida se declaran siempre en application/port/out, incluso cuando la infraestructura sea una BD, Redis o SOAP.
Ejemplo:
public interface ExperianPort {
ExperianScore consultarScore(ExperianRequest request);
}
si el caso de uso necesita persistencia:
public interface ClaimsStorePort {
void save(Claims claims);
Optional<Claims> findByUser(String username);
}
Reglas:
No contienen anotaciones de frameworks
No dependen de:
RestClient
CXF Client
HTTP models
DTO externos
Response, codes, headers, namespaces
Sólo expresan contratos de negocio o modelos neutrales
Infrastructure implementa estas interfaces con tecnología concreta
### 6.2.3 Relación entre Ports y Adapters
Inbound Ports
Se invocan desde adaptadores de entrada:
REST controllers (infrastructure/rest)
Camel RouteBuilders (route/)
Consumers Kafka (infrastructure/adapters)
Permiten desacoplar la exposición de casos de uso de cualquier HTTP, Camel o mensajería
Outbound Ports
Se implementan únicamente en la capa infrastructure:
infrastructure/proxy/<sistema>/*.java (REST, SOAP)
infrastructure/repository/*.java (Redis, JPA, JDBC, DB)
infrastructure/adapters/*.java
Los adaptadores técnicos usan RestClient, SOAP (CXF), Redis Client, SQL, etc., pero la capa application no debe conocerlos.
Flujo completo (ejemplo canónico)
REST Controller invoca un inbound port
ConsultarScoreUseCase
El Caso de Uso orquesta la lógica y llama a un outbound port
ExperianPort
La capa Infrastructure implementa ese outbound port
usando RestClient o SOAP
Ejemplo
application/port/in/ConsultarScoreUseCase.java
application/port/out/ExperianPort.java
infrastructure/proxy/experian/ExperianHttpClientAdapter.java
application/usecase/ConsultarScoreUseCaseImpl.java
### 6.2.4 Beneficios de los Ports en el Framework
Dominio completamente libre de dependencias técnicas
Ports expresan contratos formales entre capas
Tests simples con mocks (se mockean outbound ports en tests de application)
Flexibilidad en tecnología:
REST ↔ SOAP ↔ gRPC
Redis ↔ DB
Fuse → Quarkus → cualquier otra
Cero contaminaciones del dominio
sin DTO externos
sin protocolos
sin errores HTTP en el modelo
sin Response, JSON, headers, etc.
### 6.3 application/
La capa application actúa como el núcleo orquestador de lógica de aplicación: coordina casos de uso, aplica reglas de proceso, gestiona flujos y define los puertos (ports) necesarios para comunicarse con el exterior.
No implementa infraestructura y no conoce detalles técnicos. Se centra en la intención del negocio: qué debe ocurrir para cumplir un caso de uso, no cómo se conecta con sistemas externos.
Responsabilidades centrales
Definir los casos de uso del microservicio.
Modelar las reglas de proceso y coordinación entre componentes de negocio.
Exponer operaciones de negocio hacia adaptadores de entrada (REST, Camel, eventos) a través de Input Ports.
Declarar (mediante Output Ports) lo que la aplicación necesita del exterior:
sistemas HTTP/SOAP
persistencia
mensajería
colas
almacenamiento temporal
Mantener la lógica del proceso completamente libre de tecnología.
Depender únicamente de:
domain
ports (application/port/in y application/port/out)
Subcarpetas:
application/usecase/
Define las clases que implementan los casos de uso y la lógica de aplicación.
Representan procesos del negocio o flujos de integración.
Ejemplos:
GetCampaignsUseCase
CreateCustomerUseCase
ConsultarScorePersonaUseCase
ConsultarDatosExperianUseCase
Reglas:
Orquestan lógica y decisiones de proceso.
Consumen puertos de salida (application/port/out) para interactuar con el exterior.
Pueden usar modelos de dominio y DTO internos.
No contienen lógica de presentación ni detalles tecnológicos.
Nunca realizan llamadas HTTP, DB o Redis directamente.
application/service/
Servicios reutilizables de aplicación que no representan un caso de uso específico, pero soportan la lógica de coordinación.
Ejemplos:
Validaciones transversales del negocio
Cálculo de reglas
Conversión de formatos internos
Si el comportamiento forma parte de un flujo de negocio claramente definido, va en usecase.
Si es soporte reutilizable, va en service.
application/dto/
DTO internos opcionales usados para transportar información entre casos de uso y puertos.
Características:
No representan contratos externos (esos viven en infrastructure/rest/dto o proxy/.../dto)
No contienen anotaciones de frameworks.
Pueden mapearse contra modelos de dominio cuando corresponde.
application/port/in/ (Input Ports)
Interfaces que representan las operaciones del negocio que el microservicio expone hacia el exterior.
Ubicación: application/port/in/
Se invocan desde adaptadores de entrada:
infrastructure/rest
route/ (Camel)
infrastructure/adapters/ (consumidores Kafka, schedulers, etc.)
Qué representan: “Lo que la aplicación ofrece al exterior”.
Ejemplos:
ConsultarScorePersonaPort
GetCampaignsPort
ValidarParametrosPort
Reglas:
No dependen de frameworks (Quarkus, Camel, Redis, JDBC, etc.).
Usan modelos del dominio o DTO de aplicación.
Son el contrato formal hacia los adaptadores.
Los controladores REST, rutas Camel y otros adaptadores no llaman directamente a casos de uso concretos:
Siempre invocan un input port.
application/port/out/ (Output Ports)
Interfaces que representan las dependencias externas que la aplicación necesita para realizar un caso de uso.
Ubicación: application/port/out/
Sistemas HTTP (Experian, Bantotal, SCO, Catálogo)
Repositorios (Redis, DB)
Mensajería
Qué representan: “Lo que la aplicación necesita del exterior”.
Ejemplos:
ExperianPort
BantotalScorePort
BantotalConfigPort
SCOExperianPort
ClaimsRepositoryPort
Reglas
No contienen tecnología: cero HTTP, SOAP, persistencia, códigos de respuesta, DTO externos, etc.
No contienen anotaciones de frameworks.
Usan modelos de dominio o tipos internos neutrales.
Definen contratos formales para:
consulta de datos
operaciones remotas
persistencia temporal o permanente
Los adaptadores concretos (HTTP, SOAP, Redis, DB, Kafka, etc.) que satisfacen estos contratos viven en infrastructure.
Reglas clave de la capa application
Orquesta dominio + ports
Coordina el uso del modelo de dominio y las dependencias declaradas en ports.
No contiene lógica de presentación
Los detalles de exposición HTTP, versionamiento, CORS, OpenAPI, formatos de respuesta, etc., viven en infrastructure/rest.
No realiza llamadas externas directas
Todo uso de sistemas externos o persistencia se hace exclusivamente a través de application/port/out.
No usa clases de frameworks (application) es puro Java.
Ejemplos prohibidos:
@RestClient
@Table
EntityManager
RedisClient
SOAPClient
WebClient, Uni
Puede ser invocada desde cualquier adaptador externo, pero sólo mediante input ports:
REST controllers
Camel RouteBuilders
Jobs programados
Consumers Kafka
Integraciones internas
No depende de infrastructure
No conoce:
RestClient
HTTP clients
Redis
JDBC
stubs SOAP
repositorios concretos
Solo depende de:
domain
application/port/in
application/port/out
### 6.4 domain/
La carpeta domain representa el núcleo del modelo de negocio según DDD. Se mantiene 100% agnóstica a infraestructura, protocolos, frameworks o tecnología. Su responsabilidad es modelar conceptos de negocio de forma pura, libre de detalles técnicos.
Contenido
Entidades
Agregados
Value Objects
Eventos de dominio
Servicios de dominio (servicios de dominio puros, cuando aplica)
Interfaces de repositorio de dominio (opcionales)
ubicadas en: domain/repository/
Sobre domain/repository/
Si el dominio necesita expresar la intención de persistir (por ejemplo: “guardar el score calculado”, “leer claims”, “guardar una gestión”), puede declarar interfaces en domain/repository/.
Pero estas interfaces NO se usan directamente por la aplicación ni por infraestructura.
En la arquitectura del framework, NO son outbound ports.
En su lugar:
La intención del repositorio se expresa en domain/repository/MyRepository
La aplicación declara el contrato real de integración en:
application/port/out/MyRepositoryPort
La infraestructura implementa application/port/out, no domain/repository.
Esto permite mantener un dominio muy puro y alineado a DDD, sin mezclarlo con puertos hexagonales.
Reglas
No depende de Quarkus, Camel ni cualquier otro framework.
No contiene puertos de entrada ni puertos de salida.
(Los ports viven exclusivamente en application/port/in y application/port/out).
No contiene anotaciones técnicas (JPA, REST, JSON, Redis, etc.).
No conoce DTOs externos, ni modelos de transporte (REST, SOAP, Kafka, etc.).
No conoce implementaciones concretas de persistencia:
Redis
JDBC
JPA
colas
RestClient
Si se declaran repositorios en domain/repository, deben ser:
interfaces puras
libres de tecnología
con tipos de dominio, no DTO de infraestructura
Resultado
El dominio permanece completamente agnóstico a la infraestructura. Esto permite:
Reutilizar el modelo en otros contexto (frontend, batch, microservicios, pruebas unitarias sin frameworks).
Probar la lógica de dominio con tests puros de JVM (sin contenedores, sin RestClient, sin Camel, sin Redis.)
Evolucionar la forma de exponer o persistir los datos (REST, eventos, DB, Redis, etc.) sin modificar el núcleo del negocio.
Cambiar adaptadores sin afectar el dominio, el dominio depende solo de sí mismo.
### 6.5 DTO expuestos (REST)
Los DTO expuestos hacia afuera deben ubicarse en:
infrastructure/rest/dto/
Reglas
Preferible usar record (Java 21).
No tener anotaciones de persistencia.
Versionar contratos si corresponde:
infrastructure/rest/v1/dto
infrastructure/rest/v2/dto
### 6.6 infrastructure/
La capa infrastructure materializa los puertos (ports) definidos en application/port/out mediante adaptadores técnicos concretos (REST Client, SOAP, Redis, DB, colas, etc.) y expone los casos de uso de la aplicación a través de adaptadores de entrada que invocan application/port/in.
Principio clave:
Infrastructure nunca define contratos de negocio (puertos). Sólo implementa los ports declarados en application/port/out y llama a los ports declarados en application/port/in.
Esta capa se encarga del detalle tecnológico, protocolos, formatos, clientes, frameworks, serialización, almacenamiento y conectividad.
Subcarpetas
infrastructure/rest/
Adaptadores de entrada para exponer el servicio por HTTP/REST usando Quarkus (RESTEasy Reactive).
Son los controladores REST que reciben la solicitud externa y son el punto de entrada de integración vía HTTP.
Responsabilidades:
Recibir request HTTP
Mapear DTO externos ↔ modelos internos
Invocar application/port/in (input ports)
Los input ports delegan en los casos de uso (application/usecase)
Mapear la respuesta a DTO expuestos.
Devolver respuestas HTTP
Propagar headers estándar de trazabilidad:
X-Trace-Id
X-Correlation-Id
X-Business-Id
Reglas
Cero lógicas de negocio.
Debe producir siempre JSON:
@Produces(MediaType.APPLICATION_JSON)
Endpoints versionados:
/v1/campaigns
/v1/customers/{id}
Solo usa DTO ubicados en:
infrastructure/rest/dto
Diseñado para pruebas de contrato:
OpenAPI
RestAssured
infrastructure/proxy/
Adaptadores hacia sistemas externos. Cada clase implementa un outbound port definido en application/port/out.
Tecnologías:
REST Clients
SOAP stubs (CXF)
Conectores HTTP, MQ, etc.
gRPC
Cada clase en esta carpeta implementa un port de application/port/out.
Ejemplo:
public class ExperianHttpClientAdapter implements ExperianPort { ... }
DTO de sistemas externos:
infrastructure/proxy/<sistema>/dto/
infrastructure/repository/
Implementaciones tecnológicas de persistencia necesarias para ejecutar casos de uso.
Redis
BD Relacionales (JPA/JDBC)
Cassandra
SQL u otros
Ejemplo:
RedisClaimsRepository implements ClaimsRepositoryPort
infrastructure/bean/
Configuraciones técnicas del runtime:
Kafka producers/consumers
Beans de conexión
Factories
Configuración de serializers
Conexiones HTTP/SSL corporativas
Esta carpeta contiene sólo configuración, nunca lógica de negocio.
infrastructure/adapters/
Otros adaptadores técnicos o integraciones no clasificadas como REST ni repositorios:
File conectors
Kafka consumers/producers
Integraciones legacy
Eventos internos y externos
Triggers
Reglas
Implementa los puertos definidos en: application/port/out.
Aplica resiliencia (timeouts, retries, CB vía:  resilience-starter.
Se comunica con el mundo externo.
HTTP
SOAP
Kafka
DB
File
MQ
u otros mecanismos
Nunca define puertos. Sólo los implementa.
### 6.6.1 Relación entre application/port y infrastructure/
En la arquitectura propuesta
Los puertos viven en la capa de aplicación
Las implementaciones concretas en infraestructura.
application/port/in
Define interfaces que representan los casos de uso que la aplicación expone. Son invocados por adaptadores de entrada, ubicados en:
infrastructure/rest (controladores HTTP)
route/ (rutas Camel)
infrastructure/adapters (consumidores Kafka, schedulers, etc.)
application/port/out
Define interfaces que representan lo que la aplicación necesita del exterior:
Sistemas HTTP/SOAP
Persistencia (Redis, BD, colas)
Integraciones corporativas
Son implementados exclusivamente en infrastructure:
infrastructure/proxy/<sistema>/…
infrastructure/repository/…
infrastructure/adapters/…
Ejemplo conceptual
Outbound Port:
application/port/out/ExperianPort.java
public interface ExperianPort {
ExperianScore consultarScore(ExperianRequest request);
}
Infraestructura que lo implementa:
infrastructure/proxy/experian/ExperianHttpClientAdapter.java
public class ExperianHttpClientAdapter implements ExperianPort { ... }
Ejemplo de persistencia:
Outbound Port:
application/port/out/ClaimsPort.java
public interface ClaimsPort {
void save(Claims claims);
Optional<Claims> find(String businessId);
}
Infraestructura:
infrastructure/repository/redis/RedisClaimsRepository.java
public class RedisClaimsRepository implements ClaimsPort { ... }
Reglas clave
Los puertos (in/out) siempre viven en application/port,
Nunca en domain.
Nunca en infrastructure
Infrastructure solo implementa interfaces definidas en application/port
No define contratos de negocio por sí misma.
Domain se mantiene agnóstico a infraestructura
No conoce puertos
No conoce adaptadores
No conoce tecnología
application conoce dominio y puertos
Actúa como core orquestador
infrastructure implementa tecnología
HTTP, SOAP, Redis, DB, Kafka, Schedulers, etc.
.
### 6.7 route/ (opcional arquetipo Camel)
Objetivo
La carpeta route/ se incluye únicamente en los microservicios cuyas integraciones se realicen mediante Apache Camel. Representa adaptadores de entrada basados en Camel, en línea con los principios de Arquitectura Hexagonal.
Las rutas son canales de entrada (inbound adapters) que reciben eventos, mensajes, archivos u otras señales externas, y delegan la ejecución del negocio hacia los Input Ports definidos en application/port/in.
Las rutas nunca deben contener lógica de negocio ni lógica de orquestación.
Su única responsabilidad es recibir, transformar y delegar.
Objetivo de las rutas Camel
Centralizar la integración de mensajes/eventos externos.
Actuar como puente de entrada entre transportes técnicos (HTTP, colas, archivos, timers, FTP, etc.) y los casos de uso declarados por el microservicio.
Convertir mensajes y protocolos externos a formatos internos neutrales.
Invocar exclusivamente interfaces de application/port/in como punto de entrada al negocio.
Reglas
Cada ruta implementa un RouteBuilder.
Su función es puramente técnica:
endpoints Camel (direct:, timer:, kafka:, file:, etc.)
logs, métricas, observabilidad
enricher/split/aggregate cuando aplique
validaciones técnicas
La coordinación de pasos, lógica de proceso o decisiones de negocio no se implementan aquí.
Delegación obligatoria a Input Ports
Toda ruta debe delegar hacia la capa application invocando únicamente puertos de entrada (application/port/in).
Prohibido:
invocar clases concretas de application/usecase
instanciar directamente un servicio
hacer HTTP o consumo de repositorios
Permitido:
La ruta Camel solo hace binding y delegación al puerto.
La lógica del proceso está en el usecase.
ConsultarScoreUseCase port;
from("direct:score")
.process(exchange -> {
ScoreRequest req = exchange.getMessage().getBody(ScoreRequest.class);
ScoreData response = port.consultar(req.tipoDocumento(), req.numeroDocumento());
exchange.getMessage().setBody(response);
});
Sin lógica de negocio
La ruta no:
valida reglas de negocio
decide flujos por condiciones del negocio
manipula entidades de dominio
aplica políticas, cálculos o decisiones funcionales
Todo eso vive en:
application/usecase
Trazabilidad y métricas estándar
Las rutas deben integrar:
etiquetas de métricas (ex: camel_route_id)
logs para auditoría técnica
propagación de headers de correlación:
X-Trace-Id
X-Correlation-Id
X-Business-Id
La estandarización de trazabilidad y observabilidad proviene de los starters del Framework.
Resiliencia solo en infraestructura
Patterns como:
retries
circuit breakers
fallbacks
timeouts
Se declaran únicamente en adaptadores de infraestructura que implementan puertos de salida (application/port/out).
Las rutas Camel no declaran resiliencia, solo enrutan.
La resiliencia aplicada sobre puertos outbound vive en:
infrastructure/proxy/
infrastructure/repository/
Configuración técnica fuera de las rutas
Las rutas mantienen exclusivamente la definición de flujos Camel.
Toda configuración técnica se ubica en:
infrastructure/config/
cross/
Ejemplos:
configuración de Camel
opciones de Marshalling
validadores de payload técnico
políticas de error técnico
Camel Context tuning
Conectores inbound, no logic business
Dependiendo del origen del mensaje, las rutas pueden:
Exponer endpoints HTTP (siempre delegando a ports)
Consumir Kafka topics
Leer archivos (FTP, Samba, local)
Recibir eventos mediante colas
Dispararse con schedulers
Integrar con SOAP si se encapsula como entrada técnica
Pero siempre cumplen el mismo rol:
Adaptar protocolo → Mapear payload técnico → Delegar a un Input Port
from("kafka:topic.score-request")
.routeId("consultar-score-kafka-route")
.unmarshal(json(ScoreKafkaEvent.class))
.process(ex -> {
var event = ex.getMessage().getBody(ScoreKafkaEvent.class);
var result = consultarScorePort.consultar(event.tipoDocumento(), event.numeroDocumento());
ex.getMessage().setBody(result);
});
Regla de oro
Una ruta Camel es un adaptador de entrada (inbound adapter).
Debe:
Recibir un mensaje/protocolo externo
Transformarlo a formato interno
Delegar únicamente a puertos de entrada (application/port/in)
Propagar respuesta o mensaje interno resultante
No debe:
acceder a infrastructure/proxy/
invocar repositorios
instanciar use cases concretos
aplicar políticas de negocio
Beneficio total
Mantiene el negocio desacoplado del transporte
Cambios de protocolo no afectan casos de uso
Facilita pruebas unitarias de application
Centraliza integración técnica
Alinea Camel con Hexagonal Architecture
### 6.8 config/
La carpeta config/ centraliza las configuraciones técnicas transversales del microservicio. Su objetivo es separar explícitamente toda configuración del runtime de la lógica de negocio, casos de uso o adaptaciones de integración externas.
Estas configuraciones abarcan temas como serialización JSON, manejo de errores globales, políticas CORS, configuración de Camel o filtros técnicos.
Nunca deben contener adaptadores ni implementaciones de puertos.
Objetivo
Agrupar todas las configuraciones técnicas relacionadas con el ecosistema Quarkus/Camel, asegurando:
claridad arquitectónica,
orden estructural,
facilidad de mantenimiento,
reducción del ruido técnico en adaptadores concretos.
Contenido típico
Configuración de serialización JSON (Jackson / JSON-B)
Configuración CORS
Configuración de Camel
Configuración de autenticación y autorización (OIDC/JWT) si no viene del starter corporativo
Mappers de error técnico (por ejemplo, RFC 7807) si aplica
Configuración adicional utilizada por otros módulos de infraestructura
Ejemplos comunes:
@ApplicationScoped
public class JsonConfig {
// Configuración Jackson o Json-B
}
@Singleton
public class CamelRuntimeConfig {
// Ajustes técnicos para rutas Camel
}
@ApplicationScoped
public class ErrorMapperConfig {
// Configuración de mappers globales de error
}
Reglas
No contiene lógica de negocio.
No define puertos de entrada ni salida.
No expone casos de uso.
No implementa adaptadores ni clientes hacia sistemas externos.
No contiene repositorios ni clases con acceso a datos.
Sus clases típicamente emplean anotaciones como:
@Singleton
@ApplicationScoped
Ubicación en el proyecto
La carpeta config/ se ubica directamente bajo el recurso de negocio, al mismo nivel que:
domain/
application/
infrastructure/
route/
cross/
config/
Aunque el contenido de config/ está relacionado conceptualmente con aspectos técnicos del runtime (y por tanto con infraestructura), no debe colocarse dentro de la carpeta infrastructure/.
La convención corporativa del Framework establece que config/ debe ubicarse como carpeta independiente y al mismo nivel jerárquico que domain, application, infrastructure, route y cross.
Esto garantiza una separación explícita entre:
Infraestructura de integración (adaptadores y puertos)
Configuración técnica transversal (runtime)
La ubicación clara evita confusión y facilita el gobierno arquitectónico del código.
Justificación:
Claridad conceptual
infrastructure/ contiene adaptadores concretos (REST, SOAP, Redis, DB, colas, eventos, etc.) que implementan puertos.
config/ solo configura la base técnica del runtime.
Separación de responsabilidades
infrastructure habla de tecnología y protocolos externos.
config habla de runtime del microservicio.
Neutralidad técnica y mantenibilidad
Configuraciones típicas pueden cambiar sin impactar puertos ni adaptadores.
Alineación con Quarkus, DDD y Clean Architecture
Muchas arquitecturas limpias separan:
configuración técnica,
infraestructura concreta,
dominio,
aplicación.
Evita el “code smell”
Si config/ viviera dentro de infrastructure/, sería fácil confundir su propósito con adaptadores o repositorios.
### 6.9 cross/
La carpeta cross/ se incluye únicamente para permitir que un microservicio defina componentes transversales propios, siempre que estos no formen parte del Framework de Integración.
Los componentes transversales estándar (trazabilidad, resiliencia, seguridad, utilitarios comunes) no deben ubicarse aquí, ya que provienen de los starters oficiales y se consumen como dependencias Maven.
Qué puede ir en cross/
Validadores específicos del servicio
Helpers utilitarios propios del dominio
Reglas técnicas o políticas que no pertenecen a:
domain/
application/
infrastructure/
Qué NO debe ir en cross/
Componentes provistos por el Framework:
TraceFilter (observability-starter)
ResilientHttpOutbound, fallbacks, FT (resilience-starter)
JWT/OIDC filters, roles validators (security-starter)
Helpers genéricos de utilidades (commons-core)
Regla
En la mayoría de los microservicios, esta carpeta deberá permanecer vacía, ya que toda la transversalidad estándar es provista por el Framework a través de JARs reutilizables.
### 6.10 Arquitecturta Hexagonal y Clean Architecture en el Arquetipo Framework
Para garantizar que los microservicios desarrollados bajo el Framework de Integración mantengan un diseño sostenible, desacoplado y alineado con DDD, se adopta una convención clara de arquitectura Hexagonal complementada con principios de Clean Architecture.
Esta convención se aplica estrictamente al arquetipo corporativo basado en Quarkus + Apache Camel, manteniendo un dominio totalmente agnóstico y una separación explícita entre:
Dominio (conceptos de negocio)
Aplicación (orquestación y puertos)
Infraestructura (adaptadores y tecnología)
Exposición vía REST o Camel (entrada)
Dominio (domain/) sin puertos
El dominio representa el corazón funcional del microservicio. Es el modelo estable y desacoplado de infraestructura, y refleja el lenguaje ubicuo del negocio (DDD):
Contiene:
Entidades
Agregados
Value Objects
Servicios de dominio puros
Eventos de dominio
No contiene:
Puertos de entrada ni de salida
Clases o anotaciones de frameworks
RestClient, SOAP Client, Redis, JDBC, JPA
DTOs externos
Manejo de errores HTTP
Adaptadores o llamadas a APIs externas
El dominio no conoce cómo es consumido ni cómo son persistidos sus datos.
Solo expresa el modelo de negocio.
Esto permite:
reutilizar el conocimiento del negocio sin reescribir infraestructura,
probar el dominio con tests JVM puros,
evolucionar protocolos, API, DB, integración y resiliencia sin modificar el modelo.
Application como “core orquestador” y lugar de los ports
La capa application/ concentra la lógica de orquestación y la definición de puertos hexagonales. Su misión es coordinar el flujo de negocio entre dominio y sistemas externos:
Subcarpetas:
application/usecase/
application/port/in/
application/port/out/
application/dto/
application/usecase
Define los casos de uso, procesos de integración y lógica de aplicación (NO de dominio).
Coordina pasos, decisiones, validaciones y acceso a puertos externos.
application/port/in
Puertos de entrada:
Interfaces que exponen casos de uso a los adaptadores inbound.
Ejemplo:
public interface ConsultarScoreUseCase {
ScoreData consultar(String tipoDoc, String numeroDoc);
}
Son invocados desde:
REST Controllers
Routes Camel
Event Consumers
Schedulers
No dependen de tecnología ni infraestructura.
application/port/out
Puertos de salida:
Interfaces que expresan qué necesita la aplicación del mundo externo — sin importar cómo se implementa.
Ejemplos:
ExperianPort
BantotalConfigPort
SCOExperianPort
ClaimsRepositoryPort
No contienen:
RestClient
CXF
Redis
DTO externos
HTTP codes
Anotaciones de frameworks
El caso de uso solo “conoce” el contrato, no la implementación.
Infrastructure como implementador de ports
La infrastructure/ provee adaptadores concretos que cumplen contratos declarados en application/port.
Adaptadores de entrada
infrastructure/rest/
route/
infrastructure/adapters/
Responsabilidad:
Recibir inputs externos, mapear y llamar a puertos de entrada (application/port/in).
Ejemplo:
@RestController
public class ScoreResource {
@Inject
ConsultarScoreUseCase useCase;  // Nunca un service concreto
@GET
public Response consultar(…) {
return useCase.consultar(...);
}
}
Adaptadores de salida
infrastructure/proxy/<sistema>/
infrastructure/repository/
infrastructure/adapters/
Responsabilidad:
Implementar los puertos de salida (application/port/out) usando tecnología:
REST Client
SOAP CXF
Redis
JDBC/SQL
gRPC
MQ
Ejemplo:
public class ExperianHttpClientAdapter implements ExperianPort {
@Inject
@RestClient
ExperianClient client;
@Override
public ExperianScore consultarScore(Request req) {
return client.call(req);
}
}
Reglas clave
Infrastructure NUNCA define contratos de negocio.
Solo implementa interfaces declaradas en application/port/out.
Lógica técnica vive aquí, nunca en Application o Domain.
Resiliencia (timeouts, retries, circuit breakers) se aplica exclusivamente en los adaptadores.
Beneficios del enfoque
Dominio limpio y estable:
Sin puertos, sin frameworks, sin DTO externos.
Aplicación como core orquestador:
application define “qué” se necesita (puertos in/out).
Solo depende de puertos y modelos de dominio.
Infraestructura interchangeable
infrastructure define “cómo” se implementa (adapters concretos).
Sin romper el dominio ni los casos de uso.
Alinea el arquetipo con Quarkus + Camel:
Rutas Camel y controllers REST invocan input ports.
Proxies REST/SOAP implementan output ports.
Tests simples:
El dominio se prueba con JVM pura.
Los usecases se prueban mockeando puertos.
La infraestructura se prueba aislada por contrato.
Evolución sin fricción
Cambios tecnológicos NO rompen modelos del negocio.
Migraciones de Camel, DB, APIs externas son transparentes.
Arquitectura gobernada
Los límites son explícitos y visibles:
DOMAIN  --> NO conoce nada
APPLICATION --> conoce Domain y Ports
INFRASTRUCTURE --> implementa Ports
ROUTE/REST --> invocan Ports
El arquetipo adopta una interpretación clara de Arquitectura Hexagonal y Clean Architecture donde:
Domain permanece neutral y puro
Application define puertos y casos de uso
Infrastructure implementa tecnología
Adaptadores inbound conectan al exterior vía puertos
El resultado es una arquitectura modular, testeable, gobernable y alineada con DDD, lista para escalar y evolucionar en ecosistemas complejos como Quarkus + Camel.
.
### 6.11 Regla de dependencias entre capas
Para asegurar que cada microservicio mantenga una arquitectura gobernable, limpia y evolutiva, el Framework establece reglas estrictas de dependencia entre capas.
Estas reglas reflejan el flujo natural de la Arquitectura Hexagonal:
Domain → Application → Ports → Infrastructure → Mundo externo
Y se basan en la “Regla de inversión de dependencias” y el aislamiento completo del dominio frente a tecnología.
Tabla de dependencias
Principios clave
### 1. Domain es el núcleo y solo depende de sí mismo
No conoce puertos
No conoce infraestructura
No conoce frameworks
Debe ser capaz de vivir solo con la JVM.
Si el dominio “sabe” que estamos usando REST, SOAP, Redis o CXF, ya no es dominio.
### 2. Application depende de Domain y de los Ports
Application es el “director de orquesta”.
Puede depender de:
domain/
application/port/in
application/port/out
No puede depender de infraestructura ni frameworks.
Si un caso de uso (usecase) llama directamente un RestClient, SOAP, repository físico o Redis, es desviación arquitectónica.
El acceso a sistemas externos SIEMPRE va por puertos de salida (application/port/out).
### 3. Infrastructure depende de Domain y Ports
Esta capa provee las implementaciones concretas, usando tecnología:
RestClient
CXF SOAP
Camel endpoints
Redis client
JDBC
Messaging
Configuración técnica
Pero nunca define contratos de negocio, solo implementa los ya definidos en application/port/out.
Ejemplo correcto:
public class ExperianAdapter implements ExperianPort { ... }
Ejemplo incorrecto (prohibido):
// Definir un port en infrastructure
public interface ExperianHttpContract { ... }
### 4. Adaptadores Inbound llaman EXCLUSIVAMENTE a Ports de entrada
REST controllers, rutas Camel, Kafka consumers, schedulers NO deben:
Invocar casos de uso concretos
Instanciar servicios
Inyectar adapters
Interactuar con ports de salida
Siempre invocan puertos de entrada (application/port/in):
@Inject
ConsultarScoreUseCase port;
Prohibido:
@Inject
ConsultarScoreUseCaseImpl service; // Desviación
Regla de Oro
Las dependencias solo se mueven hacia el centro:
Infrastructure → Application (ports) → Domain
Nunca al revés.
Infrastructure depende de Application (porque implementa ports)
Application depende de Domain (modelos y reglas)
Domain depende solo de sí mismo
### 7. Estructura de recursos (src/main/resources)
La carpeta src/main/resources/ contiene la configuración del runtime y los recursos estáticos necesarios para el funcionamiento del microservicio. Su contenido se mantiene mínimo, ya que muchas configuraciones transversales provienen directamente de los starters del Framework de Integración.
src/main/resources/
├── application.properties
├── META-INF/resources/
├── logging.properties   (opcional)
└── camel/               (opcional)
### 7.1 application.properties
Archivo principal de configuración del microservicio.
Responsabilidades
Definir parámetros básicos de ejecución:
Puerto del servicio
Configuración de JSON/Jackson
Integración con servicios externos (quarkus.rest-client.*)
Configuración de Camel (si aplica)
Configuración de DB o Redis (si aplica)
Permitir que los pipelines CI/CD sobreescriban valores mediante variables de entorno:
Credenciales
Endpoints por entorno
Configuración de seguridad
Datos sensibles marcados como ${ENV_VAR}
Reglas
No se deben incluir secretos ni tokens explícitos.
La configuración de trazabilidad, resiliencia, seguridad y métricas es mínima porque:
Logging JSON ya viene del observability-starter
OpenTelemetry y Micrometer también vienen del starter
Políticas de resiliencia vienen del resilience-starter
Validación JWT/OIDC viene del security-starter
Solo se declaran los parámetros específicos del microservicio.
### 7.2 META-INF/resources/
Carpeta que expone recursos estáticos.
Responsabilidades
Entregar contenido accesible vía HTTP sin necesidad de controladores.
Hospedar herramientas de desarrollo, por ejemplo:
Swagger UI / OpenAPI UI (si se habilita)
Paginas estáticas de referencia
Landing de endpoints de salud / estado (opcional)
Reglas
Desde Quarkus, todo archivo en esta carpeta se expone en http://host/<archivo>.
Su uso debe ser limitado y controlado por arquitectura.
Para documentación interna/autogenerada (OpenAPI) no se deben incluir archivos manuales; Quarkus los genera automáticamente.
### 7.3 logging.properties (opcional)
Archivo opcional para ajustes finos de logging.
Cuando usarlo
Solo cuando se requieren políticas avanzadas de logging no soportadas por application.properties.
Ejemplos:
Ajustar niveles de logging de sistemas externos.
Cambiar comportamiento del log handler para troubleshooting específico.
Reconstruir patrones temporales de salida en entornos locales.
Reglas
No se debe duplicar las configuraciones que ya provee el observability-starter.
No se deben definir nuevos formatos de logging (JSON ya viene estándar).
No se debe versionar logging.properties si no hay justificación técnica.
### 7.4 camel/ (opcional)
Carpeta para configuraciones Camel específicas cuando el arquetipo es de integración o routing.
Responsabilidades
Contener DSLs declarativos (XML, YAML) si un equipo opta por ese formato.
Reglas de rutas adicionales para:
Timers
Cron jobs
Integraciones basadas en endpoints (REST, JMS, File, Kafka, etc.)
Reglas
Si se usan rutas en Java DSL (preferido), esta carpeta puede quedar vacía.
La configuración base de Camel provista por el framework NO debe duplicarse aquí.
La carpeta existe solo para proyectos del tipo “Camel Integration”.
### 8. Estructura de pruebas (src/test/java)
Las pruebas deben reflejar la misma separación de responsabilidades que el código de producción (dominio, aplicación, infraestructura), pero sin sobredimensionar la estructura. El objetivo es garantizar que cada microservicio generado por el arquetipo:
Valide correctamente su lógica de negocio (domain + application).
Verifique el wiring técnico (REST, Camel, clientes externos, repositorios, seguridad).
Cumpla con los contratos expuestos (OpenAPI / REST).
### 8.1 Vista general
Estructura base sugerida:
src/test/java/com/compartamos/process/<recurso>/
├── domain/
├── application/              # Tests de casos de uso y servicios de aplicación (opcional pero recomendado)
├── infrastructure/           # Tests de adaptadores técnicos (opcional, según complejidad)
│   ├── rest/                 # Tests de controladores / adapters REST
│   ├── proxy/                # Tests de clientes externos
│   └── repository/           # Tests de repositorios, si aplica
├── route/                    # Tests de rutas Camel, si aplica
└── integration/              # Tests end-to-end / de integración
Donde <recurso> es el subdominio de negocio (por ejemplo, campaign, customer, payments).
Nota: los arquetipos pueden generar inicialmente solo domain/ e integration/ y dejar application/ e infrastructure/ como opcionales según el tipo de servicio.
### 8.2 domain/
Objetivo:
Probar la lógica de dominio de manera aislada, sin Quarkus, sin Camel y sin infraestructura.
Características:
Pruebas unitarias puras (JVM estándar, JUnit 5).
Sin anotaciones de Quarkus (@QuarkusTest, @Inject, etc.).
Sin acceso a base de datos, colas ni servicios externos.
Ejemplos de lo que se prueba aquí:
Reglas de negocio sobre entidades y agregados.
Comportamiento de Value Objects.
Generación de eventos de dominio.
Invariantes y validaciones internas del modelo.
### 8.3 application/ (opcional pero recomendado)
Objetivo:
Validar los casos de uso y servicios de aplicación, orquestando dominio e interfaces (ports) pero usando dobles de prueba para la infraestructura.
Características:
Se usan mocks o fakes (por ejemplo, Mockito) para:
Los outbound ports definidos en application/port/out (repositorios, clientes externos, colas, etc.).
Cualquier otra dependencia externa de la capa de aplicación.
No se levanta el runtime de Quarkus completo.
Se prueba la orquestación: qué se llama, en qué orden, con qué reglas.
Ejemplos:
GetCampaignsUseCaseTest
CreateCustomerUseCaseTest
ConsultarScorePersonaUseCaseTest
Estas pruebas aseguran que, incluso sin levantar el contenedor, la lógica de aplicación (use cases) se comporta como se espera, apoyándose únicamente en interfaces (port/in y port/out) y modelos de dominio.
### 8.4 infrastructure/ (según complejidad del servicio)
Carpeta opcional que organiza pruebas de componentes de infraestructura cuando es necesario ir un nivel más abajo que la integración completa.
infrastructure/
├── rest/
├── proxy/
└── repository/
Uso típico por subcarpeta:
rest/
Tests centrados en los controladores REST (pueden usar @QuarkusTest + RestAssured).
Validación de mapeo DTO externos ↔ modelos/DTO internos.
Manejo de códigos HTTP y errores (400, 404, 500, etc.).
Verificación de que los controladores invocan correctamente los input ports de application/port/in.
proxy/
Tests de clientes externos (REST Client, HTTP, SOAP, gRPC, etc.).
Verificación de serialización/deserialización.
Manejo de errores remotos (timeouts, códigos HTTP no exitosos, payload inválido).
Comportamiento de políticas de resiliencia aplicadas en los adaptadores (cuando se desee probarlas de forma aislada).
repository/
Tests de repositorios y acceso a datos (si el microservicio persiste información).
Verificación de consultas, mapeos a entidades de persistencia, índices clave, etc.
En muchos casos, estos escenarios también se pueden cubrir como tests de integración; la carpeta infrastructure/ se recomienda cuando la complejidad lo amerita.
### 8.5 route/ (tests de rutas Camel, si aplica)
Objetivo
Validar el comportamiento de las rutas Camel cuando el arquetipo genera microservicios de integración orientados a routing.
Características
Tests centrados en RouteBuilder y flujos Camel.
Pueden usar @QuarkusTest con el contexto de Camel levantado, o extensiones específicas de testing para Camel.
Se trabaja con endpoints Camel (direct:, timer:, kafka:, http4:, etc.), mensajes y headers.
Ejemplos de lo que se prueba aquí
Que al enviar un mensaje a un endpoint de entrada, se sigan las rutas correctas.
Transformaciones de mensajes (body, headers, propiedades de intercambio).
Enriquecimiento de mensajes antes de invocar un input port de application/port/in.
Manejo de errores y rutas de compensación, cuando apliquen.
Al igual que con REST, las rutas Camel no contienen lógica de negocio, solo orquestan y delegan en application/port/in. Los tests deben reflejar este principio.
### 8.6 integration/
Objetivo
Validar el funcionamiento end-to-end del microservicio (o de una parte representativa) levantando el runtime de Quarkus y, cuando aplique, las rutas Camel.
Características
Uso de @QuarkusTest.
Pruebas contra endpoints HTTP reales (por ejemplo, con RestAssured).
Validación de la integración entre capas:
infrastructure/rest → application/port/in → application/usecase → application/port/out → infrastructure/proxy / infrastructure/repository.
Verificación típica
Seguridad aplicada:
JWT válido / inválido.
Roles y permisos requeridos.
Trazabilidad:
Presencia y propagación de headers estándar (X-Trace-Id, X-Correlation-Id, X-Business-Id).
Resiliencia básica:
Timeouts.
Retries (cuando corresponda).
Propagación de errores técnicos y de negocio.
Observabilidad:
Health checks (/q/health o equivalente).
Métricas técnicas básicas.
Logs técnicos esperados (mínimo nivel de correlación y categorización).
Ejemplos de lo que se prueba aquí
GET /v1/campaigns devuelve 200 con el payload esperado.
Una llamada sin JWT o con JWT inválido devuelve 401/403.
Una ruta Camel principal se ejecuta correctamente cuando se envía un mensaje a un endpoint determinado, invocando el input port correspondiente y produciendo el resultado esperado.
### 8.7 Relación con los starters del Framework
Dado que los starters (exception-starter, security-starter, logging-starter, observability-starter, resilience-starter) ya incluyen su propia batería de pruebas a nivel de Framework, las pruebas en los proyectos generados por el arquetipo deben enfocarse en verificar la correcta integración, no en re-probar la lógica interna de los starters.
Objetivo de las pruebas en el microservicio
Verificar que el microservicio consume correctamente los starters, con configuración adecuada en application.properties / application.yaml.
Confirmar que:
Los errores se mapean al formato estándar definido por el Framework (códigos, estructura de error, trazabilidad).
Los filtros de seguridad y trazabilidad se aplican correctamente a los endpoints del servicio.
Las métricas básicas se exponen y los health checks responden en los endpoints estándar.
Las políticas de resiliencia se aplican donde corresponde (en adaptadores de infrastructure/proxy y infrastructure/repository que implementan application/port/out).
En resumen: los tests del microservicio confirman que el arquetipo y el equipo de desarrollo están usando bien el Framework, no que el Framework funcione internamente (eso ya está probado en los módulos starters).
### 9. Carpetas de despliegue y DevSecOps
Los arquetipos del Framework de Integración generan un proyecto con una estructura estándar orientada a:
Garantizar despliegues consistentes, auditables y seguros.
Asegurar que cada microservicio pueda ser operado en GKE on-prem (GDC).
Integrarse a los pipelines corporativos de build, análisis de seguridad, escaneo, firma y despliegue.
Facilitar el versionado de manifiestos y plantillas Helm uniformes.
Esta estructura también permite que el equipo de Plataforma evolucione los lineamientos sin romper los proyectos existentes.
### 9.1 k8s/
Carpeta que contiene los manifiestos Kubernetes estándar para desplegar el microservicio.
k8s/
├── deployment.yaml
├── service.yaml
├── configmap.yaml          (opcional)
├── networkpolicy.yaml
├── poddisruptionbudget.yaml
├── topologyspread.yaml
└── values.yaml             (si se usa Helm desde /helm)
Objetivo
Proveer los objetos Kubernetes mínimos y obligatorios para ejecutar el servicio en GDC con políticas de seguridad alineadas al marco corporativo.
Buenas prácticas obligatorias
Los arquetipos deben generar manifiestos que cumplan:
Seguridad
Containers non-root.
runAsNonRoot: true, readOnlyRootFilesystem: true.
Privilegios mínimos (seccomp y capabilities restrictivas).
NetworkPolicy obligatoria (solo puertos permitidos).
Resiliencia
livenessProbe, readinessProbe usando /q/health/*.
podDisruptionBudget configurado.
topologySpreadConstraints (cuando aplique).
Operabilidad
Etiquetas estándar: app, version, component, team.
Métricas disponibles vía /q/metrics.
Validación obligatoria
Los manifiestos deben ser validados con:
OPA/Conftest, usando las reglas corporativas ubicadas en ops/policy/.
Esto garantiza que ningún equipo despliegue configuraciones inseguras o no alineadas al estándar del banco.
### 9.2 helm/
Carpeta opcional cuando el microservicio se despliega mediante Helm Charts.
helm/
├── Chart.yaml
└── templates/
├── deployment.yaml
├── service.yaml
├── configmap.yaml
├── networkpolicy.yaml
└── _helpers.tpl
Objetivo
Estandarizar despliegues Helm para ambientes:
Preproducción
Producción
DR o ambientes especializados
El arquetipo puede incluir:
Plantillas de deployment, service, configmap, secret, networkpolicy.
Valores estándar en values.yaml.
Labels y anotaciones obligatorias (incluyendo trazabilidad de versión e imagen).
Los valores dinámicos (imagen, recursos, variables de entorno) deben ser sobreescritos por los pipelines.
### 9.3 ops/
Carpeta orientada a gobernabilidad operativa y automatización. Representa la “línea de defensa DevSecOps” para cada microservicio.
ops/
├── policy/
├── pipeline/
└── scans/
### 9.3.1 ops/policy/
Contiene reglas OPA/Conftest que aplican controles de seguridad, operación y cumplimiento regulatorio sobre los YAML en k8s/ y helm/.
Ejemplos de reglas:
Prohibir runAsRoot.
Validar límites y requests de CPU/memoria.
Verificar NetworkPolicy presente.
Impedir pullPolicy Always en producción.
Verificar readiness/liveness probes obligatorias.
Validar anotaciones de auditoría.
Objetivo:
Prevenir despliegues no conformes antes de llegar a los entornos gestionados.
### 9.3.2 ops/pipeline/
Incluye ejemplos y plantillas de pipelines recomendados para Azure DevOps o GitHub Actions:
build → test → SAST → SCA → build image → SBOM → container scan → firma → deploy
Debe incluir:
Análisis de calidad: SonarQube.
SAST: Veracode, Semgrep u otra herramienta aprobada.
SCA: OWASP Dependency Check, Trivy o Grype.
Escaneo de contenedor: Trivy / Grype / Prisma Cloud.
Firma de imágenes (Cosign).
Push al registry corporativo (ACR/GCR on-prem).
Deploy con Helm/Kubectl.
El arquetipo no incluye pipelines completos para no atar a un proveedor, pero sí ejemplos corporativos listos para copiar.
### 9.3.3 ops/scans/
Carpeta destinada a configuraciones específicas de herramientas de seguridad:
Ejemplos:
.trivyignore, trivy.yaml
grype.yaml
dependency-check.properties
Configuraciones para generar SBOM (CycloneDX)
Esto permite que cada microservicio mantenga configuraciones claras y versionadas de sus herramientas de análisis.
### 9.4 docker/
docker/
├── Dockerfile
└── docker-compose.yml (opcional)
Dockerfile
Generado siguiendo buenas prácticas:
Build multi-stage:
stage 1 → build Maven
stage 2 → runtime minimalista (distroless o ubi-micro)
Usuario no root.
Variables externas via ENV sin codificar secretos.
Healthcheck opcional para entornos locales.
docker-compose.yml (opcional)
Solo para entorno local y no destinado a producción.
Permite levantar:
Base de datos local (Postgres, SQL Server, etc.)
Kafka / Zookeeper
Servicios mock
Tempo / Prometheus / Grafana para debugging
### 10. BOMs y dependencias base del arquetipo
El Framework de Integración define un modelo de gestión centralizada de dependencias basado en dos niveles:
BOM Corporativo (corporate-bom)
Gestionado por Arquitectura y Plataforma, contiene las versiones aprobadas de los frameworks base.
Integration BOM (integration-bom)
Gestionado por el equipo del Framework de Integración.
Extiende el BOM corporativo añadiendo dependencias transversales como los starters y commons.
Los arquetipos NO declaran versiones en sus pom.xml; se limitan a referenciar artefactos sin versión, garantizando estandarización y evitando desviaciones.
### 10.1 BOM corporativo (corporate-bom)
Propósito
Centralizar las versiones oficiales aprobadas para:
Quarkus (versión base para servicios Java 21).
Camel Quarkus (componentes de integración).
Librerías de seguridad (JWT, OAuth2, MP-JWT).
Observabilidad (Micrometer, OpenTelemetry).
Logging (JBoss Logging, log JSON).
Testing (JUnit 5, Mockito, RestAssured).
El objetivo es asegurar que todos los microservicios del banco usen:
versiones homogéneas
extensiones compatibles entre sí
dependencias aprobadas por Seguridad y Arquitectura
base tecnológica estable para los entornos regulados
Contenido típico (referencial)
io.quarkus:quarkus-bom
org.apache.camel.quarkus:camel-quarkus-bom
org.slf4j:slf4j-api
com.fasterxml.jackson.core:jackson-databind
org.junit.jupiter:junit-jupiter
Nota: este BOM NO incluye los starters del framework, solo herramientas de la plataforma tecnológica.
### 10.2 Integration BOM (integration-bom)
Propósito
Extender el corporate-bom con:
Dependencias mínimas obligatorias para cualquier microservicio del Framework de Integración.
Los artefactos internos del framework:
Starters: exception, resilience, logging, observability, security.
Commons: utilitarios, DTOs compartidos, helpers.
Componentes incluidos
A. Extensiones Quarkus mínimas
Estas son obligatorias en cualquier microservicio del framework:
quarkus-rest
quarkus-rest-jackson
quarkus-smallrye-health
quarkus-micrometer
quarkus-micrometer-registry-prometheus
quarkus-opentelemetry
quarkus-logging-json
quarkus-smallrye-jwt
B. Extensiones Camel mínimas
Sujetas al tipo de servicio, pero incluidas por defecto:
camel-quarkus-http
camel-quarkus-kafka
camel-quarkus-direct
camel-quarkus-xml-io-dsl
C. Starters del Framework
Se incluyen sin versión:
exception-starter
logging-starter
observability-starter
resilience-starter
security-starter
Estos starters proporcionan:
Trazabilidad estándar
Logging JSON + MDC
Resiliencia configurable (timeouts, retries, circuit breaker)
Seguridad JWT + validación de headers
Manejo de errores estándar (RFC 7807 opcional)
D. Librerías comunes
Incluyen:
DTOs corporativos reutilizables
Validadores y utilitarios
Modelos de headers estándar (traceId, correlationId, businessId)
### 10.3 POM del proyecto generado
El pom.xml del microservicio debe:
Reglas obligatorias
Heredar del integration-bom
Esto garantiza que:
No se definan versiones manualmente
Todas las extensiones de Quarkus y Camel estén alineadas
Los starters estén correctamente versionados
Declarar únicamente las dependencias necesarias (sin versión)
Ejemplo correcto:
<dependency>
<groupId>com.compartamos.integration</groupId>
<artifactId>resilience-starter</artifactId>
</dependency>
No declarar versiones de:
Extensiones Quarkus
Extensiones Camel
Starters del Framework
Librerías transversales (logging, JSON, testing, seguridad)
El BOM se encarga de ello.
Solicitar cualquier actualización de dependencias a Plataforma
El proyecto individual no debe cambiar versiones.
Se garantiza la gobernabilidad del stack tecnológico.
Resumen
### 11. Arquetipo y Starters del Framework
### 11.1 Propósito de esta sección
Los arquetipos del Framework de Integración no implementan directamente las capacidades transversales (logging, seguridad, resiliencia, observabilidad, calidad).
En su lugar, consumen los starters oficiales del Framework, que encapsulan dichas capacidades en librerías reutilizables y versionadas de forma centralizada.
Esta sección define:
Qué starters se consideran comunes y obligatorios para todos los arquetipos.
Qué starters se aplican por tipo de arquetipo (REST, Camel, EDA).
Qué responsabilidades NO deben quedar en el arquetipo ni en el microservicio (para evitar duplicidad con los starters).
La relación entre arquetipo, starters y librerías commons.
Nota: Los nombres concretos de los starters (groupId/artifactId) se gestionan en el Integration BOM y pueden evolucionar; esta sección define el modelo y responsabilidades.
### 11.2 Starters comunes obligatorios (todos los arquetipos)
Independientemente del tipo de arquetipo (REST, Camel, EDA), todo microservicio generado debe consumir un conjunto de starters transversales proporcionados por el Framework de Integración:
exception-starter
Envelope estándar de errores (ApiError corporativo).
Mapeo uniforme de excepciones técnicas y de negocio.
Integración con el modelo de errores definido en los lineamientos (p. ej. RFC 7807, códigos corporativos).
logging-starter
Configuración de logs estructurados en formato JSON.
Uso de MDC para incluir traceId, correlationId, businessId, userId, etc.
Convenciones de categorías de log (técnico, negocio, auditoría) y patrones de salida homogéneos.
observability-starter
Integración con OpenTelemetry para trazas distribuidas.
Métricas técnicas (RED/USE) expuestas vía Micrometer/Prometheus.
Health checks estándar (liveness/readiness) integrados con Kubernetes.
resilience-starter
Integración con MicroProfile Fault Tolerance (@Timeout, @Retry, @CircuitBreaker, @Fallback).
Catálogo de políticas de resiliencia (ResiliencePolicies + ResiliencePolicyConfig).
Fallbacks corporativos (por ejemplo, EmptyList, PropagateAsUncheckedString).
Excepciones de resiliencia (ResilienceException) mapeadas a errores estándar.
security-starter (cuando aplique)
Filtros de autenticación/autorización basados en JWT/OAuth2.
Validación de headers de canal/usuario/aplicación según lineamientos de seguridad.
Integración con Mesh / mTLS / políticas L7 cuando corresponda.
quality-starter (orientado a pruebas y calidad)
Dependencias mínimas y configuraciones estándar para pruebas unitarias e integración (JUnit, Testcontainers, etc.).
Integración base con herramientas de calidad (coverage, reports para Sonar, etc.).
Regla general
El arquetipo únicamente:
Declara la dependencia a los starters (vía Integration BOM, sin versiones explícitas).
Ajusta la configuración mínima en application.properties necesaria para que arranquen.
El microservicio generado no debe replicar código ni configuración “dura” que ya proveen estos starters.
### 11.3 Starters para el arquetipo REST
El arquetipo REST genera microservicios centrados en APIs síncronas (HTTP/JSON) y se apoya en:
Starters comunes obligatorios
exception-starter, logging-starter, observability-starter, resilience-starter, security-starter (cuando aplique), quality-starter.
Responsabilidades cubiertas por los starters en el contexto REST
Resiliencia HTTP outbound
Uso de @ResilientHttpOutbound(<POLICY>) sobre servicios de aplicación que consumen APIs externas.
Aplicación de @Timeout, @Retry, @CircuitBreaker, @Fallback según lineamientos.
Trazabilidad y headers corporativos
Filtros estándar para lectura/propagación de traceId, correlationId, businessId.
Logs y trazas correlacionados extremo a extremo.
Manejo estándar de errores REST
Conversión uniforme de excepciones a ApiError con estructura corporativa.
Alineamiento con los lineamientos de contratos API (códigos HTTP, payload de error).
Qué NO debe hacer el arquetipo REST
No incluir en el proyecto implementaciones propias de filtros de logging, resiliencia o seguridad que dupliquen lo provisto por los starters.
No definir clases en cross/* que repliquen componentes del framework; cross/ se reserva para extensiones específicas del microservicio que no apliquen a todos los servicios.
### 11.4 Starters para el arquetipo Camel
El arquetipo Camel está orientado a integraciones, orquestaciones y patrones EIP. Además de los starters comunes, puede apoyarse en starters específicos de Camel.
Starters comunes obligatorios
Mismo set que el arquetipo REST: exception, logging, observability, resilience, security (si aplica), quality.
Starters específicos para Camel (nombres referenciales)
integration-camel-starter (o equivalente corporativo)
Provee una base estandarizada para rutas Camel:
BaseRouteBuilder o clase base corporativa.
Convenciones de naming de rutas (<dominio>.<capacidad>.<operación>.<vX>).
RoutePolicy corporativas para métricas, logging y resiliencia.
Manejo unificado de onException() y errores de integración.
Integra las métricas Camel con observability-starter (RED por ruta).
Integra resiliencia-starter para outbound HTTP/Kafka dentro de rutas.
messaging-starter (cuando Camel use mensajería)
DTOs de eventos comunes, convenciones de topics, configuración estándar de DLQ/Retry.
Serializadores/deserializadores corporativos.
Utilitarios para idempotencia y correlación de mensajes.
Qué NO debe hacer el arquetipo Camel
No definir RouteBuilder base duplicando lo que ya provea integration-camel-starter.
No reimplementar manejo de DLQ/Retry ni políticas de resiliencia que ya están en los starters.
No codificar nombres de topics, colas o patrones de DLQ/Retry que deban ser corporativos; estos deben centralizarse en configuración y/o commons.
### 11.5 Starters para el arquetipo EDA (Kafka / PubSub / RabbitMQ)
Cuando el arquetipo está orientado a arquitectura basada en eventos (EDA), el foco está en consumidores/productores de mensajes y flujos asíncronos.
Starters comunes obligatorios
exception-starter, logging-starter, observability-starter, resilience-starter, security-starter (si aplica), quality-starter.
Starters específicos recomendados
messaging-starter
DTOs corporativos de eventos (contracts estándar).
Convenciones de topics/queues (<dominio>.<evento>.<vX>, DLQ, retry topics).
Soporte para idempotencia y deduplicación.
Utilitarios para auditoría y trazabilidad de eventos.
integration-camel-starter (si se usa Camel como runtime EDA)
Rutas de consumo/producción estándar.
Manejo de errores, DLQ/Retry y métricas por ruta.
Responsabilidades típicas cubiertas por estos starters
Registro y propagación de traceId, correlationId, businessId en mensajes.
Configuración estándar de DLQ/Retry topics.
Métricas por consumidor/productor (rate, error rate, lag, etc.).
Integración con lineamientos de SAGA/Outbox e idempotencia.
### 11.6 Relación entre arquetipo, starters y commons
La siguiente tabla resume la relación de responsabilidades:
Regla final
El arquetipo se limita a:
Estructura,
wiring a BOM y starters,
configuración mínima.
Las capacidades transversales viven en los starters.
El microservicio solo agrega lógica de negocio, integraciones específicas y extensiones puntuales (en application, domain, infrastructure y, si hace falta, cross/ pero sin duplicar lo corporativo).
### 12. DTOs y contratos
Los arquetipos del Framework de Integración deben dejar explícito cómo se modelan y versionan los contratos expuestos, garantizando una separación clara entre dominio, transporte y persistencia.
A nivel de lineamientos de framework, se establece que:
Los contratos públicos (REST, eventos, mensajes) deben estar siempre modelados con DTO tipados, evitando el uso de Map<String, Object> u objetos genéricos.
Debe existir una separación estricta entre:
Modelo de dominio (domain.*).
Modelo de transporte / contratos (…infrastructure.rest.dto, …infrastructure.proxy.*.dto, application.dto si aplica).
Entidades de persistencia (…infrastructure.repository.*).
A nivel de arquetipo, se definen los siguientes patrones de ubicación:
DTO de contrato REST (expuestos vía OpenAPI)
Deben vivir en la capa de interfaces/adaptadores REST, por ejemplo:
com.compartamos.process.<recurso>.infrastructure.rest.dto
Son la única fuente de verdad de los modelos expuestos por la API (request/response).
DTO de sistemas externos (integraciones)
Deben agruparse por sistema bajo infrastructure.proxy, por ejemplo:
com.compartamos.process.<recurso>.infrastructure.proxy.<sistema>.dto
Representan el contrato tal como se habla con cada sistema externo (REST, SOAP, etc.).
DTO internos de aplicación (opcional)
Cuando se requiera una capa adicional de desacoplamiento, se pueden definir DTO internos en:
com.compartamos.process.<recurso>.application.dto
Se usan para orquestación dentro de la capa de aplicación, sin exponerse directamente al exterior.
Relación entre DTOs y Ports:
Puertos definen modelos internos NEUTRALES
Los DTO de transporte viven en infrastructure:
infrastructure/rest/dto
infrastructure/proxy/.../dto
Mappings:
REST DTO ↔ modelo interno: en rest controllers
DTO externo ↔ modelo interno: en adaptadores outbound
Puertos NO dependen de:
Request/Response HTTP
JSON
SOAP XML
Anotaciones Jackson
Reglas generales para los DTO:
Se recomienda el uso de record (Java 21) para simplificar modelos inmutables.
No deben incluir anotaciones de persistencia (JPA, etc.).
Pueden incluir anotaciones de validación (Bean Validation) cuando agreguen valor a los contratos.
Versionado de contratos REST
Los contratos expuestos deben versionarse explícitamente:
Por path: /v1/..., /v2/....
Opcionalmente, también por paquete:
infrastructure.rest.v1.dto, infrastructure.rest.v2.dto.
Un cambio rompiente en el contrato implica:
Nuevo path de versión y/o
Nuevo paquete de DTO versionado, manteniendo convivencias controladas entre versiones.
El detalle fino de contratos (OpenAPI obligatorio, versionado, linters, naming de topics/mensajes) se alinea con la sección 7. Lineamientos de contratos API (REST y eventos) del documento “Lineamientos y Estándares del Framework de Integración”, mientras que este apartado define dónde viven y cómo se organizan los DTO dentro de los proyectos generados por los arquetipos.
### 13. Resiliencia (visión estructural)
En alineación con el documento 2.5 Lineamientos de Resiliencia, el arquetipo define cómo y dónde deben aplicarse las políticas de resiliencia dentro de un microservicio generado. El objetivo es asegurar que todo servicio sea Resilient by default, utilizando patrones estándar y evitando implementaciones ad-hoc por cada equipo.
### 13.1 Ubicación de los componentes de resiliencia
Las responsabilidades se dividen en dos niveles:
1) cross/resilience/
Los microservicios no deben contener una carpeta local /cross/resilience/ ni implementar clases de resiliencia dentro del propio repositorio del servicio.
Toda la funcionalidad de resiliencia proviene del resilience-starter del Framework, que expone:
Anotaciones corporativas (@ResilientHttpOutbound, @CircuitBreakerPolicy, etc.).
Wrappers reutilizables para integraciones remotas (HTTP, Kafka, DB).
Políticas preconfiguradas de timeout, retry con jitter, circuit breaker y bulkhead.
Manejo estándar de fallbacks y excepciones técnicas.
Métricas y trazabilidad asociadas a resiliencia.
El servicio solo debe referenciar el starter en el pom.
Cuando el arquetipo habilita useResilience=true:
Se incluye la dependencia al resilience-starter.
Se generan únicamente ejemplos funcionales (por ejemplo, ResilienceDemoResource).
NO se crea la carpeta /cross/resilience/ en el proyecto.
Cuando useResilience=false:
No se incluyen ejemplos.
No se agregan propiedades adicionales.
Tampoco se genera /cross/resilience/.
En todos los casos:
Las carpetas /cross/ del proyecto están reservadas para componentes propios del microservicio, y nunca deben replicar código del framework (starters).
2) infrastructure/proxy/
Aquí viven los adaptadores de infraestructura que “hablan” con otros sistemas:
REST clients (MicroProfile RestClient o WebClient).
Conectores a colas, brokers, DBs u otros sistemas externos.
Es en esta capa donde los equipos deben aplicar explícitamente las políticas de resiliencia provistas por el framework, por ejemplo:
@Timeout
@Retry (incluyendo backoff exponencial y jitter)
@CircuitBreaker
@Bulkhead
Uso del wrapper:
resilientHttp.execute(() -> restClient.callExternal());
Esto asegura que todas las integraciones externas tengan resiliencia uniforme.
Resiliencia y Ports
Las políticas de resiliencia deben aplicarse ÚNICAMENTE en infrastructure:
RestClient
SOAP Client
Redis Repository
adaptadores
@Timeout, @Retry, @CircuitBreaker, @ResilientHttpOutbound
Nunca aparecen en application ni en los ports
Esto mantiene:
Domain + Application sin dependencias técnicas
Resiliencia estandarizada mediante nuestros starters
### 13.2 Reglas clave para aplicar resiliencia
La resiliencia nunca se implementa en la capa application ni domain.
→ Esto mantiene el diseño hexagonal limpio y sin dependencias técnicas.
Toda llamada remota realizada desde infrastructure/proxy debe ser resiliente.
→ Ningún cliente externo puede quedar sin política de protección.
Las configuraciones por defecto de los starters deben estar activas sin necesidad de código adicional:
Timeout conservador.
Retries con jitter.
Circuit breaker con contadores estándar.
Bulkhead para evitar saturación del pool de threads.
Los equipos solo pueden modificar parámetros de resiliencia a través de application.properties, nunca cambiando el comportamiento por defecto del starter.
### 13.3 Relación con los lineamientos
Este capítulo define la ubicación y responsabilidades de resiliencia dentro del arquetipo.
Los detalles técnicos (umbrales, política de backoff, configuraciones por tipo de integración, excepción estándar, naming de métricas, etc.) se documentan en:
Sección 5 — Lineamientos de Resiliencia, del documento “Lineamientos y Estándares del Framework de Integración”
### 14. Seguridad y headers
En alineación con la sección Lineamientos de Seguridad del documento “Lineamientos y Estándares del Framework de Integración”, la seguridad en los microservicios del Framework se implementa de manera centralizada mediante el componente reutilizable security-starter, que es incluido como dependencia Maven en cada proyecto.
El arquetipo no debe contener una carpeta cross/security/, ya que:
La lógica de seguridad no se copia en cada microservicio.
Todos los filtros, validaciones, anotaciones y mecanismos viven en el starter.
Esto garantiza estandarización y evita desviaciones o reimplementaciones por proyecto.
### 14.1 Seguridad provista por el security-starter
El security-starter incluye todos los componentes transversales necesarios para cumplir “Secure by default”, entre ellos:
1) Filtros de autenticación (autenticación obligatoria)
Implementados en el starter:
Validación de JWT RS256
Validación de:
issuer
expiración
firma
grupos/roles
Integración con MicroProfile JWT y Quarkus SmallRye JWT
Estos filtros no deben ser modificados ni copiados en los microservicios.
2) Filtros de autorización (roles y permisos)
Verificación de roles (groups en el JWT).
Anotaciones como @RolesAllowed, @DenyAll, @PermitAll.
Validación de permisos por recurso (si el modelo se extiende).
También provisto por el starter, no por el arquetipo.
3) Validación de headers mínimos
El security-starter valida automáticamente la presencia —y coherencia— de headers estándar:
X-User-Id
X-Channel-Id
X-Client-Id
(Opcional) X-Device-Id
Los microservicios no deben volver a implementar estos filtros.
4) Integración con OIDC/JWT
El starter incluye los mecanismos para:
Validación vía JWKS (si se habilita).
Token introspection (si aplica).
Selección de esquema de autenticación (Bearer/Token).
El arquetipo solo debe colocar plantillas de configuración en application.properties.
### 14.2 Relación con Observabilidad: TraceFilter
Aunque pertenece al observability-starter, el TraceFilter complementa la seguridad operacional aportando trazabilidad completa.
El TraceFilter:
Garantiza el ingreso/propagación de:
X-Trace-Id
X-Correlation-Id
X-Business-Id
Inyecta estos valores en MDC (para logs JSON)
Envía los traceId al span de OpenTelemetry
Importante:
El arquetipo no debe tener cross/observability. La trazabilidad también vive en el starter como dependencia.
### 14.3 Reglas generales de seguridad aplicables al arquetipo
El proyecto generado incluye por defecto el security-starter
Desde el pom.xml, sin copiar código.
No se permite lógica de seguridad en:
domain/
application/
infrastructure/rest (salvo anotaciones)
Los endpoints REST deben requerir autenticación, excepto:
/q/health
/q/metrics (si se configura privado vía gateway)
La seguridad debe configurarse exclusivamente vía application.properties
Nunca mediante hardcoding.
Todos los headers de trazabilidad y de seguridad forman parte del “contrato operativo”
Son validados por los starters, no por el microservicio.
### 15. Observabilidad
La observabilidad en los microservicios del Framework de Integración se implementa mediante el componente reutilizable observability-starter, que provee trazabilidad, métricas, logs estructurados y compatibilidad con OpenTelemetry, Prometheus y los lineamientos operativos del banco.
El arquetipo no debe incluir código en la carpeta cross/observability/.
Toda la lógica transversal de observabilidad se entrega como JAR a través del starter.
### 15.1 Componentes provistos por el observability-starter
El starter incluye de forma centralizada:
1) TraceFilter (trazabilidad obligatoria “by default”)
Este filtro es aplicado automáticamente a todos los endpoints y asegura:
Lectura de headers:
X-Trace-Id
X-Correlation-Id
X-Business-Id
Generación automática de IDs faltantes.
Propagación hacia:
MDC para logs JSON.
spans de OpenTelemetry.
llamadas outbound (REST Client, Camel, Kafka).
Inclusión de traceId y correlationId en:
logs
métricas
auditoría
eventos de dominio (si se propaga manualmente)
Este comportamiento no debe ser reimplementado en los microservicios.
2) Logging JSON estandarizado
El starter configura:
quarkus.log.console.json=true
Formato homogéneo con campos obligatorios:
timestamp
level
logger
message
traceId, correlationId, businessId
host, pod, thread
Integración con MDC automática.
Los microservicios no deben modificar el formato, solo extender categorías si es necesario.
3) Métricas
El starter habilita soporte estándar para:
Micrometer API
Prometheus Registry
Métricas técnicas (HTTP, JVM, GC, ThreadPools)
Métricas Camel (si aplica)
Métricas de negocio (opcional, vía anotaciones o beans)
Incluye:
/metrics del runtime Quarkus
Exportación Push/Pull según configuración operativa
El arquetipo solo debe exponer una estructura opcional donde los squads puedan:
registrar métricas de negocio
definir contadores o timers propios
4) Health Checks
El starter activa SmallRye Health:
/q/health/live
/q/health/ready
/q/health/started
Reglas:
Los microservicios no deben reescribir health checks base.
Solo agregar health checks específicos cuando exista dependencia externa crítica.
5) OpenTelemetry (traces distribuidos)
Habilitado desde el starter:
Exportación OTLP (gRPC y HTTP)
Integración automática con:
REST
REST Client
Kafka
Camel
JDBC / reactive SQL que se configure
El arquetipo únicamente provee el archivo base de configuración en application.properties.
### 15.2 Qué incluye el arquetipo (y qué no)
Incluye en el arquetipo
Estructura básica en application.properties:
configuración de Micrometer
configuración de OTel (endpoint OTLP)
configuración mínima de logging
Plantillas comentadas explicando:
qué se puede sobrescribir
qué dejar a los starters
No incluye en el arquetipo
Ningún filtro en cross/observability/
Ningún interceptor para trazas o logs
Ningún bean de métrica técnica
Ninguna lógica de serialización JSON
Todo esto es responsabilidad del observability-starter para asegurar comportamiento homogéneo en todos los microservicios.
### 15.3 Reglas operativas aplicables a todos los microservicios
Toda llamada REST inbound/outbound debe incluir traceId y correlationId
(propagado automáticamente por los starters).
Los logs deben seguir el formato JSON estándar
No se permite logging plano o manual sin el MDC.
Los microservicios deben exponer /q/health y /metrics sin modificación del contrato.
La instrumentación no debe ser redeclarada en el proyecto.
Si se necesita una métrica de negocio, se registra mediante:
anotaciones (@Counted, @Timed)
inyección del MeterRegistry
Para Camel
Las rutas están instrumentadas automáticamente.
El arquetipo puede agregar etiquetas de negocio vía exchange.getMessage().setHeader("businessTag").
### 15.4 Qué queda en el proyecto
Los equipos pueden agregar únicamente:
1) Métricas custom de negocio
Ejemplo (solo si se requiere):
@ApplicationScoped
public class CampaignMetrics {
private final Counter campaignsCreated;
public CampaignMetrics(MeterRegistry registry) {
this.campaignsCreated = Counter.builder("campaigns_created_total")
.description("Cantidad de campañas creadas")
.register(registry);
}
public void increment() {
campaignsCreated.increment();
}
}
2) Health checks específicos de dependencias críticas
Ejemplo: verificación de conexión a un sistema legacy.
Pero nunca health checks estándar — ya vienen desde el starter.
### 16. Configuración mínima (application.properties)
El arquetipo debe entregar un application.properties mínimo, funcional y estandarizado, alineado con los lineamientos del Framework y con los starters corporativos.
Este archivo sirve como punto de partida para cualquier microservicio, definiendo:
configuración base de Quarkus
configuración de observabilidad (OTel, Micrometer)
configuración de seguridad (JWT/OIDC)
configuración de resiliencia (timeouts globales opcionales)
parámetros que serán sobreescritos en los pipelines por entorno
estructura clara y comentada para facilitar adopción
Los microservicios NO deben redefinir configuración ya incorporada en los starters (logging, trazabilidad, filtros de seguridad, interceptores de resiliencia, métricas técnicas).
El objetivo es que este archivo sea simple, limpio, portable y 100% gobernable.
### 16.1 Estructura recomendada del archivo
El application.properties del arquetipo será generado con los siguientes bloques:
# ===================================================================
# 1. Configuración base de Quarkus
# ===================================================================
quarkus.application.name=${project.artifactId}
quarkus.http.port=8080
quarkus.http.host=0.0.0.0
# ===================================================================
# 2. Logging (provisto por logging-starter)
# ===================================================================
# JSON Logging activado en el starter, no redefinir.
# quarkus.log.console.json=true → viene desde logging-starter
# Solo agregar categorías opcionales:
# quarkus.log.category."com.compartamos".level=INFO
# ===================================================================
# 3. Observabilidad (provisto por observability-starter)
# ===================================================================
# --- OpenTelemetry export ---
quarkus.otel.exporter.otlp.endpoint=http://otel-collector:4317
quarkus.otel.resource.attributes=service.name=${project.artifactId}
# --- Micrometer / Prometheus ---
quarkus.micrometer.export.prometheus.enabled=true
# ===================================================================
# 4. Seguridad (provisto por security-starter)
# ===================================================================
# Clave pública / JWKS
mp.jwt.verify.publickey.location=META-INF/resources/publicKey.pem
mp.jwt.verify.issuer=compartamos-dev
# Header estándar para autorización
mp.jwt.token.header=Authorization
mp.jwt.token.cookie=
# ===================================================================
# 5. Resiliencia (provisto por resilience-starter)
# ===================================================================
# Timeouts globales opcionales (se sobrescriben por proxy o client).
# Se recomienda mantener estos valores como placeholders.
# quarkus.smallrye-fault-tolerance.timeout.default=2000
# ===================================================================
# 6. Camel (solo en arquetipos Camel)
# ===================================================================
# camel.context.name=${project.artifactId}
# camel.main.shutdown-timeout=10s
# ===================================================================
# 7. Kafka (solo en arquetipos EDA)
# ===================================================================
# kafka.bootstrap.servers=${KAFKA_BOOTSTRAP_SERVERS}
# mp.messaging.outgoing.events.topic=enterprise.v1
# ===================================================================
# 8. Configuración por entorno (sobreescrita por pipeline)
# ===================================================================
%dev.quarkus.datasource.jdbc.url=jdbc:h2:mem:test
%test.quarkus.datasource.jdbc.url=jdbc:h2:mem:test
%prod.quarkus.datasource.jdbc.url=${DB_JDBC_URL}
# ===================================================================
# 9. Configuración de negocio opcional
# ===================================================================
# campaign.external.api.url=${EXTERNAL_API_URL}
### 16.2 Principios aplicados
Secure by default
JWT obligatorio y validado por security-starter.
No se exponen endpoints sin seguridad salvo /q/health y /metrics.
Observable by default
Logging JSON, trazabilidad y spans OTel activados sin intervención del equipo.
Métricas técnicas disponibles desde el primer mvn quarkus:dev.
Resilient by default
Timeouts, retries y circuit breakers se aplican desde el resilience-starter.
El arquetipo solo provee placeholders si el squad necesita configurarlos.
Default configuration overrideable by pipeline
Propiedades sensibles (URLs, tokens, credenciales) no deben ir aquí.
Los pipelines (Azure DevOps) deben reemplazar variables vía:
variable groups
KeyVault (si aplica)
parámetros Helm
Nada de duplicación
Si algo ya está en un starter, NO va al application.properties.
### 16.3 Qué trae el arquetipo vs qué trae el framework
Esto asegura que todos los microservicios nacen con configuración homogénea, auditada, y alineada a estándares corporativos.
### 17. Calidad y pruebas
La calidad en los microservicios generados por el arquetipo debe ser predecible, medible y gobernada. Esta sección define qué tipos de pruebas, qué herramientas, y qué reglas de DevSecOps deben aplicarse desde el primer día, sin que cada squad tenga que “reinventar” cómo probar.
El objetivo es que todo microservicio generado por el arquetipo posea:
Calidad técnica mínima garantizada
Pruebas unitarias, de integración y de contrato listadas y con plantillas
Integración automática con SAST, SCA, SonarQube, SBOM y scanning
Estructura coherente con arquitectura hexagonal
Compatibilidad con pipelines corporativos
Todo esto se habilita mediante la estructura del arquetipo + los starters + el Integration BOM.
### 17.1 Estructura de pruebas
Los proyectos generados deben tener esta estructura base:
src/test/java/com/compartamos/process/<recurso>/
├── domain/          # Pruebas unitarias puras del dominio
├── application/     # Tests de casos de uso (sin infra real)
└── integration/     # Tests con QuarkusTest (REST, Camel, EDA)
### 17.1.1 domain/
Pruebas unitarias puras, sin framework, totalmente independientes de Quarkus.
Incluyen:
Validación de entidades y value objects
Reglas de negocio puras (Domain Services)
Generación de eventos de dominio
Estas pruebas deben ejecutarse rápido y con alta cobertura del dominio.
### 17.1.2 application/
Pruebas de casos de uso que:
no acceden a infraestructura real
mockean puertos definidos en domain.repository.*
validan reglas de aplicación, flujos, validadores, policies
Ejemplo:
GetCampaignsUseCaseTest, usando Mockito o similar.
### 17.1.3 integration/
Pruebas de integración completas, usando Quarkus:
@QuarkusTest
Pruebas a endpoints REST (RestAssured)
Validación del contrato OpenAPI
Pruebas básicas de resiliencia (Timeout, CircuitBreaker)
Pruebas de rutas Camel (si aplica)
Pruebas de mensajería (Kafka / PubSub / MQ)
Este nivel valida que la aplicación funciona de punta a punta en local.
### 17.2 Tipos de pruebas obligatorias
Unidad (Domain + Application)
Mínimo 60% del dominio cubierto.
Sin dependencias a frameworks.
Ejecución ultrarrápida.
Integración técnica
@QuarkusTest
Validación de endpoints REST
Pruebas de contrato API
El arquetipo debe incluir:
OpenAPI generado automáticamente
Test RestAssured validando:
status HTTP
esquema de respuesta
presence/shape de DTOs
Pruebas de resiliencia
Aplicadas a componentes proxy:
timeout configurado funciona
retry respeta backoff
circuit breaker abre / cierra apropiadamente
Pruebas de observabilidad
Que /metrics exponga métricas técnicas
Que /health tenga liveness + readiness
Que exista traceId en logs
Pruebas EDA/Kafka (si aplica)
Consumo de eventos
Publicación a topics
Formato JSON válido
Validación de claves (key/value)
### 17.3 Herramientas de testing incluidas (propuesta)
El Integration BOM incluye:
JUnit 5
Framework principal.
RestAssured
Pruebas a endpoints REST.
Mockito / MockK
Mocks para pruebas de aplicación.
QuarkusTest
Tests de integración con contenedor embebido.
Awaitility
Para pruebas reactivas o de mensajería.
Jackson assert / JSONAssert
Validación de contratos JSON.
Camel Test (si arquetipo Camel)
Validación de rutas DSL.
Testcontainers (opcional, activable)
Permitido solo para pruebas locales o pipelines específicos.
### 17.4 DevSecOps y calidad continua
Los arquetipos se generan listos para pipelines corporativos, integrando calidad en tres niveles:
### 17.4.1 SAST – Análisis estático de seguridad
El pipeline debe ejecutar:
SonarQube + SonarCloud
Veracode SAST (si se licencian módulos corporativos)
Validando:
vulnerabilidades de código
code smells
duplicidad
pruebas mínimas
debt técnico
El arquetipo entrega:
configuración sonar-project.properties mínima
umbrales por defecto heredados del framework
### 17.4.2 SCA – Análisis de librerías
Ejecutado en pipeline:
OWASP Dependency Check
Snyk o Veracode SCA
Objetivo:
detectar dependencias vulnerables (CVEs)
validar que las dependencias vienen del BOM
### 17.4.3 Análisis de contenedores
El pipeline debe escanear imágenes con (revisar con equipo DevOps):
Trivy
Grype
Aqua (si aplica)
Validación obligatoria para producción:
No CVE CRITICAL o HIGH
Imagen firmada (cosign) – opcional
### 17.4.4 SBOM (Software Bill of Materials)
Generado automáticamente en build:
con Steve, CycloneDX o syft
exportado para auditorías
### 17.4.5 Escaneo de infraestructura
Para k8s y Helm:
OPA/Conftest
Kube-score
Polaris
Validación de:
NetworkPolicy
PodDisruptionBudget
Non-root containers
Requests/limits
### 17.5 Integración en pipelines (ejemplo recomendado)
Pipeline estándar del arquetipo:
Build
mvn clean verify -DskipTests=false
SAST + calidad
SonarQube
Veracode SAST
Linters de contratos
SCA (Software Composition Analysis)
dependency-check
Veracode SCA
Unit + Integration tests
Reports JUnit
Coverage > mínimo
Container build
Jib o Dockerfile estándar
Container scan
Trivy / Grype
SBOM
syft
Push to Registry
imagen validada
Deploy a ambiente
Helm
Validación OPA/Conftest
### 17.6 Qué valida el “Definition of Done” en Calidad
Para un microservicio generado por el arquetipo:
### 17.7 Resultado esperado
Todo microservicio generado con el arquetipo debe:
compilar sin cambios
ejecutar pruebas desde el día 1
tener seguridad, trazabilidad y resiliencia activada automáticamente
integrarse sin fricción con Sonar, Veracode, Trivy y SBOM
cumplir estándares corporativos (auditoría, ciberseguridad, producción)
empezar a construir calidad desde la base, no después
### 18. Parametrización inicial de arquetipos (Velocity + Groovy)
La parametrización de los arquetipos del Framework de Integración Compartamos se realiza utilizando arquetipos Maven combinados con:
Plantillas Velocity para generar contenido dinámico en pom.xml, clases Java, application.properties y recursos de configuración.
Scripts Groovy de post-generación para ajustar la estructura final del proyecto (eliminar carpetas/archivos que no aplican según parámetros, renombrar elementos, etc.).
BOMs corporativos (corporate-bom e integration-bom) que centralizan las versiones de Quarkus, Camel, librerías de seguridad, observabilidad, testing y los starters del framework.
A nivel conceptual, la parametrización permite que, a partir de un único arquetipo estándar, se puedan generar diferentes tipos de servicios:
Servicios REST, de integración Camel o EDA/Kafka.
Proyectos con o sin ejemplos de rutas, endpoints de demo, conectividad a Kafka, etc.
Estructuras alineadas al modelo hexagonal (domain, application, infrastructure, cross) con las carpetas opcionales activadas o eliminadas según los parámetros de generación.
De forma resumida, la parametrización cubre:
# Definición de parámetros del arquetipo
Declaración de propiedades en archetype-metadata.xml (por ejemplo: subdomainName, serviceType, enableKafka, generateDemoEndpoints).
Selección de archivos que serán filtrados con Velocity (POM, properties, clases de ejemplo, manifiestos k8s/Helm).
Generación dinámica con Velocity
Inclusión condicional de dependencias (REST, Camel, Kafka, etc.) en el pom.xml.
Generación de paquetes, nombres de clases y configuraciones según el subdominio y tipo de servicio.
Activación/ desactivación de bloques de configuración (application.properties, métricas, mensajería, etc.) según parámetros.
Ajustes estructurales con Groovy
Eliminación de carpetas o clases de ejemplo que no aplican al servicio generado.
Limpieza de rutas, beans y recursos técnicos según capacidades habilitadas (por ejemplo, eliminar route/ si el servicio no es tipo Camel).
Generación y validación de proyectos
Uso de mvn archetype:generate en modo interactivo o automatizado (para pipelines).
Validación mínima del proyecto generado con mvn clean verify -DskipTests y arranque con mvn quarkus:dev para asegurar que:
La estructura respeta la guía de arquetipos.
Las dependencias se resuelven correctamente vía BOMs corporativos.
No quedan marcas sin resolver de Velocity o errores en el script Groovy.
La descripción técnica de la configuración, parametrización y troubleshooting de los arquetipos (incluyendo ejemplos de archetype-metadata.xml, plantillas Velocity y scripts Groovy) se encuentra en el documento: “Guía configuración y parametrización del arquetipo”.
### 19. Documentación (README.md y otros)
Todo proyecto generado a partir de los arquetipos del Framework de Integración debe incluir un archivo README.md en la raíz del repositorio, siguiendo la plantilla corporativa definida para servicios middleware en Quarkus + Camel.
El objetivo del README es:
Servir como punto de entrada único para entender el servicio (qué hace, cómo se ejecuta, cómo se despliega).
Facilitar el onboarding de nuevos desarrolladores, QA, DevOps y arquitectura.
Entregar información mínima y verificable para construcción, operación, soporte y auditoría.
### 19.1 Contenido mínimo esperado
El arquetipo debe garantizar que el proyecto generado incluya, al menos, las siguientes secciones en el README.md:
Nombre, tipo, versión y autor/es
Nombre oficial del servicio, tipo (microservicio, librería, adaptador), versión SemVer y equipo responsable.
Descripción general
Propósito técnico del servicio, dominio funcional al que pertenece y runtimes clave (Java, Quarkus, Camel).
Arquitectura del microservicio
Diagramas (idealmente Mermaid) de componentes y, cuando aplique, de secuencia principal.
Relación con otros sistemas, colas, bases de datos y IAM.
Dependencias técnicas
Frameworks y librerías principales (Quarkus, Camel, starters del framework).
Integraciones externas (sistemas, colas, topics, bases de datos) con una descripción de alto nivel.
Configuración y propiedades
Principales propiedades de application.properties y su mapeo a variables de entorno / ConfigMaps / Secrets.
Endpoints de health, readiness y métricas.
Estructura del proyecto
Resumen del árbol de carpetas (domain, application, infrastructure, cross, k8s, helm, ops, docker, etc.).
Ubicación de rutas Camel, controladores REST, pruebas, manifiestos k8s/Helm y Dockerfiles.
Endpoints REST principales
Tabla con método, path versionado, descripción, autenticación y códigos de respuesta principales.
Enlace a la especificación OpenAPI expuesta por el servicio.
Configuración y despliegue (Docker / Helm / k8s)
Comandos de build y ejecución local.
Nombre de imagen, tags esperados y variables requeridas.
Referencia a chart Helm y parámetros mínimos para entornos estándar.
Pruebas y calidad
Tipos de pruebas (unitarias, integración, contrato), herramientas y comandos básicos (mvn o equivalentes).
Criterios mínimos de calidad (cobertura, análisis estático, etc.) y relación con pipelines CI/CD.
Equipo responsable y canales de soporte
PO, TL, dev principal, QA, DevOps (según aplique) y canales de contacto.
Historial de cambios
Tabla con versión, fecha, descripción del cambio y referencia a PR/issue cuando aplique.
En todos los casos se debe respetar:
No exponer secretos ni datos sensibles en texto plano (usar placeholders).
Mantener el README alineado con las “fuentes de verdad”: OpenAPI, charts Helm, pipelines, ADRs, etc.
Actualizar el README siempre que haya cambios relevantes en contratos, seguridad, despliegue, arquitectura u observabilidad.
### 19.2 Rol del arquetipo frente al README
El arquetipo debe generar un README.md inicial ya estructurado con todas las secciones obligatorias, ejemplos mínimos y enlaces genéricos.
Cada equipo deberá completar y adaptar el contenido al caso de uso específico del servicio, manteniendo la estructura estándar.
Las evoluciones posteriores del servicio (nuevos endpoints, cambios de autenticación, migraciones de runtime, etc.) deben quedar reflejadas en el README y en su historial de cambios.
### 19.3 Referencia al documento maestro de README
El detalle completo sobre:
Plantilla oficial de README.md.
Responsabilidades por rol (Dev, TL, Arquitectura, QA, DevOps, Seguridad, etc.).
Matriz RACI por sección.
Reglas de estilo, ejemplos de diagramas Mermaid y criterios de validación en CI/CD.
se encuentra descrito en el documento “Guía para la elaboración del README.md”
### 20. Definición de “Done” del arquetipo
El Definition of Done (DoD) establece los criterios mínimos que deben cumplirse para considerar que un arquetipo del Framework de Integración está completamente listo para ser utilizado por los equipos de desarrollo.
Todo arquetipo debe cumplir con estándares de calidad, alineamiento arquitectónico, trazabilidad y reproducibilidad corporativa.
La siguiente DoD se aplica tanto a arquetipos funcionales (ej. servicio REST + Camel) como a arquetipos base del Framework (ej. integración, microservicio simple, adaptador, consumer Kafka, etc.).
### 20.1. Compilación, estructura y generación
El arquetipo compila correctamente (mvn clean install) sin warnings graves.
Genera un proyecto funcional, ejecutable con mvn quarkus:dev.
No quedan marcadores Velocity sin resolver (#if, #end, ${var}).
El script archetype-post-generate.groovy ejecuta correctamente y aplica todas las exclusiones/ajustes definidos.
Solo se generan carpetas y clases asociadas a parámetros activados (Resilience, Observability, Camel route, etc.).
La estructura del proyecto generado cumple lo definido en las secciones 5, 6, 7 y 8 del presente documento.
### 20.2. BOMs y dependencias
El arquetipo consume las versiones oficiales del corporate-bom e integration-bom.
El proyecto generado no declara versiones explícitas de dependencias que ya están gestionadas por los BOMs.
No se incluyen dependencias obsoletas, duplicadas o inconsistentes con los lineamientos del Framework.
Si se usan starters del Framework (exception, logging, observability, resilience, security), estos se incluyen de forma correcta.
### 20.3. Cumplimiento de lineamientos técnicos del Framework
Observabilidad by default
TraceFilter operativo y propagación de headers implementada.
Métricas expuestas vía Micrometer/Prometheus.
Tracing OTLP configurado.
Seguridad by default
Integración JWT/OIDC habilitada según el tipo de arquetipo.
Validación de headers mínimos y políticas base.
Resiliencia by default
Arquitectura preparada para usar @Timeout, @Retry, @CircuitBreaker, @Bulkhead.
Si el parámetro useResilience=true, se generan wrappers y ejemplos funcionales.
Estandares de arquitectura y naming
Paquetes y carpetas alineados al estándar process-or-experience → domain → application → infrastructure → cross.
Paths REST versionados (/v1/...).
### 20.4. Validación del proyecto generado
El proyecto generado con parámetros básicos:
mvn archetype:generate -DarchetypeCatalog=local
arranca sin errores con:
mvn quarkus:dev
El proyecto generado con parámetros avanzados (resilience, observability, camel, kafka, etc.) funciona y solo incluye las capacidades seleccionadas.
El comando de validación finaliza correctamente.
mvn clean verify -DskipTests
Los endpoints de ejemplo responden correctamente:
/q/health
/q/openapi
/demo/... (si aplica en el arquetipo)
### 20.5. README y documentación mínima
El arquetipo genera un README.md inicial basado en la plantilla estándar.
El README contiene todas las secciones obligatorias (sección 18).
El README incluye placeholders claros y no contiene secretos.
Se referencia el documento corporativo: “Guía para la elaboración del README.md”.
### 20.6. Calidad de código y estándares
Pasa linters aplicables (spotless/checkstyle si están habilitados).
No contiene código muerto, duplicado o sin uso.
No quedan imports o clases generadas por error desde el arquetipo base.
Los nombres de archivos .java coinciden con los nombres de las clases.
### 20.7. Alineamiento con DevSecOps
Incluye carpetas estándar:
/k8s/
/helm/
/ops/policy/
/ops/pipeline/
/docker/
Manifiestos Kubernetes sintácticamente correctos.
Dockerfile compilable y probado (build multi-etapa).
Pipeline de ejemplo funcional (Azure DevOps / GitHub Actions).
### 20.8. Revisión y aprobación corporativa
Revisión por roles:
Arquitectura
Seguridad
DevOps
QA
Leader Técnico del Framework
La versión del arquetipo está registrada en SemVer.
Publicado en el repositorio Maven interno (Artifactory/Azure Artifacts).
Documentado en el catálogo corporativo del Framework de Integración.
### 20.9. Usabilidad real
Al menos un equipo ha podido generar y levantar un servicio funcional usando el arquetipo.
Se han corregido issues de primera generación.
El arquetipo puede ser utilizado en pipelines CI/CD reales.
# Definición final
El arquetipo se considera DONE cuando:
Genera proyectos funcionales, escalables, seguros y observables alineados al Framework de Integración, sin intervención manual adicional, con documentación mínima y capacidad comprobada de despliegue en los entornos corporativos.