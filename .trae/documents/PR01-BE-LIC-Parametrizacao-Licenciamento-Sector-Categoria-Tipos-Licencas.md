# PR01-BE-LIC - Parametrização de Licenciamento - Setores, Categorias e Tipos de Licenças

## 1. Visão Geral

Este documento especifica a implementação dos módulos principais de parametrização do sistema de licenciamento, abrangendo a gestão hierárquica de setores, categorias e tipos de licenças. Estes módulos formam a base estrutural para todo o sistema de licenciamento.

**Módulo:** Parametrização de Licenciamento  
**Componentes:** Setores, Categorias, Tipos de Licenças  
**Versão:** 1.0  
**Data:** 2024

---

## 2. Arquitetura Geral

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
```

### 2.2 Relacionamentos
- **Setor 1:N Categoria**: Um setor pode ter múltiplas categorias
- **Categoria 1:N Tipo de Licença**: Uma categoria pode ter múltiplos tipos de licença
- **Categoria 1:N Categoria**: Categorias podem ter subcategorias (hierarquia)

---

## 3. Módulo de Setores

### 3.1 API REST - Setores

#### 3.1.1 Listagem de Setores
**Endpoint:** `GET /api/v1/sectors`

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
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-15T10:30:00Z"
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
  "createdAt": "2024-01-15T11:00:00Z",
  "updatedAt": "2024-01-15T11:00:00Z"
}
```

#### 4.1.4 Atualização de Categoria
**Endpoint:** `PUT /api/v1/categories/{categoryId}`

**Parâmetros de Path:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| categoryId | string | ID único da categoria |

**Payload de Requisição:** (mesmo formato da criação)

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
  "timestamp": "2024-01-15T10:30:00Z"
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