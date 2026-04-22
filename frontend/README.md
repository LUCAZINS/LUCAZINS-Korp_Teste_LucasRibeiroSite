# Sistema de Emissão de Notas Fiscais - Frontend Angular

Frontend em Angular para o sistema de emissão de notas fiscais com arquitetura de microsserviços.

## Estrutura do Projeto

```
frontend/
├── src/
│   ├── app/
│   │   ├── components/
│   │   │   ├── produtos/
│   │   │   │   ├── produtos.component.ts
│   │   │   │   ├── produtos.component.html
│   │   │   │   └── produtos.component.css
│   │   │   └── notas-fiscais/
│   │   │       ├── notas-fiscais.component.ts
│   │   │       ├── notas-fiscais.component.html
│   │   │       └── notas-fiscais.component.css
│   │   ├── models/
│   │   │   └── index.ts
│   │   ├── services/
│   │   │   ├── estoque.service.ts
│   │   │   └── faturamento.service.ts
│   │   ├── app-routing.module.ts
│   │   ├── app.module.ts
│   │   ├── app.component.ts
│   │   ├── app.component.html
│   │   └── app.component.css
│   ├── main.ts
│   ├── index.html
│   └── styles.css
├── angular.json
├── package.json
├── tsconfig.json
└── README.md
```

## Acesso (Somente Site)

- Sistema publicado: `https://korp-teste-frontend.onrender.com`
- Não é necessário executar `npm install` ou `ng serve` para uso funcional

## Funcionalidades

### 1. Produtos
- Listar todos os produtos
- Criar novo produto (Código, Descrição, Saldo)
- Editar produto existente
- Deletar produto

### 2. Notas Fiscais
- Listar todas as notas fiscais com status (Aberta/Fechada)
- Criar nova nota fiscal com múltiplos itens
- Adicionar produtos com quantidade à nota
- Imprimir nota (fecha a nota e baixa saldo no estoque)
- Indicador de processamento durante impressão

## Serviços Utilizados

### EstoqueService
- URL: `http://localhost:5001/api/produtos`
- Métodos: GET, POST, PUT, DELETE, POST (baixar-saldo)

### FaturamentoService
- URL: `http://localhost:5002/api/notasfiscais`
- Métodos: GET, POST, POST (imprimir)

### Produção (Render)
- Sistema: `https://korp-teste-frontend.onrender.com`
- Estoque: `https://estoque-fbnk.onrender.com/api/produtos`
- Faturamento: `https://faturamento-i4h7.onrender.com/api/notasfiscais`

## Tratamento de Erros

- Mensagens de erro claras para o usuário
- Timeout em chamadas HTTP
- Fallback em caso de falha de conexão com microsserviços

## Ciclos de Vida Angular Utilizados

- **ngOnInit**: Carregamento de dados ao inicializar componentes
- **ngOnDestroy**: não implementado no estado atual

## RxJS Utilizados

- **Observable**: Para chamadas HTTP
- **subscription**: Para observar respostas
- **operators**: não há uso explícito de `map`/`catchError` no estado atual

## Próximos Passos

1. Acessar `https://korp-teste-frontend.onrender.com`
2. Validar o módulo de produtos (`/produtos`)
3. Validar o módulo de notas fiscais (`/notas-fiscais`)
4. Confirmar ausência de erros de comunicação no navegador
