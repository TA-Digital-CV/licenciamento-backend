# Arquitetura Técnica - Sistema de Gestão de Licenças Emitidas

## Sistema de Licenciamento de Cabo Verde

## 1. Arquitetura Spring Boot DDD

```mermaid
graph TD
    A[React Frontend] --> B[Spring Boot REST Controllers]
    B --> C[Application Services]
    C --> D[Domain Services]
    D --> E[Domain Model]
    E --> F[Repository Layer - JPA]
    F --> G[PostgreSQL 15+]

    B --> H[JWT Authentication Filter]
    H --> I[Spring Security]

    C --> J[Integration Services]
    J --> K[Activity Server]
    J --> L[Financial System]
    J --> M[Notification System]
    J --> N[Parametrização System]

    D --> O[Cache Layer - Redis]
    F --> P[Event Store]

    subgraph "Presentation Layer"
        A
    end

    subgraph "Web Layer (Spring Boot)"
        B
        H
        I
    end

    subgraph "Application Layer"
        C
    end

    subgraph "Domain Layer"
        D
        E
    end

    subgraph "Infrastructure Layer"
        F
        G
        O
        P
    end

    subgraph "External Bounded Contexts"
        K
        L
        M
        N
    end
```

### 1.1 Bounded Context: Gestão de Licenças Emitidas

#### Aggregates Principais:

- **IssuedLicense** - Aggregate Root para licenças emitidas com controle de ciclo de vida

- **Establishment** - **Aggregate Root para estabelecimentos registrados (NOVO)**

- **EstablishmentRegistration** - **Aggregate para processos de registro de estabelecimentos (NOVO)**

- **LicenseIssuer** - Aggregate para órgãos emissores de licenças

- **LicenseHolder** - Aggregate base para titulares de licenças

- **IndividualHolder** - Aggregate para pessoas físicas titulares

- **CorporateHolder** - Aggregate para pessoas jurídicas titulares

- **LegalRepresentative** - Entity para representantes legais

- **LicenseRenewal** - Aggregate para processos de renovação

- **LicenseAmendment** - Aggregate para alterações de licenças

- **LicenseTransfer** - Aggregate para transferências de titularidade

#### Domain Services:

- **EstablishmentValidationService** - **Validações de registro e conformidade de estabelecimentos (NOVO)**

- **GeoreferencingService** - **Validação de coordenadas GPS e dados cartográficos (NOVO)**

- **DocumentValidationService** - **Validação de documentação específica por segmento (NOVO)**

- **ComplianceService** - **Verificação de conformidade com legislação vigente (NOVO)**

- **LicenseValidationService** - Validações de regras de negócio e integridade

- **HolderValidationService** - Validações específicas de titulares (PF/PJ)

- **NotificationService** - Gestão de alertas e notificações

- **FeeCalculationService** - Cálculo de taxas e valores

- **AuditService** - Rastreabilidade e auditoria completa

- **LicenseLifecycleService** - Gestão do ciclo de vida das licenças

## 2. Stack Tecnológico Spring Boot DDD

### 2.1 Frontend (Presentation Layer)

- **React 18** - Framework principal com TypeScript

- **Axios** - Cliente HTTP para integração com Spring Boot REST APIs

- **TanStack Query** - Cache e sincronização de estado servidor

- **React Hook Form** - Gerenciamento de formulários com validação

- **React Router v6** - Roteamento SPA

- **Material-UI v5** - Design System corporativo

- **Recharts** - Visualização de dados e dashboards

- **Date-fns** - Manipulação de datas e períodos

### 2.2 Backend Spring Boot (Domain + Application + Infrastructure)

#### Framework Principal

- **Spring Boot 3.2+** - Framework principal com Java 17+

- **Spring Web MVC** - Controllers REST e tratamento de requisições

- **Spring Security 6** - Autenticação e autorização com JWT

- **Spring Data JPA** - Abstração de persistência e repositórios

#### Domain Layer

- **Java 17+** - Linguagem principal com records e pattern matching

- **Bean Validation (JSR-303)** - Validação de Value Objects e Entities

- **Domain Events** - Comunicação entre Aggregates via Spring Events

- **MapStruct** - Mapeamento entre DTOs e Domain Objects

#### Application Layer

- **Spring Boot Starters** - Configuração automática de dependências

- **CQRS Pattern** - Separação Command/Query com Spring Components

- **Spring Transaction Management** - Controle transacional declarativo

- **Spring Validation** - Validação de entrada via annotations

#### Infrastructure Layer

- **PostgreSQL 15+** - Banco de dados relacional principal

- **HikariCP** - Pool de conexões de alta performance

- **Flyway** - Versionamento e migração de schema

- **Redis** - Cache distribuído e sessões via Spring Data Redis

- **Spring Boot Actuator** - Monitoramento e métricas

- **Logback** - Logging estruturado com SLF4J

### 2.3 Segurança e Autenticação

- **JWT (JSON Web Tokens)** - Autenticação stateless

- **Spring Security JWT** - Implementação segura de JWT

- **BCrypt** - Hash de senhas com salt

- **CORS Configuration** - Configuração de Cross-Origin Resource Sharing

- **Rate Limiting** - Controle de taxa de requisições

### 2.4 Infraestrutura e DevOps

- **Docker** - Containerização de serviços

- **Docker Compose** - Orquestração local de containers

- **NGINX** - Load balancer e proxy reverso

- **Prometheus + Grafana** - Observabilidade e métricas de negócio

- **Sentry** - Monitoramento de erros e performance

## 3. Estrutura de Pacotes Spring Boot

### 3.1 Organização do Projeto

```
src/main/java/cv/gov/licensing/
├── LicensingApplication.java                    # Classe principal Spring Boot
├── config/                                      # Configurações
│   ├── SecurityConfig.java                     # Configuração Spring Security
│   ├── JwtConfig.java                          # Configuração JWT
│   ├── DatabaseConfig.java                     # Configuração PostgreSQL
│   ├── RedisConfig.java                        # Configuração Redis
│   ├── CorsConfig.java                         # Configuração CORS
│   └── GeospatialConfig.java                   # Configuração GIS/GPS (NOVO)
├── web/                                         # Web Layer (Controllers)
│   ├── controller/
│   │   ├── LicenseController.java
│   │   ├── HolderController.java
│   │   ├── IssuerController.java
│   │   ├── EstablishmentController.java         # NOVO
│   │   ├── EstablishmentRegistrationController.java # NOVO
│   │   └── EstablishmentDocumentController.java # NOVO
│   ├── dto/                                     # Data Transfer Objects
│   │   ├── request/
│   │   └── response/
│   └── filter/
│       └── JwtAuthenticationFilter.java
├── application/                                 # Application Layer
│   ├── service/
│   │   ├── LicenseApplicationService.java
│   │   ├── HolderApplicationService.java
│   │   ├── AuthenticationService.java
│   │   ├── EstablishmentApplicationService.java # NOVO
│   │   ├── EstablishmentRegistrationService.java # NOVO
│   │   └── EstablishmentQueryService.java       # NOVO
│   ├── command/                                 # CQRS Commands
│   └── query/                                   # CQRS Queries
├── domain/                                      # Domain Layer
│   ├── model/                                   # Aggregates e Entities
│   │   ├── license/
│   │   │   ├── IssuedLicense.java
│   │   │   ├── LicenseNumber.java              # Value Object
│   │   │   └── LicenseStatus.java              # Enum
│   │   ├── holder/
│   │   │   ├── LicenseHolder.java
│   │   │   ├── IndividualHolder.java
│   │   │   └── CorporateHolder.java
│   │   ├── issuer/
│   │   │   └── LicenseIssuer.java
│   │   └── establishment/                       # NOVO - Módulo Estabelecimentos
│   │       ├── Establishment.java              # Aggregate Root
│   │       ├── EstablishmentId.java            # Value Object
│   │       ├── MatricialNumber.java            # Value Object
│   │       ├── GeoLocation.java                # Value Object GPS
│   │       ├── EstablishmentClassification.java # Entity
│   │       ├── EstablishmentDocument.java      # Entity
│   │       ├── EstablishmentRegistration.java  # Aggregate
│   │       └── EstablishmentSegment.java       # Enum
│   ├── service/                                 # Domain Services
│   │   ├── LicenseValidationService.java
│   │   ├── HolderValidationService.java
│   │   ├── EstablishmentValidationService.java # NOVO
│   │   ├── GeoreferencingService.java          # NOVO
│   │   ├── DocumentValidationService.java      # NOVO
│   │   └── ComplianceService.java              # NOVO
│   ├── repository/                              # Repository Interfaces
│   │   ├── LicenseRepository.java
│   │   ├── HolderRepository.java
│   │   ├── EstablishmentRepository.java        # NOVO
│   │   ├── EstablishmentClassificationRepository.java # NOVO
│   │   └── EstablishmentRegistrationRepository.java # NOVO
│   └── event/                                   # Domain Events
│       ├── LicenseIssuedEvent.java
│       └── LicenseExpiredEvent.java
└── infrastructure/                              # Infrastructure Layer
    ├── persistence/                             # JPA Implementation
    │   ├── entity/                             # JPA Entities
    │   ├── repository/                         # JPA Repository Implementation
    │   └── mapper/                             # Entity-Domain Mappers
    ├── cache/                                   # Redis Implementation
    ├── integration/                             # External Services
    └── security/                                # Security Implementation
        ├── JwtTokenProvider.java
        └── UserDetailsServiceImpl.java
```

### 3.2 Configuração de Dependências (pom.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>

    <groupId>cv.gov</groupId>
    <artifactId>licensing-system</artifactId>
    <version>1.0.0</version>
    <name>Sistema de Licenciamento</name>

    <properties>
        <java.version>17</java.version>
        <mapstruct.version>1.5.5.Final</mapstruct.version>
        <jjwt.version>0.12.3</jjwt.version>
    </properties>

    <dependencies>
        <!-- Spring Boot Starters -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!-- Database -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.flywaydb</groupId>
            <artifactId>flyway-core</artifactId>
        </dependency>

        <!-- JWT -->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-api</artifactId>
            <version>${jjwt.version}</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-impl</artifactId>
            <version>${jjwt.version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-jackson</artifactId>
            <version>${jjwt.version}</version>
            <scope>runtime</scope>
        </dependency>

        <!-- MapStruct -->
        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct</artifactId>
            <version>${mapstruct.version}</version>
        </dependency>

        <!-- Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.mapstruct</groupId>
                            <artifactId>mapstruct-processor</artifactId>
                            <version>${mapstruct.version}</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### 3.3 Configuração de Propriedades (application.yml)

```yaml
spring:
  application:
    name: licensing-system

  datasource:
    url: jdbc:postgresql://localhost:5432/licensing_db
    username: ${DB_USERNAME:licensing_user}
    password: ${DB_PASSWORD:licensing_pass}
    driver-class-name: org.postgresql.Driver
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000

  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: false
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect
        format_sql: true
        use_sql_comments: true
        jdbc:
          batch_size: 25
        order_inserts: true
        order_updates: true

  flyway:
    enabled: true
    locations: classpath:db/migration
    baseline-on-migrate: true

  data:
    redis:
      host: ${REDIS_HOST:localhost}
      port: ${REDIS_PORT:6379}
      password: ${REDIS_PASSWORD:}
      timeout: 2000ms
      lettuce:
        pool:
          max-active: 8
          max-idle: 8
          min-idle: 0

  security:
    jwt:
      secret: ${JWT_SECRET:mySecretKey}
      expiration: 86400000 # 24 hours
      refresh-expiration: 604800000 # 7 days

  servlet:
    multipart:
      max-file-size: 10MB
      max-request-size: 10MB

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: when-authorized

logging:
  level:
    cv.gov.licensing: DEBUG
    org.springframework.security: DEBUG
    org.hibernate.SQL: DEBUG
  pattern:
    console: '%d{yyyy-MM-dd HH:mm:ss} - %msg%n'
    file: '%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n'
  file:
    name: logs/licensing-system.log

app:
  cors:
    allowed-origins: ${CORS_ORIGINS:http://localhost:3000,http://localhost:5173}
    allowed-methods: GET,POST,PUT,DELETE,OPTIONS
    allowed-headers: '*'
    allow-credentials: true
```

### 3.4 Padrões de Injeção de Dependência

#### Constructor Injection (Recomendado)

```java
@RestController
@RequestMapping("/api/v1/licenses")
@RequiredArgsConstructor
public class LicenseController {

    private final LicenseApplicationService licenseService;
    private final LicenseMapper licenseMapper;

    @GetMapping
    public ResponseEntity<PagedResponse<LicenseResponse>> getAllLicenses(
            @Valid @ModelAttribute LicenseSearchRequest request,
            Pageable pageable) {

        Page<IssuedLicense> licenses = licenseService.searchLicenses(request, pageable);
        PagedResponse<LicenseResponse> response = licenseMapper.toPagedResponse(licenses);

        return ResponseEntity.ok(response);
    }
}
```

#### Service Layer com Transações

```java
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class LicenseApplicationService {

    private final LicenseRepository licenseRepository;
    private final HolderRepository holderRepository;
    private final LicenseValidationService validationService;
    private final ApplicationEventPublisher eventPublisher;

    @Transactional
    public IssuedLicense issueLicense(IssueLicenseCommand command) {
        // Validações de domínio
        validationService.validateLicenseIssuance(command);

        // Buscar titular
        LicenseHolder holder = holderRepository.findById(command.getHolderId())
            .orElseThrow(() -> new HolderNotFoundException(command.getHolderId()));

        // Criar licença
        IssuedLicense license = IssuedLicense.builder()
            .licenseNumber(generateLicenseNumber())
            .holder(holder)
            .licenseType(command.getLicenseType())
            .issuer(command.getIssuer())
            .issueDate(LocalDate.now())
            .expiryDate(calculateExpiryDate(command.getLicenseType()))
            .status(LicenseStatus.ACTIVE)
            .build();

        // Salvar
        IssuedLicense savedLicense = licenseRepository.save(license);

        // Publicar evento de domínio
        eventPublisher.publishEvent(new LicenseIssuedEvent(savedLicense.getId()));

        return savedLicense;
    }
}
```

## 4. Rotas Frontend (Presentation Layer)

| Rota                       | Propósito                                   | Aggregate/Domain Service |
| -------------------------- | ------------------------------------------- | ------------------------ |
| `/licencas`                | Lista principal com filtros RLS             | IssuedLicense            |
| `/licencas/:id`            | Detalhes completos via Aggregate Root       | IssuedLicense            |
| `/licencas/:id/renovar`    | Workflow de renovação integrado             | LicenseRenewal           |
| `/licencas/:id/alterar`    | Alterações via Domain Services              | LicenseAmendment         |
| `/licencas/:id/transferir` | Transferência de titularidade               | LicenseTransfer          |
| `/licencas/:id/suspender`  | Suspensão com auditoria completa            | IssuedLicense            |
| `/licencas/:id/historico`  | Histórico via Event Sourcing                | AuditService             |
| `/dashboard`               | Métricas de negócio em tempo real           | DashboardService         |
| `/alertas`                 | Central de alertas baseada em Domain Events | NotificationService      |
| `/relatorios`              | Relatórios com dados normalizados           | ReportingService         |
| `/auditoria`               | Logs estruturados com rastreabilidade       | AuditService             |

## 4. APIs DDD (Application Layer)

### 4.1 LicenseIssuer Aggregate APIs

#### Query: Consulta de Emissores

```
GET /api/v1/license-issuers
```

**Query Parameters:**

| Parâmetro | Tipo    | Obrigatório | Descrição                     | Validação Domain |
| --------- | ------- | ----------- | ----------------------------- | ---------------- |
| page      | number  | false       | Página atual (padrão: 1)      | PaginationVO     |
| limit     | number  | false       | Itens por página (padrão: 25) | PaginationVO     |
| search    | string  | false       | Busca por nome ou código      | SearchVO         |
| is_active | boolean | false       | Filtro por status ativo       | StatusVO         |

**Response:**

```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Ministério da Economia Marítima",
      "code": "MEM",
      "description": "Responsável por licenças marítimas e portuárias",
      "contact_info": {
        "email": "licencas@mem.gov.cv",
        "phone": "+238 260 3000",
        "address": "Palácio do Governo, Praia"
      },
      "is_active": true,
      "created_at": "2024-01-01T00:00:00Z"
    }
  ],
  "pagination": {
    "total": 15,
    "page": 1,
    "limit": 25,
    "total_pages": 1
  }
}
```

#### Command: Criar Emissor

```
POST /api/v1/license-issuers
```

**Request:**

```json
{
  "name": "Câmara Municipal da Praia",
  "code": "CMP",
  "description": "Emissão de licenças municipais",
  "contact_info": {
    "email": "licencas@cmpraia.cv",
    "phone": "+238 260 1000",
    "address": "Câmara Municipal, Praia"
  },
  "is_active": true
}
```

### 4.2 LicenseHolder Aggregate APIs

#### Query: Consulta de Titulares

```
GET /api/v1/license-holders
```

**Query Parameters:**

| Parâmetro       | Tipo   | Obrigatório | Descrição                      | Validação Domain |
| --------------- | ------ | ----------- | ------------------------------ | ---------------- |
| page            | number | false       | Página atual (padrão: 1)       | PaginationVO     |
| limit           | number | false       | Itens por página (padrão: 25)  | PaginationVO     |
| holder_type     | string | false       | 'individual' ou 'corporate'    | HolderTypeVO     |
| search          | string | false       | Busca por nome ou documento    | SearchVO         |
| document_number | string | false       | Filtro por número de documento | DocumentVO       |

**Response:**

```json
{
  "data": [
    {
      "id": "uuid",
      "holder_type": "individual",
      "full_name": "Maria Santos Silva",
      "document_number": "123456789",
      "document_type": "citizen_card",
      "birth_date": "1985-03-15",
      "nationality": "cabo-verdiana",
      "gender": "female",
      "profession": "Comerciante",
      "contacts": [
        {
          "type": "email",
          "value": "maria.santos@email.cv",
          "is_primary": true
        },
        {
          "type": "phone",
          "value": "+238 991 2345",
          "is_primary": true
        }
      ],
      "created_at": "2024-01-10T00:00:00Z"
    },
    {
      "id": "uuid",
      "holder_type": "corporate",
      "company_name": "Empresa ABC Ltda",
      "trade_name": "ABC Comércio",
      "tax_id": "987654321",
      "registration_number": "REG-2024-001",
      "incorporation_date": "2020-05-10",
      "legal_form": "sociedade_limitada",
      "share_capital": 1000000.0,
      "business_activity": "Comércio geral",
      "legal_representatives": [
        {
          "id": "uuid",
          "full_name": "João Silva",
          "document_number": "987654321",
          "role": "gerente",
          "appointment_date": "2020-05-10"
        }
      ],
      "contacts": [
        {
          "type": "email",
          "value": "geral@abc.cv",
          "is_primary": true
        }
      ],
      "created_at": "2024-01-05T00:00:00Z"
    }
  ],
  "pagination": {
    "total": 2500,
    "page": 1,
    "limit": 25,
    "total_pages": 100
  }
}
```

#### Command: Criar Titular Pessoa Física

```
POST /api/v1/license-holders/individual
```

**Request:**

```json
{
  "full_name": "Ana Costa Pereira",
  "document_number": "456789123",
  "document_type": "citizen_card",
  "birth_date": "1990-07-20",
  "nationality": "cabo-verdiana",
  "gender": "female",
  "marital_status": "single",
  "profession": "Arquiteta",
  "contacts": [
    {
      "type": "email",
      "value": "ana.costa@email.cv",
      "is_primary": true
    },
    {
      "type": "phone",
      "value": "+238 995 6789",
      "is_primary": true
    },
    {
      "type": "address",
      "value": "Rua da Liberdade, 45, Praia",
      "is_primary": true
    }
  ]
}
```

#### Command: Criar Titular Pessoa Jurídica

```
POST /api/v1/license-holders/corporate
```

**Request:**

```json
{
  "company_name": "Construtora XYZ S.A.",
  "trade_name": "XYZ Construções",
  "tax_id": "123987456",
  "registration_number": "REG-2024-002",
  "incorporation_date": "2019-03-15",
  "legal_form": "sociedade_anonima",
  "share_capital": 5000000.0,
  "business_activity": "Construção civil",
  "legal_representatives": [
    {
      "full_name": "Pedro Mendes",
      "document_number": "321654987",
      "document_type": "citizen_card",
      "birth_date": "1975-11-30",
      "nationality": "cabo-verdiana",
      "role": "administrador",
      "appointment_date": "2019-03-15",
      "powers": "Representação legal e assinatura de contratos"
    }
  ],
  "contacts": [
    {
      "type": "email",
      "value": "geral@xyz.cv",
      "is_primary": true
    },
    {
      "type": "phone",
      "value": "+238 260 5000",
      "is_primary": true
    },
    {
      "type": "address",
      "value": "Zona Industrial, Lote 15, Praia",
      "is_primary": true
    }
  ]
}
```

### 4.3 IssuedLicense Aggregate APIs

#### Query: Consulta de Licenças com RLS

```
GET /api/v1/issued-licenses
```

**Query Parameters (com validação via Domain Services):**

| Parâmetro        | Tipo   | Obrigatório | Descrição                                    | Validação Domain |
| ---------------- | ------ | ----------- | -------------------------------------------- | ---------------- |
| page             | number | false       | Página atual (padrão: 1)                     | PaginationVO     |
| limit            | number | false       | Itens por página (padrão: 25, max: 100)      | PaginationVO     |
| search           | string | false       | Busca por número, titular (T_LICENSE_HOLDER) | SearchVO         |
| status           | string | false       | Filtro por status via T_OPTIONS              | StatusVO         |
| license_type_id  | uuid   | false       | Filtro por T_LICENSE_TYPE                    | LicenseTypeVO    |
| issuer_id        | uuid   | false       | Filtro por T_LICENSE_ISSUER                  | IssuerVO         |
| holder_id        | uuid   | false       | Filtro por T_LICENSE_HOLDER                  | HolderVO         |
| holder_type      | string | false       | Filtro por tipo: 'individual' ou 'corporate' | HolderTypeVO     |
| sector_id        | uuid   | false       | Filtro por T_SECTOR                          | SectorVO         |
| issue_date_from  | date   | false       | Data inicial de emissão                      | DateRangeVO      |
| issue_date_to    | date   | false       | Data final de emissão                        | DateRangeVO      |
| expiry_date_from | date   | false       | Data inicial de vencimento                   | DateRangeVO      |
| expiry_date_to   | date   | false       | Data final de vencimento                     | DateRangeVO      |

**Response (DTO via Application Service):**

```json
{
  "data": [
    {
      "id": "uuid",
      "license_number": "LIC-2024-001234",
      "issuer": {
        "id": "uuid",
        "name": "Ministério da Economia Marítima",
        "code": "MEM"
      },
      "holder": {
        "id": "uuid",
        "holder_type": "corporate",
        "name": "Empresa XYZ Ltda",
        "document_number": "123456789",
        "primary_contact": {
          "email": "contato@empresa.cv",
          "phone": "+238 260 1234"
        }
      },
      "license_type": {
        "id": "uuid",
        "name": "Licença Comercial",
        "code": "LC001",
        "sector_id": "uuid",
        "category_id": "uuid"
      },
      "sector": {
        "id": "uuid",
        "name": "Comércio",
        "code": "COM"
      },
      "category": {
        "id": "uuid",
        "name": "Varejo",
        "code": "VAR"
      },
      "status": "active",
      "issue_date": "2024-01-15T00:00:00Z",
      "expiry_date": "2025-01-15T00:00:00Z",
      "days_to_expiry": 45,
      "created_at": "2024-01-15T10:30:00Z",
      "updated_at": "2024-01-15T10:30:00Z"
    }
  ],
  "pagination": {
    "total": 1250,
    "page": 1,
    "limit": 25,
    "total_pages": 50,
    "has_next": true,
    "has_previous": false
  }
}
```

#### Query: Detalhes da Licença (Aggregate Root)

```

GET /api/v1/issued-licenses/{id}

```

**Headers:**

- `Authorization: Bearer {jwt_token}`

- `Content-Type: application/json`

**RLS Validation:** Acesso filtrado por perfil de usuário via Supabase RLS

**Response (Aggregate Root completo via Domain Services):**

```json
{
  "id": "uuid",
  "license_number": "LIC-2024-001234",
  "issuer": {
    "id": "uuid",
    "name": "Ministério da Economia Marítima",
    "code": "MEM",
    "description": "Responsável por licenças marítimas e portuárias",
    "contact_info": {
      "email": "licencas@mem.gov.cv",
      "phone": "+238 260 3000",
      "address": "Palácio do Governo, Praia"
    }
  },
  "holder": {
    "id": "uuid",
    "holder_type": "corporate",
    "company_name": "Empresa XYZ Ltda",
    "trade_name": "XYZ Comércio",
    "tax_id": "123456789",
    "registration_number": "REG-2024-001",
    "incorporation_date": "2020-05-10",
    "legal_form": "sociedade_limitada",
    "share_capital": 1000000.0,
    "business_activity": "Comércio geral",
    "contacts": [
      {
        "id": "uuid",
        "type": "email",
        "value": "contato@empresa.cv",
        "is_primary": true
      },
      {
        "id": "uuid",
        "type": "phone",
        "value": "+238 123 4567",
        "is_primary": true
      },
      {
        "id": "uuid",
        "type": "address",
        "value": "Rua Principal, 123, Praia, Santiago",
        "is_primary": true
      }
    ],
    "created_at": "2024-01-10T00:00:00Z"
  },
  "legal_representatives": [
    {
      "id": "uuid",
      "full_name": "João Silva",
      "document_number": "987654321",
      "document_type": "citizen_card",
      "birth_date": "1980-05-15",
      "nationality": "cabo-verdiana",
      "role": "gerente",
      "appointment_date": "2020-05-10",
      "powers": "Representação legal e assinatura de contratos",
      "contacts": [
        {
          "type": "email",
          "value": "joao@empresa.cv",
          "is_primary": true
        },
        {
          "type": "phone",
          "value": "+238 987 6543",
          "is_primary": true
        }
      ]
    }
  ],
  "license_type": {
    "id": "uuid",
    "name": "Licença Comercial",
    "code": "LC001",
    "validity_months": 12,
    "sector_id": "uuid",
    "category_id": "uuid",
    "is_active": true
  },
  "sector": {
    "id": "uuid",
    "name": "Comércio",
    "code": "COM"
  },
  "category": {
    "id": "uuid",
    "name": "Varejo",
    "code": "VAR"
  },
  "status": "active",
  "issue_date": "2024-01-15T00:00:00Z",
  "expiry_date": "2025-01-15T00:00:00Z",
  "provisional_validity": null,
  "observations": "Licença emitida conforme processo 2024/001234",
  "process_number": "PROC-2024-001234",
  "documents": [
    {
      "id": "uuid",
      "name": "Licença Original",
      "document_type": "license_certificate",
      "file_type": "PDF",
      "download_url": "/api/v1/documents/uuid/download",
      "uploaded_at": "2024-01-15T10:30:00Z",
      "uploaded_by": "uuid"
    }
  ],
  "audit_trail": [
    {
      "id": "uuid",
      "action": "license_issued",
      "description": "Licença emitida após aprovação",
      "user_id": "uuid",
      "user_name": "Ana Santos",
      "timestamp": "2024-01-15T10:30:00Z",
      "metadata": {
        "process_id": "uuid",
        "approval_level": "supervisor"
      }
    }
  ],
  "fees": {
    "issuance": {
      "amount": 5000.0,
      "currency": "CVE",
      "status": "paid",
      "payment_date": "2024-01-10T00:00:00Z",
      "payment_reference": "PAY-2024-001234"
    },
    "renewal": {
      "amount": 3000.0,
      "currency": "CVE",
      "status": "pending",
      "due_date": "2024-12-15T00:00:00Z"
    }
  },
  "amendments": [
    {
      "id": "uuid",
      "amendment_type": "status_change",
      "description": "Alteração de status para ativo",
      "amendment_date": "2024-01-20T00:00:00Z",
      "requested_by": "uuid",
      "approved_by": "uuid",
      "status": "approved",
      "justification": "Documentação complementar apresentada"
    }
  ],
  "transfers": [
    {
      "id": "uuid",
      "previous_holder_id": "uuid",
      "new_holder_id": "uuid",
      "transfer_date": "2024-02-01T00:00:00Z",
      "transfer_reason": "Venda da empresa",
      "status": "completed",
      "conditions": "Transferência com todas as obrigações"
    }
  ],
  "documents": [
    {
      "id": "uuid",
      "name": "Licença Original",
      "document_type": "license_certificate",
      "file_type": "PDF",
      "file_size": 2048576,
      "upload_date": "2024-01-15T10:30:00Z",
      "uploaded_by": "uuid",
      "download_url": "/api/v1/documents/uuid/download"
    }
  ],
  "fees": [
    {
      "id": "uuid",
      "fee_type": "issuance",
      "amount": 5000.0,
      "currency": "CVE",
      "status": "paid",
      "due_date": "2024-01-10T00:00:00Z",
      "payment_date": "2024-01-10T00:00:00Z",
      "payment_reference": "PAY-2024-001234"
    },
    {
      "id": "uuid",
      "fee_type": "renewal",
      "amount": 3000.0,
      "currency": "CVE",
      "status": "pending",
      "due_date": "2024-12-15T00:00:00Z"
    }
  ],
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-15T10:30:00Z"
}
```

### 4.4 Establishment Management APIs (NOVO)

#### Registro de Estabelecimentos

```
POST /api/v1/establishments/register
```

**Request Body:**

```json
{
  "name": "Restaurante Cabo Verde",
  "holder_id": "uuid",
  "classification_code": "TURISMO_RESTAURACAO",
  "segment": "TURISTICO",
  "category": "RESTAURANTE",
  "size_classification": "MEDIO",
  "location": {
    "latitude": 14.9177,
    "longitude": -23.5092,
    "address_line1": "Rua da Praia, 123",
    "address_line2": "Plateau",
    "city": "Praia",
    "parish": "Nossa Senhora da Graça",
    "municipality": "Praia",
    "postal_code": "7600",
    "zone_classification": "COMERCIAL",
    "land_use_type": "URBANO"
  },
  "documents": [
    {
      "document_type": "LICENCA_TURISTICA",
      "document_name": "Licença de Estabelecimento Turístico",
      "file_base64": "base64_encoded_file",
      "mime_type": "application/pdf",
      "issue_date": "2024-01-15",
      "expiry_date": "2025-01-15",
      "issuing_authority": "Ministério do Turismo"
    }
  ]
}
```

**Response:**

```json
{
  "id": "uuid",
  "matricial_number": "EST-2024-001234",
  "name": "Restaurante Cabo Verde",
  "registration_status": "PENDING_VALIDATION",
  "location": {
    "latitude": 14.9177,
    "longitude": -23.5092,
    "cartographic_validated": false,
    "validation_required": true
  },
  "compliance_status": false,
  "required_documents": ["LICENCA_TURISTICA", "CERTIFICADO_SEGURANCA", "ALVARA_SANITARIO"],
  "submitted_documents": ["LICENCA_TURISTICA"],
  "pending_documents": ["CERTIFICADO_SEGURANCA", "ALVARA_SANITARIO"],
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### Consulta de Estabelecimentos

```
GET /api/v1/establishments
```

**Query Parameters:**

| Parâmetro          | Tipo    | Obrigatório | Descrição                                              |
| ------------------ | ------- | ----------- | ------------------------------------------------------ |
| holder_id          | uuid    | false       | Filtro por titular                                     |
| segment            | string  | false       | Filtro por segmento (COMERCIAL, TURISTICO, INDUSTRIAL) |
| category           | string  | false       | Filtro por categoria específica                        |
| operational_status | string  | false       | Status operacional (ACTIVE, INACTIVE, SUSPENDED)       |
| compliance_status  | boolean | false       | Status de conformidade                                 |
| municipality       | string  | false       | Filtro por município                                   |
| parish             | string  | false       | Filtro por freguesia                                   |
| latitude_min       | decimal | false       | Coordenada mínima de latitude                          |
| latitude_max       | decimal | false       | Coordenada máxima de latitude                          |
| longitude_min      | decimal | false       | Coordenada mínima de longitude                         |
| longitude_max      | decimal | false       | Coordenada máxima de longitude                         |
| page               | number  | false       | Página (padrão: 0)                                     |
| size               | number  | false       | Tamanho da página (padrão: 20, max: 100)               |

#### Validação de Georreferenciamento

```
POST /api/v1/establishments/{id}/validate-location
```

**Request Body:**

```json
{
  "latitude": 14.9177,
  "longitude": -23.5092,
  "validation_method": "GPS_SURVEY",
  "accuracy_meters": 2.5,
  "validator_notes": "Coordenadas validadas por levantamento GPS"
}
```

#### Upload de Documentos

```
POST /api/v1/establishments/{id}/documents
```

**Request (Multipart Form):**

```
Content-Type: multipart/form-data

document_type: CERTIFICADO_SEGURANCA
document_name: Certificado de Segurança Contra Incêndios
file: [binary_file_data]
issue_date: 2024-01-15
expiry_date: 2025-01-15
issuing_authority: Bombeiros de Cabo Verde
```

#### Verificação de Conformidade

```
POST /api/v1/establishments/{id}/compliance-check
```

**Response:**

```json
{
  "establishment_id": "uuid",
  "compliance_status": true,
  "compliance_score": 95.5,
  "checks_performed": [
    {
      "check_type": "DOCUMENT_VALIDATION",
      "status": "PASSED",
      "score": 100,
      "details": "Todos os documentos obrigatórios válidos"
    },
    {
      "check_type": "LOCATION_VALIDATION",
      "status": "PASSED",
      "score": 100,
      "details": "Coordenadas GPS validadas cartograficamente"
    },
    {
      "check_type": "ZONING_COMPLIANCE",
      "status": "WARNING",
      "score": 85,
      "details": "Estabelecimento em zona comercial adequada, mas próximo a área residencial"
    }
  ],
  "recommendations": ["Considerar medidas de redução de ruído para conformidade com zoneamento"],
  "next_inspection_date": "2024-07-15",
  "checked_at": "2024-01-15T14:30:00Z",
  "checked_by": "uuid"
}
```

### 4.5 License Process APIs

#### Command: Criar Alteração de Licença

```
POST /api/v1/issued-licenses/{id}/amendments
```

**Request:**

```json
{
  "amendment_type": "status_change",
  "description": "Solicitação de reativação da licença",
  "justification": "Regularização da situação fiscal",
  "requested_by": "uuid",
  "new_values": {
    "status": "active",
    "conditions": "Licença reativada mediante regularização"
  },
  "supporting_documents": [
    {
      "document_type": "tax_clearance",
      "file_id": "uuid"
    }
  ]
}
```

**Response:**

```json
{
  "success": true,
  "amendment_id": "uuid",
  "process_number": "AMD-2024-001234",
  "status": "pending_approval",
  "estimated_completion_days": 5,
  "created_at": "2024-01-20T10:00:00Z"
}
```

#### Command: Criar Transferência de Licença

```
POST /api/v1/issued-licenses/{id}/transfers
```

**Request:**

```json
{
  "new_holder_id": "uuid",
  "transfer_reason": "Venda da empresa",
  "transfer_date": "2024-02-01T00:00:00Z",
  "conditions": "Transferência com todas as obrigações e direitos",
  "supporting_documents": [
    {
      "document_type": "sale_contract",
      "file_id": "uuid"
    },
    {
      "document_type": "new_holder_documents",
      "file_id": "uuid"
    }
  ],
  "requested_by": "uuid"
}
```

**Response:**

```json
{
  "success": true,
  "transfer_id": "uuid",
  "process_number": "TRF-2024-001234",
  "status": "pending_approval",
  "estimated_completion_days": 10,
  "transfer_fee": {
    "amount": 2500.0,
    "currency": "CVE",
    "due_date": "2024-01-25T00:00:00Z"
  },
  "created_at": "2024-01-20T10:00:00Z"
}
```

#### Command: Renovar Licença

```
POST /api/v1/issued-licenses/{id}/renewals
```

**Request:**

```json
{
  "renewal_type": "standard",
  "new_expiry_date": "2026-01-15T00:00:00Z",
  "observations": "Renovação padrão - documentação em dia",
  "supporting_documents": [
    {
      "document_type": "updated_business_license",
      "file_id": "uuid"
    }
  ],
  "requested_by": "uuid"
}
```

**Response:**

```json
{
  "success": true,
  "renewal_id": "uuid",
  "process_number": "REN-2024-001234",
  "status": "approved",
  "new_expiry_date": "2026-01-15T00:00:00Z",
  "renewal_fee": {
    "amount": 3000.0,
    "currency": "CVE",
    "status": "pending",
    "due_date": "2024-12-15T00:00:00Z",
    "payment_reference": "REN-PAY-2024-001234"
  },
  "created_at": "2024-12-01T10:00:00Z"
}
```

#### Command: Renovação de Licença (Domain Service)

**Endpoint:** `POST /api/v1/issued-licenses/{id}/renew`

**Headers:**

- Authorization: Bearer {supabase_jwt_token}

- Content-Type: application/json

**Domain Service:** `LicenseRenewalService`\
**Aggregate:** `IssuedLicense` + `LicenseRenewal`\
**Tables:** `T_ISSUED_LICENSE`, `T_LICENSE_RENEWAL`, `T_PROCESS_TYPE_FEE`, `T_LICENSE_AUDIT`

**Request:**

```json
{
  "renewal_type": "automatic",
  "observations": "Renovação automática - documentação em dia",
  "provisional_validity": "2024-03-15T00:00:00Z",
  "requested_by": "uuid",
  "supporting_documents": [
    {
      "document_type": "financial_statement",
      "file_id": "uuid"
    }
  ]
}
```

**Response (Command Result via CQRS):**

```json
{
  "success": true,
  "message": "Licença renovada com sucesso",
  "command_id": "uuid",
  "data": {
    "license_id": "uuid",
    "license_number": "LIC-2024-001234",
    "new_expiry_date": "2025-01-15T00:00:00Z",
    "status": "active",
    "renewal_process": {
      "id": "uuid",
      "process_number": "REN-2024-001234",
      "start_date": "2024-12-01T00:00:00Z",
      "completion_date": "2024-12-05T00:00:00Z",
      "status": "completed",
      "renewal_type": "automatic",
      "processed_by": "uuid"
    },
    "fees": {
      "renewal_fee": {
        "amount": 3000.0,
        "currency": "CVE",
        "status": "pending",
        "due_date": "2024-12-31T00:00:00Z",
        "fee_category_id": "uuid",
        "payment_reference": "REN-PAY-2024-001234"
      }
    },
    "audit_entry": {
      "action": "license_renewed",
      "timestamp": "2024-12-05T10:30:00Z",
      "user_id": "uuid"
    }
  }
}
```

### 4.2 API de Monitoramento

#### Query: Dashboard de Alertas (Read Model)

**Endpoint:** `GET /api/v1/license-alerts`

**Headers:**

- Authorization: Bearer {supabase_jwt_token}

**Domain Service:** `AlertMonitoringService`\
**Read Model:** `LicenseAlertView`\
**Tables:** `T_LICENSE_ALERT`, `T_ISSUED_LICENSE`, `T_LICENSE_HOLDER`, `T_OPTIONS`

**Query Parameters:**

- `alert_type`: expiry, renewal, suspension (opcional)

- `priority`: high, medium, low (opcional)

- `days_ahead`: número de dias para alertas de vencimento (padrão: 30)

- `status`: pending, acknowledged, resolved (opcional)

- `page`: número da página (padrão: 1)

- `limit`: itens por página (padrão: 20)

**Response (via RLS + Cache Redis):**

```json
{
  "alerts": [
    {
      "id": "uuid",
      "license_id": "uuid",
      "license_number": "LIC-2024-001234",
      "holder": {
        "id": "uuid",
        "name": "Empresa XYZ Ltda",
        "organization_id": "uuid"
      },
      "alert_type": "expiry",
      "priority": "high",
      "expiry_date": "2024-02-15T00:00:00Z",
      "days_remaining": 15,
      "status": "pending",
      "created_at": "2024-01-31T00:00:00Z",
      "acknowledged_at": null,
      "acknowledged_by": null,
      "metadata": {
        "license_type": "Licença Comercial",
        "sector": "Comércio",
        "auto_renewal_eligible": true
      }
    }
  ],
  "summary": {
    "total_alerts": 1,
    "by_priority": {
      "high": 1,
      "medium": 0,
      "low": 0
    },
    "by_type": {
      "expiry": 1,
      "renewal": 0,
      "suspension": 0
    },
    "by_status": {
      "pending": 1,
      "acknowledged": 0,
      "resolved": 0
    }
  },
  "pagination": {
    "current_page": 1,
    "total_pages": 1,
    "total_items": 1,
    "items_per_page": 20
  }
}
```

### 4.3 API de Relatórios (CQRS + Event Sourcing)

#### Command: Geração de Relatório

**Endpoint:** `POST /api/v1/reports/generate`

**Headers:**

- Authorization: Bearer {supabase_jwt_token}

- Content-Type: application/json

**Domain Service:** `ReportGenerationService`\
**Command Handler:** `GenerateReportCommandHandler`\
**Tables:** `T_ISSUED_LICENSE`, `T_LICENSE_HOLDER`, `T_LICENSE_TYPE`, `T_SECTOR`, `T_CATEGORY`, `T_LICENSE_AUDIT`

**Request:**

```json
{
  "report_type": "licenses_by_sector",
  "period": {
    "start_date": "2024-01-01T00:00:00Z",
    "end_date": "2024-12-31T23:59:59Z"
  },
  "filters": {
    "sector_ids": ["uuid1", "uuid2"],
    "category_ids": ["uuid3", "uuid4"],
    "license_status": ["active", "expired"],
    "holder_type": "organization"
  },
  "output_format": "PDF",
  "delivery_method": "download",
  "requested_by": "uuid"
}
```

**Response (Command Accepted):**

```json
{
  "success": true,
  "command_id": "uuid",
  "report_id": "uuid",
  "status": "processing",
  "estimated_completion_minutes": 5,
  "status_url": "/api/v1/reports/uuid/status",
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### Query: Status do Relatório

**Endpoint:** `GET /api/v1/reports/{id}/status`

**Headers:**

- Authorization: Bearer {supabase_jwt_token}

**Read Model:** `ReportStatusView`\
**Tables:** `T_REPORT_GENERATION`, `T_REPORT_AUDIT`

**Response (Event Sourcing State):**

```json
{
  "id": "uuid",
  "report_type": "licenses_by_sector",
  "status": "completed",
  "progress_percentage": 100,
  "download_url": "/api/v1/reports/uuid/download",
  "generated_at": "2024-01-15T10:35:00Z",
  "file_size_mb": 2.5,
  "download_expires_at": "2024-01-22T10:35:00Z",
  "requested_by": "uuid",
  "generation_metadata": {
    "total_records": 1250,
    "processing_time_seconds": 45,
    "filters_applied": {
      "sectors": 2,
      "categories": 2,
      "date_range_days": 365
    }
  },
  "audit_trail": [
    {
      "event": "report_requested",
      "timestamp": "2024-01-15T10:30:00Z"
    },
    {
      "event": "processing_started",
      "timestamp": "2024-01-15T10:30:15Z"
    },
    {
      "event": "report_completed",
      "timestamp": "2024-01-15T10:35:00Z"
    }
  ]
}
```

## 5. Arquitetura DDD do Servidor

### 5.1 Camadas DDD e Bounded Context

```mermaid
graph TD
  A[React Frontend] --> B[Presentation Layer]
  B --> C[Application Layer]
  C --> D[Domain Layer]
  D --> E[Infrastructure Layer]
  E --> F[(Supabase PostgreSQL)]
  E --> G[Redis Cache]
  E --> H[RabbitMQ]

  subgraph "Bounded Context: Gestão de Licenças Emitidas"
    subgraph "Presentation Layer"
      B1[Controllers]
      B2[DTOs]
      B3[Validators]
    end

    subgraph "Application Layer"
      C1[Command Handlers]
      C2[Query Handlers]
      C3[Application Services]
      C4[Event Handlers]
    end

    subgraph "Domain Layer"
      D1[Aggregates]
      D2[Domain Services]
      D3[Domain Events]
      D4[Value Objects]
      D5[Repositories Interfaces]
    end

    subgraph "Infrastructure Layer"
      E1[Repository Implementations]
      E2[External Service Adapters]
      E3[Event Publishers]
      E4[Cache Providers]
    end
  end

  subgraph "External Bounded Contexts"
    I[Activity Server]
    J[Sistema Financeiro]
    K[Notificações]
    L[Parametrização]
  end

  E2 --> I
  E2 --> J
  E2 --> K
  E2 --> L
```

## 6. Modelo de Dados Normalizado (Padrão T\_)

### 6.1 Diagrama Entidade-Relacionamento DDD

```mermaid
erDiagram
    %% Entidades de Emissão
    T_LICENSE_ISSUER ||--o{ T_ISSUED_LICENSE : "issues"
    T_ISSUED_LICENSE ||--|| T_LICENSE_TYPE : "is of type"

    %% Entidades de Titularidade
    T_LICENSE_HOLDER ||--o{ T_ISSUED_LICENSE : "owns"
    T_LICENSE_HOLDER ||--o| T_INDIVIDUAL_HOLDER : "extends"
    T_LICENSE_HOLDER ||--o| T_CORPORATE_HOLDER : "extends"
    T_CORPORATE_HOLDER ||--o{ T_LEGAL_REPRESENTATIVE : "has representatives"
    T_LICENSE_HOLDER ||--o{ T_HOLDER_CONTACT : "has contacts"

    %% Entidades de Processo
    T_ISSUED_LICENSE ||--o{ T_LICENSE_RENEWAL : "has renewals"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_AMENDMENT : "has amendments"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_TRANSFER : "has transfers"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_AUDIT : "has audit trail"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_DOCUMENT : "has documents"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_FEE : "has fees"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_ALERT : "generates alerts"

    %% Parametrização
    T_LICENSE_TYPE ||--|| T_SECTOR : "belongs to"
    T_LICENSE_TYPE ||--|| T_CATEGORY : "belongs to"
    T_CATEGORY ||--|| T_SECTOR : "belongs to"

    %% Definições das Entidades
    T_LICENSE_ISSUER {
        uuid id PK
        varchar issuer_code UK
        varchar name
        varchar jurisdiction
        varchar competence_area
        varchar contact_email
        varchar contact_phone
        varchar status
        timestamp created_at
        timestamp updated_at
        uuid created_by FK
        uuid updated_by FK
    }

    T_LICENSE_HOLDER {
        uuid id PK
        varchar holder_type
        varchar status
        varchar classification
        timestamp created_at
        timestamp updated_at
        uuid created_by FK
        uuid updated_by FK
    }

    T_INDIVIDUAL_HOLDER {
        uuid id PK
        uuid holder_id FK
        varchar full_name
        varchar father_name
        varchar mother_name
        varchar marital_status
        varchar nationality
        varchar birthplace
        date birth_date
        varchar document_type
        varchar document_number UK
        varchar gender
        timestamp created_at
        timestamp updated_at
    }

    T_CORPORATE_HOLDER {
        uuid id PK
        uuid holder_id FK
        varchar corporate_name
        varchar trade_name
        varchar tax_id UK
        varchar registration_number
        varchar economic_activity
        varchar corporate_type
        date incorporation_date
        varchar share_capital
        timestamp created_at
        timestamp updated_at
    }

    T_LEGAL_REPRESENTATIVE {
        uuid id PK
        uuid corporate_holder_id FK
        varchar full_name
        varchar document_type
        varchar document_number UK
        varchar representation_type
        varchar powers_description
        date valid_from
        date valid_until
        varchar status
        timestamp created_at
        timestamp updated_at
    }

    T_HOLDER_CONTACT {
        uuid id PK
        uuid holder_id FK
        varchar contact_type
        varchar contact_value
        varchar classification
        boolean is_primary
        boolean is_verified
        varchar verification_method
        timestamp verified_at
        varchar communication_preference
        timestamp created_at
        timestamp updated_at
    }

    T_ISSUED_LICENSE {
        uuid id PK
        varchar license_number UK
        uuid issuer_id FK
        uuid license_type_id FK
        uuid holder_id FK
        varchar status
        date issue_date
        date expiry_date
        date provisional_expiry_date
        varchar licensing_model
        boolean renewable
        integer renewal_count
        text observations
        jsonb metadata
        timestamp created_at
        timestamp updated_at
        uuid created_by FK
        uuid updated_by FK
    }

    T_LICENSE_TYPE {
        uuid id PK
        varchar name
        varchar code UK
        text description
        integer validity_months
        uuid sector_id FK
        uuid category_id FK
        boolean is_active
        uuid created_by FK
        uuid updated_by FK
        timestamp created_at
        timestamp updated_at
    }

    T_SECTOR {
        uuid id PK
        varchar name
        varchar code UK
        text description
        boolean is_active
        integer sort_order
        uuid created_by FK
        uuid updated_by FK
        timestamp created_at
        timestamp updated_at
    }

    T_CATEGORY {
        uuid id PK
        varchar name
        varchar code UK
        text description
        uuid sector_id FK
        boolean is_active
        integer sort_order
        uuid created_by FK
        uuid updated_by FK
        timestamp created_at
        timestamp updated_at
    }

    T_LICENSE_RENEWAL {
        uuid id PK
        uuid license_id FK
        varchar process_number UK
        varchar renewal_type
        date start_date
        date completion_date
        varchar status
        text observations
        uuid requested_by FK
        uuid processed_by FK
        timestamp created_at
        timestamp updated_at
    }

    T_LICENSE_AMENDMENT {
        uuid id PK
        uuid license_id FK
        varchar amendment_type
        varchar process_number UK
        text description
        jsonb old_values
        jsonb new_values
        varchar status
        date effective_date
        uuid requested_by FK
        uuid approved_by FK
        timestamp created_at
        timestamp updated_at
    }

    T_LICENSE_TRANSFER {
        uuid id PK
        uuid license_id FK
        uuid old_holder_id FK
        uuid new_holder_id FK
        varchar process_number UK
        varchar transfer_type
        text reason
        varchar status
        date effective_date
        uuid requested_by FK
        uuid approved_by FK
        timestamp created_at
        timestamp updated_at
    }

    T_LICENSE_AUDIT {
        uuid id PK
        uuid license_id FK
        varchar action
        text description
        uuid user_id FK
        varchar user_name
        jsonb metadata
        varchar ip_address
        varchar user_agent
        timestamp timestamp
    }

    T_LICENSE_DOCUMENT {
        uuid id PK
        uuid license_id FK
        varchar document_name
        varchar document_type
        varchar file_type
        varchar file_path
        integer file_size
        varchar status
        uuid uploaded_by FK
        timestamp uploaded_at
        timestamp created_at
        timestamp updated_at
    }

    T_LICENSE_FEE {
        uuid id PK
        uuid license_id FK
        varchar fee_type
        decimal amount
        varchar currency
        varchar status
        date due_date
        date payment_date
        varchar payment_reference
        uuid fee_category_id FK
        timestamp created_at
        timestamp updated_at
    }

    T_LICENSE_ALERT {
        uuid id PK
        uuid license_id FK
        varchar alert_type
        varchar priority
        varchar status
        date trigger_date
        integer days_ahead
        text message
        uuid acknowledged_by FK
        timestamp acknowledged_at
        timestamp created_at
        timestamp updated_at
    }

    T_OPTIONS {
        uuid id PK
        varchar option_group
        varchar option_key UK
        varchar option_value
        text description
        boolean is_active
        integer sort_order
        uuid created_by FK
        uuid updated_by FK
        timestamp created_at
        timestamp updated_at
    }
```

### 6.2 Data Definition Language (DDL)

#### Tabela de Emissores: T_LICENSE_ISSUER

```sql
-- Tabela de Órgãos Emissores de Licenças
CREATE TABLE T_LICENSE_ISSUER (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    issuer_code VARCHAR(20) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    jurisdiction VARCHAR(100),
    competence_area TEXT,
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20),
    status VARCHAR(20) NOT NULL DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'suspended')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by UUID NOT NULL,
    updated_by UUID
);

-- Índices
CREATE INDEX idx_license_issuer_code ON T_LICENSE_ISSUER(issuer_code);
CREATE INDEX idx_license_issuer_status ON T_LICENSE_ISSUER(status);
```

#### Tabela Base de Titulares: T_LICENSE_HOLDER

```sql
-- Tabela Base de Titulares (Herança)
CREATE TABLE T_LICENSE_HOLDER (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    holder_type VARCHAR(20) NOT NULL CHECK (holder_type IN ('individual', 'corporate')),
    status VARCHAR(20) NOT NULL DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'suspended', 'blocked')),
    classification VARCHAR(50),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by UUID NOT NULL,
    updated_by UUID
);

-- Índices
CREATE INDEX idx_license_holder_type ON T_LICENSE_HOLDER(holder_type);
CREATE INDEX idx_license_holder_status ON T_LICENSE_HOLDER(status);
```

#### Tabela de Pessoas Físicas: T_INDIVIDUAL_HOLDER

```sql
-- Tabela de Titulares Pessoas Físicas
CREATE TABLE T_INDIVIDUAL_HOLDER (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    holder_id UUID NOT NULL UNIQUE,
    full_name VARCHAR(255) NOT NULL,
    father_name VARCHAR(255),
    mother_name VARCHAR(255),
    marital_status VARCHAR(20),
    nationality VARCHAR(50),
    birthplace VARCHAR(100),
    birth_date DATE,
    document_type VARCHAR(20) NOT NULL,
    document_number VARCHAR(50) UNIQUE NOT NULL,
    gender VARCHAR(10),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_individual_holder FOREIGN KEY (holder_id) REFERENCES T_LICENSE_HOLDER(id) ON DELETE CASCADE
);

-- Índices
CREATE INDEX idx_individual_holder_document ON T_INDIVIDUAL_HOLDER(document_number);
CREATE INDEX idx_individual_holder_name ON T_INDIVIDUAL_HOLDER(full_name);
CREATE INDEX idx_individual_holder_birth_date ON T_INDIVIDUAL_HOLDER(birth_date);
```

#### Tabela de Pessoas Jurídicas: T_CORPORATE_HOLDER

```sql
-- Tabela de Titulares Pessoas Jurídicas
CREATE TABLE T_CORPORATE_HOLDER (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    holder_id UUID NOT NULL UNIQUE,
    corporate_name VARCHAR(255) NOT NULL,
    trade_name VARCHAR(255),
    tax_id VARCHAR(50) UNIQUE NOT NULL,
    registration_number VARCHAR(50),
    economic_activity VARCHAR(100),
    corporate_type VARCHAR(50),
    incorporation_date DATE,
    share_capital VARCHAR(50),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_corporate_holder FOREIGN KEY (holder_id) REFERENCES T_LICENSE_HOLDER(id) ON DELETE CASCADE
);

-- Índices
CREATE INDEX idx_corporate_holder_tax_id ON T_CORPORATE_HOLDER(tax_id);
CREATE INDEX idx_corporate_holder_corporate_name ON T_CORPORATE_HOLDER(corporate_name);
CREATE INDEX idx_corporate_holder_registration ON T_CORPORATE_HOLDER(registration_number);
```

#### Tabela de Representantes Legais: T_LEGAL_REPRESENTATIVE

```sql
-- Tabela de Representantes Legais
CREATE TABLE T_LEGAL_REPRESENTATIVE (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    corporate_holder_id UUID NOT NULL,
    full_name VARCHAR(255) NOT NULL,
    document_type VARCHAR(20) NOT NULL,
    document_number VARCHAR(50) UNIQUE NOT NULL,
    representation_type VARCHAR(50) NOT NULL,
    powers_description TEXT,
    valid_from DATE NOT NULL,
    valid_until DATE,
    status VARCHAR(20) NOT NULL DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'expired')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_legal_representative_corporate FOREIGN KEY (corporate_holder_id) REFERENCES T_CORPORATE_HOLDER(id) ON DELETE CASCADE
);

-- Índices
CREATE INDEX idx_legal_representative_corporate ON T_LEGAL_REPRESENTATIVE(corporate_holder_id);
CREATE INDEX idx_legal_representative_document ON T_LEGAL_REPRESENTATIVE(document_number);
CREATE INDEX idx_legal_representative_validity ON T_LEGAL_REPRESENTATIVE(valid_from, valid_until);
```

#### Tabela de Contatos: T_HOLDER_CONTACT

```sql
-- Tabela de Contatos dos Titulares
CREATE TABLE T_HOLDER_CONTACT (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    holder_id UUID NOT NULL,
    contact_type VARCHAR(20) NOT NULL CHECK (contact_type IN ('email', 'phone', 'mobile', 'address', 'fax')),
    contact_value VARCHAR(255) NOT NULL,
    classification VARCHAR(20) DEFAULT 'personal' CHECK (classification IN ('personal', 'business', 'emergency')),
    is_primary BOOLEAN DEFAULT false,
    is_verified BOOLEAN DEFAULT false,
    verification_method VARCHAR(50),
    verified_at TIMESTAMP WITH TIME ZONE,
    communication_preference VARCHAR(20) DEFAULT 'optional' CHECK (communication_preference IN ('required', 'preferred', 'optional', 'blocked')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_holder_contact FOREIGN KEY (holder_id) REFERENCES T_LICENSE_HOLDER(id) ON DELETE CASCADE
);

-- Índices
CREATE INDEX idx_holder_contact_holder_id ON T_HOLDER_CONTACT(holder_id);
CREATE INDEX idx_holder_contact_type ON T_HOLDER_CONTACT(contact_type);
CREATE INDEX idx_holder_contact_primary ON T_HOLDER_CONTACT(is_primary) WHERE is_primary = true;
```

#### Tabela Principal: T_ISSUED_LICENSE

```sql
-- Tabela de Licenças Emitidas (Atualizada)
CREATE TABLE T_ISSUED_LICENSE (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_number VARCHAR(50) UNIQUE NOT NULL,
    issuer_id UUID NOT NULL,
    license_type_id UUID NOT NULL,
    holder_id UUID NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'active' CHECK (status IN ('active', 'expired', 'suspended', 'cancelled', 'provisional')),
    issue_date DATE NOT NULL,
    expiry_date DATE NOT NULL,
    provisional_expiry_date DATE,
    licensing_model VARCHAR(20) DEFAULT 'standard' CHECK (licensing_model IN ('standard', 'simplified', 'provisional', 'emergency')),
    renewable BOOLEAN DEFAULT true,
    renewal_count INTEGER DEFAULT 0,
    observations TEXT,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by UUID NOT NULL,
    updated_by UUID,

    CONSTRAINT fk_issued_license_issuer FOREIGN KEY (issuer_id) REFERENCES T_LICENSE_ISSUER(id),
    CONSTRAINT fk_issued_license_holder FOREIGN KEY (holder_id) REFERENCES T_LICENSE_HOLDER(id),
    CONSTRAINT fk_issued_license_type FOREIGN KEY (license_type_id) REFERENCES T_LICENSE_TYPE(id)
);

-- Índices para performance
CREATE INDEX idx_issued_license_issuer_id ON T_ISSUED_LICENSE(issuer_id);
CREATE INDEX idx_issued_license_holder_id ON T_ISSUED_LICENSE(holder_id);
CREATE INDEX idx_issued_license_type_id ON T_ISSUED_LICENSE(license_type_id);
CREATE INDEX idx_issued_license_status ON T_ISSUED_LICENSE(status);
CREATE INDEX idx_issued_license_expiry_date ON T_ISSUED_LICENSE(expiry_date);
CREATE INDEX idx_issued_license_issue_date ON T_ISSUED_LICENSE(issue_date DESC);
CREATE INDEX idx_issued_license_number ON T_ISSUED_LICENSE(license_number);
```

#### Tabela de Alterações: T_LICENSE_AMENDMENT

```sql
-- Tabela de Alterações de Licenças
CREATE TABLE T_LICENSE_AMENDMENT (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_id UUID NOT NULL,
    amendment_type VARCHAR(50) NOT NULL,
    process_number VARCHAR(50) UNIQUE NOT NULL,
    description TEXT,
    old_values JSONB,
    new_values JSONB,
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'approved', 'rejected', 'cancelled')),
    effective_date DATE,
    requested_by UUID NOT NULL,
    approved_by UUID,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_license_amendment_license FOREIGN KEY (license_id) REFERENCES T_ISSUED_LICENSE(id)
);

-- Índices
CREATE INDEX idx_license_amendment_license_id ON T_LICENSE_AMENDMENT(license_id);
CREATE INDEX idx_license_amendment_status ON T_LICENSE_AMENDMENT(status);
CREATE INDEX idx_license_amendment_type ON T_LICENSE_AMENDMENT(amendment_type);
```

#### Tabela de Transferências: T_LICENSE_TRANSFER

```sql
-- Tabela de Transferências de Licenças
CREATE TABLE T_LICENSE_TRANSFER (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_id UUID NOT NULL,
    old_holder_id UUID NOT NULL,
    new_holder_id UUID NOT NULL,
    process_number VARCHAR(50) UNIQUE NOT NULL,
    transfer_type VARCHAR(20) NOT NULL CHECK (transfer_type IN ('sale', 'inheritance', 'merger', 'other')),
    reason TEXT,
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'approved', 'rejected', 'cancelled')),
    effective_date DATE,
    requested_by UUID NOT NULL,
    approved_by UUID,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_license_transfer_license FOREIGN KEY (license_id) REFERENCES T_ISSUED_LICENSE(id),
    CONSTRAINT fk_license_transfer_old_holder FOREIGN KEY (old_holder_id) REFERENCES T_LICENSE_HOLDER(id),
    CONSTRAINT fk_license_transfer_new_holder FOREIGN KEY (new_holder_id) REFERENCES T_LICENSE_HOLDER(id)
);

-- Índices
CREATE INDEX idx_license_transfer_license_id ON T_LICENSE_TRANSFER(license_id);
CREATE INDEX idx_license_transfer_old_holder ON T_LICENSE_TRANSFER(old_holder_id);
CREATE INDEX idx_license_transfer_new_holder ON T_LICENSE_TRANSFER(new_holder_id);
CREATE INDEX idx_license_transfer_status ON T_LICENSE_TRANSFER(status);
```

#### Tabela de Documentos: T_LICENSE_DOCUMENT

```sql
-- Tabela de Documentos das Licenças
CREATE TABLE T_LICENSE_DOCUMENT (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_id UUID NOT NULL,
    document_name VARCHAR(255) NOT NULL,
    document_type VARCHAR(50) NOT NULL,
    file_type VARCHAR(10),
    file_path VARCHAR(500),
    file_size INTEGER,
    status VARCHAR(20) NOT NULL DEFAULT 'active' CHECK (status IN ('active', 'archived', 'deleted')),
    uploaded_by UUID NOT NULL,
    uploaded_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_license_document_license FOREIGN KEY (license_id) REFERENCES T_ISSUED_LICENSE(id)
);

-- Índices
CREATE INDEX idx_license_document_license_id ON T_LICENSE_DOCUMENT(license_id);
CREATE INDEX idx_license_document_type ON T_LICENSE_DOCUMENT(document_type);
CREATE INDEX idx_license_document_status ON T_LICENSE_DOCUMENT(status);
```

#### Tabela de Taxas: T_LICENSE_FEE

```sql
-- Tabela de Taxas das Licenças
CREATE TABLE T_LICENSE_FEE (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_id UUID NOT NULL,
    fee_type VARCHAR(50) NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'CVE',
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'paid', 'overdue', 'cancelled', 'refunded')),
    due_date DATE,
    payment_date DATE,
    payment_reference VARCHAR(100),
    fee_category_id UUID,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_license_fee_license FOREIGN KEY (license_id) REFERENCES T_ISSUED_LICENSE(id)
);

-- Índices
CREATE INDEX idx_license_fee_license_id ON T_LICENSE_FEE(license_id);
CREATE INDEX idx_license_fee_status ON T_LICENSE_FEE(status);
CREATE INDEX idx_license_fee_due_date ON T_LICENSE_FEE(due_date);
CREATE INDEX idx_license_fee_type ON T_LICENSE_FEE(fee_type);

-- RLS (Row Level Security) para todas as tabelas

-- T_LICENSE_ISSUER
ALTER TABLE T_LICENSE_ISSUER ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view issuers" ON T_LICENSE_ISSUER
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Admins can manage issuers" ON T_LICENSE_ISSUER
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' = 'admin_sistema');

-- T_LICENSE_HOLDER
ALTER TABLE T_LICENSE_HOLDER ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view holders" ON T_LICENSE_HOLDER
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage holders" ON T_LICENSE_HOLDER
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_INDIVIDUAL_HOLDER
ALTER TABLE T_INDIVIDUAL_HOLDER ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view individual holders" ON T_INDIVIDUAL_HOLDER
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage individual holders" ON T_INDIVIDUAL_HOLDER
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_CORPORATE_HOLDER
ALTER TABLE T_CORPORATE_HOLDER ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view corporate holders" ON T_CORPORATE_HOLDER
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage corporate holders" ON T_CORPORATE_HOLDER
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_LEGAL_REPRESENTATIVE
ALTER TABLE T_LEGAL_REPRESENTATIVE ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view legal representatives" ON T_LEGAL_REPRESENTATIVE
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage legal representatives" ON T_LEGAL_REPRESENTATIVE
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_HOLDER_CONTACT
ALTER TABLE T_HOLDER_CONTACT ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view holder contacts" ON T_HOLDER_CONTACT
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage holder contacts" ON T_HOLDER_CONTACT
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_ISSUED_LICENSE
ALTER TABLE T_ISSUED_LICENSE ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view licenses based on role" ON T_ISSUED_LICENSE
    FOR SELECT USING (
        auth.role() = 'authenticated' AND (
            -- Consultores: apenas visualização
            (auth.jwt() ->> 'user_role' = 'consultor') OR
            -- Gestores: acesso completo às suas licenças
            (auth.jwt() ->> 'user_role' = 'gestor_licencas') OR
            -- Administradores: acesso total
            (auth.jwt() ->> 'user_role' = 'admin_sistema')
        )
    );
CREATE POLICY "Managers can insert licenses" ON T_ISSUED_LICENSE
    FOR INSERT WITH CHECK (
        auth.role() = 'authenticated' AND
        auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema')
    );
CREATE POLICY "Managers can update licenses" ON T_ISSUED_LICENSE
    FOR UPDATE USING (
        auth.role() = 'authenticated' AND
        auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema')
    );

-- T_LICENSE_AMENDMENT
ALTER TABLE T_LICENSE_AMENDMENT ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view amendments" ON T_LICENSE_AMENDMENT
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage amendments" ON T_LICENSE_AMENDMENT
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_LICENSE_TRANSFER
ALTER TABLE T_LICENSE_TRANSFER ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view transfers" ON T_LICENSE_TRANSFER
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage transfers" ON T_LICENSE_TRANSFER
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_LICENSE_DOCUMENT
ALTER TABLE T_LICENSE_DOCUMENT ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view documents" ON T_LICENSE_DOCUMENT
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage documents" ON T_LICENSE_DOCUMENT
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));

-- T_LICENSE_FEE
ALTER TABLE T_LICENSE_FEE ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view fees" ON T_LICENSE_FEE
    FOR SELECT USING (auth.role() = 'authenticated');
CREATE POLICY "Managers can manage fees" ON T_LICENSE_FEE
    FOR ALL USING (auth.role() = 'authenticated' AND auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema'));
```

#### Tabela de Titulares: T_LICENSE_HOLDER

```sql
-- Tabela de Titulares de Licenças
CREATE TABLE T_LICENSE_HOLDER (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    document_number VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255),
    phone VARCHAR(20),
    address_street VARCHAR(255),
    address_city VARCHAR(100),
    address_island VARCHAR(50),
    address_postal_code VARCHAR(10),
    organization_id UUID,
    legal_representative_id UUID,
    is_active BOOLEAN DEFAULT true,
    created_by UUID NOT NULL,
    updated_by UUID,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_license_holder_document ON T_LICENSE_HOLDER(document_number);
CREATE INDEX idx_license_holder_name ON T_LICENSE_HOLDER(name);
CREATE INDEX idx_license_holder_organization ON T_LICENSE_HOLDER(organization_id);
CREATE INDEX idx_license_holder_active ON T_LICENSE_HOLDER(is_active);

-- RLS
ALTER TABLE T_LICENSE_HOLDER ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view holders" ON T_LICENSE_HOLDER
    FOR SELECT USING (auth.role() = 'authenticated');

CREATE POLICY "Managers can manage holders" ON T_LICENSE_HOLDER
    FOR ALL USING (
        auth.role() = 'authenticated' AND
        auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema')
    );
```

#### Tabela de Renovações: T_LICENSE_RENEWAL

```sql
-- Tabela de Renovações de Licenças
CREATE TABLE T_LICENSE_RENEWAL (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_id UUID NOT NULL,
    process_number VARCHAR(50) UNIQUE NOT NULL,
    renewal_type VARCHAR(20) NOT NULL DEFAULT 'manual' CHECK (renewal_type IN ('automatic', 'manual', 'conditional')),
    start_date DATE NOT NULL,
    completion_date DATE,
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'in_progress', 'completed', 'rejected', 'cancelled')),
    observations TEXT,
    requested_by UUID NOT NULL,
    processed_by UUID,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_license_renewal_license FOREIGN KEY (license_id) REFERENCES T_ISSUED_LICENSE(id)
);

-- Índices
CREATE INDEX idx_license_renewal_license_id ON T_LICENSE_RENEWAL(license_id);
CREATE INDEX idx_license_renewal_status ON T_LICENSE_RENEWAL(status);
CREATE INDEX idx_license_renewal_start_date ON T_LICENSE_RENEWAL(start_date DESC);

-- RLS
ALTER TABLE T_LICENSE_RENEWAL ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view renewals" ON T_LICENSE_RENEWAL
    FOR SELECT USING (auth.role() = 'authenticated');

CREATE POLICY "Managers can manage renewals" ON T_LICENSE_RENEWAL
    FOR ALL USING (
        auth.role() = 'authenticated' AND
        auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema')
    );
```

#### Tabela de Auditoria: T_LICENSE_AUDIT

```sql
-- Tabela de Auditoria de Licenças
CREATE TABLE T_LICENSE_AUDIT (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_id UUID NOT NULL,
    action VARCHAR(50) NOT NULL,
    description TEXT,
    user_id UUID NOT NULL,
    user_name VARCHAR(255),
    metadata JSONB,
    ip_address INET,
    user_agent TEXT,
    timestamp TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_license_audit_license FOREIGN KEY (license_id) REFERENCES T_ISSUED_LICENSE(id)
);

-- Índices
CREATE INDEX idx_license_audit_license_id ON T_LICENSE_AUDIT(license_id);
CREATE INDEX idx_license_audit_user_id ON T_LICENSE_AUDIT(user_id);
CREATE INDEX idx_license_audit_timestamp ON T_LICENSE_AUDIT(timestamp DESC);
CREATE INDEX idx_license_audit_action ON T_LICENSE_AUDIT(action);

-- RLS
ALTER TABLE T_LICENSE_AUDIT ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view audit logs" ON T_LICENSE_AUDIT
    FOR SELECT USING (
        auth.role() = 'authenticated' AND
        auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema')
    );

-- Apenas o sistema pode inserir logs de auditoria
CREATE POLICY "System can insert audit logs" ON T_LICENSE_AUDIT
    FOR INSERT WITH CHECK (auth.role() = 'service_role');
```

#### Tabela de Alertas: T_LICENSE_ALERT

```sql
-- Tabela de Alertas de Licenças
CREATE TABLE T_LICENSE_ALERT (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_id UUID NOT NULL,
    alert_type VARCHAR(20) NOT NULL CHECK (alert_type IN ('expiry', 'renewal', 'suspension', 'document_required')),
    priority VARCHAR(10) NOT NULL DEFAULT 'medium' CHECK (priority IN ('low', 'medium', 'high', 'critical')),
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'acknowledged', 'resolved', 'dismissed')),
    trigger_date DATE NOT NULL,
    days_ahead INTEGER,
    message TEXT,
    acknowledged_by UUID,
    acknowledged_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT fk_license_alert_license FOREIGN KEY (license_id) REFERENCES T_ISSUED_LICENSE(id)
);

-- Índices
CREATE INDEX idx_license_alert_license_id ON T_LICENSE_ALERT(license_id);
CREATE INDEX idx_license_alert_type ON T_LICENSE_ALERT(alert_type);
CREATE INDEX idx_license_alert_priority ON T_LICENSE_ALERT(priority);
CREATE INDEX idx_license_alert_status ON T_LICENSE_ALERT(status);
CREATE INDEX idx_license_alert_trigger_date ON T_LICENSE_ALERT(trigger_date);

-- RLS
ALTER TABLE T_LICENSE_ALERT ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view alerts" ON T_LICENSE_ALERT
    FOR SELECT USING (auth.role() = 'authenticated');

CREATE POLICY "Managers can manage alerts" ON T_LICENSE_ALERT
    FOR ALL USING (
        auth.role() = 'authenticated' AND
        auth.jwt() ->> 'user_role' IN ('gestor_licencas', 'admin_sistema')
    );
```

### 6.3 Dados Iniciais

```sql
-- Inserir opções de configuração
INSERT INTO T_OPTIONS (option_group, option_key, option_value, description, is_active, sort_order, created_by) VALUES
('license_status', 'active', 'Ativa', 'Licença em vigor', true, 1, '00000000-0000-0000-0000-000000000000'),
('license_status', 'expired', 'Vencida', 'Licença vencida', true, 2, '00000000-0000-0000-0000-000000000000'),
('license_status', 'suspended', 'Suspensa', 'Licença suspensa', true, 3, '00000000-0000-0000-0000-000000000000'),
('license_status', 'cancelled', 'Cancelada', 'Licença cancelada', true, 4, '00000000-0000-0000-0000-000000000000'),
('license_status', 'provisional', 'Provisória', 'Licença provisória', true, 5, '00000000-0000-0000-0000-000000000000'),

('alert_priority', 'low', 'Baixa', 'Prioridade baixa', true, 1, '00000000-0000-0000-0000-000000000000'),
('alert_priority', 'medium', 'Média', 'Prioridade média', true, 2, '00000000-0000-0000-0000-000000000000'),
('alert_priority', 'high', 'Alta', 'Prioridade alta', true, 3, '00000000-0000-0000-0000-000000000000'),
('alert_priority', 'critical', 'Crítica', 'Prioridade crítica', true, 4, '00000000-0000-0000-0000-000000000000'),

('renewal_type', 'automatic', 'Automática', 'Renovação automática', true, 1, '00000000-0000-0000-0000-000000000000'),
('renewal_type', 'manual', 'Manual', 'Renovação manual', true, 2, '00000000-0000-0000-0000-000000000000'),
('renewal_type', 'conditional', 'Condicional', 'Renovação condicional', true, 3, '00000000-0000-0000-0000-000000000000');
```

## 6. Arquitetura de Serviços

### 6.1 Serviço de Licenças

```typescript
// src/services/licencas/licencas.service.ts
@Injectable()
export class LicencasService {
  constructor(
    @InjectRepository(Licenca)
    private licencasRepository: Repository<Licenca>,
    private alertasService: AlertasService,
    private notificacoesService: NotificacoesService,
    private auditoriaService: AuditoriaService,
  ) {}

  async buscarLicencas(filtros: FiltrosLicencaDto): Promise<PaginatedResult<Licenca>> {
    const queryBuilder = this.licencasRepository
      .createQueryBuilder('licenca')
      .leftJoinAndSelect('licenca.titular', 'titular')
      .leftJoinAndSelect('licenca.tipo', 'tipo')
      .leftJoinAndSelect('licenca.representanteLegal', 'representante');

    // Aplicar filtros
    if (filtros.search) {
      queryBuilder.andWhere('(licenca.numero ILIKE :search OR titular.nome ILIKE :search)', {
        search: `%${filtros.search}%`,
      });
    }

    if (filtros.status) {
      queryBuilder.andWhere('licenca.status = :status', { status: filtros.status });
    }

    if (filtros.dataVencimentoInicio && filtros.dataVencimentoFim) {
      queryBuilder.andWhere('licenca.dataVencimento BETWEEN :inicio AND :fim', {
        inicio: filtros.dataVencimentoInicio,
        fim: filtros.dataVencimentoFim,
      });
    }

    // Paginação
    const [licencas, total] = await queryBuilder
      .skip((filtros.page - 1) * filtros.limit)
      .take(filtros.limit)
      .orderBy('licenca.dataVencimento', 'ASC')
      .getManyAndCount();

    return {
      data: licencas,
      meta: {
        total,
        page: filtros.page,
        limit: filtros.limit,
        totalPages: Math.ceil(total / filtros.limit),
      },
    };
  }

  async obterDetalhesLicenca(id: string): Promise<Licenca> {
    const licenca = await this.licencasRepository
      .createQueryBuilder('licenca')
      .leftJoinAndSelect('licenca.titular', 'titular')
      .leftJoinAndSelect('licenca.tipo', 'tipo')
      .leftJoinAndSelect('licenca.representanteLegal', 'representante')
      .leftJoinAndSelect('licenca.documentos', 'documentos')
      .leftJoinAndSelect('licenca.historico', 'historico')
      .leftJoinAndSelect('licenca.taxas', 'taxas')
      .where('licenca.id = :id', { id })
      .getOne();

    if (!licenca) {
      throw new NotFoundException('Licença não encontrada');
    }

    return licenca;
  }

  async iniciarRenovacao(id: string, dadosRenovacao: RenovacaoDto): Promise<ProcessoRenovacao> {
    const licenca = await this.obterDetalhesLicenca(id);

    // Validar se licença pode ser renovada
    if (!licenca.tipo.permiteRenovacao) {
      throw new BadRequestException('Este tipo de licença não permite renovação');
    }

    // Criar processo de renovação
    const processo = await this.criarProcessoRenovacao(licenca, dadosRenovacao);

    // Registrar no histórico
    await this.auditoriaService.registrarAcao({
      licencaId: id,
      acao: 'renovacao_iniciada',
      descricao: 'Processo de renovação iniciado',
      usuario: dadosRenovacao.usuario,
    });

    return processo;
  }

  @Cron('0 6 * * *') // Executa diariamente às 6h
  async verificarVencimentos(): Promise<void> {
    const hoje = new Date();
    const em30Dias = addDays(hoje, 30);
    const em15Dias = addDays(hoje, 15);

    // Buscar licenças que vencem em 30 dias
    const licencasVencendo30 = await this.licencasRepository
      .createQueryBuilder('licenca')
      .where('licenca.dataVencimento = :data', { data: format(em30Dias, 'yyyy-MM-dd') })
      .andWhere('licenca.status = :status', { status: 'ativa' })
      .getMany();

    // Criar alertas e notificações
    for (const licenca of licencasVencendo30) {
      await this.alertasService.criarAlerta({
        licencaId: licenca.id,
        tipo: 'vencimento_30_dias',
        prioridade: 'media',
        mensagem: `Licença ${licenca.numero} vence em 30 dias`,
      });

      await this.notificacoesService.enviarNotificacao({
        licencaId: licenca.id,
        canal: 'email',
        destinatario: licenca.titular.email,
        assunto: 'Licença próxima ao vencimento',
        template: 'vencimento_30_dias',
      });
    }

    // Repetir para 15 dias e licenças vencidas
    // ...
  }
}
```

### 6.2 Serviço de Alertas

```typescript
// src/services/alertas/alertas.service.ts
@Injectable()
export class AlertasService {
  constructor(
    @InjectRepository(Alerta)
    private alertasRepository: Repository<Alerta>,
    private eventEmitter: EventEmitter2,
  ) {}

  async criarAlerta(dadosAlerta: CriarAlertaDto): Promise<Alerta> {
    const alerta = this.alertasRepository.create({
      ...dadosAlerta,
      dataAlerta: new Date(),
    });

    const alertaSalvo = await this.alertasRepository.save(alerta);

    // Emitir evento para processamento em tempo real
    this.eventEmitter.emit('alerta.criado', alertaSalvo);

    return alertaSalvo;
  }

  async obterDashboardAlertas(): Promise<DashboardAlertasDto> {
    const hoje = new Date();
    const em30Dias = addDays(hoje, 30);
    const em15Dias = addDays(hoje, 15);

    const [vencendo30, vencendo15, vencidas, renovacoesPendentes] = await Promise.all([
      this.contarLicencasVencendo(30),
      this.contarLicencasVencendo(15),
      this.contarLicencasVencidas(),
      this.contarRenovacoesPendentes(),
    ]);

    return {
      resumo: {
        licencasVencendo30Dias: vencendo30,
        licencasVencendo15Dias: vencendo15,
        licencasVencidas: vencidas,
        renovacoesPendentes: renovacoesPendentes,
      },
      alertasPorTipo: await this.obterAlertasPorTipo(),
      tendencias: await this.obterTendenciasRenovacao(),
    };
  }

  private async contarLicencasVencendo(dias: number): Promise<number> {
    const dataLimite = addDays(new Date(), dias);

    return this.alertasRepository
      .createQueryBuilder('alerta')
      .innerJoin('alerta.licenca', 'licenca')
      .where('licenca.dataVencimento <= :data', { data: dataLimite })
      .andWhere('licenca.status = :status', { status: 'ativa' })
      .andWhere('alerta.ativo = true')
      .getCount();
  }
}
```

## 7. Configurações de Deploy

### 7.1 Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - '3000:3000'
    environment:
      - REACT_APP_API_URL=http://localhost:3001/api
    depends_on:
      - backend

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - '3001:3001'
    environment:
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/licencas
      - REDIS_URL=redis://redis:6379
      - ELASTICSEARCH_URL=http://elasticsearch:9200
      - JWT_SECRET=your-jwt-secret
    depends_on:
      - postgres
      - redis
      - elasticsearch

  postgres:
    image: postgres:14
    environment:
      - POSTGRES_DB=licencas
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - '5432:5432'

  redis:
    image: redis:7-alpine
    ports:
      - '6379:6379'
    volumes:
      - redis_data:/data

  elasticsearch:
    image: elasticsearch:8.8.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    ports:
      - '9200:9200'
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data

  nginx:
    image: nginx:alpine
    ports:
      - '80:80'
      - '443:443'
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/ssl:/etc/nginx/ssl
    depends_on:
      - frontend
      - backend

volumes:
  postgres_data:
  redis_data:
  elasticsearch_data:
```

### 7.2 Configuração NGINX

```nginx
# nginx/nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream frontend {
        server frontend:3000;
    }

    upstream backend {
        server backend:3001;
    }

    server {
        listen 80;
        server_name localhost;

        # Redirect HTTP to HTTPS
        return 301 https://$server_name$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name localhost;

        ssl_certificate /etc/nginx/ssl/cert.pem;
        ssl_certificate_key /etc/nginx/ssl/key.pem;

        # Frontend
        location / {
            proxy_pass http://frontend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Backend API
        location /api/ {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # CORS headers
            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Methods "GET, POST, PUT, DELETE, OPTIONS";
            add_header Access-Control-Allow-Headers "Authorization, Content-Type";
        }

        # Health check
        location /health {
            access_log off;
            return 200 "healthy\n";
            add_header Content-Type text/plain;
        }
    }
}
```

## 8. Monitoramento e Observabilidade

### 8.1 Métricas de Aplicação

```typescript
// src/monitoring/metrics.service.ts
@Injectable()
export class MetricsService {
  private readonly registry = new Registry();
  private readonly httpRequestDuration: Histogram<string>;
  private readonly httpRequestTotal: Counter<string>;
  private readonly licencasAtivas: Gauge<string>;
  private readonly alertasAtivos: Gauge<string>;

  constructor() {
    // Métricas HTTP
    this.httpRequestDuration = new Histogram({
      name: 'http_request_duration_seconds',
      help: 'Duration of HTTP requests in seconds',
      labelNames: ['method', 'route', 'status'],
      buckets: [0.1, 0.5, 1, 2, 5],
    });

    this.httpRequestTotal = new Counter({
      name: 'http_requests_total',
      help: 'Total number of HTTP requests',
      labelNames: ['method', 'route', 'status'],
    });

    // Métricas de negócio
    this.licencasAtivas = new Gauge({
      name: 'licencas_ativas_total',
      help: 'Total number of active licenses',
    });

    this.alertasAtivos = new Gauge({
      name: 'alertas_ativos_total',
      help: 'Total number of active alerts',
      labelNames: ['tipo', 'prioridade'],
    });

    this.registry.registerMetric(this.httpRequestDuration);
    this.registry.registerMetric(this.httpRequestTotal);
    this.registry.registerMetric(this.licencasAtivas);
    this.registry.registerMetric(this.alertasAtivos);
  }

  @Cron('*/5 * * * *') // A cada 5 minutos
  async atualizarMetricasNegocio(): Promise<void> {
    // Atualizar contadores de licenças ativas
    const licencasAtivas = await this.contarLicencasAtivas();
    this.licencasAtivas.set(licencasAtivas);

    // Atualizar contadores de alertas por tipo e prioridade
    const alertasPorTipo = await this.contarAlertasPorTipo();
    for (const [tipo, dados] of Object.entries(alertasPorTipo)) {
      for (const [prioridade, count] of Object.entries(dados)) {
        this.alertasAtivos.set({ tipo, prioridade }, count as number);
      }
    }
  }

  getMetrics(): string {
    return this.registry.metrics();
  }
}
```

### 8.2 Health Checks

```typescript
// src/health/health.controller.ts
@Controller('health')
export class HealthController {
  constructor(
    private health: HealthCheckService,
    private db: TypeOrmHealthIndicator,
    private redis: RedisHealthIndicator,
    private elasticsearch: ElasticsearchHealthIndicator,
  ) {}

  @Get()
  @HealthCheck()
  check() {
    return this.health.check([
      () => this.db.pingCheck('database'),
      () => this.redis.pingCheck('redis'),
      () => this.elasticsearch.pingCheck('elasticsearch'),
      () => this.checkExternalServices(),
    ]);
  }

  private async checkExternalServices(): Promise<HealthIndicatorResult> {
    const isHealthy = await this.verificarServicosExternos();

    const result = this.getStatus('external-services', isHealthy);

    if (isHealthy) {
      result['external-services'] = {
        status: 'up',
        services: {
          parametrizacao: 'up',
          activity: 'up',
          financeiro: 'up',
        },
      };
    }

    return result;
  }
}
```

## 9. Testes

### 9.1 Testes Unitários

```typescript
// src/services/licencas/licencas.service.spec.ts
describe('LicencasService', () => {
  let service: LicencasService;
  let repository: Repository<Licenca>;
  let alertasService: AlertasService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        LicencasService,
        {
          provide: getRepositoryToken(Licenca),
          useClass: Repository,
        },
        {
          provide: AlertasService,
          useValue: {
            criarAlerta: jest.fn(),
          },
        },
      ],
    }).compile();

    service = module.get<LicencasService>(LicencasService);
    repository = module.get<Repository<Licenca>>(getRepositoryToken(Licenca));
    alertasService = module.get<AlertasService>(AlertasService);
  });

  describe('buscarLicencas', () => {
    it('deve retornar licenças paginadas', async () => {
      const mockLicencas = [
        { id: '1', numero: 'LIC-001', status: 'ativa' },
        { id: '2', numero: 'LIC-002', status: 'ativa' },
      ];

      jest.spyOn(repository, 'createQueryBuilder').mockReturnValue({
        leftJoinAndSelect: jest.fn().mockReturnThis(),
        andWhere: jest.fn().mockReturnThis(),
        skip: jest.fn().mockReturnThis(),
        take: jest.fn().mockReturnThis(),
        orderBy: jest.fn().mockReturnThis(),
        getManyAndCount: jest.fn().mockResolvedValue([mockLicencas, 2]),
      } as any);

      const resultado = await service.buscarLicencas({
        page: 1,
        limit: 10,
        search: 'LIC',
      });

      expect(resultado.data).toEqual(mockLicencas);
      expect(resultado.meta.total).toBe(2);
    });
  });

  describe('verificarVencimentos', () => {
    it('deve criar alertas para licenças vencendo', async () => {
      const mockLicencas = [
        {
          id: '1',
          numero: 'LIC-001',
          dataVencimento: addDays(new Date(), 30),
          titular: { email: 'test@example.com' },
        },
      ];

      jest.spyOn(repository, 'createQueryBuilder').mockReturnValue({
        where: jest.fn().mockReturnThis(),
        andWhere: jest.fn().mockReturnThis(),
        getMany: jest.fn().mockResolvedValue(mockLicencas),
      } as any);

      await service.verificarVencimentos();

      expect(alertasService.criarAlerta).toHaveBeenCalledWith({
        licencaId: '1',
        tipo: 'vencimento_30_dias',
        prioridade: 'media',
        mensagem: 'Licença LIC-001 vence em 30 dias',
      });
    });
  });
});
```

### 9.2 Testes de Integração

```typescript
// test/licencas.e2e-spec.ts
describe('Licenças (e2e)', () => {
  let app: INestApplication;
  let repository: Repository<Licenca>;

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    repository = moduleFixture.get<Repository<Licenca>>(getRepositoryToken(Licenca));

    await app.init();
  });

  describe('/api/licencas (GET)', () => {
    it('deve retornar lista de licenças', async () => {
      // Criar dados de teste
      await repository.save({
        numero: 'LIC-TEST-001',
        status: 'ativa',
        dataEmissao: new Date(),
        dataVencimento: addDays(new Date(), 365),
      });

      return request(app.getHttpServer())
        .get('/api/licencas')
        .expect(200)
        .expect((res) => {
          expect(res.body.data).toHaveLength(1);
          expect(res.body.data[0].numero).toBe('LIC-TEST-001');
        });
    });
  });

  describe('/api/licencas/:id/renovar (POST)', () => {
    it('deve iniciar processo de renovação', async () => {
      const licenca = await repository.save({
        numero: 'LIC-TEST-002',
        status: 'ativa',
        dataEmissao: new Date(),
        dataVencimento: addDays(new Date(), 30),
      });

      return request(app.getHttpServer())
        .post(`/api/licencas/${licenca.id}/renovar`)
        .send({
          observacoes: 'Renovação de teste',
        })
        .expect(201)
        .expect((res) => {
          expect(res.body.processoId).toBeDefined();
          expect(res.body.status).toBe('em_analise');
        });
    });
  });
});
```

## 5. Modelagem PostgreSQL

### 5.1 Diagrama ER Completo

```mermaid
erDiagram
    USERS {
        uuid id PK
        varchar email UK
        varchar password_hash
        varchar full_name
        varchar role
        timestamp created_at
        timestamp updated_at
        boolean active
    }

    LICENSE_HOLDERS {
        uuid id PK
        varchar holder_type
        varchar document_number UK
        varchar full_name
        varchar email
        varchar phone
        text address
        varchar city
        varchar postal_code
        date birth_date
        varchar gender
        varchar nationality
        timestamp created_at
        timestamp updated_at
        boolean active
    }

    CORPORATE_HOLDERS {
        uuid id PK
        uuid holder_id FK
        varchar company_name
        varchar tax_number UK
        varchar legal_form
        varchar business_sector
        date establishment_date
        varchar registration_number
        text business_address
        varchar contact_person
        varchar contact_email
        varchar contact_phone
    }

    LICENSE_ISSUERS {
        uuid id PK
        varchar issuer_code UK
        varchar issuer_name
        varchar department
        text address
        varchar contact_email
        varchar contact_phone
        boolean active
        timestamp created_at
        timestamp updated_at
    }

    LICENSE_TYPES {
        uuid id PK
        varchar type_code UK
        varchar type_name
        text description
        integer validity_months
        decimal fee_amount
        varchar currency
        text requirements
        boolean active
        timestamp created_at
        timestamp updated_at
    }

    ISSUED_LICENSES {
        uuid id PK
        varchar license_number UK
        uuid holder_id FK
        uuid license_type_id FK
        uuid issuer_id FK
        varchar status
        date issue_date
        date expiry_date
        date renewal_date
        text conditions
        text notes
        decimal fee_paid
        varchar payment_reference
        timestamp created_at
        timestamp updated_at
        uuid created_by FK
        uuid updated_by FK
    }

    LICENSE_DOCUMENTS {
        uuid id PK
        uuid license_id FK
        varchar document_type
        varchar file_name
        varchar file_path
        varchar mime_type
        bigint file_size
        varchar checksum
        timestamp uploaded_at
        uuid uploaded_by FK
    }

    LICENSE_HISTORY {
        uuid id PK
        uuid license_id FK
        varchar action_type
        varchar old_status
        varchar new_status
        text description
        timestamp action_date
        uuid performed_by FK
        jsonb metadata
    }

    AUDIT_LOGS {
        uuid id PK
        varchar entity_type
        uuid entity_id
        varchar action
        jsonb old_values
        jsonb new_values
        timestamp timestamp
        uuid user_id FK
        varchar ip_address
        varchar user_agent
    }

    %% Relacionamentos
    LICENSE_HOLDERS ||--o{ ISSUED_LICENSES : "possui"
    LICENSE_HOLDERS ||--o| CORPORATE_HOLDERS : "extends"
    LICENSE_TYPES ||--o{ ISSUED_LICENSES : "classifica"
    LICENSE_ISSUERS ||--o{ ISSUED_LICENSES : "emite"
    ISSUED_LICENSES ||--o{ LICENSE_DOCUMENTS : "possui"
    ISSUED_LICENSES ||--o{ LICENSE_HISTORY : "registra"
    USERS ||--o{ ISSUED_LICENSES : "cria/atualiza"
    USERS ||--o{ LICENSE_DOCUMENTS : "carrega"
    USERS ||--o{ LICENSE_HISTORY : "executa"
    USERS ||--o{ AUDIT_LOGS : "gera"
```

### 5.2 Esquema de Tabelas e Relacionamentos

#### Tabela: users

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL DEFAULT 'USER' CHECK (role IN ('ADMIN', 'OPERATOR', 'USER')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    active BOOLEAN DEFAULT true
);
```

#### Tabela: license_holders

```sql
CREATE TABLE license_holders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    holder_type VARCHAR(20) NOT NULL CHECK (holder_type IN ('INDIVIDUAL', 'CORPORATE')),
    document_number VARCHAR(50) UNIQUE NOT NULL,
    full_name VARCHAR(255) NOT NULL,
    email VARCHAR(255),
    phone VARCHAR(20),
    address TEXT,
    city VARCHAR(100),
    postal_code VARCHAR(20),
    birth_date DATE,
    gender VARCHAR(10) CHECK (gender IN ('M', 'F', 'OTHER')),
    nationality VARCHAR(100),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    active BOOLEAN DEFAULT true
);
```

#### Tabela: corporate_holders

```sql
CREATE TABLE corporate_holders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    holder_id UUID NOT NULL REFERENCES license_holders(id) ON DELETE CASCADE,
    company_name VARCHAR(255) NOT NULL,
    tax_number VARCHAR(50) UNIQUE NOT NULL,
    legal_form VARCHAR(100),
    business_sector VARCHAR(100),
    establishment_date DATE,
    registration_number VARCHAR(100),
    business_address TEXT,
    contact_person VARCHAR(255),
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20)
);
```

#### Tabela: license_types

```sql
CREATE TABLE license_types (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    type_code VARCHAR(20) UNIQUE NOT NULL,
    type_name VARCHAR(255) NOT NULL,
    description TEXT,
    validity_months INTEGER NOT NULL DEFAULT 12,
    fee_amount DECIMAL(10,2) NOT NULL DEFAULT 0.00,
    currency VARCHAR(3) DEFAULT 'CVE',
    requirements TEXT,
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

#### Tabela: license_issuers

```sql
CREATE TABLE license_issuers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    issuer_code VARCHAR(20) UNIQUE NOT NULL,
    issuer_name VARCHAR(255) NOT NULL,
    department VARCHAR(255),
    address TEXT,
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20),
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

#### Tabela: issued_licenses

```sql
CREATE TABLE issued_licenses (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    license_number VARCHAR(50) UNIQUE NOT NULL,
    holder_id UUID NOT NULL REFERENCES license_holders(id),
    license_type_id UUID NOT NULL REFERENCES license_types(id),
    issuer_id UUID NOT NULL REFERENCES license_issuers(id),
    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE' CHECK (status IN ('ACTIVE', 'EXPIRED', 'SUSPENDED', 'REVOKED', 'PENDING')),
    issue_date DATE NOT NULL DEFAULT CURRENT_DATE,
    expiry_date DATE NOT NULL,
    renewal_date DATE,
    conditions TEXT,
    notes TEXT,
    fee_paid DECIMAL(10,2) DEFAULT 0.00,
    payment_reference VARCHAR(100),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by UUID REFERENCES users(id),
    updated_by UUID REFERENCES users(id)
);
```

### 5.3 Índices e Otimizações

```sql
-- Índices para performance de consultas

-- Users
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_active ON users(active);
CREATE INDEX idx_users_role ON users(role);

-- License Holders
CREATE INDEX idx_license_holders_document_number ON license_holders(document_number);
CREATE INDEX idx_license_holders_email ON license_holders(email);
CREATE INDEX idx_license_holders_type ON license_holders(holder_type);
CREATE INDEX idx_license_holders_active ON license_holders(active);
CREATE INDEX idx_license_holders_created_at ON license_holders(created_at DESC);

-- Corporate Holders
CREATE INDEX idx_corporate_holders_holder_id ON corporate_holders(holder_id);
CREATE INDEX idx_corporate_holders_tax_number ON corporate_holders(tax_number);
CREATE INDEX idx_corporate_holders_company_name ON corporate_holders(company_name);

-- License Types
CREATE INDEX idx_license_types_code ON license_types(type_code);
CREATE INDEX idx_license_types_active ON license_types(active);

-- License Issuers
CREATE INDEX idx_license_issuers_code ON license_issuers(issuer_code);
CREATE INDEX idx_license_issuers_active ON license_issuers(active);

-- Issued Licenses (Índices mais críticos)
CREATE INDEX idx_issued_licenses_number ON issued_licenses(license_number);
CREATE INDEX idx_issued_licenses_holder_id ON issued_licenses(holder_id);
CREATE INDEX idx_issued_licenses_type_id ON issued_licenses(license_type_id);
CREATE INDEX idx_issued_licenses_issuer_id ON issued_licenses(issuer_id);
CREATE INDEX idx_issued_licenses_status ON issued_licenses(status);
CREATE INDEX idx_issued_licenses_issue_date ON issued_licenses(issue_date DESC);
CREATE INDEX idx_issued_licenses_expiry_date ON issued_licenses(expiry_date);
CREATE INDEX idx_issued_licenses_created_at ON issued_licenses(created_at DESC);

-- Índices compostos para consultas complexas
CREATE INDEX idx_issued_licenses_holder_status ON issued_licenses(holder_id, status);
CREATE INDEX idx_issued_licenses_type_status ON issued_licenses(license_type_id, status);
CREATE INDEX idx_issued_licenses_expiry_status ON issued_licenses(expiry_date, status) WHERE status = 'ACTIVE';

-- License Documents
CREATE INDEX idx_license_documents_license_id ON license_documents(license_id);
CREATE INDEX idx_license_documents_type ON license_documents(document_type);
CREATE INDEX idx_license_documents_uploaded_at ON license_documents(uploaded_at DESC);

-- License History
CREATE INDEX idx_license_history_license_id ON license_history(license_id);
CREATE INDEX idx_license_history_action_date ON license_history(action_date DESC);
CREATE INDEX idx_license_history_action_type ON license_history(action_type);

-- Audit Logs
CREATE INDEX idx_audit_logs_entity ON audit_logs(entity_type, entity_id);
CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_timestamp ON audit_logs(timestamp DESC);
CREATE INDEX idx_audit_logs_action ON audit_logs(action);
```

### 5.4 Scripts SQL de Criação do Banco

#### V1\_\_Create_Initial_Schema.sql

```sql
-- Extensões necessárias
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";

-- Função para atualizar timestamp
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Criação das tabelas (conforme definido acima)
-- ... (todas as tabelas CREATE TABLE)

-- Triggers para updated_at
CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_license_holders_updated_at BEFORE UPDATE ON license_holders
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_license_types_updated_at BEFORE UPDATE ON license_types
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_license_issuers_updated_at BEFORE UPDATE ON license_issuers
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_issued_licenses_updated_at BEFORE UPDATE ON issued_licenses
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

#### V2\_\_Create_Indexes.sql

```sql
-- Todos os índices definidos na seção 5.3
```

#### V3\_\_Insert_Initial_Data.sql

```sql
-- Dados iniciais para license_types
INSERT INTO license_types (type_code, type_name, description, validity_months, fee_amount) VALUES
('DRIVING', 'Carta de Condução', 'Licença para conduzir veículos motorizados', 60, 5000.00),
('BUSINESS', 'Licença Comercial', 'Licença para atividade comercial', 12, 15000.00),
('CONSTRUCTION', 'Licença de Construção', 'Licença para atividades de construção', 24, 25000.00),
('FISHING', 'Licença de Pesca', 'Licença para atividade pesqueira', 12, 3000.00),
('TOURISM', 'Licença Turística', 'Licença para atividades turísticas', 12, 20000.00);

-- Dados iniciais para license_issuers
INSERT INTO license_issuers (issuer_code, issuer_name, department, contact_email) VALUES
('DGTT', 'Direção Geral de Transportes Terrestres', 'Transportes', 'dgtt@gov.cv'),
('DGCI', 'Direção Geral de Comércio e Indústria', 'Comércio', 'dgci@gov.cv'),
('DGOTDU', 'Direção Geral de Ordenamento do Território', 'Construção', 'dgotdu@gov.cv'),
('DGPESCA', 'Direção Geral das Pescas', 'Pescas', 'dgpesca@gov.cv'),
('DGTURISMO', 'Direção Geral do Turismo', 'Turismo', 'dgturismo@gov.cv');

-- Usuário administrador padrão
INSERT INTO users (email, password_hash, full_name, role) VALUES
('admin@gov.cv', '$2a$10$N9qo8uLOickgx2ZMRZoMye7VFnjZcmcOjdvqrxmfO/VfpEUfTXTvi', 'Administrador Sistema', 'ADMIN');
```

## 6. Autenticação JWT

### 6.1 Configuração Spring Security

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity(prePostEnabled = true)
@RequiredArgsConstructor
public class SecurityConfig {

    private final JwtAuthenticationEntryPoint jwtAuthenticationEntryPoint;
    private final JwtAccessDeniedHandler jwtAccessDeniedHandler;
    private final JwtTokenProvider jwtTokenProvider;

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(12);
    }

    @Bean
    public AuthenticationManager authenticationManager(
            AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .sessionManagement(session ->
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .exceptionHandling(exceptions -> exceptions
                .authenticationEntryPoint(jwtAuthenticationEntryPoint)
                .accessDeniedHandler(jwtAccessDeniedHandler))
            .authorizeHttpRequests(authz -> authz
                // Endpoints públicos
                .requestMatchers("/api/v1/auth/**").permitAll()
                .requestMatchers("/api/v1/public/**").permitAll()
                .requestMatchers("/actuator/health").permitAll()
                .requestMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()

                // Endpoints administrativos
                .requestMatchers("/api/v1/admin/**").hasRole("ADMIN")
                .requestMatchers(HttpMethod.DELETE, "/api/v1/**").hasRole("ADMIN")

                // Endpoints de operadores
                .requestMatchers(HttpMethod.POST, "/api/v1/licenses").hasAnyRole("ADMIN", "OPERATOR")
                .requestMatchers(HttpMethod.PUT, "/api/v1/licenses/**").hasAnyRole("ADMIN", "OPERATOR")

                // Demais endpoints requerem autenticação
                .anyRequest().authenticated())
            .addFilterBefore(new JwtAuthenticationFilter(jwtTokenProvider),
                           UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
}
```

### 6.2 JWT Token Provider

```java
@Component
@Slf4j
public class JwtTokenProvider {

    private final String jwtSecret;
    private final long jwtExpirationMs;
    private final long jwtRefreshExpirationMs;
    private final Key key;

    public JwtTokenProvider(
            @Value("${spring.security.jwt.secret}") String jwtSecret,
            @Value("${spring.security.jwt.expiration}") long jwtExpirationMs,
            @Value("${spring.security.jwt.refresh-expiration}") long jwtRefreshExpirationMs) {

        this.jwtSecret = jwtSecret;
        this.jwtExpirationMs = jwtExpirationMs;
        this.jwtRefreshExpirationMs = jwtRefreshExpirationMs;
        this.key = Keys.hmacShaKeyFor(Decoders.BASE64.decode(jwtSecret));
    }

    public String generateAccessToken(Authentication authentication) {
        UserPrincipal userPrincipal = (UserPrincipal) authentication.getPrincipal();
        Date expiryDate = new Date(System.currentTimeMillis() + jwtExpirationMs);

        return Jwts.builder()
                .setSubject(userPrincipal.getId().toString())
                .setIssuedAt(new Date())
                .setExpiration(expiryDate)
                .claim("email", userPrincipal.getEmail())
                .claim("role", userPrincipal.getAuthorities().iterator().next().getAuthority())
                .signWith(key, SignatureAlgorithm.HS512)
                .compact();
    }

    public String generateRefreshToken(UUID userId) {
        Date expiryDate = new Date(System.currentTimeMillis() + jwtRefreshExpirationMs);

        return Jwts.builder()
                .setSubject(userId.toString())
                .setIssuedAt(new Date())
                .setExpiration(expiryDate)
                .claim("type", "refresh")
                .signWith(key, SignatureAlgorithm.HS512)
                .compact();
    }

    public UUID getUserIdFromToken(String token) {
        Claims claims = Jwts.parserBuilder()
                .setSigningKey(key)
                .build()
                .parseClaimsJws(token)
                .getBody();

        return UUID.fromString(claims.getSubject());
    }

    public boolean validateToken(String token) {
        try {
            Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token);
            return true;
        } catch (SecurityException ex) {
            log.error("Invalid JWT signature: {}", ex.getMessage());
        } catch (MalformedJwtException ex) {
            log.error("Invalid JWT token: {}", ex.getMessage());
        } catch (ExpiredJwtException ex) {
            log.error("Expired JWT token: {}", ex.getMessage());
        } catch (UnsupportedJwtException ex) {
            log.error("Unsupported JWT token: {}", ex.getMessage());
        } catch (IllegalArgumentException ex) {
            log.error("JWT claims string is empty: {}", ex.getMessage());
        }
        return false;
    }
}
```

### 6.3 Authentication Filter

```java
@RequiredArgsConstructor
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtTokenProvider tokenProvider;

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain) throws ServletException, IOException {

        try {
            String jwt = getJwtFromRequest(request);

            if (StringUtils.hasText(jwt) && tokenProvider.validateToken(jwt)) {
                UUID userId = tokenProvider.getUserIdFromToken(jwt);

                UserDetails userDetails = userDetailsService.loadUserById(userId);
                UsernamePasswordAuthenticationToken authentication =
                    new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());

                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
        } catch (Exception ex) {
            logger.error("Could not set user authentication in security context", ex);
        }

        filterChain.doFilter(request, response);
    }

    private String getJwtFromRequest(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

## 7. Integração Frontend

### 7.1 Endpoints REST Disponíveis

#### Autenticação

```java
@RestController
@RequestMapping("/api/v1/auth")
@RequiredArgsConstructor
public class AuthController {

    private final AuthenticationService authService;

    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@Valid @RequestBody LoginRequest request) {
        AuthResponse response = authService.authenticate(request);
        return ResponseEntity.ok(response);
    }

    @PostMapping("/refresh")
    public ResponseEntity<AuthResponse> refresh(@Valid @RequestBody RefreshTokenRequest request) {
        AuthResponse response = authService.refreshToken(request.getRefreshToken());
        return ResponseEntity.ok(response);
    }

    @PostMapping("/logout")
    @PreAuthorize("isAuthenticated()")
    public ResponseEntity<Void> logout(HttpServletRequest request) {
        String token = extractTokenFromRequest(request);
        authService.logout(token);
        return ResponseEntity.ok().build();
    }
}
```

#### Gestão de Licenças

```java
@RestController
@RequestMapping("/api/v1/licenses")
@RequiredArgsConstructor
@Validated
public class LicenseController {

    private final LicenseApplicationService licenseService;
    private final LicenseMapper licenseMapper;

    @GetMapping
    @PreAuthorize("hasAnyRole('ADMIN', 'OPERATOR', 'USER')")
    public ResponseEntity<PagedResponse<LicenseResponse>> getAllLicenses(
            @Valid @ModelAttribute LicenseSearchRequest request,
            @PageableDefault(size = 25, sort = "createdAt", direction = Sort.Direction.DESC) Pageable pageable) {

        Page<IssuedLicense> licenses = licenseService.searchLicenses(request, pageable);
        PagedResponse<LicenseResponse> response = licenseMapper.toPagedResponse(licenses);

        return ResponseEntity.ok(response);
    }

    @GetMapping("/{id}")
    @PreAuthorize("hasAnyRole('ADMIN', 'OPERATOR') or @licenseSecurityService.canAccessLicense(#id, authentication.name)")
    public ResponseEntity<LicenseDetailResponse> getLicenseById(@PathVariable UUID id) {
        IssuedLicense license = licenseService.findById(id);
        LicenseDetailResponse response = licenseMapper.toDetailResponse(license);
        return ResponseEntity.ok(response);
    }

    @PostMapping
    @PreAuthorize("hasAnyRole('ADMIN', 'OPERATOR')")
    public ResponseEntity<LicenseResponse> createLicense(
            @Valid @RequestBody CreateLicenseRequest request,
            Authentication authentication) {

        IssueLicenseCommand command = licenseMapper.toCommand(request, authentication);
        IssuedLicense license = licenseService.issueLicense(command);
        LicenseResponse response = licenseMapper.toResponse(license);

        return ResponseEntity.status(HttpStatus.CREATED)
                .location(URI.create("/api/v1/licenses/" + license.getId()))
                .body(response);
    }

    @PutMapping("/{id}")
    @PreAuthorize("hasAnyRole('ADMIN', 'OPERATOR')")
    public ResponseEntity<LicenseResponse> updateLicense(
            @PathVariable UUID id,
            @Valid @RequestBody UpdateLicenseRequest request,
            Authentication authentication) {

        UpdateLicenseCommand command = licenseMapper.toUpdateCommand(id, request, authentication);
        IssuedLicense license = licenseService.updateLicense(command);
        LicenseResponse response = licenseMapper.toResponse(license);

        return ResponseEntity.ok(response);
    }

    @PostMapping("/{id}/renew")
    @PreAuthorize("hasAnyRole('ADMIN', 'OPERATOR')")
    public ResponseEntity<LicenseResponse> renewLicense(
            @PathVariable UUID id,
            @Valid @RequestBody RenewLicenseRequest request,
            Authentication authentication) {

        RenewLicenseCommand command = licenseMapper.toRenewCommand(id, request, authentication);
        IssuedLicense license = licenseService.renewLicense(command);
        LicenseResponse response = licenseMapper.toResponse(license);

        return ResponseEntity.ok(response);
    }

    @PostMapping("/{id}/suspend")
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<Void> suspendLicense(
            @PathVariable UUID id,
            @Valid @RequestBody SuspendLicenseRequest request,
            Authentication authentication) {

        SuspendLicenseCommand command = licenseMapper.toSuspendCommand(id, request, authentication);
        licenseService.suspendLicense(command);

        return ResponseEntity.ok().build();
    }
}
```

### 7.2 Modelos de Requisição/Resposta

#### DTOs de Autenticação

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class LoginRequest {

    @NotBlank(message = "Email é obrigatório")
    @Email(message = "Email deve ter formato válido")
    private String email;

    @NotBlank(message = "Senha é obrigatória")
    @Size(min = 6, message = "Senha deve ter pelo menos 6 caracteres")
    private String password;
}

@Data
@Builder
public class AuthResponse {
    private String accessToken;
    private String refreshToken;
    private String tokenType = "Bearer";
    private Long expiresIn;
    private UserInfo user;

    @Data
    @Builder
    public static class UserInfo {
        private UUID id;
        private String email;
        private String fullName;
        private String role;
    }
}
```

#### DTOs de Licenças

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class CreateLicenseRequest {

    @NotNull(message = "ID do titular é obrigatório")
    private UUID holderId;

    @NotNull(message = "ID do tipo de licença é obrigatório")
    private UUID licenseTypeId;

    @NotNull(message = "ID do emissor é obrigatório")
    private UUID issuerId;

    @Future(message = "Data de vencimento deve ser futura")
    private LocalDate expiryDate;

    @Size(max = 1000, message = "Condições não podem exceder 1000 caracteres")
    private String conditions;

    @Size(max = 500, message = "Observações não podem exceder 500 caracteres")
    private String notes;

    @DecimalMin(value = "0.0", message = "Taxa paga deve ser positiva")
    private BigDecimal feePaid;

    private String paymentReference;
}

@Data
@Builder
public class LicenseResponse {
    private UUID id;
    private String licenseNumber;
    private HolderSummary holder;
    private LicenseTypeSummary licenseType;
    private IssuerSummary issuer;
    private LicenseStatus status;
    private LocalDate issueDate;
    private LocalDate expiryDate;
    private LocalDate renewalDate;
    private BigDecimal feePaid;
    private String paymentReference;
    private Instant createdAt;
    private Instant updatedAt;

    @Data
    @Builder
    public static class HolderSummary {
        private UUID id;
        private String name;
        private String documentNumber;
        private HolderType type;
    }

    @Data
    @Builder
    public static class LicenseTypeSummary {
        private UUID id;
        private String code;
        private String name;
        private Integer validityMonths;
    }

    @Data
    @Builder
    public static class IssuerSummary {
        private UUID id;
        private String code;
        private String name;
        private String department;
    }
}
```

### 7.3 Tratamento de Erros e Status Codes

#### Global Exception Handler

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(ValidationException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ErrorResponse handleValidation(ValidationException ex) {
        log.warn("Validation error: {}", ex.getMessage());
        return ErrorResponse.builder()
                .error("VALIDATION_ERROR")
                .message(ex.getMessage())
                .timestamp(Instant.now())
                .build();
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ErrorResponse handleMethodArgumentNotValid(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error ->
            errors.put(error.getField(), error.getDefaultMessage()));

        return ErrorResponse.builder()
                .error("VALIDATION_ERROR")
                .message("Dados de entrada inválidos")
                .details(errors)
                .timestamp(Instant.now())
                .build();
    }

    @ExceptionHandler(EntityNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponse handleEntityNotFound(EntityNotFoundException ex) {
        log.warn("Entity not found: {}", ex.getMessage());
        return ErrorResponse.builder()
                .error("ENTITY_NOT_FOUND")
                .message(ex.getMessage())
                .timestamp(Instant.now())
                .build();
    }

    @ExceptionHandler(BusinessRuleException.class)
    @ResponseStatus(HttpStatus.UNPROCESSABLE_ENTITY)
    public ErrorResponse handleBusinessRule(BusinessRuleException ex) {
        log.warn("Business rule violation: {}", ex.getMessage());
        return ErrorResponse.builder()
                .error("BUSINESS_RULE_VIOLATION")
                .message(ex.getMessage())
                .timestamp(Instant.now())
                .build();
    }

    @ExceptionHandler(AccessDeniedException.class)
    @ResponseStatus(HttpStatus.FORBIDDEN)
    public ErrorResponse handleAccessDenied(AccessDeniedException ex) {
        log.warn("Access denied: {}", ex.getMessage());
        return ErrorResponse.builder()
                .error("ACCESS_DENIED")
                .message("Acesso negado para este recurso")
                .timestamp(Instant.now())
                .build();
    }

    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ErrorResponse handleGeneral(Exception ex) {
        log.error("Unexpected error", ex);
        return ErrorResponse.builder()
                .error("INTERNAL_SERVER_ERROR")
                .message("Erro interno do servidor")
                .timestamp(Instant.now())
                .build();
    }
}

@Data
@Builder
public class ErrorResponse {
    private String error;
    private String message;
    private Map<String, Object> details;
    private Instant timestamp;
}
```

#### Status Codes Padronizados

| Status Code               | Situação                 | Exemplo                      |
| ------------------------- | ------------------------ | ---------------------------- |
| 200 OK                    | Operação bem-sucedida    | GET /api/v1/licenses         |
| 201 Created               | Recurso criado           | POST /api/v1/licenses        |
| 204 No Content            | Operação sem retorno     | DELETE /api/v1/licenses/{id} |
| 400 Bad Request           | Dados inválidos          | Validação de campos          |
| 401 Unauthorized          | Não autenticado          | Token JWT inválido           |
| 403 Forbidden             | Sem permissão            | Acesso negado por role       |
| 404 Not Found             | Recurso não encontrado   | Licença inexistente          |
| 409 Conflict              | Conflito de estado       | Licença já existe            |
| 422 Unprocessable Entity  | Regra de negócio violada | Titular inativo              |
| 500 Internal Server Error | Erro interno             | Falha no banco de dados      |

## 8. Diagramas Arquiteturais

### 8.1 Diagrama de Componentes Atualizado

```mermaid
graph TB
    subgraph "Frontend Layer"
        WEB["React Web App"]
        MOBILE["React Native App"]
    end

    subgraph "API Gateway"
        NGINX["Nginx Reverse Proxy"]
    end

    subgraph "Application Layer - Spring Boot"
        AUTH["Authentication Service"]
        LICENSE["License Service"]
        HOLDER["Holder Service"]
        ISSUER["Issuer Service"]
        AUDIT["Audit Service"]
    end

    subgraph "Security Layer"
        JWT["JWT Token Provider"]
        RBAC["Role-Based Access Control"]
        FILTER["JWT Authentication Filter"]
    end

    subgraph "Data Access Layer"
        REPO["JPA Repositories"]
        CACHE["Redis Cache"]
    end

    subgraph "Database Layer"
        POSTGRES[("PostgreSQL 15+")]
        REDIS[("Redis 7+")]
    end

    subgraph "External Services"
        EMAIL["Email Service"]
        FILE["File Storage"]
        PAYMENT["Payment Gateway"]
    end

    WEB --> NGINX
    MOBILE --> NGINX
    NGINX --> AUTH
    NGINX --> LICENSE
    NGINX --> HOLDER
    NGINX --> ISSUER

    AUTH --> JWT
    AUTH --> RBAC
    LICENSE --> FILTER
    HOLDER --> FILTER
    ISSUER --> FILTER

    LICENSE --> REPO
    HOLDER --> REPO
    ISSUER --> REPO
    AUDIT --> REPO

    REPO --> POSTGRES
    CACHE --> REDIS

    LICENSE --> EMAIL
    LICENSE --> FILE
    LICENSE --> PAYMENT
```

### 8.2 Fluxo Completo de Requisições

#### Fluxo de Autenticação

```mermaid
sequenceDiagram
    participant C as Cliente
    participant N as Nginx
    participant A as Auth Service
    participant J as JWT Provider
    participant P as PostgreSQL
    participant R as Redis

    C->>N: POST /api/v1/auth/login
    N->>A: Forward request
    A->>P: Validate credentials
    P-->>A: User data
    A->>J: Generate tokens
    J-->>A: Access + Refresh tokens
    A->>R: Store refresh token
    A-->>N: AuthResponse
    N-->>C: 200 OK + tokens
```

#### Fluxo de Criação de Licença

```mermaid
sequenceDiagram
    participant C as Cliente
    participant N as Nginx
    participant F as JWT Filter
    participant L as License Service
    participant H as Holder Service
    participant I as Issuer Service
    participant P as PostgreSQL
    participant Au as Audit Service
    participant E as Email Service

    C->>N: POST /api/v1/licenses + JWT
    N->>F: Validate JWT
    F->>L: Authorized request
    L->>H: Validate holder
    H-->>L: Holder valid
    L->>I: Validate issuer
    I-->>L: Issuer valid
    L->>P: Create license
    P-->>L: License created
    L->>Au: Log creation
    L->>E: Send notification
    L-->>N: LicenseResponse
    N-->>C: 201 Created
```

#### Fluxo de Consulta com RLS

```mermaid
sequenceDiagram
    participant C as Cliente
    participant N as Nginx
    participant F as JWT Filter
    participant L as License Service
    participant R as Redis Cache
    participant P as PostgreSQL

    C->>N: GET /api/v1/licenses + JWT
    N->>F: Validate JWT
    F->>L: Authorized request + User context
    L->>R: Check cache
    alt Cache hit
        R-->>L: Cached data
    else Cache miss
        L->>P: Query with RLS context
        Note over P: SET app.current_user_id = user_id
        P-->>L: Filtered results
        L->>R: Update cache
    end
    L-->>N: PagedResponse
    N-->>C: 200 OK + data
```

### 8.3 Topologia de Implantação

#### Ambiente de Produção

```mermaid
graph TB
    subgraph "Load Balancer"
        LB["AWS ALB / Azure Load Balancer"]
    end

    subgraph "DMZ - Public Subnet"
        NGINX1["Nginx 1"]
        NGINX2["Nginx 2"]
    end

    subgraph "Application Tier - Private Subnet"
        APP1["Spring Boot App 1<br/>Port 8080"]
        APP2["Spring Boot App 2<br/>Port 8080"]
        APP3["Spring Boot App 3<br/>Port 8080"]
    end

    subgraph "Cache Tier"
        REDIS_MASTER["Redis Master"]
        REDIS_REPLICA["Redis Replica"]
    end

    subgraph "Database Tier - Private Subnet"
        PG_MASTER["PostgreSQL Master<br/>Port 5432"]
        PG_REPLICA["PostgreSQL Read Replica"]
    end

    subgraph "Monitoring & Logging"
        PROMETHEUS["Prometheus"]
        GRAFANA["Grafana"]
        ELK["ELK Stack"]
    end

    subgraph "External Services"
        S3["AWS S3 / Azure Blob"]
        SES["AWS SES / SendGrid"]
        PAYMENT_GW["Payment Gateway"]
    end

    LB --> NGINX1
    LB --> NGINX2

    NGINX1 --> APP1
    NGINX1 --> APP2
    NGINX2 --> APP2
    NGINX2 --> APP3

    APP1 --> REDIS_MASTER
    APP2 --> REDIS_MASTER
    APP3 --> REDIS_MASTER

    REDIS_MASTER --> REDIS_REPLICA

    APP1 --> PG_MASTER
    APP2 --> PG_MASTER
    APP3 --> PG_MASTER

    APP1 --> PG_REPLICA
    APP2 --> PG_REPLICA
    APP3 --> PG_REPLICA

    PG_MASTER --> PG_REPLICA

    APP1 --> S3
    APP1 --> SES
    APP1 --> PAYMENT_GW

    APP1 --> PROMETHEUS
    APP2 --> PROMETHEUS
    APP3 --> PROMETHEUS

    PROMETHEUS --> GRAFANA

    APP1 --> ELK
    APP2 --> ELK
    APP3 --> ELK
```

#### Configuração de Rede e Segurança

| Componente       | Subnet  | Portas                | Acesso        |
| ---------------- | ------- | --------------------- | ------------- |
| Load Balancer    | Public  | 80, 443               | Internet      |
| Nginx            | Public  | 80, 443, 8080         | Load Balancer |
| Spring Boot Apps | Private | 8080, 8081 (actuator) | Nginx         |
| PostgreSQL       | Private | 5432                  | Apps only     |
| Redis            | Private | 6379                  | Apps only     |
| Monitoring       | Private | 9090, 3000, 9200      | Admin VPN     |

#### Especificações de Infraestrutura

**Aplicação (Spring Boot)**

- **CPU**: 2-4 vCPUs por instância

- **RAM**: 4-8 GB por instância

- **Storage**: 50 GB SSD

- **JVM**: OpenJDK 17+ com G1GC

- **Heap**: -Xms2g -Xmx6g

**Banco de Dados (PostgreSQL)**

- **CPU**: 4-8 vCPUs

- **RAM**: 16-32 GB

- **Storage**: 500 GB SSD com IOPS provisionado

- **Connections**: max_connections = 200

- **Shared Buffers**: 25% da RAM

**Cache (Redis)**

- **CPU**: 2 vCPUs

- **RAM**: 8-16 GB

- **Storage**: 100 GB SSD

- **Persistence**: RDB + AOF

- **Max Memory Policy**: allkeys-lru

## 9. Configurações de Deploy

### 9.1 Docker Configuration

#### Dockerfile

```dockerfile
FROM openjdk:17-jdk-slim

VOLUME /tmp

# Criar usuário não-root
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Copiar JAR
COPY target/license-management-*.jar app.jar

# Configurar propriedades do JVM
ENV JAVA_OPTS="-Xms2g -Xmx6g -XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:+UseStringDeduplication"

# Expor porta
EXPOSE 8080 8081

# Mudar para usuário não-root
USER appuser

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8081/actuator/health || exit 1

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app.jar"]
```

#### docker-compose.yml

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - '8080:8080'
      - '8081:8081'
    environment:
      - SPRING_PROFILES_ACTIVE=docker
      - SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/license_db
      - SPRING_REDIS_HOST=redis
    depends_on:
      - postgres
      - redis
    networks:
      - app-network
    restart: unless-stopped

  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=license_db
      - POSTGRES_USER=license_user
      - POSTGRES_PASSWORD=license_pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./scripts/init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - '5432:5432'
    networks:
      - app-network
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes --maxmemory 1gb --maxmemory-policy allkeys-lru
    volumes:
      - redis_data:/data
    ports:
      - '6379:6379'
    networks:
      - app-network
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    ports:
      - '80:80'
      - '443:443'
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/ssl:/etc/nginx/ssl
    depends_on:
      - app
    networks:
      - app-network
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:

networks:
  app-network:
    driver: bridge
```

### 9.2 Kubernetes Configuration

#### deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: license-management-app
  labels:
    app: license-management
spec:
  replicas: 3
  selector:
    matchLabels:
      app: license-management
  template:
    metadata:
      labels:
        app: license-management
    spec:
      containers:
        - name: app
          image: license-management:latest
          ports:
            - containerPort: 8080
            - containerPort: 8081
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: 'kubernetes'
            - name: SPRING_DATASOURCE_URL
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: url
            - name: SPRING_DATASOURCE_USERNAME
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: username
            - name: SPRING_DATASOURCE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: password
          resources:
            requests:
              memory: '2Gi'
              cpu: '1000m'
            limits:
              memory: '6Gi'
              cpu: '2000m'
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8081
            initialDelaySeconds: 60
            periodSeconds: 30
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8081
            initialDelaySeconds: 30
            periodSeconds: 10
```

## 10. Conformidade Legal e Regulamentação (NOVO)

### 10.1 Alinhamento com Legislação Cabo-Verdiana

#### Marcos Legais de Referência

- **Lei nº 103/VIII/2016** - Lei de Bases do Licenciamento Industrial

- **Decreto-Lei nº 54/2018** - Regulamento do Licenciamento de Estabelecimentos Turísticos

- **Lei nº 85/VII/2010** - Código do Ordenamento do Território e Urbanismo

- **Decreto-Lei nº 43/2010** - Regulamento Nacional de Edificações Urbanas

- **Lei nº 41/VIII/2014** - Lei de Bases do Ambiente

#### Requisitos de Conformidade por Segmento

**Estabelecimentos Comerciais:**

- Licença de funcionamento municipal

- Certificado de segurança contra incêndios

- Alvará sanitário (quando aplicável)

- Certificado de conformidade urbanística

- Declaração de impacto ambiental (estabelecimentos específicos)

**Estabelecimentos Turísticos:**

- Licença de estabelecimento turístico (Ministério do Turismo)

- Certificado de classificação turística

- Licença de exploração de atividades turísticas

- Certificado de segurança e higiene

- Licença ambiental (quando aplicável)

**Estabelecimentos Industriais:**

- Licença industrial

- Estudo de impacto ambiental

- Licença de instalação

- Certificado de conformidade técnica

- Licença de exploração

### 10.2 Sistema de Validação Automática

#### Validação de Documentos

```java
@Service
public class LegalComplianceValidationService {

    public ComplianceResult validateEstablishmentCompliance(
            EstablishmentType type,
            List<Document> documents,
            Location location) {

        ComplianceResult result = new ComplianceResult();

        // Validação por segmento
        switch (type.getSegment()) {
            case COMERCIAL:
                result = validateCommercialCompliance(documents, location);
                break;
            case TURISTICO:
                result = validateTourismCompliance(documents, location);
                break;
            case INDUSTRIAL:
                result = validateIndustrialCompliance(documents, location);
                break;
        }

        // Validação de zoneamento
        result.addCheck(validateZoningCompliance(type, location));

        // Validação ambiental
        result.addCheck(validateEnvironmentalCompliance(type, location));

        return result;
    }
}
```

#### Integração com Órgãos Reguladores

```java
@Component
public class RegulatoryIntegrationService {

    @Autowired
    private MunicipalLicensingClient municipalClient;

    @Autowired
    private TourismMinistryClient tourismClient;

    @Autowired
    private EnvironmentalAgencyClient environmentalClient;

    public ValidationResult validateWithAuthorities(
            String establishmentId,
            EstablishmentType type) {

        ValidationResult result = new ValidationResult();

        // Validação municipal
        if (requiresMunicipalValidation(type)) {
            MunicipalValidation municipal = municipalClient
                .validateEstablishment(establishmentId);
            result.addValidation("MUNICIPAL", municipal);
        }

        // Validação turística
        if (type.getSegment() == EstablishmentSegment.TURISTICO) {
            TourismValidation tourism = tourismClient
                .validateTourismEstablishment(establishmentId);
            result.addValidation("TOURISM", tourism);
        }

        // Validação ambiental
        if (requiresEnvironmentalValidation(type)) {
            EnvironmentalValidation environmental = environmentalClient
                .validateEnvironmentalCompliance(establishmentId);
            result.addValidation("ENVIRONMENTAL", environmental);
        }

        return result;
    }
}
```

### 10.3 Auditoria e Controle

#### Sistema de Auditoria

```java
@Entity
@Table(name = "t_compliance_audit")
public class ComplianceAudit {

    @Id
    private UUID id;

    @Column(name = "establishment_id")
    private UUID establishmentId;

    @Column(name = "audit_type")
    @Enumerated(EnumType.STRING)
    private AuditType auditType; // INITIAL, PERIODIC, COMPLAINT, RENEWAL

    @Column(name = "audit_date")
    private LocalDateTime auditDate;

    @Column(name = "auditor_id")
    private UUID auditorId;

    @Column(name = "compliance_score")
    private BigDecimal complianceScore;

    @Column(name = "status")
    @Enumerated(EnumType.STRING)
    private ComplianceStatus status; // COMPLIANT, NON_COMPLIANT, CONDITIONAL

    @Column(name = "findings", columnDefinition = "TEXT")
    private String findings;

    @Column(name = "recommendations", columnDefinition = "TEXT")
    private String recommendations;

    @Column(name = "next_audit_date")
    private LocalDate nextAuditDate;
}
```

#### Workflow de Aprovação

```mermaid
graph TD
    A[Solicitação de Registro] --> B[Validação Documental]
    B --> C{Documentos Válidos?}
    C -->|Não| D[Solicitação de Correção]
    D --> A
    C -->|Sim| E[Validação de Localização]
    E --> F{Coordenadas Válidas?}
    F -->|Não| G[Correção de Georreferenciamento]
    G --> E
    F -->|Sim| H[Validação de Zoneamento]
    H --> I{Zona Adequada?}
    I -->|Não| J[Análise Especial]
    J --> K{Aprovação Especial?}
    K -->|Não| L[Rejeição]
    K -->|Sim| M[Aprovação Condicional]
    I -->|Sim| N[Validação Regulatória]
    N --> O{Conformidade Legal?}
    O -->|Não| P[Correções Regulatórias]
    P --> N
    O -->|Sim| Q[Inspeção Técnica]
    Q --> R{Inspeção Aprovada?}
    R -->|Não| S[Correções Técnicas]
    S --> Q
    R -->|Sim| T[Emissão de Número Matricial]
    T --> U[Estabelecimento Registrado]
    M --> V[Monitoramento Especial]
    L --> W[Processo Encerrado]
```

### 10.4 Integração com Sistema de Parametrização

#### Configuração de Parâmetros Legais

```java
@Entity
@Table(name = "t_legal_parameter")
public class LegalParameter {

    @Id
    private UUID id;

    @Column(name = "parameter_code")
    private String parameterCode; // ex: "TOURISM_MIN_AREA", "COMMERCIAL_SAFETY_REQ"

    @Column(name = "segment")
    @Enumerated(EnumType.STRING)
    private EstablishmentSegment segment;

    @Column(name = "category")
    private String category;

    @Column(name = "parameter_value")
    private String parameterValue;

    @Column(name = "data_type")
    @Enumerated(EnumType.STRING)
    private ParameterDataType dataType; // STRING, NUMBER, BOOLEAN, DATE

    @Column(name = "validation_rule")
    private String validationRule; // Regex ou regra de validação

    @Column(name = "legal_reference")
    private String legalReference; // Referência à lei ou decreto

    @Column(name = "effective_date")
    private LocalDate effectiveDate;

    @Column(name = "expiry_date")
    private LocalDate expiryDate;

    @Column(name = "is_mandatory")
    private Boolean isMandatory;
}
```

## 11. Considerações de Performance

### 10.1 Otimizações de Banco de Dados

```sql
-- Índices compostos para consultas frequentes
CREATE INDEX idx_licencas_status_vencimento ON licencas(status, data_vencimento);
CREATE INDEX idx_licencas_titular_status ON licencas(titular_id, status);

-- Índice para busca textual
CREATE INDEX idx_licencas_busca ON licencas USING gin(
  to_tsvector('portuguese', numero || ' ' || COALESCE(observacoes, ''))
);

-- Particionamento por ano para tabela de histórico
CREATE TABLE licencas_historico_2024 PARTITION OF licencas_historico
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

-- View materializada para dashboard
CREATE MATERIALIZED VIEW dashboard_resumo AS
SELECT
  COUNT(*) FILTER (WHERE status = 'ativa') as licencas_ativas,
  COUNT(*) FILTER (WHERE status = 'expirada') as licencas_expiradas,
  COUNT(*) FILTER (WHERE data_vencimento <= CURRENT_DATE + INTERVAL '30 days' AND status = 'ativa') as vencendo_30_dias
FROM licencas;

-- Refresh automático da view materializada
CREATE OR REPLACE FUNCTION refresh_dashboard_resumo()
RETURNS void AS $$
BEGIN
  REFRESH MATERIALIZED VIEW dashboard_resumo;
END;
$$ LANGUAGE plpgsql;

-- Trigger para refresh automático
CREATE OR REPLACE FUNCTION trigger_refresh_dashboard()
RETURNS trigger AS $$
BEGIN
  PERFORM pg_notify('refresh_dashboard', '');
  RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER licencas_changed
  AFTER INSERT OR UPDATE OR DELETE ON licencas
  FOR EACH STATEMENT
  EXECUTE FUNCTION trigger_refresh_dashboard();
```

### 10.2 Cache Strategy

```typescript
// src/cache/cache.service.ts
@Injectable()
export class CacheService {
  constructor(
    @Inject(CACHE_MANAGER) private cacheManager: Cache,
    private redisService: RedisService,
  ) {}

  // Cache de consultas frequentes
  async getLicencasCacheKey(filtros: FiltrosLicencaDto): string {
    const key = `licencas:${JSON.stringify(filtros)}`;
    return createHash('md5').update(key).digest('hex');
  }

  async cacheLicencas(filtros: FiltrosLicencaDto, dados: any, ttl = 300): Promise<void> {
    const key = await this.getLicencasCacheKey(filtros);
    await this.cacheManager.set(key, dados, ttl);
  }

  async getCachedLicencas(filtros: FiltrosLicencaDto): Promise<any> {
    const key = await this.getLicencasCacheKey(filtros);
    return this.cacheManager.get(key);
  }

  // Cache de dashboard com invalidação inteligente
  async cacheDashboard(dados: any): Promise<void> {
    await this.cacheManager.set('dashboard:resumo', dados, 600); // 10 minutos
  }

  async invalidateDashboard(): Promise<void> {
    await this.cacheManager.del('dashboard:resumo');
    // Invalidar caches relacionados
    const keys = await this.redisService.keys('licencas:*');
    if (keys.length > 0) {
      await this.redisService.del(...keys);
    }
  }
}
```

Esta arquitetura técnica fornece uma base sólida para implementação do sistema de gestão de licenças emitidas, com foco em performance, escalabilidade e manutenibilidade.
