# PR01-BE-LIC - Parametrização de Licenciamento - Setores, Categorias e Tipos de Licenças

## 1. Visão Geral

Este documento especifica a implementação dos módulos principais de parametrização do sistema de licenciamento, abrangendo a gestão hierárquica de setores, categorias e tipos de licenças. Estes módulos formam a base estrutural para todo o sistema de licenciamento.

**Módulo:** Parametrização de Licenciamento  
**Componentes:** Setores, Categorias, Tipos de Licenças  
**Versão:** 1.0  
**Data:** 2025

---

## 2. Arquitetura Geral

<<<<<<< HEAD
### 2.1 Hierarquia de Dados
```
Setor (Comercial, Industrial, etc.)
└── Categoria (Restauração, Comércio Geral, etc.)
    └── Tipo de Licença (Licença de Restaurante, Licença de Loja, etc.)
        ├── Parâmetros de Licença
        ├── Legislações Aplicáveis
        ├── Entidades Envolvidas
        ├── Tipos de Processo
        └── Taxas Associadas
=======
### 1.2 Entidades Principais

- **T_SECTOR**: Setores económicos (Agricultura, Turismo, Indústria, etc.)
- **T_CATEGORY**: Categorias hierárquicas de atividades económicas
- **T_LICENSE_TYPE**: Tipos de licenças com configurações específicas

## 2. Modelo de Dados

### 2.1 Tabela T_SECTOR

```sql
CREATE TABLE t_sector (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    sector_type_key VARCHAR(50) NOT NULL,  -- Referência para t_options (SECTOR_TYPE)
    code VARCHAR(20) UNIQUE NOT NULL,      -- Código único do setor
    active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER,
    metadata JSONB,                        -- Configurações específicas
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);

-- Índices
CREATE INDEX idx_sector_type ON t_sector(sector_type_key);
CREATE INDEX idx_sector_active ON t_sector(active);
CREATE INDEX idx_sector_code ON t_sector(code);
CREATE INDEX idx_sector_sort ON t_sector(sort_order NULLS LAST);
>>>>>>> parent of 2bd9194 (refactor(database): standardize timestamp column names to created_date and last_modified_date)
```

### 2.2 Relacionamentos
- **Setor 1:N Categoria**: Um setor pode ter múltiplas categorias
- **Categoria 1:N Tipo de Licença**: Uma categoria pode ter múltiplos tipos de licença
- **Categoria 1:N Categoria**: Categorias podem ter subcategorias (hierarquia)

<<<<<<< HEAD
---
=======
```sql
CREATE TABLE t_category (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    code VARCHAR(30) UNIQUE NOT NULL,      -- Código hierárquico (ex: AGR.001, AGR.001.001)
    parent_id UUID REFERENCES t_category(id),
    sector_id UUID NOT NULL REFERENCES t_sector(id),
    level INTEGER NOT NULL DEFAULT 1,      -- Nível hierárquico (1=raiz, 2=sub, etc.)
    path VARCHAR(500),                     -- Caminho hierárquico completo
    active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);
>>>>>>> parent of 2bd9194 (refactor(database): standardize timestamp column names to created_date and last_modified_date)

## 3. Módulo de Setores

### 3.1 API REST - Setores

#### 3.1.1 Listagem de Setores
**Endpoint:** `GET /api/v1/sectors`

<<<<<<< HEAD
**Parâmetros de Query:**
| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| sectorType | string | Não | - | Tipo do setor |
| name | string | Não | - | Nome do setor ou termo de busca por descrição |
| code | string | Não | - | Código do setor |
| active | boolean | Não | true | Status ativo/inativo |
| pageNumber | string | Não | "0" | Número da página |
| pageSize | string | Não | "20" | Tamanho da página |
| sort | string | Não | "name" | Campo de ordenação (ex: name, code, createdAt) |
| direction | string | Não | "ASC" | Direção da ordenação (ASC/DESC) |
=======
    -- Configurações de Licenciamento
    licensing_model_key VARCHAR(50) NOT NULL,  -- Referência para t_options (LICENSING_MODEL)
    validity_period INTEGER,                   -- Período de validade
    validity_unit_key VARCHAR(50),            -- Referência para t_options (VALIDITY_UNIT)
    renewable BOOLEAN DEFAULT TRUE,
    auto_renewal BOOLEAN DEFAULT FALSE,

    -- Configurações de Processo
    requires_inspection BOOLEAN DEFAULT FALSE,
    requires_public_consultation BOOLEAN DEFAULT FALSE,
    max_processing_days INTEGER,

    -- Configurações Financeiras
    has_fees BOOLEAN DEFAULT TRUE,
    base_fee DECIMAL(10,2),
    currency_code VARCHAR(3) DEFAULT 'CVE',

    -- Status e Metadados
    active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER,
    metadata JSONB,                           -- Configurações específicas adicionais

    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);

-- Índices
CREATE INDEX idx_license_type_category ON t_license_type(category_id);
CREATE INDEX idx_license_type_model ON t_license_type(licensing_model_key);
CREATE INDEX idx_license_type_active ON t_license_type(active);
CREATE INDEX idx_license_type_code ON t_license_type(code);
CREATE INDEX idx_license_type_renewable ON t_license_type(renewable);
```

### 2.4 Dados Iniciais para Tipos de Setores

```sql
-- Inserir tipos de setores no sistema de opções
INSERT INTO t_options (ccode, ckey, cvalue, locale, sort_order, description) VALUES
('SECTOR_TYPE', 'PRIMARY', 'Setor Primário', 'pt-CV', 1, 'Agricultura, Pesca, Mineração'),
('SECTOR_TYPE', 'SECONDARY', 'Setor Secundário', 'pt-CV', 2, 'Indústria, Construção, Manufatura'),
('SECTOR_TYPE', 'TERTIARY', 'Setor Terciário', 'pt-CV', 3, 'Serviços, Comércio, Turismo'),
('SECTOR_TYPE', 'QUATERNARY', 'Setor Quaternário', 'pt-CV', 4, 'Tecnologia, Investigação, Consultoria');
```

## 3. Arquitetura DDD

### 3.1 Estrutura de Camadas

```
src/main/java/cv/gov/licensing/
├── domain/
│   ├── sector/
│   │   ├── Sector.java                    # Aggregate Root
│   │   ├── SectorId.java                  # Value Object
│   │   ├── SectorCode.java                # Value Object
│   │   ├── SectorType.java                # Value Object
│   │   ├── SectorRepository.java          # Repository Interface
│   │   └── SectorDomainService.java
│   ├── category/
│   │   ├── Category.java                  # Aggregate Root
│   │   ├── CategoryId.java                # Value Object
│   │   ├── CategoryCode.java              # Value Object
│   │   ├── CategoryPath.java              # Value Object
│   │   ├── CategoryRepository.java        # Repository Interface
│   │   └── CategoryDomainService.java
│   └── licensetype/
│       ├── LicenseType.java               # Aggregate Root
│       ├── LicenseTypeId.java             # Value Object
│       ├── LicenseTypeCode.java           # Value Object
│       ├── LicensingConfiguration.java    # Value Object
│       ├── FinancialConfiguration.java    # Value Object
│       ├── LicenseTypeRepository.java     # Repository Interface
│       └── LicenseTypeDomainService.java
├── application/
│   ├── sector/
│   │   ├── usecase/
│   │   │   ├── CreateSectorUseCase.java
│   │   │   ├── UpdateSectorUseCase.java
│   │   │   ├── DeleteSectorUseCase.java
│   │   │   ├── GetSectorUseCase.java
│   │   │   └── ListSectorsUseCase.java
│   │   └── service/
│   │       └── SectorApplicationService.java
│   ├── category/
│   │   ├── usecase/
│   │   │   ├── CreateCategoryUseCase.java
│   │   │   ├── UpdateCategoryUseCase.java
│   │   │   ├── DeleteCategoryUseCase.java
│   │   │   ├── GetCategoryTreeUseCase.java
│   │   │   └── MoveCategoryUseCase.java
│   │   └── service/
│   │       └── CategoryApplicationService.java
│   └── licensetype/
│       ├── usecase/
│       │   ├── CreateLicenseTypeUseCase.java
│       │   ├── UpdateLicenseTypeUseCase.java
│       │   ├── DeleteLicenseTypeUseCase.java
│       │   ├── GetLicenseTypeUseCase.java
│       │   └── ListLicenseTypesByCategoryUseCase.java
│       └── service/
│           └── LicenseTypeApplicationService.java
├── interfaces/
│   └── rest/
│       ├── SectorController.java
│       ├── CategoryController.java
│       ├── LicenseTypeController.java
│       └── dto/
│           ├── SectorResponse.java
│           ├── CategoryResponse.java
│           ├── LicenseTypeResponse.java
│           ├── CreateSectorRequest.java
│           ├── CreateCategoryRequest.java
│           └── CreateLicenseTypeRequest.java
└── infrastructure/
    ├── persistence/
    │   ├── SectorJpaEntity.java
    │   ├── CategoryJpaEntity.java
    │   ├── LicenseTypeJpaEntity.java
    │   ├── SectorJpaRepository.java
    │   ├── CategoryJpaRepository.java
    │   ├── LicenseTypeJpaRepository.java
    │   ├── SectorRepositoryAdapter.java
    │   ├── CategoryRepositoryAdapter.java
    │   └── LicenseTypeRepositoryAdapter.java
    └── cache/
        ├── SectorCacheService.java
        ├── CategoryCacheService.java
        └── LicenseTypeCacheService.java
```

### 3.2 Domain Layer - Entidades Principais

#### 3.2.1 Sector Aggregate

```java
@Entity
public class Sector {
    private SectorId id;
    private Name name;
    private Description description;
    private SectorCode code;
    private SectorType sectorType;
    private Boolean active;
    private Integer sortOrder;
    private Map<String, Object> metadata;
    private AuditInfo auditInfo;

    // Factory Method
    public static Sector create(Name name, SectorCode code, SectorType sectorType) {
        var sector = new Sector();
        sector.id = SectorId.generate();
        sector.name = Objects.requireNonNull(name, "Nome é obrigatório");
        sector.code = Objects.requireNonNull(code, "Código é obrigatório");
        sector.sectorType = Objects.requireNonNull(sectorType, "Tipo de setor é obrigatório");
        sector.active = true;
        sector.auditInfo = AuditInfo.create();
        return sector;
    }

    // Business Methods
    public void updateName(Name newName) {
        this.name = Objects.requireNonNull(newName, "Nome é obrigatório");
        this.auditInfo = this.auditInfo.update();
    }

    public void changeSectorType(SectorType newType) {
        this.sectorType = Objects.requireNonNull(newType, "Tipo de setor é obrigatório");
        this.auditInfo = this.auditInfo.update();
    }

    public void deactivate() {
        this.active = false;
        this.auditInfo = this.auditInfo.update();
    }
}
```

#### 3.2.2 Category Aggregate

```java
@Entity
public class Category {
    private CategoryId id;
    private Name name;
    private Description description;
    private CategoryCode code;
    private CategoryId parentId;
    private SectorId sectorId;
    private Integer level;
    private CategoryPath path;
    private Boolean active;
    private Integer sortOrder;
    private Map<String, Object> metadata;
    private AuditInfo auditInfo;

    // Factory Methods
    public static Category createRoot(Name name, CategoryCode code, SectorId sectorId) {
        var category = new Category();
        category.id = CategoryId.generate();
        category.name = Objects.requireNonNull(name, "Nome é obrigatório");
        category.code = Objects.requireNonNull(code, "Código é obrigatório");
        category.sectorId = Objects.requireNonNull(sectorId, "Setor é obrigatório");
        category.level = 1;
        category.path = CategoryPath.root(code);
        category.active = true;
        category.auditInfo = AuditInfo.create();
        return category;
    }

    public static Category createChild(Name name, CategoryCode code,
                                     Category parent, SectorId sectorId) {
        var category = new Category();
        category.id = CategoryId.generate();
        category.name = Objects.requireNonNull(name, "Nome é obrigatório");
        category.code = Objects.requireNonNull(code, "Código é obrigatório");
        category.parentId = parent.getId();
        category.sectorId = Objects.requireNonNull(sectorId, "Setor é obrigatório");
        category.level = parent.getLevel() + 1;
        category.path = parent.getPath().append(code);
        category.active = true;
        category.auditInfo = AuditInfo.create();
        return category;
    }

    // Business Methods
    public boolean isRoot() {
        return parentId == null;
    }

    public boolean canHaveChildren() {
        return level < 5; // Máximo 5 níveis
    }

    public void moveTo(Category newParent) {
        if (newParent != null && !newParent.canHaveChildren()) {
            throw new BusinessRuleException("Categoria pai não pode ter mais filhos");
        }

        this.parentId = newParent != null ? newParent.getId() : null;
        this.level = newParent != null ? newParent.getLevel() + 1 : 1;
        this.path = newParent != null ? newParent.getPath().append(this.code)
                                     : CategoryPath.root(this.code);
        this.auditInfo = this.auditInfo.update();
    }
}
```

#### 3.2.3 LicenseType Aggregate

```java
@Entity
public class LicenseType {
    private LicenseTypeId id;
    private Name name;
    private Description description;
    private LicenseTypeCode code;
    private CategoryId categoryId;
    private LicensingConfiguration licensingConfig;
    private FinancialConfiguration financialConfig;
    private Boolean active;
    private Integer sortOrder;
    private Map<String, Object> metadata;
    private AuditInfo auditInfo;

    // Factory Method
    public static LicenseType create(Name name, LicenseTypeCode code,
                                   CategoryId categoryId,
                                   LicensingConfiguration licensingConfig) {
        var licenseType = new LicenseType();
        licenseType.id = LicenseTypeId.generate();
        licenseType.name = Objects.requireNonNull(name, "Nome é obrigatório");
        licenseType.code = Objects.requireNonNull(code, "Código é obrigatório");
        licenseType.categoryId = Objects.requireNonNull(categoryId, "Categoria é obrigatória");
        licenseType.licensingConfig = Objects.requireNonNull(licensingConfig,
                                                            "Configuração é obrigatória");
        licenseType.active = true;
        licenseType.auditInfo = AuditInfo.create();
        return licenseType;
    }

    // Business Methods
    public void updateLicensingConfiguration(LicensingConfiguration newConfig) {
        this.licensingConfig = Objects.requireNonNull(newConfig,
                                                     "Configuração é obrigatória");
        this.auditInfo = this.auditInfo.update();
    }

    public void updateFinancialConfiguration(FinancialConfiguration newConfig) {
        this.financialConfig = newConfig;
        this.auditInfo = this.auditInfo.update();
    }

    public boolean isRenewable() {
        return licensingConfig.isRenewable();
    }

    public boolean requiresInspection() {
        return licensingConfig.requiresInspection();
    }
}
```

## 4. APIs REST

### 4.1 Sector Controller

#### GET /api/v1/sectors

Listar todos os setores.

**Parâmetros:**

- `sectorType` (query, opcional): Filtrar por tipo de setor
- `active` (query, opcional): Filtrar por status (default: true)
- `page` (query, opcional): Página (default: 0)
- `size` (query, opcional): Tamanho da página (default: 20)
- `sort` (query, opcional): Ordenação (ex: name,asc)

**Resposta:**
>>>>>>> parent of 2bd9194 (refactor(database): standardize timestamp column names to created_date and last_modified_date)

**Resposta de Sucesso (200):**
```json
{
  "pageNumber": 0,
  "pageSize": 20,
  "totalElements": 25,
  "totalPages": 2,
  "last": false,
  "first": true,
  "content": [
    {
      "id": "sec-001",
      "name": "Comercial",
      "description": "Setor de atividades comerciais",
      "code": "COM",
      "sectorType": "COMMERCIAL",
      "active": true,
      "sortOrder": 1,
      "metadata": {
        "icon": "store",
        "color": "#2563eb"
      }
    }
  ]
}
```

#### 3.1.2 Busca de Setor por ID
**Endpoint:** `GET /api/v1/sectors/{sectorId}`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------||
| sectorId | string | ID único do setor |

**Resposta de Sucesso (200):**
```json
{
  "id": "sec-001",
  "name": "Comercial",
  "description": "Setor de atividades comerciais",
  "code": "COM",
  "sectorType": "COMMERCIAL",
  "active": true,
  "sortOrder": 1,
  "metadata": {
    "icon": "store",
    "color": "#2563eb"
  }
}
```

**Códigos de Status:**
- 200: Setor encontrado
- 404: Setor não encontrado
- 500: Erro interno do servidor

#### 3.1.3 Criação de Setor
**Endpoint:** `POST /api/v1/sectors`

**Payload de Requisição:**
```json
{
  "name": "Industrial",
  "description": "Setor de atividades industriais",
  "sectorTypeKey": "INDUSTRIAL",
  "code": "IND",
  "sortOrder": 2,
  "metadata": {
    "icon": "factory",
    "color": "#dc2626",
    "requiresEnvironmentalLicense": true
  }
}
```

**Validações:**
- `name`: Obrigatório, máximo 100 caracteres
- `description`: Opcional, máximo 500 caracteres
- `code`: Obrigatório, único, máximo 10 caracteres
- `sectorTypeKey`: Obrigatório, deve existir nas opções

**Resposta de Sucesso (201):**
```json
{
  "id": "sec-002",
  "name": "Industrial",
  "description": "Setor de atividades industriais",
  "code": "IND",
  "sectorType": "INDUSTRIAL",
  "active": true,
  "sortOrder": 2,
  "metadata": {
    "icon": "factory",
    "color": "#dc2626",
    "requiresEnvironmentalLicense": true
  }
}
```

**Códigos de Status:**
- 201: Setor criado com sucesso
- 400: Dados inválidos
- 409: Código já existe
- 500: Erro interno do servidor

#### 3.1.4 Atualização de Setor
**Endpoint:** `PUT /api/v1/sectors/{sectorId}`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------||
| sectorId | string | ID único do setor |

**Payload de Requisição:** (mesmo formato da criação)

**Códigos de Status:**
- 200: Setor atualizado com sucesso
- 400: Dados inválidos
- 404: Setor não encontrado
- 409: Código já existe
- 500: Erro interno do servidor

#### 3.1.5 Estrutura de Dados - Setor
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@IgrpDTO
public class SectorRequestDTO {
    @NotNull
    @NotBlank
    @Size(min = 2, max = 100)
    private String name;
    
    @Size(max = 500)
    private String description;
    
    @NotNull
    @NotBlank
    private String sectorTypeKey;  // Referência para OPTIONS com ccode="SECTOR_TYPE"
    
    @NotNull
    @NotBlank
    @Pattern(regexp = "^[A-Z0-9_]+$")
    private String code;
    
    @Min(0)
    private Integer sortOrder;
    
    private Object metadata;
}
```

---

## 4. Módulo de Categorias

### 4.1 API REST - Categorias

#### 4.1.1 Listagem Hierárquica de Categorias
**Endpoint:** `GET /api/v1/categories`

**Parâmetros de Query:**
| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| sectorId | string | Não | - | Filtro por setor |
| parentId | string | Não | - | Filtro por categoria pai |
| level | integer | Não | - | Nível hierárquico |
| name | string | Não | - | Nome da categoria |
| code | string | Não | - | Código da categoria |
| pageNumber | string | Não | "0" | Número da página |
| pageSize | string | Não | "20" | Tamanho da página |
| sort | string | Não | "name" | Campo de ordenação (name, code, createdAt) |
| direction | string | Não | "ASC" | Direção da ordenação (ASC/DESC) |

**Resposta de Sucesso (200):**
```json
{
  "pageNumber": 0,
  "pageSize": 20,
  "totalElements": 45,
  "totalPages": 3,
  "content": [
    {
      "id": "cat-001",
      "code": "REST",
      "description": "Atividades de restauração e bebidas",
      "name": "Restauração",
      "sectorId": "sec-001",
      "sectorName": "Comercial",
      "level": 1,
      "path": "/Comercial/Restauração",
      "children": [
        {
          "id": "cat-002",
          "code": "REST_BAR",
          "name": "Bares e Cafés",
          "level": 2,
          "path": "/Comercial/Restauração/Bares e Cafés",
          "children": []
        }
      ],
      "metadata": {
        "requiresHealthLicense": true,
        "inspectionFrequency": "quarterly"
      }
    }
  ]
}
```

#### 4.1.2 Busca de Categoria por ID
**Endpoint:** `GET /api/v1/categories/{categoryId}`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| categoryId | string | ID único da categoria |

**Resposta de Sucesso (200):**
```json
{
  "id": "cat-001",
  "name": "Restauração",
  "description": "Atividades de restauração e bebidas",
  "code": "REST",
  "sectorId": "sec-001",
  "sectorName": "Comercial",
  "active": true,
  "sortOrder": 1,
  "createdAt": "2025-01-15T10:30:00Z",
  "updatedAt": "2025-01-15T10:30:00Z"
}
```

#### 4.1.3 Criação de Categoria
**Endpoint:** `POST /api/v1/categories`

**Payload de Requisição:**
```json
{
  "name": "Hotelaria",
  "description": "Atividades hoteleiras e alojamento",
  "code": "HOT",
  "sectorId": "sec-001",
  "active": true,
  "sortOrder": 2
}
```

**Validações:**
- `name`: Obrigatório, máximo 100 caracteres
- `description`: Opcional, máximo 500 caracteres
- `code`: Obrigatório, único, máximo 10 caracteres
- `sectorId`: Obrigatório, deve existir

**Resposta de Sucesso (201):**
```json
{
  "id": "cat-002",
  "name": "Hotelaria",
  "description": "Atividades hoteleiras e alojamento",
  "code": "HOT",
  "sectorId": "sec-001",
  "sectorName": "Comercial",
  "active": true,
  "sortOrder": 2,
  "createdAt": "2025-01-15T11:00:00Z",
  "updatedAt": "2025-01-15T11:00:00Z"
}
```

#### 4.1.4 Atualização de Categoria
**Endpoint:** `PUT /api/v1/categories/{categoryId}`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| categoryId | string | ID único da categoria |

**Payload de Requisição:** (mesmo formato da criação)

<<<<<<< HEAD
**Códigos de Status:**
- 200: Categoria atualizada com sucesso
- 400: Dados inválidos
- 404: Categoria não encontrada
- 409: Código já existe
- 500: Erro interno do servidor

#### 4.1.5 Movimentação de Categoria
**Endpoint:** `POST /api/v1/categories/{categoryId}/move`

**Payload de Requisição:**
```json
{
  "newParentId": "cat-003",
  "newSectorId": "sec-002",
  "newSortOrder": 5
=======
-- Criar tabela de setores
CREATE TABLE t_sector (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    sector_type_key VARCHAR(50) NOT NULL,
    code VARCHAR(20) UNIQUE NOT NULL,
    active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);

-- Criar tabela de categorias
CREATE TABLE t_category (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    code VARCHAR(30) UNIQUE NOT NULL,
    parent_id UUID REFERENCES t_category(id),
    sector_id UUID NOT NULL REFERENCES t_sector(id),
    level INTEGER NOT NULL DEFAULT 1,
    path VARCHAR(500),
    active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);

-- Criar tabela de tipos de licenças
CREATE TABLE t_license_type (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    code VARCHAR(30) UNIQUE NOT NULL,
    category_id UUID NOT NULL REFERENCES t_category(id),
    licensing_model_key VARCHAR(50) NOT NULL,
    validity_period INTEGER,
    validity_unit_key VARCHAR(50),
    renewable BOOLEAN DEFAULT TRUE,
    auto_renewal BOOLEAN DEFAULT FALSE,
    requires_inspection BOOLEAN DEFAULT FALSE,
    requires_public_consultation BOOLEAN DEFAULT FALSE,
    max_processing_days INTEGER,
    has_fees BOOLEAN DEFAULT TRUE,
    base_fee DECIMAL(10,2),
    currency_code VARCHAR(3) DEFAULT 'CVE',
    active BOOLEAN DEFAULT TRUE,
    sort_order INTEGER,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(100),
    updated_by VARCHAR(100)
);

-- Criar índices
CREATE INDEX idx_sector_type ON t_sector(sector_type_key);
CREATE INDEX idx_sector_active ON t_sector(active);
CREATE INDEX idx_sector_code ON t_sector(code);
CREATE INDEX idx_sector_sort ON t_sector(sort_order NULLS LAST);

CREATE INDEX idx_category_parent ON t_category(parent_id);
CREATE INDEX idx_category_sector ON t_category(sector_id);
CREATE INDEX idx_category_level ON t_category(level);
CREATE INDEX idx_category_path ON t_category USING GIN(to_tsvector('portuguese', path));
CREATE INDEX idx_category_active ON t_category(active);
CREATE INDEX idx_category_code ON t_category(code);

CREATE INDEX idx_license_type_category ON t_license_type(category_id);
CREATE INDEX idx_license_type_model ON t_license_type(licensing_model_key);
CREATE INDEX idx_license_type_active ON t_license_type(active);
CREATE INDEX idx_license_type_code ON t_license_type(code);
CREATE INDEX idx_license_type_renewable ON t_license_type(renewable);
```

### 9.2 Dados Iniciais

```sql
-- V004__insert_licensing_initial_data.sql

-- Inserir tipos de setores
INSERT INTO t_options (ccode, ckey, cvalue, locale, sort_order, description) VALUES
('SECTOR_TYPE', 'PRIMARY', 'Setor Primário', 'pt-CV', 1, 'Agricultura, Pesca, Mineração'),
('SECTOR_TYPE', 'SECONDARY', 'Setor Secundário', 'pt-CV', 2, 'Indústria, Construção, Manufatura'),
('SECTOR_TYPE', 'TERTIARY', 'Setor Terciário', 'pt-CV', 3, 'Serviços, Comércio, Turismo'),
('SECTOR_TYPE', 'QUATERNARY', 'Setor Quaternário', 'pt-CV', 4, 'Tecnologia, Investigação, Consultoria');

-- Inserir setores iniciais
INSERT INTO t_sector (name, description, sector_type_key, code, sort_order) VALUES
('Agricultura', 'Setor agrícola e pecuário', 'PRIMARY', 'AGR', 1),
('Pesca', 'Setor pesqueiro e aquacultura', 'PRIMARY', 'PES', 2),
('Indústria', 'Setor industrial e manufatureiro', 'SECONDARY', 'IND', 3),
('Construção', 'Setor da construção civil', 'SECONDARY', 'CON', 4),
('Turismo', 'Setor de turismo e hotelaria', 'TERTIARY', 'TUR', 5),
('Comércio', 'Setor comercial e distribuição', 'TERTIARY', 'COM', 6),
('Tecnologia', 'Setor de tecnologia e inovação', 'QUATERNARY', 'TEC', 7);

-- Inserir categorias iniciais (exemplos)
INSERT INTO t_category (name, description, code, sector_id, level, path, sort_order)
SELECT
    'Agricultura Familiar',
    'Atividades de agricultura familiar',
    'AGR.001',
    s.id,
    1,
    'AGR.001',
    1
FROM t_sector s WHERE s.code = 'AGR';

INSERT INTO t_category (name, description, code, sector_id, level, path, sort_order)
SELECT
    'Hotelaria',
    'Atividades hoteleiras e alojamento',
    'TUR.001',
    s.id,
    1,
    'TUR.001',
    1
FROM t_sector s WHERE s.code = 'TUR';

-- Inserir tipos de licenças iniciais (exemplos)
INSERT INTO t_license_type (
    name, description, code, category_id, licensing_model_key,
    validity_period, validity_unit_key, renewable, requires_inspection,
    max_processing_days, has_fees, base_fee
)
SELECT
    'Licença de Exploração Agrícola',
    'Licença para exploração de atividades agrícolas familiares',
    'LEA-001',
    c.id,
    'STANDARD',
    24,
    'MONTHS',
    true,
    true,
    60,
    true,
    15000.00
FROM t_category c
JOIN t_sector s ON c.sector_id = s.id
WHERE s.code = 'AGR' AND c.code = 'AGR.001';

INSERT INTO t_license_type (
    name, description, code, category_id, licensing_model_key,
    validity_period, validity_unit_key, renewable, requires_inspection,
    max_processing_days, has_fees, base_fee
)
SELECT
    'Licença de Estabelecimento Hoteleiro',
    'Licença para funcionamento de estabelecimentos hoteleiros',
    'LEH-001',
    c.id,
    'COMPLEX',
    36,
    'MONTHS',
    true,
    true,
    90,
    true,
    50000.00
FROM t_category c
JOIN t_sector s ON c.sector_id = s.id
WHERE s.code = 'TUR' AND c.code = 'TUR.001';
```

## 10. Monitorização e Métricas

### 10.1 Métricas de Performance

```java
@Component
public class LicensingMetrics {

    private final Counter sectorRequestsCounter;
    private final Counter categoryRequestsCounter;
    private final Counter licenseTypeRequestsCounter;
    private final Timer sectorResponseTimer;
    private final Gauge activeSectorsGauge;

    public LicensingMetrics(MeterRegistry meterRegistry, SectorRepository sectorRepository) {
        this.sectorRequestsCounter = Counter.builder("licensing.sectors.requests.total")
            .description("Total number of sector requests")
            .register(meterRegistry);

        this.categoryRequestsCounter = Counter.builder("licensing.categories.requests.total")
            .description("Total number of category requests")
            .register(meterRegistry);

        this.licenseTypeRequestsCounter = Counter.builder("licensing.license_types.requests.total")
            .description("Total number of license type requests")
            .register(meterRegistry);

        this.sectorResponseTimer = Timer.builder("licensing.sectors.response.time")
            .description("Sector response time")
            .register(meterRegistry);

        this.activeSectorsGauge = Gauge.builder("licensing.sectors.active.count")
            .description("Number of active sectors")
            .register(meterRegistry, this, LicensingMetrics::getActiveSectorsCount);
    }

    public void incrementSectorRequests() {
        sectorRequestsCounter.increment();
    }

    public Timer.Sample startSectorTimer() {
        return Timer.start(sectorResponseTimer);
    }

    private double getActiveSectorsCount() {
        return sectorRepository.countByActiveTrue();
    }
>>>>>>> parent of 2bd9194 (refactor(database): standardize timestamp column names to created_date and last_modified_date)
}
```

#### 4.1.6 Estrutura de Dados - Categoria
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@IgrpDTO
public class CategoryRequestDTO {
    @NotNull
    @NotBlank
    @Size(min = 2, max = 100)
    private String name;
    
    @Size(max = 500)
    private String description;
    
    @NotNull
    @NotBlank
    @Pattern(regexp = "^[A-Z0-9_]+$")
    private String code;
    
    private String parentId;  // Opcional para subcategorias
    
    @NotNull
    @NotBlank
    private String sectorId;
    
    @Min(0)
    private Integer sortOrder;
    
    private Object metadata;
}
```

---

## 5. Módulo de Tipos de Licença

### 5.1 API REST - Tipos de Licença

#### 5.1.1 Listagem de Tipos de Licença
**Endpoint:** `GET /api/v1/license-types`

**Parâmetros de Query:**
| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| categoryId | string | Não | - | Filtro por categoria |
| licensingModel | string | Não | - | Modelo de licenciamento |
| active | boolean | Não | true | Status ativo |
| renewable | boolean | Não | - | Licenças renováveis |
| name | string | Não | - | Nome do tipo |
| code | string | Não | - | Código do tipo |
| pageNumber | string | Não | "0" | Número da página |
| pageSize | string | Não | "20" | Tamanho da página |

**Resposta de Sucesso (200):**
```json
{
  "pageNumber": 0,
  "pageSize": 20,
  "totalElements": 120,
  "totalPages": 6,
  "content": [
    {
      "id": "lt-001",
      "name": "Licença de Restaurante",
      "description": "Licença para exploração de restaurante",
      "code": "LIC_REST_001",
      "categoryId": "cat-001",
      "categoryName": "Restauração",
      "licensingModelKey": "STANDARD",
      "validityPeriod": 12,
      "validityUnitKey": "MONTHS",
      "renewable": true,
      "autoRenewal": false,
      "requiresInspection": true,
      "requiresPublicConsultation": false,
      "maxProcessingDays": 30,
      "hasFees": true,
      "baseFee": 15000.00,
      "currencyCode": "CVE",
      "metadata": {
        "minimumArea": 50,
        "maxCapacity": 100,
        "requiresFireSafety": true
      }
    }
  ]
}
```

#### 5.1.2 Criação de Tipo de Licença
**Endpoint:** `POST /api/v1/license-types`

**Payload de Requisição:**
```json
{
  "name": "Licença de Café",
  "description": "Licença para exploração de café/bar",
  "code": "LIC_CAFE_001",
  "categoryId": "cat-002",
  "licensingModelKey": "SIMPLIFIED",
  "validityPeriod": 24,
  "validityUnitKey": "MONTHS",
  "renewable": true,
  "autoRenewal": true,
  "requiresInspection": false,
  "requiresPublicConsultation": false,
  "maxProcessingDays": 15,
  "hasFees": true,
  "baseFee": 8000.00,
  "currencyCode": "CVE",
  "metadata": {
    "minimumArea": 25,
    "maxCapacity": 50,
    "allowsMusic": true
  }
}
```

#### 5.1.3 Ativação/Desativação
**Endpoints:**
- `PATCH /api/v1/license-types/{licenseTypeId}/ativar`
- `PATCH /api/v1/license-types/{licenseTypeId}/desativar`

**Resposta de Sucesso (200):**
```json
{
  "message": "Tipo de licença ativado com sucesso",
  "licenseTypeId": "lt-001",
  "status": "active",
  "timestamp": "2025-01-15T10:30:00Z"
}
```

#### 5.1.4 Estrutura de Dados - Tipo de Licença
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@IgrpDTO
public class LicenseTypeRequestDTO {
    @NotNull
    @NotBlank
    @Size(min = 2, max = 200)
    private String name;
    
    @Size(max = 1000)
    private String description;
    
    @NotNull
    @NotBlank
    @Pattern(regexp = "^[A-Z0-9_]+$")
    private String code;
    
    @NotNull
    @NotBlank
    private String categoryId;
    
    @NotNull
    @NotBlank
    private String licensingModelKey;  // Referência para OPTIONS
    
    @NotNull
    @Min(1)
    private Integer validityPeriod;
    
    @NotNull
    @NotBlank
    private String validityUnitKey;    // Referência para OPTIONS
    
    @NotNull
    private Boolean renewable;
    
    @NotNull
    private Boolean autoRenewal;
    
    @NotNull
    private Boolean requiresInspection;
    
    @NotNull
    private Boolean requiresPublicConsultation;
    
    @Min(1)
    private Integer maxProcessingDays;
    
    @NotNull
    private Boolean hasFees;
    
    @DecimalMin("0.0")
    private BigDecimal baseFee;
    
    private String currencyCode;       // Referência para OPTIONS
    
    private Object metadata;
}
```

---

## 6. Regras de Negócio

### 6.1 Regras de Setor

#### 6.1.1 Validações
- Nome do setor deve ser único
- Código do setor deve ser único e seguir padrão UPPER_CASE
- Tipo de setor deve existir nas opções (SECTOR_TYPE)
- Setor não pode ser excluído se tiver categorias associadas

#### 6.1.2 Comportamentos
- Ao desativar setor, todas as categorias ficam inacessíveis
- Ordenação automática por sortOrder, depois por nome

### 6.2 Regras de Categoria

#### 6.2.1 Validações
- Nome da categoria deve ser único dentro do mesmo setor
- Código da categoria deve ser único globalmente
- Categoria pai deve pertencer ao mesmo setor
- Não pode haver referência circular na hierarquia
- Máximo de 5 níveis hierárquicos

#### 6.2.2 Comportamentos
- Ao mover categoria, todas as subcategorias são movidas junto
- Ao excluir categoria, subcategorias podem ser reparentizadas ou excluídas
- Path é calculado automaticamente baseado na hierarquia

### 6.3 Regras de Tipo de Licença

#### 6.3.1 Validações
- Nome deve ser único dentro da mesma categoria
- Código deve ser único globalmente
- Categoria deve existir e estar ativa
- Modelo de licenciamento deve existir nas opções
- Unidade de validade deve existir nas opções
- Se hasFees=true, baseFee deve ser informada
- Se autoRenewal=true, renewable deve ser true

#### 6.3.2 Comportamentos
- Tipos inativos não aparecem em formulários de solicitação
- Alterações em tipos ativos afetam apenas novas solicitações
- Histórico de alterações deve ser mantido

---

## 7. Casos de Uso Integrados

### 7.1 UC001 - Configurar Nova Área de Negócio
**Objetivo:** Configurar estrutura completa para nova área de negócio

**Fluxo:**
1. Criar setor (ex: "Turismo")
2. Criar categorias principais (ex: "Hotelaria", "Agências de Viagem")
3. Criar subcategorias se necessário (ex: "Hotéis", "Pousadas")
4. Criar tipos de licença para cada categoria
5. Configurar parâmetros específicos de cada tipo

### 7.2 UC002 - Reorganizar Estrutura Existente
**Objetivo:** Reestruturar hierarquia de categorias

**Fluxo:**
1. Identificar categorias a serem movidas
2. Verificar impactos nos tipos de licença
3. Executar movimentação via API
4. Validar nova estrutura
5. Comunicar mudanças aos usuários

### 7.3 UC003 - Desativar Área de Negócio
**Objetivo:** Descontinuar área de negócio mantendo histórico

**Fluxo:**
1. Desativar tipos de licença da área
2. Desativar categorias relacionadas
3. Desativar setor
4. Manter dados para consulta histórica
5. Redirecionar solicitações pendentes

---

## 8. Integrações e Dependências

### 8.1 Dependências de Módulos
- **Opções (Options)**: Para tipos de setor, modelos de licenciamento, unidades de validade
- **Parâmetros de Licença**: Configurações específicas por tipo
- **Legislações**: Legislações aplicáveis por tipo
- **Entidades**: Entidades envolvidas no processo
- **Taxas**: Estrutura de taxas por tipo

### 8.2 Impactos em Outros Módulos
- **Solicitações de Licença**: Dependem da estrutura de tipos
- **Processos**: Utilizam configurações dos tipos
- **Relatórios**: Agrupam dados por setor/categoria
- **Dashboard**: Exibe estatísticas por estrutura

---

## 9. Considerações Técnicas

### 9.1 Performance
- Índices em campos de busca frequente (code, name, sectorId, categoryId)
- Cache de estrutura hierárquica de categorias
- Paginação obrigatória em listagens
- Lazy loading de subcategorias em interfaces

### 9.2 Segurança
- Validação rigorosa de hierarquias para evitar loops
- Auditoria completa de alterações estruturais
- Controle de acesso por perfil de usuário
- Backup automático antes de alterações críticas

### 9.3 Manutenibilidade
- Versionamento de estruturas
- Scripts de migração para alterações
- Documentação automática de dependências
- Testes automatizados de integridade

---

## 10. Monitoramento e Métricas

### 10.1 Métricas de Uso
- Número de setores/categorias/tipos ativos
- Frequência de uso por tipo de licença
- Tempo médio de processamento por tipo
- Taxa de renovação por tipo

### 10.2 Alertas
- Tipos de licença sem uso por período prolongado
- Inconsistências na hierarquia
- Falhas em operações críticas
- Performance degradada em consultas

---

## 11. Roadmap

### 11.1 Versão Atual (1.0)
- CRUD completo de setores, categorias e tipos
- Hierarquia de categorias
- Ativação/desativação
- Validações básicas

### 11.2 Próximas Versões
- Importação/exportação em massa
- Versionamento de configurações
- Templates de configuração
- API de sincronização entre ambientes
- Interface de configuração visual (drag & drop)
- Análise de impacto de mudanças
- Sugestões automáticas de otimização

Este documento estabelece a base para a implementação dos módulos principais de parametrização, garantindo flexibilidade e escalabilidade do sistema de licenciamento.