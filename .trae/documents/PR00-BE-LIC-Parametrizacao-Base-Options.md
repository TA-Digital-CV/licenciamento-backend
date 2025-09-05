# PR00-BE-LIC - Parametrização Base - Gestão de Opções

## 1. Visão Geral

Este documento especifica a implementação da gestão de opções no sistema de licenciamento, que serve como base para parametrização de valores utilizados em todo o sistema, como tipos de setor, unidades de validade, modelos de licenciamento, entre outros.

**Módulo:** Parametrização Base  
**Componente:** Gestão de Opções  
**Versão:** 1.0  
**Data:** 2025

---

## 2. Arquitetura do Módulo

### 2.1 Estrutura de Pacotes
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

Este documento serve como referência completa para a implementação e manutenção do módulo de gestão de opções no sistema de licenciamento.