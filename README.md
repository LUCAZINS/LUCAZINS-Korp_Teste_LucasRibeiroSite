# Sistema de Emissão de Notas Fiscais - Arquitetura Completa

Sistema completo com microsserviços em C# e frontend em Angular.

## Estrutura de Pastas

```
Korp_Teste_LucasRibeiro/
├── Servico.Estoque/      # Microsserviço de Estoque (.NET 10)
│   ├── Models/
│   ├── Controllers/
│   ├── Context/
│   └── Migrations/
├── Servico.Faturamento/  # Microsserviço de Faturamento (.NET 10)
│   ├── Models/
│   ├── Controllers/
│   ├── Context/
│   └── Migrations/
├── frontend/             # Frontend Angular 17
│   ├── src/
│   ├── angular.json
│   └── package.json
└── Microsservicos.slnx   # Solução Visual Studio
```

## Como Acessar (Somente Site)

### 1. Abrir o sistema em produção

Acesse: `https://korp-teste-frontend.onrender.com`

### 2. Navegar pelos módulos

- **Dashboard**: visão geral
- **Produtos**: cadastro, edição e exclusão
- **Notas Fiscais**: criação e impressão
- **Auditoria**: histórico de operações

### 3. Validar backend publicado (opcional)

- Estoque: `https://estoque-fbnk.onrender.com/health`
- Faturamento: `https://faturamento-i4h7.onrender.com/health`

## Endpoints Disponíveis

### Serviço de Estoque (Produção)

**Produtos:**
- `GET /api/produtos` - Listar todos
- `GET /api/produtos/{id}` - Obter por ID
- `POST /api/produtos` - Criar novo
- `PUT /api/produtos/{id}` - Atualizar
- `DELETE /api/produtos/{id}` - Deletar
- `POST /api/produtos/{id}/baixar-saldo` - Baixar saldo (usado na impressão)

### Serviço de Faturamento (Produção)

**Notas Fiscais:**
- `GET /api/notasfiscais` - Listar todas
- `GET /api/notasfiscais/{id}` - Obter por ID com itens
- `POST /api/notasfiscais` - Criar nova (com numeração sequencial automática)
- `POST /api/notasfiscais/{id}/imprimir` - Imprimir nota (fecha e baixa estoque)

## Fluxo da Aplicação

1. **Criar Produtos** (via Angular ou Swagger)
   - Acesso ao Serviço de Estoque

2. **Criar Nota Fiscal** (via Angular)
   - O Faturamento registra a nota com status "Aberta"
   - Numeração é gerada automaticamente (1, 2, 3...)

3. **Imprimir Nota Fiscal** (via Angular)
   - Faturamento chama Estoque para validar e baixar saldo
   - Se sucesso: nota passa para "Fechada", estoque é atualizado
   - Se falha: nota permanece "Aberta", saldo não é alterado

## Tratamento de Falhas

- **Timeout**: 10 segundos na chamada HTTP entre microsserviços
- **Fallback**: Se Estoque falhar, Faturamento retorna erro claro
- **Transação**: Rollback automático se algo der errado na impressão
- **Validação**: Saldo insuficiente, produto não encontrado, nota já fechada

## Banco de Dados

**PostgreSQL via Render**
- Serviço: postgres-korp
- Tipo: PostgreSQL (Render)
- Credenciais: definidas por variáveis de ambiente (`ConnectionStrings__ConexaoPadrao`)

Tabelas:
- produtos (Estoque)
- "NotasFiscais" (Faturamento)
- "NotasFiscaisItens" (Faturamento)

## Diagrama de Comunicação

```
┌─────────────────────────────┐
│   Frontend Angular 4200     │
├─────────────────────────────┤
│                             │
│  Componentes:               │
│  - Produtos                 │
│  - Notas Fiscais            │
│                             │
│  Serviços:                  │
│  - EstoqueService (5001)    │
│  - FaturamentoService (5002)│
└──────────────┬──────────────┘
               │
      ┌────────┴────────┐
      │                 │
      ▼                 ▼
┌──────────────┐  ┌──────────────┐
│   Estoque    │  │ Faturamento  │
│    5001      │  │    5002      │
├──────────────┤  ├──────────────┤
│              │  │              │
│ Produtos     │  │ Notas        │
│ BaixarSaldo  │  │ Imprimir     │
│              │  │ (chama API)  │
└──────┬───────┘  └───────┬──────┘
       │                  │
       └──────────┬───────┘
                  │
                  ▼
          ┌──────────────────┐
          │   PostgreSQL     │
          │   Render         │
          └──────────────────┘
```

## Tecnologias Utilizadas

**Backend:**
- .NET 10 / C#
- ASP.NET Core (Web API)
- Entity Framework Core
- PostgreSQL

**Frontend:**
- Angular 17
- TypeScript
- RxJS
- FormsModule

## Deploy em Produção (Render)

- Frontend: Web Service Docker (Nginx), com build Angular via `npm run build`
- Backend: dois Web Services Docker (`microsservicos-estoque` e `microsservicos-faturamento`)
- Banco: PostgreSQL gerenciado no Render (`postgres-korp`)
- Link de acesso ao sistema: `https://korp-teste-frontend.onrender.com`
- URLs de produção usadas no frontend:
   - Estoque: `https://estoque-fbnk.onrender.com`
   - Faturamento: `https://faturamento-i4h7.onrender.com`
- O backend usa `ForwardedHeaders` para operar corretamente atrás do proxy do Render

## Próximos Passos (Recomendados)

1. Adicionar autenticação (JWT)
2. Implementar logging mais robusto
3. Adicionar health checks entre serviços
4. Implementar circuit breaker (Polly)
5. Adicionar unit tests
6. Expandir documentação OpenAPI/Swagger
7. Gerar relatórios de nota fiscal em PDF

## Notas Importantes

- O sistema deve ser acessado pelo link de produção
- O banco de dados PostgreSQL no Render deve estar online e acessível
- CORS está configurado para aceitar requisições do frontend
- As migrations já foram executadas no banco
