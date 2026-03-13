# n8n-assistente-financeiro

## Guia dos Arquivos — n8n + Telegram + IA Financeira + Dashboard

Este repositório contém os arquivos necessários para rodar dois workflows no n8n que formam um assistente financeiro completo via Telegram, com registro inteligente de gastos, geração de relatórios e um dashboard web interativo.

---

### workflow_assistente_financeiro.json

Arquivo principal do agente financeiro. Como usar:

Abra o n8n → importe o conteúdo do JSON.

Este fluxo recebe mensagens no Telegram (texto, áudio ou imagem) e usa um agente de IA para interpretar e executar ações financeiras automaticamente.

Ajuste as credenciais do Telegram Bot, OpenAI e Redis antes de rodar.

#### O que este workflow faz:

- **Recepção de mensagens**: Escuta mensagens via Telegram Trigger e normaliza texto, áudio e imagens
- **Análise de mídia**: Transcreve áudios (Whisper), analisa imagens de notas fiscais (GPT-4o) e extrai dados automaticamente
- **Agente financeiro (Brain)**: Um agente LLM com três tools disponíveis:
  - `registrar_gasto` — registra entradas e saídas financeiras na data table do n8n
  - `gerar_relatorio` — consulta registros filtrados por período e/ou categoria
  - `calcular` — executa operações matemáticas para cálculos financeiros
- **Memória de contexto**: Usa Redis para manter o histórico da conversa por usuário
- **Resposta ao usuário**: Envia a resposta formatada de volta ao Telegram

#### Categorias suportadas:

Saídas: Alimentação, Transporte, Moradia, Saúde, Lazer, Educação, Vestuário, Tecnologia, Financeiro, Outros

Entradas: Salário, Freelance, Investimento, Venda, Outros

#### Exemplos de uso:

```
"R$45 mercado hoje pix"         → registra saída em Alimentação
"Aluguel R$800 dia 05/03 pix"   → registra saída em Moradia/Fixo
"Recebi meu salário R$5000"     → registra entrada em Salário
"gastos de fevereiro"           → gera relatório do mês
"quanto gastei com transporte?" → filtra por categoria
```

#### Credenciais necessárias:

- Telegram Bot (token do BotFather)
- OpenAI API (para o agente, transcrição e análise de imagem)
- Redis (para memória de contexto entre mensagens)
- n8n Data Table configurada com as colunas: `IdConversa`, `Mes`, `Data`, `Categoria`, `Valor`, `Tipo`, `Metodo`, `Descricao`

---

### dashboard_workflow.json

Workflow do dashboard web financeiro. Como usar:

Abra o n8n → importe o conteúdo do JSON.

Este fluxo expõe dois endpoints webhook:

- `GET /webhook/dashboard-financeiro` — serve o dashboard como página HTML
- `POST /webhook/dashboard-data` — recebe filtros de data e retorna os registros da data table em JSON

Ajuste o `IdConversa` fixo no nó `Get row(s)` para o ID do seu usuário do Telegram antes de rodar.

#### O que este workflow faz:

- **Serve o dashboard**: Renderiza uma página HTML completa com gráficos interativos usando Chart.js
- **Fornece os dados**: Consulta a data table do n8n com filtros de período e retorna os registros em JSON para o frontend

#### Funcionalidades do dashboard:

- Cards de métricas: Receita total, Despesas totais e Saldo do período
- Gráfico de linha: Evolução de gastos vs. faturamento agrupado por dia, semana ou mês
- Gráfico de barras horizontal: Benchmark visual entre receita e despesa
- Gráfico de rosca: Distribuição de gastos por categoria
- Tabela de extrato: Lista completa de lançamentos com data, categoria, descrição e valor

#### Credenciais necessárias:

- n8n Data Table (mesma tabela usada pelo workflow do assistente)

---

### Estrutura do Repositório

```
n8n-assistente-financeiro/
├── workflow_assistente_financeiro.json   # Workflow do agente financeiro via Telegram
├── dashboard_workflow.json               # Workflow do dashboard web
└── README.md
```

---

### Sobre

Este projeto foi desenvolvido para automatizar o controle financeiro pessoal via Telegram, usando IA para interpretar mensagens em linguagem natural e registrar transações automaticamente, sem necessidade de preencher formulários.
