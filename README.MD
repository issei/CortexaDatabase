# Cortexa Database

Bem-vindo ao repositório do **Cortexa Database**. Este projeto gerencia o schema e as migrações do banco de dados do Cortexa, utilizando uma abordagem moderna de CI/CD com **Neon** para garantir agilidade, segurança e confiança em cada mudança.

## Tabela de Conteúdos

- [Filosofia: Database as Code](#filosofia-database-as-code)
- [Como Funciona o CI/CD](#como-funciona-o-ci-cd)
  - [Abertura de um Pull Request](#1-abertura-de-um-pull-request)
  - [Fechamento de um Pull Request](#2-fechamento-de-um-pull-request)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Configuração Necessária](#configuração-necessária)
- [Como Contribuir](#como-contribuir)

## Filosofia: Database as Code

Tratamos a infraestrutura do nosso banco de dados da mesma forma que tratamos o código. Graças à funcionalidade de **Database Branching** da Neon, abandonamos os ambientes de desenvolvimento compartilhados e problemáticos.

A cada Pull Request, um **banco de dados temporário, isolado e instantâneo** é criado. Isso nos permite:
- **Testar migrações** sem impactar o ambiente de produção ou outros desenvolvedores.
- **Validar o schema** de forma automatizada.
- **Revisar o impacto** das mudanças no banco de dados diretamente no Pull Request.

## Como Funciona o CI/CD

Nosso processo é orquestrado pelo workflow do GitHub Actions localizado em `.github/workflows/neon_workflow.yml`.

### 1. Abertura de um Pull Request

Quando um PR é aberto ou atualizado, o pipeline executa as seguintes ações:

1.  **Cria um Branch no Neon:** Um novo branch do banco de dados é criado com um nome único (ex: `preview/pr-123-minha-feature`).
2.  **Aplica as Migrações:** Os scripts `.sql` da pasta `migrations/` são executados no banco de dados temporário.
3.  **Executa Testes:** Testes unitários são executados para garantir a saúde do código.
4.  **Comenta no PR (Opcional):** Uma análise das diferenças de schema é postada como um comentário no PR, facilitando a revisão.

Se qualquer etapa falhar, o PR é marcado com um erro, impedindo o merge de código quebrado.

### 2. Fechamento de um Pull Request

Quando o PR é fechado (seja por merge ou descarte), o workflow automaticamente:

1.  **Deleta o Branch do Neon:** O banco de dados temporário associado ao PR é permanentemente removido, mantendo nosso ambiente limpo e sem custos residuais.

## Estrutura do Projeto

```
.
├── .github/
│   └── workflows/
│       └── neon_workflow.yml   # <-- Nosso pipeline de CI/CD
├── docs/
│   └── spec_database.md        # <-- Documentação técnica detalhada
├── migrations/
│   └── V1__create_initial_tables.sql # <-- Scripts de migração do banco
└── README.MD                   # <-- Este arquivo
```

## Configuração Necessária

Para que a integração funcione, as seguintes variáveis e segredos devem ser configurados nas `Settings` do repositório GitHub:

- **Secrets:**
  - `NEON_API_KEY`: Chave da API da Neon para autenticação.
- **Variables:**
  - `NEON_PROJECT_ID`: ID do projeto na Neon que será gerenciado.

## Como Contribuir

1.  Crie um novo branch para sua feature ou correção.
2.  Adicione seus commits. Se a mudança impactar o banco de dados, adicione um novo arquivo de migração na pasta `migrations/`.
3.  Abra um Pull Request direcionado ao branch `main` (ou `develop`).
4.  Aguarde o pipeline de CI/CD executar. Verifique se todos os testes passaram.
5.  Peça a revisão de um colega. O revisor poderá ver o impacto no schema diretamente nos comentários do PR.
6.  Após a aprovação e o merge, o pipeline cuidará da limpeza automaticamente.
