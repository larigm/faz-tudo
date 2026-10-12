# 🎨 Tokens de Design

**Projeto:** Faz-Tudo  
**Versão:** 1.1.0 · Tokens derivados do Design System do Figma  
**Última atualização:** 2026-10-11  

> 🤖 **Este documento existe para a IA parar de inventar um botão diferente a cada
> tela.** Não é um design system — é o mínimo que dá à prototipagem assistida algo a
> que obedecer.

---

## Paleta

Nome semântico, nunca `azul-2` — a cor muda, o papel dela não.

| Token              | Valor     | Onde se usa                                                                    |
| ------------------ | --------- | ------------------------------------------------------------------------------ |
| `primaria`         | `#267052` | Ação principal, botões de destaque (Aprovar, Aceitar, Simular pagamento)      |
| `superficie`       | `#FFFFFF` | Fundo dos cards e containers                                                   |
| `superficie-fundo` | `#F5F7F5` | Fundo geral da página                                                          |
| `texto`            | `#24372F` | Texto principal e títulos                                                      |
| `texto-suave`      | `#626F68` | Texto secundário (datas, endereços, legendas e apoios)                         |
| `perigo`           | `#AF4946` | Ações destrutivas ou negativas (Recusar, Rejeitar, Cancelar) e alertas de erro |
| `sucesso`          | `#267052` | Indicadores de confirmação, sucesso e status concluído                         |
| `desabilitado`     | `#DFE7E1` | Fundo de botões e controles inativos (com texto em `#626F68`)                  |

---

## Escala de espaçamento

Uma progressão só, usada em tudo.

| Token | Valor  |
| ----- | ------ |
| `xs`  | `4px`  |
| `sm`  | `8px`  |
| `md`  | `16px` |
| `lg`  | `24px` |
| `xl`  | `32px` |

---

## Tipografia

| Token       | Família · tamanho · peso | Papel                                           |
| ----------- | ------------------------ | ----------------------------------------------- |
| `titulo`    | Lato · 23px · Bold       | Título principal das telas                      |
| `subtitulo` | Lato · 17px · Bold       | Títulos de seções, cabeçalhos de cards e modais |
| `corpo`     | Lato · 13-14px · Regular | Texto corrido, campos de formulário e inputs    |

---

## Estados de botão

* **Borda & Sombra:** `border-radius: 8px`. Sem sombra (`drop shadow`) em nenhum elemento.

| Estado           | Aparência                                                                              |
| ---------------- | -------------------------------------------------------------------------------------- |
| `normal`         | Fundo `primaria` (`#267052`) com texto branco em negrito, `border-radius: 8px`         |
| `hover`          | Escurecimento suave da cor `primaria` (`#1e5a42`), sem sombra                          |
| `foco (teclado)` | Contorno outline `2px` externo com contraste nítido, sem sombra                        |
| `desabilitado`   | Fundo `desabilitado` (`#DFE7E1`) com texto em `texto-suave` (`#626F68`), sem sombra   |
| `carregando`     | Fundo `primaria` com opacidade reduzida, spinner animado e rótulo "Processando..."     |

---

## Protótipo

**Link:** [Protótipo no Figma](https://www.figma.com/proto/YyzpTPWguiYt9UtFqmFkBV/Sem-t%C3%ADtulo?node-id=2-9277&t=3nCrPZY7zo0f1i6q-1&scaling=min-zoom&content-scaling=fixed&page-id=0%3A1&starting-point-node-id=2%3A9277&show-proto-sidebar=1)

**Telas:**
- Busca de Profissionais
- Perfil e Solicitação (com validação)
- Painel do Profissional
- Fazer Contraproposta
- Recusar Pedido
- Checkout — Aguardando Pagamento / Confirmado / Expirado
- Acompanhamento — Profissional / Cliente (confirmar conclusão) / Concluído / Avaliação enviada
- Moderação — fila + Rejeitar cadastro (modal) + Cadastro aprovado/rejeitado
