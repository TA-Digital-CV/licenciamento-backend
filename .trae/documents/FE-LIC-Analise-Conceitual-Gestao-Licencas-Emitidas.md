# Análise Conceitual - Módulos de Gestão de Licenças Emitidas

## Sistema de Licenciamento de Cabo Verde

## 1. Visão Geral do Módulo

O módulo de **Gestão de Licenças Emitidas** é responsável pela gestão completa do ciclo de vida das licenças após sua emissão, incluindo monitoramento de validade, processos de renovação, alterações, suspensão e controle de conformidade. Este módulo integra-se com o sistema de parametrização existente e utiliza arquitetura DDD para garantir robustez e manutenibilidade.

### 1.1 Objetivos

- Implementar gestão completa do ciclo de vida de licenças emitidas

- **Estabelecer obrigatoriedade de registro de estabelecimentos como pré-requisito para licenciamento**

- **Implementar sistema de registro matricial com identificação única para estabelecimentos**

- **Integrar sistema de georreferenciamento com coordenadas GPS para localização precisa**

- Fornecer monitoramento automatizado de validade e alertas

- Processar renovações, alterações e transferências de titularidade

- Garantir rastreabilidade completa através de auditoria

- Integrar com sistemas externos (Activity, Financeiro, Notificações)

- Suportar diferentes modelos de licenciamento (simples, complexo, automático)

- **Validar documentação específica por segmento (comercial, turístico, industrial)**

- **Implementar controles de conformidade com legislação vigente de Cabo Verde**

- Implementar controles de acesso baseados em perfis de usuário

### 1.2 Entidades Principais

#### 1.2.1 Entidades de Emissão

- **T_LICENSE_ISSUER**: Órgãos emissores de licenças
  - Identificação única do emissor

  - Competências e jurisdições

  - Dados de contato institucional

  - Status operacional

- **T_ISSUED_LICENSE**: Licenças emitidas com controle de ciclo de vida
  - Vinculação ao emissor e titular

  - Controle de validade e status

  - Metadados específicos do tipo de licença

#### 1.2.2 Entidades de Titularidade

- **T_LICENSE_HOLDER**: Entidade base para titulares de licenças
  - Tipo de titular (pessoa física/jurídica)

  - Dados comuns (identificação, contatos)

  - Status e classificação

- **T_INDIVIDUAL_HOLDER**: Pessoas físicas titulares de licenças
  - Nome completo e dados pessoais

  - Filiação (pai e mãe)

  - Estado civil e naturalidade

  - Documentos de identificação específicos

- **T_CORPORATE_HOLDER**: Pessoas jurídicas titulares de licenças
  - Razão social e nome fantasia

  - NIF e inscrições

  - Atividade econômica principal

  - Dados societários

- **T_LEGAL_REPRESENTATIVE**: Representantes legais de pessoas jurídicas
  - Vinculação à pessoa jurídica

  - Dados pessoais do representante

  - Tipo e poderes de representação

  - Período de validade da representação

#### 1.2.3 Entidades de Contato

- **T_HOLDER_CONTACT**: Contatos dos titulares
  - Tipo de contato (telefone, email, endereço)

  - Classificação (principal, secundário, comercial)

  - Status de verificação

  - Preferências de comunicação

#### 1.2.4 Entidades de Estabelecimento

- **T_ESTABLISHMENT**: Estabelecimentos registrados no sistema
  - Registro matricial único
  - Dados de localização e georreferenciamento
  - Classificação por segmento (comercial, turístico, industrial)
  - Status operacional e conformidade

- **T_ESTABLISHMENT_LOCATION**: Dados de georreferenciamento
  - Coordenadas GPS (latitude/longitude)
  - Endereço completo normalizado
  - Área de influência e zoneamento
  - Validação cartográfica

- **T_ESTABLISHMENT_DOCUMENT**: Documentação específica por segmento
  - Documentos obrigatórios por tipo de estabelecimento
  - Status de validação e conformidade
  - Datas de validade e renovação
  - Histórico de alterações

- **T_ESTABLISHMENT_CLASSIFICATION**: Classificação de estabelecimentos
  - Segmento de atividade (comercial, turístico, industrial, serviços)
  - Categoria específica dentro do segmento
  - Porte do estabelecimento (micro, pequeno, médio, grande)
  - Critérios de classificação aplicáveis

#### 1.2.5 Entidades de Processo

- **T_LICENSE_RENEWAL**: Processos de renovação de licenças

- **T_LICENSE_AMENDMENT**: Alterações e aditivos às licenças

- **T_LICENSE_TRANSFER**: Transferências de titularidade

- **T_ESTABLISHMENT_REGISTRATION**: Processos de registro de estabelecimentos

- **T_LICENSE_AUDIT**: Histórico completo de auditoria

## 2. Modelo de Dados Normalizado

### 2.1 Diagrama ER Normalizado

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

    %% Entidades de Estabelecimento
    T_ISSUED_LICENSE }o--|| T_ESTABLISHMENT : "licensed_establishment"
    T_ESTABLISHMENT }o--|| T_LICENSE_HOLDER : "owned_by"
    T_ESTABLISHMENT }o--|| T_ESTABLISHMENT_CLASSIFICATION : "classified_as"
    T_ESTABLISHMENT ||--|| T_ESTABLISHMENT_LOCATION : "located_at"
    T_ESTABLISHMENT ||--o{ T_ESTABLISHMENT_DOCUMENT : "has_documents"
    T_ESTABLISHMENT ||--o{ T_ESTABLISHMENT_REGISTRATION : "registration_process"

    %% Entidades de Processo
    T_ISSUED_LICENSE ||--o{ T_LICENSE_RENEWAL : "has renewals"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_AMENDMENT : "has amendments"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_TRANSFER : "has transfers"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_AUDIT : "has audit trail"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_DOCUMENT : "has documents"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_FEE : "has fees"
    T_ISSUED_LICENSE ||--o{ T_LICENSE_ALERT : "generates alerts"

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
        uuid establishment_id FK
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

    T_ESTABLISHMENT {
        uuid id PK
        varchar matricial_number UK
        varchar name
        uuid holder_id FK
        uuid classification_id FK
        varchar segment
        varchar category
        varchar size_classification
        varchar operational_status
        date registration_date
        date last_inspection_date
        boolean compliance_status
        text compliance_notes
        timestamp created_at
        timestamp updated_at
        uuid created_by FK
    }

    T_ESTABLISHMENT_LOCATION {
        uuid id PK
        uuid establishment_id FK
        decimal latitude
        decimal longitude
        varchar address_line1
        varchar address_line2
        varchar city
        varchar parish
        varchar municipality
        varchar postal_code
        varchar zone_classification
        varchar land_use_type
        boolean cartographic_validated
        timestamp validation_date
        uuid validated_by FK
    }

    T_ESTABLISHMENT_DOCUMENT {
        uuid id PK
        uuid establishment_id FK
        varchar document_type
        varchar document_name
        varchar file_path
        varchar mime_type
        bigint file_size
        varchar validation_status
        date issue_date
        date expiry_date
        varchar issuing_authority
        text validation_notes
        timestamp uploaded_at
        uuid uploaded_by FK
    }

    T_ESTABLISHMENT_CLASSIFICATION {
        uuid id PK
        varchar code UK
        varchar name
        varchar segment
        varchar category
        text description
        json required_documents
        json validation_criteria
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }

    T_ESTABLISHMENT_REGISTRATION {
        uuid id PK
        uuid establishment_id FK
        varchar registration_type
        varchar status
        date application_date
        date approval_date
        uuid approved_by FK
        text approval_notes
        json submitted_documents
        json validation_results
        timestamp created_at
        uuid created_by FK
    }
```

### 2.2 Descrição das Entidades Normalizadas

#### Entidades de Emissão

##### T_LICENSE_ISSUER (Emissores de Licenças)

Entidade que representa os órgãos ou entidades responsáveis pela emissão de licenças.

**Campos principais:**

- `issuer_code`: Código único do emissor

- `name`: Nome oficial do órgão emissor

- `jurisdiction`: Jurisdição de competência

- `competence_area`: Área de competência específica

- `contact_email`: Email oficial de contato

- `contact_phone`: Telefone oficial de contato

- `status`: Status do emissor (ativo, inativo)

##### T_ISSUED_LICENSE (Licenças Emitidas)

Entidade principal que representa uma licença emitida pelo sistema.

**Campos principais:**

- `license_number`: Número único da licença

- `issuer_id`: Referência ao emissor da licença

- `license_type_id`: Referência ao tipo de licença

- `holder_id`: Referência ao titular da licença

- `status`: Estado atual da licença (ativa, suspensa, cancelada, expirada)

- `issue_date`: Data de emissão

- `expiry_date`: Data de expiração

- `renewable`: Indica se a licença é renovável

- `renewal_count`: Contador de renovações realizadas

#### Entidades de Titularidade

##### T_LICENSE_HOLDER (Titular Base)

Entidade base que representa os titulares das licenças, servindo como superclasse para pessoas físicas e jurídicas.

**Campos principais:**

- `holder_type`: Tipo de titular (individual, corporate)

- `status`: Status do titular (ativo, inativo, suspenso)

- `classification`: Classificação adicional do titular

##### T_INDIVIDUAL_HOLDER (Pessoa Física)

Entidade específica para titulares pessoas físicas, contendo atributos específicos.

**Campos principais:**

- `holder_id`: Referência ao titular base

- `full_name`: Nome completo da pessoa

- `father_name`: Nome do pai (filiação paterna)

- `mother_name`: Nome da mãe (filiação materna)

- `marital_status`: Estado civil

- `nationality`: Nacionalidade

- `birthplace`: Local de nascimento

- `birth_date`: Data de nascimento

- `document_type`: Tipo de documento de identificação

- `document_number`: Número do documento (único)

- `gender`: Gênero

##### T_CORPORATE_HOLDER (Pessoa Jurídica)

Entidade específica para titulares pessoas jurídicas, contendo atributos corporativos.

**Campos principais:**

- `holder_id`: Referência ao titular base

- `corporate_name`: Razão social

- `trade_name`: Nome fantasia

- `tax_id`: Número de identificação fiscal (único)

- `registration_number`: Número de registro comercial

- `economic_activity`: Atividade econômica principal

- `corporate_type`: Tipo societário

- `incorporation_date`: Data de constituição

- `share_capital`: Capital social

##### T_LEGAL_REPRESENTATIVE (Representante Legal)

Entidade que representa os representantes legais de pessoas jurídicas.

**Campos principais:**

- `corporate_holder_id`: Referência à pessoa jurídica

- `full_name`: Nome completo do representante

- `document_type`: Tipo de documento de identificação

- `document_number`: Número do documento (único)

- `representation_type`: Tipo de representação (administrador, procurador, etc.)

- `powers_description`: Descrição dos poderes de representação

- `valid_from`: Data de início da representação

- `valid_until`: Data de fim da representação

- `status`: Status da representação (ativa, inativa)

#### Entidades de Contato

##### T_HOLDER_CONTACT (Contatos do Titular)

Entidade que armazena os diferentes tipos de contato dos titulares.

**Campos principais:**

- `holder_id`: Referência ao titular

- `contact_type`: Tipo de contato (email, phone, address, etc.)

- `contact_value`: Valor do contato

- `classification`: Classificação do contato (pessoal, comercial, etc.)

- `is_primary`: Indica se é o contato principal

- `is_verified`: Indica se o contato foi verificado

- `verification_method`: Método de verificação utilizado

- `verified_at`: Data/hora da verificação

- `communication_preference`: Preferência de comunicação

#### Entidades de Processo

As entidades de processo mantêm a estrutura original, mas agora se relacionam com a nova estrutura normalizada de titulares:

- **T_LICENSE_RENEWAL**: Processos de renovação de licenças

- **T_LICENSE_AMENDMENT**: Alterações em licenças existentes

- **T_LICENSE_TRANSFER**: Transferências de titularidade

- **T_LICENSE_AUDIT**: Trilha de auditoria completa

- **T_LICENSE_DOCUMENT**: Documentos anexos às licenças

- **T_LICENSE_FEE**: Taxas associadas às licenças

- **T_LICENSE_ALERT**: Alertas e notificações do sistema

### 2.3 Relacionamentos e Regras de Negócio

#### Relacionamentos Principais

**Emissão de Licenças:**

- Um `T_LICENSE_ISSUER` pode emitir múltiplas `T_ISSUED_LICENSE`

- Uma `T_ISSUED_LICENSE` pertence a exatamente um `T_LICENSE_ISSUER`

- Uma `T_ISSUED_LICENSE` é de exatamente um `T_LICENSE_TYPE`

- Uma `T_ISSUED_LICENSE` pertence a exatamente um `T_LICENSE_HOLDER`

**Titularidade:**

- Um `T_LICENSE_HOLDER` pode possuir múltiplas `T_ISSUED_LICENSE`

- Um `T_LICENSE_HOLDER` pode ser estendido por um `T_INDIVIDUAL_HOLDER` OU um `T_CORPORATE_HOLDER` (herança)

- Um `T_CORPORATE_HOLDER` pode ter múltiplos `T_LEGAL_REPRESENTATIVE`

- Um `T_LICENSE_HOLDER` pode ter múltiplos `T_HOLDER_CONTACT`

**Processos:**

- Uma `T_ISSUED_LICENSE` pode ter múltiplos processos de cada tipo (renovação, alteração, transferência)

- Todos os processos mantêm trilha de auditoria através de `T_LICENSE_AUDIT`

#### Regras de Integridade

**Titulares Pessoas Físicas:**

- `document_number` deve ser único no sistema

- `full_name`, `father_name`, `mother_name` são obrigatórios

- `birth_date` deve ser anterior à data atual

- `marital_status` deve seguir valores pré-definidos

**Titulares Pessoas Jurídicas:**

- `tax_id` deve ser único no sistema

- `corporate_name` é obrigatório

- `incorporation_date` deve ser anterior à data atual

- Deve ter pelo menos um representante legal ativo

**Representantes Legais:**

- `document_number` deve ser único por pessoa jurídica

- `valid_from` deve ser anterior ou igual a `valid_until`

- Não pode haver sobreposição de períodos para o mesmo tipo de representação

**Contatos:**

- Apenas um contato pode ser marcado como `is_primary` por tipo

- `contact_value` deve seguir formato específico por tipo (email, telefone, etc.)

- Contatos verificados não podem ser alterados sem nova verificação

**Licenças:**

- `license_number` deve ser único no sistema

- `issue_date` deve ser anterior ou igual a `expiry_date`

- Status deve seguir transições válidas (ativa → suspensa → ativa, etc.)

- Licenças expiradas não podem ser renovadas após período de carência

#### Validações de Negócio

**Emissão de Licenças:**

- Verificar se o emissor tem competência para o tipo de licença

- Validar se o titular atende aos requisitos do tipo de licença

- Verificar se não existem impedimentos legais

**Transferência de Titularidade:**

- Novo titular deve atender aos mesmos requisitos do titular original

- Licenças suspensas não podem ser transferidas

- Transferência deve ser aprovada pelo emissor competente

**Renovação:**

- Licença deve estar dentro do período de renovação

- Titular deve manter os requisitos originais

- Taxas devem estar quitadas

**Alterações:**

- Apenas campos não críticos podem ser alterados sem novo processo

- Alterações críticas requerem aprovação do emissor

- Histórico completo deve ser mantido

## 3. Requisitos Funcionais

### 3.1 Gestão do Ciclo de Vida das Licenças

#### RF001 - Consulta de Licenças Emitidas

- **Descrição:** Permitir consulta e visualização de todas as licenças emitidas no sistema com filtros avançados

- **Critérios de Aceitação:**
  - Busca por número da licença, titular, tipo, setor ou categoria

  - Filtros por status (ACTIVE, EXPIRED, SUSPENDED, CANCELLED)

  - Filtros por período de emissão e validade

  - Visualização em lista paginada com ordenação

  - Exportação de resultados em CSV/Excel

  - Integração com sistema de cache para performance

#### RF002 - Detalhamento de Licença

- **Descrição:** Exibir informações completas de uma licença específica com histórico de auditoria

- **Critérios de Aceitação:**
  - Dados do titular (T_LICENSE_HOLDER) e representante legal

  - Informações técnicas da licença vinculadas ao T_LICENSE_TYPE

  - Histórico completo de alterações (T_LICENSE_AUDIT)

  - Documentos anexos (T_LICENSE_DOCUMENT) e legislação aplicável

  - Status atual e datas relevantes com validações de negócio

  - Taxas pagas e pendentes (T_LICENSE_FEE)

  - Integração com sistema de parametrização para dados dinâmicos

#### RF003 - Monitoramento de Validade

- **Descrição:** Controlar e alertar sobre vencimentos de licenças através do T_LICENSE_ALERT

- **Critérios de Aceitação:**
  - Dashboard com licenças próximas ao vencimento

  - Alertas automáticos configuráveis (90, 60, 30, 15 dias)

  - Notificações por email integradas com sistema de notificações

  - Relatórios de licenças vencidas com filtros avançados

  - Configuração de períodos de alerta por tipo de licença via T_OPTIONS

  - Processamento assíncrono de alertas com queue system

#### RF004 - Processo de Renovação

- **Descrição:** Gerenciar solicitações de renovação através da tabela T_LICENSE_RENEWAL

- **Critérios de Aceitação:**
  - Iniciação automática baseada em configurações do T_LICENSE_PARAMETER

  - Validação de documentos e requisitos via Domain Services

  - Cálculo automático de taxas através de T_PROCESS_TYPE_FEE

  - Workflow de aprovação integrado com Activity Server

  - Geração de nova licença com numeração sequencial

  - Versionamento de licenças renovadas

  - Integração com sistema de pagamentos

#### RF005 - Suspensão e Cancelamento

- **Descrição:** Permitir suspensão temporária ou cancelamento definitivo com auditoria completa

- **Critérios de Aceitação:**
  - Registro de motivo da suspensão/cancelamento no T_LICENSE_AUDIT

  - Workflow de aprovação para ações administrativas via Activity

  - Notificação automática ao titular via sistema de notificações

  - Histórico completo de ações com rastreabilidade

  - Possibilidade de reativação de licenças suspensas

  - Validações de regras de negócio através de Domain Services

  - Integração com sistema de compliance

### 3.2 Gestão de Alterações

#### RF006 - Alteração de Dados da Licença

- **Descrição:** Permitir alterações em dados não críticos através da tabela T_LICENSE_AMENDMENT

- **Critérios de Aceitação:**
  - Identificação de campos editáveis por tipo de licença via T_LICENSE_PARAMETER

  - Workflow de aprovação para alterações integrado com Activity Server

  - Versionamento completo de dados alterados no T_LICENSE_AUDIT

  - Geração de aditivos à licença original com numeração sequencial

  - Cobrança de taxas quando aplicável via T_PROCESS_TYPE_FEE

  - Validações de regras de negócio através de Domain Services

  - Notificação automática ao titular sobre alterações

#### RF007 - Transferência de Titularidade

- **Descrição:** Processar transferências através da tabela T_LICENSE_TRANSFER

- **Critérios de Aceitação:**
  - Validação de documentação do novo titular via T_LICENSE_HOLDER

  - Verificação de impedimentos legais através de Domain Services

  - Workflow de aprovação específico integrado com Activity

  - Atualização de dados cadastrais com auditoria completa

  - Emissão de nova licença com nova titularidade

  - Histórico completo da transferência no T_LICENSE_AUDIT

  - Integração com sistema de organizações globais

### 3.3 Relatórios e Analytics

#### RF008 - Dashboard Executivo

- **Descrição:** Apresentar indicadores-chave baseados em dados normalizados das tabelas T_ISSUED_LICENSE e T_LICENSE_AUDIT

- **Critérios de Aceitação:**
  - Métricas de licenças ativas, vencidas e em processo via consultas otimizadas

  - Gráficos de tendências temporais com cache Redis para performance

  - Indicadores de receita por tipo de licença integrados com T_PROCESS_TYPE_FEE

  - Alertas de licenças próximas ao vencimento via T_LICENSE_ALERT

  - Filtros por período, setor e categoria usando índices otimizados

  - Integração com sistema de Business Intelligence

  - Atualização em tempo real via WebSockets

#### RF009 - Relatórios Operacionais

- **Descrição:** Gerar relatórios detalhados através de consultas estruturadas no modelo normalizado

- **Critérios de Aceitação:**
  - Relatório de licenças por status com dados do T_ISSUED_LICENSE

  - Relatório de renovações pendentes via T_LICENSE_RENEWAL

  - Relatório de receitas por período integrado com sistema financeiro

  - Relatório de auditoria completa baseado em T_LICENSE_AUDIT

  - Exportação em múltiplos formatos (PDF, Excel, CSV) via serviços especializados

  - Agendamento automático de relatórios

  - Controle de acesso baseado em perfis de usuário

## 4. Requisitos Não Funcionais

### 4.1 Performance e Escalabilidade

#### RNF001 - Tempo de Resposta

- Consultas simples: máximo 2 segundos com índices otimizados

- Relatórios complexos: máximo 30 segundos com cache Redis

- Exportações: processamento assíncrono com notificação via WebSockets

#### RNF002 - Capacidade

- Suporte a 10.000 licenças ativas simultâneas com arquitetura distribuída

- 1.000 usuários concorrentes com load balancing

- Crescimento de 20% ao ano com escalabilidade horizontal

#### RNF003 - Disponibilidade

- 99.5% de uptime durante horário comercial com alta disponibilidade

- Janela de manutenção: domingos 02:00-06:00

- Backup automático diário com retenção configurável

### 4.2 Segurança e Compliance

#### RNF004 - Autenticação e Autorização

- Integração com Supabase Auth e Active Directory/LDAP

- Controle de acesso baseado em perfis via RLS (Row Level Security)

- Sessões com timeout configurável

- Log de tentativas de acesso com monitoramento

#### RNF005 - Proteção de Dados

- Criptografia de dados sensíveis em repouso e em trânsito

- Comunicação via HTTPS/TLS 1.3

- Anonimização de dados para relatórios

- Compliance com LGPD/GDPR

### 4.3 Usabilidade e Experiência

#### RNF006 - Interface

- Design responsivo baseado em Design System corporativo

- Compatibilidade com navegadores modernos

- Acessibilidade WCAG 2.1 nível AA

- Suporte a múltiplos idiomas (PT, EN) via internacionalização

- PWA com funcionalidades offline para consultas básicas

## 5. Fluxos de Trabalho

### 5.1 Monitoramento de Validade

```mermaid
flowchart TD
    A[Sistema de Monitoramento] --> B{Consulta T_ISSUED_LICENSE}
    B --> C{Licença próxima ao vencimento?}
    C -->|Sim| D[Criar T_LICENSE_ALERT]
    C -->|Não| E[Continuar Monitoramento]
    D --> F[Domain Service: NotificationService]
    F --> G[Notificar Titular via Sistema]
    F --> H[Notificar Gestor via Dashboard]
    G --> I[Registrar em T_LICENSE_AUDIT]
    H --> I
    I --> J[Aguardar Ação]
    J --> K{Renovação Iniciada?}
    K -->|Sim| L[Workflow Activity Server]
    K -->|Não| M{Licença Vencida?}
    M -->|Sim| N[Atualizar Status em T_ISSUED_LICENSE]
    M -->|Não| J
```

### 5.2 Processo de Renovação

```mermaid
flowchart TD
    A[Solicitação via T_LICENSE_RENEWAL] --> B[Domain Service: ValidationService]
    B --> C{Documentação OK?}
    C -->|Não| D[Retornar Erros de Validação]
    D --> B
    C -->|Sim| E[Domain Service: FeeCalculationService]
    E --> F[Consultar T_PROCESS_TYPE_FEE]
    F --> G[Integração Sistema Financeiro]
    G --> H{Pagamento Confirmado?}
    H -->|Não| I[Aguardar Callback Pagamento]
    I --> H
    H -->|Sim| J[Activity Server: Análise Técnica]
    J --> K{Aprovado?}
    K -->|Não| L[Rejeitar via T_LICENSE_AUDIT]
    K -->|Sim| M[Gerar Nova T_ISSUED_LICENSE]
    M --> N[Domain Service: NotificationService]
```

## 3. Processos de Registro de Estabelecimentos

### 3.1 Obrigatoriedade de Registro

O sistema implementa a **obrigatoriedade de registro de estabelecimentos** como pré-requisito fundamental para o licenciamento, especialmente para:

#### 3.1.1 Segmentos Obrigatórios

- **Estabelecimentos Comerciais**
  - Lojas de varejo e atacado
  - Centros comerciais e galerias
  - Mercados e feiras permanentes
  - Postos de combustível
  - Farmácias e estabelecimentos de saúde

- **Estabelecimentos Turísticos**
  - Hotéis, pousadas e alojamentos
  - Restaurantes e estabelecimentos de alimentação
  - Agências de viagem e turismo
  - Estabelecimentos de entretenimento
  - Operadores turísticos

- **Estabelecimentos Industriais**
  - Fábricas e unidades de produção
  - Armazéns e centros de distribuição
  - Oficinas e serviços técnicos
  - Estabelecimentos de transformação

#### 3.1.2 Critérios de Registro

- **Registro Matricial Único**: Cada estabelecimento recebe um número matricial único e permanente

- **Georreferenciamento Obrigatório**: Coordenadas GPS precisas (latitude/longitude) validadas cartograficamente

- **Documentação Específica por Segmento**: Conjunto de documentos obrigatórios definidos por tipo de estabelecimento

- **Conformidade Legal**: Alinhamento com legislação vigente de Cabo Verde

- **Validação Técnica**: Inspeção e validação das condições operacionais

### 3.2 Processo de Registro de Estabelecimento

```mermaid
flowchart TD
    A[Solicitação de Registro] --> B[Validação de Documentos]
    B --> C{Documentos Completos?}
    C -->|Não| D[Notificação de Pendências]
    D --> A
    C -->|Sim| E[Validação de Localização]
    E --> F[Georreferenciamento GPS]
    F --> G[Validação Cartográfica]
    G --> H{Localização Válida?}
    H -->|Não| I[Correção de Coordenadas]
    I --> F
    H -->|Sim| J[Inspeção Técnica]
    J --> K[Avaliação de Conformidade]
    K --> L{Conforme?}
    L -->|Não| M[Notificação de Não Conformidades]
    M --> N[Correções Necessárias]
    N --> J
    L -->|Sim| O[Aprovação do Registro]
    O --> P[Emissão de Número Matricial]
    P --> Q[Estabelecimento Registrado]
    Q --> R[Habilitado para Licenciamento]
```

### 3.3 Documentação Obrigatória por Segmento

#### 3.3.1 Estabelecimentos Comerciais

- Certidão de registo comercial
- Planta de localização e layout
- Certificado de conformidade urbanística
- Licença de funcionamento municipal
- Certificado de segurança contra incêndios
- Alvará sanitário (quando aplicável)
- Comprovativo de propriedade ou arrendamento

#### 3.3.2 Estabelecimentos Turísticos

- Licença de estabelecimento turístico
- Certificado de classificação turística
- Plano de segurança e evacuação
- Certificado ambiental
- Licença de restauração (quando aplicável)
- Certificado de acessibilidade
- Seguro de responsabilidade civil

#### 3.3.3 Estabelecimentos Industriais

- Licença industrial
- Estudo de impacto ambiental
- Certificado de conformidade técnica
- Plano de gestão de resíduos
- Certificado de segurança ocupacional
- Licença de emissões atmosféricas
- Autorização de captação de água (quando aplicável)

### 3.4 Sistema de Georreferenciamento

#### 3.4.1 Requisitos Técnicos

- **Precisão**: Coordenadas GPS com precisão mínima de 3 metros
- **Sistema de Referência**: WGS84 (EPSG:4326)
- **Validação**: Verificação cartográfica obrigatória
- **Atualização**: Revisão periódica das coordenadas

#### 3.4.2 Dados de Localização

- Latitude e longitude em graus decimais
- Endereço completo normalizado
- Código postal e zona administrativa
- Classificação de zoneamento urbano
- Tipo de uso do solo
- Área de influência do estabelecimento

### 3.5 Fluxo de Suspensão/Cancelamento

```mermaid
graph TD
    A[Identificação de irregularidade] --> B[Abertura de processo administrativo]
    B --> C[Notificação ao titular]
    C --> D[Prazo para defesa]
    D --> E{Defesa apresentada?}
    E -->|Sim| F[Análise da defesa]
    E -->|Não| G[Decisão por revelia]
    F --> H{Defesa aceita?}
    H -->|Sim| I[Arquivamento do processo]
    H -->|Não| J[Aplicação de penalidade]
    G --> J
    J --> K{Tipo de penalidade?}
    K -->|Suspensão| L[Suspende licença temporariamente]
    K -->|Cancelamento| M[Cancela licença definitivamente]
    L --> N[Agenda revisão]
    M --> O[Atualiza registros]
```

## 5. Integração com Outros Módulos

### 5.1 Integração com Módulo de Parametrização

#### Dependências

- **Tipos de Licença:** Herda configurações através de T_LICENSE_TYPE com validações de Domain Services

- **Legislação:** Aplica normas vigentes via T_LEGISLATION com versionamento

- **Entidades:** Utiliza dados de órgãos através de T_ENTITY com cache distribuído

- **Processos:** Executa workflows via T_LICENSE_TYPE_PROCESS_TYPE

#### Sincronização

- Atualização automática via Event Sourcing quando parâmetros são alterados

- Versionamento de configurações através de T_LICENSE_AUDIT

- Migração controlada via Domain Services especializados

### 5.2 Integração com Sistema de Processos (Activity)

#### Funcionalidades

- Criação automática via Domain Services integrados

- Acompanhamento através de T_LICENSE_AUDIT

- Notificações via Event-Driven Architecture

- Integração com workflow engine via APIs REST

#### APIs Utilizadas

- `POST /api/processes` - Criação com dados normalizados

- `GET /api/processes/{id}/status` - Consulta com cache Redis

- `PUT /api/processes/{id}/advance` - Avanço com auditoria

- `GET /api/processes/search` - Busca com Elasticsearch

### 5.3 Integração com Sistema Financeiro

#### Funcionalidades

- Geração via T_PROCESS_TYPE_FEE com cálculos automatizados

- Consulta através de webhooks com retry pattern

- Conciliação via Saga Pattern para consistência

- Relatórios integrados com Business Intelligence

#### Dados Compartilhados

- Códigos via T_FEE_CATEGORY estruturados

- Valores através de Domain Services especializados

- Histórico normalizado em T_LICENSE_AUDIT

- Dados de inadimplência com alertas automáticos

### 5.4 Integração com Sistema de Notificações

#### Canais de Comunicação

- Email via templates estruturados em T_LICENSE_ALERT

- SMS através de Domain Services especializados

- Push notifications via WebSockets em tempo real

- Publicação automatizada com workflow aprovação

#### Templates de Mensagens

- Alertas baseados em T_LICENSE_PARAMETER configuráveis

- Confirmações via T_LICENSE_AUDIT estruturadas

- Notificações com dados normalizados do titular

- Lembretes programados via scheduler distribuído

## 6. Controles de Acesso e Segurança

### 6.1 Perfis de Usuário (Row Level Security)

#### Consultor

- **Permissões via RLS:**
  - `SELECT` em T_ISSUED_LICENSE (filtrado por setor)

  - `SELECT` em T_LICENSE_HOLDER (dados básicos)

  - `SELECT` em relatórios pré-definidos

  - Exportação limitada via Domain Services

- **Restrições:**
  - Não acessa dados pessoais completos

  - Não visualiza informações financeiras

  - Limitado a consultas básicas

#### Analista

- **Permissões via RLS:**
  - Herda permissões de Consultor

  - `INSERT/UPDATE` em T_LICENSE_RENEWAL

  - `UPDATE` em T_ISSUED_LICENSE (campos específicos)

  - `INSERT` em T_LICENSE_AMENDMENT

  - `SELECT` em T_LICENSE_AUDIT (auditoria básica)

- **Restrições:**
  - Não pode suspender/cancelar licenças

  - Não acessa configurações do sistema

  - Limitado a ações de rotina

#### Supervisor

- **Permissões via RLS:**
  - Herda permissões do Analista

  - `UPDATE` em T_ISSUED_LICENSE (status temporário)

  - `INSERT` em T_LICENSE_ALERT

  - Acesso a workflows via Activity Server

- **Restrições:**
  - Não pode cancelar licenças definitivamente

  - Não altera parâmetros do sistema

#### Gestor

- **Permissões via RLS:**
  - Herda permissões do Supervisor

  - `UPDATE` em T_ISSUED_LICENSE (cancelamento definitivo)

  - `INSERT/UPDATE` em T_LICENSE_TRANSFER

  - `SELECT` em relatórios executivos

  - Configuração de workflows via Activity

- **Restrições:**
  - Não altera estrutura de dados

  - Não gerencia usuários do sistema

#### Administrador

- **Permissões via RLS:**
  - `ALL PRIVILEGES` em todas as tabelas T\_\*

  - Acesso completo a T_LICENSE_AUDIT

  - Configuração de T_LICENSE_PARAMETER

  - **Gerenciamento de usuários via Spring Security**

  - **Configuração de JWT tokens e refresh tokens**

  - Execução de operações em lote via Domain Services

### 6.2 Controles de Segurança por Funcionalidade

#### Consulta de Licenças

- **Autenticação:** Obrigatória para todas as consultas

- **Autorização:** Baseada no perfil do usuário via RLS

- **Auditoria:** Log completo em T_LICENSE_AUDIT com rastreabilidade

- **Filtros de Dados:** Aplicados conforme perfil com cache distribuído

- **Rate Limiting:** Controle de taxa via middleware especializado

#### Alteração de Dados

- **Autenticação:** Dupla verificação para alterações críticas

- **Autorização:** Workflow de aprovação via Activity Server

- **Auditoria:** Versionamento completo em T_LICENSE_AUDIT

- **Validação:** Verificação através de Domain Services

- **Rollback:** Controle via Event Sourcing

#### Relatórios

- **Autenticação:** Sessão válida obrigatória

- **Autorização:** Acesso granular por tipo de relatório

- **Auditoria:** Log detalhado em T_LICENSE_AUDIT

- **Anonimização:** Sanitização conforme LGPD

- **Watermark:** Identificação dinâmica do usuário

### 6.3 Políticas de Segurança

#### Gestão de Senhas

- Complexidade mínima: 8 caracteres, maiúsculas, minúsculas, números

- Renovação obrigatória a cada 90 dias

- Histórico de 12 senhas anteriores

- Bloqueio após 5 tentativas incorretas

#### Controle de Sessão

- Timeout automático após 30 minutos de inatividade

- Sessão única por usuário

- Logout automático fora do horário comercial

- Monitoramento de sessões suspeitas

#### Proteção de Dados

- Criptografia AES-256 para dados em repouso

- Hashing SHA-256 para senhas

- Comunicação exclusiva via HTTPS

- Backup criptografado com chaves rotacionadas

#### Monitoramento e Alertas

- Detecção de tentativas de acesso não autorizado

- Alertas para alterações em dados críticos

- Monitoramento de performance e disponibilidade

- Relatórios de segurança semanais

## 7. Especificações Técnicas

### 7.1 Stack Tecnológica

#### Framework Backend

- **Spring Boot 3.2+** - Framework principal para desenvolvimento
  - Configuração automática e convenções sobre configuração

  - Embedded server (Tomcat) para facilitar deployment

  - Starter dependencies para integração simplificada

  - Profile-based configuration para diferentes ambientes

#### Autenticação e Segurança

- **Spring Security 6** - Framework de segurança enterprise
  - Method-level security com anotações

  - CORS configuration para integração frontend

  - CSRF protection configurável

  - Password encoding com BCrypt

- **JWT (JSON Web Tokens)** - Autenticação stateless
  - Access tokens com expiração configurável

  - Refresh tokens para renovação automática

  - Claims customizados para roles e permissões

  - Assinatura HMAC SHA-512 para segurança

#### Banco de Dados

- **PostgreSQL 15+** - Banco de dados relacional principal
  - Row Level Security (RLS) para controle granular

  - Índices especializados para performance

  - Extensões para UUID e funções avançadas

  - Particionamento para tabelas de auditoria

- **Redis 7+** - Cache distribuído e sessões
  - Cache de consultas frequentes

  - Armazenamento de refresh tokens

  - Rate limiting e throttling

  - Pub/Sub para notificações em tempo real

#### Persistência e Mapeamento

- **Spring Data JPA** - Abstração para acesso a dados
  - Repository pattern com interfaces declarativas

  - Specifications para consultas dinâmicas

  - Auditing automático com @EntityListeners

  - Pagination e sorting nativos

- **Hibernate** - ORM para mapeamento objeto-relacional
  - Lazy loading otimizado

  - Second-level cache integration

  - Batch processing para operações em lote

  - Custom types para campos específicos

- **MapStruct** - Mapeamento entre DTOs e Entities
  - Geração de código em tempo de compilação

  - Performance superior a reflection-based mappers

  - Type-safe mapping com validação

  - Custom mapping methods para casos complexos

#### Versionamento e Migração

- **Flyway** - Controle de versão do banco de dados
  - Scripts SQL versionados

  - Migração automática em startup

  - Rollback controlado para ambientes de desenvolvimento

  - Validação de integridade do schema

### 7.2 Padrões Arquiteturais

#### Domain-Driven Design (DDD)

- **Aggregates** - Consistência transacional
  - IssuedLicense como Aggregate Root

  - LicenseHolder com invariantes de negócio

  - Boundaries bem definidos entre contextos

- **Domain Services** - Lógica de negócio complexa
  - ValidationService para regras de validação

  - NotificationService para comunicação

  - FeeCalculationService para cálculos financeiros

- **Repository Pattern** - Abstração de persistência
  - Interfaces no domínio, implementação na infraestrutura

  - Queries específicas do negócio

  - Unit of Work pattern com @Transactional

#### Arquitetura em Camadas

- **Presentation Layer** - Controllers REST
  - @RestController com endpoints RESTful

  - DTOs para request/response

  - Validation com Bean Validation

  - Exception handling global

- **Application Layer** - Orquestração de casos de uso
  - Application Services com @Service

  - Command/Query separation

  - Transaction management

  - Event publishing

- **Domain Layer** - Regras de negócio
  - Entities com comportamentos ricos

  - Value Objects imutáveis

  - Domain Events para comunicação

  - Specifications para consultas complexas

- **Infrastructure Layer** - Detalhes técnicos
  - JPA Repositories

  - External service integrations

  - Configuration properties

  - Monitoring e logging

## 8. Considerações de Implementação

### 8.1 Arquitetura DDD Recomendada

#### Frontend

- React 18+ com TypeScript e Design System corporativo

- Material-UI ou Ant Design para componentes

- Redux Toolkit para gerenciamento de estado

- React Query para cache e sincronização

#### Backend (Domain-Driven Design)

- **Spring Boot 3.2+** com Java 17+ para arquitetura robusta

- **PostgreSQL 15+** com Row Level Security (RLS) nativo

- **Spring Security 6** com autenticação JWT

- **Redis 7+** distribuído para cache de alta performance

- **Event-Driven Architecture** com Spring Events e RabbitMQ

- **Spring Data JPA** com Hibernate para persistência

- **MapStruct** para mapeamento entre DTOs e Entities

#### Infraestrutura

- Containerização com Docker e OpenJDK 17

- Orquestração com Kubernetes

- Load balancer com NGINX

- Observabilidade com Prometheus/Grafana e Spring Actuator

- **Flyway** para versionamento de banco de dados

### 8.2 Fases de Implementação DDD

#### Fase 1 - Domain Core (3 meses)

- **Implementação de Aggregates principais** (IssuedLicense, LicenseHolder)
  - Entities JPA com anotações Spring Data

  - Value Objects com validações Bean Validation

  - Domain Services com @Service e @Transactional

- **Repository Pattern com Spring Data JPA**
  - Interfaces JpaRepository customizadas

  - Queries nativas para PostgreSQL com RLS

  - Specifications para consultas dinâmicas

- **Segurança e Autenticação**
  - Spring Security com JWT Token Provider

  - Row Level Security (RLS) no PostgreSQL

  - Method-level security com @PreAuthorize

- **Consulta básica com cache Redis**
  - @Cacheable em services críticos

  - Cache distribuído para consultas frequentes

#### Fase 2 - Application Services (2 meses)

- **Command/Query Pattern com Spring**
  - Controllers REST com @RestController

  - DTOs com MapStruct para conversão

  - Validation com Bean Validation (@Valid)

- **Integration Events com Spring Events**
  - @EventListener para eventos de domínio

  - @Async para processamento assíncrono

  - RabbitMQ para comunicação entre bounded contexts

- **Monitoramento e Observabilidade**
  - Spring Actuator para health checks

  - Micrometer para métricas customizadas

  - Scheduled tasks para validação automática

#### Fase 3 - Advanced Features (2 meses)

- **Complex Domain Services**
  - FeeCalculationService com regras de negócio complexas

  - TransferService com workflow de aprovação

  - NotificationService integrado com email/SMS

- **Auditoria Avançada com JPA Auditing**
  - @EntityListeners para auditoria automática

  - @CreatedDate, @LastModifiedDate, @CreatedBy

  - Histórico completo de alterações

- **Performance e Otimização**
  - Índices especializados no PostgreSQL

  - Connection pooling com HikariCP

  - Query optimization com @Query nativas

- **Relatórios e Business Intelligence**
  - JasperReports para relatórios complexos

  - Spring Batch para processamento em lote

  - Exportação para Excel/PDF

### 8.3 Critérios de Sucesso DDD

#### Métricas de Performance

- Tempo médio de consulta < 2 segundos com cache distribuído

- Disponibilidade > 99.5% com arquitetura resiliente

- Satisfação do usuário > 4.0/5.0

- Redução de 50% no tempo de processamento

#### Métricas de Negócio

- Aumento de 30% na taxa de renovação

- Redução de 40% em licenças vencidas

- Melhoria de 60% na arrecadação

- Diminuição de 70% em retrabalho manual

#### Métricas de Qualidade

- 100% dos conceitos de negócio modelados como Aggregates

- Cobertura de testes > 80% em Domain Services

- Zero incidentes com RLS e auditoria completa

## 8. Conclusão

Esta análise conceitual estabelece as bases para o desenvolvimento de um sistema robusto de gestão de licenças emitidas, complementando o sistema de parametrização existente. A implementação deve seguir uma abordagem incremental, priorizando funcionalidades core e expandindo gradualmente para recursos avançados.

O sucesso do projeto depende da integração efetiva com os sistemas existentes, da implementação adequada dos controles de segurança e do atendimento aos requisitos de performance e usabilidade especificados.
