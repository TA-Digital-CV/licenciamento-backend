# Documentação Técnica da API de Licenciamento

## 1. Visão Geral

A API de Licenciamento é um sistema backend desenvolvido em Java Spring Boot que gerencia tipos de licença, parâmetros, legislações, entidades, categorias, setores, opções e taxas. A API segue padrões REST e utiliza arquitetura CQRS (Command Query Responsibility Segregation) com CommandBus e QueryBus.

**Base URL:** `http://localhost:8083`
**Versão da API:** v1
**Prefixo:** `/api/v1`

---

## 2. Endpoints Consolidados

### 2.1 License Types (Tipos de Licença)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/license-types` | Lista tipos de licença com filtros |
| GET | `/api/v1/license-types/{licenseTypeId}` | Busca tipo de licença por ID |
| POST | `/api/v1/license-types` | Cria novo tipo de licença |
| PUT | `/api/v1/license-types/{licenseTypeId}` | Atualiza tipo de licença |
| PATCH | `/api/v1/license-types/{licenseTypeId}/ativar` | Ativa tipo de licença |
| PATCH | `/api/v1/license-types/{licenseTypeId}/desativar` | Desativa tipo de licença |

### 2.2 Options (Opções)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/options` | Lista opções com filtros |
| GET | `/api/v1/options/{optionId}` | Busca opção por ID |
| GET | `/api/v1/options/{ccode}` | Busca opções por código |
| GET | `/api/v1/options/{ccode}/existsByCcode` | Verifica existência por código |
| POST | `/api/v1/options` | Cria nova opção |
| PUT | `/api/v1/options/{optionId}` | Atualiza opção |
| PATCH | `/api/v1/options/{optionId}/enable` | Ativa opção |
| PATCH | `/api/v1/options/{optionId}/disable` | Desativa opção |

### 2.3 Sectors (Setores)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/sectors` | Lista setores com filtros |
| GET | `/api/v1/sectors/{sectorId}` | Busca setor por ID |
| POST | `/api/v1/sectors` | Cria novo setor |
| PUT | `/api/v1/sectors/{sectorId}` | Atualiza setor |

### 2.4 Categories (Categorias)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/categories` | Lista categorias com filtros |
| GET | `/api/v1/categories/{categoryId}` | Busca categoria por ID |
| POST | `/api/v1/categories` | Cria nova categoria |
| PUT | `/api/v1/categories/{categoryId}` | Atualiza categoria |
| POST | `/api/v1/categories/{categoryId}/move` | Move categoria |

### 2.5 License Parameters (Parâmetros de Licença)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/license-parameters` | Lista parâmetros com filtros |
| GET | `/api/v1/license-parameters/{licenseParameterId}` | Busca parâmetro por ID |
| POST | `/api/v1/license-parameters` | Cria novo parâmetro |
| PUT | `/api/v1/license-parameters/{licenseParameterId}` | Atualiza parâmetro |
| PATCH | `/api/v1/license-parameters/{licenseParameterId}/enable` | Ativa parâmetro |
| PUT | `/api/v1/license-parameters/{licenseParameterId}/disable` | Desativa parâmetro |

### 2.6 Legislations (Legislações)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/legislations` | Lista legislações com filtros |
| GET | `/api/v1/legislations/{legislationId}` | Busca legislação por ID |
| POST | `/api/v1/legislations` | Cria nova legislação |
| PUT | `/api/v1/legislations/{legislationId}` | Atualiza legislação |
| PATCH | `/api/v1/legislations/{legislationId}/enable` | Ativa legislação |
| PUT | `/api/v1/legislations/{legislationId}/disable` | Desativa legislação |

### 2.7 License Process Types (Tipos de Processo de Licença)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/license-types/{licenseTypeId}/process-type` | Lista tipos de processo por licença |
| GET | `/api/v1/license-types/{licenseTypeId}/process-types/{licenseProcessTypeId}` | Busca tipo de processo específico |
| POST | `/api/v1/license-types/{licenseTypeId}/process-types` | Cria tipo de processo |
| PUT | `/api/v1/license-types/{licenseTypeId}/process-types/{licenseProcessTypeId}` | Atualiza tipo de processo |
| PATCH | `/api/v1/license-types/{licenseTypeId}/process-types/{licenseProcessTypeId}/disable` | Desativa tipo de processo |

### 2.8 Process Type Fees (Taxas por Tipo de Processo)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/process-type-fees` | Lista taxas com filtros |
| GET | `/api/v1/process-type-fees/{process_type_fee_id}` | Busca taxa por ID |
| POST | `/api/v1/process-type-fees` | Cria nova taxa |
| POST | `/api/v1/process-type-fees/calculate` | Calcula taxas |
| PUT | `/api/v1/process-type-fees/{process_type_fee_id}` | Atualiza taxa |
| PATCH | `/api/v1/process-type-fees/{process_type_fee_id}/enable` | Ativa taxa |
| PATCH | `/api/v1/process-type-fees/{process_type_fee_id}/disable` | Desativa taxa |

### 2.9 Fee Categories (Categorias de Taxa)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/fee-categories` | Lista categorias de taxa |
| GET | `/api/v1/fee-categories/{feeCategoryId}` | Busca categoria de taxa por ID |
| POST | `/api/v1/fee-categories` | Cria categoria de taxa |
| PUT | `/api/v1/fee-categories/{feeCategoryId}` | Atualiza categoria de taxa |
| PATCH | `/api/v1/fee-categories/{feeCategoryId}/enable` | Ativa categoria |
| PATCH | `/api/v1/fee-categories/{feeCategoryId}/disable` | Desativa categoria |

### 2.10 Entities (Entidades)

| Método | Endpoint | Descrição |
|--------|----------|----------|
| GET | `/api/v1/entities` | Lista entidades com filtros |
| GET | `/api/v1/entities/{entitiyId}` | Busca entidade por ID |
| POST | `/api/v1/entities` | Cria nova entidade |
| PUT | `/api/v1/entities/{entitiyId}` | Atualiza entidade |

---

## 3. Estruturas de Payloads

### 3.1 License Type (Tipo de Licença)

#### Request DTO (LicenseTypeRequestDTO)
```json
{
  "name": "string",
  "description": "string",
  "code": "string",
  "categoryId": "string",
  "licensingModelKey": "string",
  "validityPeriod": "integer",
  "validityUnitKey": "string",
  "renewable": "boolean",
  "autoRenewal": "boolean",
  "requiresInspection": "boolean",
  "requiresPublicConsultation": "boolean",
  "maxProcessingDays": "integer",
  "hasFees": "boolean",
  "baseFee": "number",
  "currencyCode": "string",
  "metadata": {}
}
```

#### Response DTO (LicenseTypeResponseDTO)
```json
{
  "id": "string",
  "name": "string",
  "description": "string",
  "code": "string",
  "categoryId": "string",
  "categoryName": "string",
  "licensingModelKey": "string",
  "validityPeriod": "integer",
  "validityUnitKey": "string",
  "renewable": "boolean",
  "autoRenewal": "boolean",
  "requiresInspection": "boolean",
  "requiresPublicConsultation": "boolean",
  "maxProcessingDays": "integer",
  "hasFees": "boolean",
  "baseFee": "number",
  "currencyCode": "string",
  "metadata": {}
}
```

### 3.2 Option (Opção)

#### Request DTO (OptionRequestDTO)
```json
{
  "ccode": "string",
  "ckey": "string",
  "cvalue": "string",
  "locale": "string",
  "sort_order": "integer",
  "metadata": {}
}
```

#### Response DTO (OptionResponseDTO)
```json
{
  "id": "string",
  "ccode": "string",
  "ckey": "string",
  "cvalue": "string",
  "locale": "string",
  "sort_order": "integer"
}
```

### 3.3 Sector (Setor)

#### Request DTO (SectorRequestDTO)
```json
{
  "name": "string",
  "description": "string",
  "sectorTypeKey": "string",
  "code": "string",
  "sortOrder": "integer",
  "metadata": {}
}
```

#### Response DTO (SectorResponseDTO)
```json
{
  "id": "string",
  "name": "string",
  "description": "string",
  "code": "string",
  "sectorType": "string",
  "active": "boolean",
  "sortOrder": "integer",
  "metadata": {}
}
```

### 3.4 License Parameter (Parâmetro de Licença)

#### Request DTO (LicenseParameterRequestDTO)
```json
{
  "licenseTypeId": "string",
  "validityUnit": "string",
  "validityPeriod": "integer",
  "model": "string",
  "provisionalValidity": "integer",
  "definitiveLicenseValidity": "integer",
  "provisionalDefaultPeriod": "integer",
  "definitiveDefaultPeriod": "integer",
  "provisionalRenewalPeriod": "integer",
  "maxProvisonalRenewal": "integer",
  "definitiveRenewalPeriod": "integer",
  "definitiveRenewalDefaultPeriod": "integer",
  "renewalDefaultPeriod": "integer",
  "maxRenewalPeriod": "integer",
  "vitalityFlag": "boolean"
}
```

### 3.5 Wrapper Lists (Listas Paginadas)

Todas as listas seguem o padrão de wrapper com paginação:

```json
{
  "pageNumber": "integer",
  "pageSize": "integer",
  "totalElements": "integer",
  "totalPages": "integer",
  "last": "boolean",
  "first": "boolean",
  "content": []
}
```

---

## 4. Códigos de Status e Respostas

### 4.1 Códigos de Sucesso

| Código | Descrição | Uso |
|--------|-----------|-----|
| 200 | OK | Operações GET, PUT, PATCH bem-sucedidas |
| 201 | Created | Recursos criados com POST (implícito) |

### 4.2 Códigos de Erro

| Código | Descrição | Cenário | Handler |
|--------|-----------|----------|----------|
| 400 | Bad Request | Dados de entrada inválidos, validação falhou | GlobalExceptionHandler |
| 404 | Not Found | Recurso não encontrado | IgrpResponseStatusException |
| 500 | Internal Server Error | Erro interno do servidor, ClassCastException | GlobalExceptionHandler |

### 4.3 Estrutura de Resposta de Erro

A API utiliza o padrão **ProblemDetail** (RFC 7807) para respostas de erro:

#### 4.3.1 Erro de Validação (400)
```json
{
  "type": "about:blank",
  "title": "Validation Errors",
  "status": 400,
  "errors": {
    "fieldName": "Error message",
    "anotherField": "Another error message"
  }
}
```

#### 4.3.2 Erro de Constraint Violation (400)
```json
{
  "type": "about:blank",
  "title": "Constraint Violation Errors",
  "status": 400,
  "errors": {
    "propertyPath": "Constraint violation message"
  }
}
```

#### 4.3.3 Erro de Formato Enum (400)
```json
{
  "type": "about:blank",
  "title": "Invalid value for enum type: EnumName",
  "status": 400,
  "CurrentValue": "invalid_value",
  "AllowedValues": ["VALUE1", "VALUE2", "VALUE3"]
}
```

#### 4.3.4 Erro de JSON Malformado (400)
```json
{
  "type": "about:blank",
  "title": "Malformed JSON request",
  "status": 400,
  "detail": "Detailed error message"
}
```

#### 4.3.5 Erro Interno do Servidor (500)
```json
{
  "type": "about:blank",
  "title": "Internal Server Error",
  "status": 500
}
```

---

## 5. Parâmetros de Query

### 5.1 Parâmetros de Paginação (Padrão)

| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| pageNumber | string | Não | "0" | Número da página |
| pageSize | string | Não | "20" | Tamanho da página |

### 5.2 License Types - Parâmetros de Filtro

| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| categoryId | string | Não | - | Filtro por categoria |
| licensingModel | string | Não | - | Filtro por modelo de licenciamento |
| active | boolean | Não | true | Filtro por status ativo |
| renewable | boolean | Não | - | Filtro por renovável |
| name | string | Não | - | Filtro por nome |
| code | string | Não | - | Filtro por código |

### 5.3 Options - Parâmetros de Filtro

| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| ccode | string | Não | - | Código da categoria |
| ckey | string | Não | - | Chave da opção |
| cvalue | string | Não | - | Valor da opção |
| locale | string | Não | - | Localização |
| sortOrder | integer | Não | - | Ordem de classificação |
| active | boolean | Não | true | Status ativo |

### 5.4 Sectors - Parâmetros de Filtro

| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|-----------|------|-------------|--------|-----------|
| sectorType | string | Não | - | Tipo do setor |
| name | string | Não | - | Nome do setor |
| code | string | Não | - | Código do setor |
| active | boolean | Não | true | Status ativo |

---

## 6. Regras de Validação

### 6.1 Validações Gerais

- Todos os endpoints POST e PUT utilizam `@Valid` para validação automática
- DTOs utilizam anotações Jakarta Validation (Bean Validation 3.0)
- Campos obrigatórios são validados automaticamente
- Tratamento de erros centralizado via `GlobalExceptionHandler`
- Validação de tipos enum com mensagens específicas
- Validação de constraints personalizadas

### 6.2 License Type - Regras de Validação

#### Campos Obrigatórios:
- `name`: Nome do tipo de licença (String, não nulo)
- `code`: Código único do tipo (String, não nulo, único)
- `categoryId`: Referência à categoria (String, não nulo, deve existir)
- `licensingModelKey`: Chave do modelo de licenciamento (String, não nulo)
- `validityPeriod`: Período de validade (Integer, positivo)
- `validityUnitKey`: Unidade de tempo da validade (String, não nulo)

#### Campos Opcionais com Validação:
- `baseFee`: Taxa base (Number, >= 0 se informado)
- `maxProcessingDays`: Dias máximos de processamento (Integer, > 0 se informado)
- `currencyCode`: Código da moeda (String, formato ISO 4217 se informado)

### 6.3 Option - Regras de Validação

#### Campos Obrigatórios:
- `ccode`: Código da categoria (String, não nulo)
- `ckey`: Chave da opção (String, não nulo)
- `cvalue`: Valor da opção (String, não nulo)

#### Campos Opcionais:
- `locale`: Localização (String, formato locale válido)
- `sort_order`: Ordem de classificação (Integer)

### 6.4 Sector - Regras de Validação

#### Campos Obrigatórios:
- `name`: Nome do setor (String, não nulo)
- `sectorTypeKey`: Tipo do setor (String, não nulo)
- `code`: Código do setor (String, não nulo, único)

### 6.5 Category - Regras de Validação

#### Campos Obrigatórios:
- `name`: Nome da categoria (String, não nulo)
- `sectorId`: ID do setor (String, não nulo, deve existir)

#### Campos Opcionais:
- `parentId`: ID da categoria pai (String, deve existir se informado)
- `sortOrder`: Ordem de classificação (Integer)

### 6.6 Process Type Fee - Regras de Validação

#### Campos Obrigatórios:
- `licenseTypeProcessTypeId`: ID do tipo de processo (String, não nulo)
- `feeCategoryId`: ID da categoria de taxa (String, não nulo)
- `name`: Nome da taxa (String, não nulo)
- `feeType`: Tipo da taxa (String, não nulo)
- `baseAmount`: Valor base (Number, >= 0)
- `currencyCode`: Código da moeda (String, não nulo)

#### Campos Opcionais com Validação:
- `minimumAmount`: Valor mínimo (Number, >= 0 se informado)
- `maximumAmount`: Valor máximo (Number, >= minimumAmount se informado)
- `validityDays`: Dias de validade (Integer, > 0 se informado)

---

## 7. Dependências e Requisitos Técnicos

### 7.1 Framework e Tecnologias

- **Java Spring Boot**: Framework principal
- **Spring Web**: Para endpoints REST
- **Jakarta Validation**: Validação de dados
- **Swagger/OpenAPI 3**: Documentação da API
- **Lombok**: Redução de boilerplate
- **SLF4J**: Logging

### 7.2 Arquitetura

- **CQRS Pattern**: Separação entre Commands e Queries
- **CommandBus**: Processamento de comandos (CREATE, UPDATE, DELETE)
- **QueryBus**: Processamento de consultas (GET)
- **DTO Pattern**: Transferência de dados entre camadas

### 7.3 Anotações Personalizadas

- `@IgrpController`: Controlador personalizado do framework iGRP
- `@IgrpDTO`: DTO personalizado do framework iGRP

### 7.4 Estrutura de Pacotes

```
cv.igrp.license.configuration/
├── interfaces/rest/          # Controladores REST
├── application/
│   ├── dto/                  # Data Transfer Objects
│   ├── commands/             # Comandos CQRS
│   └── queries/              # Consultas CQRS
├── domain/                   # Lógica de domínio
└── infrastructure/           # Infraestrutura e persistência
```

---

## 8. Considerações de Segurança

### 8.1 Configuração de Segurança

- Classe `SecurityConfig.java` presente no projeto
- Configurações específicas não detalhadas no escopo atual

### 8.2 Auditoria

- `AuditEntity.java` e `ApplicationAuditorAware.java` indicam suporte a auditoria
- Rastreamento automático de criação e modificação de entidades

---

## 9. Observações Importantes

### 9.1 Geração Automática

- Código gerado automaticamente pelo iGRP Studio
- **IMPORTANTE**: Não modificar arquivos gerados, pois podem ser sobrescritos

### 9.2 Logging

- Todos os controladores implementam logging de debug
- Logs de início e fim de operação em cada endpoint

### 9.3 Tratamento de Resposta

- Respostas padronizadas com `ResponseEntity`
- Preservação de headers e status codes dos handlers internos

### 9.4 Endpoints Especiais

- Endpoints de ativação/desativação seguem padrão PATCH
- Alguns endpoints utilizam PUT para desativação (inconsistência no padrão)
- Endpoint de cálculo de taxas: `/process-type-fees/calculate`
- Endpoint de movimentação de categoria: `/categories/{categoryId}/move`

---

## 10. Exemplos de Uso

### 10.1 Criar Tipo de Licença

```http
POST /api/v1/license-types
Content-Type: application/json

{
  "name": "Licença Comercial",
  "description": "Licença para atividades comerciais",
  "code": "LIC_COM_001",
  "categoryId": "cat-123",
  "licensingModelKey": "STANDARD",
  "validityPeriod": 12,
  "validityUnitKey": "MONTHS",
  "renewable": true,
  "autoRenewal": false,
  "requiresInspection": true,
  "requiresPublicConsultation": false,
  "maxProcessingDays": 30,
  "hasFees": true,
  "baseFee": 150.00,
  "currencyCode": "CVE"
}
```

### 10.2 Listar Opções por Código

```http
GET /api/v1/options?ccode=VALIDITY_UNIT&active=true&pageSize=10
```

### 10.3 Ativar Tipo de Licença

```http
PATCH /api/v1/license-types/{licenseTypeId}/ativar
```

Esta documentação fornece uma visão abrangente da API de Licenciamento, facilitando a integração e manutenção do sistema.