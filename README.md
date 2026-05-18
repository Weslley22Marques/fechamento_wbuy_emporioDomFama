# Dom Fama — Sistema de Fechamento Financeiro WBuy

Sistema interno de auditoria financeira e consolidação operacional integrado à API WBuy através de n8n, desenvolvido para processamento de pedidos, análise de faturamento e controle gerencial do e-commerce do Empório Dom Fama.

---

# Arquitetura da Aplicação

```txt
Frontend (HTML / JS)
        ↓
EasyPanel Reverse Proxy (/api)
        ↓
n8n Webhook Engine
        ↓
API WBuy
        ↓
Processamento Financeiro
        ↓
Relatório Consolidado
```

---

# Objetivo do Sistema

O sistema foi desenvolvido para automatizar:

* auditoria financeira de pedidos
* processamento de planilhas com IDs de pedidos
* consultas automáticas na API WBuy
* consolidação de faturamento
* cálculo operacional
* análise por canal de venda

---

# Funcionalidades

## Processamento de Pedidos

* Upload de planilhas `.xlsx`, `.xls` e `.csv`
* Extração automática dos IDs dos pedidos
* Consulta automática na API WBuy
* Processamento em lote
* Controle de rate limit

---

## Consolidação Financeira

O sistema calcula automaticamente:

* faturamento bruto
* lucro estimado
* custos operacionais
* taxas marketplace
* custos de frete
* ticket médio
* margem operacional
* total de pedidos válidos

---

## Separação por Canal

Detecção automática dos canais:

* Mercado Livre
* Shopee
* Loja Própria / Site

---

# Tecnologias Utilizadas

## Frontend

* HTML5
* CSS3
* JavaScript Vanilla

## Infraestrutura

* Nginx
* EasyPanel
* Docker
* n8n Self Hosted

## Backend / Dados

* PostgreSQL
* API WBuy

---

# Estrutura do Projeto

```txt
.
├── index.html
├── assets/
│   └── dom-fama-logo.jpeg
├── Dockerfile
├── workflow-domfama.json
└── README.md
```

---

# Deploy via EasyPanel

## Tipo do Serviço

```txt
Aplicativo
```

---

# Build Strategy

```txt
Dockerfile
```

---

# Dockerfile

```dockerfile
FROM nginx:alpine

COPY . /usr/share/nginx/html

EXPOSE 80
```

---

# Porta da Aplicação

```txt
80
```

---

# Frontend

O frontend opera como aplicação estática servida via nginx interno do container Docker.

---

# Proxy Reverso `/api`

No EasyPanel configure:

## Origem

```txt
/api
```

## Destino

```txt
http://IP_DO_N8N:3000/webhook
```

## Exemplo

```txt
http://187.127.10.227:3000/webhook
```

---

# Configuração Frontend

No arquivo `index.html`:

```js
const API_BASE = "/api";
```

> NÃO utilizar IP direto da VPS no frontend.

---

# Sistema de Autenticação

A autenticação atual opera via token interno de sessão gerado no workflow n8n.

## Credenciais padrão

### Usuário

```txt
admin
```

### Senha

```txt
DomFama@2026#
```

---

# Segurança

## O token da WBuy:

* NÃO fica exposto no frontend
* NÃO é enviado ao navegador
* NÃO aparece em requests públicos
* permanece exclusivamente no workflow n8n

---

# Compatibilidade n8n Self Hosted

O ambiente atual possui restrições de sandbox nos Code Nodes.

## Recursos bloqueados

```txt
$env
require('crypto')
```

---

# Estratégia adotada

Por compatibilidade:

* JWT assinado foi removido
* autenticação utiliza token interno simples
* sem dependência de módulos externos

---

# Fluxo Operacional

```txt
Upload da planilha
        ↓
Extração dos IDs
        ↓
Limpeza da tabela temporária
        ↓
Gravação dos códigos da consulta
        ↓
Consulta individual na WBuy
        ↓
Normalização dos dados
        ↓
Processamento financeiro
        ↓
Consolidação do relatório
        ↓
Retorno JSON
```

---

# Banco de Dados

O sistema NÃO armazena histórico completo de pedidos. Devido a capacidade computacional da VPS utilizada.

---

# Estrutura armazenada

Apenas:

```txt
consulta_codigo
pedido_codigo
criado_em
```

---

# Estratégia de Persistência

Antes de cada nova análise:

```sql
DELETE FROM consulta_pedidos_wbuy;
```

---

# Benefícios da Estratégia

* baixo consumo de armazenamento
* alta velocidade operacional
* VPS enxuta
* sem crescimento descontrolado do banco
* menor custo de infraestrutura

---

# Controle de Rate Limit

O node Wait do n8n NÃO aceita `milliseconds`.

## Configuração correta

```txt
1 second
```

## Configuração inválida

```txt
700 milliseconds
```

---

# Status Ignorados

Pedidos considerados inválidos:

```txt
1
2
6
8
10
11
```

---

# Requisitos do Ambiente

* EasyPanel
* Docker
* n8n Self Hosted
* PostgreSQL
* acesso API WBuy
* VPS Linux

---

# Melhorias Futuras

## Infraestrutura

* Redis Queue
* cache incremental
* processamento paralelo
* workers dedicados

## Backend

* autenticação JWT real
* múltiplos usuários
* histórico persistente
* dashboard analítico

## Relatórios

* exportação XLSX
* exportação PDF
* gráficos financeiros
* indicadores operacionais

---

# Arquitetura Recomendada

```txt
Frontend
        ↓
EasyPanel Reverse Proxy
        ↓
n8n
        ↓
WBuy API
```

---

# Observações Técnicas

O sistema foi otimizado para:

* VPS de baixo custo
* processamento rápido
* baixa persistência
* consultas temporárias
* alto volume operacional sem crescimento excessivo do banco

---

# Licença

Sistema interno proprietário — Weslley Marques.
