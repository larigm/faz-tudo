# 🎨 Tokens de Design

**Projeto:** Faz-Tudo  
**Versão:** 1.0.0 · Versão Inicial aprovada pelo aluno  
**Última atualização:** 2026-09-20

> 🤖 **Este documento existe para a IA parar de inventar um botão diferente a cada
> tela.** Não é um design system — é o mínimo que dá à prototipagem assistida algo a
> que obedecer.

---

## Paleta

Nome semântico, nunca `azul-2` — a cor muda, o papel dela não.

| Token          | Valor                 | Onde se usa                                                    |
| -------------- | --------------------- | -------------------------------------------------------------- |
| `primaria`     | `#0066CC`             | Ação principal, botões de destaques e links                    |
| `superficie`   | `#FFFFFF` / `#F5F5F5` | Fundo de cards, modal e estrutura de painel                    |
| `texto`        | `#212121`             | Texto padrão e títulos principais                              |
| `texto-suave`  | `#666666`             | Legendas, notas de apoio e rótulos secundários                 |
| `perigo`       | `#D32F2F`             | Mensagens de erro, ações de cancelamento ou rejeição           |
| `sucesso`      | `#2E7D32`             | Confirmações de pagamento, sucesso de ações e status concluído |
| `desabilitado` | `#BDBDBD`             | Controles inativos e botões desabilitados                      |

## Escala de espaçamento

Uma progressão só, usada em tudo.

| Token | Valor  |
| ----- | ------ |
| `xs`  | `4px`  |
| `sm`  | `8px`  |
| `md`  | `16px` |
| `lg`  | `24px` |
| `xl`  | `32px` |

## Tipografia

| Token       | Família · tamanho · peso              | Papel                                   |
| ----------- | ------------------------------------- | --------------------------------------- |
| `titulo-lg` | Inter / System UI · `24px` · Bold     | Títulos principais das telas            |
| `titulo-md` | Inter / System UI · `18px` · SemiBold | Títulos de seções e títulos de cards    |
| `corpo`     | Inter / System UI · `14px` · Regular  | Texto corrido, descrições e inputs      |
| `legenda`   | Inter / System UI · `12px` · Regular  | Textos de apoio, horários e observações |

## Estados de botão

| Estado           | Aparência                                                         |
| ---------------- | ----------------------------------------------------------------- |
| `normal`         | Fundo cor `primaria` sólida com texto branco em negrito           |
| `hover`          | Escurecimento suave (10%) da cor `primaria`                       |
| `foco (teclado)` | Anel de destaque externo (outline `2px` com offset)               |
| `desabilitado`   | Fundo `desabilitado` com texto `texto-suave` sem evento de clique |
| `carregando`     | Opacidade reduzida, spinner animado e rótulo "Processando..."     |

## Protótipo

**Link:** Pendente (a criar durante a prototipagem assistida)  
**Telas:** Telas das jornadas principais (Busca de profissionais, Solicitação de serviço, Checkout de pagamento e Painel de acompanhamento).
