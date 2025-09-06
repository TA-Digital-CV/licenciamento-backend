# PR00-BE-LIC - Parametrização Base - Gestão de Opções

## 1. Visão Geral

Este documento especifica a implementação da gestão de opções no sistema de licenciamento, que serve como base para parametrização de valores utilizados em todo o sistema, como tipos de setor, unidades de validade, modelos de licenciamento, entre outros.

**Módulo:** Parametrização Base  
**Componente:** Gestão de Opções  
**Versão:** 1.0  
**Data:** 2025

---

## 2. Arquitetura do Módulo

<<<<<<< HEAD
### 2.1 Estrutura de Pacotes
=======
### 1.2 Casos de Uso Principais

- **Consulta de Opções**: Obter lista de opções por código (ex: estados de licença)
- **Gestão Administrativa**: CRUD completo de parametrizações
- **Suporte Multilíngue**: Opções em diferentes idiomas
- **Cache Dinâmico**: Performance otimizada para consultas frequentes

## 2. Modelo de Dados

### 2.1 Estrutura da Tabela t_options

```sql
CREATE TABLE t_options (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ccode VARCHAR(50) NOT NULL,           -- Código do conjunto de opções
    ckey VARCHAR(50) NOT NULL,            -- Chave da opção específica
    cvalue VARCHAR(255) NOT NULL,         -- Valor/Label da opção
    locale VARCHAR(10) DEFAULT 'pt-CV',   -- Idioma (pt-CV, en, etc.)
    sort_order INTEGER,                   -- Ordem de apresentação
    active BOOLEAN DEFAULT TRUE,          -- Status ativo/inativo
    metadata JSONB,                       -- Metadados adicionais
    description TEXT,                     -- Descrição detalhada
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);

-- Índices para performance
CREATE UNIQUE INDEX idx_options_unique ON t_options(ccode, ckey, locale);
CREATE INDEX idx_options_ccode ON t_options(ccode);
CREATE INDEX idx_options_active ON t_options(active);
CREATE INDEX idx_options_sort ON t_options(ccode, sort_order NULLS LAST, ckey);
>>>>>>> parent of 2bd9194 (refactor(database): standardize timestamp column names to created_date and last_modified_date)
```
cv.igrp.license.configuration/
├── interfaces/rest/
│   └── OptionsController.java
├── application/
│   ├── dto/
│   │   ├── OptionRequestDTO.java
│   │   ├── OptionResponseDTO.java
│   │   └── WrapperListOptionsDTO.java
│   ├── commands/
│   │   ├── CreateOptionCommand.java
│   │   ├── UpdateOptionCommand.java
│   │   ├── EnableOptionCommand.java
│   │   └── DisableOptionCommand.java
│   └── queries/
│       ├── GetListOptionsQuery.java
│       ├── GetOptionByIdQuery.java
│       └── GetOptionsByCcodeQuery.java
└── domain/
    ├── models/Option.java
    ├── repository/OptionRepository.java
    └── service/OptionService.java
```

### 2.2 Padrão Arquitetural
- **CQRS (Command Query Responsibility Segregation)**
- **CommandBus** para operações de escrita
- **QueryBus** para operações de leitura
- **Repository Pattern** para acesso a dados

---

## 3. API REST - Endpoints

### 3.1 Listagem de Opções

**Endpoint:** `GET /api/v1/options`

**Descrição:** Lista opções com filtros e paginação

**Parâmetros de Query:**
| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| ccode | string | Não | - | Código da categoria |
| ckey | string | Não | - | Chave da opção |
| cvalue | string | Não | - | Valor da opção |
| locale | string | Não | - | Localização |
| sortOrder | integer | Não | - | Ordem de classificação |
| active | boolean | Não | true | Status ativo |
| pageNumber | string | Não | "0" | Número da página |
| pageSize | string | Não | "20" | Tamanho da página |

**Resposta de Sucesso (200):**
```json
{
  "pageNumber": 0,
  "pageSize": 20,
  "totalElements": 150,
  "totalPages": 8,
  "last": false,
  "first": true,
  "content": [
    {
      "id": "opt-001",
      "ccode": "VALIDITY_UNIT",
      "ckey": "MONTHS",
      "cvalue": "Meses",
      "locale": "pt_CV",
      "sort_order": 1
    }
  ]
}
```

### 3.2 Busca por ID

**Endpoint:** `GET /api/v1/options/{optionId}`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| optionId | string | ID único da opção |

**Resposta de Sucesso (200):**
```json
{
  "id": "opt-001",
  "ccode": "VALIDITY_UNIT",
  "ckey": "MONTHS",
  "cvalue": "Meses",
  "locale": "pt_CV",
  "sort_order": 1
}
```

### 3.3 Atualização de Opção

**Endpoint:** `PUT /api/v1/options/{optionId}`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| optionId | string | ID único da opção |

**Payload de Requisição:**
```json
{
  "ccode": "SECTOR_TYPE",
  "ckey": "COMMERCIAL",
  "cvalue": "Comercial Atualizado",
  "locale": "pt_CV",
  "sort_order": 1,
  "metadata": {
    "description": "Setor comercial atualizado",
    "icon": "commerce"
  }
}
```

**Resposta de Sucesso (200):**
```json
{
  "id": "opt-001",
  "ccode": "SECTOR_TYPE",
  "ckey": "COMMERCIAL",
  "cvalue": "Comercial Atualizado",
  "locale": "pt_CV",
  "sort_order": 1
}
```

### 3.4 Criação de Opção

**Endpoint:** `POST /api/v1/options`

**Payload de Requisição:**
```json
{
  "ccode": "SECTOR_TYPE",
  "ckey": "COMMERCIAL",
  "cvalue": "Comercial",
  "locale": "pt_CV",
  "sort_order": 1,
  "metadata": {
    "description": "Setor comercial",
    "icon": "commerce"
  }
}
```

**Resposta de Sucesso (200):**
```json
{
  "id": "opt-new-001",
  "ccode": "SECTOR_TYPE",
  "ckey": "COMMERCIAL",
  "cvalue": "Comercial",
  "locale": "pt_CV",
  "sort_order": 1
}
```

### 3.5 Ativação de Opção

**Endpoint:** `PATCH /api/v1/options/{optionId}/enable`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| optionId | string | ID único da opção |

**Resposta de Sucesso (200):**
```json
{
  "message": "Opção ativada com sucesso",
  "optionId": "opt-001",
  "status": "active"
}
```

### 3.6 Desativação de Opção

**Endpoint:** `PATCH /api/v1/options/{optionId}/disable`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| optionId | string | ID único da opção |

**Resposta de Sucesso (200):**
```json
{
  "message": "Opção desativada com sucesso",
  "optionId": "opt-001",
  "status": "inactive"
}
```

### 3.7 Busca por Código de Categoria

**Endpoint:** `GET /api/v1/options/{ccode}/findByCcode`

**Descrição:** Busca opções por código de categoria com formatação específica

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| ccode | string | Código da categoria |

**Parâmetros de Query:**
| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| locale | string | Não | - | Localização |
| includeInactive | boolean | Não | false | Incluir opções inativas |
| format | string | Não | "list" | Formato da resposta |

**Resposta de Sucesso (200):**
```json
{
  "ccode": "VALIDITY_UNIT",
  "options": [
    {
      "ckey": "DAYS",
      "cvalue": "Dias",
      "locale": "pt_CV",
      "active": true
    },
    {
      "ckey": "MONTHS",
      "cvalue": "Meses",
      "locale": "pt_CV",
      "active": true
    },
    {
      "ckey": "YEARS",
      "cvalue": "Anos",
      "locale": "pt_CV",
      "active": true
    }
  ]
}
```

### 3.8 Verificação de Existência por Código

**Endpoint:** `GET /api/v1/options/{ccode}/existsByCcode`

**Descrição:** Verifica se existe pelo menos uma opção para o código de categoria especificado

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| ccode | string | Código da categoria |

**Resposta de Sucesso (200):**
```json
true
```

**Resposta quando não existe (200):**
```json
false
```

---

## 4. Estruturas de Dados

### 4.1 OptionRequestDTO
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@IgrpDTO
public class OptionRequestDTO {
    @NotNull
    @NotBlank
    private String ccode;      // Código da categoria
    
    @NotNull
    @NotBlank
    private String ckey;       // Chave da opção
    
    @NotNull
    @NotBlank
    private String cvalue;     // Valor da opção
    
    private String locale;     // Localização (pt_CV, en_US, etc.)
    
    @Min(0)
    private Integer sort_order; // Ordem de classificação
    
    private Object metadata;   // Metadados adicionais
}
```

### 4.2 OptionResponseDTO
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@IgrpDTO
public class OptionResponseDTO {
    private String id;
    private String ccode;
    private String ckey;
    private String cvalue;
    private String locale;
    private Integer sort_order;
}
```

### 4.3 OptionCodeResponseDTO
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@IgrpDTO
public class OptionCodeResponseDTO {
    private String ccode;
    private List<OptionItem> options;
    
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public static class OptionItem {
        private String ckey;
        private String cvalue;
        private String locale;
        private boolean active;
    }
}
```

### 4.4 WrapperListOptionsDTO
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@IgrpDTO
public class WrapperListOptionsDTO {
    private int pageNumber;
    private int pageSize;
    private long totalElements;
    private int totalPages;
    private boolean last;
    private boolean first;
    private List<OptionResponseDTO> content;
}
```

---

## 5. Regras de Negócio

### 5.1 Validações

#### 5.1.1 Campos Obrigatórios
- `ccode`: Código da categoria (não nulo, não vazio)
- `ckey`: Chave da opção (não nulo, não vazio)
- `cvalue`: Valor da opção (não nulo, não vazio)

#### 5.1.2 Validações Específicas
- `ccode`: Deve seguir padrão UPPER_CASE com underscores
- `ckey`: Deve seguir padrão UPPER_CASE com underscores
- `sort_order`: Deve ser >= 0 se informado
- `locale`: Deve seguir padrão ISO (ex: pt_CV, en_US)

#### 5.1.3 Unicidade
- Combinação `ccode + ckey + locale` deve ser única
- Não pode haver duplicatas na mesma categoria e localização

### 5.2 Regras de Ativação/Desativação
- Opções desativadas não aparecem em listagens com `active=true`
- Opções em uso não podem ser desativadas (verificação de dependências)
- Histórico de ativação/desativação deve ser mantido

---

## 6. Categorias de Opções Padrão

### 6.1 VALIDITY_UNIT (Unidades de Validade)
```json
[
  {"ckey": "DAYS", "cvalue": "Dias"},
  {"ckey": "MONTHS", "cvalue": "Meses"},
  {"ckey": "YEARS", "cvalue": "Anos"}
]
```

### 6.2 SECTOR_TYPE (Tipos de Setor)
```json
[
  {"ckey": "COMMERCIAL", "cvalue": "Comercial"},
  {"ckey": "INDUSTRIAL", "cvalue": "Industrial"},
  {"ckey": "RESIDENTIAL", "cvalue": "Residencial"},
  {"ckey": "TOURISM", "cvalue": "Turismo"},
  {"ckey": "AGRICULTURE", "cvalue": "Agricultura"}
]
```

### 6.3 LICENSING_MODEL (Modelos de Licenciamento)
```json
[
  {"ckey": "STANDARD", "cvalue": "Padrão"},
  {"ckey": "SIMPLIFIED", "cvalue": "Simplificado"},
  {"ckey": "FAST_TRACK", "cvalue": "Via Rápida"},
  {"ckey": "PROVISIONAL", "cvalue": "Provisório"}
]
```

### 6.4 CURRENCY_CODE (Códigos de Moeda)
```json
[
  {"ckey": "CVE", "cvalue": "Escudo Cabo-verdiano"},
  {"ckey": "EUR", "cvalue": "Euro"},
  {"ckey": "USD", "cvalue": "Dólar Americano"}
]
```

### 6.5 FEE_TYPE (Tipos de Taxa)
```json
[
  {"ckey": "FIXED", "cvalue": "Taxa Fixa"},
  {"ckey": "PERCENTAGE", "cvalue": "Percentual"},
  {"ckey": "VARIABLE", "cvalue": "Variável"},
  {"ckey": "CALCULATED", "cvalue": "Calculada"}
]
```

---

## 7. Tratamento de Erros

### 7.1 Erros de Validação (400)
```json
{
  "type": "about:blank",
  "title": "Validation Errors",
  "status": 400,
  "errors": {
    "ccode": "Código da categoria é obrigatório",
    "ckey": "Chave da opção é obrigatória",
    "sort_order": "Ordem deve ser maior ou igual a 0"
  }
}
```

### 7.2 Erro de Duplicata (400)
```json
{
  "type": "about:blank",
  "title": "Constraint Violation Errors",
  "status": 400,
  "errors": {
    "uniqueConstraint": "Já existe uma opção com esta combinação de código, chave e localização"
  }
}
```

### 7.3 Opção Não Encontrada (404)
```json
{
  "type": "about:blank",
  "title": "Not Found",
  "status": 404,
  "detail": "Opção com ID 'opt-001' não encontrada"
}
```

---

## 8. Casos de Uso

### 8.1 UC001 - Listar Opções por Categoria
**Ator:** Administrador do Sistema  
**Objetivo:** Visualizar todas as opções de uma categoria específica

**Fluxo Principal:**
1. Administrador acessa a tela de opções
2. Seleciona uma categoria (ccode)
3. Sistema exibe lista de opções da categoria
4. Administrador pode filtrar por chave ou valor

### 8.2 UC002 - Criar Nova Opção
**Ator:** Administrador do Sistema  
**Objetivo:** Adicionar nova opção ao sistema

**Fluxo Principal:**
1. Administrador clica em "Nova Opção"
2. Preenche formulário com dados obrigatórios
3. Sistema valida dados
4. Sistema cria opção e retorna confirmação

**Fluxos Alternativos:**
- 3a. Dados inválidos: Sistema exibe erros de validação
- 3b. Opção duplicada: Sistema exibe erro de duplicata

### 8.3 UC003 - Ativar/Desativar Opção
**Ator:** Administrador do Sistema  
**Objetivo:** Controlar disponibilidade de opções

**Fluxo Principal:**
1. Administrador localiza opção na lista
2. Clica em ação de ativar/desativar
3. Sistema verifica dependências (se desativando)
4. Sistema atualiza status da opção

---

## 9. Considerações Técnicas

### 9.1 Performance
- Índices em `ccode`, `ckey` e `active` para otimizar consultas
- Cache de opções frequentemente utilizadas
- Paginação obrigatória para listas grandes

### 9.2 Segurança
- Validação de entrada em todos os endpoints
- Sanitização de dados de metadata
- Auditoria de alterações

### 9.3 Internacionalização
- Suporte a múltiplas localizações via campo `locale`
- Valores padrão em português (pt_CV)
- Possibilidade de tradução de valores

---

## 10. Dependências

### 10.1 Módulos Dependentes
- Gestão de Setores (usa SECTOR_TYPE)
- Gestão de Tipos de Licença (usa VALIDITY_UNIT, LICENSING_MODEL)
- Gestão de Taxas (usa FEE_TYPE, CURRENCY_CODE)
- Gestão de Parâmetros (usa VALIDITY_UNIT)

### 10.2 Tecnologias
- Spring Boot 3.x
- Jakarta Validation
- Swagger/OpenAPI 3
- Lombok
- SLF4J para logging

---

## 11. Roadmap

### 11.1 Versão Atual (1.0)
- CRUD básico de opções
- Filtros e paginação
- Ativação/desativação

### 11.2 Próximas Versões
- Importação/exportação em massa
- Versionamento de opções
- API de sincronização entre ambientes
- Interface de tradução automática

<<<<<<< HEAD
Este documento serve como referência completa para a implementação e manutenção do módulo de gestão de opções no sistema de licenciamento.
=======
    @Cacheable(key = "#code + '_' + #locale")
    public List<OptionResponse> getOptionsByCode(String code, String locale) {
        return optionRepository.findByCodeAndLocaleAndActiveTrue(code, locale);
    }

    @CacheEvict(key = "#code + '_' + #locale")
    public void evictCache(String code, String locale) {
        // Cache invalidation
    }

    @CacheEvict(allEntries = true)
    public void evictAllCache() {
        // Clear all cache
    }
}
```

### 7.2 Configuração de Cache

```yaml
# application.yml
spring:
  cache:
    type: caffeine
    caffeine:
      spec: maximumSize=1000,expireAfterWrite=1h
    cache-names:
      - options
```

## 8. Segurança

### 8.1 Controlo de Acesso

- **Consulta (GET)**: Acesso público (sem autenticação)
- **Gestão (POST/PUT/DELETE)**: Requer autenticação JWT + role ADMIN
- **Auditoria**: Todas as operações administrativas são auditadas

### 8.2 Configuração de Segurança

```java
@Configuration
@EnableWebSecurity
public class ParameterizationSecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.authorizeHttpRequests(authz -> authz
            .requestMatchers(HttpMethod.GET, "/api/v1/options/**").permitAll()
            .requestMatchers("/api/v1/options/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated()
        );
        return http.build();
    }
}
```

## 9. Testes

### 9.1 Testes Unitários

```java
@ExtendWith(MockitoExtension.class)
class GetOptionsByCodeUseCaseTest {

    @Mock
    private OptionRepositoryPort optionRepository;

    @Mock
    private OptionCachePort optionCache;

    @InjectMocks
    private GetOptionsByCodeUseCase useCase;

    @Test
    void shouldReturnOptionsWhenCodeExists() {
        // Given
        String code = "LICENSE_STATUS";
        String locale = "pt-CV";
        List<Option> options = createSampleOptions();

        when(optionCache.getOptionsByCode(code, locale))
            .thenReturn(Optional.empty());
        when(optionRepository.findByCodeAndLocaleAndActiveTrue(code, locale))
            .thenReturn(options);

        // When
        OptionSetResponse result = useCase.execute(code, locale, "list", false);

        // Then
        assertThat(result.getCode()).isEqualTo(code);
        assertThat(result.getItems()).hasSize(3);
        verify(optionCache).cacheOptions(code, locale, any());
    }
}
```

### 9.2 Testes de Integração

```java
@SpringBootTest
@Testcontainers
class ParameterizationControllerIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16")
            .withDatabaseName("licensing_test")
            .withUsername("test")
            .withPassword("test");

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldReturnOptionsWhenValidCodeProvided() {
        // Given
        String code = "LICENSE_STATUS";

        // When
        ResponseEntity<OptionSetResponse> response = restTemplate.getForEntity(
            "/api/v1/options/" + code, OptionSetResponse.class);

        // Then
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(response.getBody().getCode()).isEqualTo(code);
        assertThat(response.getBody().getItems()).isNotEmpty();
    }
}
```

## 10. Migração e Dados Iniciais

### 10.1 Script de Migração Flyway

```sql
-- V001__create_options_table.sql
CREATE TABLE t_options (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ccode VARCHAR(50) NOT NULL,
    ckey VARCHAR(50) NOT NULL,
    cvalue VARCHAR(255) NOT NULL,
    locale VARCHAR(10) DEFAULT 'pt-CV',
    sort_order INTEGER,
    active BOOLEAN DEFAULT TRUE,
    metadata JSONB,
    description TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);

CREATE UNIQUE INDEX idx_options_unique ON t_options(ccode, ckey, locale);
CREATE INDEX idx_options_ccode ON t_options(ccode);
CREATE INDEX idx_options_active ON t_options(active);
CREATE INDEX idx_options_sort ON t_options(ccode, sort_order NULLS LAST, ckey);
```

### 10.2 Dados Iniciais

```sql
-- V002__insert_initial_options.sql

-- Estados de Licenças
INSERT INTO t_options (ccode, ckey, cvalue, locale, sort_order, description) VALUES
('LICENSE_STATUS', 'DRAFT', 'Rascunho', 'pt-CV', 1, 'Licença em fase de preparação'),
('LICENSE_STATUS', 'PENDING', 'Pendente', 'pt-CV', 2, 'Licença submetida para análise'),
('LICENSE_STATUS', 'UNDER_REVIEW', 'Em Análise', 'pt-CV', 3, 'Licença em processo de avaliação'),
('LICENSE_STATUS', 'APPROVED', 'Aprovada', 'pt-CV', 4, 'Licença aprovada e ativa'),
('LICENSE_STATUS', 'REJECTED', 'Rejeitada', 'pt-CV', 5, 'Licença rejeitada'),
('LICENSE_STATUS', 'SUSPENDED', 'Suspensa', 'pt-CV', 6, 'Licença temporariamente suspensa'),
('LICENSE_STATUS', 'EXPIRED', 'Expirada', 'pt-CV', 7, 'Licença expirada'),
('LICENSE_STATUS', 'CANCELLED', 'Cancelada', 'pt-CV', 8, 'Licença cancelada pelo titular');

-- Tipos de Entidades Reguladoras
INSERT INTO t_options (ccode, ckey, cvalue, locale, sort_order, description) VALUES
('ENTITY_TYPE', 'MINISTRY', 'Ministério', 'pt-CV', 1, 'Ministério do governo'),
('ENTITY_TYPE', 'AGENCY', 'Agência', 'pt-CV', 2, 'Agência reguladora'),
('ENTITY_TYPE', 'INSPECTION', 'Inspeção', 'pt-CV', 3, 'Órgão de inspeção'),
('ENTITY_TYPE', 'MUNICIPALITY', 'Câmara Municipal', 'pt-CV', 4, 'Autoridade municipal'),
('ENTITY_TYPE', 'INSTITUTE', 'Instituto', 'pt-CV', 5, 'Instituto público');

-- Modelos de Licenciamento
INSERT INTO t_options (ccode, ckey, cvalue, locale, sort_order, description, metadata) VALUES
('LICENSING_MODEL', 'SIMPLE', 'Licenciamento Simples', 'pt-CV', 1, 'Processo simplificado', '{"duration_days": 30, "complexity": "low"}'),
('LICENSING_MODEL', 'STANDARD', 'Licenciamento Padrão', 'pt-CV', 2, 'Processo padrão', '{"duration_days": 60, "complexity": "medium"}'),
('LICENSING_MODEL', 'COMPLEX', 'Licenciamento Complexo', 'pt-CV', 3, 'Processo complexo', '{"duration_days": 90, "complexity": "high"}'),
('LICENSING_MODEL', 'AUTOMATIC', 'Licenciamento Automático', 'pt-CV', 4, 'Aprovação automática', '{"duration_days": 1, "complexity": "none"}');

-- Unidades de Validade
INSERT INTO t_options (ccode, ckey, cvalue, locale, sort_order, description) VALUES
('VALIDITY_UNIT', 'DAYS', 'Dias', 'pt-CV', 1, 'Validade em dias'),
('VALIDITY_UNIT', 'MONTHS', 'Meses', 'pt-CV', 2, 'Validade em meses'),
('VALIDITY_UNIT', 'YEARS', 'Anos', 'pt-CV', 3, 'Validade em anos'),
('VALIDITY_UNIT', 'INDEFINITE', 'Indefinida', 'pt-CV', 4, 'Sem prazo de validade');

-- Tipos de Infração
INSERT INTO t_options (ccode, ckey, cvalue, locale, sort_order, description) VALUES
('INFRACTION_TYPE', 'MINOR', 'Leve', 'pt-CV', 1, 'Infração de natureza leve'),
('INFRACTION_TYPE', 'SERIOUS', 'Grave', 'pt-CV', 2, 'Infração de natureza grave'),
('INFRACTION_TYPE', 'VERY_SERIOUS', 'Muito Grave', 'pt-CV', 3, 'Infração de natureza muito grave');
```

## 11. Monitorização e Métricas

### 11.1 Métricas de Performance

```java
@Component
public class ParameterizationMetrics {

    private final Counter optionRequestsCounter;
    private final Timer optionRequestsTimer;
    private final Gauge cacheHitRatio;

    public ParameterizationMetrics(MeterRegistry meterRegistry) {
        this.optionRequestsCounter = Counter.builder("options.requests.total")
            .description("Total number of option requests")
            .tag("module", "parameterization")
            .register(meterRegistry);

        this.optionRequestsTimer = Timer.builder("options.requests.duration")
            .description("Option request duration")
            .register(meterRegistry);

        this.cacheHitRatio = Gauge.builder("options.cache.hit.ratio")
            .description("Cache hit ratio for options")
            .register(meterRegistry, this, ParameterizationMetrics::getCacheHitRatio);
    }

    public void recordRequest(String code, String locale) {
        optionRequestsCounter.increment(
            Tags.of("code", code, "locale", locale)
        );
    }

    private double getCacheHitRatio() {
        // Implementar lógica de cálculo do cache hit ratio
        return 0.85; // Exemplo
    }
}
```

### 11.2 Health Checks

```java
@Component
public class ParameterizationHealthIndicator implements HealthIndicator {

    private final OptionRepository optionRepository;

    @Override
    public Health health() {
        try {
            long totalOptions = optionRepository.count();
            long activeOptions = optionRepository.countByActiveTrue();

            return Health.up()
                .withDetail("totalOptions", totalOptions)
                .withDetail("activeOptions", activeOptions)
                .withDetail("inactiveOptions", totalOptions - activeOptions)
                .build();
        } catch (Exception e) {
            return Health.down()
                .withDetail("error", e.getMessage())
                .build();
        }
    }
}
```

## 12. Documentação da API

### 12.1 Configuração OpenAPI

```java
@Configuration
@OpenAPIDefinition(
    info = @Info(
        title = "Parametrização Base API",
        version = "1.0",
        description = "API para gestão de parametrizações do sistema de licenciamento"
    )
)
public class ParameterizationOpenApiConfig {

    @Bean
    public GroupedOpenApi parameterizationApi() {
        return GroupedOpenApi.builder()
            .group("parameterization")
            .pathsToMatch("/api/v1/options/**")
            .build();
    }
}
```

### 12.2 Anotações nos Controllers

```java
@RestController
@RequestMapping("/api/v1/options")
@Tag(name = "Parametrização", description = "Gestão de opções e parametrizações do sistema")
public class ParameterizationController {

    @GetMapping("/{code}")
    @Operation(
        summary = "Obter opções por código",
        description = "Retorna todas as opções ativas para um código específico"
    )
    @ApiResponses(value = {
        @ApiResponse(responseCode = "200", description = "Opções encontradas"),
        @ApiResponse(responseCode = "404", description = "Código não encontrado")
    })
    public ResponseEntity<OptionSetResponse> getOptionsByCode(
        @Parameter(description = "Código do conjunto de opções", example = "LICENSE_STATUS")
        @PathVariable String code,

        @Parameter(description = "Idioma das opções", example = "pt-CV")
        @RequestParam(defaultValue = "pt-CV") String locale,

        @Parameter(description = "Formato da resposta", example = "list")
        @RequestParam(defaultValue = "list") String format
    ) {
        // Implementação
    }
}
```

## 13. Considerações de Implementação

### 13.1 Fases de Desenvolvimento

1. **Fase 1** (1 semana): Modelo de dados e migração
2. **Fase 2** (1 semana): Domain layer e use cases
3. **Fase 3** (1 semana): APIs REST e cache
4. **Fase 4** (0.5 semana): Testes e documentação
5. **Fase 5** (0.5 semana): Integração e deploy

### 13.2 Riscos e Mitigações

| Risco                              | Impacto | Mitigação                              |
| ---------------------------------- | ------- | -------------------------------------- |
| Performance de consultas           | Alto    | Implementar cache eficiente            |
| Crescimento descontrolado de dados | Médio   | Implementar soft delete e arquivamento |
| Inconsistência de dados            | Alto    | Validações rigorosas e transações      |
| Falha de cache                     | Médio   | Fallback para base de dados            |

### 13.3 Métricas de Sucesso

- **Performance**: Tempo de resposta < 100ms para consultas em cache
- **Disponibilidade**: 99.9% uptime
- **Cache Hit Ratio**: > 80%
- **Cobertura de Testes**: > 90%
- **Tempo de Deploy**: < 5 minutos

## 14. Conclusão

O módulo de Parametrização Base fornece uma solução robusta e flexível para gestão de opções dinâmicas no sistema de licenciamento. A arquitetura DDD garante separação de responsabilidades, enquanto o cache otimiza a performance. A API REST bem documentada facilita a integração com o frontend e outros sistemas.

A implementação seguirá as melhores práticas de desenvolvimento, incluindo testes automatizados, monitorização e documentação completa, garantindo um módulo confiável e de fácil manutenção.
>>>>>>> parent of 2bd9194 (refactor(database): standardize timestamp column names to created_date and last_modified_date)
