# 🗺️ Jornadas de Usuário

**Projeto:** Faz-Tudo  
**Versão:** 1.0.0 · Versão Inicial aprovada pelo aluno  
**Última atualização:** 2026-09-20

> 🤖 **Este documento é a fonte da verdade sobre O QUE A PESSOA VIVE na tela** —
> o caminho do primeiro clique até o objetivo, e principalmente os pontos onde ela
> trava, espera ou desiste.
>
> 🚫 **Não duplique:** regra de negócio mora no `prd.md`; estado, entidade e contrato
> moram no `architecture.md`. Aqui mora o caminho.

---

## Jornada 1 — US04 (Pagamento e Confirmação de Serviço)

**Story:** US04  
**Critérios que ela marca:** sai do site e volta · depende do tempo · depende de outra pessoa agir · pode ser abandonada no meio

```mermaid
flowchart TD
    A(["Solicitação no status 'Aceita'"]) --> B{"Solicitação ainda está<br/>aguardando pagamento?"}
    B -->|"não"| C["Exibe aviso: 'Solicitação cancelada ou já paga'<br/>e redireciona ao painel"]
    B -->|"sim"| D["«pessoa» clica em 'Pagar Serviço'<br/>e acessa o checkout sandbox"]
    D --> E["«pessoa» insere os dados<br/>de teste e confirma"]
    E --> F{"Resultado do checkout<br/>(sandbox)"}
    F -->|"cartão recusado / erro"| G["Exibe erro de pagamento<br/>e permite tentar novamente"]
    F -->|"aprovado no site"| H["Retorna para tela de sucesso<br/>aguardando confirmação"]
    F -->|"cliente fecha a aba<br/>ou cai a conexão"| X1[["Pessoa fecha a aba no checkout —<br/>e a solicitação fica AGUARDANDO PAGAMENTO para sempre?"]]

    H --> I{"Confirmação do gateway"}
    I -->|"confirmou"| J(["Status muda para CONFIRMADO"])
    I -->|"resposta não chegou"| K["«pessoa» vê 'Processando pagamento'<br/>no painel"]

    style X1 fill:#ffe0e0,stroke:#c62828
```

**O que decidimos sobre o nó vermelho (X1):**

A solicitação muda para o status `Aguardando Pagamento` assim que o cliente inicia o checkout, reservando o horário por até 3 horas. Se o cliente fechar a aba, a bateria acabar ou a conexão cair, a solicitação continua nesse estado — o profissional vê o horário como reservado e não pode aceitar outro pedido no mesmo período, evitando conflito de agenda. A confirmação definitiva do pagamento vem sempre do webhook enviado pelo gateway, nunca da tela que o cliente vê no navegador. Se as 3 horas se esgotarem sem a confirmação do webhook, a solicitação expira automaticamente e o horário é liberado novamente para o profissional. Caso o webhook chegue atrasado, mesmo depois da expiração, o pagamento ainda é confirmado — como a cobrança já foi efetuada no lado do cliente, o serviço é considerado válido.

---

## Dúvidas em aberto

| #   | Dúvida                                                                                                                                                          | Onde ela precisa ser resolvida |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| 1   | Mapeamento exato dos estados da solicitação (`Pendente`, `Aceita`, `Em Negociação`, `Aguardando Pagamento`, `Confirmado`, `Expirado`, `Concluído`, `Cancelado`) | `docs/architecture.md`         |
