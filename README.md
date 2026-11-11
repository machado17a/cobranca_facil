# CobrançaFácil — App do Haras

Aplicativo para iPhone que lembra de cobrar os clientes de hospedagem de animais
(cavalos/éguas/potros) e controla as despesas do **Haras** e da **Casa**.

É um web app de **um único arquivo** (`index.html`), sem servidor e sem dependências
externas: os dados ficam salvos no próprio iPhone.

## Privacidade

**Este repositório não contém nenhum dado pessoal** (nomes de clientes, animais, valores,
telefones, chave PIX). O código começa vazio. O cadastro real fica num arquivo `.json`
privado, que é enviado por fora (WhatsApp, e-mail) e importado no app. Por isso o repositório
pode ser público sem expor nada.

> Nunca faça commit de um arquivo de cadastro/backup real. O `.gitignore` já bloqueia
> `cadastro-haras*.json` e `backup*.json`.

## O que o app faz

- **Cobranças (Hospedagem)** — lista em ordem crescente pelo dia de pagamento (01 → 31).
  Os pagos **não** vão para o fim da lista e **não** ficam riscados.
  - Numeração geral (`Animal #01`, `#02`…) e por cliente (`Animal 1 de 2 do cliente`).
  - Situação automática: *Atrasado X dias*, *Vence hoje*, *Vence em X dias*, *Em aberto*, *Pago*.
  - Botão **Pago**: registra o pagamento e passa o vencimento para o mês seguinte.
    **Reabrir** desfaz. Ao virar o mês, o animal pago volta a ficar "em aberto" sozinho.
  - Suporta "último dia útil do mês" e descrição livre do dia (ex.: "entre 30 e 05").
- **Despesas do Haras** — Ração Peletizada, Ração Farelada, Farelo de Trigo, Combustível,
  Óleo 2T, Medicamentos Veterinários, Casqueamento e Ferrageamento, Funcionários.
- **Despesas da Casa** — Luz, Condomínio, IPTU, Mercado, Curso, Carro 1, Carro 2, Carro 3.
  - Edição rápida do valor e do dia direto na lista + Salvar; Pago/Reabrir; Editar; Excluir.
  - O "Pago" das despesas volta para "Em aberto" automaticamente no mês seguinte.
- **Balanço mensal** no topo: Receita de hospedagem, Despesas Haras, Despesas Casa e Saldo líquido.
- **Avisos**
  - Exporta os vencimentos para o **Calendário do iPhone** (`.ics`, repetição mensal, alerta
    no horário e X dias antes) — é o que garante o alarme mesmo com o app fechado.
  - Ao abrir o app: som de lembrete + notificação (uma vez por dia por cobrança).
- **WhatsApp + PIX** — mensagem pronta (Preventivo / Vence hoje / Em atraso) com a chave PIX.
- **Ajustes** — nome do negócio, chave PIX, textos das mensagens e arquivos:
  - **Backup / cadastro completo (.json)** — animais, proprietários, despesas e ajustes.
  - **Planilha (.csv)** — abre no Excel/Numbers.
  - Os botões abrem o menu **Compartilhar** do iPhone (*Salvar em Arquivos*, WhatsApp,
    e-mail, AirDrop). Onde não houver, o arquivo é baixado.
  - **Restaurar / importar** o `.json`.
  - Lembrete na tela de Cobranças quando o último backup tem mais de 7 dias.
- Valores podem ser digitados como `1.400,00`, `1400,50`, `1.400` ou `1400`.

## Como colocar no iPhone

1. No GitHub: repositório › **Settings** › **Pages** › *Deploy from a branch* › branch `main`,
   pasta `/ (root)` › **Save**. Em ~1 minuto o app fica em
   **https://machado17a.github.io/cobranca_facil/**
2. No iPhone, abra o endereço no **Safari** › **Compartilhar** › **Adicionar à Tela de Início**.
3. Abra pelo ícone **Cobrança iOS**. Na primeira vez a lista estará vazia.
4. Importe o cadastro: abra o arquivo `.json` recebido (ex.: no WhatsApp) › **Compartilhar** ›
   **Salvar em Arquivos**. No app: **Importar cadastro (.json)** (ou Ajustes › Restaurar /
   importar) e escolha o arquivo.

**Onde ficam os dados:** só no iPhone, no armazenamento do Safari para o endereço do app
(nada vai para o GitHub ou para servidores). Apagar os dados do Safari apaga o cadastro —
faça backup em Ajustes de vez em quando.

## Para desenvolver

Não há build. Basta servir a pasta:

```bash
python3 -m http.server 8000
# abra http://localhost:8000
```

| Arquivo | Para quê |
|---|---|
| `index.html` | O app inteiro (HTML + CSS + JS) |
| `manifest.webmanifest` | Nome/ícone ao instalar na Tela de Início |
| `sw.js` | Funciona sem internet depois da primeira abertura |
| `icons/` | Ícones do app |
| `docs/exemplo-cadastro.json` | Exemplo **fictício** do formato do arquivo de cadastro |
| `docs/HANDOVER.md` | Requisitos vindos do protótipo anterior |

Ao alterar o `index.html`, aumente `APP_VERSION` e o nome do cache em `sw.js`
(`cobrancafacil-vX`) para o iPhone pegar a versão nova.

### Formato do arquivo de cadastro (`tipo: cadastro-haras`)

```js
{
  app: "CobrancaFacil", tipo: "cadastro-haras", versao: 2, geradoEm,
  resumo: { totalProprietarios, totalAnimais, receitaMensal, despesasHaras, despesasCasa },
  proprietarios: [{
    nome, telefone, totalAnimais, mensalidadeTotal,
    animais: [{ id, numeroGeral, numeroNoCliente, nome, mensalidade, diaPagamento,
                regraDia /* dia-fixo | ultimo-dia-util */, descricaoDia, proximoVencimento,
                situacao /* em-aberto | pago */, repeticao /* mensal | quinzenal | semanal | uma-vez */,
                aviso: { horario, antecedenciaDias }, servico, observacoes,
                pagamentos: [{ referencia, valor, pagoEm }] }]
  }],
  despesasHaras: [{ id, descricao, categoria, valor, diaVencimento, situacao, mesPago }],
  despesasCasa:  [ /* mesmo formato */ ],
  ajustes: { nomeNegocio, chavePix, mensagens: { preventiva, venceHoje, emAtraso } }
}
```

Ao importar, as seções presentes no arquivo substituem as do app; seções ausentes
(ex.: um arquivo só com `proprietarios`) mantêm o que já existe. O app também aceita
backups no formato interno (`charges`, `harasExpenses`, `casaExpenses`, `settings`).
