# 🚀 Passo a Passo: Deploy no Render

## ✅ Pré-requisitos

- [ ] Conta no GitHub
- [ ] Repositório Git com o código atualizado
- [ ] Conta no [Render.com](https://render.com)
- [ ] Arquivos no repositório: `render.yaml`, `frontend/Dockerfile`, `Servico.Estoque/Dockerfile`, `Servico.Faturamento/Dockerfile`

---

## 📋 Passo 1: Preparar o Repositório Git

### 1.1 Commit e push das mudanças

```bash
git add .
git commit -m "chore: atualizar configuracao de deploy render"
git push
```

### 1.2 Verificar no GitHub

Confirme que os arquivos de deploy estão na branch que o Render usa (normalmente `main`).

---

## 🌐 Passo 2: Criar o Banco no Render

### 2.1 Criar PostgreSQL

1. Dashboard Render → **+ New** → **PostgreSQL**
2. Nome sugerido: `postgres-korp`
3. Plano: Free (teste) ou pago (produção)

### 2.2 Guardar a connection string

Depois da criação, copie a connection string para uso nas variáveis dos serviços.

---

## 🐳 Passo 3: Criar os Web Services

Crie 3 serviços Web (todos com **Environment: Docker**):

1. `microsservicos-estoque`
2. `microsservicos-faturamento`
3. `microsservicos-frontend`

### 3.1 Configurações recomendadas

| Serviço | Root Directory | Dockerfile Path | Docker Build Context |
|--------|-----------------|-----------------|----------------------|
| Estoque | *(vazio)* | `Servico.Estoque/Dockerfile` | *(padrão do repositório)* |
| Faturamento | *(vazio)* | `Servico.Faturamento/Dockerfile` | *(padrão do repositório)* |
| Frontend | `frontend` | `frontend/Dockerfile` | `frontend` |

---

## 🔐 Passo 4: Variáveis de Ambiente

### 4.1 Serviço Estoque (`microsservicos-estoque`)

Defina:

```env
ASPNETCORE_ENVIRONMENT=Production
ASPNETCORE_URLS=http://+:8080
ConnectionStrings__ConexaoPadrao=<connection-string-do-postgres-render>
```

### 4.2 Serviço Faturamento (`microsservicos-faturamento`)

Defina:

```env
ASPNETCORE_ENVIRONMENT=Production
ASPNETCORE_URLS=http://+:8080
ConnectionStrings__ConexaoPadrao=<connection-string-do-postgres-render>
EstoqueServiceUrl=https://estoque-fbnk.onrender.com
```

### 4.3 Serviço Frontend (`microsservicos-frontend`)

As variáveis NGINX não são obrigatórias para o fluxo atual, mas podem permanecer:

```env
NGINX_HOST=localhost
NGINX_PORT=8080
```

---

## 🧱 Passo 5: Build e Deploy

### 5.1 Ordem de deploy recomendada

1. Deploy de `microsservicos-estoque`
2. Deploy de `microsservicos-faturamento`
3. Deploy de `microsservicos-frontend`

### 5.2 Dica importante

Se houver erro de build antigo, rode **Manual Deploy** com **Clear build cache**.

---

## 🗄️ Passo 6: Migrations no Banco

Se as tabelas ainda não existirem, execute migrations manualmente com a connection string do Render.

Exemplo:

```bash
cd Servico.Estoque
dotnet ef database update --connection "<connection-string-do-postgres-render>"

cd ../Servico.Faturamento
dotnet ef database update --connection "<connection-string-do-postgres-render>"
```

---

## ✅ Passo 7: Verificação Final

Teste no navegador ou via `curl`:

```bash
# Saúde dos serviços
curl https://estoque-fbnk.onrender.com/health
curl https://faturamento-i4h7.onrender.com/health

# Endpoints principais
curl https://estoque-fbnk.onrender.com/api/produtos
curl https://faturamento-i4h7.onrender.com/api/notasfiscais
```

No frontend, valide:

- `https://korp-teste-frontend.onrender.com`
- `/produtos`
- `/notas-fiscais`

Sem erros de `0 Unknown Error` nas requisições HTTP.

---

## 🚨 Troubleshooting

### Problema: `ng: Permission denied` no build do frontend

Verifique se `frontend/.dockerignore` contém `node_modules` para não copiar dependências do host para a imagem.

### Problema: `npm run build` retorna `127`

Confirme no `frontend/Dockerfile` uso de:

```dockerfile
RUN npm ci --include=dev
RUN npm run build
```

### Problema: Frontend com `status 0 Unknown Error`

1. Confirme URLs de produção no `frontend/src/environments/environment.prod.ts`
2. Confirme CORS habilitado nos serviços
3. Confirme que os serviços backend estão `Live` no Render

### Problema: Faturamento não acessa Estoque

Verifique a variável `EstoqueServiceUrl` no serviço de faturamento apontando para a URL pública correta do estoque.

---

## 📊 Monitoramento

No Render Dashboard, use:

- **Logs** para erro de runtime/build
- **Events** para histórico de deploy
- **Metrics** para consumo de CPU/memória

---

## 💡 Dicas Importantes

1. Free tier pode entrar em sleep por inatividade
2. Sempre use `https://` entre serviços públicos do Render
3. Após alterar URL de API no frontend, é obrigatório novo deploy do frontend
4. Mantenha secrets somente em variáveis de ambiente do Render

---

## Próximas Otimizações (depois)

- [ ] Adicionar CI/CD (pipeline de build + deploy)
- [ ] Adicionar testes automatizados
- [ ] Publicar documentação Swagger também em produção
- [ ] Definir domínio customizado
- [ ] Adicionar observabilidade (logs estruturados e tracing)

---

**Dúvidas?** Consulte também o [README.md](./README.md) para visão geral da arquitetura.
