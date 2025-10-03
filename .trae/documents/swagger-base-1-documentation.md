# Documentação API Licenciamento Base – Revisão Completa (swagger-base-1)

Esta documentação foi revisada e alinhada integralmente ao Swagger "swagger-base-1" (OpenAPI 3.1.0). Foram validados todos os endpoints, parâmetros, DTOs, exemplos de requisição e resposta, códigos de status HTTP e possíveis erros.

* URL base (desenvolvimento local): `http://localhost:8083`

* Server do Swagger: `https://ui-simple-sv-dev.nosi.cv`

* Padrões de resposta: `200 OK`, `400 Bad Request`, `404 Not Found`, `500 Internal Server Error`

* Paginação (listagens): `pageNumber` (padrão: "0"), `pageSize` (padrão: "20")

* Ativação/Desativação: conforme o domínio, algumas rotas usam `/ativar` e `/desativar`; outras usam `/enable` e `/disable` (mantidas conforme o Swagger).

## Convenções Gerais

* Content-Type:

  * JSON: `application/json`

  * Upload: `multipart/form-data` (campo `file` binário)

* Formato de paginação (DTO wrapper):

  * Campos comuns: `pageNumber`, `pageSize`, `totalElements`, `totalPages`, `last`, `first`, `content`

* Erros:

  * `400 Bad Request`: validação de dados, parâmetros inválidos

  * `404 Not Found`: recurso não encontrado

  * `500 Internal Server Error`: erro inesperado do servidor

  * Exemplo de corpo de erro (genérico):

    ```json
    {
      "timestamp": "2025-01-01T12:00:00Z",
      "status": 400,
      "error": "Bad Request",
      "message": "Parâmetro 'licenseTypeId' é obrigatório",
      "path": "/api/v1/license-parameters"
    }
    ```

***

## 1. License Types (Tipos de Licença)

### DTOs

* LicenseTypeRequestDTO

  ```json
  {
    "name": "string",
    "description": "string",
    "code": "string",
    "categoryId": "string",
    "licensingModelKey": "string",
    "validityPeriod": 0,
    "validityUnitKey": "string",
    "renewable": true,
    "autoRenewal": true,
    "requiresInspection": true,
    "requiresPublicConsultation": true,
    "maxProcessingDays": 0,
    "hasFees": true,
    "baseFee": 0,
    "currencyCode": "string",
    "active": true,
    "metadata": {}
  }
  ```

* LicenseTypeResponseDTO

  ```json
  {
    "id": "string",
    "name": "string",
    "description": "string",
    "code": "string",
    "categoryId": "string",
    "categoryName": "string",
    "licensingModelKey": "string",
    "validityPeriod": 0,
    "validityUnitKey": "string",
    "renewable": true,
    "autoRenewal": true,
    "requiresInspection": true,
    "requiresPublicConsultation": true,
    "maxProcessingDays": 0,
    "hasFees": true,
    "baseFee": 0,
    "currencyCode": "string",
    "active": true,
    "metadata": {}
  }
  ```

* WrapperListLicenseTypeDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/license-types`

  * Descrição: Lista tipos de licença com filtros.

  * Query (opcional): `name`, `code`, `categoryId`, `licensingModel`, `active` (padrão: `true`), `renewable`, `pageNumber` (padrão: `"0"`), `pageSize` (padrão: `"20"`).

  * 200: `WrapperListLicenseTypeDTO`

  * Exemplo de resposta:

    ```json
    {
      "pageNumber": 0,
      "pageSize": 20,
      "totalElements": 1,
      "totalPages": 1,
      "last": true,
      "first": true,
      "content": [
        {
          "id": "lt-001",
          "name": "Licença de Construção",
          "description": "Licença para obras de construção",
          "code": "LC001",
          "categoryId": "cat-001",
          "categoryName": "Construção",
          "licensingModelKey": "TEMPORARY",
          "validityPeriod": 12,
          "validityUnitKey": "MONTHS",
          "renewable": true,
          "autoRenewal": false,
          "requiresInspection": true,
          "requiresPublicConsultation": false,
          "maxProcessingDays": 30,
          "hasFees": true,
          "baseFee": 500,
          "currencyCode": "AOA",
          "active": true,
          "metadata": {}
        }
      ]
    }
    ```

* POST `/api/v1/license-types`

  * Descrição: Cria um novo tipo de licença.

  * Body: `LicenseTypeRequestDTO` (obrigatório)

  * 200: `LicenseTypeResponseDTO`

  * 400: validação

  * Exemplo de requisição:

    ```json
    {
      "name": "Licença de Funcionamento",
      "description": "Licença para estabelecimentos",
      "code": "LF001",
      "categoryId": "cat-456",
      "licensingModelKey": "PERMANENT",
      "validityPeriod": 24,
      "validityUnitKey": "MONTHS",
      "renewable": true,
      "autoRenewal": false,
      "requiresInspection": true,
      "requiresPublicConsultation": true,
      "maxProcessingDays": 45,
      "hasFees": true,
      "baseFee": 750,
      "currencyCode": "AOA",
      "active": true,
      "metadata": {}
    }
    ```

* GET `/api/v1/license-types/{licenseTypeId}`

  * Path: `licenseTypeId` (string, obrigatório)

  * 200: `LicenseTypeResponseDTO`; 404

* PUT `/api/v1/license-types/{licenseTypeId}`

  * Path: `licenseTypeId` (string, obrigatório)

  * Body: `LicenseTypeRequestDTO` (obrigatório)

  * 200: `LicenseTypeResponseDTO`; 400; 404

* PATCH `/api/v1/license-types/{licenseTypeId}/ativar`

  * Path: `licenseTypeId` (string, obrigatório)

  * 200: `string` (mensagem)

* PATCH `/api/v1/license-types/{licenseTypeId}/desativar`

  * Path: `licenseTypeId` (string, obrigatório)

  * 200: `string` (mensagem)

***

## 2. License Parameters (Parâmetros de Licença)

### DTOs

* LicenseParameterRequestDTO

  ```json
  {
    "licenseTypeId": "string",
    "validityUnit": "string",
    "validityPeriod": 0,
    "model": "string",
    "provisionalValidity": 0,
    "definitiveLicenseValidity": 0,
    "provisionalDefaultPeriod": 0,
    "definitiveDefaultPeriod": 0,
    "provisionalRenewalPeriod": 0,
    "maxProvisonalRenewal": 0,
    "definitiveRenewalPeriod": 0,
    "definitiveRenewalDefaultPeriod": 0,
    "renewalDefaultPeriod": 0,
    "maxRenewalPeriod": 0,
    "vitalityFlag": true
  }
  ```

* LicenseParameterResponseDTO

  ```json
  {
    "id": "string",
    "licenseTypeId": "string",
    "licenseTypeName": "string",
    "validityUnit": "string",
    "validityPeriod": 0,
    "model": "string",
    "provisionalValidity": 0,
    "definitiveLicenseValidity": 0,
    "provisionalDefaultPeriod": 0,
    "definitiveDefaultPeriod": 0,
    "provisionalRenewalPeriod": 0,
    "maxProvisonalRenewal": 0,
    "definitiveRenewalPeriod": 0,
    "definitiveRenewalDefaultPeriod": 0,
    "renewalDefaultPeriod": 0,
    "maxRenewalPeriod": 0,
    "vitalityFlag": true,
    "active": true
  }
  ```

* WrapperListLicenseParameterDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/license-parameters`

  * Query (opcional): `id`, `licenseTypeId`, `validityUnit`, `model`, `active` (padrão: `true`), `vitalityFlag`, `pageNumber` (padrão: `"0"`), `pageSize` (padrão: `"20"`).

  * 200: `WrapperListLicenseParameterDTO`

* POST `/api/v1/license-parameters`

  * Body: `LicenseParameterRequestDTO` (obrigatório)

  * 200: `LicenseParameterResponseDTO`; 400

* GET `/api/v1/license-parameters/{licenseParameterId}`

  * Path: `licenseParameterId` (string, obrigatório)

  * 200: `LicenseParameterResponseDTO`; 404

* PUT `/api/v1/license-parameters/{licenseParameterId}`

  * Path: `licenseParameterId` (string, obrigatório)

  * Body: `LicenseParameterRequestDTO` (obrigatório)

  * 200: `LicenseParameterResponseDTO`; 400; 404

* PATCH `/api/v1/license-parameters/{licenseParameterId}/enable`

  * Path: `licenseParameterId` (string, obrigatório)

  * 200: `string` (mensagem)

* PATCH `/api/v1/license-parameters/{licenseParameterId}/disable`

  * Path: `licenseParameterId` (string, obrigatório)

  * 200: `string` (mensagem)

* GET `/api/v1/license-parameters/license-types/{licenseTypeId}`

  * Path: `licenseTypeId` (string, obrigatório)

  * 200: `LicenseParameterResponseDTO` (lista paginada ou conteúdo relacionado no wrapper);

***

## 3. License Process Types (Tipos de Processo de Licença)

### DTOs

* LicenseProcessTypeRequestDTO

  ```json
  {
    "licenseTypeId": "string",
    "processName": "string",
    "description": "string",
    "activityServerUrl": "string",
    "processDefinitionKey": "string"
  }
  ```

* LicenseProcessTypeResponseDTO

  ```json
  {
    "id": "string",
    "licenseTypeId": "string",
    "processName": "string",
    "description": "string",
    "activityServerUrl": "string",
    "processDefinitionKey": "string",
    "active": true
  }
  ```

* WrapperLicenseProcessTypeDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/license-types/{licenseTypeId}/process-types`

  * Path: `licenseTypeId` (string, obrigatório)

  * Query (opcional): `processName`, `processDefinitionKey`, `active` (padrão: `true`), `pageNumber` (padrão: `"0"`), `pageSize` (padrão: `"20"`).

  * 200: `WrapperLicenseProcessTypeDTO`

* POST `/api/v1/license-types/{licenseTypeId}/process-types`

  * Path: `licenseTypeId` (string, obrigatório)

  * Body: `LicenseProcessTypeRequestDTO` (obrigatório)

  * 200: `LicenseProcessTypeResponseDTO`; 400

* GET `/api/v1/license-types/{licenseTypeId}/process-types/{licenseProcessTypeId}`

  * Path: `licenseTypeId`, `licenseProcessTypeId` (string, obrigatórios)

  * 200: `LicenseProcessTypeResponseDTO`; 404

* PUT `/api/v1/license-types/{licenseTypeId}/process-types/{licenseProcessTypeId}`

  * Path: `licenseTypeId`, `licenseProcessTypeId` (string, obrigatórios)

  * Body: `LicenseProcessTypeRequestDTO` (obrigatório)

  * 200: `LicenseProcessTypeResponseDTO`; 400; 404

* PATCH `/api/v1/license-types/{licenseTypeId}/process-types/{licenseProcessTypeId}/ativar`

  * Path: `licenseTypeId`, `licenseProcessTypeId` (string, obrigatórios)

  * 200: `string` (mensagem)

* PATCH `/api/v1/license-types/{licenseTypeId}/process-types/{licenseProcessTypeId}/desativar`

  * Path: `licenseTypeId`, `licenseProcessTypeId` (string, obrigatórios)

  * 200: `string` (mensagem)

***

## 4. Process Type Fees (Taxas de Tipo de Processo)

### DTOs

* ProcessTypeFeeRequestDTO

  ```json
  {
    "licenseTypeProcessTypeId": "string",
    "feeType": "string",
    "feeCategory": "string",
    "name": "string",
    "description": "string",
    "baseAmount": 0,
    "currencyCode": "string",
    "calculationMethod": "string",
    "minimumAmount": 0,
    "maximumAmount": 0,
    "validityDays": 0
  }
  ```

* ProcessTypeFeeResponseDTO

  ```json
  {
    "id": "string",
    "licenseTypeProcessTypeId": "string",
    "feeType": "string",
    "feeCategory": "string",
    "name": "string",
    "description": "string",
    "baseAmount": 0,
    "currencyCode": "string",
    "calculationMethod": "string",
    "minimumAmount": 0,
    "maximumAmount": "string",
    "validityDays": 0,
    "active": true
  }
  ```

* CalculatParameterProcTypeFeeDTO

  ```json
  {
    "applicationValue": 0,
    "urgentProcessing": true,
    "inspectionRequired": true,
    "companySize": "string"
  }
  ```

* CalculateTaxProcessTypeFeeDTO

  ```json
  {
    "licenseTypeProcessTypeId": "string",
    "calculationParameters": {
      "applicationValue": 0,
      "urgentProcessing": false,
      "inspectionRequired": true,
      "companySize": "SMALL"
    }
  }
  ```

* ProcessFeeTypeCalcResponseDTO

  ```json
  {
    "totalAmount": 0,
    "currencyCode": "string",
    "calculationDate": "date",
    "feeDetails": [
      {
        "licenseTypeProcessTypeId": "string",
        "id": "string",
        "feeName": "string",
        "feeType": "string",
        "feeCategory": "string",
        "amount": 0,
        "calculationMethod": "string"
      }
    ]
  }
  ```

* WrapperListProcessTypeFeeDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/process-type-fees`

  * Query (opcional): `licenseTypeProcessTypeId`, `feeCategoryId`, `name`, `feeType`, `active` (padrão: `true`), `pageNumber` (padrão: `"0"`), `pageSize` (padrão: `"20"`).

  * 200: `WrapperListProcessTypeFeeDTO`

* POST `/api/v1/process-type-fees`

  * Body: `ProcessTypeFeeRequestDTO`

  * 200: `ProcessTypeFeeResponseDTO`; 400

* GET `/api/v1/process-type-fees/{process_type_fee_id}`

  * Path: `process_type_fee_id` (string, obrigatório)

  * 200: `ProcessTypeFeeResponseDTO`; 404

* PUT `/api/v1/process-type-fees/{process_type_fee_id}`

  * Path: `process_type_fee_id` (string, obrigatório)

  * Body: `ProcessTypeFeeRequestDTO` (obrigatório)

  * 200: `ProcessTypeFeeResponseDTO`; 400; 404

* PATCH `/api/v1/process-type-fees/{process_type_fee_id}/ativar`

  * Path: `process_type_fee_id` (string, obrigatório)

  * 200: `string` (mensagem)

* PATCH `/api/v1/process-type-fees/{process_type_fee_id}/desativar`

  * Path: `process_type_fee_id` (string, obrigatório)

  * 200: `string` (mensagem)

* POST `/api/v1/process-type-fees/calculate`

  * Body: `CalculateTaxProcessTypeFeeDTO` (obrigatório)

  * 200: `ProcessFeeTypeCalcResponseDTO`; 400

  * Exemplo de requisição:

    ```json
    {
      "licenseTypeProcessTypeId": "ltpt-456",
      "calculationParameters": {
        "applicationValue": 100000,
        "urgentProcessing": false,
        "inspectionRequired": true,
        "companySize": "MEDIUM"
      }
    }
    ```

***

## 5. Categories (Categorias)

### DTOs

* CategoryRequestDTO

  ```json
  {
    "name": "string",
    "description": "string",
    "code": "string",
    "parentId": "string",
    "sectorId": "string",
    "sortOrder": 0,
    "active": true,
    "metadata": {}
  }
  ```

* CategoryResponseDTO

  ```json
  {
    "id": "string",
    "code": "string",
    "description": "string",
    "name": "string",
    "sectorId": "string",
    "sectorName": "string",
    "level": 0,
    "path": "string",
    "active": true,
    "children": [ { "id": "string" } ],
    "metadata": {}
  }
  ```

* MoveCategoryDTO

  ```json
  {
    "newParentId": "string"
  }
  ```

* WrapperListCategoryDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/categories/{categoryId}`

  * Path: `categoryId` (string, obrigatório)

  * 200: `CategoryResponseDTO`; 404

* PUT `/api/v1/categories/{categoryId}`

  * Path: `categoryId` (string, obrigatório)

  * Body: `CategoryRequestDTO` (obrigatório)

  * 200: `CategoryResponseDTO`; 400; 404

* PATCH `/api/v1/categories/{categoryId}/move`

  * Path: `categoryId` (string, obrigatório)

  * Body: `MoveCategoryDTO` (obrigatório; campo `newParentId`)

  * 200: `string` (mensagem)

* PATCH `/api/v1/categories/{categoryId}/ativar`

  * Path: `categoryId` (string, obrigatório)

  * 200: `string` (mensagem)

* PATCH `/api/v1/categories/{categoryId}/desativar`

  * Path: `categoryId` (string, obrigatório)

  * 200: `string` (mensagem)

***

## 6. Sectors (Setores)

### DTOs

* SectorRequestDTO

  ```json
  {
    "name": "string",
    "description": "string",
    "sectorTypeKey": "string",
    "code": "string",
    "active": true,
    "sortOrder": 0,
    "metadata": {}
  }
  ```

* SectorResponseDTO

  ```json
  {
    "id": "string",
    "name": "string",
    "description": "string",
    "code": "string",
    "sectorType": "string",
    "active": true,
    "sortOrder": 0,
    "metadata": {}
  }
  ```

* WrapperListSectorDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/sectors`

  * Query (opcional): `sectorType`, `name`, `code`, `active` (padrão: `true`), `pageNumber` (padrão: `"0"`), `pageSize` (padrão: `"20"`).

  * 200: `WrapperListSectorDTO`

* POST `/api/v1/sectors`

  * Body: `SectorRequestDTO` (obrigatório)

  * 200: `SectorResponseDTO`; 400

* GET `/api/v1/sectors/{sectorId}`

  * Path: `sectorId` (string, obrigatório)

  * 200: `SectorResponseDTO`; 404

* PUT `/api/v1/sectors/{sectorId}`

  * Path: `sectorId` (string, obrigatório)

  * Body: `SectorRequestDTO` (obrigatório)

  * 200: `SectorResponseDTO`; 400; 404

***

## 7. Legislations (Legislações)

### DTOs

* LegislationRequestDTO

  ```json
  {
    "name": "string",
    "legislationType": "string",
    "republicBulletin": "string",
    "publicationDate": "date",
    "description": "string",
    "documentUrl": "string",
    "documentName": "string",
    "licenseTypeId": "string"
  }
  ```

* LegislationResponseDTO

  ```json
  {
    "id": "string",
    "name": "string",
    "legislationType": "string",
    "publicationDate": "date",
    "description": "string",
    "documentUrl": "string",
    "documentName": "string",
    "active": true,
    "licenseTypeId": "string"
  }
  ```

* WrapperListLegislationDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/legislations`

  * Query (opcional): `licenseTypeId`, `name`, `legislationType`, `active` (padrão: `true`), `pageNumber` (padrão: `"0"`), `pageSize` (padrão: `"20"`).

  * 200: `WrapperListLegislationDTO`

* POST `/api/v1/legislations`

  * Body: `LegislationRequestDTO` (obrigatório)

  * 200: `LegislationResponseDTO`; 400

* GET `/api/v1/legislations/{legislationId}`

  * Path: `legislationId` (string, obrigatório)

  * 200: `LegislationResponseDTO`; 404

* PUT `/api/v1/legislations/{legislationId}`

  * Path: `legislationId` (string, obrigatório)

  * Body: `LegislationRequestDTO` (obrigatório)

  * 200: `LegislationResponseDTO`; 400; 404

* PATCH `/api/v1/legislations/{legislationId}/enable`

  * Path: `legislationId` (string, obrigatório)

  * 200: `string` (mensagem)

* PATCH `/api/v1/legislations/{legislationId}/disable`

  * Path: `legislationId` (string, obrigatório)

  * 200: `string` (mensagem)

* GET `/api/v1/legislations/license-types/{licenseTypeId}`

  * Path: `licenseTypeId` (string, obrigatório)

  * 200: `WrapperListLegislationDTO` (conteúdo relacionado);

***

## 8. Entity (Entidades / Legal Parties)

### DTOs

* ContactRequestDTO

  ```json
  {
    "id": "string",
    "contactType": "string",
    "contactName": "string",
    "primary": true
  }
  ```

* ContactResponseDTO

  ```json
  {
    "id": "string",
    "contactType": "string",
    "contactName": "string",
    "active": true,
    "primary": true
  }
  ```

* LegalPartyRequestDTO

  ```json
  {
    "organizationId": "string",
    "entityName": "string",
    "entityType": "string",
    "contacts": [
      { "id": "string", "contactType": "string", "contactName": "string", "primary": true }
    ],
    "licenseTypeId": "string"
  }
  ```

* LegalPartyResponseDTO

  ```json
  {
    "id": "string",
    "licenseTypeId": "string",
    "organizationId": "string",
    "entityName": "string",
    "entityType": "string",
    "contacts": [ { "id": "string" } ],
    "active": true
  }
  ```

* WrapperListLegalPartyDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/entities`

  * Query (opcional): `licenseTypeId`, `entityName`, `entityType`, `active` (padrão: `true`), `pageNumber` (padrão: `"0"`), `pageSize` (padrão: `"20"`).

  * 200: `WrapperListLegalPartyDTO`

* POST `/api/v1/entities`

  * Body: `LegalPartyRequestDTO` (obrigatório)

  * 200: `LegalPartyResponseDTO`; 400

* GET `/api/v1/entities/{entitiyId}`

  * Path: `entitiyId` (string, obrigatório) — nota: grafia no Swagger é "entitiyId".

  * 200: `LegalPartyResponseDTO`; 404

* PUT `/api/v1/entities/{entitiyId}`

  * Path: `entitiyId` (string, obrigatório)

  * Body: `LegalPartyRequestDTO` (obrigatório)

  * 200: `LegalPartyResponseDTO`; 400; 404

* PATCH `/api/v1/entities/{entityId}/ativar`

  * Path: `entityId` (string, obrigatório)

  * 200: `string` (mensagem)

* PATCH `/api/v1/entities/{entityId}/desativar`

  * Path: `entityId` (string, obrigatório)

  * 200: `string` (mensagem)

* GET `/api/v1/entities/license-types/{licenseTypeId}`

  * Path: `licenseTypeId` (string, obrigatório)

  * 200: `WrapperListLegalPartyDTO` (conteúdo relacionado);

***

## 9. Options (Opções)

### DTOs

* OptionRequestDTO

  ```json
  {
    "ccode": "string",
    "ckey": "string",
    "cvalue": "string",
    "locale": "string",
    "sort_order": 0,
    "metadata": {},
    "description": "string"
  }
  ```

* OptionResponseDTO

  ```json
  {
    "id": "string",
    "ccode": "string",
    "ckey": "string",
    "cvalue": "string",
    "locale": "string",
    "sort_order": 0,
    "description": "string"
  }
  ```

* OptionCodeItemDTO

  ```json
  {
    "key": "string",
    "value": "string",
    "sortOrder": 0,
    "metadata": {},
    "description": "string"
  }
  ```

* OptionCodeResponseDTO

  ```json
  {
    "code": "string",
    "locale": "string",
    "items": [ { "key": "string", "value": "string" } ]
  }
  ```

* WrapperListOptionsDTO

  ```json
  {
    "pageNumber": 0,
    "pageSize": 20,
    "totalElements": 0,
    "totalPages": 0,
    "last": false,
    "first": false,
    "content": [ { "id": "string" } ]
  }
  ```

### Endpoints

* GET `/api/v1/options`

  * Query (opcional): `ccode`, `ckey`, `locale`, `active` (padrão: `true`).

  * 200: `WrapperListOptionsDTO`

* GET `/api/v1/options/by-code/{code}`

  * Path: `code` (string, obrigatório)

  * 200: `OptionCodeResponseDTO`

* GET `/api/v1/options/{ccode}/findByCcode`

  * Path: `ccode` (string, obrigatório)

  * 200: `WrapperListOptionsDTO` (ou lista compatível)

* GET `/api/v1/options/{ccode}/{ckey}/exists`

  * Path: `ccode`, `ckey` (string, obrigatórios)

  * 200: `boolean`

* GET `/api/v1/options/{optionId}`

  * Path: `optionId` (string, obrigatório)

  * 200: `OptionResponseDTO`; 404

* PUT `/api/v1/options/{optionId}`

  * Path: `optionId` (string, obrigatório)

  * Body: `OptionRequestDTO` (obrigatório)

  * 200: `OptionResponseDTO`; 400; 404

* POST `/api/v1/options`

  * Body: `OptionRequestDTO` (obrigatório)

  * 200: `OptionResponseDTO`; 400

* PATCH `/api/v1/options/{optionId}/ativar`

  * Path: `optionId` (string, obrigatório)

  * 200: `string` (mensagem)

* PATCH `/api/v1/options/{optionId}/desativar`

  * Path: `optionId` (string, obrigatório)

  * 200: `string` (mensagem)

***

## 10. Documento (Files)

### DTOs

* FileResponseDTO

  ```json
  {
    "displayName": "string",
    "fileId": "string"
  }
  ```

* FileUrlDTO

  ```json
  {
    "url": "string"
  }
  ```

### Endpoints

* POST `/documento/public/{path}`

  * Descrição: Upload público com URL acessível.

  * Path: `path` (string, obrigatório)

  * Form-data: `file` (binário, obrigatório)

  * 200: `FileResponseDTO`; 400: arquivo inválido/vazio

  * Exemplo de resposta:

    ```json
    { "displayName": "exemplo.pdf", "fileId": "file-123" }
    ```

* POST `/documento/private/{folder}`

  * Descrição: Upload privado para pasta específica.

  * Path: `folder` (string, obrigatório; enum: `LEGISLATIONFOLDER`)

  * Form-data: `file` (binário, obrigatório)

  * 200: `FileResponseDTO`; 400: arquivo inválido/vazio

* GET `/documento`

  * Descrição: Gera link assinado temporário.

  * Query: `fileId` (string, obrigatório)

  * 200: `FileUrlDTO`

***

## Schemas Adicionais e Wrappers

* ProcessTypeFeeResponseLightDTO

  ```json
  {
    "licenseTypeProcessTypeId": "string",
    "id": "string",
    "feeName": "string",
    "feeType": "string",
    "feeCategory": "string",
    "amount": 0,
    "calculationMethod": "string"
  }
  ```

***

## Exemplos Adicionais

* Exemplo de ativação de Licença (PATCH):

  * `PATCH /api/v1/license-types/lt-001/ativar`

  * Resposta 200:

    ```json
    "LicenseType lt-001 ativado com sucesso"
    ```

* Exemplo de desativação de Legislação (PATCH):

  * `PATCH /api/v1/legislations/leg-001/disable`

  * Resposta 200:

    ```json
    "Legislation leg-001 disabled"
    ```

* Exemplo de cálculo de taxas:

  ```json
  {
    "totalAmount": 750.0,
    "currencyCode": "AOA",
    "calculationDate": "2024-01-15",
    "feeDetails": [
      {
        "licenseTypeProcessTypeId": "ltpt-456",
        "id": "fee-123",
        "feeName": "Taxa de Análise",
        "feeType": "FIXED",
        "feeCategory": "ADMIN",
        "amount": 250.0,
        "calculationMethod": "FIXED_AMOUNT"
      },
      {
        "id": "fee-124",
        "feeName": "Taxa de Inspeção",
        "feeType": "PERCENTAGE",
        "feeCategory": "INSPECTION",
        "amount": 500.0,
        "calculationMethod": "PERCENTAGE_OF_VALUE"
      }
    ]
  }
  ```

***

## Notas de Compatibilidade

* Alguns domínios usam endpoints de ativação/desativação com `/ativar` e `/desativar` (Português), enquanto outros usam `/enable` e `/disable` (Inglês). A documentação reflete fielmente o Swagger.

* Grafia `entitiyId` em `/api/v1/entities/{entitiyId}` segue o Swagger.

* `MoveCategoryDTO` possui campo `newParentId` (conforme Swagger); qualquer documentação anterior com `parentId/position` foi ajustada.

***

## Boas Práticas de Consumo

* Validar campos obrigatórios antes de enviar requisições (reduz `400`).

* Tratar `404` para buscas por ID inexistente.

* Em uploads, garantir `Content-Type: multipart/form-data` e presença do campo `file`.

* Em paginação, respeitar os limites e interpretar metadados (`totalElements`, `totalPages`).

* Logar e monitorar respostas `5xx` para análise de estabilidade.

***

## Histórico

* Revisão concluída e validada contra Swagger `swagger-base-1`.

* Inclusão de todos os DTOs principais e wrappers.

* Padronização dos exemplos de requisição e resposta.

* Observações de compatibilidade e grafia conforme especificação.

