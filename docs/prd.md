# 📄 Product Requirements Document (PRD)

**Projeto:** Faz-Tudo  
**Versão:** 1.0.0 · Versão Inicial aprovada pelo aluno  
**Última atualização:** 2026-09-17

> 🤖 **Este documento é a fonte da verdade sobre o QUE o produto faz.** Regra de
> negócio que não estiver aqui não existe — nem para a equipe, nem para a IA.
> Tecnologia **não** se discute aqui: isso é assunto do `architecture.md`.

---

## 🎯 1. Visão Geral e Objetivo

**O problema:** Clientes têm dificuldade de encontrar profissionais de serviços residenciais confiáveis, qualificados e disponíveis na sua região.

**A solução:** O **Faz-Tudo** é uma plataforma que conecta clientes a profissionais de serviços residenciais com base no tipo de serviço e na localização, permitindo a contratação, moderação por administradores e pagamento direto pela plataforma.

**Como saberemos que deu certo:** Um cliente consegue pesquisar um serviço, encontrar profissionais aprovados da sua região, ver seus perfis e enviar uma solicitação. O profissional consegue receber essa solicitação e aceitar, recusar ou negociar os detalhes, permitindo que o serviço seja confirmado entre as duas partes.

---

## 📖 2. Glossário Ubíquo

| Termo                         | Significa                                                                                       | Não confundir com                                                        |
| :---------------------------- | :---------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------- |
| **Cliente**                   | Pessoa que utiliza a plataforma para encontrar profissionais e solicitar serviços residenciais. | Profissional prestador do serviço.                                       |
| **Profissional**              | Pessoa cadastrada que oferece e realiza serviços residenciais.                                  | Administrador da plataforma.                                             |
| **Administrador**             | Usuário responsável por analisar e moderar perfis de profissionais e ocorrências da plataforma. | Cliente ou Profissional.                                                 |
| **Solicitação de Serviço**    | Pedido inicial feito pelo cliente para realização de um serviço por um profissional.            | Serviço já confirmado ou em andamento.                                   |
| **Categoria / Especialidade** | Classificação dos tipos de serviços prestados (ex.: elétrica, hidráulica, pintura, limpeza).    | Solicitação de serviço específica de um cliente.                         |
| **Serviço**                   | Atividade residencial necessária que pode ser executada por um profissional.                    | Solicitação de serviço (pedido do cliente para realizar essa atividade). |

---

## 👤 3. Atores e Permissões

| Ator              | Quem é                                         | Pode                                                                                                                                                                                                                                                                                                         | Não pode                                                                                                                                                                                       |
| :---------------- | :--------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cliente**       | Usuário solicitante de serviços residenciais.  | Criar e editar seu próprio cadastro, pesquisar profissionais aprovados por categoria ou região, visualizar perfis públicos, solicitar serviços, informar detalhes, negociar horário e preço, aceitar ou cancelar solicitações dentro das regras, avaliar profissionais após o serviço e denunciar problemas. | Aprovar ou rejeitar profissionais, alterar dados de outros usuários, visualizar informações privadas de outros clientes, administrar denúncias ou aplicar punições.                            |
| **Profissional**  | Prestador de serviços residenciais cadastrado. | Criar e editar seu próprio perfil profissional (categorias e região de atendimento), receber solicitações, aceitar, rejeitar ou negociar solicitações, cancelar serviços dentro das regras, visualizar dados necessários para realizar o serviço e avaliar clientes.                                         | Aprovar seu próprio cadastro ou de outros profissionais, alterar dados de clientes, acessar solicitações de outros profissionais ou aplicar punições a usuários.                               |
| **Administrador** | Gestor e moderador da plataforma.              | Analisar, aprovar ou rejeitar cadastros de profissionais, analisar alterações importantes nos perfis, receber e analisar denúncias, conversar com envolvidos, emitir advertências, suspender ou bloquear usuários quando necessário e administrar regras de moderação.                                       | Utilizar a conta de outro usuário, alterar ou criar solicitações em nome de clientes ou profissionais sem autorização, nem acessar informações desnecessárias para atividades administrativas. |
| **Visitante**     | Usuário não autenticado navegando no site.     | Acessar a plataforma, pesquisar profissionais por categoria ou região e visualizar os perfis públicos de profissionais aprovados.                                                                                                                                                                            | Solicitar serviços, negociar com profissionais, avaliar usuários, fazer denúncias que exijam identificação ou acessar informações privadas.                                                    |

---

## 📝 4. Escopo Funcional (User Stories)

### US01 — Cadastro e Moderação de Profissional · `Must Have` · `M` · Status: `⚪ Draft`

**Como** profissional, **eu quero** me cadastrar informando minhas categorias e localização, **para que** o administrador possa analisar meu perfil e liberar meu acesso na plataforma.

**Critérios de aceite:**

- [ ] **Dado** que sou um profissional não cadastrado, **quando** preencho o formulário com dados válidos, uma ou mais categorias de serviço e região de atendimento, **então** meu perfil é criado com status `Pendente de Aprovação`.
- [ ] **Dado** que preencho o cadastro com informações obrigatórias faltando ou inválidas, **quando** tento enviar o cadastro, **então** o sistema informa os campos que precisam ser corrigidos e não cria o perfil como pendente.
- [ ] **Dado** que sou um Administrador, **quando** analiso o cadastro pendente do profissional e clico em "Aprovar", **então** o status muda para `Aprovado` e ele passa a ser exibido nas buscas.
- [ ] **Dado** que o Administrador rejeitou o cadastro informando um motivo, **quando** o profissional acessa seu painel, **então** ele consegue visualizar o motivo, corrigir as informações necessárias e reenviar o perfil para análise.

**Regras relacionadas:** RN01, RN02

---

### US02 — Busca e Visualização de Profissionais · `Must Have` · `S` · Status: `⚪ Draft`

**Como** cliente ou visitante, **eu quero** pesquisar profissionais por categoria de serviço e região, **para que** possa visualizar perfis públicos de prestadores aprovados.

**Critérios de aceite:**

- [ ] **Dado** que informo uma categoria de serviço e/ou cidade/região válida, **quando** realizo a busca, **então** o sistema exibe a lista de profissionais com status `Aprovado` que atendem àquela combinação.
- [ ] **Dado** que busco por uma categoria ou região onde não há prestadores disponíveis, **quando** realizo a busca, **então** o sistema exibe mensagem informando que nenhum profissional foi encontrado.
- [ ] **Dado** que clico em um profissional da lista, **quando** visualizo seu perfil público, **então** vejo suas categorias, biografia/descrição e nota de avaliação média.
- [ ] **Dado** que um profissional está com cadastro `Pendente` ou `Rejeitado`, **quando** realizo qualquer busca, **então** esse profissional nunca é exibido na lista.

**Regras relacionadas:** RN01, RN02

---

### US03 — Solicitação e Negociação de Serviço · `Must Have` · `M` · Status: `⚪ Draft`

**Como** cliente autenticado, **eu quero** enviar uma solicitação de serviço a um profissional aprovado informando a descrição da necessidade e a localização, **para que** o profissional possa aceitar, recusar ou negociar os detalhes.

**Critérios de aceite:**

- [ ] **Dado** que sou um cliente autenticado no perfil de um profissional aprovado, **quando** preencho o formulário da solicitação (descrição do problema, endereço de atendimento, data/horário preferencial e orçamento opcional que estou disposto a pagar) e envio, **então** a solicitação é criada com status `Pendente` e o profissional recebe o pedido.
- [ ] **Dado** que tento enviar a solicitação sem preencher os campos obrigatórios (descrição ou endereço), **quando** tento submeter, **então** o sistema exibe mensagens de erro e impede o envio.
- [ ] **Dado** que sou um profissional com uma solicitação `Pendente`, **quando** clico em "Aceitar", **então** a solicitação muda para `Aceita`.
- [ ] **Dado** que sou um profissional com uma solicitação `Pendente`, **quando** clico em "Recusar", **então** a solicitação muda para `Recusada` e o cliente é notificado.
- [ ] **Dado** que o profissional faz uma contraproposta alterando o valor e/ou a data e horário do serviço, **quando** envia essa contraproposta, **então** a solicitação muda para `Em Negociação`, aguardando que o cliente aceite ou cancele.

**Regras relacionadas:** RN02

---

### US04 — Pagamento e Confirmação de Serviço · `Must Have` · `M` · Status: `⚪ Draft`

**Como** cliente com uma solicitação aceita ou negociada, **eu quero** realizar o pagamento pela plataforma em ambiente de testes (sandbox), **para que** o serviço seja oficialmente confirmado.

**Critérios de aceite:**

- [ ] **Dado** que o profissional aceitou a solicitação ou o cliente aceitou uma contraproposta, **quando** o cliente realiza o pagamento em ambiente de testes (sandbox) com dados válidos, **então** a transação é aprovada e a solicitação passa para o status `Confirmado`.
- [ ] **Dado** que o pagamento no ambiente sandbox falha ou é recusado, **quando** a transação é processada, **então** o serviço permanece aguardando pagamento e o cliente poderá tentar novamente.
- [ ] **Dado** que o gateway envia uma notificação assíncrona (webhook) sobre a mudança de status da transação, **quando** o servidor recebe o evento, **então** o status da solicitação é atualizado no sistema de forma consistente.

**Regras relacionadas:** RN03

---

### US05 — Conclusão e Avaliação do Serviço · `Must Have` · `S` · Status: `⚪ Draft`

**Como** participante de um serviço confirmado, **eu quero** indicar a conclusão do atendimento e avaliar a outra parte, **para que** a reputação da plataforma seja mantida.

**Critérios de aceite:**

- [ ] **Dado** que a solicitação está no status `Confirmado`, **quando** o profissional realiza o atendimento e clica em marcar como concluído, **então** o serviço fica aguardando confirmação do cliente.
- [ ] **Dado** que o profissional marcou como concluído, **quando** o cliente confirma a conclusão do serviço, **então** a solicitação passa definitivamente para o status `Concluído`.
- [ ] **Dado** que a solicitação está no status `Concluído`, **quando** o cliente ou o profissional envia sua avaliação (nota de 1 a 5 estrelas e comentário), **então** a avaliação é registrada na plataforma e a média do perfil é atualizada.
- [ ] **Dado** que um usuário tenta avaliar um serviço que não foi concluído ou do qual não participou, **quando** tenta submeter a avaliação, **então** o sistema nega a ação.

**Regras relacionadas:** RN04, RN05

---

### US06 — Moderação de Denúncias e Ocorrências · `Should Have` · `M` · Status: `⚪ Draft`

**Como** cliente ou profissional, **eu quero** registrar uma denúncia sobre conduta inadequada ou problema em um serviço, **para que** o Administrador possa analisar a ocorrência e aplicar medidas administrativas.

**Critérios de aceite:**

- [ ] **Dado** que sou um cliente ou profissional envolvido em uma solicitação, **quando** preencho o formulário de denúncia (motivo e descrição) sobre o outro usuário, **então** a denúncia é registrada com status `Em Análise`.
- [ ] **Dado** que sou um Administrador, **quando** acesso o painel de denúncias, **então** consigo visualizar as ocorrências pendentes, os relatórios e as informações dos envolvidos.
- [ ] **Dado** que sou um Administrador analisando uma denúncia válida, **quando** aplico uma ação (emitir advertência, suspender ou bloquear o usuário), **então** o status da denúncia muda para `Resolvida` e a punição é aplicada à conta do usuário.
- [ ] **Dado** que a denúncia é improcedente, **quando** o Administrador clica em "Arquivar/Rejeitar", **então** a ocorrência é encerrada sem sanções.

**Regras relacionadas:** RN06

---

## 🛡️ 5. Regras de Negócio (Constraints)

| ID       | Regra                                                                                                                                                                                                                         |
| :------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **RN01** | **Aprovação Prévia de Profissionais:** O perfil de um profissional cadastrado só fica visível nas buscas da plataforma após aprovação explícita por um Administrador.                                                         |
| **RN02** | **Atendimento por Categoria e Região:** O profissional só deve receber solicitações de categorias de serviço que efetivamente oferece e de regiões que atende.                                                                |
| **RN03** | **Confirmação Mediante Pagamento:** O serviço só passa para o status `Confirmado` após o pagamento ser aprovado no ambiente sandbox.                                                                                          |
| **RN04** | **Confirmação de Conclusão:** O profissional marca o serviço como realizado e o cliente confirma a conclusão antes de o serviço passar para o status `Concluído`.                                                             |
| **RN05** | **Avaliação Restrita:** Somente participantes de um serviço `Concluído` podem avaliar um ao outro, com nota de 1 a 5 estrelas e comentário.                                                                                   |
| **RN06** | **Efeitos de Suspensão e Bloqueio:** Profissionais suspensos ou bloqueados não aparecem nas buscas nem recebem novas solicitações; clientes suspensos ou bloqueados não podem criar novas solicitações nem aceitar propostas. |

---

## 🚫 6. Fora de Escopo (Non-goals)

- **Chat em tempo real com envio de mídias/arquivos:** A negociação será feita por campos de texto simples na solicitação e contraproposta.
- **Aplicativo móvel nativo (iOS/Android):** A plataforma será exclusivamente Web Responsiva.
- **Repasse/split bancário real de valores para o profissional:** O pagamento será processado apenas em ambiente de testes (sandbox) sem integração bancária real de saída.
- **Agendamento automático com integração a calendários externos (Google Agenda / iCal):** Os horários serão definidos manualmente em texto entre cliente e profissional.

---

## ⚙️ 7. Requisitos Não Funcionais (Qualidade)

- **RNF01 — Segurança e Autenticação:** O sistema deve possuir autenticação segura e controle de acesso baseado nos papéis de Cliente, Profissional e Administrador.
- **RNF02 — Proteção de Dados Sensíveis:** Credenciais, chaves e outros dados sensíveis não devem ser versionados no repositório.
- **RNF03 — Responsividade:** A interface web deve funcionar adequadamente em telas desktop e dispositivos móveis.
- **RNF04 — Documentação da API:** A API deve possuir documentação interativa e atualizada para facilitar seu uso e testes.

---

## 🛠️ 8. Histórico

| Data       | Versão | O que mudou                                                           |
| :--------- | :----- | :-------------------------------------------------------------------- |
| 2026-09-17 | 1.0.0  | Versão inicial gerada via entrevista `/utf-prd` e aprovada pelo aluno |
