# Requisitos vindos do protótipo anterior

O primeiro protótipo foi feito em outra ferramenta; o código não veio junto, então o app foi
reconstruído a partir da especificação abaixo. **Dados pessoais (clientes, animais, valores)
foram propositalmente deixados fora do repositório** — eles ficam no arquivo de cadastro
privado, importado no app.

## Objetivo

App de iPhone que lembra de cobrar os clientes de hospedagem de animais, em ordem crescente
de data de pagamento, numerando os animais por cliente e no geral, com abas de Despesas do
Haras e Despesas da Casa (três veículos separados).

## Requisitos → status

| # | Requisito | Status |
|---|---|---|
| 1 | Funcionar no iPhone com lembretes de cobrança | ✅ calendário `.ics`, som, notificação |
| 2 | Cadastro dos animais e proprietários | ✅ via arquivo de cadastro privado |
| 3 | Ordem crescente pelo dia de pagamento, sem jogar pagos para o fim | ✅ |
| 4 | Sem "Baia X"; numeração por cliente e geral | ✅ |
| 5 | Nome do cliente pago não riscado | ✅ |
| 6 | Aba Despesas do Haras (8 itens) | ✅ |
| 7 | Aba Despesas da Casa com 3 carros separados | ✅ |
| 8 | Editar tudo pelos botões do app | ✅ |
| 9 | Mensagem WhatsApp com PIX (3 tons) | ✅ |
| 10 | Exportar alarmes para o Calendário (.ics) | ✅ individual e em lote, com repetição mensal |
| 11 | Som (acorde 587/880/1174 Hz) e notificações | ✅ |
| 12 | Planilha CSV e backup/restauração JSON | ✅ (menu Compartilhar do iPhone) |

## Mudanças em relação ao protótipo

- **Sem CDN** (Tailwind, Lucide e Google Fonts removidos): abre sem internet e carrega mais rápido.
- **Tela de Início de verdade:** `manifest.webmanifest`, ícone e service worker (offline).
- **Barra inferior com 5 abas** (Cobranças, Desp. Haras, Desp. Casa, Avisos, Ajustes).
- **Pagamento mensal automático:** "Pago" avança o vencimento e guarda o histórico; na virada
  do mês volta a "em aberto" sozinho. As despesas também.
- **Último dia útil** calculado de verdade (pula sábado/domingo).
- **Sem dados pessoais no código:** o app começa vazio e o cadastro é importado de um `.json`.
