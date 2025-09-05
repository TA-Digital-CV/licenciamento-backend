# Especificação Frontend - Parametrização de Licenciamento

## 1. Visão Geral

Este documento especifica os requisitos e integrações do frontend para o módulo de parametrização do sistema de licenciamento, incluindo a comunicação com a API backend e as interfaces de usuário necessárias.

**Versão:** 1.0  
**Data:** 2025  
**Responsável:** Equipe de Desenvolvimento Frontend

---

## 2. Arquitetura de Integração

### 2.1 Comunicação com Backend

**Base URL da API:** `http://localhost:8083/api/v1`

### 2.2 Tecnologias Frontend

- **Framework:** Next.js 14+ com React 18
- **Linguagem:** TypeScript
- **Estilização:** Tailwind CSS
- **Componentes:** IGRP Framework React Design System
- **Gerenciamento de Estado:** React Hooks + Context API
- **HTTP Client:** Fetch API nativo

---

## 3. Módulos de Parametrização

### 3.1 Gestão de Setores

#### 3.1.1 Funcionalidades
- Listagem de setores com filtros
- Criação de novos setores
- Edição de setores existentes
- Visualização de detalhes do setor

#### 3.1.2 Integração com API
```typescript
// Interfaces TypeScript
interface Sector {
  id: string;
  name: string;
  description: string;
  code: string;
  sectorType: string;
  active: boolean;
  sortOrder: number;
  metadata?: any;
}

interface SectorRequest {
  name: string;
  description: string;
  sectorTypeKey: string;
  code: string;
  sortOrder: number;
  metadata?: any;
}
```

#### 3.1.3 Endpoints Utilizados
- `GET /api/v1/sectors` - Listagem com filtros
- `GET /api/v1/sectors/{id}` - Busca por ID
- `POST /api/v1/sectors` - Criação
- `PUT /api/v1/sectors/{id}` - Atualização

### 3.2 Gestão de Categorias

#### 3.2.1 Funcionalidades
- Listagem hierárquica de categorias
- Criação de categorias e subcategorias
- Edição de categorias existentes
- Movimentação de categorias na hierarquia
- Visualização em árvore

#### 3.2.2 Integração com API
```typescript
interface Category {
  id: string;
  code: string;
  description: string;
  name: string;
  sectorId: string;
  sectorName: string;
  level: number;
  path: string;
  children: Category[];
  metadata?: any;
}

interface CategoryRequest {
  name: string;
  description: string;
  code: string;
  parentId?: string;
  sectorId: string;
  sortOrder: number;
  metadata?: any;
}
```

#### 3.2.3 Endpoints Utilizados
- `GET /api/v1/categories` - Listagem com filtros
- `GET /api/v1/categories/{id}` - Busca por ID
- `POST /api/v1/categories` - Criação
- `PUT /api/v1/categories/{id}` - Atualização
- `POST /api/v1/categories/{id}/move` - Movimentação

### 3.3 Gestão de Tipos de Licença

#### 3.3.1 Funcionalidades
- Listagem de tipos de licença
- Criação de novos tipos
- Edição de tipos existentes
- Ativação/desativação de tipos
- Configuração de parâmetros de validade

#### 3.3.2 Integração com API
```typescript
interface LicenseType {
  id: string;
  name: string;
  description: string;
  code: string;
  categoryId: string;
  categoryName: string;
  licensingModelKey: string;
  validityPeriod: number;
  validityUnitKey: string;
  renewable: boolean;
  autoRenewal: boolean;
  requiresInspection: boolean;
  requiresPublicConsultation: boolean;
  maxProcessingDays: number;
  hasFees: boolean;
  baseFee: number;
  currencyCode: string;
  metadata?: any;
}
```

#### 3.3.3 Endpoints Utilizados
- `GET /api/v1/license-types` - Listagem com filtros
- `GET /api/v1/license-types/{id}` - Busca por ID
- `POST /api/v1/license-types` - Criação
- `PUT /api/v1/license-types/{id}` - Atualização
- `PATCH /api/v1/license-types/{id}/ativar` - Ativação
- `PATCH /api/v1/license-types/{id}/desativar` - Desativação

### 3.4 Gestão de Opções

#### 3.4.1 Funcionalidades
- Listagem de opções por categoria
- Criação de novas opções
- Edição de opções existentes
- Ativação/desativação de opções
- Verificação de existência por código

#### 3.4.2 Integração com API
```typescript
interface Option {
  id: string;
  ccode: string;
  ckey: string;
  cvalue: string;
  locale: string;
  sort_order: number;
}

interface OptionRequest {
  ccode: string;
  ckey: string;
  cvalue: string;
  locale: string;
  sort_order: number;
  metadata?: any;
}
```

---

## 4. Componentes de Interface

### 4.1 Componentes Reutilizáveis

#### 4.1.1 DataGrid Parametrização
```typescript
interface ParametrizationDataGridProps<T> {
  data: T[];
  columns: Column<T>[];
  loading?: boolean;
  onEdit?: (item: T) => void;
  onDelete?: (item: T) => void;
  onActivate?: (item: T) => void;
  onDeactivate?: (item: T) => void;
  pagination?: PaginationProps;
}
```

#### 4.1.2 Formulário Base
```typescript
interface BaseFormProps<T> {
  initialData?: Partial<T>;
  onSubmit: (data: T) => Promise<void>;
  onCancel: () => void;
  loading?: boolean;
  mode: 'create' | 'edit';
}
```

#### 4.1.3 Filtros Avançados
```typescript
interface AdvancedFiltersProps {
  filters: Record<string, any>;
  onFiltersChange: (filters: Record<string, any>) => void;
  onClear: () => void;
  fields: FilterField[];
}
```

### 4.2 Páginas Específicas

#### 4.2.1 Página de Setores (`/parametrizacao/setores`)
- Lista de setores com filtros
- Modal de criação/edição
- Ações de ativação/desativação

#### 4.2.2 Página de Categorias (`/parametrizacao/categorias`)
- Visualização hierárquica
- Drag & drop para movimentação
- Modal de criação/edição

#### 4.2.3 Página de Tipos de Licença (`/parametrizacao/tipos-licenca`)
- Lista com filtros avançados
- Formulário detalhado de criação/edição
- Configuração de parâmetros

---

## 5. Tratamento de Erros

### 5.1 Estrutura de Erro da API
```typescript
interface ApiError {
  type: string;
  title: string;
  status: number;
  detail?: string;
  errors?: Record<string, string>;
  CurrentValue?: any;
  AllowedValues?: string[];
}
```

### 5.2 Tratamento no Frontend
```typescript
const handleApiError = (error: ApiError) => {
  switch (error.status) {
    case 400:
      if (error.errors) {
        // Exibir erros de validação nos campos
        showValidationErrors(error.errors);
      } else {
        // Exibir erro genérico
        showErrorMessage(error.title || 'Dados inválidos');
      }
      break;
    case 404:
      showErrorMessage('Recurso não encontrado');
      break;
    case 500:
      showErrorMessage('Erro interno do servidor');
      break;
    default:
      showErrorMessage('Erro inesperado');
  }
};
```

---

## 6. Validações Frontend

### 6.1 Validações de Formulário
```typescript
// Validação de Setor
const sectorValidation = {
  name: { required: true, minLength: 2, maxLength: 100 },
  code: { required: true, pattern: /^[A-Z0-9_]+$/ },
  sectorTypeKey: { required: true },
  sortOrder: { required: false, min: 0 }
};

// Validação de Tipo de Licença
const licenseTypeValidation = {
  name: { required: true, minLength: 2, maxLength: 200 },
  code: { required: true, pattern: /^[A-Z0-9_]+$/ },
  categoryId: { required: true },
  validityPeriod: { required: true, min: 1 },
  baseFee: { required: false, min: 0 }
};
```

### 6.2 Validação em Tempo Real
- Validação de campos obrigatórios
- Verificação de formato de códigos
- Validação de valores numéricos
- Verificação de unicidade de códigos

---

## 7. Estados de Loading e Feedback

### 7.1 Estados de Carregamento
```typescript
interface LoadingStates {
  loading: boolean;
  saving: boolean;
  deleting: boolean;
  activating: boolean;
}
```

### 7.2 Feedback Visual
- Spinners durante carregamento
- Mensagens de sucesso após operações
- Mensagens de erro com detalhes
- Confirmações para ações destrutivas

---

## 8. Navegação e Roteamento

### 8.1 Estrutura de Rotas
```
/parametrizacao/
├── setores/
│   ├── index (lista)
│   ├── novo (criação)
│   └── [id]/editar (edição)
├── categorias/
│   ├── index (lista hierárquica)
│   ├── nova (criação)
│   └── [id]/editar (edição)
├── tipos-licenca/
│   ├── index (lista)
│   ├── novo (criação)
│   └── [id]/
│       ├── editar (edição)
│       ├── parametros (configuração)
│       └── processos (tipos de processo)
└── opcoes/
    ├── index (lista por categoria)
    ├── nova (criação)
    └── [id]/editar (edição)
```

---

## 9. Considerações de Performance

### 9.1 Otimizações
- Paginação server-side para listas grandes
- Debounce em filtros de busca
- Cache de opções e dados de referência
- Lazy loading de componentes pesados

### 9.2 Gerenciamento de Estado
- Context API para dados globais
- useState para estados locais
- useEffect para sincronização com API
- Custom hooks para lógica reutilizável

---

## 10. Testes

### 10.1 Testes Unitários
- Componentes de formulário
- Validações
- Utilitários de formatação

### 10.2 Testes de Integração
- Fluxos completos de CRUD
- Integração com API
- Navegação entre páginas

---

## 11. Documentação Adicional

### 11.1 Guias de Desenvolvimento
- Padrões de código
- Convenções de nomenclatura
- Estrutura de componentes

### 11.2 Manuais de Usuário
- Guia de uso da parametrização
- Troubleshooting comum
- FAQ técnico

Esta especificação serve como base para o desenvolvimento do módulo de parametrização do frontend, garantindo integração adequada com a API backend e experiência de usuário consistente.