# FE-LIC-Especificação Frontend Completa

## 1. Visão Geral

Este documento especifica a implementação completa do frontend para o Sistema de Licenciamento de Cabo Verde, baseado na arquitetura modular DDD definida nos documentos técnicos existentes. O frontend será desenvolvido em React com TypeScript, seguindo princípios de design responsivo e experiência de usuário otimizada.

### 1.1 Objetivos

* Implementar interface completa para gestão de configurações base

* Criar sistema hierárquico de navegação Sector → Categoria → Tipo de Licença

* Desenvolver funcionalidades CRUD completas com validações robustas

* Integrar sistema de Dossier Tipo Licença com todas as parametrizações

* Garantir experiência de usuário consistente e intuitiva

* Implementar sistema de filtros avançados e busca

## 2. Arquitetura Frontend

### 2.1 Stack Tecnológico

```typescript
// Tecnologias principais
- React 18+ com TypeScript
- Vite como bundler
- TailwindCSS para estilização
- React Query para gestão de estado servidor
- React Hook Form para formulários
- Zod para validação de schemas
- React Router para navegação
- Axios para comunicação HTTP
```

### 2.2 Estrutura de Pastas

```
src/
├── components/
│   ├── ui/                    # Componentes base reutilizáveis
│   ├── forms/                 # Componentes de formulários
│   ├── tables/                # Componentes de tabelas
│   └── layout/                # Componentes de layout
├── pages/
│   ├── configurations/        # Páginas de configurações
│   ├── licensing/             # Páginas de licenciamento
│   └── dossier/              # Páginas de dossier
├── hooks/                     # Custom hooks
├── services/                  # Serviços de API
├── types/                     # Definições TypeScript
├── utils/                     # Utilitários
└── constants/                 # Constantes da aplicação
```

## 3. Configurações Base

### 3.1 Página de Configurações Base

**Rota**: `/configuracoes/base`

#### 3.1.1 Layout da Página

```typescript
interface BaseConfigurationsPageProps {
  // Estrutura da página principal
}

const BaseConfigurationsPage: React.FC = () => {
  return (
    <div className="min-h-screen bg-gray-50">
      <PageHeader 
        title="Configurações Base" 
        subtitle="Gestão de opções e parâmetros do sistema"
      />
      
      <div className="container mx-auto px-4 py-6">
        <div className="grid grid-cols-1 lg:grid-cols-4 gap-6">
          {/* Sidebar com categorias */}
          <div className="lg:col-span-1">
            <ConfigurationSidebar />
          </div>
          
          {/* Área principal de conteúdo */}
          <div className="lg:col-span-3">
            <ConfigurationContent />
          </div>
        </div>
      </div>
    </div>
  );
};
```

#### 3.1.2 Componente de Sidebar

```typescript
interface ConfigurationCategory {
  id: string;
  name: string;
  code: string;
  description?: string;
  icon: string;
  count: number;
}

const ConfigurationSidebar: React.FC = () => {
  const [selectedCategory, setSelectedCategory] = useState<string>('');
  const { data: categories, isLoading } = useConfigurationCategories();

  return (
    <div className="bg-white rounded-lg shadow-sm border">
      <div className="p-4 border-b">
        <h3 className="font-semibold text-gray-900">Categorias</h3>
      </div>
      
      <div className="p-2">
        {categories?.map((category) => (
          <button
            key={category.id}
            onClick={() => setSelectedCategory(category.code)}
            className={`w-full text-left p-3 rounded-md mb-1 transition-colors ${
              selectedCategory === category.code
                ? 'bg-blue-50 text-blue-700 border-l-4 border-blue-500'
                : 'hover:bg-gray-50'
            }`}
          >
            <div className="flex items-center justify-between">
              <div className="flex items-center space-x-3">
                <Icon name={category.icon} className="w-5 h-5" />
                <span className="font-medium">{category.name}</span>
              </div>
              <span className="text-sm text-gray-500 bg-gray-100 px-2 py-1 rounded">
                {category.count}
              </span>
            </div>
          </button>
        ))}
      </div>
    </div>
  );
};
```

#### 3.1.3 Área de Conteúdo Principal

```typescript
const ConfigurationContent: React.FC = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const [selectedCategory, setSelectedCategory] = useState<string>('');
  const [isCreateModalOpen, setIsCreateModalOpen] = useState(false);
  
  const {
    data: options,
    isLoading,
    refetch
  } = useConfigurationOptions({
    category: selectedCategory,
    search: searchTerm
  });

  return (
    <div className="space-y-6">
      {/* Barra de ações */}
      <div className="bg-white rounded-lg shadow-sm border p-4">
        <div className="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4">
          <div className="flex-1">
            <SearchInput
              placeholder="Buscar configurações..."
              value={searchTerm}
              onChange={setSearchTerm}
              className="max-w-md"
            />
          </div>
          
          <div className="flex items-center space-x-3">
            <FilterDropdown
              options={filterOptions}
              onFilterChange={handleFilterChange}
            />
            
            <Button
              onClick={() => setIsCreateModalOpen(true)}
              className="bg-blue-600 hover:bg-blue-700"
            >
              <PlusIcon className="w-4 h-4 mr-2" />
              Nova Configuração
            </Button>
          </div>
        </div>
      </div>

      {/* Tabela de configurações */}
      <div className="bg-white rounded-lg shadow-sm border">
        <ConfigurationTable
          data={options}
          isLoading={isLoading}
          onEdit={handleEdit}
          onDelete={handleDelete}
          onToggleStatus={handleToggleStatus}
        />
      </div>

      {/* Modal de criação/edição */}
      <ConfigurationModal
        isOpen={isCreateModalOpen}
        onClose={() => setIsCreateModalOpen(false)}
        onSuccess={() => {
          setIsCreateModalOpen(false);
          refetch();
        }}
      />
    </div>
  );
};
```

### 3.2 Tabela de Configurações

```typescript
interface ConfigurationTableProps {
  data: ConfigurationOption[];
  isLoading: boolean;
  onEdit: (id: string) => void;
  onDelete: (id: string) => void;
  onToggleStatus: (id: string, active: boolean) => void;
}

const ConfigurationTable: React.FC<ConfigurationTableProps> = ({
  data,
  isLoading,
  onEdit,
  onDelete,
  onToggleStatus
}) => {
  const columns = [
    {
      header: 'Código',
      accessor: 'ccode',
      cell: (value: string) => (
        <span className="font-mono text-sm bg-gray-100 px-2 py-1 rounded">
          {value}
        </span>
      )
    },
    {
      header: 'Chave',
      accessor: 'ckey',
      cell: (value: string) => (
        <span className="font-medium">{value}</span>
      )
    },
    {
      header: 'Valor',
      accessor: 'cvalue',
      cell: (value: string) => (
        <span className="text-gray-700">{value}</span>
      )
    },
    {
      header: 'Idioma',
      accessor: 'locale',
      cell: (value: string) => (
        <span className="text-xs bg-blue-100 text-blue-800 px-2 py-1 rounded">
          {value}
        </span>
      )
    },
    {
      header: 'Status',
      accessor: 'active',
      cell: (value: boolean, row: ConfigurationOption) => (
        <StatusToggle
          active={value}
          onChange={(active) => onToggleStatus(row.id, active)}
        />
      )
    },
    {
      header: 'Ações',
      accessor: 'id',
      cell: (value: string) => (
        <div className="flex items-center space-x-2">
          <Button
            variant="ghost"
            size="sm"
            onClick={() => onEdit(value)}
          >
            <EditIcon className="w-4 h-4" />
          </Button>
          <Button
            variant="ghost"
            size="sm"
            onClick={() => onDelete(value)}
            className="text-red-600 hover:text-red-700"
          >
            <TrashIcon className="w-4 h-4" />
          </Button>
        </div>
      )
    }
  ];

  return (
    <DataTable
      columns={columns}
      data={data}
      isLoading={isLoading}
      emptyMessage="Nenhuma configuração encontrada"
    />
  );
};
```

## 4. Configurações Hierárquicas (Sector → Categoria → Tipo de Licença)

### 4.1 Layout Principal com Tabuladores

**Rota**: `/licenciamento/configuracoes`

```typescript
const LicensingConfigurationsPage: React.FC = () => {
  const [activeTab, setActiveTab] = useState<'sectors' | 'categories' | 'license-types'>('sectors');
  
  const tabs = [
    {
      id: 'sectors',
      name: 'Setores',
      icon: 'building',
      component: SectorsTab
    },
    {
      id: 'categories',
      name: 'Categorias',
      icon: 'folder',
      component: Categor
```

