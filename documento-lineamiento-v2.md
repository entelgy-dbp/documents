Lineamientos y Estándares del Framework de Integración
Historial de las Revisiones
Las siguientes tablas describen la historia de modificación del documento para propósitos de seguimiento.
Únicamente los cambios que produzcan una nueva versión deberán ser mostrados en estas tablas.
# Resumen Ejecutivo
Este documento define los fundamentos, estándares y reglas corporativas que deben seguir todos los microservicios e integraciones desarrollados dentro del ecosistema tecnológico del banco. Su objetivo es asegurar interoperabilidad, seguridad, resiliencia, trazabilidad, calidad y mantenibilidad, garantizando una capacidad de integración homogénea y sustentable en el tiempo.
Propósito general del Framework
El Framework establece una base tecnológica y metodológica única para:
construir microservicios,
diseñar integraciones,
modelar dominios,
y publicar APIs corporativas,
bajo lineamientos uniformes, probados y alineados a la arquitectura empresarial del Banco.
Busca eliminar soluciones aisladas, duplicación de código, fragilidad operativa y riesgos de seguridad, promoviendo componentes reutilizables, buenas prácticas y automatización por defecto.
1. Principios rectores
El documento define once principios estratégicos obligatorios:
Reutilización por capacidades: los servicios y componentes se diseñan para ser compartidos transversalmente.
Seguridad by design (Zero Trust): la protección de información y acceso es mandataria desde la concepción.
Arquitectura Hexagonal + DDD + BIAN: se exige diseño centrado en el dominio, alineado a los service domains BIAN y desacoplado de infraestructura.
Clean Code y estándares corporativos: las soluciones deben ser legibles, mantenibles, probadas y alineadas al catálogo de patrones del banco.
Resiliencia por defecto: timeouts, circuit breakers, fallbacks e idempotencia deben ser implementados desde el arquetipo.
Trazabilidad y auditoría end-to-end: cada request debe ser rastreable de extremo a extremo.
Observabilidad nativa: logs estructurados, métricas y traces son obligatorios.
12-Factor y entregabilidad reproducible: los servicios deben poder desplegarse de forma consistente en cualquier ambiente.
DevSecOps nativo: validaciones automáticas de calidad, seguridad y compliance en CI/CD.
Diseño de APIs y contratos estables: OpenAPI/AsyncAPI como source of truth y versionamiento explícito.
Gobernanza de datos: definiciones estándar, consistencia semántica y cuidado del dato como activo.
2. Modelado de dominio
El framework exige un modelado basado en:
DDD, con dominios claros, entidades, value objects y agregados.
Arquitectura Hexagonal, separando núcleo de negocio de infrastructure.
Lenguaje ubicuo, reglas de negocio accionables y documentadas.
Los casos de uso se implementan en la capa application, las integraciones mediante ports, y los adaptadores externos en infrastructure. Se establecen reglas explícitas para mapping, invariantes, pruebas, alineamiento BIAN, estructura de paquetes y documentación.
3. Seguridad y secretos
Se demandan controles corporativos:
Autenticación/Autorización OAuth2/JWT
mTLS con Service Mesh
gestión de secretos mediante GSM y KMS
políticas de autorización mínimas por tipo de servicio
cumplimiento estricto de regulaciones y auditorías
La seguridad es transversal e indelegable, y todos los desarrollos deben cumplir Zero Trust.
4. Observabilidad
El framework incorpora:
identificación estándar (traceId, correlationId, businessId)
propagación de contexto hacia servicios externos
logs estructurados con categorías técnicas, de negocio y auditoría
manejo unificado de errores
métricas y health checks operativos
auditoría transaccional
La observabilidad es parte nativa del diseño, no un agregado.
5. Resiliencia
Todos los servicios deben incluir:
timeouts,
políticas de retry,
circuit breaker,
fallbacks corporativos,
interceptor @ResilientHttpOutbound para llamadas externas,
idempotencia y manejo de DLQ/Retry topics,
políticas explícitas de cuándo NO reintentar.
La resiliencia es obligatoria, centralizada y estandarizada.
6. Calidad, testing y DevSecOps
El framework define estándares mínimos:
cobertura de pruebas,
unit e integration tests,
uso de Testcontainers,
contract testing,
pipelines con gates para SAST, SCA, DAST,
reglas CI/CD corporativas.
Ningún servicio pasa a producción sin validación automática.
7. Contratos API (REST y eventos)
Todo API REST debe tener:
OpenAPI como source of truth,
versionamiento explícito,
linters automáticos,
testing de contratos.
Para EDA se exige AsyncAPI, estandarización de tópicos y versionamiento compatible.
Los contratos son gobernados, publicados y validados desde pipeline.
8. Arquetipos, commons y starters
Se definen:
Cuándo usar REST/Camel/EDA/DB,
starters obligatorios (exception, logging, resilience, observability),
reglas de dependencia usando Integration BOM,
criterios para extensiones,
lineamientos para publicar nuevos componentes.
El arquetipo del framework es la “fuente única” de servicios y garantiza consistencia.
9. Checklists de cumplimiento
Se establecen listas obligatorias según tipo de servicio:
REST,
Camel,
EDA,
y reglas de validación mediante pipeline y revisión técnica.
Estas checklists permiten asegurar que todo desarrollo cumple los estándares corporativos antes de pasar a QA o producción.
Conclusión
Este documento establece el marco integral de cómo diseñar, construir, operar y gobernar las soluciones de integración del banco. Conecta metodología, arquitectura empresarial (BIAN), dominio funcional, seguridad, observabilidad, resiliencia, modelado, pruebas y DevSecOps.
El objetivo final es garantizar interoperabilidad, estandarización, calidad, seguridad operativa, trazabilidad, y un modelo sostenible de evolución tecnológica, asegurando que cada servicio creado sea robusto, reutilizable, auditable y alineado a la estrategia del Banco.
# 1. Principios rectores del Framework de Integración
Los principios rectores definen las “reglas de juego” no negociables del Framework de Integración. Aplican a todos los servicios e integraciones generados con el framework (REST, Camel, EDA, DB, batch) y garantizan que cumplan criterios uniformes de reutilización, seguridad, trazabilidad, resiliencia, observabilidad, calidad y despliegue reproducible, alineados con la estrategia DevSecOps y la plataforma corporativa de contenedores/Kubernetes (incluyendo GKE on-prem / GDC u otras variantes).
Cada principio se describe con:
Objetivo: qué problema resuelve o qué valor protege.
Lineamientos de cumplimiento: cómo el framework obliga o facilita su aplicación.
Indicadores de verificación: qué evidencias concretas se revisan en código, pipelines o runtime.
## 1.1. Reutilización orientada a capacidades
Objetivo
Acelerar el time-to-market y reducir deuda técnica mediante el uso sistemático de componentes compartidos y estandarizados, evitando “microframeworks” paralelos.
Lineamientos de cumplimiento
Uso obligatorio de librerías commons (DTOs, error-envelope, headers, utilitarios) y starters (logging, seguridad, resiliencia, observabilidad, calidad) publicados por el framework.
Consumo de dependencias exclusivamente a través del Integration BOM (o BOM corporativo), sin versiones “a dedo”.
Selección de arquetipos por tipo de capacidad:
REST (APIs síncronas),
Camel (integraciones y EIPs),
EDA Kafka/PubSub/RabbitMQ,
DB (servicios centrados en persistencia),
otros que se definan.
Indicadores de verificación
El pom.xml declara el BOM de integración/corporativo como dependencyManagement.
El servicio usa ≥ 1 módulo commons y ≥ 1 starter del framework.
Nivel de duplicidad de código entre servicios ≤ 5% (analizado vía Sonar o equivalente).
El README del proyecto referencia el catálogo oficial de componentes del framework.
## 1.2. Seguridad por diseño (Zero Trust)
Objetivo
Prevenir, detectar y responder a amenazas desde el diseño, garantizando confidencialidad, integridad y trazabilidad de la información en todo el ciclo de vida.
Lineamientos de cumplimiento
Todos los servicios expuestos deben usar OAuth2/JWT para autenticación y autorización basada en roles/scopes.
Zero Trust:
mTLS entre servicios (Mesh / Service Mesh),
políticas L7 para controlar tráfico entrante/saliente,
principio de menor privilegio en RBAC, permisos de servicio y acceso a datos.
Gestión de secretos exclusivamente mediante:
Google Secret Manager (GSM) u otro vault corporativo,
KMS para cifrado y rotación de claves.
Imágenes de contenedor:
ejecución non-root,
base aprobada por Golden Images corporativas.
Pipeline con:
SBOM,
análisis SAST/SCA (p.ej. Veracode o Prisma),
container scan,
firma de artefactos.
Indicadores de verificación
Endpoints protegidos por roles/scopes explícitos en configuración o anotaciones.
Dockerfile y values.yaml sin usuarios root ni secretos en texto plano.
Existe SBOM generado y no se despliegan imágenes con vulnerabilidades críticas abiertas.
Contenedor ejecutándose con usuario no root y políticas de red aplicadas (NetworkPolicy / Mesh).
## 1.3. Arquitectura Hexagonal + DDD + BIAN como estándar
Objetivo
Separar completamente las reglas de negocio de los detalles técnicos, alineando el modelo funcional del banco (BIAN) con DDD y Arquitectura Hexagonal.
El propósito es garantizar que la evolución del negocio no esté condicionada por tecnologías, protocolos o mecanismos de integración, asegurando mantenibilidad y trazabilidad clara entre dominios, procesos y capacidades del Banco.
## 1.3.1 Lineamientos de cumplimiento
1. Modelo por capas obligatorio
Todo microservicio generado por los arquetipos corporativos debe respetar las siguientes capas:
Dominio (domain/):
Entidades
Aggregates
Value Objects
Eventos de dominio
Servicios de dominio
Reglas e invariantes
Lenguaje Ubicuo alineado a BIAN
No contiene puertos. No depende de infraestructura ni frameworks.
Aplicación (application/):
Casos de uso (application/usecase)
Orquestación del dominio
Puertos:
application/port/in → casos de uso expuestos
application/port/out → dependencias externas requeridas
No contiene lógica técnica. No usa RestClient, JPA, Redis, Kafka, Camel, ni DTO externos.
Infraestructura (infrastructure/, route/):
Adaptadores que implementan puertos de salida (port/out):
clientes HTTP/SOAP
repositorios de datos
mensajería, colas, caches
Adaptadores de entrada que invocan puertos de entrada (port/in):
REST Controllers
Camel Routes
Event Consumers
Jobs/Schedulers
Solo orquestan la invocación y el mapeo técnico. Nunca contienen reglas de negocio.
Config (config/):
Configuración del runtime, serialización, CORS, seguridad, JWT, trazabilidad, error-handling, timeouts globales, etc.
Sin lógica de negocio ni lógica técnica propia del microservicio.
2. Uso de BIAN para organización funcional
Los proyectos deben mapearse a dominios y service domains BIAN.
El subdominio <recurso> del proyecto (ej. campaign, credit, customer) debe alinearse con el bounded context.
El lenguaje ubicuo debe reflejar BIAN y los conceptos del negocio real.
## 1.3.2 Restricciones de dependencias (NO permitido)
El Dominio NO depende de:
application
infrastructure
route
cross
frameworks (Camel, Quarkus, JPA, Redis, Kafka, OTEL)
Application NO depende de:
infrastructure
frameworks técnicos
DTO REST, Response HTTP, códigos, anotaciones técnicas
Solo depende de:
domain
application/port/in
application/port/out
Infrastructure NO contiene reglas de negocio
Implementa cómo se accede a recursos externos y cómo se expone el servicio.
Las rutas Camel NO están en application
Van en route/, como adaptadores de entrada, e invocan exclusivamente puertos de entrada (application/port/in).
REST Controllers nunca llaman casos de uso concretos
Siempre llaman interfaces de application/port/in.
Resiliencia no va en domain ni application
Solo en adaptadores outbound de infraestructura.
## 1.3.3 Principios arquitectónicos clave
1. Dominio puro e independiente
sin puertos
sin infraestructura
sin anotaciones técnicas
sin DTO ni modelos de transporte
sin clientes HTTP, Redis, JDBC
2. Puertos solo en application
application/port/in: lo que el servicio expone
application/port/out: lo que el servicio necesita de afuera
3. Infraestructura implementa los puertos
ExperianHttpAdapter implements ExperianPort
RedisClaimsRepository implements ClaimsPort
4. Inbound adapters llaman input ports
REST
Camel
Messaging
Scheduler
## 1.3.4 Indicadores de verificación
Dependencias entre capas
Herramienta de análisis estático (SonarQube) valida que:
domain no depende de application, infrastructure ni frameworks
application no depende de infrastructure
route/ y rest/ solo dependen de application/port/in
Reporte de violaciones en el pipeline.
Pureza del Dominio
Ninguna clase importa Quarkus, Camel, RestClient, DTO REST, orquestadores, DAOs, JPA.
Validación con reglas ArchUnit:
noClasses()
.that().resideInAPackage("..domain..")
.should()
```java
.dependOnClassesThat().resideInAPackage("..quarkus..");
```
Pruebas unitarias del Dominio
Tests en domain ejecutan sin levantar contexto Quarkus/Camel.
Indicador: tiempo de ejecución < 1s
Sin anotaciones: @QuarkusTest, @Inject, @ApplicationScoped.
Cumplimiento de puertos y adaptadores
Interfaces de puertos definidas en domain o application.
Ports solo en application/port/in y application/port/out
Adaptadores en:
infrastructure/rest
infrastructure/proxy
infrastructure/repository
infrastructure/adapters
Naming convencional:
*Port
*Adapter
Separación de lógica
Ningún adaptador contiene lógica de dominio
Ningún caso de uso contiene detalles técnicos
Baja complejidad ciclomática en application (indicador de orquestador puro)
Documentación
Todo proyecto debe incluir en README:
Diagrama de capas
Mapa de bounded contexts y alineamiento BIAN
Ubicación de ports y adapters
El pipeline valida la existencia del bloque de arquitectura.
Cobertura mínima en Dominio
≥ 80% de cobertura en domain
Tests unitarios que prueban reglas de negocio puras
Evaluado automáticamente por SonarQube.
## 1.4. Clean Code y estándares corporativos
Objetivo
Promover legibilidad, mantenibilidad y consistencia en todo el ecosistema de servicios e integraciones.
Lineamientos de cumplimiento
Aplicación de principios SOLID y SRP (Single Responsibility Principle).
Nombres expresivos para clases, métodos y variables, alineados a lenguaje ubicuo DDD.
Uso de formatters/lints estandarizados (Java, YAML, Helm, etc.).
Presencia obligatoria de:
README con forma de uso, configuración, endpoints, diagramas,
diagramas arquitectónicos (C4 nivel sistema/contenedor/componente) idealmente en Mermaid o draw.io gestionado.
Checklist de Pull Request con puntos mínimos:
tests,
seguridad,
documentación,
impacto en contratos.
Indicadores de verificación
El código pasa las reglas de lint/format sin errores.
Cobertura de pruebas del dominio ≥ 80%.
README actualizado con:
descripción del servicio,
dependencias clave,
diagramas,
cómo correr local / en entorno de prueba.
Revisiones de código (PR) incluyen checklist de estándares completado.
## 1.5. Resiliencia e idempotencia por defecto
Objetivo
Garantizar continuidad operativa ante fallas parciales (red, dependencias externas, picos de carga) y evitar efectos duplicados en operaciones críticas.
Lineamientos de cumplimiento
Aplicación sistemática de patrones de tolerancia a fallas:
Timeouts en todas las llamadas remotas,
Retries con backoff + jitter, solo en errores recuperables,
Circuit Breakers en integraciones inestables,
Bulkheads / límites de recursos donde aplique.
Manejo estándar de DLQ / Retry Topic en mensajería.
Uso de patrones SAGA / Outbox para consistencia eventual entre servicios.
Implementación de idempotencia en operaciones mutantes (POST/PUT que puedan reintentar).
Indicadores de verificación
Configuración de resiliencia visible (propiedades, anotaciones, RoutePolicies, etc.).
Operaciones mutantes aceptan algún tipo de Idempotency-Key o clave de negocio única.
Pruebas de reintento demuestran ausencia de efectos duplicados.
Existen métricas de errores, reintentos, circuitos abiertos y mensajes en DLQ.
## 1.6. Trazabilidad y auditoría end-to-end
Objetivo
Permitir el seguimiento completo de cada transacción técnica y de negocio a lo largo de todos los componentes del ecosistema.
Lineamientos de cumplimiento
Propagación de W3C Trace Context u otro estándar soportado de trazas distribuidas.
Uso de MDC para loguear siempre:
traceId
correlationId
businessId
y otros campos relevantes (userId, canal, etc.).
Registro de eventos de auditoría para operaciones sensibles, con:
quién,
qué,
cuándo,
desde dónde,
resultado.
Indicadores de verificación
Logs JSON incluyen traceId, correlationId, businessId de forma consistente.
Spans visibles en la plataforma de observabilidad (por ej. Tempo, Jaeger, Dynatrace, etc.) enlazan todo el recorrido.
Auditorías clave disponibles (por evento o en data store) para reconstruir una transacción ante incidentes o requerimientos regulatorios.
## 1.7. Observabilidad por defecto
Objetivo
Asegurar visibilidad proactiva del comportamiento, degradaciones y errores de los servicios de integración, facilitando la operación y la mejora continua.
Lineamientos de cumplimiento
Exposición de:
endpoints de health/readiness integrados con Kubernetes (/q/health, /q/health/ready o equivalentes),
métricas técnicas y de negocio vía Micrometer/Prometheus u otro estándar corporativo,
logs estructurados con contexto de trazabilidad.
Trazas distribuidas con OpenTelemetry u otro SDK compatible.
Dashboards estándar por tipo de servicio (REST, Camel, EDA) con métricas de:
RED (Rate, Errors, Duration),
USE (Utilization, Saturation, Errors).
Indicadores de verificación
Los endpoints de health están activos y configurados en los probes de Kubernetes.
Métricas disponibles y consumidas por la plataforma (Prometheus, Dynatrace, etc.).
Alarmas definidas según SLOs de cada servicio (latencia, error rate, saturación).
Existe al menos un dashboard operativo por servicio crítico.
## 1.8. Entregabilidad reproducible (12-Factor + plataforma)
Objetivo
Eliminar variabilidad entre entornos y asegurar despliegues consistentes y repetibles, desde desarrollo hasta producción.
Lineamientos de cumplimiento
Uso obligatorio de:
Docker multi-stage generado por el framework,
Helm chart base del framework/plataforma,
Golden Images corporativas (UBI/JDK21 u otras aprobadas).
Configuración externa al código (12-Factor): parámetros en variables de entorno, ConfigMaps, Secrets.
Promoción de artefactos mediante tags/versiones, no “rebuild” por entorno.
Manifiestos y Helm charts homogéneos entre entornos; solo difieren valores de configuración.
Indicadores de verificación
El build es determinístico (mismo commit → misma imagen).
Comandos helm lint / helm template ejecutan sin errores.
El mismo artefacto se promueve a DEV → QA → PROD (no se recompila).
El pipeline registra el ID de commit y la versión asociada al despliegue.
## 1.9. DevSecOps nativo
Objetivo
Integrar calidad, seguridad y despliegue continuo desde el pipeline, evitando controles manuales y subjetivos.
Lineamientos de cumplimiento
Uso del pipeline estándar del framework/plataforma que incluya al menos:
build & test,
análisis estático/cobertura (Sonar u otros),
SAST/SCA (Veracode/Prisma),
generación de SBOM,
escaneo de contenedor,
firma de imagen,
despliegue,
smoke tests / verificación post-deploy,
mecanismo de rollback.
Definición de quality gates bloqueantes para:
vulnerabilidades críticas,
deuda técnica extrema,
cobertura mínima,
fallas en pruebas.
Indicadores de verificación
El pipeline corre completo con las etapas definidas; no hay “saltos” manuales.
No se despliegan servicios con:
vulnerabilidades críticas abiertas,
quality gate en rojo.
Las imágenes desplegadas están firmadas y registradas en el registry corporativo.
El proceso de rollback ha sido probado al menos en un entorno de QA.
## 1.10. Diseño de APIs y contratos estables
Objetivo
Evitar rupturas a consumidores y promover compatibilidad evolutiva de APIs REST y eventos.
Lineamientos de cumplimiento
Versionado explícito de APIs:
vía path (/v1, /v2),
o headers de versión, con reglas claras de deprecación.
Uso de:
OpenAPI para servicios REST,
AsyncAPI para eventos/mensajería.
Errores estandarizados (por ejemplo, formato RFC 7807 para errores HTTP).
Uso de CloudEvents/schema registry cuando aplique en EDA.
Evolución de contratos bajo el principio de compatibilidad backward; breaking changes ⇒ nueva versión.
Indicadores de verificación
Existen definiciones OpenAPI/AsyncAPI versionadas en repositorio y/o catálogo API.
Tests de contract testing (productor/consumidor) pasan para cada versión publicada.
Rompimientos de contrato detectados en pipeline bloquean el despliegue.
La taxonomía de errores está documentada y se refleja en respuestas reales del servicio.
## 1.11. Datos, consistencia y gobernanza del framework
Objetivo
Asegurar exactitud, coherencia y evolución controlada tanto de los datos como del propio Framework de Integración.
Lineamientos de cumplimiento – Datos
Delimitación clara de transacciones y responsabilidades entre servicios.
Uso de SAGA, Outbox, deduplicación e idempotencia para integraciones distribuidas.
Políticas de retención y borrado seguro (logs, eventos, DLQ, datos temporales).
Lineamientos de cumplimiento – Gobernanza del framework
Uso del Initializer como punto único de creación de servicios (no proyectos “a mano”).
Registro de arquetipos/librerías en un catálogo central (Backstage u otro).
Política de deprecación y upgrade (ventanas de actualización, versiones soportadas, EoL).
Indicadores de verificación
Invariantes de negocio/documentos descritos en el dominio y en la documentación técnica.
Pruebas de idempotencia y manejo de reintentos incluidas para casos críticos.
Cada servicio indica en su README:
con qué versión de arquetipo/framework fue generado,
y está registrado en el catálogo corporativo.
Existen planes de deprecación/comunicación para cambios mayores de arquetipos y librerías.
# 2. Lineamientos de modelado de dominio
El modelado de dominio es el núcleo conceptual del Framework de Integración. Define cómo se representa el negocio dentro del software y garantiza que todos los servicios generados —sin importar si son REST, Camel, EDA, DB o híbridos— compartan un entendimiento común, preciso y desacoplado del negocio, inspirado en BIAN, DDD y Arquitectura Hexagonal.
Los lineamientos de esta sección aseguran que:
los servicios modelen correctamente las capacidades de negocio,
se mantenga el orden y claridad del dominio,
el software sea cambiable, testeable y estable,
las integraciones no “ensucien” la lógica del negocio,
los servicios estén alineados entre sí y con la arquitectura empresarial.
El objetivo final es que el dominio sea el centro del diseño, y la tecnología (REST, Kafka, Camel, DB, Mesh, etc.) sea solo una “capa de entrega”.
## 2.1. Fundamentos obligatorios del modelado
Todos los servicios generados por el framework deben cumplir 4 fundamentos:
### 2.1.1. Fundamento 1 — Modelo funcional basado en BIAN
Cada servicio debe alinearse explícitamente a un BIAN Service Domain o subdominio.
Cada Bounded Context DDD debe corresponder a una capacidad BIAN.
Las entidades del dominio deben usar lenguaje BIAN cuando aplica (Agreement, Arrangement, Facility, Control Record, etc.).
La documentación del servicio debe indicar:
Dominio BIAN,
Capacidad (qué hace el negocio),
Bounded Context (qué parte del dominio implementa el software)
Beneficio: interoperabilidad y estandarización en banca.
Una BIAN Capability es:
un concepto de negocio,
independiente de tecnología,
expresado desde la perspectiva de banca,
estandarizado internacionalmente.
Ejemplo BIAN: Execute Campaign
Un Bounded Context DDDes:
es el límite donde tu modelo de dominio es válido,
agrupa entidades, reglas y casos de uso,
evita ambigüedades,
define un contrato de integración hacia otros BCs.
Un BC puede implementar “parte” de una capacidad BIAN
Ejemplo DDD: Campaign Execution
Un BC puede implementar “parte” de una capacidad BIAN (no siempre es 1 = 1)
Ejemplo:
BIAN Capability: Customer Offer Management
Puede dividirse en 2 BCs:
OfferEligibilityContext
OfferFulfillmentContext
Un BC puede agrupar subcapacidades de BIAN
Ejemplo:
BIAN define:
Plan Campaign
Execute Campaign
Monitor Campaign
Tu operación puede decidir agrupar todo eso en un BC:
CampaignManagementContext
Ver ejemplo
### 2.1.2. Fundamento 2  — Dominio diseñado con DDD
Separación clara entre:
Entidades
Value Objects
Agregados
Eventos de dominio
Servicios de dominio
Repositorios (interfaces)
El dominio NO puede depender de frameworks (Quarkus, Camel, drivers, JPA, Kafka, etc.).
Reglas de negocio e invariantes deben vivir solo en el dominio.
Beneficio: mantenibilidad y claridad.
Ver ejemplo
### 2.1.3. Fundamento 3 — Arquitectura Hexagonal (Ports & Adapters)
Cada bounded context debe organizar su código separando estrictamente las reglas del negocio (dominio y aplicación) de la infraestructura técnica (REST, DB, mensajería, integración externa), mediante el uso de puertos (interfaces declaradas en application) y adaptadores (implementaciones técnicas ubicadas en infraestructura).
Este patrón permite controlar las dependencias entre capas, facilitar la mantenibilidad, habilitar pruebas más simples y permitir el reemplazo de tecnologías sin afectar el núcleo funcional.
Estructura de paquetes requerida:
/domain
/model
/events
/services
/repository
/application
/usecase
/service
/dto
/port
/in         ← input ports (casos de uso)
/out        ← output ports (dependencias externas)
/infrastructure
/rest         ← adaptadores REST (puertos de entrada)
/proxy        ← adaptadores HTTP/SOAP hacia sistemas externos
/repository   ← adaptadores de acceso a datos
/messaging    ← adaptadores a Kafka/PubSub/RabbitMQ
/adapters     ← otros conectores técnicos
/bean         ← factories y beans técnicos
/config         ← configuración del runtime (seguridad, serialización, CORS)
/route          ← rutas Camel (si aplica)
/cross          ← componentes técnicos propios, no parte del Framework
Puntos clave:
Los puertos (de entrada y salida) siempre viven en application/port.
El dominio nunca define puertos ni depende de infraestructura.
La infraestructura implementa los puertos y provee los adaptadores técnicos necesarios.
Reglas normativas
Dominio aislado
No contiene puertos.
No conoce infraestructura ni frameworks.
No importa clases externas a sí mismo.
Application como capa de orquestación
Declara los puertos de entrada (input ports) y de salida (output ports).
Orquesta la ejecución de casos de uso y la interacción con el dominio.
No implementa detalles técnicos.
Infraestructura solo implementa puertos y adapta protocolos
REST Controllers, Camel Routes, repositorios concretos, clientes externos y adaptadores de mensajería viven exclusivamente en esta capa.
Nunca contienen reglas de negocio.
Inbound adapters invocan únicamente input ports
No llaman servicios concretos
No acceden directamente al dominio
Output ports solo se implementan en infraestructura
Por ejemplo:
clientes HTTP
repositorios persistentes
publishers de eventos
Ver ejemplo
2.1.4. Fundamento 4 — Clean Code + lenguaje ubicuo
El modelo del dominio debe ser expresivo, simple, sin duplicación.
Nombres deben corresponder al lenguaje del negocio (ubiquitous language).
Código del dominio debe ser testeable sin levantar Quarkus ni Camel.
Beneficio: claridad y calidad del código.
Ver ejemplo
## 2.2. Lineamientos de diseño del dominio
Los lineamientos establecen cómo crear, estructurar y validar el dominio para cada servicio.
### 2.2.1. Entidades (Entities)
Reglas
Son objetos con identidad estable a lo largo del tiempo.
Su estado puede cambiar mediante reglas de negocio.
Deben ser modeladas en domain.model.
La identidad debe ser inmutable y preferir:
UUID,
identificadores BIAN (arrangementId, productId),
businessId cuando sea relevante.
Prohibido
Entidades que dependan de frameworks (@Entity, @Column, JPA) → eso va en infraestructura.
Reglas técnicas dentro de la entidad (validaciones de API, parsing de fechas, serializaciones).
### 2.2.2. Value Objects
Reglas
Representan conceptos inmutables:
Monto,
Fecha,
Documento,
Score,
Estado,
CustomerRef, etc.
Deben ser inmutables (campos final).
Validan su consistencia al construirse.
Prohibido
Value objects con setters o mutabilidad.
Que contengan lógica externa a su propósito.
### 2.2.3. Agregados (Aggregates)
Reglas
Cada agregado representa una unidad completa de consistencia.
Tiene un Aggregate Root que coordina invariantes y reglas.
Un servicio solo puede modificar su propio agregado, nunca el de otro dominio.
Naming recomendado
<Nombre>Aggregate
<Nombre>Root
Prohibido
Agregados gigantes con demasiadas responsabilidades.
Uno que dependa directamente de modelos de infraestructura.
### 2.2.4. Casos de Uso (Application Layer)
Reglas
Representan operaciones del negocio, no detalles técnicos.
Deben vivir en /application/usecases.
Deben orquestar el dominio, no implementarlo.
Deben depender solo de:
servicios de dominio,
repositorios (interfaces),
eventos de dominio.
Prohibido
Casos de uso que llamen APIs externas directamente.
Lógica de negocio dentro de rutas Camel.
### 2.2.5. Repositorios (Domain Ports)
Los repositorios son un patrón central de DDD, utilizados para declarar la interacción del dominio con mecanismos de persistencia. Su función es expresar desde el lenguaje de negocio qué datos se necesitan leer y guardar, sin revelar ni acoplar cómo se almacenan (SQL, NoSQL, Redis, API externa, etc.).
Reglas
Deben ser interfaces puras ubicadas en domain/repository, reflejando el lenguaje ubicuo del dominio.
Se redactan desde la perspectiva del modelo de negocio, usando:
Value Objects
Entity IDs
Entidades y agregados
Definen qué operaciones de persistencia necesita el dominio, pero jamás cómo se implementan.
La tecnología de persistencia (JPA, Panache, Redis, JDBC, HTTP, MQ, APIs internas) nunca aparece en el repositorio del dominio.
No se anotan ni dependen de frameworks (jakarta.persistence.*, @Entity, @Repository, RestClient, etc.).
Infraestructura nunca implementa directamente estas interfaces.
Las implementaciones se hacen contra application/port/out.
Patrón establecido para el Framework
Cuando el dominio define un repositorio como interface DDD, este no se expone directamente a infraestructura. Siguiendo el principio hexagonal:
La interfaz se define en domain/repository
application/port/out crea un puerto de salida que extiende esa interfaz
Infraestructura implementa siempre el puerto, no el repositorio del dominio
Ejemplo conceptual:
domain/repository/CampaignRepository           --> lenguaje de dominio
application/port/out/CampaignRepositoryPort    --> puerto de salida
infrastructure/db/JpaCampaignRepositoryAdapter --> implementación
Esto permite que:
El dominio permanezca 100% agnóstico
La aplicación controle cómo la infraestructura satisface sus necesidades
Los adaptadores puedan cambiar sin modificar el dominio
Ejemplo
Dominio
// domain/repository/CampaignRepository.java
```java
public interface CampaignRepository {
Optional<Campaign> findById(CampaignId id);
void save(Campaign campaign);
```
}
Puerto de aplicación
// application/port/out/CampaignRepositoryPort.java
```java
public interface CampaignRepositoryPort extends CampaignRepository { }
```
Infraestructura
// infrastructure/db/JpaCampaignRepositoryAdapter.java
@ApplicationScoped
```java
public class JpaCampaignRepositoryAdapter implements CampaignRepositoryPort {
```
...
}
Beneficios
Mantiene el dominio limpio y orientado a negocio.
Reduce acoplamiento y facilita cambios tecnológicos.
Permite reemplazar infraestructura sin alterar la lógica central.
Habilita testing sencillo del dominio y de la aplicación.
Mantiene los bounded contexts alineados con DDD y Hexagonal.
Prohibido
Exponer repositorios de dominio directamente hacia infraestructura.
Implementar repositorios del dominio (interfaces en domain/repository) directamente en infrastructure.
Utilizar interfaces técnicas (ej. PanacheRepository, JpaRepository, DAOs) como repositorios dentro de domain.
Declarar repositorios en dominio que revelen mecanismos técnico
### 2.2.6. Eventos de Dominio
Reglas
Se emiten cuando cambia el estado del negocio.
Deben vivir en /domain/events.
Son independientes de Kafka, Pub/Sub o RabbitMQ.
La traducción la realiza la capa infraestructura.
Prohibido
Eventos acoplados a mensajería (Kafka record, Rabbit message).
### 2.2.7. Servicios de Dominio
Reglas
Encapsulan lógica compleja que no pertenece a una sola entidad.
Deben ser stateless.
Prohibido
Servicios que mezclen lógica técnica (HTTP, DB, Camel).
## 2.3. Lineamientos de organización de paquetes
Todos los proyectos generados por el Initializer del Framework de Integración deben seguir una estructura de paquetes homogénea, alineada a DDD + Arquitectura Hexagonal y a las convenciones de arquetipos descritas en el documento de estructura de proyecto.
src/main/java/com/compartamos/process/<recurso>/
domain/
model/
events/
services/
repository/          # Opcional: interfaces de repositorio puramente DDD
application/
usecase/
service/             # Opcional: servicios de aplicación reutilizables
dto/                 # DTO internos de aplicación
port/
in/                # Input Ports (casos de uso expuestos)
out/               # Output Ports (dependencias externas)
infrastructure/
rest/
dto/               # DTO expuestos vía API REST
proxy/
<sistema>/dto/     # DTO de integración con sistemas externos
repository/          # Implementaciones técnicas de persistencia
adapters/            # Otros adaptadores técnicos (mensajería, schedulers, etc.)
bean/                # Beans y factories técnicas
config/                # Configuración del runtime (CORS, JSON, Camel, seguridad local)
route/                 # Opcional: rutas Camel (si el servicio usa Camel)
cross/                 # Componentes transversales propios del microservicio
Donde <recurso> es un nombre de negocio simple en minúsculas, alineado a capacidades/BIAN, por ejemplo:
campaign, customer, payments, credit, loan, score, collection, etc.
Reglas clave
domain/ — núcleo de negocio puro
Contiene:
Entidades, agregados y value objects (domain/model).
Eventos de dominio (domain/events).
Servicios de dominio (domain/services), cuando aplica.
Opcionalmente, interfaces de repositorio (domain/repository), definidas en lenguaje de negocio.
No contiene:
Puertos de entrada ni de salida (los puertos viven en application/port).
Dependencias a frameworks (Quarkus, Camel, JPA, Redis, Kafka, etc.).
DTO de transporte (REST, SOAP, mensajería) ni detalles técnicos.
Dependencia permitida: solo hacia sí mismo (otros paquetes domain.* y java.*).
application/ — orquestación y puertos hexagonales
Contiene:
Casos de uso (application/usecase).
Servicios de aplicación (application/service), si corresponde.
DTO internos (application/dto), no expuestos hacia el exterior.
Todos los puertos hexagonales:
application/port/in → input ports que exponen casos de uso.
application/port/out → output ports que declaran dependencias externas (repositorios, sistemas remotos, colas, caches, etc.).
Función:
Orquestar el flujo de negocio utilizando el dominio.
Declarar contratos de integración entre la lógica de negocio y la infraestructura.
No contiene:
Anotaciones ni clases de frameworks de infraestructura (RESTEasy, Panache, Camel, Redis, Kafka, etc.).
Llamadas directas a HTTP, BD, Redis, colas, ni código de conectividad.
Dependencias permitidas:
Hacia domain.* (modelo de negocio).
Hacia sus propios paquetes de puertos (application.port.in, application.port.out) y DTO internos.
Prohibido:
Que application dependa de infrastructure o de clases técnicas específicas.
infrastructure/ — adaptadores y detalles técnicos
Contiene:
infrastructure/rest → controladores REST (adaptadores de entrada).
infrastructure/rest/dto → DTO públicos expuestos en las APIs.
infrastructure/proxy → clientes HTTP/SOAP/gRPC a sistemas externos, organizados por sistema.
infrastructure/proxy/<sistema>/dto → modelos/DTO para integración con cada sistema externo.
infrastructure/repository → implementaciones técnicas de persistencia (JPA, JDBC, Redis, etc.), que implementan puertos definidos en application/port/out.
infrastructure/adapters → otros adaptadores (consumidores Kafka, schedulers, conectores file/legacy, etc.).
infrastructure/bean → definición de beans, factories y wiring técnico.
Función:
Implementar los puertos de salida (application/port/out) usando tecnologías concretas.
Proveer adaptadores de entrada (REST, eventos, jobs) que llamen a puertos de entrada (application/port/in).
Dependencias permitidas:
Hacia application.port.in y application.port.out.
Hacia domain.* (para usar modelos de negocio donde corresponda).
Prohibido:
Definir reglas de negocio o casos de uso en infraestructura.
Hacer que infrastructure sea dependencia de domain o application.
config/ — configuración del runtime
Contiene configuraciones técnicas locales del microservicio:
JSON/Jackson, JSON-B.
CORS.
Configuración de Camel específica del servicio.
OIDC/JWT cuando no viene resuelto por starters.
Mapeadores de errores locales (si aplica).
No contiene lógica de negocio.
Se considera parte de la “infraestructura técnica” del servicio, pero se mantiene como carpeta independiente para claridad.
route/ — rutas Camel (cuando aplica)
Solo presente en servicios que utilizan Apache Camel.
Contiene clases RouteBuilder que:
Actúan como adaptadores de entrada/salida.
No implementan lógica de negocio.
Delegan siempre en application/port/in para ejecutar casos de uso.
cross/ — componentes transversales específicos del microservicio
Solo para componentes transversales propios del microservicio que no forman parte del Framework estándar.
No debe contener clases que pertenezcan a los starters corporativos (logging, observabilidad, resiliencia, seguridad, etc.), ni duplicar funcionalidades ya provistas por el Framework.
Restricciones de dependencias (vista de paquetes)
domain
Puede depender de: domain
No puede depender de: application, infrastructure, route, cross, frameworks.
application
Puede depender de: domain, application.port.in, application.port.out, application.dto, application.service.
No puede depender de: infrastructure, route, cross, frameworks técnicos.
infrastructure, route, config, cross
Pueden depender de: application.port.in, application.port.out, domain.
No pueden hacer que domain o application dependan de ellos.
Esta organización de paquetes es obligatoria para todos los proyectos generados por el Initializer y es la base para aplicar las reglas de arquitectura hexagonal, las validaciones automáticas (SonarQube, ArchUnit) y los checklists de cumplimiento del Framework de Integración.
## 2.4. Lineamientos de alineamiento BIAN
Este marco define cómo usar BIAN para mapear capacidades bancarias:
Reglas
Cada servicio debe indicar:
Dominio BIAN primario (ej. Consumer Loan, Collections, Payments).
Subdominio o Service Operation si aplica.
El Bounded Context debe alinearse a un Service Domain.
Los DTOs de entrada/salida deben intentar reflejar la semántica BIAN cuando corresponda.
Prohibido
Mezclar capacidades que pertenezcan a BIAN diferentes sin justificación.
Crear modelos que rompan los invariantes de BIAN.
## 2.5. Lineamientos para invariantes y reglas de negocio
Se deben declarar explícitamente
Reglas que siempre deben cumplirse.
Estados válidos e inválidos.
Secuencia válida de operaciones.
Condiciones que disparan eventos de dominio.
Políticas de consistencia (SAGA, outbox, idempotencia).
Ejemplos
Un crédito no puede pasar a "Desembolsado" si no está "Aprobado".
Un evento CustomerRegistered no puede dispararse sin un customerId válido.
## 2.6. Requisitos de testeo del dominio
Obligatorio
Tests unitarios del dominio deben ejecutarse sin levantar Quarkus.
Casos de uso deben tener pruebas que validen:
invariantes,
flujos de negocio,
estados válidos,
eventos generados.
Cobertura mínima recomendada
80% del dominio.
## 2.7. Lineamientos de DTOs y mapeos
Reglas
Los DTOs no representan el dominio, solo la interfaz externa.
Los mappers deben estar en los módulos commons o en infraestructura.
Los DTOs deben incluir:
businessId,
traceId/correlationId (opcional si va solo en encabezados),
campos funcionales del API.
Prohibido
Lógica de negocio en DTOs.
Validaciones complejas en DTOs (van en dominio).
## 2.8. Lineamientos para integraciones dentro del dominio
Reglas
El dominio nunca debe conocer:
direcciones de endpoints,
colas/topics,
queries SQL,
modelos JPA,
APIs externas.
Toda integración debe representarse como un puerto del dominio (interfaz) o como un adaptador en infraestructura.
## 2.9. Lineamientos de documentación del dominio
Todo servicio debe documentar:
Dominio BIAN y Bounded Context.
Entidades, agregados y value objects principales.
Eventos de dominio.
Casos de uso principales.
Diagrama C4-L3 de componentes del dominio (Mermaid o draw.io).
Invariantes clave.
# 3. Lineamientos de seguridad y gestión de secretos
Esta sección define cómo deben protegerse todos los servicios generados por el framework, desde la autenticación/autorización hasta el manejo de secretos y la integración con Service Mesh. Lo que aquí se define no es opcional: es el “contrato de seguridad” mínimo del Framework de Integración.
## 3.1. Modelo estándar de autenticación y autorización (JWT/OAuth2)
Objetivo
Asegurar que todos los servicios expuestos (REST, APIs Camel, gateways de integración) utilicen un modelo homogéneo de autenticación y autorización basado en JWT/OAuth2 y MicroProfile JWT.
Alcance
Todos los endpoints REST expuestos por interfaces.rest.
Endpoints Camel expuestos vía platform-http o rest-dsl.
Servicios internos que se comuniquen mediante HTTP entre microservicios (malla este–oeste).
Lineamientos
JWT obligatorio en servicios expuestos
Todo endpoint que exponga capacidades de negocio debe exigir un token JWT firmado (RS256) emitido por el IdP corporativo.
Se utilizará MicroProfile JWT (smallrye-jwt en Quarkus) como implementación estándar.
Configuración centralizada de validación JWT
Propiedades mínimas en application.properties (o equivalente):
quarkus.smallrye-jwt.enabled=true
mp.jwt.verify.publickey.location (archivo PEM o JWKS corporativo).
mp.jwt.verify.issuer (issuer corporativo: dev, qa, prod).
El arquetipo debe venir con estos parámetros ya definidos para ambientes de ejemplo, y ser sobreescritos vía configuración de plataforma.
Autorización basada en roles/grupos
La autorización se implementa con @RolesAllowed sobre recursos REST y servicios de aplicación.
Los roles se mapearán con el claim groups del JWT.
Se deben usar constantes centralizadas (por ejemplo, SecurityConstants.ROLE_INTEGRATION_API, ROLE_BACKOFFICE), nunca literales de texto dispersos.
Fachada de seguridad obligatoria en el framework
Se utilizará una clase tipo JwtInfo como fachada para leer claims (userId, channel, tenant, roles) de forma tipada y consistente.
Esta fachada será parte del módulo security-starter del framework y estará disponible en todos los arquetipos.
Integración con trazabilidad y logging
Después de la validación de JWT, un filtro como SecurityContextFilter deberá:
Extraer userId y channel de JwtInfo.
Poblar el MDC (MDC.put("userId", ...); MDC.put("channel", ...);).
Esto complementa al TraceFilter (que ya setea traceId, correlationId, businessId).
Reglas mínimas de claims del token
Obligatorios:
iss (issuer corporativo).
sub (identificador del sujeto).
groups (roles).
exp (expiración).
Recomendados:
iat (issued at).
tenant, channel, u otros definidos en SecurityConstants.
Justificación
Evita múltiples modelos de autenticación dispersos.
Facilita auditoría, trazabilidad y análisis de incidentes.
Permite que herramientas de observabilidad vean usuario + canal + traceId en el mismo log.
Verificación
application.properties contiene las propiedades MP-JWT definidas.
Todos los recursos en interfaces.rest que exponen negocio tienen @RolesAllowed.
No hay jwt.getClaim("…") suelto en recursos; solo uso de JwtInfo.
Logs JSON incluyen userId y channel además de traceId y correlationId.
Ver ejemplo
## 3.2. Lineamientos de gestión de secretos (GSM y KMS)
Objetivo
Eliminar secretos en código y configuraciones de repositorio, centralizando su administración en servicios gestionados de GCP (GSM/KMS) y la plataforma de seguridad.
Alcance
Credenciales de bases de datos, colas, APIs externas.
API keys, client secrets, passwords, certificados, tokens técnicos.
Claves de cifrado de datos sensibles.
Lineamientos
Prohibición explícita de secretos en código/repositorio
No se permite:
Usuarios/contraseñas embebidos en application.properties.
Claves privadas (*.pem, *.p12) en src/main/resources.
API keys en código Java, YAML o JSON dentro del repo.
Cualquier ejemplo de token/clave en el arquetipo debe ser claramente ficticio y marcado como “solo dev/local”.
Uso obligatorio de Google Secret Manager (GSM)
Credenciales deben almacenarse en GSM y referenciarse desde el servicio mediante:
Variables de entorno que el runtime resuelve desde GSM.
Integración nativa de plataforma (sidecar, CSI driver, etc.).
Los arquetipos deben venir con ejemplos de cómo parametrizar DB_USERNAME, DB_PASSWORD, API_KEY_X, etc., como placeholders, nunca valores reales.
Uso de Google KMS para claves de cifrado
Cualquier clave usada para cifrar datos en reposo o en tránsito (cuando no se delega a la base o al proveedor) debe vivir en KMS.
Las aplicaciones nunca deben tener acceso directo a la clave “en claro”, sino usar servicios/KMS para operaciones de cifrado/descifrado, según las capacidades de la plataforma.
Separación de responsabilidades
El equipo de plataforma define:
Políticas de creación, rotación y revocación de secretos.
Roles IAM para acceso a GSM/KMS.
Los equipos de desarrollo solo consumen secretos desreferenciando nombres (secret-id) definidos por la plataforma.
Entornos locales / desarrollo
Se permite el uso de archivos como privateKey-dev.pem solo para pruebas locales, marcados explícitamente con comentarios de “NO subir a repositorio” y excluidos con .gitignore.
El framework debe incluir advertencias claras en el README del arquetipo.
Justificación
Minimiza el riesgo de filtración de credenciales en repositorios Git.
Facilita rotación y gestión centralizada de secretos.
Cumple requisitos regulatorios y de auditoría de entidades financieras.
Verificación
Escaneos SAST/SCA y secretos (Trivy, GitLeaks, etc.) sin hallazgos críticos de secretos.
Revisión de application.properties/application.yaml: no hay passwords ni tokens.
Manifiestos de despliegue (Helm, K8s) usan referencias a secretos (GSM/KMS), no valores literales.
## 3.3. Lineamientos de integración con Service Mesh (mTLS y políticas L7)
Objetivo
Asegurar comunicaciones seguras servicio–servicio mediante mTLS obligatorio, controles de tráfico de red y, cuando aplique, políticas de capa 7 (L7) alineadas al modelo de seguridad corporativo.
Alcance
Tráfico este–oeste entre servicios en GKE/Anthos (microservicios, integraciones Camel, componentes EDA).
Integración con sidecars de Service Mesh (por ejemplo, Anthos Service Mesh / Istio).
Lineamientos
mTLS obligatorio entre servicios internos
Todo tráfico interno debe pasar por el Mesh con:
Certificados gestionados por el Mesh.
Políticas de autorización que impidan tráfico en texto plano entre namespaces y servicios.
Separación de autenticación
La autenticación de usuario/cliente final se maneja vía JWT en la capa de aplicación.
La autenticación entre servicios técnicos se apoya en:
mTLS (identidad del workload).
Opcionalmente, JWT técnicos (client credentials) cuando se requiera trazabilidad adicional.
Políticas de capa 7 (L7)
Para servicios REST expuestos, se podrán aplicar:
Restricciones por método (GET/POST/PUT).
Reglas de path (solo rutas declaradas).
Throttling / rate limiting corporativo.
El diseño de estas políticas debe ser coherente con los endpoints generados por los arquetipos (naming y paths estándar).
Compatibilidad con observabilidad y trazabilidad
El Mesh no debe “romper” la propagación de traceparent y tracestate.
Los arquetipos deben incluir configuración de propagación de headers de traza compatible con el Mesh.
Justificación
Reduce superficie de ataque en la red interna.
Permite políticas de seguridad declarativas, independientes del código.
Refuerza el modelo Zero-Trust (nadie es confiable por estar “dentro” del cluster).
Verificación
Namespaces y servicios con políticas de PeerAuthentication/AuthorizationPolicy (u objeto equivalente).
No existen servicios internos accesibles en HTTP sin TLS dentro del cluster.
Pruebas de conectividad demuestran que tráfico directo sin mTLS es rechazado.
## 3.4. Estándares mínimos de autorización por tipo de servicio
Objetivo
Definir cómo debe aplicarse la autorización según el tipo de servicio generado por el framework (REST, Camel, EDA), evitando zonas “grises” sin control de acceso claro.
Alcance
Arquetipos REST.
Arquetipos Camel (HTTP in/out).
Arquetipos EDA (Kafka/PubSub/RabbitMQ).
Lineamientos
Servicios REST (interfaces.rest)
Todo endpoint que exponga funcionalidades de negocio debe:
Estar protegido con @RolesAllowed.
Utilizar roles definidos en SecurityConstants.
Endpoints técnicos (health, metrics, info) tendrán tratamiento diferenciado:
/q/health → acceso Mesh/monitoring, no público.
/q/metrics → sólo scrapeadores autorizados.
Rutas Camel con entrada HTTP
Cualquier from("platform-http:/...") o equivalente:
Debe estar detrás de validación JWT (JAX-RS o filtro equivalente).
Debe respetar los mismos roles que un endpoint REST tradicional.
No se permite exponer integraciones HTTP “anónimas” salvo casos muy específicos acordados con seguridad.
Integraciones EDA (Kafka/PubSub/RabbitMQ)
La autorización primaria se gestiona por:
ACLs a nivel de broker/topic/cola (responsabilidad de la plataforma).
A nivel de aplicación:
Los mensajes deben incluir contexto mínimo (ej. tenant, channel, businessId) cuando provienen de interacción de usuario.
Los consumidores deben validar lo que controlan (por ejemplo, rechazar mensajes con tenant no soportado por el servicio).
Servicios internos “sólo backplane”
Para servicios que sólo exponen endpoints internos de sistema a sistema:
Se puede utilizar JWT técnico con roles como INTEGRATION_API.
Estos endpoints siguen usando @RolesAllowed, pero con roles de integración, no roles de usuario final.
Justificación
Clarifica quién debe proteger qué y cómo.
Evita que queden “zonas libres” de autorización por malentender el tipo de servicio.
Permite que auditoría pueda revisar de forma sistemática qué controles aplican a cada arquetipo.
Verificación
Para servicios REST/Camel: revisión de código y/o herramientas de escaneo que verifiquen uso de @RolesAllowed en recursos expuestos.
Para servicios EDA: revisión de configuración de topics/colas y definición clara de quién puede publicar/consumir.
Para todos: documentación de seguridad en el README del servicio, indicando:
Tipo de autenticación,
Roles requeridos,
Contexto de seguridad transportado (claims, headers, etc.).
## 3.5. Reglas transversales de cumplimiento de seguridad
Para cerrar la sección, se establecen las siguientes reglas transversales:
Secure by default
Todo arquetipo debe nacer con seguridad activada: JWT habilitado, filtros de seguridad, propiedades base configuradas.
Menor privilegio
Roles y permisos deben ser lo más específicos posible.
No se deben crear roles “god-mode” genéricos (ej. ADMIN_ALL) salvo acuerdos explícitos.
Rotación y ciclo de vida
Claves y secretos deben tener ciclo de vida definido (rotación periódica, revocación).
Los servicios deben poder refrescar claves/secretos sin redeploy completo cuando la plataforma lo soporte.
Ambientes separados
Issuer, claves y endpoints de IdP deben ser distintos por ambiente (dev, QA, prod).
No se permite reutilizar credenciales de producción en otros entornos.
# 4. Lineamientos de observabilidad
Objetivo
Definir un modelo estándar de observabilidad para todos los servicios generados por el Framework de Integración, que garantice:
Trazabilidad extremo a extremo (trazas distribuidas, IDs de correlación, businessId).
Logs estructurados y correlables con trazas y métricas.
Manejo uniforme de errores.
Métricas técnicas y de negocio visibles en la plataforma de monitoreo.
Auditoría de acciones relevantes de negocio.
La observabilidad se implementa siempre “by default” mediante:
OpenTelemetry (traces y métricas).
Micrometer + Prometheus (/metrics).
Filtros de trazabilidad (TraceFilter y HeadersPropagationFilter).
JSON logging con MDC / categorías lógicas.
Modelo estándar de errores (ApiError + GlobalExceptionMapper).
Módulo de logging y auditoría (TECH/BUSINESS/AUDIT).
Todos los arquetipos REST, Camel y EDA deben incluir el starter de observabilidad del framework.
observability-starter
logging-starter
exception-starter
resilience-starter (para resiliencia integrada con observabilidad)
## 4.1 Trazabilidad estándar (traceId, correlationId, businessId)
Lineamientos
Cada request HTTP entrante debe tener un contexto de trazabilidad compuesto al menos por:
traceId (OpenTelemetry – W3C Trace Context).
correlationId (X-Correlation-Id).
businessId (X-Business-Id, opcional según el caso de negocio).
El framework provee un filtro estándar TraceFilter (en observability-starter) que:
TraceFilter es un adaptador técnico de entrada que actúa antes de invocar los puertos de entrada definidos en application/port/in, cumpliendo los lineamientos de Arquitectura Hexagonal.
Lee los headers de entrada:
X-Correlation-Id.
X-Business-Id.
Si X-Correlation-Id no existe, genera un nuevo UUID.
Obtiene el traceId real del span activo de OpenTelemetry:
Span.current().getSpanContext().getTraceId()
Inserta en MDC:
traceId, correlationId, businessId.
Actualiza un TraceContext request-scoped con estos valores.
Agrega en la respuesta HTTP:
X-Trace-Id, X-Correlation-Id, X-Business-Id.
Todos los servicios REST generados por el arquetipo deben registrar TraceFilter como @Provider con prioridad alta (AUTHENTICATION/SECURITY), para que:
El MDC esté poblado antes de ejecutar controladores y lógica.
Los logs de aplicación y de Camel compartan los mismos IDs.
El traceId de OpenTelemetry es la referencia única para correlación:
Debe aparecer en los logs.
Debe verse en las trazas del APM (Dynatrace / Grafana Tempo / Jaeger).
Debe propagarse a servicios downstream vía traceparent/tracestate (OTel).
Ningún servicio debe “inventar” su propio esquema de IDs de trazabilidad;
siempre se utilizan los headers y MDC establecidos por TraceFilter.
Ejemplo de cumplimiento esperado para una llamada GET:
Request:
GET /campaigns
X-Correlation-Id: 31ab4a1f-1234-5678-9012-f0d0b0f0c0a0
Log:
{ "traceId": "5a3b8e4d8a0b12c3", "correlationId": "31ab4a1f-1234-5678-9012-f0d0b0f0c0a0", ... }
Traza:
Span GET /campaigns con Trace ID: 5a3b8e4d8a0b12c3.
Regla
Ningún servicio debe “inventar” su propio esquema de IDs de trazabilidad; siempre se utilizan los headers y MDC establecidos por TraceFilter.
Ver ejemplo
## 4.2 Propagación de contexto hacia servicios externos
Objetivo
Asegurar que todas las llamadas HTTP salientes hechas desde los microservicios (vía MicroProfile REST Client) hereden el mismo contexto de trazabilidad que la request entrante:
X-Correlation-Id
X-Business-Id
traceparent / tracestate (gestionados por OpenTelemetry)
Esto permite construir trazas distribuidas end-to-end entre servicios A → B → C y correlacionar todo con los logs del framework, manteniendo la separación de responsabilidades propia de la Arquitectura Hexagonal: las llamadas HTTP salientes se realizan exclusivamente desde adaptadores de infraestructura (infrastructure/proxy, infrastructure/adapters) que implementan puertos definidos en application/port/out; ni domain ni application realizan llamadas HTTP directas.
Lineamientos
Toda llamada HTTP saliente (MicroProfile REST Client / quarkus-rest-client ) debe propagar el contexto de trazabilidad y negocio actual:
X-Correlation-Id.
X-Business-Id.
El traceId se propaga vía traceparent/tracestate manejados automáticamente por OpenTelemetry.
Estas llamadas deben originarse únicamente en adaptadores de infraestructura (por ejemplo, clases en infrastructure/proxy/... que implementan puertos de application/port/out), nunca desde la capa de dominio ni desde la capa de aplicación.
El framework provee HeadersPropagationFilter (en observability-starter) implementando ClientHeadersFactory de MicroProfile REST Client, que:
Lee los headers entrantes (si existen).
Si no existen, toma correlationId y businessId desde MDC.
Inyecta X-Correlation-Id y X-Business-Id en cada request saliente.
Devuelve un conjunto de headers a agregar en la request saliente.
No modifica el cuerpo ni la semántica de la petición.
La aplicación de este factory se realiza en los clientes REST declarados en la capa de infraestructura, manteniendo a domain y application libres de detalles técnicos de HTTP.
Cada cliente REST generado por el arquetipo debe declarar en application.properties:
quarkus.rest-client.<config-key>.headers-factory=\ com.compartamos.integration.observability.restclient.RestClientHeadersPropagationFactory
De este modo, cualquier adaptador HTTP que implemente un puerto de application/port/out heredará automáticamente el contexto de trazabilidad definido por el framework.
La propagación de traceparent/tracestate la gestiona OpenTelemetry; los equipos no deben implementarla manualmente. Siempre se utiliza:
TraceFilter para poblar MDC en las solicitudes entrantes.
HeadersPropagationFilter para propagar X-Correlation-Id y X-Business-Id en las solicitudes salientes.
El soporte OTel de Quarkus para la propagación del contexto de trazas
Beneficio
Permite que una única transacción distribuida (por ejemplo, desde un canal hasta varios microservicios internos) se visualice como una sola traza con múltiples spans, conservando los mismos IDs de correlación y negocio.
Los logs de ambos servicios comparten:
el mismo traceId,
el mismo correlationId,
el mismo businessId.
No hay lógica duplicada por servicio; todo se centraliza en:
TraceFilter (entrante),
HeadersPropagationFilter (saliente),
Soporte OpenTelemetry en Quarkus.
manteniendo el dominio y la capa de aplicación completamente agnósticos a estos detalles de infraestructura.
Ver ejemplo
## 4.3 Logging estructurado estándar
Lineamientos
Todos los servicios del framework deben:
Habilitar logging JSON en consola:
quarkus.log.console.json=true
Trabajar con niveles por categoría:
TECH → logs técnicos y de integración.
BUSINESS → eventos funcionales.
AUDIT → eventos de auditoría.
El módulo de logging-starter define categorías y modelos:
LogCategories
Constantes:
TECH, BUSINESS, AUDIT.
AuditEvent
Modelo inmutable con builder para eventos de auditoría, con los campos:
action (obligatorio)
entity
entityId
outcome (por defecto "SUCCESS", o "FAILURE" mediante failure(reason))
reason
userId
channel
traceId
correlationId
businessId
timestamp (por defecto Instant.now() si no se envía)
metadata (mapa clave–valor para detalles adicionales)
AuditLogger
Emite logs estructurados en la categoría AUDIT.
Construye un payload tipo mapa con:
type = "AUDIT"
action, entity, entityId, outcome, reason
userId, channel, traceId, correlationId, businessId
timestamp
meta (cuando metadata no está vacío)
Completa automáticamente (si no vienen seteados en el AuditEvent):
traceId, correlationId, businessId desde MDC (llenado por TraceFilter).
userId, channel desde MDC (llenado típicamente por SecurityContextFilter cuando hay JWT).
BusinessLogger
Emite logs estructurados en la categoría BUSINESS.
Expone al menos un método:
info(String event, String message, Map<String, Object> metadata)
Construye un payload tipo mapa con:
type = "BUSINESS"
event, message
userId, channel, traceId, correlationId, businessId desde MDC
timestamp
meta (cuando metadata no está vacío)
Campos mínimos que deben aparecer en cada log relevante
Especialmente en logs de tipo BUSINESS y AUDIT:
timestamp
level
loggerName (una de: TECH, BUSINESS, AUDIT)
traceId
correlationId
businessId (si aplica al caso de negocio)
userId, channel (si aplica JWT / contexto de seguridad)
message / event / action (según tipo TECH/BUSINESS/AUDIT)
meta / metadata para detalles adicionales de negocio o contexto.
Buenas prácticas por categoría:
TECH
Integraciones y llamadas a sistemas externos.
Tiempos de respuesta, timeouts, reintentos.
Errores técnicos, excepciones no controladas.
BUSINESS
Eventos funcionales clave del proceso:
“Campaña creada”
“Ofertas generadas”
“Regla aplicada”
“GetCampaigns ejecutado”
AUDIT
Aprobaciones / rechazos.
Cambios de estado sensibles.
Accesos a información crítica.
Operaciones ejecutadas por un usuario identificado sobre entidades de negocio.
Ejemplo (AUDIT simplificado)
{
"loggerName": "AUDIT",
"level": "INFO",
"message": {
"type": "AUDIT",
"action": "GET_CAMPAIGNS",
"entity": "Campaign",
"entityId": "",
"outcome": "SUCCESS",
"reason": "",
"userId": "test-user",
"channel": "INTEGRATION_API",
"traceId": "0da1b75075183817...",
"correlationId": "a57a204b-224c-4805-bd87-694c3e3156c6",
"businessId": "123456789",
"timestamp": "2025-11-11T23:27:54.841Z",
"meta": { "count": 2 }
}
}
Este ejemplo corresponde a una llamada a:
AuditLogger.log(
AuditEvent.builder("GET_CAMPAIGNS")
.entity("Campaign")
.outcome("SUCCESS")
.metadata(Map.of("count", campaigns.size()))
.build()
```java
);
```
Regla
Ningún equipo debe inventar formatos de logs diferentes por microservicio.
Es obligatorio utilizar:
LogCategories (TECH, BUSINESS, AUDIT).
AuditEvent + AuditLogger para logging de auditoría.
BusinessLogger para logging de negocio.
Cualquier log de negocio/auditoría que no use estas abstracciones se considera fuera de estándar del framework.
Ver ejemplo
## 4.4 Manejo estándar de errores
Objetivo
Tener un formato único y estándar para todas las respuestas de error, con trazabilidad automática y logs consistentes.
Lineamientos
Todos los errores expuestos por servicios REST del framework deben respetar un formato JSON estándar ApiError, con al menos:
code → código de error (funcional o técnico).
message → mensaje técnico legible (no stacktrace).
detail → detalle opcional controlado.
traceId → para correlación con trazas.
correlationId → para correlación de negocio.
timestamp.
El módulo exception-starter provee:
ApiError → modelo estándar de error.
ErrorCode → enum con códigos base:
VALIDATION_ERROR
DOWNSTREAM_TIMEOUT
DOWNSTREAM_UNAVAILABLE
DOWNSTREAM_ERROR
TECHNICAL_ERROR
GlobalExceptionMapper:
Captura excepciones y las transforma en ApiError.
Pone traceId y correlationId desde MDC.
Mapea:
ConstraintViolationException → VALIDATION_ERROR.
ResilienceException → DOWNSTREAM_ERROR.
Otras excepciones no controladas → TECHNICAL_ERROR.
Integración con resiliencia:
ResilienceException encapsula fallos de:
@Retry, @Timeout, @CircuitBreaker.
Fallbacks técnicos.
Fallbacks provee manejadores estándar:
EmptyList → devuelve lista vacía controlada.
PropagateAsUncheckedString → convierte el fallo en ResilienceException para que GlobalExceptionMapper genere un ApiError DOWNSTREAM_ERROR.
Reglas de uso:
No devolver stacktrace al consumidor.
No exponer mensajes internos de sistemas terceros.
Mantener la taxonomía de ErrorCode sincronizada con monitoreo y soporte.
Cualquier nuevo tipo de error funcional debe:
Definir un nuevo ErrorCode.
Documentarse en el catálogo de errores corporativo.
Ejemplo de respuesta estándar:
{
"code": "DOWNSTREAM_ERROR",
"message": "Error al invocar un servicio externo.",
"detail": "Downstream failure: Simulated downstream crash for demo",
"traceId": "4e8d61c3b54e8d9f...",
"correlationId": "b8c1-9ad5-...",
"timestamp": "2025-11-10T20:21:14.123Z"
}
Ver ejemplo
## 4.5 Métricas y health checks
Objetivo
La observabilidad de los servicios generados por el Framework de Integración se sustenta en la exposición estandarizada de métricas técnicas y de negocio, así como en el monitoreo continuo del estado operativo mediante health checks. Estas capacidades permiten al equipo de operaciones, monitoreo y SRE disponer de información consistente y confiable para la toma de decisiones, análisis de resiliencia y gestión de incidentes.
Lineamientos
1. Endpoints estándar
Todos los microservicios generados mediante el arquetipo del framework deben exponer de forma obligatoria los siguientes endpoints:
Endpoint de métricas Prometheus
GET /metrics
Expone métricas técnicas y de negocio recolectadas por Micrometer y expuestas en formato Prometheus para el sistema de monitoreo (Grafana, Prometheus Operator, etc.).
Endpoint de health checks (SmallRye Health)
GET /health
Muestra el estado de salud general del servicio, integrando liveness y readiness.
Asimismo, Quarkus habilita sub-paths especializados cuando se requieren para orquestación:
GET /health/live
GET /health/ready
Estos endpoints son utilizados por Kubernetes/OpenShift para determinar reinicios seguros, control de rollout y verificación de disponibilidad.
2. Configuración base obligatoria del arquetipo
El arquetipo del framework debe incluir la configuración mínima necesaria para habilitar métricas, trazabilidad y health checks. Las dependencias obligatorias son:
quarkus-opentelemetry
quarkus-micrometer
quarkus-micrometer-registry-prometheus
quarkus-smallrye-health
Asimismo, se deben establecer las siguientes propiedades:
OpenTelemetry
# OpenTelemetry Configuration
quarkus.otel.service.name=<nombre-servicio>
quarkus.otel.enabled=true
quarkus.otel.traces.enabled=true
quarkus.otel.metrics.enabled=true
Micrometer / Prometheus
# Micrometer / Prometheus
quarkus.micrometer.enabled=true
quarkus.micrometer.export.prometheus.enabled=true
quarkus.micrometer.export.prometheus.path=/metrics
Health Checks
# Health
quarkus.smallrye-health.root-path=/health
quarkus.smallrye-health.ui.always-include=true
Con estas configuraciones, todos los microservicios ofrecen visibilidad consistente y estandarizada desde el primer despliegue.
3. Métricas mínimas por servicio
Cada servicio debe exponer dos tipos de métricas:
a) Métricas técnicas (obligatorias)
Quarkus y Micrometer exponen por defecto un conjunto de métricas técnicas asociadas al runtime y al comportamiento de los endpoints:
Latencias HTTP (RED metrics).
Contadores de solicitudes por código HTTP.
Métricas de JVM (heap, GC, threads, file descriptors).
Métricas del pool de threads y consumo de recursos.
Estas métricas no requieren implementación adicional, solo la configuración mínima del framework.
b) Métricas de negocio (obligatorias)
Cada caso de uso relevante debe registrar al menos una métrica de negocio, que permita monitorear su comportamiento funcional. Ejemplos:
Número de campañas consultadas o creadas.
Número de ofertas generadas.
Conteo de mensajes procesados en flujos EDA.
Tamaño del último resultado devuelto en procesos de consulta.
Estas métricas deben ser implementadas mediante Counter, Gauge o Timer de Micrometer, utilizando tags que faciliten su filtrado en Grafana.
4. Health Checks
Los microservicios deben implementar health checks para monitorear la disponibilidad interna y externa del servicio.
a) Readiness Checks (obligatorio)
Valida que el servicio se encuentra apto para recibir tráfico.
Deben incluir validaciones ligeras de conectividad hacia dependencias críticas:
API externas (REST clients).
Bases de datos.
Colas / topics (Kafka, JMS).
Otros servicios de integración.
Los readiness checks deben impedir que el servicio se marque como “READY” mientras sus dependencias no estén disponibles, evitando fallos en cascada.
b) Liveness Checks
Valida que la instancia del servicio está viva, es decir, no se encuentra en un estado bloqueado o irrecuperable.
Quarkus provee liveness checks básicos, los cuales pueden extenderse en caso de ser necesario (por ejemplo, validación del event loop en servicios reactivos).
5. Relación con Kubernetes / OpenShift
Los orquestadores deben usar los endpoints /health (o sus variantes) para:
Reiniciar automáticamente pods en mal estado (liveness probes).
Controlar el tráfico solo a pods completamente operativos (readiness probes).
Gestionar despliegues progresivos (rolling updates) evitando interrupciones del servicio.
La estandarización de health checks garantiza un comportamiento homogéneo entre servicios, reduciendo riesgos en despliegues y escalamiento.
6. Reglas del framework
Todo servicio debe exponer /metrics y /health desde su primera versión.
Es obligatorio implementar al menos un health check de conectividad a dependencias externas críticas.
Cada caso de uso debe registrar métricas de negocio significativas.
Los servicios no deben definir formatos o paths personalizados para métricas o health checks.
Los nombres de las métricas deben seguir un patrón uniforme, evitando prefijos específicos del dominio o equipo.
Ver ejemplo
## 4.6 Auditoría de negocio
Lineamientos
Acciones sensibles o relevantes (aprobaciones, rechazos, actualizaciones críticas) deben registrar un evento de auditoría mediante AuditLogger:
Ejemplos:
Aprobación de una solicitud de crédito.
Cambio de estado de una campaña.
Acceso a datos de cliente en contextos regulados.
Cada AuditEvent debe contener al menos:
action → nombre funcional de la acción (CREATE_CAMPAIGN, APPROVE_LOAN, etc.).
entity → tipo de entidad (Campaign, Loan, Customer).
entityId → identificador (CMP-001, LOAN-123, etc.).
outcome → SUCCESS / FAILURE.
reason → detalle en caso de fallo o rechazo.
userId, channel → desde JWT (vía SecurityContextFilter).
traceId, correlationId → desde MDC.
timestamp.
Los equipos no deben escribir logs de auditoría “a mano” usando Logger.getLogger("AUDIT"); deben usar AuditEvent + AuditLogger para asegurar formato y campos mínimos.
Beneficio
Permite responder a “quién hizo qué, sobre qué, cuándo y con qué resultado” de forma uniforme, y correlacionar esos eventos con trazas y errores técnicos para análisis forense y cumplimiento regulatorio.
Ver ejemplo
Ver ejemplo completo de Observabilidad
# 5. Lineamientos de resiliencia
Objetivo
Definir un modelo estándar de resiliencia para todos los servicios generados por el Framework de Integración, de forma que:
El comportamiento ante fallos sea predecible y homogéneo entre servicios.
Los tiempos de respuesta sean controlados (fail fast, sin colgar hilos).
Se eviten efectos cascada cuando fallan dependencias externas (HTTP, colas, archivos, etc.).
Se registren adecuadamente los fallos (para observabilidad y mejora continua).
Se apliquen patrones de degradación segura (fallbacks) e idempotencia donde corresponda.
La resiliencia se implementa “by default” mediante:
MicroProfile Fault Tolerance (MP FT): @Timeout, @Retry, @CircuitBreaker, @Fallback.
Anotación estándar @ResilientHttpOutbound para marcar integraciones HTTP salientes.
Policies declarativas (resilience.policies.*) centralizadas en application.properties y leídas por ResiliencePolicyConfig.
Overrides por método (FQCN/metodo/...) cuando una operación requiere ajuste fino.
Fallbacks corporativos del módulo resilience-starter.
Errores estandarizados: ResilienceException → ApiError.
Patrones de DLQ/Retry topics, SAGA/Outbox e idempotencia en integraciones asíncronas.
Todos los arquetipos REST/Camel/EDA deben venir preconfigurados con estos mecanismos.
## 5.1 Modelo estándar de resiliencia HTTP outbound
Objetivo
Asegurar que toda integración HTTP outbound (hacia core bancario, sistemas de terceros, APIs de socios, etc.) se ejecute con políticas de resiliencia consistentes, declarativas y controladas de forma corporativa, evitando implementaciones manuales, duplicación de lógica y fallas silenciosas en producción.
El modelo se aplica exclusivamente en los adaptadores de infraestructura que implementan puertos de salida definidos en application/port/out. Ni el dominio ni la aplicación realizan invocaciones HTTP directas.
Lineamientos
Toda llamada HTTP saliente crítica (a core, terceros, partners, etc.) debe cumplir:
Estar encapsulada en un adaptador de infraestructura, por ejemplo:
infrastructure/proxy/<sistema>
implementando un puerto declarado en application/port/out.
Ejemplo:
ExternalUsersHttpClientAdapter implements ExternalUsersPort.
Declarar una política de resiliencia mediante la anotación:
@ResilientHttpOutbound(<POLICY>)
Usar obligatoriamente componentes estándar provistos por el framework:
@Timeout
@Retry (solo si el servicio externo es idempotente)
@CircuitBreaker
@Fallback (local o corporativo)
Está prohibido implementar:
timeouts manuales,
retries explícitos,
circuit breakers,
lógica de espera (sleep),
bucles de reintentos, dentro de código de infraestructura o aplicación.
Toda resiliencia outbound debe usar MicroProfile Fault Tolerance y las políticas declarativas del framework.
Catálogo corporativo de políticas de resiliencia
Se define un catálogo estándar de políticas de resiliencia:
ResiliencePolicies.HTTP_FAST → policy.http.fast
Integraciones rápidas e idempotentes (ej: consultas cacheables).
ResiliencePolicies.HTTP_MEDIUM → policy.http.medium
Default corporativo para la mayoría de integraciones síncronas.
ResiliencePolicies.HTTP_SLOW → policy.http.slow
Integraciones lentas o legacy pero inevitables (ej: mainframes/host).
Políticas específicas por cliente/downstream (ej: ResiliencePolicies.EXTERNAL_USERS).
Configuración declarativa desde application.properties
Cada política se configura (timeout, retries, circuit breaker) mediante propiedades:
resilience.policies.policy.http.fast.*
resilience.policies.policy.http.medium.*
resilience.policies.policy.http.slow.*
resilience.policies.external-users.*
La clase ResiliencePolicyConfig (en resilience-starter) interpreta estas políticas:
@ApplicationScoped
```java
public class ResiliencePolicyConfig {
public ResiliencePolicy get(String policyId) { ... }
```
}
Documentar y trazar qué política se aplica.
Permitir un futuro enlace automático entre policy lógica y valores efectivos MP FT.
Configuración efectiva de MP Fault Tolerance
La configuración efectiva de tiempo de espera, reintentos y circuit breaker se realiza mediante:
Defaults globales mp.fault.tolerance.*.
Overrides específicos por método:
com.mi.paquete.MiServicio/miMetodo/Timeout/value=...
com.mi.paquete.MiServicio/miMetodo/Retry/maxRetries=...
com.mi.paquete.MiServicio/miMetodo/CircuitBreaker/failureRatio=...
La prioridad de aplicación es:
Overrides por método
Policies declarativas (resilience.policies.*)
Defaults globales MP FT
Aplicación en runtime
El interceptor @ResilientHttpOutbound
Obtiene la política mediante policyConfig.get(policyId).
Aplica internamente las anotaciones MP FT.
Registra en logs qué política fue aplicada.
Ejemplo de log:
DEBUG ResilientHttpOutbound policy='external-users'
applied to ExternalUsersHttpClientAdapter#getUsers
Gobernanza y cumplimiento:
Obligatorio:
Todas las integraciones HTTP outbound deben declarar una política de resiliencia.
Las llamadas deben existir únicamente en infrastructure/proxy/* (o adaptadores equivalentes), implementando puertos en application/port/out.
No se permite lógica técnica de resiliencia dentro de:
domain
application
casos de uso
Prohibido:
Implementar manualmente timeouts, retries, circuit breakers.
Colocar lógica HTTP en domain o application.
Omitir @ResilientHttpOutbound.
El pipeline CI/CD puede validar:
Presencia de puertos Application Outbound.
Uso obligatorio de políticas sobre adaptadores HTTP.
Ubicación correcta de invocaciones outboun
Beneficio
Políticas homogéneas y auditables en todos los servicios.
Eliminación de retries/timeout ad-hoc.
Trazabilidad clara de políticas aplicadas.
Flexibilidad para ajustar comportamiento vía properties sin modificar código.
Acoplamiento cero entre negocio y tecnología.
Lo más importante:
Ni domain ni application se ven contaminados con lógica HTTP o resiliencia:
solo coordinan puertos y delegan a adaptadores en infraestructura.
Esto mantiene la pureza del bosque arquitectónico (DDD + Hexagonal), y permite reemplazar APIs externas, protocolos o mecanismos de comunicación sin modificar la lógica del negocio ni los casos de uso.
Ver ejemplo
## 5.2 Uso obligatorio de Timeout, Retries y Circuit Breaker
Lineamientos
Timeouts (@Timeout)
Todo método que invoque un sistema externo debe tener un timeout finito.
Valores de referencia (ajustables por política):
FAST: ≤ 500 ms
MEDIUM: 500–1500 ms
SLOW: hasta 5000 ms (casos excepcionales y justificados).
El timeout efectivo debe quedar configurado vía:
Anotación @Timeout sin valor (usa mp.fault.tolerance.timeout.value), o
Override por método (…/Timeout/value).
Reintentos (@Retry)
Se permiten reintentos solo para operaciones idempotentes (ej: GETs, POST de lectura técnica, validaciones).
Máximo de intentos:
FAST: 0–1 extra (total 1–2 ejecuciones).
MEDIUM: hasta 2 reintentos.
SLOW: hasta 3 reintentos, siempre con backoff.
Deben definirse explícitamente:
maxRetries, delay, jitter, maxDuration (para evitar reintentos infinitos).
Se debe evitar reintentar ante:
Errores de validación (400).
Errores de autenticación/autorización (401/403).
Errores de negocio claros (422, reglas de negocio violadas).
Circuit Breaker (@CircuitBreaker)
Todos los métodos que dependan de un externo crítico deben protegidos por CB.
Parámetros mínimos obligatorios:
requestVolumeThreshold (ej: 10).
failureRatio (ej: ≥ 0.5).
delay para semi-open (ej: 5s).
Objetivo:
Evitar tormentas de requests a un servicio ya degradado.
Proteger el pool de threads y la latencia general.
Fallbacks (@Fallback)
Debe declararse un fallback explícito para operaciones donde exista una degradación segura (lista vacía, respuesta cacheada, mensaje técnico controlado, etc.).
Si no hay degradación segura posible (operación crítica de escritura), el fallback debe:
Registrar adecuadamente el error.
Propagar una excepción técnica estandarizada (ResilienceException) para ser convertida en ApiError.
Ver ejemplo
## 5.3 Fallbacks corporativos (resilience-starter)
1. EmptyList – degradación segura para GET
@ApplicationScoped
```java
public static class EmptyList implements FallbackHandler<List<?>> {
public List<?> handle(ExecutionContext ctx) {
return Collections.emptyList();
```
}
}
2. PropagateAsUncheckedString – error crítico estandarizado
@ApplicationScoped
```java
public static class PropagateAsUncheckedString implements FallbackHandler<String> {
public String handle(ExecutionContext ctx) {
Throwable cause = ctx.getFailure();
```
throw new ResilienceException(
"Downstream failure: " + (cause != null ? cause.getMessage() : "unknown"),
cause
```java
);
```
}
}
3. Error corporativo
El GlobalExceptionMapper convierte ResilienceException en:
{
"code": "DOWNSTREAM_ERROR",
"message": "Error al invocar un servicio externo.",
"detail": "Downstream failure: <cause>",
"traceId": "...",
"correlationId": "...",
"businessId": null
}
## 5.4 Interceptor corporativo @ResilientHttpOutbound
Marca integraciones HTTP salientes:
@ResilientHttpOutbound
@Interceptor
@Priority(APPLICATION + 10)
```java
public class ResilientHttpOutboundInterceptor { ... }
```
Funciones:
Identificar métodos externos.
Cargar la política declarada.
Registrar la política en logs.
Futuro: Enlazar policy → configuración efectiva MP FT.
## 5.5 Estándares para DLQ/Retry topics, SAGA/Outbox e idempotencia
Estos lineamientos aplican principalmente a rutas Camel con Kafka (u otros brokers).
Lineamientos
DLQ y Retry topics
Toda ruta de consumo desde Kafka (u otro broker) debe definir:
Topic principal de consumo (ej: campaign.events.v1).
Retry topic (ej: campaign.events.retry.v1) para reintentos asíncronos con backoff.
Dead-letter topic (DLT) (ej: campaign.dlt.v1) para mensajes que agotaron reintentos.
La política de reintentos en Kafka/Camel debe estar alineada con:
Los lineamientos de reintentos de MP FT (no reintentar errores de negocio).
La capacidad del consumidor y el SLA del proceso.
SAGA / Outbox
Para operaciones que impliquen consistencia entre base de datos y eventos, el patrón recomendado es:
Outbox: escribir evento en una tabla outbox dentro de la misma transacción que la operación de negocio.
Un publicador (Camel o servicio batch/reactivo) lee la tabla outbox y publica al broker.
Para orquestaciones largas multi-servicio:
Usar patrón SAGA (coreografía u orquestación).
Cada paso debe ser compensable; las compensaciones deben ser explícitas y auditables.
Idempotencia
Todos los consumidores de eventos críticos deben ser idempotentes:
Uso de businessId o eventId para deduplicar.
Tablas de “processed messages” o uso de claves de negocio únicas.
No se debe depender exclusivamente de “al menos una vez” del broker sin controles adicionales.
DLT y observabilidad
Los mensajes enviados a DLT deben:
Incluir traceId, correlationId, businessId.
Registrar el motivo del envío (tipo de error, stack técnico depurado, código de negocio si aplica).
Debe existir monitoreo y alertas sobre volumen y tasa de mensajes en DLT.
## 5.6 Políticas de reintentos: máximos, backoff y cuándo NO reintentar
Lineamientos
Máximos globales (referenciales)
MP FT (síncrono HTTP):
mp.fault.tolerance.retry.max-retries ≤ 3.
mp.fault.tolerance.retry.max-duration acorde al SLA del endpoint (ej: ≤ 5s).
Mensajería (Kafka/Camel):
Configurar número de reintentos acotado y backoff progresivo.
Evitar reintentos sin límite de tiempo o número.
Backoff y Jitter
Reintentos deben usar backoff y jitter para evitar thundering herd.
Parámetros de ejemplo (ajustables por política):
mp.fault.tolerance.retry.delay=300
mp.fault.tolerance.retry.jitter=200
kafka.retry.backoff.ms=200   # equivalente en mundo Kafka
Cuándo NO reintentar
No reintentar:
Validaciones de entrada fallidas (400, DTO inválido, campos requeridos faltantes).
Restricciones de negocio (limites de riesgo, saldo insuficiente, reglas de KYC).
Errores de autenticación/autorización (401/403).
Sí se pueden reintentar (con límites):
Timeouts, errores de red transitorios.
Errores 5xx del proveedor externo, cuando la operación es idempotente.
Errores de capacidad (429/503) con backoff adecuado.
Documentación y trazabilidad
Las políticas de reintentos (síncronas y asíncronas) deben ser:
Documentadas en el diseño de la integración.
Trazables en métricas (ej: número de reintentos, tasa de fallos definitivos).
Ver ejemplo
# 6. Lineamientos de calidad, testing y DevSecOps
Objetivo
Definir un modelo estándar de calidad, pruebas y DevSecOps para todos los servicios generados por el Framework de Integración, garantizando que:
El código sea testeable, mantenible y seguro por defecto.
Existan niveles mínimos de cobertura por tipo de servicio (REST, Camel, EDA).
Las pruebas (unitarias, de integración y de contrato) se integren de forma natural con Quarkus.
Los pipelines de CI/CD apliquen gates de seguridad y calidad (SAST, SCA, SBOM, container scan, firma de imagen).
El despliegue a ambientes superiores (QA/PROD) esté bloqueado automáticamente cuando se incumplan los estándares.
Todos los arquetipos del framework deben generar por defecto:
Estructura de testing alineada al Test Pyramid (más unitarias, menos e2e).
Configuración base de quarkus-junit5 + RestAssured.
Hooks de DevSecOps integrables con la plataforma corporativa (Sonar, Veracode, Prisma, etc.).
## 6.1 Estrategia de pruebas y cobertura mínima
Lineamientos
Pirámide de pruebas obligatoria
Todo servicio debe seguir esta pirámide:
Pruebas unitarias (base):
Dominios, servicios de aplicación, mappers, validaciones.
Pruebas de integración/componentes:
Recursos REST, integraciones Camel, clientes a externos (simulados).
Pruebas de contrato:
Entre productor ↔ consumidor (REST / eventos).
Pruebas end-to-end (e2e):
Ejecutadas en pipelines de sistema/regresión (no en el repo del microservicio).
Cobertura mínima recomendada
Por tipo de proyecto:
Servicio REST puro:
Cobertura global líneas: ≥ 80%.
Cobertura en capa de dominio/aplicación: ≥ 90%.
Servicio Camel de integración:
Cobertura en beans (procesors, services): ≥ 80%.
Rutas Camel críticas: escenarios felices + errores.
Servicio EDA (Kafka/PubSub):
Handlers/consumers: ≥ 80%.
Lógica de SAGA/outbox: pruebas con escenarios de compensación.
Regla: los pipelines deben verificar como mínimo la cobertura global y la cobertura de paquetes domain y application.
Tipos de pruebas obligatorias por servicio
REST:
Unitarias de dominio.
Tests de recurso REST con @QuarkusTest + RestAssured.
Contract tests (OpenAPI/consumer contracts).
Camel:
Unitarias de beans.
Tests de rutas con Camel Quarkus (mock endpoints).
EDA:
Unitarias de handlers.
Tests con Testcontainers (Kafka) para flujos críticos.
## 6.2 Lineamientos de pruebas unitarias e integración (Quarkus)
Lineamientos
Separación clara de tipos de test
Pruebas unitarias puras (rápidas):
No deben usar @QuarkusTest.
Usan JUnit 5 + Mockito u otro framework de mocks.
No levantan contexto de Quarkus, ni CDI, ni DevServices.
Pruebas de integración (Quarkus):
Usan @QuarkusTest.
Validan REST endpoints, wiring CDI, configuración real.
Deben evitar tocar sistemas externos reales (usar mocks/stubs).
Buenas prácticas específicas con Quarkus
No ubicar tests en src/main/java
Regla: todos los tests van en src/test/java con paquetes espejo a src/main/java.
Evitar que @QuarkusTest dependa de:
Kafka real.
Endpoints HTTPS externos de la oficina (certificados corporativos, proxies, etc.).
Para clientes externos (ExternalUserClient):
En unit tests: usar mocks (Mockito).
En integration tests: usar stub/WireMock o Testcontainers.
Naming y estructura
Por clase de producción debe existir al menos una clase de test:
GetExternalUsersService → GetExternalUsersServiceTest.
Nombres de métodos de test:
should_return_empty_list_when_external_fails()
should_call_external_users_with_policy_medium()
Evitar nombres genéricos tipo comTest y rutas “mágicas” como /hello de ejemplo:
Las pruebas deben apuntar a endpoints reales del servicio (/health, /v1/..., etc.).
Ejemplo de test unitario correcto (sin QuarkusTest, sin red)
GetExternalUsersService se testea con un stub de ExternalUserClient inyectado manualmente (constructor) o con Mockito, sin levantar Quarkus.
Ver ejemplo
## 6.3 Uso de Testcontainers y contract testing
Lineamientos
Testcontainers
Obligatorio para pruebas de integración con:
Kafka, bases de datos, Redis, etc.
Principios:
Los contenedores se levantan solo en tests anotados (slow tests) o en perfiles específicos (-P integration-tests).
Los tests deben ser repeatables: no depender del orden ni de datos “sucios”.
Ejemplos de uso:
Consumidores Kafka Camel (campaign.dlt.v1) probados con un Kafka Testcontainer.
Repositorios JPA/SQL probados con Postgres Testcontainer.
Contract testing
Para APIs REST:
Uso de OpenAPI + contratos de consumidor (ej: Pact).
Los servicios productores deben publicar su contrato como artefacto versionado.
Los consumidores deben validar sus expectativas contra el contrato oficial.
Para eventos:
Uso de AsyncAPI o esquemas versionados (Avro/JSON Schema).
Validación estructural de los mensajes producidos/consumidos.
Regla:
Ningún cambio incompatible (breaking) en un contrato REST/evento debe ser desplegado si los contract tests fallan.
## 6.4 DevSecOps y gates obligatorios en CI/CD
Lineamientos
Etapas mínimas de pipeline (por microservicio)
Build & Unit tests
mvn -B clean verify (sin integración lenta).
Debe ejecutar:
Pruebas unitarias.
Análisis de cobertura.
SAST + Code Quality
Análisis estático en SonarQube/SonarCloud o equivalente.
Revisión de vulnerabilidades en código (SQLi, XSS, etc.).
SCA (Software Composition Analysis)
Análisis de dependencias (Snyk, OWASP, Veracode SCA, etc.).
Reporte de vulnerabilidades en librerías open source.
SBOM
Generación de Software Bill of Materials (CycloneDX o similar).
Publicación como artefacto asociable al release.
Build & Scan de imagen de contenedor
Build de imagen (Docker/Buildpacks).
Escaneo de vulnerabilidades (Prisma/Trivy/Anchore).
Firma de imagen
Firma criptográfica (ej: Cosign, Notary).
Registro solo acepta imágenes firmadas.
Pruebas de integración / contract tests
Tests con Testcontainers.
Contract tests REST/EDA.
Despliegue a DEV + smoke tests
Health checks + endpoints críticos.
Promoción a QA/PROD
Solo si todos los gates previos se cumplen.
Gates obligatorios
SAST:
Ninguna vulnerabilidad de severidad Crítica puede quedar sin mitigar o documentar en excepción aprobada.
SCA:
Vulnerabilidades Críticas y Altas en dependencias bloquean el pipeline, salvo excepciones formales.
Container scan:
Imágenes con vulnerabilidades críticas en SO/base están bloqueadas.
Coverage:
Coverage global por debajo del umbral corporativo (ej. 80%) → bloquea merge a rama principal.
Contract tests:
Cualquier fallo → bloqueo automático de deployment.
Criterios para bloquear un despliegue
Un despliegue hacia QA/Producción debe ser bloqueado cuando:
Calidad de código / testing
mvn test o mvn verify falla.
Cobertura de tests por debajo de:
80% global.
90% en domain y application (ajustable por política).
Existen tests marcados como @Disabled en código productivo sin justificación/documentación.
Seguridad
SAST detecta vulnerabilidades Críticas no resueltas.
SCA detecta dependencias con vulnerabilidades Críticas/Altas sin plan de mitigación.
Escaneo de contenedor detecta issues Críticos no mitigados.
La imagen no está firmada o la firma no es válida.
Contratos y compatibilidad
Contract tests REST o EDA fallan (breaking change no controlado).
El servicio rompe compatibilidad de esquema con otros sistemas (según AsyncAPI/OpenAPI).
Operación y estabilidad
Fallan smoke tests de health (/health) o checks funcionales básicos.
No se cumple el checklist de resiliencia mínimo (Timeout + Retry + CircuitBreaker configurados para integraciones externas).
Regla: Los equipos no deben bypassear manualmente los gates de calidad/seguridad sin una excepción registrada, aprobada y con fecha de expiración.
## 6.5 Recomendaciones prácticas (lecciones aprendidas con Quarkus)
Mantener los tests rápidos y confiables
La mayoría de tests deben ser unitarios y ejecutar en segundos.
Los tests con @QuarkusTest y Testcontainers deben marcarse como “slow” o usar perfiles específicos de pipeline.
Evitar dependencias externas en los tests
Nunca depender de:
Certificados corporativos no instalados en local.
Proxies de oficina.
Brokers Kafka remotos.
Usar:
Stub/WireMock para HTTP.
Testcontainers para Kafka/DB.
Usar ejemplos alineados al servicio real
Evitar pruebas “de ejemplo” que apunten a /hello o endpoints inexistentes:
Generan ruido (errores 404/500) y confusión.
El arquetipo debe exponer:
/health funcional.
Uno o dos endpoints de negocio/ejemplos bien testeados.
Facilitar el trabajo del desarrollador
El arquetipo debe incluir:
Ejemplos de @QuarkusTest que compilen y pasen desde el día 0.
Configuración de test containers apagada por defecto en dev, activable solo con un perfil (-P it).
# 7. Lineamientos de contratos API (REST y eventos)
Objetivo
Definir un estándar único para el diseño, publicación, versionado y validación de contratos API, tanto para servicios REST como para integración basada en eventos (EDA), garantizando:
Consistencia entre equipos y dominios BIAN.
Evolución controlada sin romper consumidores (backward compatibility).
Validación automatizada mediante linters y contract testing.
Reutilización de modelos y esquemas en un catálogo corporativo.
Alineamiento con gobernanza, seguridad y observabilidad.
Todo servicio generado por el Framework de Integración debe exponer y/o consumir contratos que cumplan estos lineamientos.
## 7.1 Contratos REST (OpenAPI obligatorio)
Lineamientos
OpenAPI obligatorio (source of truth)
Todo servicio REST debe exponer un contrato OpenAPI 3.x.
El contrato se genera automáticamente desde:
Anotaciones JAX-RS (@Path, @GET, etc.).
DTOs tipados del paquete infrastructure.rest.dto.
Bean Validation (@NotNull, @Size, etc.).
Opcional: anotaciones MP OpenAPI (@Schema, @Operation, @APIResponse).
Rutas estándar:
/q/openapi – contrato técnico (JSON/YAML).
/q/swagger-ui – exploración del contrato (solo dev/staging).
El contrato publicado es el que captura el pipeline CI/CD.
Estructura mínima por endpoint
Operaciones deben documentar:
summary y description claros.
Parámetros (path, query, header) con tipo y ejemplo.
Códigos de respuesta (200, 400, 401, 403, 404, 422, 500).
Esquema tipado para request/response
Uso obligatorio del error estándar ApiError.
Uso obligatorio de modelos tipados
Prohibido: Map<String,Object>, Object, o modelos no tipados.
Los DTO de contrato deben vivir en:
com.<dominio>.infrastructure.rest.dto
Deben estar separados de:
Entidades de dominio (domain)
Modelos de persistencia (infrastructure.repository)
DTO de sistemas externos (infrastructure.proxy)
Cambios incompatibles → nueva versión:
/v1/... → /v2/...
Paquetes versionados:
infrastructure.rest.v1.dto
infrastructure.rest.v2.dto
Estándar de path y recursos
Paths en plural:
/customers, /campaigns, /products
Sub-recursos cuando aplique:
/customers/{id}/accounts
Prohibido: mezclar verbos en paths
(/getCustomer, /createCampaign)
Verbos vienen del método HTTP: GET/POST/PUT/DELETE.
Versionado de APIs REST
Versión obligatoria en ruta:
/v1, /v2, …
Cambios:
No rompientes (agregar campos opcionales) → misma versión.
Rompientes (remover campos, modificar tipos) → nueva versión.
Deprecación:
deprecated: true en OpenAPI.
Fecha de sunset en API Gateway.
Modelo estándar de errores
Todos los errores deben mapearse al tipo corporativo ApiError con:
code
message
traceId
details (opcional)
La conversión se centraliza en GlobalExceptionMapper.
Paginación, filtros y ordenamiento
Paginación:
Parámetros estándar: page, size.
Respuesta incluye: content, page, size, totalElements, totalPages.
Filtros y orden:
Parametrización simple por query (ej. status, sort).
Para casos complejos, evaluar especificaciones bien documentadas en OpenAPI.
Headers corporativos obligatorios
Todos los endpoints deben aceptar/propagar:
X-Correlation-Id
X-Business-Id (cuando aplique)
Deben estar documentados en OpenAPI como headers.
Ver ejemplo
## 7.2 Contratos de eventos (EDA – AsyncAPI)
Lineamientos
AsyncAPI obligatorio
Todo productor y consumidor de eventos corporativos debe documentar sus canales usando AsyncAPI (versión 2.x o superior).
El contrato debe incluir:
channels (topics/colas).
messages
payloads tipados.
components/schemas reutilizables.
Tipos de mensajes
Se distinguen, como mínimo:
Eventos de dominio: CustomerCreated, LoanApproved.
Comandos: CreateCampaignCommand.
Notificaciones: CampaignNotification.
El tipo de mensaje debe estar claro en el name y/o metadata del mensaje.
Naming estándar para topics/colas
Estructura recomendada:
<dominio>-<subdominio>.<agregado>.<evento>.<version>
Ejemplo: campaign.management.campaign.created.v1
Para DLT / retry topics:
<topic>.dlt.v1
<topic>.retry.v1
Esquemas de mensajes
Los payloads deben tener esquema explícito (JSON Schema / Avro) gestionado en un esquema registry corporativo (v2+ del framework).
Regla:
Cambios no rompientes → misma versión (v1), campos nuevos opcionales.
Cambios rompientes → nuevo topic/version (v2).
Metadatos mínimos en headers
Todo mensaje debe incluir en headers (o metadata):
traceId, correlationId, businessId.
eventType (cuando aplique).
source (servicio emisor).
Compatibilidad y evolución
Los consumidores deben ser tolerantes a campos nuevos (forward compatible).
El productor no debe eliminar campos hasta que lo permitan las reglas de deprecación corporativa.
Contrato AsyncAPI gestionado como archivo de proyecto
Se debe ubicar en:
src/main/resources/META-INF/resources/contracts/asyncapi-<service>.yaml
Ejemplo: src/main/resources/META-INF/resources/contracts/asyncapi-integration-service.yaml
Exposición estándar en runtime
Quarkus expone automáticamente los recursos estáticos ubicados en META-INF/resources.
Con la estructura anterior, el contrato AsyncAPI queda disponible en: /contracts/asyncapi-integration-service.yaml
Flujo corporativo para eventos
El pipeline de CI/CD del Framework de Integración debe:
Descargar el contrato desde /contracts/asyncapi-<service>.yaml.
Validarlo sintáctica y semánticamente (estructura AsyncAPI, schemas, channels).
Verificar consistencia con las reglas corporativas (naming de topics, versionado, metadatos obligatorios).
Publicarlo en el Catálogo de eventos corporativo, con su versión y fecha de publicación.
Ejecutar pruebas de contrato para productores y consumidores:
Productor: verifica que los mensajes publicados cumplen el schema definido (por ejemplo, para SagaDltMessage y EnterpriseDltMessage).
Consumidor: verifica que puede deserializar e interpretar correctamente la versión vigente (y cuando aplique, la previa).
## 7.3 Flujo estándar de publicación de contratos REST (OpenAPI como Source of Truth)
El Framework de Integración adopta OpenAPI 3.x como estándar obligatorio para la definición, documentación y publicación de contratos REST.
El contrato OpenAPI es considerado el source of truth técnico y funcional del servicio, y su generación, validación y publicación siguen el siguiente flujo estándar:
1. Generación automática del contrato (en tiempo de desarrollo y runtime)
Todos los microservicios generados mediante el arquetipo del framework deben incluir las extensiones:
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-smallrye-openapi</artifactId>
</dependency>
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-swagger-ui</artifactId>
</dependency>
Estas extensiones permiten:
Generar automáticamente el documento OpenAPI 3.x a partir de:
Anotaciones JAX-RS (@Path, @GET, @POST, etc.).
Modelos DTO del contrato (record o POJO en infrastructure.rest.dto).
Bean Validation (@NotNull, @Size, etc.).
Anotaciones de MP OpenAPI (@Schema, @Operation, @APIResponse).
2. Exposición del contrato OpenAPI en runtime
Con el servicio en ejecución, el contrato técnico puede consultarse en:
/q/openapi — Contrato OpenAPI (JSON/YAML)
Ejemplo en local:    http://localhost:8080/q/openapi
Este endpoint retorna el documento OpenAPI completo, que representa el contrato oficial del servicio. Es el insumo que consume el pipeline de CI/CD para validación y publicación.
/q/swagger-ui — UI de exploración del contrato
Ejemplo en local: http://localhost:8080/q/swagger-ui
Permite visualizar y probar los endpoints. La UI nunca se publica en productivo (solo dev/staging), pero sirve como herramienta de diseño y validación.
3. Estructura del contrato generado
El documento OpenAPI generado debe incluir:
Información del servicio:
info.title = <quarkus.application.name>
info.version = <service.version>
Endpoints versionados (/v1/...).
Request/response tipados usando los DTO del paquete:
com.….infrastructure.rest.dto.*
Header obligatorios:
X-Correlation-Id
X-Business-Id
Modelo de error estándar ApiError.
El framework exige enriquecer los recursos REST usando anotaciones opcionales de MP OpenAPI:
@Operation(summary = "Descripción corta", description = "Descripción detallada del endpoint.")
@APIResponse(responseCode = "200", description = "Respuesta exitosa")
4. Exportación automática del contrato (CI/CD)
El pipeline corporativo realiza:
Extracción automática del contrato
Ejecuta un curl o wget al endpoint /q/openapi, por ejemplo:
curl http://service-url/q/openapi -o openapi.yaml
Validación automática (linters y reglas corporativas)
Spectral para OpenAPI ruleset corporativo.
Validación sintáctica.
Validación de versionado y cambios rompientes.
Publicación en catálogos corporativos
API Gateway / API Portal.
Consola de documentación interna.
Registro de auditoría
Versión del contrato.
Fecha de publicación.
Equipo responsable.
Este proceso garantiza que el contrato publicado coincide exactamente con el que implementa el servicio, evitando divergencias entre equipos.
## 7.4 Linters y validación automática
Lineamientos
Uso obligatorio de linters para OpenAPI
Se debe usar Spectral (u otra herramienta corporativa) con un ruleset estándar:
Naming de paths, parámetros y esquemas.
Validación de códigos de respuesta.
Presencia de description/summary.
El linter debe ejecutarse en CI/CD:
api-contract-lint como job obligatorio en el pipeline.
Validación de AsyncAPI
AsyncAPI debe validarse sintáctica y semánticamente:
Estructura correcta de channels y messages.
Referencias y schemas consistentes.
Cualquier error de validación debe bloquear el pipeline.
Publicación automatizada
Los contratos aprobados (OpenAPI/AsyncAPI) se publican automáticamente en:
API Portal (para REST).
Catálogo de eventos (para EDA).
La publicación debe quedar registrada (auditoría) con versión y fecha.
## 7.5 Testing de contratos (API y eventos)
Lineamientos
Contract testing para APIs REST
Productores y consumidores deben contar con pruebas de contrato:
Productor: verifica que su implementación cumple el OpenAPI publicado.
Consumidor: verifica que el contrato del productor no cambió de manera rompiente.
Contract testing para eventos
Los productores deben probar que los mensajes publicados respetan el esquema de AsyncAPI / Schema Registry.
Los consumidores deben validar que pueden deserializar/interpretar correctamente los mensajes de la versión actual y, cuando aplique, la anterior.
Integración con CI/CD
Las pruebas de contrato deben ejecutarse antes de desplegar a entornos compartidos:
Job api-contract-test para REST.
Job event-contract-test para EDA.
Un fallo en contract testing bloquea el despliegue.
# 8. Lineamientos de uso de arquetipos, commons y starters
El Framework de Integración provee un conjunto de arquetipos, módulos commons y starters preconfigurados que los equipos deben utilizar obligatoriamente para garantizar consistencia, seguridad, trazabilidad, resiliencia, calidad y mantenibilidad.
Esta sección define cuándo usar cada tipo de servicio, qué starters son obligatorios, cómo deben declararse las dependencias y bajo qué condiciones se aceptan extensiones personalizadas.
## 8.1 Cuándo usar REST vs Camel vs EDA vs DB
El Framework establece reglas claras para elegir el tipo de integración adecuado.
La decisión depende del patrón de comunicación, características funcionales y requerimientos no funcionales.
### 8.1.1 REST (sincronía, request/response)
Usar REST cuando:
El consumidor requiere respuesta inmediata.
El flujo es CRUD, consulta o comando simple.
Se necesita exponer APIs corporativas para canales móviles, web o API Gateway.
El servicio es parte de un dominio BIAN orientado a operaciones sincrónicas (por ejemplo: Party, Customer, Product, Campaign Management).
No usar REST cuando:
El proveedor no garantiza tiempo de respuesta bajo SLAs (<1s).
El flujo requiere reintentos o compensaciones.
La cardinalidad puede generar carga masiva (ej. > 500 req/s sostenido).
Requiere orquestaciones complejas → usar Camel o SAGA.
## 8.1.2 Camel (integración, orquestación, mediación)
Usar Camel cuando se necesita:
Orquestación o workflow técnico con múltiples pasos.
Integración con sistemas legacy, colas, SFTP, HTTP, DB, archivos, etc.
Transformaciones de datos con responsabilidad técnica.
Manejo de SAGA, compensaciones, DLT, Enrichment, Split/Aggregate.
Reglas de resiliencia complejas:
Retries
Circuit Breakers
Timeouts diferenciados
Reprocesos
Procesamiento en pipelines EIP (Enterprise Integration Patterns).
Evitar usar Camel cuando:
La lógica es simple y cabe en un REST + servicio de aplicación.
La orquestación es puramente funcional → debe ir al dominio (application service).
## 8.1.3 EDA (Event-Driven Architecture – Kafka/PubSub/RabbitMQ)
Usar EDA cuando:
Se necesita desacoplar productores de consumidores.
Los flujos son asíncronos y no requieren respuesta inmediata.
El volumen de mensajes es alto o variable.
Requiere resiliencia nativa con:
Retry topics
DLT
Consumer groups
Se publican eventos de dominio o eventos auditables.
Se requiere integrar múltiples sistemas de forma distribuida.
No usar EDA cuando:
El negocio exige respuesta inmediata (debe ser REST).
El flujo no tolera asincronía.
No hay garantía de consumidores.
## 8.1.4 DB (acceso a datos)
Usar acceso directo a BD:
Solo para persistencia local dentro del propio Bounded Context.
Solo dentro de la capa Infrastructure Repository.
Con patrones:
Repository
Aggregate Root
Event Store (en evoluciones futuras)
Nunca usar DB para:
Integraciones entre sistemas
Intercambio de datos entre bounded contexts
Composición de funcionalidades
## 8.2 Starters obligatorios y opcionales
El Framework provee starters corporativos que entregan capacidades transversales estandarizadas.
Las capacidades que brindan estos starters no deben reimplementarse en los microservicios, y su política de uso es uniforme en todos los proyectos generados desde los arquetipos.
Los starters se aplican exclusivamente en los lugares definidos por la arquitectura hexagonal:
Entradas HTTP (REST controllers)
Adaptadores de salida (implementaciones de puertos outbound, en infrastructure/proxy/*)
Filtros globales de observabilidad y seguridad proporcionados por el framework
Ningún componente de domain o application debe contener anotaciones de estos starters, ni referencias a clases propias de ellos.
### 8.2.1 Starters obligatorios (todos los servicios)
1. logging-starter
Log JSON unificado
Campos obligatorios en MDC:
traceId
correlationId
businessId
Filtros y formato estándar
Integración automática con OpenTelemetry
2. security-starter
Autenticación y autorización JWT/OAuth2
Validación estándar de scopes por endpoint
Validación de headers corporativos
Politicas Zero Trust
3. tracing-starter
OpenTelemetry (traces distribuidas)
Propagación W3C (traceparent / tracestate)
Nombre de servicio estándar
4. metrics-starter
Micrometer
Prometheus
Métricas técnicas y de negocio por defecto
Exporter estandarizado
5. resilience-starter
@Timeout, @Retry, @CircuitBreaker, @Fallback
Políticas corporativas FAST / MEDIUM / SLOW + overrides
Anotación corporativa @ResilientHttpOutbound
Aplicación exclusiva en adaptadores outbound que implementan puertos en application/port/out
Prohibido aplicar resiliencia en domain o application
6. exception-starter
GlobalExceptionMapper
ApiError corporativo (RFC 7807)
Mapeo técnico-business unificado
Sin lógica duplicada de manejo de errores en los servicios
### 8.2.2 Starters obligatorios para Camel
Requeridos solo para servicios que utilicen Apache Camel como motor de integración.
camel-observability-starter
camel-opentelemetry
Instrumentación automática de rutas Camel
Métricas camel_route_exchanges_*
camel-kafka-starter
Configuración estándar de Kafka
Retries
Serializadores
Tópicos DLT corporativos para DLQ
camel-saga-starter
Implementación estándar de patrones SAGA
Compensaciones
Naming corporativo de tópicos
### 8.2.3 Starters opcionales
openapi-starter
Solo para servicios REST expuestos
Generación automática de OpenAPI
Validación de contratos
asyncapi-starter (ROADMAP)
Generación automática de contratos AsyncAPI
Validación de esquemas
db-starter
Repositorios JDBC/JPA
Flyway para control de esquemas
cache-starter
Redis / Caffeine
Control centralizado de expiraciones
Métricas de caché
## 8.3 Reglas de dependencia – siempre vía Integration BOM
Para garantizar consistencia y evitar versiones divergentes:
Todas las dependencias deben resolverse vía:
corporate-bom
integration-bom
pom del microservicio
Prohibido:
Declarar versiones manualmente.
Usar dependencias que no estén aprobadas en el BOM.
Incorporar librerías externas sin revisión técnica.
Si una dependencia no está en el BOM:
Se solicita vía ticket técnico.
Arquitectura revisa seguridad, licenciamiento, CVEs, trazabilidad.
Se agrega al BOM solo si cumple estándares corporativos.
análisis forense y cumplimiento regulatorio.
### 8.3.1 BOM corporativo (nivel banco)
Rol:
Define las versiones aprobadas a nivel organización para librerías genéricas, no específicas de integración. Lo consumen tanto proyectos de integración como otros (batch, data, etc.).
Qué tipo de cosas van aquí:
Librerías genéricas de Java:
Jackson (com.fasterxml.jackson.core:jackson-databind, etc.)
Apache Commons (org.apache.commons:commons-lang3, etc.)
Logging estándar:
Puentes slf4j, logback/log4j-bridge, si se usa.
Seguridad genérica (si hay libs transversales):
nimbus-jose-jwt u equivalentes aprobadas.
Testing base:
org.junit.jupiter:junit-jupiter
org.mockito:mockito-core
org.assertj:assertj-core
Utilitarios comunes corporativos:
Algún commons-core interno de la organización, si existe.
Importante:
El BOM corporativo no debería saber de Quarkus, Camel, Kafka directamente.
Es el “piso” común para todo el banco.
análisis forense y cumplimiento regulatorio.
## 8.3.2 BOM de Integración (Framework de Integración)
Este es el BOM que sí conocen los equipos de integración y que tus arquetipos van a usar.
Rol:
Define la stack de integración estándar:
Versión de Quarkus
Versión de Camel (Camel Quarkus)
Versión de Kafka clients
Versión de librerías de observabilidad (Micrometer, OTel)
Los módulos “cross” y starters corporativos.
Ejemplo:
<dependencyManagement>
<dependencies>
<!-- 1. Importa el BOM corporativo -->
<dependency>
<groupId>com.compartamos</groupId>
<artifactId>corporate-bom</artifactId>
<version>${corporate.bom.version}</version>
<type>pom</type>
<scope>import</scope>
</dependency>
<!-- 2. Importa BOMs de vendor (Quarkus, Camel, etc.) -->
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-bom</artifactId>
<version>${quarkus.version}</version>
<type>pom</type>
<scope>import</scope>
</dependency>
<dependency>
<groupId>org.apache.camel.quarkus</groupId>
<artifactId>camel-quarkus-bom</artifactId>
<version>${camel.quarkus.version}</version>
<type>pom</type>
<scope>import</scope>
</dependency>
<!-- 3. Versiones estándar de stack de integración -->
<dependency>
<groupId>io.micrometer</groupId>
<artifactId>micrometer-registry-prometheus</artifactId>
<version>${micrometer.version}</version>
</dependency>
<dependency>
<groupId>io.opentelemetry</groupId>
<artifactId>opentelemetry-api</artifactId>
<version>${otel.version}</version>
</dependency>
<!-- 4. Starters / commons del framework -->
<dependency>
<groupId>com.compartamos.framework</groupId>
<artifactId>logging-starter</artifactId>
<version>${framework.version}</version>
</dependency>
<dependency>
<groupId>com.compartamos.framework</groupId>
<artifactId>security-starter</artifactId>
<version>${framework.version}</version>
</dependency>
<dependency>
<groupId>com.compartamos.framework</groupId>
<artifactId>tracing-starter</artifactId>
<version>${framework.version}</version>
</dependency>
<!-- etc. -->
</dependencies>
</dependencyManagement>
¿Qué se define aquí?
Versiones de:
extensiones Quarkus (resteasy, openapi, micrometer, otel, jwt, etc.)
camel-quarkus (kafka, timer, rest, etc.)
kafka-clients
micrometer / otel si tienes libs extra
starters del framework: logging-starter, resilience-starter, exception-starter, etc.
Todo aquello que se defina que los proyectos usen sin poner versión en su POM.
Regla de oro:
Si un microservicio de integración va a usar una librería del framework, su versión ya está en el BOM de Integración.
## 8.3.3 POM del proyecto (ms-campaign, ms-loan, etc.)
Este es el POM del microservicio generado por el arquetipo.
Rol:
Ser lo más “delgado” posible: no define versiones, solo el qué usa.
Ejemplo:
<project>
<!-- ... -->
<dependencyManagement>
<dependencies>
<!-- Solo importas tu BOM de Integración -->
<dependency>
<groupId>com.compartamos.framework</groupId>
<artifactId>integration-bom</artifactId>
<version>${integration.bom.version}</version>
<type>pom</type>
<scope>import</scope>
</dependency>
</dependencies>
</dependencyManagement>
<dependencies>
<!-- Quarkus core -->
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-resteasy-reactive</artifactId>
</dependency>
<!-- Observabilidad -->
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-micrometer-registry-prometheus</artifactId>
</dependency>
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-opentelemetry-exporter-otlp</artifactId>
</dependency>
<!-- Camel + Kafka -->
<dependency>
<groupId>org.apache.camel.quarkus</groupId>
<artifactId>camel-quarkus-kafka</artifactId>
</dependency>
<!-- Starters del framework -->
<dependency>
<groupId>com.compartamos.framework</groupId>
<artifactId>logging-starter</artifactId>
</dependency>
<dependency>
<groupId>com.compartamos.framework</groupId>
<artifactId>resilience-starter</artifactId>
</dependency>
<!-- Módulos internos -->
<dependency>
<groupId>com.compartamos.process.campaign</groupId>
<artifactId>campaign-domain</artifactId>
</dependency>
<dependency>
<groupId>com.compartamos.process.campaign</groupId>
<artifactId>campaign-application</artifactId>
</dependency>
<dependency>
<groupId>com.compartamos.process.campaign</groupId>
<artifactId>campaign-infrastructure</artifactId>
</dependency>
<!-- Test (sin versión, vienen del BOM) -->
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-junit5</artifactId>
<scope>test</scope>
</dependency>
<dependency>
<groupId>org.testcontainers</groupId>
<artifactId>postgresql</artifactId>
<scope>test</scope>
</dependency>
</dependencies>
</project>
Reglas para el POM del proyecto:
No poner <version> en las dependencias que ya están cubiertas por el BOM de Integración.
Solo declarar las dependencias que el MS realmente usa
No introducir versiones nuevas (si se necesita una nueva lib/version → se agrega primero al BOM de integración o al corporativo).
Usar siempre los starters y módulos cross que ya están estandarizados.
## 8.4 Criterios para aceptar extensiones/customizaciones
Para mantener el framework estable:
Se aceptan extensiones solo si:
Aportan valor transversal a múltiples equipos.
No duplican capacidades existentes en el framework.
Cumplen estándares de:
Seguridad (SAST, CVE-score, licencias).
Observabilidad (traces, metrics, logs).
Resiliencia.
Documentación.
Tienen código testeado y con calidad mínima:
80% cobertura.
Documentación Javadoc / README.
Incluyen test de integración con Testcontainers.
No se aceptan extensiones cuando:
Solo resuelven un caso puntual (no corporativo).
Aumentan acoplamiento a proveedores no aprobados.
Introducen duplicidad o rompen principios del framework.
No cumplen estándares mínimos de seguridad.
## 8.5 Reglas para publicación de nuevos componentes
Todo componente nuevo (starter, librería, BOM update) debe incluir:
Documentación completa del propósito y alcance.
Ejemplo funcional (código + test).
Validación de compatibilidad con versiones existentes.
Pipeline de CI/CD propio (unit + integration + validation).
Revisiones de arquitectura.
# 9. Checklists de cumplimiento por tipo de servicio
Objetivo
Definir checklists mínimos de cumplimiento por tipo de servicio generado con el Framework de Integración (REST, Camel, EDA), así como el mecanismo de validación automática (CI/CD) y revisiones técnicas antes de promover a entornos compartidos (QA, PRE, PROD).
Cada checklist está dividido en items C (Crítico, obligatorio) y R (Recomendado).
## 9.1 Checklist para servicio REST
Ámbito: microservicio HTTP expuesto (por ejemplo, integration-service con recursos /v1/campaigns-alt, /v1/external/users-alt).
Dominio y arquitectura
[C] El servicio sigue el modelo DDD + Hexagonal:
Controladores REST en infrastructure.rest o infrastructure.rest.<contexto>.
Casos de uso / servicios de aplicación en application.service o application.usecase.
Modelo de dominio en domain.*.
Adaptadores externos (REST Client, repositorios, Kafka, etc.) en infrastructure.*.
[C] No hay lógica de dominio en los controladores REST (solo orquestación y mapeo DTO ↔ dominio).
[R] El servicio está alineado a un dominio / Bounded Context BIAN claramente identificado.
Contratos y versionado
[C] El servicio expone contrato OpenAPI 3.x en /q/openapi.
[C] Todos los endpoints públicos están versionados en la ruta (/v1/..., /v2/...).
[C] Los modelos de contrato (DTO) viven en un paquete tipo ...infrastructure.rest.dto y no se reutilizan entidades de dominio o de persistencia.
[C] Se usa el modelo estándar ApiError para errores, documentado en OpenAPI.
[R] OpenAPI anotado con @Operation, @APIResponse, @Schema para mejorar documentación.
Seguridad
[C] El servicio usa el security-starter (JWT/OAuth2) salvo servicios explícitamente marcados como internos sin autenticación.
[C] Las operaciones sensibles validan scopes/roles mínimos según lineamientos del dominio.
[C] Los headers corporativos X-Correlation-Id y X-Business-Id se aceptan y/o generan, y se propagan a servicios downstream cuando aplique.
[R] Todas las rutas sensibles están protegidas por políticas de seguridad a nivel API Gateway.
Logging, trazabilidad y métricas
[C] El servicio incluye el logging-starter y tracing-starter:
Logs en formato JSON.
Campos mínimos en log: traceId, correlationId, businessId.
Filtro de trazabilidad activo (ej. TraceFilter).
[C] El servicio expone métricas vía Micrometer + Prometheus en /metrics.
[C] Métricas HTTP (latencia, tasa, errores) están expuestas y visibles en los dashboards estándar del framework.
[R] El servicio define al menos 1 métrica de negocio (ej. campaigns_queries_total, external_users_requests_total).
Resiliencia
[C] Las llamadas outbound HTTP usan el resilience-starter:
@ResilientHttpOutbound con política definida (FAST, MEDIUM, SLOW o específica).
MP Fault Tolerance (@Timeout, @Retry, @CircuitBreaker, @Fallback) configurado según lineamientos.
[R] Se ha definido fallback lógico para integraciones críticas (degradación controlada).
Calidad y pruebas
[C] Pruebas unitarias con cobertura mínima definida (ej. ≥ 70% líneas/casos de uso core).
[C] Pruebas de integración básicas (REST + dependencias mínimas) usando Testcontainers u otro mecanismo estándar.
[C] Pruebas de contrato REST (api-contract-test) ejecutadas y exitosas.
[R] Pruebas de carga mínima / smoke (por ejemplo, N peticiones concurrentes a endpoints críticos).
## 9.2 Checklist para integración Camel
Ámbito: Integraciones construidas con Apache Camel dentro del servicio (routes de orquestación, SAGA, DLT, transformación).
Arquitectura y diseño de rutas
[C] Las rutas Camel se declaran en clases RouteBuilder ubicadas en paquetes application.routing, application.saga o infrastructure.integration, según el caso.
[C] No se mezcla lógica de negocio compleja dentro de la ruta; la lógica de dominio se delega a servicios/clases de aplicación.
[R] Se mantiene un naming consistente de rutas (routeId) siguiendo patrón <contexto>-<acción>-<tipo> (ej. campaign-saga-orchestrator, campaign-dlt-logger).
Kafka / endpoints técnicos
[C] Los endpoints Kafka usan configuraciones estándar definidas en application.properties (bootstrap servers, serializers, reintentos, etc.).
[C] Para DLT, se utilizan topics con naming estándar:
Ej.: campaign.dlt.v1, enterprise.dlt.v1.
[C] En rutas consumidoras, se define un errorHandler explícito (reintentos, backoff) o se documenta usar el default corporativo.
Observabilidad de rutas
[C] Camel tiene OpenTelemetry habilitado (camel.opentelemetry.enabled=true).
[C] Se usa camel.main.use-mdc-logging=true y use-breadcrumb=true para trazabilidad en logs.
[C] Las rutas críticas están etiquetadas con atributos de negocio en spans cuando aplique (ej. sagaId, businessId).
[R] Se exponen métricas de rutas (camel_route_exchanges, etc.) y se visualizan en los dashboards estándar.
DLT y manejo de errores
[C] Existe un flujo explícito de DLT para errores no recuperables (ej. seda:dlt → DLT Kafka → Enterprise DLT).
[C] Para mensajes enviados a DLT, se preserva:
traceId, correlationId, businessId.
Contexto de error (clase y mensaje de excepción).
Identificadores del flujo (routeId, sagaId).
[R] Se integra con el módulo de Enterprise DLT (EnterpriseDltMessage, repositorio, notificador).
Calidad y pruebas
[C] Hay pruebas de integración de rutas clave (entrada/salida esperada, DLT en caso de fallo).
[R] Se han realizado pruebas de resiliencia (timeouts, retries, caídas de Kafka).
## 9.3 Checklist para servicio EDA (Kafka / PubSub / RabbitMQ)
Ámbito: Productores/consumidores de eventos de negocio (Kafka u otro broker) implementados con el framework.
Contratos y esquema de eventos
[C] El servicio mantiene un contrato AsyncAPI en el repositorio, por ejemplo:
src/main/resources/META-INF/resources/contracts/asyncapi-<service>.yaml
[C] Los topics siguen el patrón estándar:
<dominio>-<subdominio>.<agregado>.<evento>.<version>
Ej.: campaign.management.campaign.created.v1
[C] Los topics de DLT / retry siguen el patrón:
<topic>.dlt.v1, <topic>.retry.v1.
[R] Los payloads usan esquema registrado en un Schema Registry corporativo cuando aplique (Avro, JSON Schema).
Metadatos de mensajes
[C] Todos los mensajes incluyen, como mínimo, en headers o metadata:
traceId
correlationId
businessId
source (nombre del servicio emisor)
[R] Se incluye un eventType o messageType para facilitar clasificación.
Productores
[C] Los productores capturan y loguean errores de publicación, con reintentos según políticas estándar.
[C] Los productores respetan la semántica de idempotencia cuando es requerida (clave de negocio adecuada).
Consumidores
[C] Los consumidores definen error handling explícito:
Reintentos locales.
Redirección a retry topic / DLT según criterios.
[C] Para mensajes no procesables, se envía un mensaje a DLT con contexto suficiente para análisis forense (EnterpriseDltMessage u otro modelo estándar).
[R] Se usan consumer groups alineados al contexto funcional (ej. integration-service-dlt-logger).
Calidad y pruebas
[C] Pruebas de contrato de eventos (event-contract-test) verifican que:
Mensajes publicados cumplen el esquema declarado en AsyncAPI/Schema Registry.
Consumidores pueden deserializar mensajes de la versión actual (y cuando aplique, de la anterior).
[R] Pruebas de carga mínima en consumidores críticos (volumen, latencia).
análisis forense y cumplimiento regulatorio.
## 9.4 Cómo se valida el cumplimiento (pipelines y revisiones técnicas)
El cumplimiento de los checklists no se valida manualmente “a ojo” en cada despliegue. El Framework de Integración define un esquema mixto de:
Validación automatizada en CI/CD
Revisiones técnicas periódicas (Architecture / Tech Review)
1. Validación automatizada en CI/CD
Cada proyecto generado por el arquetipo hereda un pipeline estándar con jobs que se mapean directamente a los ítems de estos checklists:
Quality & Tests
unit-tests (REST, Camel, EDA): ejecuta pruebas unitarias y de integración, con umbral de cobertura mínimo.
integration-tests (opcional por tipo de servicio).
Security & Compliance
sast-scan (SAST).
sca-scan (dependencias vulnerables).
sbom-generate + container-scan (imágenes).
Contracts
api-contract-lint → linters para OpenAPI (Spectral).
api-contract-test → contract testing de servicios REST.
event-contract-lint / asyncapi-validate → validación AsyncAPI.
event-contract-test → contract testing de eventos.
Observabilidad & Resiliencia
Checks básicos de health endpoints (/health).
Validación de exposición de métricas (/metrics) y presencia de métricas clave (HTTP, negocio).
Gates de promoción
Para pasar a QA / PRE / PROD, los jobs marcados como críticos deben estar en estado OK (fail → bloquea despliegue).
2. Revisiones técnicas (ARB / Tech Lead)
Además de los pipelines, se establecen revisiones técnicas periódicas:
Revisión de diseño (previa al desarrollo):
Verifica alineamiento al dominio BIAN, DDD, elección correcta de patrón (REST, Camel, EDA).
Revisión de implementación (previa a QA/PROD):
Muestra evidencias de cumplimiento de los checklists (extractos de código, configuración, dashboards).
Se valida uso correcto de starters (logging, security, tracing, resilience, exception, metrics).
Revisión de operación (post-go-live):
Se revisan dashboards de observabilidad (latencia, errores, DLT, disponibilidad).
Se ajustan políticas de resiliencia, métricas de negocio, alertas.
Regla general:
Un servicio no puede ser promovido a entornos compartidos si:
No pasa alguno de los checks críticos automatizados en CI/CD, o
Tiene observaciones graves en la revisión técnica (diseño o implementación).
# Anexos
## Ejemplo de Fundamento 1 — Modelo funcional basado en BIAN
Servicio “Campaign Management” (Gestión de Campañas)
Supongamos que el banco quiere construir un microservicio para administrar campañas de marketing. El servicio se va a llamar: process-or-experience-campaign
Alineándolo a BIAN debemos documentar lo siguiente:
Dominio BIAN
El dominio funcional al que pertenece este caso es:
BIAN Service Domain: Marketing Campaign Management (BIAN lo define como el dominio encargado de diseñar, ejecutar, monitorear y optimizar campañas de marketing).
Capacidad BIAN (BIAN Capability)
Dentro del dominio, BIAN define varias capacidades:
Ejemplos reales de BIAN:
Plan Campaign
Define Campaign
Execute Campaign
Monitor Campaign
Analyze Campaign Performance
Supongamos que el servicio aplica a: BIAN Capability: Execute Campaign
(porque lo que hace es activar campañas, ejecutar promociones, disparar mensajes multicanal, etc.)
Bounded Context (DDD)
El Bounded Context debe alinearse a la capacidad BIAN. Por lo tanto:
Bounded Context DDD: Campaign Execution
Este BC incluye:
Entidades como Campaign, ExecutionPlan, TargetList
Casos de uso como ExecuteCampaign, ScheduleCampaign, TriggerEventDelivery
Eventos como CampaignExecuted, CampaignFailed, CampaignScheduled
Cómo debe verse en la documentación del servicio (README.md)
Ejemplo directo para incluir en el README de cualquier servicio:
Contexto Funcional (BIAN + DDD)
Descripción funcional
Este microservicio implementa la capacidad BIAN Execute Campaign, permitiendo iniciar, monitorear y compensar la ejecución de campañas.
El servicio recibe solicitudes de activación desde el canal, ejecuta pasos secuenciales (e.g., reservar presupuesto, registrar campaña, publicar eventos) y asegura consistencia mediante patrones SAGA y DLT.
Modelo de dominio (resumen)
Entidades principales:
Campaign (BIAN: Campaign Instance)
BudgetAgreement (BIAN: Arrangement)
DeliveryInstruction
CampaignEvent
Casos de uso:
ExecuteCampaignUseCase
ScheduleCampaignUseCase
PublishCampaignEventUseCase
Eventos:
CampaignExecutedEvent
CampaignFailedEvent
CampaignEventPublishedEvent
## Ejemplo de Fundamento 2 — Dominio diseñado con DDD
Fundamento:
El modelo de dominio debe estar expresado en términos del negocio, con separación clara de responsabilidades y sin depender de frameworks técnicos (Quarkus, Camel, JPA, Kafka, etc.).
Contexto del ejemplo
BIAN Domain: Marketing Campaign Management
BIAN Capability: Execute Campaign
Bounded Context (DDD): CampaignExecutionContext
Carpeta del dominio en el arquetipo:
src/main/java/com/compartamos/campaign/domain
1. Componentes del dominio (qué hay adentro)
Dentro de domain/ diferenciamos:
Entidades → tienen identidad y ciclo de vida propio.
Value Objects → representan conceptos inmutables sin identidad propia.
Agregados → raíz de consistencia del dominio.
Eventos de dominio → describen hechos de negocio que ya ocurrieron.
Servicios de dominio → lógica que no pertenece claramente a una entidad.
Repositorios (interfaces) → contrato para recuperar y persistir agregados.
Una posible estructura:
domain/
model/
Campaign.java              // Entidad + Agregado raíz
CampaignBudget.java        // Value Object
CampaignStatus.java        // Enum
events/
CampaignActivatedEvent.java
CampaignExecutionFailedEvent.java
services/
CampaignExecutionService.java
repository/
CampaignRepository.java
2. Entidad + Agregado raíz: Campaign
```java
package com.compartamos.campaign.domain.model;
import java.time.LocalDate;
public class Campaign {
```
private final CampaignId id;              // Value Object de identidad
```java
private String name;
```
private CampaignBudget budget;           // Value Object
```java
private CampaignStatus status;
private LocalDate startDate;
private LocalDate endDate;
public Campaign(CampaignId id,
```
String name,
CampaignBudget budget,
LocalDate startDate,
LocalDate endDate) {
```java
if (id == null) throw new IllegalArgumentException("id is required");
if (name == null || name.isBlank()) throw new IllegalArgumentException("name is required");
if (budget == null) throw new IllegalArgumentException("budget is required");
```
if (startDate == null || endDate == null || endDate.isBefore(startDate)) {
```java
throw new IllegalArgumentException("invalid campaign period");
```
}
```java
this.id = id;
this.name = name;
this.budget = budget;
this.startDate = startDate;
this.endDate = endDate;
this.status = CampaignStatus.DRAFT;
```
}
// Reglas de negocio en el dominio (no en el controller)
```java
public void activate() {
```
if (!status.canTransitionTo(CampaignStatus.ACTIVE)) {
```java
throw new IllegalStateException("Campaign cannot be activated from status " + status);
```
}
if (budget.isExhausted()) {
```java
throw new IllegalStateException("Cannot activate campaign with exhausted budget");
```
}
```java
this.status = CampaignStatus.ACTIVE;
```
}
```java
public void close() {
```
if (!status.canTransitionTo(CampaignStatus.CLOSED)) {
```java
throw new IllegalStateException("Campaign cannot be closed from status " + status);
```
}
```java
this.status = CampaignStatus.CLOSED;
```
}
// getters… (sin setters públicos masivos)
}
Claves DDD aquí:
Las invariantes (periodo válido, presupuesto válido, transición de estados) viven en el dominio.
No hay ninguna importación de Quarkus, JPA ni Camel.
3. Value Object: CampaignBudget
```java
package com.compartamos.campaign.domain.model;
import java.math.BigDecimal;
public class CampaignBudget {
private final BigDecimal total;
private final BigDecimal consumed;
public CampaignBudget(BigDecimal total, BigDecimal consumed) {
```
if (total == null || total.compareTo(BigDecimal.ZERO) <= 0) {
```java
throw new IllegalArgumentException("total must be > 0");
```
}
if (consumed == null || consumed.compareTo(BigDecimal.ZERO) < 0) {
```java
throw new IllegalArgumentException("consumed must be >= 0");
```
}
if (consumed.compareTo(total) > 0) {
```java
throw new IllegalArgumentException("consumed cannot be greater than total");
```
}
```java
this.total = total;
this.consumed = consumed;
```
}
```java
public boolean isExhausted() {
return consumed.compareTo(total) >= 0;
```
}
```java
public CampaignBudget consume(BigDecimal amount) {
```
if (amount == null || amount.compareTo(BigDecimal.ZERO) <= 0) {
```java
throw new IllegalArgumentException("amount must be > 0");
```
}
```java
BigDecimal newConsumed = this.consumed.add(amount);
return new CampaignBudget(this.total, newConsumed);
```
}
```java
public BigDecimal getTotal() {
return total;
```
}
```java
public BigDecimal getConsumed() {
return consumed;
```
}
}
Claves DDD:
Inmutable ⇒ cada cambio devuelve un nuevo CampaignBudget.
Contiene lógica propia (isExhausted, consume) → no es solo un “DTO elegante”.
4. Evento de dominio: CampaignActivatedEvent
```java
package com.compartamos.campaign.domain.events;
import java.time.OffsetDateTime;
import com.compartamos.campaign.domain.model.CampaignId;
public class CampaignActivatedEvent {
private final CampaignId campaignId;
private final OffsetDateTime occurredAt;
public CampaignActivatedEvent(CampaignId campaignId, OffsetDateTime occurredAt) {
if (campaignId == null) throw new IllegalArgumentException("campaignId is required");
if (occurredAt == null) throw new IllegalArgumentException("occurredAt is required");
this.campaignId = campaignId;
this.occurredAt = occurredAt;
```
}
```java
public CampaignId getCampaignId() {
return campaignId;
```
}
```java
public OffsetDateTime getOccurredAt() {
return occurredAt;
```
}
}
Esto es un evento de negocio, no un mensaje Kafka aún. El adaptador de mensajería (Camel/Kafka) lo transformará a un mensaje técnico en infrastructure/messaging.
5. Repositorio (interface): CampaignRepository
```java
package com.compartamos.campaign.domain.repository;
import java.util.Optional;
import com.compartamos.campaign.domain.model.Campaign;
import com.compartamos.campaign.domain.model.CampaignId;
public interface CampaignRepository {
Optional<Campaign> findById(CampaignId id);
void save(Campaign campaign);
```
}
Aquí solo está el contrato de dominio. La implementación concreta (JPA, JDBC, Mongo, etc.) vive en infrastructure/repository.
6. Servicio de dominio: CampaignExecutionService
```java
package com.compartamos.campaign.domain.services;
import java.time.OffsetDateTime;
import com.compartamos.campaign.domain.events.CampaignActivatedEvent;
import com.compartamos.campaign.domain.model.Campaign;
import com.compartamos.campaign.domain.model.CampaignId;
import com.compartamos.campaign.domain.repository.CampaignRepository;
public class CampaignExecutionService {
private final CampaignRepository campaignRepository;
private final DomainEventPublisher eventPublisher;
public CampaignExecutionService(CampaignRepository campaignRepository,
```
DomainEventPublisher eventPublisher) {
```java
this.campaignRepository = campaignRepository;
this.eventPublisher = eventPublisher;
```
}
```java
public void activateCampaign(CampaignId campaignId) {
```
Campaign campaign = campaignRepository.findById(campaignId)
```java
.orElseThrow(() -> new IllegalArgumentException("Campaign not found " + campaignId));
```
campaign.activate(); // aplica reglas de negocio del agregado
```java
campaignRepository.save(campaign);
```
eventPublisher.publish(
new CampaignActivatedEvent(campaignId, OffsetDateTime.now())
```java
);
```
}
}
DomainEventPublisher también es una interfaz en el dominio.
La implementación que usa Camel/Kafka vive fuera, en infrastructure/messaging.
¿Qué demuestra este ejemplo para F2?
Separación clara de elementos DDD
Entidad/Agregado → Campaign
Value Object → CampaignBudget
Eventos → CampaignActivatedEvent
Servicios de dominio → CampaignExecutionService
Repositorio (interface) → CampaignRepository
Sin dependencias a frameworks
Solo java.* y tipos propios del dominio.
Quarkus, Camel, JPA, Kafka aparecen recién en infrastructure/….
Reglas de negocio en el dominio
Validación de estados, periodos, presupuesto, transiciones.
No en controllers REST, ni en routes Camel.
## Ejemplo de Fundamento 3 — Arquitectura Hexagonal (Ports & Adapters)
En el ejemplo usamos el bounded context:
BIAN Domain: Marketing Campaign Management
BIAN Capability: Execute Campaign
Bounded Context (DDD): CampaignExecutionContext
1. Dominio: repositorio DDD puro domain/repository)
domain/repository/CampaignRepository.java
```java
package com.compartamos.campaign.domain.repository;
import java.util.Optional;
import com.compartamos.campaign.domain.model.Campaign;
import com.compartamos.campaign.domain.model.CampaignId;
```
/**
* Repositorio de dominio (DDD).
* - Vive en domain.repository
* - Es 100% agnóstico a tecnología.
* - Expresa el lenguaje ubicuo del dominio.
*/
```java
public interface CampaignRepository {
Optional<Campaign> findById(CampaignId id);
void save(Campaign campaign);
```
}
Características:
Nombre y métodos definidos desde el negocio.
Sin Quarkus, sin JPA, sin Camel.
Puede ser usado por servicios de dominio si los hubiera.
2. Application: puerto de salida que “expone” el repositorio (application/port/out)
Patrón hexagonal:
El puerto de salida vive en application/port/out.
Extiende la interfaz de dominio, de modo que:
El dominio sigue hablando en términos de CampaignRepository.
Infraestructura implementa el port, no el dominio directamente.
application/port/out/CampaignRepositoryPort.java
```java
package com.compartamos.campaign.application.port.out;
import com.compartamos.campaign.domain.repository.CampaignRepository;
```
/**
* Puerto de salida de aplicación hacia persistencia.
*
* - Extiende el repositorio de dominio.
* - Es el contrato que la capa application expone hacia infrastructure.
* - Infraestructura implementa SIEMPRE esta interfaz, no directamente la del dominio.
*/
```java
public interface CampaignRepositoryPort extends CampaignRepository {
// En la mayoría de casos no agrega nada;
```
// pero aquí podrías añadir métodos específicos de aplicación si fuera necesario.
}
Con esto logramos:
domain.repository.CampaignRepository sigue siendo "la" abstracción DDD.
application.port.out.CampaignRepositoryPort es el port oficial hacia infra.
infrastructure sólo conoce CampaignRepositoryPort (capa application), nunca el paquete domain directamente.
3. Caso de uso usando el port de salida (application/usecase)
application/usecase/ActivateCampaignUseCaseImpl.java
```java
package com.compartamos.campaign.application.usecase;
import java.time.OffsetDateTime;
import com.compartamos.campaign.application.port.in.ActivateCampaignUseCase;
import com.compartamos.campaign.application.port.out.CampaignRepositoryPort;
import com.compartamos.campaign.application.port.out.DomainEventPublisherPort;
import com.compartamos.campaign.domain.events.CampaignActivatedEvent;
import com.compartamos.campaign.domain.model.Campaign;
import com.compartamos.campaign.domain.model.CampaignId;
public class ActivateCampaignUseCaseImpl implements ActivateCampaignUseCase {
private final CampaignRepositoryPort campaignRepository;
private final DomainEventPublisherPort eventPublisher;
public ActivateCampaignUseCaseImpl(CampaignRepositoryPort campaignRepository,
```
DomainEventPublisherPort eventPublisher) {
```java
this.campaignRepository = campaignRepository;
this.eventPublisher = eventPublisher;
```
}
@Override
```java
public void execute(String campaignIdRaw) {
CampaignId campaignId = new CampaignId(campaignIdRaw);
```
Campaign campaign = campaignRepository.findById(campaignId)
```java
.orElseThrow(() -> new IllegalArgumentException("Campaign not found " + campaignIdRaw));
```
campaign.activate(); // regla de negocio en el dominio
```java
campaignRepository.save(campaign);
```
eventPublisher.publish(
new CampaignActivatedEvent(campaignId, OffsetDateTime.now())
```java
);
```
}
}
Puntos para notar:
La aplicación depende de CampaignRepositoryPort (application/port/out), no del repo de dominio directamente, aunque el port lo extienda.
Para el dominio, el tipo relevante sigue siendo CampaignRepository.
4. Infraestructura: implementa el port (no la interfaz de dominio)
```java
package com.compartamos.campaign.infrastructure.repository;
import java.util.Optional;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.transaction.Transactional;
import com.compartamos.campaign.application.port.out.CampaignRepositoryPort;
import com.compartamos.campaign.domain.model.Campaign;
import com.compartamos.campaign.domain.model.CampaignId;
```
@ApplicationScoped
```java
public class JpaCampaignRepositoryAdapter implements CampaignRepositoryPort {
private final CampaignJpaMapper mapper;
private final CampaignPanacheRepository delegate;
public JpaCampaignRepositoryAdapter(CampaignJpaMapper mapper,
```
CampaignPanacheRepository delegate) {
```java
this.mapper = mapper;
this.delegate = delegate;
```
}
@Override
```java
public Optional<Campaign> findById(CampaignId id) {
```
return delegate.findByIdOptional(id.value())
```java
.map(mapper::toDomain);
```
}
@Override
@Transactional
```java
public void save(Campaign campaign) {
delegate.persist(mapper.toEntity(campaign));
```
}
}
Aquí se ve el patrón completo:
Infraestructura implementa CampaignRepositoryPort.
Esa interfaz a su vez extiende CampaignRepository de dominio, por lo que se respetan las reglas DDD.
Si mañana se crea otro adaptador (por ejemplo, uno in-memory para tests), también implementaría CampaignRepositoryPort.
5. Resumen del patrón:
1. Dominio (domain/repository)
Define interfaces de repositorio desde la perspectiva DDD.
No conoce application ni infrastructure.
2.   Application (application/port/out)
Crea puertos de salida que extienden esas interfaces de dominio.
Esos puertos son los únicos que se exponen hacia infraestructura.
3. Infraestructura (infrastructure/*)
Implementa siempre los puertos de application/port/out.
Nunca implementa directamente las interfaces del dominio.
Trabaja con JPA, Redis, HTTP, etc., pero todo escondido detrás del port.
## Ejemplo de Fundamento 4 — Clean Code + Lenguaje Ubicuo
Recordatorio del fundamento
El modelo de dominio debe ser expresivo, simple y sin duplicación.
Los nombres deben corresponder al lenguaje del negocio (ubiquitous language).
El código de dominio debe ser testeable sin levantar Quarkus ni Camel.
1. Ejemplo de “mal” dominio (anti-pattern)
// ❌ Ejemplo anti-patrón
```java
public class Camp {
private String id;
private String st;
private LocalDate sd;
private LocalDate ed;
public void chgSt(String s) {
this.st = s;
```
}
}
Problemas:
Nombres crípticos: Camp, st, sd, ed, chgSt.
No se reconoce el lenguaje del negocio (BIAN/marketing).
No hay reglas de negocio, solo setters.
No se entiende qué significa cambiar estado (¿validaciones? ¿invariantes?).
2. Ejemplo “bueno” con Clean Code + lenguaje ubicuo
```java
package com.compartamos.campaign.domain.model;
import java.time.LocalDate;
public class Campaign {
private final CampaignId id;
private CampaignStatus status;
private final LocalDate startDate;
private final LocalDate endDate;
public Campaign(CampaignId id,
```
CampaignStatus status,
LocalDate startDate,
LocalDate endDate) {
if (startDate.isAfter(endDate)) {
```java
throw new IllegalArgumentException("startDate must be before endDate");
```
}
```java
this.id = id;
this.status = status;
this.startDate = startDate;
this.endDate = endDate;
```
}
```java
public CampaignId getId() {
return id;
```
}
```java
public CampaignStatus getStatus() {
return status;
```
}
```java
public LocalDate getStartDate() {
return startDate;
```
}
```java
public LocalDate getEndDate() {
return endDate;
```
}
/**
* Regla de negocio:
* Solo se puede activar una campaña si:
* - hoy está dentro del periodo [startDate, endDate], y
* - aún no está ACTIVATED ni CLOSED.
*/
```java
public void activate(LocalDate today) {
```
if (today.isBefore(startDate) || today.isAfter(endDate)) {
```java
throw new IllegalStateException("Campaign is out of active period");
```
}
if (!status.isActivatable()) {
```java
throw new IllegalStateException("Campaign cannot be activated from status " + status);
```
}
```java
this.status = CampaignStatus.ACTIVATED;
```
}
}
Acompañado de un value object y un enum:
```java
package com.compartamos.campaign.domain.model;
public class CampaignId {
private final String value;
public CampaignId(String value) {
```
if (value == null || value.isBlank()) {
```java
throw new IllegalArgumentException("CampaignId cannot be null or blank");
```
}
```java
this.value = value;
```
}
```java
public String getValue() {
return value;
```
}
}
```java
package com.compartamos.campaign.domain.model;
public enum CampaignStatus {
```
DRAFT,
SCHEDULED,
ACTIVATED,
```java
CLOSED;
public boolean isActivatable() {
return this == SCHEDULED;
```
}
}
Puntos clave de Clean Code + lenguaje ubicuo:
Nombres:
Campaign, CampaignId, CampaignStatus, activate → hablan el mismo idioma que el negocio.
Método activate:
Expresa una regla de negocio clara: cuándo se puede activar una campaña.
Validaciones:
Invariantes en el constructor (fechas válidas).
Reglas en el método de dominio, no en el controlador REST.
Sin frameworks:
No hay anotaciones de Quarkus, JPA, Camel, ni nada técnico.
3. Test unitario del dominio sin Quarkus ni Camel
```java
package com.compartamos.campaign.domain.model;
import static org.junit.jupiter.api.Assertions.*;
import java.time.LocalDate;
import org.junit.jupiter.api.Test;
```
class CampaignTest {
@Test
void should_activate_campaign_when_scheduled_and_in_period() {
// given
```java
CampaignId id = new CampaignId("CAMP-123");
LocalDate start = LocalDate.of(2025, 1, 1);
LocalDate end   = LocalDate.of(2025, 12, 31);
Campaign campaign = new Campaign(id, CampaignStatus.SCHEDULED, start, end);
LocalDate today = LocalDate.of(2025, 6, 1);
```
// when
```java
campaign.activate(today);
```
// then
```java
assertEquals(CampaignStatus.ACTIVATED, campaign.getStatus());
```
}
@Test
void should_fail_to_activate_campaign_out_of_period() {
```java
CampaignId id = new CampaignId("CAMP-123");
LocalDate start = LocalDate.of(2025, 1, 1);
LocalDate end   = LocalDate.of(2025, 3, 31);
Campaign campaign = new Campaign(id, CampaignStatus.SCHEDULED, start, end);
LocalDate today = LocalDate.of(2025, 6, 1);
```
IllegalStateException ex = assertThrows(
IllegalStateException.class,
() -> campaign.activate(today)
```java
);
assertTrue(ex.getMessage().contains("out of active period"));
```
}
}
Tener en cuenta:
Solo se usa JUnit y la librería estándar de Java.
No hay @QuarkusTest, ni @Inject, ni @Entity.
El dominio se puede ejecutar y probar en cualquier contexto (IDE, pipeline, sin contenedor).
4. Cómo se ve el “lenguaje ubicuo” en la documentación del servicio
En el README del bounded context o en el ADR puedes documentar algo así:
Domino BIAN: Marketing Campaign Management
Capacidad BIAN: Execute Campaign
Bounded Context (DDD): CampaignExecutionContext
Entidades principales: Campaign, CampaignId, CampaignStatus
Eventos de dominio: CampaignActivatedEvent
Reglas clave:
“Una campaña solo se puede activar si está en estado SCHEDULED y la fecha actual está dentro del rango [startDate, endDate].”
Esto alinea:
Modelo conceptual de negocio,
Código (Campaign.activate),
Y documentación.
## Ejemplo modelo estándar de autenticación y autorización (JWT/OAuth2)
Caso de uso: Microservicio de campañas generado por el framework:
process-or-experience-campaign/
Este servicio expone un endpoint seguro que solo deben consumir integraciones autorizadas:
```java
package com.compartamos.campaign.infrastructure.rest;
```
@Path("/campaigns")
```java
public class CampaignResource {
```
@GET
@Path("/secure-ping")
@RolesAllowed(SecurityConstants.ROLE_INTEGRATION_API)
```java
public Response securePing() {
return Response.ok("Secure ping OK").build();
```
}
}
SecurityConstants.ROLE_INTEGRATION_API vive en cross.security:
```java
package com.compartamos.process.campaign.cross.security;
public final class SecurityConstants {
```
private SecurityConstants() {}
```java
public static final String ROLE_INTEGRATION_API = "INTEGRATION_API";
```
}
1. Configuración de seguridad en el servicio
En application.properties del servicio, el framework deja preconfigurado el uso de JWT:
quarkus.smallrye-jwt.enabled=true
mp.jwt.verify.publickey.location=classpath:/publicKey.pem
mp.jwt.verify.issuer=compartamos-dev
A tener en cuenta:
El servicio espera tokens firmados con la clave privada cuyo par público está en publicKey.pem.
Solo acepta tokens cuyo iss sea compartamos-dev (en dev; en QA/PROD cambiará).
Toda la validación de firma, exp, etc., la hace Quarkus a través de MicroProfile JWT.
2. JWT que se espera recibir
El consumidor (otro sistema o API gateway) debe enviar un JWT con un payload lógico de este estilo (solo el cuerpo, no el token real):
{
"iss": "compartamos-dev",
"sub": "user-123",
"groups": ["INTEGRATION_API", "OTHER_ROLE"],
"exp": 1731700000,
"iat": 1731696400,
"channel": "BACKOFFICE",
"tenant": "PE"
}
Puntos clave:
iss coincide con mp.jwt.verify.issuer.
groups contiene INTEGRATION_API, que es el rol exigido por @RolesAllowed.
channel y tenant son claims corporativos que el framework sabe interpretar.
El token está firmado con RS256 y validado vía publicKey.pem.
3. Cómo entra este token al microservicio
La llamada al endpoint sería algo como:
TOKEN="<jwt_valido_generado_por_el_IdP>"
curl -v "http://localhost:8080/campaigns/secure-ping" \
-H "Authorization: Bearer $TOKEN"
Flujo interno:
El request entra al servicio.
Quarkus valida el JWT automáticamente:
Firma (RS256) usando publicKey.pem.
iss, exp, iat, etc.
Si la validación falla → 401 Unauthorized antes de llegar al recurso.
4. Uso de JwtInfo y enriquecimiento del MDC
Una vez validado el JWT, el framework debe exponer una fachada JwtInfo:
```java
package com.compartamos.process.campaign.cross.security;
import jakarta.enterprise.context.RequestScoped;
import jakarta.inject.Inject;
import org.eclipse.microprofile.jwt.JsonWebToken;
```
@RequestScoped
```java
public class JwtInfo {
```
@Inject
```java
JsonWebToken jwt;
public String getUserId()  { return claim(SecurityConstants.CLAIM_USER_ID); }
public String getChannel() { return claim(SecurityConstants.CLAIM_CHANNEL); }
public String getTenant()  { return claim(SecurityConstants.CLAIM_TENANT); }
public boolean hasRole(String role) {
return jwt != null && jwt.getGroups() != null && jwt.getGroups().contains(role);
```
}
private String claim(String name) {
```java
if (jwt == null) return null;
Object v = jwt.getClaim(name);
return v != null ? v.toString() : null;
```
}
}
Y un filtro SecurityContextFilter que integra seguridad con logging:
```java
package com.compartamos.process.campaign.cross.security;
import jakarta.annotation.Priority;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import jakarta.ws.rs.Priorities;
import jakarta.ws.rs.container.ContainerRequestContext;
import jakarta.ws.rs.container.ContainerRequestFilter;
import jakarta.ws.rs.ext.Provider;
import org.jboss.logging.MDC;
```
@Provider
@Priority(Priorities.AUTHORIZATION)
@ApplicationScoped
```java
public class SecurityContextFilter implements ContainerRequestFilter {
```
@Inject
```java
JwtInfo jwtInfo;
```
@Override
```java
public void filter(ContainerRequestContext requestContext) {
String userId = jwtInfo.getUserId();
String channel = jwtInfo.getChannel();
```
if (userId != null) {
```java
MDC.put("userId", userId);
```
}
if (channel != null) {
```java
MDC.put("channel", channel);
```
}
// El TraceFilter se encarga del cleanup del MDC
}
}
Resultado: cada log generado por el servicio incluye no solo traceId y correlationId, sino también userId y channel provenientes del JWT.
Un log JSON típico podría verse así (simplificado):
{
"level": "INFO",
"message": "Secure ping OK",
"traceId": "a1b2c3d4e5f6...",
"correlationId": "CORR-2025-0001",
"businessId": "CAMP-123",
"userId": "user-123",
"channel": "BACKOFFICE"
}
5. Comportamiento esperado en los diferentes escenarios
a) Token válido y con rol correcto
Authorization: Bearer <token válido con groups=[INTEGRATION_API]>
Flujo:
JWT válido → pasa validación MP-JWT.
@RolesAllowed(INTEGRATION_API) → check OK.
Se ejecuta securePing().
Respuesta:
HTTP 200 OK
Body: "Secure ping OK"
b) Token inválido o expirado
Token mal firmado, iss incorrecto o exp vencido.
Flujo:
Falla validación MP-JWT.
Quarkus responde 401 sin llegar al recurso.
Respuesta:
HTTP 401 Unauthorized.
c) Token válido, pero sin el rol requerido
groups no contiene INTEGRATION_API.
Flujo:
Firma y claims válidos → autenticación OK.
@RolesAllowed(INTEGRATION_API) falla en autorización.
Respuesta:
HTTP 403 Forbidden.
6. Qué se verifica en este ejemplo
Que todos los servicios:
Usan JWT/OAuth2 de forma homogénea (MP-JWT, RS256, issuer corporativo).
Protegen endpoints de negocio con @RolesAllowed.
Consumen roles vía SecurityConstants, no strings sueltos.
Enriquecen los logs con contexto de seguridad (userId, channel) vía JwtInfo + SecurityContextFilter.
Que el modelo es Secure by default:
Si no configuras nada extra, el arquetipo ya viene con esta seguridad integrada.
Para desproteger algo, se tendrían que romper explícitamente el lineamiento (y eso debe saltar en revisiones/pipelines).
## Ejemplo Trazabilidad estándar (TraceFilter + TraceContext)
Este ejemplo muestra cómo se ve en código el lineamiento de trazabilidad (traceId, correlationId, businessId): dependencias, application.properties, TraceFilter y un recurso REST simple.
1. Implementación del filtro de trazabilidad
Módulo: observability-starter
Clase: com.compartamos.integration.observability.filter.TraceFilter
```java
package com.compartamos.integration.observability.filter;
import com.compartamos.integration.observability.trace.TraceContext;
import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.SpanContext;
import jakarta.annotation.Priority;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import jakarta.ws.rs.Priorities;
import jakarta.ws.rs.container.ContainerRequestContext;
import jakarta.ws.rs.container.ContainerRequestFilter;
import jakarta.ws.rs.container.ContainerResponseContext;
import jakarta.ws.rs.container.ContainerResponseFilter;
import jakarta.ws.rs.ext.Provider;
import org.jboss.logging.MDC;
import java.io.IOException;
import java.util.UUID;
```
@Provider
@Priority(Priorities.AUTHENTICATION)
@ApplicationScoped
```java
public class TraceFilter implements ContainerRequestFilter, ContainerResponseFilter {
public static final String TRACE_ID = "X-Trace-Id";
public static final String CORR_ID  = "X-Correlation-Id";
public static final String BIZ_ID   = "X-Business-Id";
```
@Inject
```java
TraceContext traceContext;
```
@Override
```java
public void filter(ContainerRequestContext req) throws IOException {
String corrId = headerOrNew(req, CORR_ID);
String bizId  = req.getHeaderString(BIZ_ID);
SpanContext spanContext = Span.current().getSpanContext();
```
if (spanContext.isValid()) {
```java
String traceId = spanContext.getTraceId();
MDC.put("traceId", traceId);
traceContext.setTraceId(traceId);
```
}
```java
MDC.put("correlationId", corrId);
```
if (bizId != null && !bizId.isBlank()) {
```java
MDC.put("businessId", bizId);
```
}
```java
traceContext.setCorrelationId(corrId);
traceContext.setBusinessId(bizId);
req.setProperty(CORR_ID, corrId);
req.setProperty(BIZ_ID, bizId);
System.out.println(">>> [TraceFilter starter] REQUEST corrId=" + corrId + ", bizId=" + bizId);
```
}
@Override
```java
public void filter(ContainerRequestContext req, ContainerResponseContext res) throws IOException {
Object corrId = req.getProperty(CORR_ID);
Object bizId  = req.getProperty(BIZ_ID);
```
if (corrId != null) {
```java
res.getHeaders().putSingle(CORR_ID, corrId);
```
}
if (bizId != null) {
```java
res.getHeaders().putSingle(BIZ_ID, bizId);
```
}
```java
SpanContext spanContext = Span.current().getSpanContext();
```
if (spanContext.isValid()) {
```java
String traceId = spanContext.getTraceId();
res.getHeaders().putSingle(TRACE_ID, traceId);
MDC.put("traceId", traceId);
traceContext.setTraceId(traceId);
```
}
```java
System.out.println(">>> [TraceFilter starter] RESPONSE corrId=" + corrId + ", bizId=" + bizId);
//MDC.clear();
```
}
private String headerOrNew(ContainerRequestContext ctx, String name) {
```java
String v = ctx.getHeaderString(name);
return (v == null || v.isBlank()) ? UUID.randomUUID().toString() : v;
```
}
}
2) Contexto de trazabilidad request-scoped
```java
package com.compartamos.integration.observability.trace;
import jakarta.enterprise.context.RequestScoped;
```
@RequestScoped
```java
public class TraceContext {
private String traceId;
private String correlationId;
private String businessId;
public String getTraceId() { return traceId; }
public void setTraceId(String traceId) { this.traceId = traceId; }
public String getCorrelationId() { return correlationId; }
public void setCorrelationId(String correlationId) { this.correlationId = correlationId; }
public String getBusinessId() { return businessId; }
public void setBusinessId(String businessId) { this.businessId = businessId; }
```
}
3) Ejemplo de recurso REST usando trazabilidad
Módulo sample: ms-campaign-demo
Clase: com.compartamos.process.campaign.infrastructure.rest.CampaignResource
```java
package com.compartamos.process.campaign.infrastructure.rest;
import com.compartamos.process.campaign.application.usecase.GetCampaignsUseCase;
import com.compartamos.process.campaign.infrastructure.rest.dto.CampaignDto;
import jakarta.inject.Inject;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import java.util.List;
```
@Path("/v1/campaigns-alt")
```java
public class CampaignResource {
```
@Inject
```java
GetCampaignsUseCase getCampaigns;
```
@GET
@Produces(MediaType.APPLICATION_JSON)
```java
public List<CampaignDto> list() {
```
return getCampaigns.execute()
.stream()
.map(c -> new CampaignDto(c.getId(), c.getName()))
```java
.toList();
```
}
}
Cuando se invoca /v1/campaigns-alt:
TraceFilter corre primero y llena MDC y TraceContext.
GetCampaignsUseCase puede leer traceId/correlationId desde MDC para enriquecer el span.
Los logs llevan siempre traceId, correlationId, businessId.
4) Flujo completo del ejemplo
Request entrante
GET http://localhost:8080/campaigns
X-Correlation-Id: 31ab4a1f-1234-5678-9012-f0d0b0f0c0a0
Lo que hace el framework
TraceFilter.filter(request):
correlationId = 31ab4a1f-1234-5678-9012-f0d0b0f0c0a0
businessId = null (no vino en el header)
traceId se obtiene del span de OpenTelemetry, p.ej. 5a3b8e4d8a0b12c3
MDC:
traceId = 5a3b8e4d8a0b12c3
correlationId = 31ab4a1f-1234-5678-9012-f0d0b0f0c0a0
businessId = null
CampaignsResource.getCampaigns():
Escribe un log TECH:
{
"loggerName": "TECH",
"level": "INFO",
"message": "Invocando /campaigns desde CampaignsResource",
"traceId": "5a3b8e4d8a0b12c3",
"correlationId": "31ab4a1f-1234-5678-9012-f0d0b0f0c0a0",
"businessId": null,
...
}
TraceFilter.filter(response):
Respuesta HTTP:
HTTP/1.1 200 OK
X-Trace-Id: 5a3b8e4d8a0b12c3
X-Correlation-Id: 31ab4a1f-1234-5678-9012-f0d0b0f0c0a0
X-Business-Id: null
OpenTelemetry:
En el Collector/APM se ve un span:
Name: GET /campaigns
Trace ID: 5a3b8e4d8a0b12c3
Resultado:
Logs, respuesta HTTP y traza comparten el mismo traceId.
El correlationId es el que mandó el canal.
businessId si se mapea desde el canal (por ejemplo, desde el campaignId en el cuerpo), pero el mecanismo ya está estandarizado.
## Ejemplo Propagación de trazas en llamadas salientes (HeadersPropagationFilter)
1) Filtro de propagación de headers
```java
package com.compartamos.integration.observability.restclient;
import org.eclipse.microprofile.rest.client.ext.ClientHeadersFactory;
import jakarta.ws.rs.core.MultivaluedHashMap;
import jakarta.ws.rs.core.MultivaluedMap;
import org.jboss.logging.MDC;
public class RestClientHeadersPropagationFactory implements ClientHeadersFactory {
private static final String CORR_ID = "X-Correlation-Id";
private static final String BIZ_ID  = "X-Business-Id";
```
@Override
```java
public MultivaluedMap<String, String> update(
```
MultivaluedMap<String, String> incoming,
MultivaluedMap<String, String> outgoing) {
```java
MultivaluedMap<String, String> headers = new MultivaluedHashMap<>();
```
String corrId = firstNonBlank(
incoming != null ? incoming.getFirst(CORR_ID) : null,
(String) MDC.get("correlationId")
```java
);
```
String bizId = firstNonBlank(
incoming != null ? incoming.getFirst(BIZ_ID) : null,
(String) MDC.get("businessId")
```java
);
if (corrId != null) headers.add(CORR_ID, corrId);
if (bizId  != null) headers.add(BIZ_ID,  bizId);
return headers;
```
}
private String firstNonBlank(String... values) {
for (String v : values) {
```java
if (v != null && !v.isBlank()) return v;
```
}
```java
return null;
```
}
}
2) Cliente REST y servicio que lo usan (ms-campaign-demo)
```java
package com.compartamos.process.campaign.infrastructure.proxy;
import com.compartamos.integration.observability.restclient.RestClientHeadersPropagationFactory;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import org.eclipse.microprofile.rest.client.inject.RegisterRestClient;
import org.eclipse.microprofile.rest.client.annotation.RegisterProvider;
import java.util.List;
```
@RegisterRestClient(configKey = "external-users")
@RegisterProvider(RestClientHeadersPropagationFactory.class)
@Path("/users")
```java
public interface ExternalUserClient {
```
@GET
@Produces(MediaType.APPLICATION_JSON)
```java
List<UserDto> list();
```
record UserDto(Integer id, String name, String username, String email) {}
}
@ApplicationScoped
```java
public class GetExternalUsersService {
```
@Inject
@RestClient
```java
ExternalUserClient client;
```
/**
* Versión v2:
* - La política concreta (timeouts, retry, circuit breaker, etc.)
*   vive en resilience-starter bajo la key EXTERNAL_USERS.
* - El servicio solo declara:
*   - qué operación es (EXTERNAL_USERS)
*   - métricas
*   - fallback local
*/
@ResilientHttpOutbound(ResiliencePolicies.EXTERNAL_USERS)
@Counted(value = "external_users_requests_total", extraTags = {"op", "list"})
@Timed(value = "external_users_requests", histogram = true)
@Fallback(fallbackMethod = "fallbackUsers")
```java
public List<UserDto> listUsers() {
return client.list();
```
}
// Degradación segura: si el externo falla, devolvemos lista vacía.
```java
public List<UserDto> fallbackUsers() {
System.out.println(">>> FALLO EXTERNO, EJECUTANDO FALLBACK");
return Collections.emptyList();
```
}
}
3) Configuración en application.properties
quarkus.rest-client.external-users.url=http://jsonplaceholder.typicode.com
quarkus.rest-client.external-users.headers-factory=\
com.compartamos.integration.observability.restclient.RestClientHeadersPropagationFactory
Resultado:
Una request entrante con:
GET /v1/campaigns-alt
X-Correlation-Id: c123-456-xyz
X-Business-Id: CMP-LIST-001
genera una llamada saliente a external-users con:
GET /users
X-Correlation-Id: c123-456-xyz
X-Business-Id: CMP-LIST-001
traceparent: 00-<traceId>-<spanId>-01
ambos servicios comparten la misma trazabilidad.
## Ejemplo Manejo de errores estándar ApiError + GlobalExceptionMapper
a) Modelo estándar: ApiError
```java
package com.compartamos.integration.exception;
public class ApiError {
private String code;
private String message;
private String detail;
private String traceId;
private String correlationId;
```
private String businessId; // 🔹 nuevo
```java
public ApiError() {
```
}
// Constructor antiguo (compatible con código previo)
```java
public ApiError(String code,
```
String message,
String detail,
String traceId,
String correlationId) {
```java
this(code, message, detail, traceId, correlationId, null);
```
}
// Nuevo constructor con businessId (usado por el mapper actualizado)
```java
public ApiError(String code,
```
String message,
String detail,
String traceId,
String correlationId,
String businessId) {
```java
this.code = code;
this.message = message;
this.detail = detail;
this.traceId = traceId;
this.correlationId = correlationId;
this.businessId = businessId;
```
}
// Getters y setters
```java
public String getCode() { return code; }
public void setCode(String code) { this.code = code; }
public String getMessage() { return message; }
public void setMessage(String message) { this.message = message; }
public String getDetail() { return detail; }
public void setDetail(String detail) { this.detail = detail; }
public String getTraceId() { return traceId; }
public void setTraceId(String traceId) { this.traceId = traceId; }
public String getCorrelationId() { return correlationId; }
public void setCorrelationId(String correlationId) { this.correlationId = correlationId; }
public String getBusinessId() { return businessId; }
public void setBusinessId(String businessId) { this.businessId = businessId; }
```
}
b) GlobalExceptionMapper (centralizado y coherente con trazabilidad)
```java
package com.compartamos.integration.exception;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.validation.ConstraintViolationException;
import jakarta.ws.rs.core.Context;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;
import jakarta.ws.rs.core.UriInfo;
import jakarta.ws.rs.ext.ExceptionMapper;
import jakarta.ws.rs.ext.Provider;
import org.jboss.logging.Logger;
import org.jboss.logging.MDC;
```
/**
* Mapeador global de excepciones a ApiError estándar del framework.
* No depende de observability-starter: usa solo MDC.
```java
* Si el TraceFilter del observability-starter está presente, los IDs vendrán poblados;
```
* si no, simplemente serán null.
*/
@Provider
@ApplicationScoped
```java
public class GlobalExceptionMapper implements ExceptionMapper<Throwable> {
private static final Logger LOG = Logger.getLogger(GlobalExceptionMapper.class);
```
@Context
```java
UriInfo uriInfo;
```
@Override
```java
public Response toResponse(Throwable exception) {
String traceId       = (String) MDC.get("traceId");
String correlationId = (String) MDC.get("correlationId");
String businessId    = (String) MDC.get("businessId");
ApiError body;
Response.Status status;
```
if (exception instanceof ConstraintViolationException) {
```java
ConstraintViolationException cve = (ConstraintViolationException) exception;
status = Response.Status.BAD_REQUEST;
```
body = new ApiError(
ErrorCode.VALIDATION_ERROR.name(),
"Error de validación en la solicitud.",
cve.getMessage(),
traceId,
correlationId,
businessId
```java
);
```
LOG.warnf("Validation error at %s (traceId=%s, corrId=%s, bizId=%s): %s",
```java
safePath(), traceId, correlationId, businessId, cve.getMessage());
```
}
// DOWNSTREAM (si resilience-starter está presente)
else if (isResilienceException(exception)) {
```java
status = Response.Status.BAD_GATEWAY;
```
body = new ApiError(
ErrorCode.DOWNSTREAM_ERROR.name(),
"Error al invocar un servicio externo.",
exception.getMessage(),
traceId,
correlationId,
businessId
```java
);
```
LOG.errorf(exception, "Downstream failure at %s (traceId=%s, corrId=%s, bizId=%s)",
```java
safePath(), traceId, correlationId, businessId);
```
}
else {
```java
status = Response.Status.INTERNAL_SERVER_ERROR;
```
body = new ApiError(
ErrorCode.TECHNICAL_ERROR.name(),
"Ocurrió un error interno al procesar la operación.",
exception.getMessage(),
traceId,
correlationId,
businessId
```java
);
```
LOG.errorf(exception, "Unhandled error at %s (traceId=%s, corrId=%s, bizId=%s)",
```java
safePath(), traceId, correlationId, businessId);
```
}
return Response.status(status)
.type(MediaType.APPLICATION_JSON)
.entity(body)
```java
.build();
```
}
private String safePath() {
```java
return uriInfo != null ? uriInfo.getPath() : "n/a";
```
}
/**
* Detecta opcionalmente ResilienceException sin depender del módulo de resiliencia.
*/
private boolean isResilienceException(Throwable exception) {
try {
```java
Class<?> clazz = Class.forName("com.compartamos.integration.resilience.ResilienceException");
return clazz.isInstance(exception);
```
} catch (ClassNotFoundException e) {
```java
return false;
```
}
}
}
Ejemplo de respuesta:
{
"code": "DOWNSTREAM_ERROR",
"message": "Error al invocar un servicio externo.",
"detail": "Timeout waiting for external API",
"traceId": "0da1b75075183817b32f9e9f3ebc4d56",
"correlationId": "3f4c61e8-0a6b-4f0b-8aaf-2acb83c9b912",
"timestamp": "2025-11-18T23:28:10.001234Z"
}
## Ejemplo de Logging estructurado estándar
1.  AuditEvent
```java
package com.compartamos.integration.logging;
import java.time.Instant;
import java.util.Collections;
import java.util.Map;
public class AuditEvent {
private final String action;
private final String entity;
private final String entityId;
private final String userId;
private final String channel;
private final String outcome;
private final String correlationId;
private final String traceId;
private final Instant timestamp;
private final Map<String, Object> metadata;
private final String reason;
```
private AuditEvent(Builder builder) {
```java
this.action = builder.action;
this.entity = builder.entity;
this.entityId = builder.entityId;
this.userId = builder.userId;
this.channel = builder.channel;
this.outcome = builder.outcome;
this.correlationId = builder.correlationId;
this.traceId = builder.traceId;
this.metadata = builder.metadata != null ? Collections.unmodifiableMap(builder.metadata) : Collections.emptyMap();
this.timestamp = builder.timestamp != null ? builder.timestamp : Instant.now();
this.reason = builder.reason;
```
}
```java
public String getAction() { return action; }
public String getEntity() { return entity; }
public String getEntityId() { return entityId; }
public String getUserId() { return userId; }
public String getChannel() { return channel; }
public String getOutcome() { return outcome; }
public String getCorrelationId() { return correlationId; }
public String getTraceId() { return traceId; }
public Instant getTimestamp() { return timestamp; }
public Map<String, Object> getMetadata() { return metadata; }
public String getReason() { return reason; }
```
// ------------------------------
// Builder
// ------------------------------
```java
public static Builder builder(String action) {
return new Builder(action);
```
}
```java
public static class Builder {
private final String action;
private String entity;
private String entityId;
private String userId;
private String channel;
private String outcome;
private String correlationId;
private String traceId;
private Map<String, Object> metadata;
private Instant timestamp;
private String reason;
public Builder(String action) {
this.action = action;
```
}
```java
public Builder entity(String entity) {
this.entity = entity;
return this;
```
}
```java
public Builder entityId(String entityId) {
this.entityId = entityId;
return this;
```
}
```java
public Builder userId(String userId) {
this.userId = userId;
return this;
```
}
```java
public Builder channel(String channel) {
this.channel = channel;
return this;
```
}
```java
public Builder outcome(String outcome) {
this.outcome = outcome;
return this;
```
}
```java
public Builder correlationId(String correlationId) {
this.correlationId = correlationId;
return this;
```
}
```java
public Builder traceId(String traceId) {
this.traceId = traceId;
return this;
```
}
```java
public Builder metadata(Map<String, Object> metadata) {
this.metadata = metadata;
return this;
```
}
```java
public Builder timestamp(Instant timestamp) {
this.timestamp = timestamp;
return this;
```
}
// ------------------------------
// NUEVO: soportar reason como campo 1er nivel
// ------------------------------
```java
public Builder reason(String reason) {
this.reason = reason;
return this;
```
}
```java
public AuditEvent build() {
return new AuditEvent(this);
```
}
}
}
2. AuditLogger
AuditLogger debe completar traceId, correlationId, businessId, userId, channel desde MDC si no vienen explícitos.
```java
package com.compartamos.integration.logging;
import org.jboss.logging.Logger;
public class AuditLogger {
private static final Logger LOG = Logger.getLogger("AUDIT");
public static void log(AuditEvent event) {
LOG.info(eventToJson(event));
```
}
private static String eventToJson(AuditEvent e) {
```java
StringBuilder sb = new StringBuilder();
sb.append("{");
sb.append("\"action\":\"").append(e.getAction()).append("\",");
sb.append("\"entity\":\"").append(e.getEntity()).append("\",");
```
if (e.getEntityId() != null) {
```java
sb.append("\"entityId\":\"").append(e.getEntityId()).append("\",");
```
}
if (e.getUserId() != null) {
```java
sb.append("\"userId\":\"").append(e.getUserId()).append("\",");
```
}
if (e.getChannel() != null) {
```java
sb.append("\"channel\":\"").append(e.getChannel()).append("\",");
```
}
```java
sb.append("\"outcome\":\"").append(e.getOutcome()).append("\",");
sb.append("\"correlationId\":\"").append(e.getCorrelationId()).append("\",");
```
if (e.getTraceId() != null) {
```java
sb.append("\"traceId\":\"").append(e.getTraceId()).append("\",");
```
}
if (e.getReason() != null) {
```java
sb.append("\"reason\":\"").append(e.getReason()).append("\",");
```
}
```java
sb.append("\"timestamp\":\"").append(e.getTimestamp()).append("\",");
sb.append("\"metadata\":").append(mapToJson(e.getMetadata()));
sb.append("}");
return sb.toString();
```
}
private static String mapToJson(java.util.Map<String, Object> map) {
if (map == null || map.isEmpty()) {
```java
return "{}";
```
}
```java
StringBuilder sb = new StringBuilder("{");
int i = 0;
```
for (var entry : map.entrySet()) {
```java
sb.append("\"").append(entry.getKey()).append("\":");
Object v = entry.getValue();
```
if (v instanceof Number || v instanceof Boolean) {
```java
sb.append(v);
```
} else {
```java
sb.append("\"").append(v).append("\"");
```
}
```java
if (i < map.size() - 1) sb.append(",");
i++;
```
}
```java
sb.append("}");
return sb.toString();
```
}
}
Con quarkus.log.console.json=true, este payload se serializa como JSON en el campo message del log de Quarkus, que es justo la forma de tu ejemplo conceptual.
3. BusinessLogger para logs BUSINESS estructurados
Definimos un logger similar a AuditLogger, pero para eventos funcionales.
```java
package com.compartamos.process.campaign.cross.logging;
import org.jboss.logging.Logger;
import org.jboss.logging.MDC;
import java.time.Instant;
import java.util.HashMap;
import java.util.Map;
import static com.compartamos.process.campaign.cross.logging.LogCategories.BUSINESS;
public final class BusinessLogger {
private static final Logger LOG = Logger.getLogger(BUSINESS);
```
private BusinessLogger() {}
```java
public static void info(String event, String message, Map<String, Object> metadata) {
```
if (!LOG.isInfoEnabled()) {
```java
return;
```
}
```java
Map<String, Object> payload = new HashMap<>();
payload.put("type", "BUSINESS");
payload.put("event", safe(event));
payload.put("message", safe(message));
```
// Desde MDC (TraceFilter + SecurityContextFilter)
```java
payload.put("userId", (String) MDC.get("userId"));
payload.put("channel", (String) MDC.get("channel"));
payload.put("traceId", (String) MDC.get("traceId"));
payload.put("correlationId", (String) MDC.get("correlationId"));
payload.put("businessId", (String) MDC.get("businessId"));
payload.put("timestamp", Instant.now().toString());
```
if (metadata != null && !metadata.isEmpty()) {
```java
payload.put("meta", metadata);
```
}
```java
LOG.info(payload);
```
}
private static String safe(String v) {
```java
return v == null ? "" : v;
```
}
}
tenemos:
TECH → Logger.getLogger(LogCategories.TECH) con payload estructurado.
BUSINESS → BusinessLogger.info(...) con payload estructurado.
AUDIT → AuditLogger.log(AuditEvent) con payload estructurado.
4. GetCampaignsUseCase (ejemplo de uso)
```java
package com.compartamos.process.campaign.application.usecase;
import com.compartamos.process.campaign.cross.logging.AuditEvent;
import com.compartamos.process.campaign.cross.logging.AuditLogger;
import com.compartamos.process.campaign.cross.logging.BusinessLogger;
import com.compartamos.process.campaign.cross.logging.LogCategories;
import com.compartamos.process.campaign.domain.Campaign;
import jakarta.annotation.PostConstruct;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import org.jboss.logging.Logger;
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.core.instrument.Counter;
import io.micrometer.core.instrument.Gauge;
import io.opentelemetry.api.trace.Tracer;
import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.StatusCode;
import io.opentelemetry.context.Scope;
import io.opentelemetry.api.common.AttributeKey;
import org.jboss.logging.MDC;
import java.util.List;
import java.util.Map;
import java.util.concurrent.atomic.AtomicInteger;
```
@ApplicationScoped
```java
public class GetCampaignsUseCase {
private static final Logger LOG_TECH = Logger.getLogger(LogCategories.TECH);
private static final Logger LOG_BUSINESS = Logger.getLogger(LogCategories.BUSINESS);
@Inject MeterRegistry registry;
@Inject Tracer tracer;
private Counter campaignsQueriesTotal;
private final AtomicInteger campaignsLastResultSize = new AtomicInteger(0);
```
@PostConstruct
void initMetrics() {
this.campaignsQueriesTotal = Counter.builder("campaigns_queries_total")
.description("Número de ejecuciones del caso de uso GetCampaigns")
.tag("usecase", "get_campaigns")
```java
.register(registry);
```
Gauge.builder("campaigns_last_result_size", campaignsLastResultSize, AtomicInteger::get)
.description("Tamaño del último resultado de campañas")
.tag("usecase", "get_campaigns")
```java
.register(registry);
```
}
```java
public List<Campaign> execute() {
Span span = Span.current();
span.setAttribute("app.usecase", "GetCampaigns");
span.setAttribute("business.entity", "Campaign");
```
try (Scope scope = span.makeCurrent()) {
```java
LOG_TECH.debug("Invocando GetCampaignsUseCase");
```
List<Campaign> campaigns = List.of(
new Campaign("CMP-001", "Campaña de ahorros"),
new Campaign("CMP-002", "Campaña de créditos")
```java
);
span.setAttribute(AttributeKey.longKey("business.count"), campaigns.size());
String userId = (String) MDC.get("userId");
String channel = (String) MDC.get("channel");
String traceId = (String) MDC.get("traceId");
String correlationId = (String) MDC.get("correlationId");
if (userId != null)       span.setAttribute("security.user.id", userId);
if (channel != null)      span.setAttribute("security.channel", channel);
if (traceId != null)      span.setAttribute("trace.id", traceId);
if (correlationId != null)span.setAttribute("correlation.id", correlationId);
// 🔹 Antes: LOG_BUSINESS.infof("Consulta de campañas ejecutada. Total campañas: %d", campaigns.size());
```
BusinessLogger.info(
"GET_CAMPAIGNS",
"Consulta de campañas ejecutada",
Map.of("count", campaigns.size())
```java
);
```
AuditLogger.log(
AuditEvent.builder("GET_CAMPAIGNS")
.entity("Campaign")
.outcome("SUCCESS")
.metadata(Map.of("count", campaigns.size()))
.build()
```java
);
campaignsQueriesTotal.increment();
campaignsLastResultSize.set(campaigns.size());
return campaigns;
```
} catch (Exception e) {
```java
span.recordException(e);
span.setStatus(StatusCode.ERROR, e.getMessage());
throw e;
```
} finally {
```java
span.end();
```
}
}
}
5. application.properties
# Logging JSON en consola
quarkus.log.console.json=true
quarkus.log.console.level=INFO
# Categorías del framework
quarkus.log.category."TECH".level=DEBUG
quarkus.log.category."BUSINESS".level=INFO
quarkus.log.category."AUDIT".level=INFO
Los logs TECH, BUSINESS y AUDIT se diferencian por loggerName.
Los logs BUSINESS y AUDIT llevan payload estructurado tipo Map con:
type (BUSINESS / AUDIT)
action / event
entity, outcome, reason (AUDIT)
userId, channel, traceId, correlationId, businessId
timestamp
meta (para metadata)
Recurso de prueba de logging (ms-campaign-demo)
```java
package com.compartamos.process.campaign.infrastructure.rest;
import com.compartamos.integration.logging.AuditEvent;
import com.compartamos.integration.logging.AuditLogger;
import jakarta.ws.rs.*;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;
import org.jboss.logging.Logger;
import org.jboss.logging.MDC;
import java.util.HashMap;
import java.util.Map;
```
@Path("/v1/test/logging")
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
```java
public class TestLoggingCampaignResource {
private static final Logger LOG = Logger.getLogger(TestLoggingCampaignResource.class);
```
@GET
@Path("/basic")
```java
public Response basicLogging(
```
@HeaderParam("X-Correlation-Id") String corrHeader,
@HeaderParam("X-Business-Id") String bizHeader) {
```java
String corr = corrHeader != null ? corrHeader : (String) MDC.get("correlationId");
String biz = bizHeader != null ? bizHeader : (String) MDC.get("businessId");
LOG.infof("TestLoggingCampaignResource.basicLogging() invocado (corrId=%s, bizId=%s)", corr, biz);
LOG.warn("Mensaje WARN de prueba para validación visual de logs.");
Map<String, Object> body = new HashMap<>();
body.put("status", "OK");
body.put("message", "Logging técnico básico generado");
body.put("correlationId", corr);
```
// Solo incluimos businessId si no es null (opcional)
if (biz != null) {
```java
body.put("businessId", biz);
```
}
```java
return Response.ok(body).build();
```
}
@GET
@Path("/audit-success")
```java
public Response auditSuccess(
```
@HeaderParam("X-Correlation-Id") String corrHeader,
@HeaderParam("X-Business-Id") String bizHeader) {
```java
String corr = corrHeader != null ? corrHeader : (String) MDC.get("correlationId");
String biz = bizHeader != null ? bizHeader : (String) MDC.get("businessId");
LOG.infof("TestLoggingCampaignResource.auditSuccess() invocado (corrId=%s, bizId=%s)", corr, biz);
```
AuditEvent event = AuditEvent.builder("CAMPAIGN_ACTION")
.entity("CAMPAIGN")
.outcome("SUCCESS")
.correlationId(corr)
// si tienes reason opcional, puedes dejarlo como null,
// pero eso ya lo maneja internamente el builder
.metadata(Map.of(
"operation", "CAMPAIGN_ACTION",
"source", "TestLoggingCampaignResource",
"demo", true
))
```java
.build();
AuditLogger.log(event);
Map<String, Object> body = new HashMap<>();
body.put("status", "OK");
body.put("audit", "logged");
body.put("correlationId", corr);
```
if (biz != null) {
```java
body.put("businessId", biz);
```
}
```java
return Response.ok(body).build();
```
}
@GET
@Path("/audit-failure")
```java
public Response auditFailure(
```
@HeaderParam("X-Correlation-Id") String corrHeader,
@HeaderParam("X-Business-Id") String bizHeader) {
```java
String corr = corrHeader != null ? corrHeader : (String) MDC.get("correlationId");
String biz = bizHeader != null ? bizHeader : (String) MDC.get("businessId");
LOG.infof("TestLoggingCampaignResource.auditFailure() invocado (corrId=%s, bizId=%s)", corr, biz);
String reason = "Simulación de fallo de negocio en TestLoggingCampaignResource";
```
AuditEvent event = AuditEvent.builder("CAMPAIGN_ACTION")
.entity("CAMPAIGN")
.outcome("FAILURE")
.correlationId(corr)
.reason(reason)
.metadata(Map.of(
"operation", "CAMPAIGN_ACTION",
"source", "TestLoggingCampaignResource",
"demo", true
))
```java
.build();
AuditLogger.log(event);
Map<String, Object> body = new HashMap<>();
body.put("status", "ERROR");
body.put("reason", reason);
body.put("correlationId", corr);
```
if (biz != null) {
```java
body.put("businessId", biz);
```
}
return Response.status(Response.Status.BAD_REQUEST)
.entity(body)
```java
.build();
```
}
}
## Ejemplo de Métricas y Health Checks
1. Ejemplo de Métricas de Negocio (Micrometer)
El siguiente ejemplo corresponde al caso de uso GetCampaignsUseCase, el cual expone:
Un Counter: número total de ejecuciones.
Un Gauge: tamaño del último resultado.
Ambas métricas se registran automáticamente en /metrics y se pueden visualizar en Prometheus o Grafana.
```java
package com.compartamos.process.campaign.application.usecase;
import com.compartamos.integration.logging.AuditEvent;
import com.compartamos.integration.logging.AuditLogger;
import com.compartamos.integration.logging.LogCategories;
import com.compartamos.process.campaign.domain.Campaign;
import jakarta.annotation.PostConstruct;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import org.jboss.logging.Logger;
import org.jboss.logging.MDC;
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.core.instrument.Counter;
import io.micrometer.core.instrument.Gauge;
import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.StatusCode;
import io.opentelemetry.context.Scope;
import io.opentelemetry.api.common.AttributeKey;
import java.util.List;
import java.util.Map;
import java.util.concurrent.atomic.AtomicInteger;
```
@ApplicationScoped
```java
public class GetCampaignsUseCase {
private static final Logger LOG_TECH = Logger.getLogger(LogCategories.TECH);
private static final Logger LOG_BUSINESS = Logger.getLogger(LogCategories.BUSINESS);
```
@Inject
```java
MeterRegistry registry;
```
// OTEL Tracer (expuesto por Quarkus vía CDI si se necesita luego)
// @Inject
```java
// Tracer tracer;
```
// Métricas de negocio
```java
private Counter campaignsQueriesTotal;
private final AtomicInteger campaignsLastResultSize = new AtomicInteger(0);
```
@PostConstruct
void initMetrics() {
this.campaignsQueriesTotal = Counter.builder("campaigns_queries_total")
.description("Número de ejecuciones del caso de uso GetCampaigns")
.tag("usecase", "get_campaigns")
```java
.register(registry);
```
Gauge.builder("campaigns_last_result_size", campaignsLastResultSize, AtomicInteger::get)
.description("Tamaño del último resultado de campañas")
.tag("usecase", "get_campaigns")
```java
.register(registry);
```
}
```java
public List<Campaign> execute() {
```
// Span actual (creado por Quarkus al atender la request HTTP)
```java
Span span = Span.current();
span.setAttribute("app.usecase", "GetCampaigns");
span.setAttribute("business.entity", "Campaign");
```
try (Scope scope = span.makeCurrent()) {
```java
LOG_TECH.debug("Invocando GetCampaignsUseCase");
```
List<Campaign> campaigns = List.of(
new Campaign("CMP-001", "Campaña de ahorros"),
new Campaign("CMP-002", "Campaña de créditos")
```java
);
```
// Atributos de negocio en el span
```java
span.setAttribute(AttributeKey.longKey("business.count"), campaigns.size());
```
// Atributos de seguridad/correlación (si están en MDC)
```java
String userId = (String) MDC.get("userId");
String channel = (String) MDC.get("channel");
String traceId = (String) MDC.get("traceId");
String correlationId = (String) MDC.get("correlationId");
if (userId != null)        span.setAttribute("security.user.id", userId);
if (channel != null)       span.setAttribute("security.channel", channel);
if (traceId != null)       span.setAttribute("trace.id", traceId);
if (correlationId != null) span.setAttribute("correlation.id", correlationId);
LOG_BUSINESS.infof("Consulta de campañas ejecutada. Total campañas: %d", campaigns.size());
```
AuditLogger.log(
AuditEvent.builder("GET_CAMPAIGNS")
.entity("Campaign")
.outcome("SUCCESS")
.metadata(Map.of("count", campaigns.size()))
.build()
```java
);
```
// Métricas
```java
campaignsQueriesTotal.increment();
campaignsLastResultSize.set(campaigns.size());
return campaigns;
```
} catch (Exception e) {
```java
span.recordException(e);
span.setStatus(StatusCode.ERROR, e.getMessage());
throw e;
```
} finally {
```java
span.end();
```
}
}
}
Métricas expuestas en /metrics
Ejemplos:
# HELP campaigns_queries_total Número de ejecuciones del caso de uso GetCampaigns
campaigns_queries_total{usecase="get_campaigns"} 125
# HELP campaigns_last_result_size Tamaño del último resultado de campañas
campaigns_last_result_size{usecase="get_campaigns"} 2
2. Ejemplo de Métrica Técnica Enriquecida con OpenTelemetry
Este ejemplo demuestra cómo enriquecer el span activo con atributos técnicos y de negocio.
```java
Span span = Span.current();
span.setAttribute("app.usecase", "GetCampaigns");
span.setAttribute("business.entity", "Campaign");
span.setAttribute("business.count", campaigns.size());
```
// Atributos provenientes del contexto de seguridad (si existen)
```java
String userId = (String) MDC.get("userId");
String channel = (String) MDC.get("channel");
if (userId != null)    span.setAttribute("security.user.id", userId);
if (channel != null)   span.setAttribute("security.channel", channel);
```
Estos atributos quedan disponibles en los backends de trazas (Jaeger, Tempo, etc.) y permiten correlacionar métricas, logs y trazas.
3. Ejemplo de Health Check de Conectividad a API Externa
Health check del tipo Readiness, asegurando que el servicio solo sea marcado como “READY” si puede comunicarse con su dependencia externa (ExternalUserClient).
@Readiness
@ApplicationScoped
```java
public class ExternalUsersHealthCheck implements HealthCheck {
```
@Inject
@RestClient
```java
ExternalUserClient client;
```
@Override
```java
public HealthCheckResponse call() {
```
try {
```java
client.list();
```
return HealthCheckResponse.named("external-users-api")
.up()
```java
.build();
```
} catch (Exception e) {
return HealthCheckResponse.named("external-users-api")
.down()
.withData("error", e.getClass().getSimpleName() + ": " + e.getMessage())
```java
.build();
```
}
}
}
Salida típica en /health/ready
{
"status": "DOWN",
"checks": [
{
"name": "external-users-api",
"status": "DOWN",
"data": {
"error": "WebApplicationException: 500 Server Error"
}
}
]
}
4. Ejemplo de Health Check de Base de Datos (readiness)
Ejemplo recomendado para servicios que dependen de una base de datos.
@Readiness
@ApplicationScoped
```java
public class DatabaseHealthCheck implements HealthCheck {
@Inject DataSource dataSource;
```
@Override
```java
public HealthCheckResponse call() {
```
try (Connection ignored = dataSource.getConnection()) {
return HealthCheckResponse.named("database")
.up()
```java
.build();
```
} catch (Exception e) {
return HealthCheckResponse.named("database")
.down()
.withData("error", e.getMessage())
```java
.build();
```
}
}
}
5. Endpoints Expuestos por el Servicio
Métricas
GET /metrics
Expone:
Métricas técnicas:
http_server_requests_seconds, jvm_memory_used_bytes, process_cpu_usage, etc.
Métricas de negocio definidas por el desarrollador.
Health Checks
GET /health
GET /health/live
GET /health/ready
Ejemplo general:
{
"status":"UP",
"checks":[
{"name":"external-users-api","status":"UP"},
{"name":"database","status":"UP"}
]
}
## Ejemplo de Auditoría de una acción funcional
El siguiente ejemplo muestra cómo registrar un evento de auditoría cuando se realiza una acción crítica de negocio, en este caso: cambio de estado de una campaña.
El evento se genera utilizando exclusivamente AuditEvent y AuditLogger, asegurando:
Consistencia de formato
Inclusión automática de traceId, correlationId, businessId, userId, channel desde el MDC
Publicación bajo la categoría estándar AUDIT
Ejemplo de uso (código)
AuditLogger.log(
AuditEvent.builder("UPDATE_CAMPAIGN_STATUS")
.entity("Campaign")
.entityId("CMP-001")
.outcome("SUCCESS")
.metadata(Map.of(
"oldStatus", "CREATED",
"newStatus", "APPROVED"
))
.build()
```java
);
```
Descripción del evento generado
Este evento captura:
action = "UPDATE_CAMPAIGN_STATUS"
entity = "Campaign"
entityId = "CMP-001"
outcome = "SUCCESS"
userId, channel → extraídos automáticamente desde el JWT por SecurityContextFilter
traceId, correlationId, businessId → provistos por TraceFilter y presentes en el MDC
timestamp → generado automáticamente
meta → datos adicionales de contexto (estado anterior y nuevo)
Ejemplo conceptual del log emitido (formato JSON)
{
"loggerName": "AUDIT",
"level": "INFO",
"message": {
"type": "AUDIT",
"action": "UPDATE_CAMPAIGN_STATUS",
"entity": "Campaign",
"entityId": "CMP-001",
"outcome": "SUCCESS",
"userId": "test-user",
"channel": "INTEGRATION_API",
"traceId": "b23f9c08f2349d11...",
"correlationId": "8c9f0e1b-7811-4a12-8b6b-4f72d88eacdd",
"businessId": "CMP-001",
"timestamp": "2025-11-11T23:30:15.123Z",
"meta": {
"oldStatus": "CREATED",
"newStatus": "APPROVED"
}
}
}
## Ejemplo completo de Observabilidad
Este ejemplo describe un flujo completo de observabilidad aplicado al servicio de campañas, integrando trazabilidad, logging estructurado, métricas, manejo estándar de errores y health checks.
El escenario ejemplificado es la invocación del caso de uso GetCampaigns a través de un endpoint HTTP expuesto por el microservicio ms-campaign.
1. Componentes involucrados
En este ejemplo participan los siguientes componentes del framework:
Trazabilidad y contexto (observability-starter)
TraceFilter
Lee o genera:
X-Correlation-Id
X-Business-Id
Obtiene el traceId del Span activo de OpenTelemetry.
Pone en MDC: traceId, correlationId, businessId.
Propaga headers en la respuesta: X-Trace-Id, X-Correlation-Id, X-Business-Id.
Manejo estándar de errores (exception-starter)
ApiError
Modelo estándar de respuesta de error con:
code, message, detail, traceId, correlationId, timestamp.
ErrorCode
Catálogo base: VALIDATION_ERROR, DOWNSTREAM_ERROR, TECHNICAL_ERROR, etc.
GlobalExceptionMapper
Captura cualquier Throwable, mapea a ApiError y registra logs técnicos con traceId y correlationId.
Logging estructurado y auditoría (logging-starter)
LogCategories
Define categorías estándar: TECH, BUSINESS, AUDIT.
AuditEvent + AuditLogger
Modelo y logger para eventos de auditoría de negocio.
Enriquecidos con información de usuario, canal, trazabilidad y metadata.
BusinessLogger (parte del framework propuesta, utilizada en este ejemplo)
Emite logs de negocio estructurados bajo la categoría BUSINESS.
Métricas y trazas (Micrometer + OpenTelemetry)
MeterRegistry (Micrometer)
Counter: campaigns_queries_total.
Gauge: campaigns_last_result_size.
Tracer / Span (OpenTelemetry)
Atributos técnicos y de negocio en el span actual.
Health checks (cross.health)
ExternalUsersHealthCheck
Readiness check de conectividad hacia ExternalUserClient (API externa).
Caso de uso de negocio (application.usecase)
GetCampaignsUseCase
Ejecuta la lógica de negocio.
Actualiza métricas.
Enriquecer el span.
Genera logs TECH, BUSINESS y AUDIT.
2. Flujo exitoso – de HTTP request a respuesta
2.1 Request de entrada
Un canal (por ejemplo, API Gateway) invoca el endpoint de campañas:
En este ejemplo participan los siguientes componentes del framework:
GET /campaigns HTTP/1.1
Host: ms-campaign
X-Correlation-Id: 3f4c61e8-0a6b-4f0b-8aaf-2acb83c9b912
X-Business-Id: CMP-LIST-001
Authorization: Bearer <jwt-con-user-y-canal>
Se asume que otro filtro (SecurityContextFilter) ya extrae del JWT el userId y channel y los coloca en MDC.
2.2 Trazabilidad – TraceFilter
Al entrar la request, se ejecuta TraceFilter:
Lee (o genera) el CorrelationId:
Si no viene X-Correlation-Id, genera un UUID.
Lee el BusinessId desde X-Business-Id.
Obtiene el TraceId real desde el Span actual de OpenTelemetry:
Span.current().getSpanContext().getTraceId()
Coloca en el MDC:
traceId, correlationId, businessId.
Guarda correlationId y businessId en propiedades del request para reutilizarlos en la respuesta.
En la respuesta, el filtro:
Devuelve:
X-Correlation-Id
X-Business-Id
X-Trace-Id (alineado con el span real).
Limpia el MDC al final del ciclo.
2.3 Ejecución del caso de uso – GetCampaignsUseCase
El controlador (no se detalla aquí) delega al caso de uso:
```java
public List<Campaign> execute() {
Span span = Span.current();
span.setAttribute("app.usecase", "GetCampaigns");
span.setAttribute("business.entity", "Campaign");
```
try (Scope scope = span.makeCurrent()) {
```java
LOG_TECH.debug("Invocando GetCampaignsUseCase");
```
List<Campaign> campaigns = List.of(
new Campaign("CMP-001", "Campaña de ahorros"),
new Campaign("CMP-002", "Campaña de créditos")
```java
);
```
// Atributos de negocio en el span
```java
span.setAttribute(AttributeKey.longKey("business.count"), campaigns.size());
```
// Atributos de seguridad/correlación desde MDC
```java
String userId = (String) MDC.get("userId");
String channel = (String) MDC.get("channel");
String traceId = (String) MDC.get("traceId");
String correlationId = (String) MDC.get("correlationId");
if (userId != null)        span.setAttribute("security.user.id", userId);
if (channel != null)       span.setAttribute("security.channel", channel);
if (traceId != null)       span.setAttribute("trace.id", traceId);
if (correlationId != null) span.setAttribute("correlation.id", correlationId);
```
// Log de negocio estructurado
BusinessLogger.info(
"GET_CAMPAIGNS",
"Consulta de campañas ejecutada",
Map.of("count", campaigns.size())
```java
);
```
// Auditoría de negocio
AuditLogger.log(
AuditEvent.builder("GET_CAMPAIGNS")
.entity("Campaign")
.outcome("SUCCESS")
.metadata(Map.of("count", campaigns.size()))
.build()
```java
);
```
// Métricas de negocio
```java
campaignsQueriesTotal.increment();
campaignsLastResultSize.set(campaigns.size());
return campaigns;
```
} catch (Exception e) {
```java
span.recordException(e);
span.setStatus(StatusCode.ERROR, e.getMessage());
throw e;
```
} finally {
```java
span.end();
```
}
}
En este punto tenemos:
Span enriquecido con atributos de negocio, seguridad y correlación.
Métricas actualizadas:
campaigns_queries_total
campaigns_last_result_size
Logs TECH (debug), BUSINESS (eventos funcionales), AUDIT (evento de auditoría).
2.4 Respuesta HTTP
El microservicio responde:
HTTP/1.1 200 OK
Content-Type: application/json
X-Trace-Id: 0da1b75075183817b32f9e9f3ebc4d56
X-Correlation-Id: 3f4c61e8-0a6b-4f0b-8aaf-2acb83c9b912
X-Business-Id: CMP-LIST-001
[
{ "id": "CMP-001", "name": "Campaña de ahorros" },
{ "id": "CMP-002", "name": "Campaña de créditos" }
]
Esta respuesta está completamente trazable en logs, métricas y trazas.
3. Logging estructurado de negocio y auditoría
3.1 Log BUSINESS generado por BusinessLogger
Ejemplo conceptual del log JSON:
{
"loggerName": "BUSINESS",
"level": "INFO",
"message": {
"type": "BUSINESS",
"event": "GET_CAMPAIGNS",
"message": "Consulta de campañas ejecutada",
"userId": "test-user",
"channel": "INTEGRATION_API",
"traceId": "0da1b75075183817b32f9e9f3ebc4d56",
"correlationId": "3f4c61e8-0a6b-4f0b-8aaf-2acb83c9b912",
"businessId": "CMP-LIST-001",
"timestamp": "2025-11-18T23:27:54.841Z",
"meta": { "count": 2 }
}
}
La forma exacta dependerá de quarkus.log.console.json=true, pero el payload estructurado está contenido en el message.
3.2 Log AUDIT generado por AuditLogger
Ejemplo conceptual:
{
"loggerName": "AUDIT",
"level": "INFO",
"message": {
"type": "AUDIT",
"action": "GET_CAMPAIGNS",
"entity": "Campaign",
"entityId": "",
"outcome": "SUCCESS",
"reason": "",
"userId": "test-user",
"channel": "INTEGRATION_API",
"traceId": "0da1b75075183817b32f9e9f3ebc4d56",
"correlationId": "3f4c61e8-0a6b-4f0b-8aaf-2acb83c9b912",
"timestamp": "2025-11-18T23:27:54.850Z",
"meta": { "count": 2 }
}
}
Permite responder, de forma uniforme: quién hizo qué, sobre qué, cuándo y con qué resultado.
4. Flujo de error – GlobalExceptionMapper + ApiError
Si durante la ejecución se produce una excepción, el framework la captura en GlobalExceptionMapper.
Ejemplo: fallo al invocar un servicio externo, envuelto en ResilienceException:
GlobalExceptionMapper detecta ResilienceException y construye:
status = 502 Bad Gateway
code = DOWNSTREAM_ERROR
message = "Error al invocar un servicio externo."
detail = mensaje técnico controlado.
traceId y correlationId tomados del MDC.
Ejemplo de respuesta de error:
HTTP/1.1 502 Bad Gateway
Content-Type: application/json
{
"code": "DOWNSTREAM_ERROR",
"message": "Error al invocar un servicio externo.",
"detail": "Timeout waiting for external API",
"traceId": "0da1b75075183817b32f9e9f3ebc4d56",
"correlationId": "3f4c61e8-0a6b-4f0b-8aaf-2acb83c9b912",
"timestamp": "2025-11-18T23:28:10.001234Z"
}
En paralelo, GlobalExceptionMapper registra un log TECH con el detalle técnico y la misma trazabilidad.
5. Health Checks y Métricas
5.1 Readiness – ExternalUsersHealthCheck
El health check de readiness verifica la conectividad con la API externa de usuarios:
Si ExternalUserClient.list() responde correctamente → UP.
Si falla con excepción → DOWN, incluyendo error en el payload.
Ejemplo de /health/ready:
{
"status": "UP",
"checks": [
{
"name": "external-users-api",
"status": "UP"
}
]
}
o en caso de falla:
{
"status": "DOWN",
"checks": [
{
"name": "external-users-api",
"status": "DOWN",
"data": {
"error": "WebApplicationException: 500 Server Error"
}
}
]
}
5.2 Métricas en /metrics
Entre otras, el servicio expondrá:
# Business metrics
campaigns_queries_total{usecase="get_campaigns"} 125
campaigns_last_result_size{usecase="get_campaigns"} 2
Y métricas técnicas estándar de Micrometer/Quarkus (latencia HTTP, JVM, etc.).
6. Código de referencia clave
6.1 BusinessLogger
```java
package com.compartamos.process.campaign.cross.logging;
import org.jboss.logging.Logger;
import org.jboss.logging.MDC;
import java.time.Instant;
import java.util.HashMap;
import java.util.Map;
import static com.compartamos.process.campaign.cross.logging.LogCategories.BUSINESS;
public final class BusinessLogger {
private static final Logger LOG = Logger.getLogger(BUSINESS);
```
private BusinessLogger() {}
```java
public static void info(String event, String message, Map<String, Object> metadata) {
```
if (!LOG.isInfoEnabled()) {
```java
return;
```
}
```java
Map<String, Object> payload = new HashMap<>();
payload.put("type", "BUSINESS");
payload.put("event", safe(event));
payload.put("message", safe(message));
```
// Desde MDC (TraceFilter + SecurityContextFilter)
```java
payload.put("userId", (String) MDC.get("userId"));
payload.put("channel", (String) MDC.get("channel"));
payload.put("traceId", (String) MDC.get("traceId"));
payload.put("correlationId", (String) MDC.get("correlationId"));
payload.put("businessId", (String) MDC.get("businessId"));
payload.put("timestamp", Instant.now().toString());
```
if (metadata != null && !metadata.isEmpty()) {
```java
payload.put("meta", metadata);
```
}
```java
LOG.info(payload);
```
}
private static String safe(String v) {
```java
return v == null ? "" : v;
```
}
}
6.2 GetCampaignsUseCase
```java
package com.compartamos.process.campaign.application.usecase;
import com.compartamos.process.campaign.cross.logging.AuditEvent;
import com.compartamos.process.campaign.cross.logging.AuditLogger;
import com.compartamos.process.campaign.cross.logging.BusinessLogger;
import com.compartamos.process.campaign.cross.logging.LogCategories;
import com.compartamos.process.campaign.domain.Campaign;
import jakarta.annotation.PostConstruct;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import org.jboss.logging.Logger;
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.core.instrument.Counter;
import io.micrometer.core.instrument.Gauge;
import io.opentelemetry.api.trace.Tracer;
import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.StatusCode;
import io.opentelemetry.context.Scope;
import io.opentelemetry.api.common.AttributeKey;
import org.jboss.logging.MDC;
import java.util.List;
import java.util.Map;
import java.util.concurrent.atomic.AtomicInteger;
```
@ApplicationScoped
```java
public class GetCampaignsUseCase {
private static final Logger LOG_TECH = Logger.getLogger(LogCategories.TECH);
```
private static final Logger LOG_BUSINESS = Logger.getLogger(LogCategories.BUSINESS); // opcional para debug puntual
```java
@Inject MeterRegistry registry;
@Inject Tracer tracer;
private Counter campaignsQueriesTotal;
private final AtomicInteger campaignsLastResultSize = new AtomicInteger(0);
```
@PostConstruct
void initMetrics() {
this.campaignsQueriesTotal = Counter.builder("campaigns_queries_total")
.description("Número de ejecuciones del caso de uso GetCampaigns")
.tag("usecase", "get_campaigns")
```java
.register(registry);
```
Gauge.builder("campaigns_last_result_size", campaignsLastResultSize, AtomicInteger::get)
.description("Tamaño del último resultado de campañas")
.tag("usecase", "get_campaigns")
```java
.register(registry);
```
}
```java
public List<Campaign> execute() {
Span span = Span.current();
span.setAttribute("app.usecase", "GetCampaigns");
span.setAttribute("business.entity", "Campaign");
```
try (Scope scope = span.makeCurrent()) {
```java
LOG_TECH.debug("Invocando GetCampaignsUseCase");
```
List<Campaign> campaigns = List.of(
new Campaign("CMP-001", "Campaña de ahorros"),
new Campaign("CMP-002", "Campaña de créditos")
```java
);
span.setAttribute(AttributeKey.longKey("business.count"), campaigns.size());
String userId = (String) MDC.get("userId");
String channel = (String) MDC.get("channel");
String traceId = (String) MDC.get("traceId");
String correlationId = (String) MDC.get("correlationId");
if (userId != null)        span.setAttribute("security.user.id", userId);
if (channel != null)       span.setAttribute("security.channel", channel);
if (traceId != null)       span.setAttribute("trace.id", traceId);
if (correlationId != null) span.setAttribute("correlation.id", correlationId);
```
BusinessLogger.info(
"GET_CAMPAIGNS",
"Consulta de campañas ejecutada",
Map.of("count", campaigns.size())
```java
);
```
AuditLogger.log(
AuditEvent.builder("GET_CAMPAIGNS")
.entity("Campaign")
.outcome("SUCCESS")
.metadata(Map.of("count", campaigns.size()))
.build()
```java
);
campaignsQueriesTotal.increment();
campaignsLastResultSize.set(campaigns.size());
return campaigns;
```
} catch (Exception e) {
```java
span.recordException(e);
span.setStatus(StatusCode.ERROR, e.getMessage());
throw e;
```
} finally {
```java
span.end();
```
}
}
}
## Ejemplo: HTTP outbound resiliente con MP FT + @ResilientHttpOutbound
Servicio de aplicación
@ApplicationScoped
```java
public class GetExternalUsersService {
```
@Inject
@org.eclipse.microprofile.rest.client.inject.RestClient
```java
ExternalUserClient client;
```
/**
* Ejemplo v1:
* - Marca outbound HTTP con política lógica corporativa.
* - Usa MP FT para resiliencia efectiva.
* - Usa fallbackMethod local (simple y compatible con el spec).
*/
@ResilientHttpOutbound(ResiliencePolicies.EXTERNAL_USERS)
@Timeout
@Retry
@CircuitBreaker
@Fallback(fallbackMethod = "fallbackUsers")
@Counted(value = "external_users_requests_total", extraTags = {"op", "list"})
@Timed(value = "external_users_requests", histogram = true)
```java
public List<UserDto> listUsers() {
return client.list();
```
}
// Degradación segura: si el externo falla, devolvemos lista vacía.
```java
public List<UserDto> fallbackUsers() {
System.out.println(">>> FALLO EXTERNO, EJECUTANDO FALLBACK");
return Collections.emptyList();
```
}
}
Cliente REST
@RegisterRestClient(configKey = "external-users")
@Path("/users")
```java
public interface ExternalUserClient {
```
@GET
@Produces(MediaType.APPLICATION_JSON)
```java
List<UserDto> list();
```
record UserDto(Integer id, String name, String username, String email) {}
}
Configuración efectiva (MP FT + overrides por método)
# Defaults globales
mp.fault.tolerance.retry.max-retries=2
mp.fault.tolerance.retry.jitter=200
mp.fault.tolerance.retry.delay=300
mp.fault.tolerance.timeout.value=1000
mp.fault.tolerance.retry.max-duration=5S
mp.fault.tolerance.circuit-breaker.request-volume-threshold=10
mp.fault.tolerance.circuit-breaker.failure-ratio=0.5
mp.fault.tolerance.circuit-breaker.delay=5S
# Overrides específicos por método
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Timeout/value=1000
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Retry/maxRetries=2
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Retry/delay=300
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Retry/jitter=200
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/CircuitBreaker/requestVolumeThreshold=10
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/CircuitBreaker/failureRatio=0.5
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/CircuitBreaker/delay=5000
## Ejemplo: Policies declarativas y catálogo de resiliencia
Catálogo de políticas
```java
public final class ResiliencePolicies {
```
private ResiliencePolicies() {}
// Políticas genéricas por velocidad / criticidad
```java
public static final String HTTP_FAST   = "policy.http.fast";
public static final String HTTP_MEDIUM = "policy.http.medium";
public static final String HTTP_SLOW   = "policy.http.slow";
```
// Políticas específicas por cliente/downstream
```java
public static final String EXTERNAL_USERS = "external-users";
```
}
Policies en application.properties
# FAST: servicios ultra rápidos / idempotentes
resilience.policies.policy.http.fast.enabled=true
resilience.policies.policy.http.fast.timeout=500
resilience.policies.policy.http.fast.retry.enabled=true
resilience.policies.policy.http.fast.retry.max-attempts=1
resilience.policies.policy.http.fast.circuit-breaker.enabled=true
# MEDIUM: default corporativo
resilience.policies.policy.http.medium.enabled=true
resilience.policies.policy.http.medium.timeout=1500
resilience.policies.policy.http.medium.retry.enabled=true
resilience.policies.policy.http.medium.retry.max-attempts=2
resilience.policies.policy.http.medium.circuit-breaker.enabled=true
# SLOW: integraciones pesadas / legacy
resilience.policies.policy.http.slow.enabled=true
resilience.policies.policy.http.slow.timeout=5000
resilience.policies.policy.http.slow.retry.enabled=true
resilience.policies.policy.http.slow.retry.max-attempts=3
resilience.policies.policy.http.slow.circuit-breaker.enabled=true
# Política específica EXTERNAL_USERS (hereda de MEDIUM)
resilience.policies.external-users.base=policy.http.medium
resilience.policies.external-users.retry.max-attempts=3
Config helper
@ApplicationScoped
```java
public class ResiliencePolicyConfig {
```
@Inject
```java
Config config;
public ResiliencePolicy get(String policyId) {
String keyPrefix = "resilience.policies." + policyId + ".";
String basePolicyId = getString(keyPrefix + "base", null);
```
ResiliencePolicy basePolicy =
(basePolicyId != null && !basePolicyId.isBlank())
? get(basePolicyId)
```java
: defaultPolicy();
ResiliencePolicy result = new ResiliencePolicy(basePolicy);
overrideBool(keyPrefix + "enabled", result::setEnabled);
overrideLong(keyPrefix + "timeout", result::setTimeoutMs);
overrideBool(keyPrefix + "retry.enabled", result::setRetryEnabled);
overrideInt(keyPrefix + "retry.max-attempts", result::setRetryMaxAttempts);
overrideBool(keyPrefix + "circuit-breaker.enabled", result::setCircuitBreakerEnabled);
return result;
```
}
// ... (helpers y DTO interno como ya los tienes)
}
Interceptor de trazabilidad de policies
@ResilientHttpOutbound
@Interceptor
@Priority(Interceptor.Priority.APPLICATION + 10)
```java
public class ResilientHttpOutboundInterceptor {
```
@Inject
```java
Logger log;
```
@Inject
```java
ResiliencePolicyConfig policyConfig;
```
@AroundInvoke
```java
public Object around(InvocationContext ctx) throws Exception {
ResilientHttpOutbound annotation = getAnnotation(ctx);
String policyId = (annotation != null) ? annotation.value() : "policy.http.medium";
ResiliencePolicyConfig.ResiliencePolicy policy = policyConfig.get(policyId);
```
if (policy.isEnabled()) {
log.debugf(
"ResilientHttpOutbound policy='%s' applied to %s#%s",
policyId,
ctx.getMethod().getDeclaringClass().getSimpleName(),
ctx.getMethod().getName()
```java
);
```
}
```java
return ctx.proceed();
```
}
// ...
}
## Ejemplo —Uso obligatorio de Timeout, Retry y Circuit Breaker
1) Código del servicio resiliente
GetExternalUsersService.java
@ApplicationScoped
```java
public class GetExternalUsersService {
```
@Inject
@RestClient
```java
ExternalUserClient client;
```
/**
* Ejemplo de cumplimiento del lineamiento:
* - Timeout finito (@Timeout)
* - Reintentos controlados para operación idempotente (@Retry)
* - Circuit Breaker para proteger a la aplicación (@CircuitBreaker)
* - Fallback para degradación segura (@Fallback)
* - Política lógica definida por el framework (@ResilientHttpOutbound)
*/
@ResilientHttpOutbound(ResiliencePolicies.EXTERNAL_USERS)
@Timeout                      // Tiempo efectivo configurable vía MP FT o override
@Retry                        // Solo permitido porque list() es idempotente
@CircuitBreaker               // Protege de fallas recurrentes del externo
@Fallback(fallbackMethod = "fallbackUsers")
```java
public List<UserDto> listUsers() {
return client.list();
```
}
// Degradación segura: para GET devolvemos lista vacía
```java
public List<UserDto> fallbackUsers() {
System.out.println(">>> FALLO EXTERNO, EJECUTANDO FALLBACK");
return Collections.emptyList();
```
}
}
2) Configuración por política declarativa (no aplica comportamiento aún, pero documenta la intención)
application.properties
# Política EXTERNAL_USERS hereda de MEDIUM
resilience.policies.external-users.base=policy.http.medium
resilience.policies.external-users.retry.max-attempts=3
Interpretación según lineamiento:
Timeout MEDIUM → ~1500 ms
Reintentos MEDIUM → 2–3
Circuit Breaker habilitado
Esta integración no es FAST ni SLOW
3) Configuración efectiva (MicroProfile Fault Tolerance)
Estos son los valores que realmente aplican en tiempo de ejecución:
# ---- TIMEOUT (obligatorio) ----
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Timeout/value=1000
# ---- RETRY (solo porque es idempotente) ----
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Retry/maxRetries=2
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Retry/delay=300
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/Retry/jitter=200
# ---- CIRCUIT BREAKER (protege la aplicación) ----
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/CircuitBreaker/requestVolumeThreshold=10
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/CircuitBreaker/failureRatio=0.5
com.compartamos.process.campaign.application.service.GetExternalUsersService/listUsers/CircuitBreaker/delay=5000
4) Logs de trazabilidad de resiliencia (interceptor estándar)
Salida generada por ResilientHttpOutboundInterceptor:
DEBUG ResilientHttpOutbound policy='external-users'
applied to GetExternalUsersService#listUsers
Esto permite verificar:
Qué política se aplicó
A qué método
Antes del timeout/retry/CB
5) Ejemplo de ejecución con falla → Retry + Fallback
Simulación del externo fallando
Caused by: java.net.SocketTimeoutException: Read timed out
Salida esperada en consola
>>> FALLO EXTERNO, EJECUTANDO FALLBACK
Respuesta final del servicio
[]
Esto demuestra:
Timeout detectado en el cliente externo
Retry agotado (según maxRetries)
Circuit Breaker actualiza estadística de fallos
Fallback responde con una degradación segura
6) Ejemplo de error crítico (sin degradación segura)
Si el método fuese crítico (ej: POST que genera una orden):
@Fallback(handler = Fallbacks.PropagateAsUncheckedString.class)
Y el fallback lanzaría:
```java
throw new ResilienceException("Downstream failure: timeout", cause);
```
Que será estandarizado como ApiError por el GlobalExceptionMapper.
## Ejemplo de prueba unitaria de servicio de integración HTTP
Objetivo
Validar, de forma aislada (sin Quarkus ni contenedor CDI), la lógica básica del servicio GetExternalUsersService:
Que el servicio devuelve los usuarios que le entrega el cliente externo (ExternalUserClient).
Que el método de degradación segura fallbackUsers() retorna una lista vacía.
Clase de servicio (referencia)
Esta clase fue definida en la sección de resiliencia
```java
package com.compartamos.process.campaign.application.service;
import com.compartamos.process.campaign.infrastructure.proxy.ExternalUserClient;
import com.compartamos.process.campaign.infrastructure.proxy.ExternalUserClient.UserDto;
import com.compartamos.process.campaign.cross.resilience.ResilientHttpOutbound;
import com.compartamos.process.campaign.cross.resilience.ResiliencePolicies;
import io.micrometer.core.annotation.Counted;
import io.micrometer.core.annotation.Timed;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import org.eclipse.microprofile.faulttolerance.Timeout;
import org.eclipse.microprofile.faulttolerance.Retry;
import org.eclipse.microprofile.faulttolerance.CircuitBreaker;
import org.eclipse.microprofile.faulttolerance.Fallback;
import java.util.Collections;
import java.util.List;
```
@ApplicationScoped
```java
public class GetExternalUsersService {
```
@Inject
@org.eclipse.microprofile.rest.client.inject.RestClient
```java
ExternalUserClient client;
```
/**
* Ejemplo v1:
* - Marca outbound HTTP con política lógica corporativa.
* - Usa MP FT para resiliencia efectiva.
* - Usa fallbackMethod local (simple y totalmente compatible con el spec).
*/
@ResilientHttpOutbound(ResiliencePolicies.EXTERNAL_USERS)
@Timeout
@Retry
@CircuitBreaker
@Fallback(fallbackMethod = "fallbackUsers")
@Counted(value = "external_users_requests_total", extraTags = {"op", "list"})
@Timed(value = "external_users_requests", histogram = true)
```java
public List<UserDto> listUsers() {
return client.list();
```
}
// Degradación segura: si el externo falla, devolvemos lista vacía.
```java
public List<UserDto> fallbackUsers() {
System.out.println(">>> FALLO EXTERNO, EJECUTANDO FALLBACK");
return Collections.emptyList();
```
}
}
Prueba unitaria (sin Quarkus)
Archivo:
src/test/java/com/compartamos/process/campaign/application/service/GetExternalUsersServiceTest.java
```java
package com.compartamos.process.campaign.application.service;
import com.compartamos.process.campaign.infrastructure.proxy.ExternalUserClient;
import com.compartamos.process.campaign.infrastructure.proxy.ExternalUserClient.UserDto;
import org.junit.jupiter.api.Test;
import java.lang.reflect.Field;
import java.util.List;
import static org.junit.jupiter.api.Assertions.*;
```
class GetExternalUsersServiceTest {
// Stub simple de ExternalUserClient (no usa Quarkus ni HTTP real)
static class StubExternalUserClient implements ExternalUserClient {
@Override
```java
public List<UserDto> list() {
```
return List.of(
new UserDto(1, "Alice", "alice", "alice@test.com"),
new UserDto(2, "Bob", "bob", "bob@test.com")
```java
);
```
}
}
// Inyección manual vía reflexión del cliente en el service
private void injectClient(GetExternalUsersService service, ExternalUserClient client) throws Exception {
```java
Field field = GetExternalUsersService.class.getDeclaredField("client");
field.setAccessible(true);
field.set(service, client);
```
}
@Test
void should_return_users_from_client() throws Exception {
// given
```java
ExternalUserClient stub = new StubExternalUserClient();
GetExternalUsersService service = new GetExternalUsersService();
injectClient(service, stub);
```
// when
```java
List<UserDto> result = service.listUsers();
```
// then
```java
assertNotNull(result);
assertEquals(2, result.size());
assertEquals("Alice", result.get(0).name());
assertEquals("Bob", result.get(1).name());
```
}
@Test
void should_return_empty_list_when_fallback_is_called_directly() {
// given
```java
GetExternalUsersService service = new GetExternalUsersService();
```
// when
```java
List<UserDto> result = service.fallbackUsers();
```
// then
```java
assertNotNull(result);
assertTrue(result.isEmpty());
```
}
}
Puntos para resaltar:
No se levanta Quarkus ni CDI → ejecución rápida, ideal para feedback temprano.
Se utiliza un stub del cliente externo para no depender de la red ni de endpoints externos.
Se prueba explícitamente el comportamiento del fallbackUsers() (degradación segura).
## Ejemplo de prueba de integración REST + resiliencia
Objetivo
Validar de extremo a extremo el comportamiento del endpoint REST:
Que el recurso ExternalUserResource expone correctamente los datos del servicio.
Que, ante fallos del externo, el endpoint sigue respondiendo HTTP 200 con lista vacía (fallback).
Que se exponen métricas técnicas de la operación (external_users_requests_total) en /metrics.
Recurso REST
```java
package com.compartamos.process.campaign.interfaces.rest;
import com.compartamos.process.campaign.application.service.GetExternalUsersService;
import com.compartamos.process.campaign.infrastructure.proxy.ExternalUserClient.UserDto;
import jakarta.annotation.security.PermitAll;
import jakarta.inject.Inject;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import java.util.List;
```
@Path("/v1/external/users-alt")
```java
public class ExternalUserResource {
```
@Inject
```java
GetExternalUsersService service;
```
@GET
@PermitAll
@Produces(MediaType.APPLICATION_JSON)
```java
public List<UserDto> list() {
return service.listUsers();
```
}
}
Prueba de integración REST + resiliencia
Archivo:
src/test/java/com/compartamos/process/campaign/interfaces/rest/ExternalUserResourceTest.java
```java
package com.compartamos.process.campaign.interfaces.rest;
import io.quarkus.test.junit.QuarkusTest;
import org.junit.jupiter.api.Test;
import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.containsString;
import static org.hamcrest.Matchers.greaterThanOrEqualTo;
```
@QuarkusTest
class ExternalUserResourceTest {
/**
* Valida que el endpoint REST responde 200 y devuelve un arreglo JSON
* (lista de usuarios o lista vacía si se ejecuta el fallback).
*
* - Caso normal: JSONPlaceholder responde bien -> lista con elementos.
* - Caso fallo externo (timeout/error): entra fallback -> "[]".
* En ambos casos el contrato HTTP es 200 y un array JSON.
*/
@Test
void should_return_array_and_http_200_even_when_fallback_is_used() {
given()
.when()
.get("/v1/external/users-alt")
.then()
.statusCode(200)
// size() >= 0 asegura que el cuerpo es un arreglo JSON (lista vacía o con elementos)
```java
.body("size()", greaterThanOrEqualTo(0));
```
}
/**
* Valida que se expongan métricas relacionadas al servicio externo,
* en particular el contador "external_users_requests_total" definido en @Counted.
*/
@Test
void should_expose_micrometer_metrics_for_external_users() {
given()
.when()
.get("/metrics")
.then()
.statusCode(200)
```java
.body(containsString("external_users_requests_total"));
```
}
}
Dependencias de test necesarias (ya incluidas en el POM del arquetipo)
En el pom.xml del servicio se deben tener, al menos, estas dependencias de test:
<dependencies>
<!-- ... demás dependencias del servicio ... -->
<!-- Testing con Quarkus -->
<dependency>
<groupId>io.quarkus</groupId>
<artifactId>quarkus-junit5</artifactId>
<scope>test</scope>
</dependency>
<dependency>
<groupId>io.rest-assured</groupId>
<artifactId>rest-assured</artifactId>
<scope>test</scope>
</dependency>
</dependencies>
Ejecución de las pruebas
Prueba unitaria:
mvn -q -Dtest=GetExternalUsersServiceTest test
Prueba de integración REST:
mvn -q -Dtest=ExternalUserResourceTest test
Puntos a resaltar :
El test de integración levanta Quarkus en modo test, incluyendo:
Rutas Camel.
Cliente REST (ExternalUserClient) con su configuración real.
Configuración de resiliencia (Timeout, Retry, CircuitBreaker, Fallback).
Micrometer + Prometheus en /metrics.
Se valida no solo el status HTTP, sino también que:
El contrato REST se mantiene estable ante fallos del externo (degradación controlada).
Las métricas técnicas definidas en el servicio efectivamente se exponen (observabilidad + calidad).
Este patrón puede reutilizarse como plantilla corporativa para:
Servicios REST que llamen a sistemas externos.
Verificación de la integración entre resiliencia, observabilidad y contratos API.
## Ejemplo Contrato REST External Users
Este ejemplo muestra cómo un servicio REST generado con el Framework de Integración aplica los lineamientos de contratos API  para un caso sencillo de consulta de usuarios externos.
Contexto funcional
Dominio: Party / Customers (referencial externo de usuarios).
Endpoint: GET /v1/external/users-alt
Responsabilidad: exponer una lista de usuarios obtenidos desde un sistema externo (JSONPlaceholder en ambiente de demo).
a) Recurso REST versionado y seguro
```java
package com.compartamos.process.campaign.infrastructure.rest;
import com.compartamos.process.campaign.application.service.GetExternalUsersService;
import com.compartamos.process.campaign.infrastructure.proxy.ExternalUserClient.UserDto;
import com.compartamos.process.campaign.infrastructure.rest.dto.ExternalUserResponse;
import jakarta.annotation.security.PermitAll;
import jakarta.inject.Inject;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.HeaderParam;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import java.util.List;
import org.eclipse.microprofile.openapi.annotations.Operation;
import org.eclipse.microprofile.openapi.annotations.media.Content;
import org.eclipse.microprofile.openapi.annotations.media.Schema;
import org.eclipse.microprofile.openapi.annotations.responses.APIResponse;
import org.eclipse.microprofile.openapi.annotations.tags.Tag;
```
@Tag(name = "External Users", description = "Consulta de usuarios externos de referencia")
@Path("/v1/external/users-alt")
```java
public class ExternalUserResource {
```
@Inject
```java
GetExternalUsersService service;
```
@GET
@PermitAll
@Produces(MediaType.APPLICATION_JSON)
@Operation(
summary = "Lista usuarios externos",
description = "Retorna una lista de usuarios obtenidos de un sistema externo de referencia."
)
@APIResponse(
responseCode = "200",
description = "Lista de usuarios externos",
content = @Content(
mediaType = "application/json",
schema = @Schema(implementation = ExternalUserResponse.class)
)
)
```java
public List<ExternalUserResponse> list(
```
@HeaderParam("X-Correlation-Id") String correlationId,
@HeaderParam("X-Business-Id") String businessId) {
```java
List<UserDto> users = service.listUsers();
```
return users.stream()
.map(u -> new ExternalUserResponse(
u.id(),
u.name(),
u.username(),
u.email()
))
```java
.toList();
```
}
}
Cómo aplica los lineamientos:
Versionado REST
La ruta está versionada: /v1/external/users-alt.
Una futura versión incompatible deberá exponerse como /v2/external/users-alt.
OpenAPI como source of truth
El contrato OpenAPI 3.x se genera automáticamente a partir de las anotaciones JAX-RS y Bean Validation del recurso.
El endpoint aparecerá en /q/openapi y será publicado en el API Gateway / Portal.
Headers corporativos
El recurso recibe explícitamente X-Correlation-Id y X-Business-Id como @HeaderParam, permitiendo:
Documentarlos en OpenAPI como headers de entrada.
Propagarlos en la cadena de integración (hacia clientes REST, Kafka, etc.).
b) DTO de contrato REST (modelo tipado)
```java
package com.compartamos.process.campaign.infrastructure.rest.dto;
```
/**
* DTO de contrato REST expuesto en /v1/external/users-alt.
*
* - Modelo tipado, sin Map ni Object genéricos.
* - Vive en la capa de interfaces/adaptadores REST (infrastructure.rest.dto).
* - Si el contrato cambia de forma incompatible, se deberá versionar
*   (por ejemplo ExternalUserResponseV2 o paquete ...rest.v2.dto).
*/
```java
public record ExternalUserResponse(
```
Integer id,
String name,
String username,
String email
) {
}
Cómo aplica los lineamientos:
Modelos tipados obligatorios
El contrato REST expone un tipo fuerte ExternalUserResponse, sin Map<String,Object> ni Object genéricos.
Este DTO está en infrastructure.rest.dto, separado de:
Entidades de dominio (domain.*).
Entidades de persistencia (infrastructure.repository.*).
DTO de sistemas externos (infrastructure.proxy.* → UserDto).
Separación productor ⇄ consumidor externo (anti-acoplamiento)
El recurso REST no devuelve directamente UserDto (DTO del sistema externo), sino que mapea explícitamente a ExternalUserResponse.
Esto permite:
Cambiar el proveedor externo sin romper el contrato público expuesto a canales y otros servicios.
Evolucionar el contrato REST con su versión propia (/v2, ExternalUserResponseV2, etc.).
Generación del contrato
El contrato es generado automáticamente en runtime por Quarkus a partir de:
anotaciones JAX-RS del recurso REST,
modelos DTO tipados ubicados en infrastructure.rest.dto,
reglas de Bean Validation aplicadas en los DTO,
anotaciones opcionales de MP OpenAPI usadas para enriquecer documentación.
El contrato se expone en:
/q/openapi — documento OpenAPI en JSON/YAML.
/q/swagger-ui — interfaz visual de documentación para equipos de desarrollo.
Este documento es utilizado posteriormente por:
Pipeline de CI/CD, que:
descarga /q/openapi,
valida el archivo usando Spectral y reglas corporativas,
ejecuta contract testing.
API Portal corporativo, donde el contrato aprobado se publica automáticamente.
Equipos consumidores, que lo usan como fuente única de especificación funcional y técnica.
análisis forense y cumplimiento regulatorio.
Evolución del contrato
Si se requiere agregar campos opcionales → cambio no rompiente, se mantiene /v1 y se amplía ExternalUserResponse.
Si se requiere un cambio rompiente (por ejemplo, cambiar tipos o eliminar campos), se debe:
Exponer /v2/external/users-alt.
Introducir ExternalUserResponseV2 o mover a paquete ...rest.v2.dto.
Versión | Fecha | Modificaciones | Modificar por
--- | --- | --- | ---
1.0 | 17/11/2025 | Versión Inicial | Sandro Reátegui
1.1 | 26/11/2025 | Adecuación starters + | Sandro Reátegui
1.2 | 02/12/2025 | Detalle Arquitectura Hexagonal + Resumen | Sandro Reátegui
Concepto | Rol | Ejemplo
--- | --- | ---
BIAN Domain | Macro área funcional | Marketing Campaign Management
BIAN Capability | Capacidad funcional específica | Execute Campaign
Bounded Context DDD | Implementación de software de esa capacidad | Campaign Execution
Política | Caso de uso | Latencia ref. | Reintentos
--- | --- | --- | ---
FAST | Integraciones rápidas / cache / idempotentes | ≤500ms | 0–1
MEDIUM | Default corporativo | 500–1500ms | 2
SLOW | Integraciones pesadas / legacy | ≤5000ms | 3
Externa específica | Ej: external-users | depende | hereda de medium
Elemento | Valor
--- | ---
Dominio BIAN | Marketing Campaign Management
Capacidad BIAN | Execute Campaign
Bounded Context (DDD) | Campaign Execution
Tipo de servicio | Orquestación / Proceso / Integración
Lineamiento | Cumple | Detalle
--- | --- | ---
Timeout finito | ✔ | 1000 ms
Retry solo para idempotentes | ✔ | GET externo
Máximo de reintentos | ✔ | maxRetries = 2
Backoff + jitter obligatorio | ✔ | delay=300, jitter=200
Circuit Breaker requerido | ✔ | requestVolumeThreshold=10
fallback obligatorio | ✔ | método fallbackUsers()