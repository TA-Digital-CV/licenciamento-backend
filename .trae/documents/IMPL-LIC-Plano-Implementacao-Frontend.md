# Documento de Aprovação - Implementação Frontend

# Sistema de Gestão de Licenças Emitidas de Cabo Verde

## 1. Visão Geral do Projeto

### 1.1 Objetivo

Implementar a interface visual das páginas do Sistema de Gestão de Licenças Emitidas de Cabo Verde, focando exclusivamente na experiência do usuário e componentes visuais. A framework IGRP será responsável por layout, cache, deployment, performance, monitoramento e autenticação.

### 1.2 Escopo da Implementação

- **Diretório Base**: `c:\projects\ta-digital\projects\licenciamento-gestao-frontend`

- **Framework**: React 18 + TypeScript integrado ao IGRP Framework

- **Bibliotecas IGRP**: `@igrp/igrp-framework-react-design-system`

- **Foco**: Interface visual, componentes de negócio, formulários e navegação

### 1.3 Responsabilidades

**Framework IGRP (Gerenciado automaticamente):**

- Layout e estrutura base
- Sistema de cache
- Deployment e configurações
- Performance e otimizações
- Monitoramento e logs
- Autenticação e autorização

**Desenvolvimento Frontend (Nosso foco):**

- Componentes visuais das páginas
- Formulários e validações de interface
- Interações do usuário
- Navegação entre páginas
- Experiência do usuário (UX/UI)

## 2. Análise dos Componentes Visuais

### 2.1 Páginas e Componentes Principais

#### PG001 - Página de Consulta de Licenças

- **Componentes Visuais**:
  - Formulário de busca avançada
  - Grid de resultados com paginação
  - Filtros laterais por categoria
  - Cards de licenças com status visual

- **Interações**: Busca em tempo real, ordenação, filtros dinâmicos

- **Estimativa**: 32 horas

#### PG002 - Página de Detalhes da Licença

- **Componentes Visuais**:
  - Cabeçalho com informações principais
  - Abas para diferentes seções de dados
  - Visualizador de documentos
  - Botões de ação contextuais

- **Interações**: Navegação por abas, download de documentos, ações rápidas

- **Estimativa**: 24 horas

#### PG003 - Dashboard de Monitoramento

- **Componentes Visuais**:
  - Cards de métricas com ícones
  - Gráficos de status e tendências
  - Lista de alertas prioritários
  - Calendário de vencimentos

- **Interações**: Filtros de período, drill-down em métricas

- **Estimativa**: 28 horas

#### PG004 - Wizard de Renovação

- **Componentes Visuais**:
  - Stepper com progresso visual
  - Formulários por etapa
  - Upload de documentos com preview
  - Resumo final com confirmação

- **Interações**: Navegação entre etapas, validação em tempo real

- **Estimativa**: 36 horas

#### PG005 - Gestão de Estabelecimentos

- **Componentes Visuais**:
  - Formulário com seções colapsáveis
  - Seletor de coordenadas GPS
  - Upload múltiplo de documentos
  - Preview de localização no mapa

- **Interações**: Geolocalização, validação de coordenadas

- **Estimativa**: 40 horas

### 2.2 Requisitos de Interface

#### Experiência do Usuário

- **Responsividade**: Adaptação para mobile, tablet e desktop

- **Acessibilidade**: Navegação por teclado, leitores de tela

- **Feedback Visual**: Estados de loading, sucesso e erro

- **Consistência**: Padrões visuais uniformes em todas as páginas

#### Design System

- **Componentes IGRP**: Utilização da biblioteca de design system

- **Customização**: Adaptação dos componentes para o contexto de licenciamento

- **Temas**: Aplicação da identidade visual governamental

## 3. Estrutura de Componentes Visuais

### 3.1 Organização dos Componentes de Interface

```
c:\projects\ta-digital\projects\licenciamento-gestao-frontend\
├── src/
│   ├── app/
│   │   ├── (igrp)/
│   │   │   ├── (generated)/                    # Páginas dinâmicas IGRP
│   │   │   │   ├── licencas/
│   │   │   │   │   ├── page.tsx               # Interface de consulta
│   │   │   │   │   ├── [id]/
│   │   │   │   │   │   ├── page.tsx           # Interface de detalhes
│   │   │   │   │   │   ├── renovar/
│   │   │   │   │   │   │   └── page.tsx       # Interface de renovação
│   │   │   │   │   │   ├── alterar/
│   │   │   │   │   │   │   └── page.tsx       # Interface de alteração
│   │   │   │   │   │   ├── transferir/
│   │   │   │   │   │   │   └── page.tsx       # Interface de transferência
│   │   │   │   │   │   ├── suspender/
│   │   │   │   │   │   │   └── page.tsx       # Interface de suspensão
│   │   │   │   │   │   └── historico/
│   │   │   │   │   │       └── page.tsx       # Interface de histórico
│   │   │   │   ├── estabelecimentos/
│   │   │   │   │   ├── page.tsx               # Interface de listagem
│   │   │   │   │   ├── [id]/
│   │   │   │   │   │   ├── page.tsx           # Interface de detalhes
│   │   │   │   │   │   ├── editar/
│   │   │   │   │   │   │   └── page.tsx       # Interface de edição
│   │   │   │   │   │   └── documentos/
│   │   │   │   │   │       └── page.tsx       # Interface de documentos
│   │   │   │   │   └── novo/
│   │   │   │   │       └── page.tsx           # Interface de criação
│   │   │   │   ├── dashboard/
│   │   │   │   │   └── page.tsx               # Interface do dashboard
│   │   │   │   ├── alertas/
│   │   │   │   │   └── page.tsx               # Interface de alertas
│   │   │   │   ├── relatorios/
│   │   │   │   │   └── page.tsx               # Interface de relatórios
│   │   │   │   └── auditoria/
│   │   │   │       └── page.tsx               # Interface de auditoria
│   │   │   └── globals.css                    # Estilos das páginas
│   ├── components/                            # Componentes visuais
│   │   ├── ui/                               # Componentes base IGRP
│   │   │   ├── Button/
│   │   │   │   ├── Button.tsx
│   │   │   │   ├── ButtonGroup.tsx
│   │   │   │   ├── IconButton.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Input/
│   │   │   │   ├── Input.tsx
│   │   │   │   ├── InputGroup.tsx
│   │   │   │   ├── SearchInput.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Card/
│   │   │   │   ├── Card.tsx
│   │   │   │   ├── CardHeader.tsx
│   │   │   │   ├── CardContent.tsx
│   │   │   │   ├── CardActions.tsx
│   │   │   │   └── index.ts
│   │   │   ├── DataGrid/
│   │   │   │   ├── DataGrid.tsx
│   │   │   │   ├── DataGridHeader.tsx
│   │   │   │   ├── DataGridRow.tsx
│   │   │   │   ├── DataGridPagination.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Modal/
│   │   │   │   ├── Modal.tsx
│   │   │   │   ├── ModalHeader.tsx
│   │   │   │   ├── ModalContent.tsx
│   │   │   │   ├── ModalActions.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Tabs/
│   │   │   │   ├── Tabs.tsx
│   │   │   │   ├── TabList.tsx
│   │   │   │   ├── TabPanel.tsx
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   ├── forms/                            # Formulários específicos
│   │   │   ├── LicenseSearchForm/
│   │   │   │   ├── LicenseSearchForm.tsx
│   │   │   │   ├── AdvancedFilters.tsx
│   │   │   │   ├── QuickFilters.tsx
│   │   │   │   └── index.ts
│   │   │   ├── EstablishmentForm/
│   │   │   │   ├── EstablishmentForm.tsx
│   │   │   │   ├── BasicInfoSection.tsx
│   │   │   │   ├── LocationSection.tsx
│   │   │   │   ├── DocumentsSection.tsx
│   │   │   │   └── index.ts
│   │   │   ├── RenewalForm/
│   │   │   │   ├── RenewalWizard.tsx
│   │   │   │   ├── StepIndicator.tsx
│   │   │   │   ├── DocumentUploadStep.tsx
│   │   │   │   ├── ReviewStep.tsx
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   ├── business/                         # Componentes de negócio
│   │   │   ├── LicenseCard/
│   │   │   │   ├── LicenseCard.tsx
│   │   │   │   ├── LicenseStatus.tsx
│   │   │   │   ├── LicenseActions.tsx
│   │   │   │   └── index.ts
│   │   │   ├── EstablishmentCard/
│   │   │   │   ├── EstablishmentCard.tsx
│   │   │   │   ├── LocationInfo.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Dashboard/
│   │   │   │   ├── MetricsCards.tsx
│   │   │   │   ├── AlertsPanel.tsx
│   │   │   │   ├── ExpirationChart.tsx
│   │   │   │   ├── RecentActivity.tsx
│   │   │   │   └── index.ts
│   │   │   ├── StatusBadge/
│   │   │   │   ├── StatusBadge.tsx
│   │   │   │   ├── ExpirationBadge.tsx
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   └── layout/                           # Componentes de navegação
│   │       ├── PageHeader/
│   │       │   ├── PageHeader.tsx
│   │       │   ├── Breadcrumb.tsx
│   │       │   ├── PageActions.tsx
│   │       │   └── index.ts
│   │       ├── Navigation/
│   │       │   ├── MainNavigation.tsx
│   │       │   ├── NavigationItem.tsx
│   │       │   └── index.ts
│   │       └── index.ts
│   ├── hooks/                                # Hooks para UI
│   │   ├── useFormValidation.ts              # Validação de formulários
│   │   ├── useModal.ts                       # Controle de modais
│   │   ├── usePagination.ts                  # Paginação de dados
│   │   ├── useSearch.ts                      # Funcionalidade de busca
│   │   ├── useFilters.ts                     # Filtros dinâmicos
│   │   └── index.ts
│   ├── lib/
│   │   ├── utils/                            # Utilitários de UI
│   │   │   ├── formatters.ts                 # Formatação de dados
│   │   │   ├── validators.ts                 # Validações de formulário
│   │   │   ├── constants.ts                  # Constantes da UI
│   │   │   ├── classNames.ts                 # Utilitários de CSS
│   │   │   └── index.ts
│   │   ├── types/                            # Tipos para componentes
│   │   │   ├── license.ts
│   │   │   ├── establishment.ts
│   │   │   ├── forms.ts
│   │   │   ├── ui.ts
│   │   │   └── index.ts
│   │   └── config/
│   │       ├── routes.ts                     # Configuração de rotas
│   │       ├── navigation.ts                 # Estrutura de navegação
│   │       └── ui-constants.ts               # Constantes da interface
│   └── styles/                               # Estilos customizados
│       ├── globals.css                       # Estilos globais
│       ├── components.css                    # Estilos de componentes
│       ├── forms.css                         # Estilos de formulários
│       └── themes/
│           ├── license-theme.css             # Tema específico
│           └── government-colors.css         # Cores governamentais
├── public/
│   ├── icons/                                # Ícones da interface
│   │   ├── license-status/
│   │   ├── actions/
│   │   └── navigation/
│   └── images/
│       ├── placeholders/
│       ├── logos/
│       └── backgrounds/
└── docs/
    ├── components/                           # Documentação de componentes
    │   ├── ui-components.md
    │   ├── form-components.md
    │   └── business-components.md
    └── style-guide/
        ├── design-tokens.md
        ├── component-patterns.md
        └── accessibility.md
```

### 3.2 Responsabilidades dos Componentes

#### Componentes UI (Base IGRP)

- **Button**: Botões de ação, grupos de botões, botões com ícones
- **Input**: Campos de entrada, grupos de campos, campos de busca
- **Card**: Containers de conteúdo, cabeçalhos, ações
- **DataGrid**: Tabelas de dados, paginação, ordenação
- **Modal**: Diálogos, confirmações, formulários modais
- **Tabs**: Navegação por abas, painéis de conteúdo

#### Componentes de Formulário

- **LicenseSearchForm**: Busca avançada, filtros rápidos, filtros avançados
- **EstablishmentForm**: Informações básicas, localização, documentos
- **RenewalForm**: Wizard de renovação, indicador de progresso, upload de documentos

#### Componentes de Negócio

- **LicenseCard**: Exibição de licenças, status, ações contextuais
- **EstablishmentCard**: Informações de estabelecimento, localização
- **Dashboard**: Métricas, alertas, gráficos, atividades recentes
- **StatusBadge**: Indicadores de status, badges de expiração

## 4. Componentização Visual

### 4.1 Componentes de Interface

#### Componentes Base IGRP

```typescript
// components/ui/Button/Button.tsx
import { Button as IGRPButton } from '@igrp/igrp-framework-react-design-system';

interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'danger' | 'success';
  size?: 'sm' | 'md' | 'lg';
  icon?: string;
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  icon,
  children,
  onClick,
  disabled = false
}) => {
  return (
    <IGRPButton
      variant={variant}
      size={size}
      icon={icon}
      onClick={onClick}
      disabled={disabled}
      className={`license-button license-button--${variant}`}
    >
      {children}
    </IGRPButton>
  );
};
```

#### Componentes de Formulário Visual

```typescript
// components/forms/LicenseSearchForm/LicenseSearchForm.tsx
import { Input, Select, DatePicker } from '@igrp/igrp-framework-react-design-system';
import { Button } from '@/components/ui/Button';

interface SearchFormProps {
  onSearch: (filters: SearchFilters) => void;
  onClear: () => void;
}

export const LicenseSearchForm: React.FC<SearchFormProps> = ({
  onSearch,
  onClear
}) => {
  return (
    <div className="bg-white p-6 rounded-lg shadow-sm border">
      <h3 className="text-lg font-semibold mb-4">Buscar Licenças</h3>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
        <Input
          label="Número da Licença"
          placeholder="Digite o número..."
          className="license-search-input"
        />

        <Select
          label="Tipo de Licença"
          placeholder="Selecione o tipo..."
          options={licenseTypeOptions}
          className="license-search-select"
        />

        <Select
          label="Status"
          placeholder="Selecione o status..."
          options={statusOptions}
          className="license-search-select"
        />
      </div>

      <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
        <DatePicker
          label="Data de Emissão (De)"
          className="license-date-picker"
        />

        <DatePicker
          label="Data de Emissão (Até)"
          className="license-date-picker"
        />
      </div>

      <div className="flex gap-3">
        <Button variant="primary" icon="search" onClick={onSearch}>
          Buscar
        </Button>
        <Button variant="secondary" icon="clear" onClick={onClear}>
          Limpar
        </Button>
      </div>
    </div>
  );
};
```

#### Componentes de Exibição de Dados

```typescript
// components/business/LicenseCard/LicenseCard.tsx
import { Card, Badge } from '@igrp/igrp-framework-react-design-system';
import { Button } from '@/components/ui/Button';
import { StatusBadge } from '@/components/business/StatusBadge';

interface LicenseCardProps {
  license: {
    id: string;
    number: string;
    type: string;
    holder: string;
    status: string;
    issueDate: string;
    expiryDate: string;
  };
  onView: () => void;
  onEdit: () => void;
  onRenew: () => void;
}

export const LicenseCard: React.FC<LicenseCardProps> = ({
  license,
  onView,
  onEdit,
  onRenew
}) => {
  return (
    <Card className="license-card p-6 hover:shadow-md transition-all duration-200">
      {/* Cabeçalho do Card */}
      <div className="flex justify-between items-start mb-4">
        <div className="license-card__header">
          <h3 className="text-lg font-semibold text-gray-900">
            {license.number}
          </h3>
          <p className="text-sm text-gray-600 mt-1">
            {license.holder}
          </p>
        </div>
        <StatusBadge status={license.status} />
      </div>

      {/* Informações da Licença */}
      <div className="license-card__info space-y-2 mb-4">
        <div className="flex justify-between">
          <span className="text-sm text-gray-500">Tipo:</span>
          <span className="text-sm font-medium">{license.type}</span>
        </div>
        <div className="flex justify-between">
          <span className="text-sm text-gray-500">Emissão:</span>
          <span className="text-sm">{license.issueDate}</span>
        </div>
        <div className="flex justify-between">
          <span className="text-sm text-gray-500">Validade:</span>
          <span className="text-sm">{license.expiryDate}</span>
        </div>
      </div>

      {/* Ações do Card */}
      <div className="license-card__actions flex gap-2 pt-4 border-t">
        <Button size="sm" variant="secondary" icon="eye" onClick={onView}>
          Ver
        </Button>
        <Button size="sm" variant="secondary" icon="edit" onClick={onEdit}>
          Editar
        </Button>
        <Button size="sm" variant="primary" icon="refresh" onClick={onRenew}>
          Renovar
        </Button>
      </div>
    </Card>
  );
};
```

### 4.2 Hooks para Interface

```typescript
// hooks/useFormValidation.ts
import { useState, useCallback } from 'react';

interface ValidationRule {
  required?: boolean;
  minLength?: number;
  maxLength?: number;
  pattern?: RegExp;
  message: string;
}

interface ValidationRules {
  [key: string]: ValidationRule[];
}

export const useFormValidation = (rules: ValidationRules) => {
  const [errors, setErrors] = useState<Record<string, string>>({});
  const [touched, setTouched] = useState<Record<string, boolean>>({});

  const validateField = useCallback(
    (name: string, value: any) => {
      const fieldRules = rules[name] || [];

      for (const rule of fieldRules) {
        if (rule.required && (!value || value.toString().trim() === '')) {
          return rule.message;
        }

        if (rule.minLength && value.length < rule.minLength) {
          return rule.message;
        }

        if (rule.maxLength && value.length > rule.maxLength) {
          return rule.message;
        }

        if (rule.pattern && !rule.pattern.test(value)) {
          return rule.message;
        }
      }

      return null;
    },
    [rules],
  );

  const validate = useCallback(
    (formData: Record<string, any>) => {
      const newErrors: Record<string, string> = {};

      Object.keys(rules).forEach((fieldName) => {
        const error = validateField(fieldName, formData[fieldName]);
        if (error) {
          newErrors[fieldName] = error;
        }
      });

      setErrors(newErrors);
      return Object.keys(newErrors).length === 0;
    },
    [validateField],
  );

  const setFieldTouched = useCallback((fieldName: string) => {
    setTouched((prev) => ({ ...prev, [fieldName]: true }));
  }, []);

  return {
    errors,
    touched,
    validate,
    validateField,
    setFieldTouched,
    hasErrors: Object.keys(errors).length > 0,
  };
};
```

```typescript
// hooks/useModal.ts
import { useState, useCallback } from 'react';

interface ModalState {
  isOpen: boolean;
  data?: any;
}

export const useModal = (initialState: boolean = false) => {
  const [state, setState] = useState<ModalState>({
    isOpen: initialState,
    data: null,
  });

  const openModal = useCallback((data?: any) => {
    setState({ isOpen: true, data });
  }, []);

  const closeModal = useCallback(() => {
    setState({ isOpen: false, data: null });
  }, []);

  const toggleModal = useCallback(() => {
    setState((prev) => ({
      isOpen: !prev.isOpen,
      data: prev.isOpen ? null : prev.data,
    }));
  }, []);

  return {
    isOpen: state.isOpen,
    data: state.data,
    openModal,
    closeModal,
    toggleModal,
  };
};
```

## 5. Integração Visual com IGRP

### 5.1 Configuração Visual do Design System

```typescript
// src/styles/igrp-visual-theme.ts
import { IGRPVisualConfig } from '@igrp/igrp-framework-react-design-system';

export const licenseVisualTheme: IGRPVisualConfig = {
  // Paleta de cores para interface de licenciamento
  colors: {
    primary: {
      main: '#1976d2', // Azul principal - botões primários
      light: '#42a5f5', // Azul claro - hover states
      dark: '#1565c0', // Azul escuro - active states
      contrast: '#ffffff', // Texto em botões primários
    },
    secondary: {
      main: '#dc004e', // Vermelho - ações secundárias
      light: '#ff5983', // Vermelho claro - hover
      dark: '#9a0036', // Vermelho escuro - active
      contrast: '#ffffff',
    },
    status: {
      success: '#2e7d32', // Verde - licenças ativas
      warning: '#ed6c02', // Laranja - licenças próximas ao vencimento
      error: '#d32f2f', // Vermelho - licenças vencidas
      info: '#0288d1', // Azul - informações gerais
    },
    background: {
      default: '#fafafa', // Fundo principal das páginas
      paper: '#ffffff', // Fundo de cards e modais
      elevated: '#f5f5f5', // Fundo de elementos elevados
    },
    text: {
      primary: '#212121', // Texto principal
      secondary: '#757575', // Texto secundário
      disabled: '#bdbdbd', // Texto desabilitado
      hint: '#9e9e9e', // Texto de dica
    },
    divider: '#e0e0e0', // Linhas divisórias
  },

  // Tipografia específica para licenciamento
  typography: {
    fontFamily: 'Roboto, "Helvetica Neue", Arial, sans-serif',
    variants: {
      pageTitle: {
        fontSize: '2.5rem',
        fontWeight: 600,
        lineHeight: 1.2,
        color: '#212121',
      },
      sectionTitle: {
        fontSize: '2rem',
        fontWeight: 600,
        lineHeight: 1.3,
        color: '#212121',
      },
      cardTitle: {
        fontSize: '1.25rem',
        fontWeight: 500,
        lineHeight: 1.4,
        color: '#212121',
      },
      body: {
        fontSize: '1rem',
        fontWeight: 400,
        lineHeight: 1.5,
        color: '#212121',
      },
      caption: {
        fontSize: '0.875rem',
        fontWeight: 400,
        lineHeight: 1.4,
        color: '#757575',
      },
      label: {
        fontSize: '0.875rem',
        fontWeight: 500,
        lineHeight: 1.4,
        color: '#212121',
      },
    },
  },

  // Espaçamentos padronizados
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
    lg: '24px',
    xl: '32px',
    xxl: '48px',
  },

  // Bordas e sombras
  elevation: {
    card: '0 2px 4px rgba(0,0,0,0.1)',
    modal: '0 8px 32px rgba(0,0,0,0.12)',
    dropdown: '0 4px 16px rgba(0,0,0,0.1)',
  },

  borderRadius: {
    sm: '4px',
    md: '8px',
    lg: '12px',
  },
};
```

### 5.2 Componentes Visuais IGRP

```typescript
// components/ui/LicenseDataGrid/LicenseDataGrid.tsx
import {
  DataGrid,
  GridColDef,
  Chip,
  IconButton,
  Tooltip
} from '@igrp/igrp-framework-react-design-system';
import { Eye, Download, Edit } from 'lucide-react';
import { License } from '@/types/license';

interface LicenseDataGridProps {
  licenses: License[];
  loading?: boolean;
  onViewLicense?: (license: License) => void;
  onEditLicense?: (license: License) => void;
  onDownloadLicense?: (license: License) => void;
}

export const LicenseDataGrid: React.FC<LicenseDataGridProps> = ({
  licenses,
  loading = false,
  onViewLicense,
  onEditLicense,
  onDownloadLicense,
}) => {
  const columns: GridColDef[] = [
    {
      field: 'numero',
      headerName: 'Número',
      width: 140,
      renderCell: (params) => (
        <span className="font-mono text-blue-600 font-medium">
          {params.value}
        </span>
      ),
    },
    {
      field: 'tipo',
      headerName: 'Tipo de Licença',
      width: 180,
      renderCell: (params) => (
        <span className="capitalize">{params.value}</span>
      ),
    },
    {
      field: 'estabelecimento',
      headerName: 'Estabelecimento',
      width: 220,
      renderCell: (params) => (
        <div className="truncate" title={params.row.estabelecimento?.nome}>
          <div className="font-medium">{params.row.estabelecimento?.nome}</div>
          <div className="text-sm text-gray-500">
            {params.row.estabelecimento?.localizacao?.ilha}
          </div>
        </div>
      ),
    },
    {
      field: 'status',
      headerName: 'Status',
      width: 120,
      renderCell: (params) => (
        <Chip
          label={params.value}
          color={getStatusColor(params.value)}
          size="small"
          variant="filled"
        />
      ),
    },
    {
      field: 'dataVencimento',
      headerName: 'Vencimento',
      width: 130,
      renderCell: (params) => {
        const date = new Date(params.value);
        const isExpired = date < new Date();
        const isNearExpiry = date < new Date(Date.now() + 30 * 24 * 60 * 60 * 1000);

        return (
          <span
            className={`
              ${isExpired ? 'text-red-600 font-medium' : ''}
              ${isNearExpiry && !isExpired ? 'text-orange-600 font-medium' : ''}
              ${!isNearExpiry && !isExpired ? 'text-gray-600' : ''}
            `}
          >
            {date.toLocaleDateString('pt-CV')}
          </span>
        );
      },
    },
    {
      field: 'actions',
      headerName: 'Ações',
      width: 140,
      sortable: false,
      renderCell: (params) => (
        <div className="flex gap-1">
          <Tooltip title="Visualizar">
            <IconButton
              size="small"
              onClick={() => onViewLicense?.(params.row)}
              className="text-blue-600 hover:bg-blue-50"
            >
              <Eye size={16} />
            </IconButton>
          </Tooltip>

          <Tooltip title="Editar">
            <IconButton
              size="small"
              onClick={() => onEditLicense?.(params.row)}
              className="text-gray-600 hover:bg-gray-50"
            >
              <Edit size={16} />
            </IconButton>
          </Tooltip>

          <Tooltip title="Download">
            <IconButton
              size="small"
              onClick={() => onDownloadLicense?.(params.row)}
              className="text-green-600 hover:bg-green-50"
            >
              <Download size={16} />
            </IconButton>
          </Tooltip>
        </div>
      ),
    },
  ];

  return (
    <div className="w-full bg-white rounded-lg border border-gray-200">
      <DataGrid
        rows={licenses}
        columns={columns}
        loading={loading}
        pageSize={25}
        rowsPerPageOptions={[10, 25, 50, 100]}
        disableSelectionOnClick
        autoHeight
        className="border-0"
        sx={{
          '& .MuiDataGrid-cell': {
            borderBottom: '1px solid #f0f0f0',
            padding: '12px 16px',
          },
          '& .MuiDataGrid-columnHeaders': {
            backgroundColor: '#fafafa',
            borderBottom: '2px solid #e0e0e0',
            fontSize: '0.875rem',
            fontWeight: 600,
          },
          '& .MuiDataGrid-row': {
            '&:hover': {
              backgroundColor: '#f9f9f9',
            },
          },
        }}
      />
    </div>
  );
};

const getStatusColor = (status: string) => {
  switch (status.toLowerCase()) {
    case 'ativa':
      return 'success';
    case 'vencida':
      return 'error';
    case 'suspensa':
      return 'warning';
    case 'cancelada':
      return 'default';
    case 'pendente':
      return 'info';
    default:
      return 'default';
  }
};
```

### 5.3 Componentes de Formulário IGRP

```typescript
// components/ui/LicenseForm/LicenseFormFields.tsx
import React, { useState } from 'react';
import {
  TextField,
  Select,
  MenuItem,
  FormControl,
  InputLabel,
  DatePicker,
  Autocomplete,
  FormHelperText,
  Button,
} from '@igrp/igrp-framework-react-design-system';
import { License, LicenseType, Establishment } from '@/types/license';
import { useFormValidation } from '@/hooks/useFormValidation';

interface LicenseFormFieldsProps {
  license?: Partial<License>;
  onSubmit: (data: Partial<License>) => void;
  loading?: boolean;
  establishments: Establishment[];
  licenseTypes: LicenseType[];
}

export const LicenseFormFields: React.FC<LicenseFormFieldsProps> = ({
  license,
  onSubmit,
  loading = false,
  establishments,
  licenseTypes,
}) => {
  const [formData, setFormData] = useState<Partial<License>>({
    numero: license?.numero || '',
    tipo: license?.tipo || '',
    estabelecimentoId: license?.estabelecimentoId || '',
    dataEmissao: license?.dataEmissao || new Date(),
    dataVencimento: license?.dataVencimento || new Date(),
    observacoes: license?.observacoes || '',
    ...license,
  });

  const { errors, touched, validate, validateField, setFieldTouched } = useFormValidation({
    numero: (value: string) => {
      if (!value) return 'Número da licença é obrigatório';
      if (value.length < 8) return 'Número deve ter pelo menos 8 caracteres';
      return null;
    },
    tipo: (value: string) => {
      if (!value) return 'Tipo de licença é obrigatório';
      return null;
    },
    estabelecimentoId: (value: string) => {
      if (!value) return 'Estabelecimento é obrigatório';
      return null;
    },
    dataVencimento: (value: Date) => {
      if (!value) return 'Data de vencimento é obrigatória';
      if (value <= new Date()) return 'Data de vencimento deve ser futura';
      return null;
    },
  });

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (validate(formData)) {
      onSubmit(formData);
    }
  };

  const handleFieldChange = (field: string, value: any) => {
    setFormData(prev => ({ ...prev, [field]: value }));
    setFieldTouched(field);
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-6">
      <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
        {/* Número da Licença */}
        <FormControl fullWidth error={touched.numero && !!errors.numero}>
          <TextField
            label="Número da Licença"
            value={formData.numero}
            onChange={(e) => handleFieldChange('numero', e.target.value)}
            onBlur={() => setFieldTouched('numero')}
            error={touched.numero && !!errors.numero}
            helperText={touched.numero && errors.numero}
            placeholder="Ex: LIC-2024-001234"
            disabled={loading}
            required
          />
        </FormControl>

        {/* Tipo de Licença */}
        <FormControl fullWidth error={touched.tipo && !!errors.tipo}>
          <InputLabel>Tipo de Licença</InputLabel>
          <Select
            value={formData.tipo}
            onChange={(e) => handleFieldChange('tipo', e.target.value)}
            onBlur={() => setFieldTouched('tipo')}
            label="Tipo de Licença"
            disabled={loading}
            required
          >
            {licenseTypes.map((type) => (
              <MenuItem key={type.id} value={type.codigo}>
                {type.nome}
              </MenuItem>
            ))}
          </Select>
          {touched.tipo && errors.tipo && (
            <FormHelperText>{errors.tipo}</FormHelperText>
          )}
        </FormControl>

        {/* Estabelecimento */}
        <FormControl fullWidth error={touched.estabelecimentoId && !!errors.estabelecimentoId}>
          <Autocomplete
            options={establishments}
            getOptionLabel={(option) => option.nome}
            value={establishments.find(est => est.id === formData.estabelecimentoId) || null}
            onChange={(_, newValue) => handleFieldChange('estabelecimentoId', newValue?.id || '')}
            onBlur={() => setFieldTouched('estabelecimentoId')}
            renderInput={(params) => (
              <TextField
                {...params}
                label="Estabelecimento"
                error={touched.estabelecimentoId && !!errors.estabelecimentoId}
                helperText={touched.estabelecimentoId && errors.estabelecimentoId}
                required
              />
            )}
            disabled={loading}
          />
        </FormControl>

        {/* Data de Vencimento */}
        <FormControl fullWidth error={touched.dataVencimento && !!errors.dataVencimento}>
          <DatePicker
            label="Data de Vencimento"
            value={formData.dataVencimento}
            onChange={(date) => handleFieldChange('dataVencimento', date)}
            onBlur={() => setFieldTouched('dataVencimento')}
            disabled={loading}
            minDate={new Date()}
            format="dd/MM/yyyy"
            slotProps={{
              textField: {
                error: touched.dataVencimento && !!errors.dataVencimento,
                helperText: touched.dataVencimento && errors.dataVencimento,
                required: true,
              },
            }}
          />
        </FormControl>
      </div>

      {/* Observações */}
      <FormControl fullWidth>
        <TextField
          label="Observações"
          value={formData.observacoes}
          onChange={(e) => handleFieldChange('observacoes', e.target.value)}
          multiline
          rows={4}
          placeholder="Observações adicionais sobre a licença..."
          disabled={loading}
        />
      </FormControl>

      {/* Botões de Ação */}
      <div className="flex justify-end gap-4 pt-4">
        <Button
          type="button"
          variant="outlined"
          disabled={loading}
          onClick={() => window.history.back()}
        >
          Cancelar
        </Button>
        <Button
          type="submit"
          variant="contained"
          disabled={loading || Object.keys(errors).length > 0}
          loading={loading}
        >
          {license?.id ? 'Atualizar' : 'Criar'} Licença
        </Button>
      </div>
    </form>
  );
};
```

````

## 6. Cronograma de Desenvolvimento da Interface Visual

### 6.1 Fase 1: Configuração Visual e Componentes Base (Semana 1-2)

#### Sprint 1.1 - Setup Visual (5 dias)

* **Dia 1-2**: Configuração do ambiente visual

  * Setup Next.js 14 com TypeScript

  * Configuração Tailwind CSS para estilização

  * Integração visual com bibliotecas IGRP

  * Configuração do tema visual personalizado

* **Dia 3-4**: Estrutura de componentes visuais

  * Criação da estrutura de diretórios para componentes

  * Configuração de rotas para páginas

  * Setup de tipos TypeScript para interface

* **Dia 5**: Componentes visuais base

  * Configuração do tema IGRP visual

  * Paleta de cores e tipografia

  * Espaçamentos e elevações

#### Sprint 1.2 - Componentes UI Fundamentais (5 dias)

* **Dia 1-2**: Componentes de interface básicos

  * Button com variações visuais

  * Input, Select, DatePicker estilizados

  * Modal e Dialog com animações

* **Dia 3-4**: Componentes de dados visuais

  * DataGrid customizado com IGRP

  * Cards de informação

  * Chips de status com cores

* **Dia 5**: Sistema visual responsivo

  * Breakpoints e grid system

  * Componentes adaptativos

  * Testes visuais em diferentes telas

### 6.2 Fase 2: Páginas Principais da Interface (Semana 3-6)

#### Sprint 2.1 - Dashboard Visual (5 dias)

* **Dia 1-2**: Layout visual do dashboard

  * Cards de métricas com design responsivo

  * Gráficos visuais com Recharts

  * Componentes de filtros de período

* **Dia 3-4**: Componentes de dados visuais

  * Indicadores visuais de status

  * Alertas e notificações visuais

  * Animações e transições

* **Dia 5**: Responsividade e acessibilidade

  * Adaptação para mobile e tablet

  * Testes de acessibilidade visual

  * Otimização de performance visual

#### Sprint 2.2 - Interface de Consulta de Licenças (5 dias)

* **Dia 1-2**: Página de listagem visual

  * DataGrid estilizado com paginação

  * Filtros visuais avançados

  * Componente de busca textual

* **Dia 3-4**: Interações visuais da lista

  * Ordenação visual com indicadores

  * Botões de ação com tooltips

  * Estados de loading e empty state

* **Dia 5**: Refinamentos visuais

  * Hover effects e micro-interações

  * Feedback visual para ações

  * Testes de usabilidade

#### Sprint 2.3 - Interface de Detalhes de Licença (5 dias)

* **Dia 1-2**: Layout da página de detalhes

  * Visualização completa com design limpo

  * Tabs de informações estilizadas

  * Seções organizadas visualmente

* **Dia 3-4**: Componentes de documentos

  * Visualizador de documentos integrado

  * Interface de upload de arquivos

  * Indicadores visuais de validação

* **Dia 5**: Polimento visual

  * Animações de transição entre tabs

  * Estados de carregamento

  * Feedback visual para interações

#### Sprint 2.4 - Interface de Monitoramento (5 dias)

* **Dia 1-2**: Dashboard de monitoramento

  * Visualização de métricas em tempo real

  * Gráficos interativos de status

  * Filtros visuais por período e tipo

* **Dia 3-4**: Componentes de alertas visuais

  * Sistema de notificações visuais

  * Indicadores de vencimento

  * Badges e chips informativos

* **Dia 5**: Interatividade e responsividade

  * Drill-down visual nos gráficos

  * Adaptação para diferentes telas

  * Testes de performance visual

### 6.3 Fase 3: Interfaces de Processos (Semana 7-10)

#### Sprint 3.1 - Interface do Wizard de Renovação (10 dias)

* **Dia 1-3**: Design do wizard visual

  * Fluxo multi-step com indicadores visuais

  * Validações visuais por etapa

  * Barra de progresso animada

* **Dia 4-6**: Componentes de pagamento visual

  * Interface de cálculo de taxas

  * Formulários de pagamento estilizados

  * Confirmação visual de pagamento

* **Dia 7-8**: Interface de documentação

  * Componente de upload com drag & drop

  * Visualização de documentos inline

  * Indicadores visuais de validação

* **Dia 9-10**: Refinamentos e testes visuais

  * Animações de transição entre etapas

  * Estados de erro e sucesso

  * Testes de usabilidade do fluxo

#### Sprint 3.2 - Interfaces de Alterações (10 dias)

* **Dia 1-3**: Interface de alterações

  * Formulário visual de alterações

  * Comparação visual de dados (antes/depois)

  * Interface de aprovação de mudanças

* **Dia 4-6**: Interface de transferência

  * Seletor visual de novo titular

  * Checklist visual de requisitos

  * Fluxo visual de aprovação

* **Dia 7-8**: Interface de suspensão/reativação

  * Seletor visual de motivos

  * Interface de processo de reativação

  * Sistema visual de notificações

* **Dia 9-10**: Integração visual dos processos

  * Navegação fluida entre processos

  * Estados visuais consistentes

  * Testes de fluxo completo

### 6.4 Fase 4: Interface de Gestão de Estabelecimentos (Semana 11-14)

#### Sprint 4.1 - Interface de Cadastro de Estabelecimentos (10 dias)

* **Dia 1-3**: Formulário visual de cadastro

  * Formulário multi-seção com navegação visual

  * Validação visual em tempo real

  * Mapa interativo para localização

* **Dia 4-6**: Interface de documentação

  * Upload visual de documentos obrigatórios

  * Preview de documentos

  * Checklist visual de requisitos

* **Dia 7-8**: Interface de revisão

  * Resumo visual dos dados inseridos

  * Comparação com requisitos legais

  * Interface de correções

* **Dia 9-10**: Finalização visual

  * Animações de confirmação

  * Estados de processamento

  * Feedback visual de conclusão

#### Sprint 4.2 - Interface de Gestão de Estabelecimentos (10 dias)

* **Dia 1-3**: Lista visual de estabelecimentos

  * DataGrid com informações detalhadas

  * Filtros visuais por status e tipo

  * Busca visual avançada

* **Dia 4-6**: Interface de detalhes

  * Visualização completa do estabelecimento

  * Tabs organizadas por categoria

  * Histórico visual de alterações

* **Dia 7-8**: Interface de edição

  * Formulário de edição inline

  * Comparação visual de alterações

  * Aprovação visual de mudanças

* **Dia 9-10**: Integração visual

  * Navegação entre estabelecimento e licenças

  * Dashboard visual do estabelecimento

  * Relatórios visuais

### 6.5 Fase 5: Polimento e Otimização Visual (Semana 15-16)

#### Sprint 5.1 - Refinamentos Visuais (5 dias)

* **Dia 1-2**: Consistência visual

  * Padronização de componentes

  * Alinhamento de estilos

  * Correção de inconsistências

* **Dia 3-4**: Micro-interações

  * Animações de hover e focus

  * Transições suaves

  * Feedback visual imediato

* **Dia 5**: Acessibilidade visual

  * Contraste de cores

  * Tamanhos de fonte

  * Navegação por teclado

#### Sprint 5.2 - Testes Finais de Interface (5 dias)

* **Dia 1-2**: Testes de usabilidade

  * Testes com usuários reais

  * Identificação de problemas de UX

  * Correções de interface

* **Dia 3-4**: Testes de responsividade

  * Validação em diferentes dispositivos

  * Ajustes para mobile e tablet

  * Otimização de performance visual

* **Dia 5**: Documentação visual

  * Guia de componentes

  * Padrões de design

  * Manual de estilo

## 7. Resumo do Cronograma

| Fase | Duração | Foco Principal |
|------|---------|----------------|
| Fase 1 | 2 semanas | Configuração visual e componentes base |
| Fase 2 | 4 semanas | Páginas principais da interface |
| Fase 3 | 4 semanas | Interfaces de processos complexos |
| Fase 4 | 4 semanas | Interface de gestão de estabelecimentos |
| Fase 5 | 2 semanas | Polimento e otimização visual |
| **Total** | **16 semanas** | **Interface visual completa** |

### 7.1 Marcos Visuais Importantes

* **Semana 2**: Componentes base funcionais
* **Semana 6**: Páginas principais implementadas
* **Semana 10**: Processos visuais completos
* **Semana 14**: Gestão de estabelecimentos finalizada
* **Semana 16**: Interface visual otimizada e testada

## 8. Plano de Testes da Interface Visual

### 8.1 Testes de Componentes Visuais

#### Configuração de Testes Visuais

```typescript
// jest.config.js para testes visuais
const nextJest = require('next/jest');

const createJestConfig = nextJest({
  dir: './',
});

const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  testEnvironment: 'jest-environment-jsdom',
  collectCoverageFrom: [
    'src/components/**/*.{js,jsx,ts,tsx}',
    'src/pages/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.stories.{js,jsx,ts,tsx}',
  ],
};

module.exports = createJestConfig(customJestConfig);
````

#### Testes de Renderização de Componentes

```typescript
// __tests__/components/LicenseCard.test.tsx
import { render, screen } from '@testing-library/react';
import { LicenseCard } from '@/components/business/LicenseCard';
import { mockLicense } from '@/test/mocks/license';

describe('LicenseCard', () => {
  it('deve renderizar as informações da licença corretamente', () => {
    render(<LicenseCard license={mockLicense} />);

    expect(screen.getByText(mockLicense.numero)).toBeInTheDocument();
    expect(screen.getByText(mockLicense.tipo)).toBeInTheDocument();
    expect(screen.getByText(mockLicense.estabelecimento.nome)).toBeInTheDocument();
  });

  it('deve aplicar a cor correta baseada no status', () => {
    const activeLicense = { ...mockLicense, status: 'ativa' };
    render(<LicenseCard license={activeLicense} />);

    const statusChip = screen.getByText('ativa');
    expect(statusChip).toHaveClass('bg-green-100', 'text-green-800');
  });

  it('deve mostrar indicador visual para licenças próximas ao vencimento', () => {
    const nearExpiryLicense = {
      ...mockLicense,
      dataVencimento: new Date(Date.now() + 15 * 24 * 60 * 60 * 1000)
    };

    render(<LicenseCard license={nearExpiryLicense} />);

    expect(screen.getByTestId('near-expiry-indicator')).toBeInTheDocument();
  });
});
```

### 8.2 Testes de Responsividade

#### Configuração de Testes Responsivos

```typescript
// __tests__/responsive/LicenseDataGrid.test.tsx
import { render, screen } from '@testing-library/react';
import { LicenseDataGrid } from '@/components/ui/LicenseDataGrid';
import { mockLicenses } from '@/test/mocks/licenses';

// Mock do matchMedia para testes responsivos
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation(query => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});

describe('LicenseDataGrid Responsividade', () => {
  it('deve adaptar colunas para tela mobile', () => {
    // Simular tela mobile
    window.matchMedia = jest.fn().mockImplementation(query => ({
      matches: query === '(max-width: 768px)',
      media: query,
    }));

    render(<LicenseDataGrid licenses={mockLicenses} />);

    // Verificar se colunas menos importantes estão ocultas
    expect(screen.queryByText('Data de Emissão')).not.toBeInTheDocument();
    expect(screen.getByText('Número')).toBeInTheDocument();
    expect(screen.getByText('Status')).toBeInTheDocument();
  });
});
```

### 8.3 Testes de Acessibilidade Visual

```typescript
// __tests__/accessibility/LicenseForm.test.tsx
import { render, screen } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import { LicenseForm } from '@/components/forms/LicenseForm';

expect.extend(toHaveNoViolations);

describe('LicenseForm Acessibilidade', () => {
  it('deve não ter violações de acessibilidade', async () => {
    const { container } = render(<LicenseForm />);
    const results = await axe(container);

    expect(results).toHaveNoViolations();
  });

  it('deve ter labels apropriados para todos os campos', () => {
    render(<LicenseForm />);

    expect(screen.getByLabelText('Tipo de Licença')).toBeInTheDocument();
    expect(screen.getByLabelText('Número da Licença')).toBeInTheDocument();
    expect(screen.getByLabelText('Data de Emissão')).toBeInTheDocument();
  });

  it('deve ter contraste adequado nas cores', () => {
    render(<LicenseForm />);

    const submitButton = screen.getByRole('button', { name: /salvar/i });
    const styles = window.getComputedStyle(submitButton);

    // Verificar se o contraste atende aos padrões WCAG
    expect(styles.backgroundColor).toBe('rgb(25, 118, 210)'); // Azul primário
    expect(styles.color).toBe('rgb(255, 255, 255)'); // Texto branco
  });
});
```

### 8.4 Testes de Interação Visual

```typescript
// __tests__/interactions/LicenseWizard.test.tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LicenseWizard } from '@/components/wizards/LicenseWizard';

describe('LicenseWizard Interações', () => {
  it('deve mostrar progresso visual ao navegar entre etapas', async () => {
    const user = userEvent.setup();
    render(<LicenseWizard />);

    // Verificar etapa inicial
    expect(screen.getByTestId('step-indicator-1')).toHaveClass('active');
    expect(screen.getByTestId('progress-bar')).toHaveStyle('width: 25%');

    // Navegar para próxima etapa
    await user.click(screen.getByRole('button', { name: /próximo/i }));

    await waitFor(() => {
      expect(screen.getByTestId('step-indicator-2')).toHaveClass('active');
      expect(screen.getByTestId('progress-bar')).toHaveStyle('width: 50%');
    });
  });

  it('deve mostrar animação de loading durante validação', async () => {
    const user = userEvent.setup();
    render(<LicenseWizard />);

    // Preencher formulário
    await user.type(screen.getByLabelText('Número da Licença'), 'LIC-2024-001');

    // Submeter e verificar loading
    await user.click(screen.getByRole('button', { name: /validar/i }));

    expect(screen.getByTestId('validation-spinner')).toBeInTheDocument();
    expect(screen.getByText('Validando informações...')).toBeInTheDocument();
  });
});
```

## 7. Plano de Testes

### 7.1 Testes Unitários

#### Configuração Jest + Testing Library

```typescript
// jest.config.js
const nextJest = require('next/jest');

const createJestConfig = nextJest({
  dir: './',
});

const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  testEnvironment: 'jest-environment-jsdom',
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/**/*.stories.{js,jsx,ts,tsx}',
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
};

module.exports = createJestConfig(customJestConfig);
```

#### Exemplo de Teste de Componente

```typescript
// tests/components/LicenseCard.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { LicenseCard } from '@/components/business/LicenseCard';
import { mockLicense } from '../__mocks__/license';

describe('LicenseCard', () => {
  const mockProps = {
    license: mockLicense,
    onView: jest.fn(),
    onEdit: jest.fn(),
    onRenew: jest.fn(),
  };

  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should render license information correctly', () => {
    render(<LicenseCard {...mockProps} />);

    expect(screen.getByText(mockLicense.licenseNumber)).toBeInTheDocument();
    expect(screen.getByText(mockLicense.holder.name)).toBeInTheDocument();
    expect(screen.getByText(mockLicense.licenseType.name)).toBeInTheDocument();
  });

  it('should call onView when view button is clicked', () => {
    render(<LicenseCard {...mockProps} />);

    const viewButton = screen.getByRole('button', { name: /visualizar/i });
    fireEvent.click(viewButton);

    expect(mockProps.onView).toHaveBeenCalledWith(mockLicense.id);
  });

  it('should show expiring soon badge for licenses expiring within 30 days', () => {
    const expiringLicense = {
      ...mockLicense,
      expiryDate: new Date(Date.now() + 15 * 24 * 60 * 60 * 1000), // 15 days from now
    };

    render(<LicenseCard {...mockProps} license={expiringLicense} />);

    expect(screen.getByText('Vence em breve')).toBeInTheDocument();
  });
});
```

#### Teste de Custom Hook

```typescript
// tests/hooks/useLicenses.test.ts
import { renderHook, waitFor } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { useLicenses } from '@/hooks/useLicenses';
import { licensesApi } from '@/services/api/licenses';

jest.mock('@/services/api/licenses');

const createWrapper = () => {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { retry: false },
      mutations: { retry: false },
    },
  });

  return ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  );
};

describe('useLicenses', () => {
  it('should fetch licenses successfully', async () => {
    const mockLicenses = [{ id: '1', licenseNumber: 'LIC001' }];
    (licensesApi.search as jest.Mock).mockResolvedValue({
      data: mockLicenses,
      total: 1,
    });

    const { result } = renderHook(() => useLicenses(), {
      wrapper: createWrapper(),
    });

    await waitFor(() => {
      expect(result.current.isSuccess).toBe(true);
    });

    expect(result.current.data?.data).toEqual(mockLicenses);
  });
});
```

### 7.2 Testes de Integração

#### Configuração MSW (Mock Service Worker)

```typescript
// tests/mocks/handlers.ts
import { rest } from 'msw';
import { mockLicenses, mockLicense } from '../__mocks__/license';

export const handlers = [
  rest.get('/api/v1/licenses', (req, res, ctx) => {
    const page = req.url.searchParams.get('page') || '0';
    const size = req.url.searchParams.get('size') || '25';

    return res(
      ctx.status(200),
      ctx.json({
        content: mockLicenses,
        totalElements: mockLicenses.length,
        totalPages: 1,
        number: parseInt(page),
        size: parseInt(size),
      }),
    );
  }),

  rest.get('/api/v1/licenses/:id', (req, res, ctx) => {
    const { id } = req.params;
    const license = mockLicenses.find((l) => l.id === id);

    if (!license) {
      return res(ctx.status(404));
    }

    return res(ctx.status(200), ctx.json(license));
  }),

  rest.post('/api/v1/licenses', (req, res, ctx) => {
    return res(ctx.status(201), ctx.json({ ...mockLicense, id: 'new-id' }));
  }),
];
```

#### Teste de Fluxo Completo

```typescript
// tests/integration/license-management.test.tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { LicensesPage } from '@/app/(igrp)/(generated)/licencas/page';
import { server } from '../mocks/server';

const renderWithProviders = (component: React.ReactElement) => {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { retry: false },
      mutations: { retry: false },
    },
  });

  return render(
    <QueryClientProvider client={queryClient}>
      {component}
    </QueryClientProvider>
  );
};

describe('License Management Integration', () => {
  beforeAll(() => server.listen());
  afterEach(() => server.resetHandlers());
  afterAll(() => server.close());

  it('should display licenses list and allow filtering', async () => {
    const user = userEvent.setup();
    renderWithProviders(<LicensesPage />);

    // Wait for licenses to load
    await waitFor(() => {
      expect(screen.getByText('LIC001')).toBeInTheDocument();
    });

    // Test search functionality
    const searchInput = screen.getByPlaceholderText('Buscar licenças...');
    await user.type(searchInput, 'LIC001');

    await waitFor(() => {
      expect(screen.getByText('LIC001')).toBeInTheDocument();
      expect(screen.queryByText('LIC002')).not.toBeInTheDocument();
    });
  });

  it('should navigate to license details when clicking on a license', async () => {
    const user = userEvent.setup();
    renderWithProviders(<LicensesPage />);

    await waitFor(() => {
      expect(screen.getByText('LIC001')).toBeInTheDocument();
    });

    const viewButton = screen.getByRole('button', { name: /visualizar/i });
    await user.click(viewButton);

    // Verify navigation (would need router mock in real scenario)
    expect(mockRouter.push).toHaveBeenCalledWith('/licencas/1');
  });
});
```

### 7.3 Testes E2E com Playwright

#### Configuração Playwright

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

#### Teste E2E de Fluxo Completo

```typescript
// tests/e2e/license-renewal-flow.spec.ts
import { test, expect } from '@playwright/test';

test.describe('License Renewal Flow', () => {
  test.beforeEach(async ({ page }) => {
    // Login
    await page.goto('/login');
    await page.fill('[data-testid="email"]', 'admin@gov.cv');
    await page.fill('[data-testid="password"]', 'password123');
    await page.click('[data-testid="login-button"]');

    // Wait for dashboard
    await expect(page.locator('[data-testid="dashboard"]')).toBeVisible();
  });

  test('should complete license renewal process', async ({ page }) => {
    // Navigate to licenses
    await page.click('[data-testid="nav-licenses"]');
    await expect(page.locator('[data-testid="licenses-grid"]')).toBeVisible();

    // Find and click on a license to renew
    await page.click('[data-testid="license-card-1"] [data-testid="renew-button"]');

    // Step 1: License Information Review
    await expect(page.locator('[data-testid="renewal-step-1"]')).toBeVisible();
    await expect(page.locator('text=Informações da Licença')).toBeVisible();
    await page.click('[data-testid="next-step"]');

    // Step 2: Document Upload
    await expect(page.locator('[data-testid="renewal-step-2"]')).toBeVisible();
    await page.setInputFiles('[data-testid="document-upload"]', 'tests/fixtures/document.pdf');
    await expect(page.locator('text=document.pdf')).toBeVisible();
    await page.click('[data-testid="next-step"]');

    // Step 3: Fee Payment
    await expect(page.locator('[data-testid="renewal-step-3"]')).toBeVisible();
    await expect(page.locator('text=Taxa de Renovação')).toBeVisible();
    await page.click('[data-testid="payment-method-card"]');
    await page.fill('[data-testid="card-number"]', '4111111111111111');
    await page.fill('[data-testid="card-expiry"]', '12/25');
    await page.fill('[data-testid="card-cvv"]', '123');
    await page.click('[data-testid="process-payment"]');

    // Step 4: Confirmation
    await expect(page.locator('[data-testid="renewal-success"]')).toBeVisible();
    await expect(page.locator('text=Renovação processada com sucesso')).toBeVisible();

    // Verify renewal appears in history
    await page.click('[data-testid="view-license"]');
    await page.click('[data-testid="history-tab"]');
    await expect(page.locator('text=Renovação aprovada')).toBeVisible();
  });

  test('should handle renewal validation errors', async ({ page }) => {
    await page.goto('/licencas/expired-license/renovar');

    // Try to renew expired license
    await page.click('[data-testid="next-step"]');

    // Should show validation error
    await expect(page.locator('[data-testid="error-message"]')).toBeVisible();
    await expect(page.locator('text=Licença expirada há mais de 90 dias')).toBeVisible();
  });
});
```

### 7.4 Testes de Performance

#### Lighthouse CI Configuration

```json
// lighthouserc.json
{
  "ci": {
    "collect": {
      "url": [
        "http://localhost:3000/",
        "http://localhost:3000/licencas",
        "http://localhost:3000/dashboard",
        "http://localhost:3000/estabelecimentos"
      ],
      "numberOfRuns": 3
    },
    "assert": {
      "assertions": {
        "categories:performance": ["error", { "minScore": 0.8 }],
        "categories:accessibility": ["error", { "minScore": 0.9 }],
        "categories:best-practices": ["error", { "minScore": 0.9 }],
        "categories:seo": ["error", { "minScore": 0.8 }]
      }
    },
    "upload": {
      "target": "temporary-public-storage"
    }
  }
}
```

### 7.5 Cobertura de Testes

#### Metas de Cobertura

- **Componentes UI**: 90% de cobertura

- **Custom Hooks**: 95% de cobertura

- **Serviços de API**: 85% de cobertura

- **Utilitários**: 100% de cobertura

- **Fluxos E2E**: 80% dos cenários críticos

#### Relatórios de Cobertura

```json
// package.json scripts
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:lighthouse": "lhci autorun",
    "test:all": "npm run test:coverage && npm run test:e2e && npm run test:lighthouse"
  }
}
```

## 8. Especificação de Rotas Dinâmicas

### 8.1 Estrutura de Rotas IGRP

Conforme especificado na arquitetura técnica, as rotas serão implementadas no diretório `src/app/(igrp)/(generated)` seguindo o padrão Next.js 14 App Router:

```
src/app/(igrp)/(generated)/
├── licencas/
│   ├── page.tsx                    # GET /licencas
│   ├── loading.tsx                 # Loading state
│   ├── error.tsx                   # Error boundary
│   └── [id]/
│       ├── page.tsx                # GET /licencas/[id]
│       ├── renovar/
│       │   └── page.tsx            # GET /licencas/[id]/renovar
│       ├── alterar/
│       │   └── page.tsx            # GET /licencas/[id]/alterar
│       ├── transferir/
│       │   └── page.tsx            # GET /licencas/[id]/transferir
│       ├── suspender/
│       │   └── page.tsx            # GET /licencas/[id]/suspender
│       └── historico/
│           └── page.tsx            # GET /licencas/[id]/historico
├── estabelecimentos/
│   ├── page.tsx                    # GET /estabelecimentos
│   ├── novo/
│   │   └── page.tsx                # GET /estabelecimentos/novo
│   └── [id]/
│       ├── page.tsx                # GET /estabelecimentos/[id]
│       ├── editar/
│       │   └── page.tsx            # GET /estabelecimentos/[id]/editar
│       └── documentos/
│           └── page.tsx            # GET /estabelecimentos/[id]/documentos
├── dashboard/
│   └── page.tsx                    # GET /dashboard
├── alertas/
│   └── page.tsx                    # GET /alertas
├── relatorios/
│   └── page.tsx                    # GET /relatorios
└── auditoria/
    └── page.tsx                    # GET /auditoria
```

### 8.2 Implementação das Páginas Principais

#### Página de Listagem de Licenças

```typescript
// src/app/(igrp)/(generated)/licencas/page.tsx
'use client';

import { useState } from 'react';
import { useLicenses } from '@/hooks/useLicenses';
import { LicenseCard } from '@/components/business/LicenseCard';
import { SearchForm } from '@/components/ui/SearchForm';
import { DataGrid } from '@/components/ui/DataGrid';
import { PageHeader } from '@/components/layout/PageHeader';
import { LicenseSearchParams } from '@/types/license';
import { useRouter } from 'next/navigation';

export default function LicensesPage() {
  const router = useRouter();
  const [searchParams, setSearchParams] = useState<LicenseSearchParams>({});
  const [viewMode, setViewMode] = useState<'grid' | 'list'>('grid');

  const { data: licenses, isLoading, error } = useLicenses(searchParams);

  const handleSearch = (params: LicenseSearchParams) => {
    setSearchParams(params);
  };

  const handleViewLicense = (id: string) => {
    router.push(`/licencas/${id}`);
  };

  const handleRenewLicense = (id: string) => {
    router.push(`/licencas/${id}/renovar`);
  };

  const handleEditLicense = (id: string) => {
    router.push(`/licencas/${id}/alterar`);
  };

  if (error) {
    return <div>Erro ao carregar licenças</div>;
  }

  return (
    <div className="container mx-auto px-4 py-6">
      <PageHeader
        title="Gestão de Licenças Emitidas"
        subtitle="Consulte e gerencie todas as licenças emitidas no sistema"
        actions={[
          {
            label: 'Exportar',
            onClick: () => {/* Export logic */},
            variant: 'secondary'
          }
        ]}
      />

      <div className="mb-6">
        <SearchForm
          onSearch={handleSearch}
          loading={isLoading}
          fields={[
            { name: 'licenseNumber', label: 'Número da Licença', type: 'text' },
            { name: 'holderName', label: 'Titular', type: 'text' },
            { name: 'status', label: 'Status', type: 'select', options: statusOptions },
            { name: 'licenseType', label: 'Tipo', type: 'select', options: typeOptions },
          ]}
        />
      </div>

      <div className="mb-4 flex justify-between items-center">
        <div className="text-sm text-gray-600">
          {licenses?.totalElements || 0} licenças encontradas
        </div>
        <div className="flex gap-2">
          <button
            onClick={() => setViewMode('grid')}
            className={`p-2 rounded ${viewMode === 'grid' ? 'bg-blue-500 text-white' : 'bg-gray-200'}`}
          >
            Grid
          </button>
          <button
            onClick={() => setViewMode('list')}
            className={`p-2 rounded ${viewMode === 'list' ? 'bg-blue-500 text-white' : 'bg-gray-200'}`}
          >
            Lista
          </button>
        </div>
      </div>

      {viewMode === 'grid' ? (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          {licenses?.content?.map((license) => (
            <LicenseCard
              key={license.id}
              license={license}
              onView={handleViewLicense}
              onEdit={handleEditLicense}
              onRenew={handleRenewLicense}
            />
          ))}
        </div>
      ) : (
        <DataGrid
          data={licenses?.content || []}
          columns={licenseColumns}
          loading={isLoading}
          onRowClick={(license) => handleViewLicense(license.id)}
        />
      )}
    </div>
  );
}
```

#### Página de Detalhes da Licença

```typescript
// src/app/(igrp)/(generated)/licencas/[id]/page.tsx
'use client';

import { useLicense } from '@/hooks/useLicenses';
import { LicenseDetails } from '@/components/business/LicenseDetails';
import { TabPanel } from '@/components/ui/TabPanel';
import { DocumentViewer } from '@/components/ui/DocumentViewer';
import { AuditHistory } from '@/components/business/AuditHistory';
import { PageHeader } from '@/components/layout/PageHeader';
import { useRouter } from 'next/navigation';

interface LicenseDetailPageProps {
  params: { id: string };
}

export default function LicenseDetailPage({ params }: LicenseDetailPageProps) {
  const router = useRouter();
  const { data: license, isLoading, error } = useLicense(params.id);

  if (isLoading) {
    return <div>Carregando...</div>;
  }

  if (error || !license) {
    return <div>Licença não encontrada</div>;
  }

  const tabs = [
    {
      id: 'details',
      label: 'Detalhes',
      content: <LicenseDetails license={license} />
    },
    {
      id: 'documents',
      label: 'Documentos',
      content: <DocumentViewer documents={license.documents} />
    },
    {
      id: 'history',
      label: 'Histórico',
      content: <AuditHistory licenseId={license.id} />
    }
  ];

  const actions = [
    {
      label: 'Renovar',
      onClick: () => router.push(`/licencas/${license.id}/renovar`),
      variant: 'primary' as const,
      disabled: !license.renewable
    },
    {
      label: 'Alterar',
      onClick: () => router.push(`/licencas/${license.id}/alterar`),
      variant: 'secondary' as const
    },
    {
      label: 'Transferir',
      onClick: () => router.push(`/licencas/${license.id}/transferir`),
      variant: 'secondary' as const
    },
    {
      label: 'Suspender',
      onClick: () => router.push(`/licencas/${license.id}/suspender`),
      variant: 'danger' as const,
      disabled: license.status !== 'ACTIVE'
    }
  ];

  return (
    <div className="container mx-auto px-4 py-6">
      <PageHeader
        title={`Licença ${license.licenseNumber}`}
        subtitle={`Titular: ${license.holder.name}`}
        actions={actions}
        breadcrumbs={[
          { label: 'Licenças', href: '/licencas' },
          { label: license.licenseNumber, href: `/licencas/${license.id}` }
        ]}
      />

      <TabPanel tabs={tabs} defaultTab="details" />
    </div>
  );
}
```

### 8.3 Middleware de Autenticação

```typescript
// src/middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { verifyJWT } from '@/lib/auth';

export async function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;

  // Public routes that don't require authentication
  const publicRoutes = ['/login', '/register', '/forgot-password'];

  if (publicRoutes.includes(pathname)) {
    return NextResponse.next();
  }

  // Check for authentication token
  const token = request.cookies.get('auth-token')?.value;

  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  try {
    const payload = await verifyJWT(token);

    // Add user info to headers for server components
    const requestHeaders = new Headers(request.headers);
    requestHeaders.set('x-user-id', payload.userId);
    requestHeaders.set('x-user-role', payload.role);

    return NextResponse.next({
      request: {
        headers: requestHeaders,
      },
    });
  } catch (error) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
};
```

### 8.4 Configuração de Tipos TypeScript

```typescript
// src/types/license.ts
import { z } from 'zod';

export const licenseSchema = z.object({
  id: z.string().uuid(),
  licenseNumber: z.string().min(1, 'Número da licença é obrigatório'),
  issuer: z.object({
    id: z.string().uuid(),
    name: z.string(),
    code: z.string(),
  }),
  licenseType: z.object({
    id: z.string().uuid(),
    name: z.string(),
    code: z.string(),
    category: z.string(),
  }),
  holder: z.object({
    id: z.string().uuid(),
    name: z.string(),
    type: z.enum(['INDIVIDUAL', 'CORPORATE']),
    documentNumber: z.string(),
  }),
  establishment: z
    .object({
      id: z.string().uuid(),
      name: z.string(),
      matricialNumber: z.string(),
      location: z.object({
        latitude: z.number(),
        longitude: z.number(),
        address: z.string(),
      }),
    })
    .optional(),
  status: z.enum(['ACTIVE', 'EXPIRED', 'SUSPENDED', 'CANCELLED']),
  issueDate: z.string().datetime(),
  expiryDate: z.string().datetime(),
  renewable: z.boolean(),
  renewalCount: z.number().default(0),
  documents: z
    .array(
      z.object({
        id: z.string().uuid(),
        name: z.string(),
        type: z.string(),
        url: z.string(),
        uploadDate: z.string().datetime(),
      }),
    )
    .default([]),
  metadata: z.record(z.any()).optional(),
  createdAt: z.string().datetime(),
  updatedAt: z.string().datetime(),
});

export type License = z.infer<typeof licenseSchema>;

export interface LicenseSearchParams {
  licenseNumber?: string;
  holderName?: string;
  status?: License['status'];
  licenseType?: string;
  issuer?: string;
  issueDateFrom?: string;
  issueDateTo?: string;
  expiryDateFrom?: string;
  expiryDateTo?: string;
  page?: number;
  size?: number;
  sort?: string;
  direction?: 'ASC' | 'DESC';
}

export interface PagedResponse<T> {
  content: T[];
  totalElements: number;
  totalPages: number;
  number: number;
  size: number;
  first: boolean;
  last: boolean;
}
```

## 9. Validações e Conformidade

### 9.1 Validação de Formulários

```typescript
// src/utils/validators.ts
import { z } from 'zod';

// Validação de CPF/CNPJ para Cabo Verde
export const documentNumberSchema = z
  .string()
  .min(1, 'Número do documento é obrigatório')
  .refine((value) => {
    // Implementar validação específica para documentos de Cabo Verde
    return validateCapeVerdeDocument(value);
  }, 'Número de documento inválido');

// Validação de coordenadas GPS
export const coordinatesSchema = z.object({
  latitude: z
    .number()
    .min(-90, 'Latitude deve estar entre -90 e 90')
    .max(90, 'Latitude deve estar entre -90 e 90'),
  longitude: z
    .number()
    .min(-180, 'Longitude deve estar entre -180 e 180')
    .max(180, 'Longitude deve estar entre -180 e 180'),
});

// Validação de estabelecimento
export const establishmentSchema = z.object({
  name: z.string().min(1, 'Nome é obrigatório'),
  matricialNumber: z.string().min(1, 'Número matricial é obrigatório'),
  segment: z.enum(['COMMERCIAL', 'TOURISM', 'INDUSTRIAL', 'SERVICES']),
  category: z.string().min(1, 'Categoria é obrigatória'),
  location: z.object({
    coordinates: coordinatesSchema,
    address: z.string().min(1, 'Endereço é obrigatório'),
    city: z.string().min(1, 'Cidade é obrigatória'),
    parish: z.string().min(1, 'Freguesia é obrigatória'),
    municipality: z.string().min(1, 'Município é obrigatório'),
  }),
  documents: z.array(
    z.object({
      type: z.string(),
      file: z.instanceof(File),
      required: z.boolean(),
    }),
  ),
});

function validateCapeVerdeDocument(document: string): boolean {
  // Implementar lógica específica de validação para Cabo Verde
  // Esta é uma implementação simplificada
  return document.length >= 8 && /^[0-9A-Z]+$/.test(document);
}
```

### 9.2 Conformidade com IGRP Studio

```typescript
// src/lib/igrp-compliance.ts
import { IGRPStudioConfig } from '@igrp/igrp-framework-react-design-system';

// Configuração de conformidade IGRP
export const igrpComplianceConfig: IGRPStudioConfig = {
  // Custom Code Functions
  functions: {
    // Função para formatação de status de licença
    getStatusBadge: (status: string): string => {
      const statusMap = {
        ACTIVE: '✅ Ativa',
        EXPIRED: '❌ Expirada',
        SUSPENDED: '⏸️ Suspensa',
        CANCELLED: '🚫 Cancelada',
      };
      return statusMap[status as keyof typeof statusMap] || 'ℹ️ Desconhecido';
    },

    // Função para validação de documentos por segmento
    validateDocumentsBySegment: (segment: string, documents: any[]): boolean => {
      const requiredDocs = getRequiredDocumentsBySegment(segment);
      return requiredDocs.every((reqDoc) =>
        documents.some((doc) => doc.type === reqDoc.type && doc.validated),
      );
    },

    // Função para cálculo de coordenadas GPS
    calculateDistance: (
      coord1: { lat: number; lng: number },
      coord2: { lat: number; lng: number },
    ): number => {
      const R = 6371; // Raio da Terra em km
      const dLat = ((coord2.lat - coord1.lat) * Math.PI) / 180;
      const dLng = ((coord2.lng - coord1.lng) * Math.PI) / 180;
      const a =
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos((coord1.lat * Math.PI) / 180) *
          Math.cos((coord2.lat * Math.PI) / 180) *
          Math.sin(dLng / 2) *
          Math.sin(dLng / 2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      return R * c;
    },
  },

  // Estados reativos
  states: {
    licenseFilter: {
      initialValue: {
        status: 'ALL',
        dateRange: 'LAST_30_DAYS',
        segment: 'ALL',
      },
    },
    establishmentLocation: {
      initialValue: {
        latitude: 14.9177, // Coordenadas de Praia, Cabo Verde
        longitude: -23.5092,
        zoom: 10,
      },
    },
  },
};

function getRequiredDocumentsBySegment(segment: string) {
  const documentRequirements = {
    COMMERCIAL: [
      { type: 'COMMERCIAL_LICENSE', name: 'Licença Comercial' },
      { type: 'TAX_CERTIFICATE', name: 'Certificado Fiscal' },
      { type: 'FIRE_SAFETY', name: 'Certificado de Segurança contra Incêndios' },
    ],
    TOURISM: [
      { type: 'TOURISM_LICENSE', name: 'Licença Turística' },
      { type: 'ENVIRONMENTAL_IMPACT', name: 'Estudo de Impacto Ambiental' },
      { type: 'HEALTH_CERTIFICATE', name: 'Certificado Sanitário' },
    ],
    INDUSTRIAL: [
      { type: 'INDUSTRIAL_LICENSE', name: 'Licença Industrial' },
      { type: 'ENVIRONMENTAL_LICENSE', name: 'Licença Ambiental' },
      { type: 'SAFETY_CERTIFICATE', name: 'Certificado de Segurança' },
    ],
  };

  return documentRequirements[segment as keyof typeof documentRequirements] || [];
}
```

## 10. Configurações de Ambiente

### 10.1 Variáveis de Ambiente

```bash
# .env.local
NEXT_PUBLIC_API_BASE_URL=http://localhost:8080/api/v1
NEXT_PUBLIC_APP_NAME="Sistema de Licenciamento - Cabo Verde"
NEXT_PUBLIC_APP_VERSION=1.0.0

# Autenticação
NEXTAUTH_SECRET=your-secret-key-here
NEXTAUTH_URL=http://localhost:3000

# APIs Externas
NEXT_PUBLIC_MAPS_API_KEY=your-maps-api-key
NEXT_PUBLIC_PAYMENT_GATEWAY_URL=https://payment.gov.cv

# Cache e Performance
NEXT_PUBLIC_CACHE_TTL=300000
NEXT_PUBLIC_MAX_FILE_SIZE=10485760

# Monitoramento
NEXT_PUBLIC_SENTRY_DSN=your-sentry-dsn
NEXT_PUBLIC_ANALYTICS_ID=your-analytics-id

# Desenvolvimento
NEXT_PUBLIC_DEBUG_MODE=true
NEXT_PUBLIC_MOCK_API=false
```

### 10.2 Configuração Next.js

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    appDir: true,
  },
  images: {
    domains: ['api.gov.cv', 'storage.gov.cv'],
    formats: ['image/webp', 'image/avif'],
  },
  async rewrites() {
    return [
      {
        source: '/api/:path*',
        destination: `${process.env.NEXT_PUBLIC_API_BASE_URL}/:path*`,
      },
    ];
  },
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'X-Frame-Options',
            value: 'DENY',
          },
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          {
            key: 'Referrer-Policy',
            value: 'strict-origin-when-cross-origin',
          },
        ],
      },
    ];
  },
  webpack: (config, { isServer }) => {
    if (!isServer) {
      config.resolve.fallback = {
        ...config.resolve.fallback,
        fs: false,
      };
    }
    return config;
  },
};

module.exports = nextConfig;
```

## 11. Métricas de Sucesso e KPIs

### 11.1 Métricas Técnicas

- **Performance**: Tempo de carregamento < 3s
- **Disponibilidade**: 99.5% uptime
- **Cobertura de Testes**: > 85%
- **Lighthouse Score**: > 80 em todas as categorias
- **Bundle Size**: < 500KB inicial

### 11.2 Métricas de Negócio

- **Tempo de Processamento**: Redução de 50% no tempo de renovação
- **Satisfação do Usuário**: Score > 4.0/5.0
- **Taxa de Erro**: < 2% nas transações
- **Adoção**: 80% dos usuários utilizando o novo sistema

### 11.3 Métricas de Conformidade

- **Documentos Validados**: 95% de precisão na validação automática
- **Georreferenciamento**: 100% dos estabelecimentos com coordenadas válidas
- **Auditoria**: 100% das ações rastreáveis
- **Conformidade Legal**: 100% aderência à legislação vigente

## 12. Riscos e Mitigações

### 12.1 Riscos Técnicos

| Risco                                  | Probabilidade | Impacto | Mitigação                                  |
| -------------------------------------- | ------------- | ------- | ------------------------------------------ |
| Incompatibilidade com bibliotecas IGRP | Média         | Alto    | Testes de integração contínuos, versioning |
| Performance em dispositivos móveis     | Alta          | Médio   | Otimização de bundle, lazy loading         |
| Problemas de conectividade             | Alta          | Médio   | Cache offline, retry mechanisms            |
| Falhas na integração GPS               | Média         | Alto    | Fallback manual, validação dupla           |

### 12.2 Riscos de Negócio

| Risco                    | Probabilidade | Impacto    | Mitigação                                     |
| ------------------------ | ------------- | ---------- | --------------------------------------------- |
| Mudanças na legislação   | Alta          | Alto       | Arquitetura flexível, configurações dinâmicas |
| Resistência dos usuários | Média         | Alto       | Treinamento, UX intuitivo                     |
| Atraso na entrega        | Média         | Alto       | Cronograma com buffer, entregas incrementais  |
| Problemas de segurança   | Baixa         | Muito Alto | Auditorias de segurança, testes de penetração |

## 13. Resumo Executivo - Interface Visual

### 13.1 Objetivos do Projeto

Este documento define o plano de implementação exclusivamente focado na **interface visual** do sistema de licenciamento, considerando que a framework IGRP será responsável por:

- Layout base e estrutura
- Sistema de cache
- Deployment e infraestrutura
- Performance e otimização
- Monitoramento e logs
- Autenticação e autorização

### 13.2 Escopo da Interface Visual

O desenvolvimento frontend concentra-se em:

- **Componentes visuais personalizados** para licenciamento
- **Formulários interativos** com validação visual
- **Navegação intuitiva** entre páginas
- **Responsividade** para diferentes dispositivos
- **Acessibilidade** visual e de interação
- **Micro-interações** e feedback visual

### 13.3 Entregáveis Principais

1. **Páginas Principais**:
   - Dashboard com métricas visuais
   - Listagem de licenças com filtros visuais
   - Detalhes de licença com layout otimizado
   - Wizard de renovação com progresso visual
   - Gestão de estabelecimentos com mapas

2. **Componentes Reutilizáveis**:
   - Sistema de design consistente
   - Biblioteca de componentes UI
   - Formulários padronizados
   - Elementos de navegação

3. **Experiência do Usuário**:
   - Interface responsiva
   - Feedback visual imediato
   - Navegação intuitiva
   - Acessibilidade completa

### 13.4 Cronograma Resumido

- **Semanas 1-4**: Configuração visual e componentes base
- **Semanas 5-8**: Páginas principais da interface
- **Semanas 9-12**: Interfaces de processos (renovação, alterações)
- **Semanas 13-16**: Gestão de estabelecimentos
- **Semanas 17-20**: Polimento e otimização visual

### 13.5 Tecnologias Visuais

- **Frontend**: Next.js 14 + React 18
- **Estilização**: Tailwind CSS + IGRP Design System
- **Componentes**: Biblioteca customizada + IGRP Components
- **Testes**: Jest + Testing Library (foco em renderização)
- **Acessibilidade**: WCAG 2.1 AA compliance

### 13.6 Critérios de Sucesso Visual

- Interface 100% responsiva (mobile-first)
- Tempo de carregamento visual < 2s
- Score de acessibilidade > 95%
- Cobertura de testes visuais > 80%
- Aprovação em testes de usabilidade

### 13.7 Próximos Passos

1. **Aprovação do Plano Visual**: Revisão pelos stakeholders
2. **Setup do Design System**: Configuração do IGRP visual
3. **Prototipagem**: Criação de protótipos das páginas principais
4. **Desenvolvimento Iterativo**: Sprints focados na interface
5. **Testes de Usabilidade**: Validação contínua da experiência

---

**Documento elaborado por**: Equipe de Desenvolvimento Frontend  
**Foco**: Interface Visual e Experiência do Usuário  
**Framework Base**: IGRP (layout, cache, deployment, performance, monitoramento, autenticação)  
**Versão**: 2.0 - Visual Focus  
**Status**: Atualizado para Foco Visual
