# Resultado — Rodada 2 (todos os blocos restantes, exceto WhatsApp com celular)

Data: 30/10/2026 · Ambiente: sandbox · Execução automatizada (Playwright) com os usuários de teste Administrador, Gestor e Vendedor.
Referências de caso: `plano-de-testes-crm.md`. Rodada anterior: `resultado-rodada-1.md`.

## Resumo

| Resultado | Qtde |
|---|---|
| OK | 52 |
| FALHA | 10 |
| Divergência da especificação (não está no guia como construído) | 7 |
| Não testado / pendente | ver lista no fim |

## Falhas (ordem de gravidade)

| # | Caso | O que aconteceu | Esperado (fonte) |
|---|---|---|---|
| F1 | P8b (rodada 1) | Perfil Marketing grava alterações em negociação e cria contato | Somente leitura (guia, cap. 2) |
| F2 | D11 | **Trocar de produto não funciona em nenhum funil real**: a janela exige um produto do funil de destino, e nenhum produto está vinculado a funil (Configurações → Produtos: todos em "Todos os funis"). Com um produto vinculado a um funil de teste, funcionou | Trocar de produto (guia 5.1) |
| F3 | D12 | Negociação excluída **não pode ser recuperada**: não existe lixeira de negociações ("Negociação não encontrada") | Lixeira com recuperação pelo administrador (especificação, requisitos não funcionais) |
| F4 | D1 | **Perda pode ser marcada sem produto e valor** (o ganho bloqueia corretamente) | Ganho e perda exigem produto e valor (especificação) |
| F5 | S4 | Vendedor **não consegue aplicar etiqueta** na própria negociação (a janela mostra só "Nenhuma etiqueta"; o administrador vê o campo "Adicionar etiqueta"). No perfil do contato não há opção de etiquetas para nenhum perfil | Vendedor usa etiquetas existentes; etiquetas no contato (guia 2 e 7.3) |
| F6 | S4 | **Não existe opção de apagar etiqueta**, só editar | "Criar, editar e apagar" (guia 11) |
| F7 | D9 | Data e hora de criação **não é editável nem pelo administrador** | Editável por gestor/administrador (especificação) |
| F8 | K12 | Não existe exclusão definitiva (LGPD). A Lixeira só tem "Restaurar" | Excluir dados a pedido do contato (especificação) |
| F9 | T3 | Tarefas **sem filtro por tipo** (WhatsApp, e-mail, ligação…) | Filtro por tipo (guia 6 e especificação) |
| F10 | P2 (rodada 1) | Negociações abre em "Todos os responsáveis" para a vendedora | Padrão = própria carteira |

## Divergências da especificação (o guia não promete, mas a especificação pede)

1. **N10 — filtros do funil:** só existem funil, responsável (um por vez), status e busca. Faltam etapa, produto, campanha, faixa de valor, data de criação, data do último contato e etiquetas.
2. **N11 — ordenação:** não há nenhuma opção de ordenar (nem por cabeçalho de coluna).
3. **N6 — card configurável pelo usuário:** não existe.
4. **N9 — ganho/perda arrastando o card:** o Kanban não tem colunas de Ganho/Perda.
5. **P6 — convite por e-mail:** "Adicionar membro" exige que a pessoa já tenha conta na plataforma.
6. **L3 — metas por produto:** há metas por mês, semana, time, vendedor e funil (e atividades/dia), mas não por produto.
7. **C8 (rodada 1) — painel lateral da tarefa:** sem telefone, valor e atalho para o WhatsApp.

## Casos OK

**Permissões:** P1 vendedora pode ver todas as carteiras · P7 gestor sem Usuários e Funis só leitura (rodada 1).

**Negociações:**
- N1: etapas nos 3 funis principais.
- N3: valor padrão = valor do produto.
- N4: soma e contagem das colunas batem com o total (333 / R$ 3.659.466,60 no Extensivo).
- N5: card mostra "Sem tarefa agendada" e "N dias parado".
- N8: etapa sincronizada entre painel, detalhe e Kanban.
- N12: colunas escolhidas ficam no endereço e sobrevivem ao recarregar.
- N13: mudança de etapa em massa move só o que a vendedora pode mover, com aviso "2 ficaram de fora…".
- N14: trocar filtro limpa a seleção.
- N15: exportação CSV com BOM e ponto e vírgula (abre certo no Excel), 333 linhas = lista.
- N16: playbook em texto abre pelo ícone do funil.

**Detalhe, ganho e perda:**
- D1: ganho sem produto bloqueado ("Preencha produto e valor").
- D2: janela de ganho com produtos, valor e comprovante; arquivo de 11 MB recusado ("Arquivo maior que 10 MB").
- D4: vendedor que não é dono vê só "Comprovante anexado", sem link.
- D5: ganho sem comprovante → "Anexar comprovante" depois → "Ver comprovante / Substituir"; histórico registra.
- D6: perda exige motivo ("Escolha o motivo da perda").
- D7: reabrir volta para a etapa em que estava (interesse) e registra no histórico.
- D8: adicionar e remover produto recalcula o total.
- D11: com produto vinculado ao funil, a antiga fica perdida com "Migrou para outro produto" e a nova é criada; a conversão no relatório exclui "Migrou".
- D13: histórico completo com autor e hora.
- D14: @menção chega no sino do gestor.

**Tarefas:**
- T4: colunas.
- T5: concluir pelo status.
- T6: vendedor só tem Concluir e Adiar em massa; adiar funciona.
- T7: tarefa atrasada em vermelho no card do Kanban.
- T8: reabrir tarefa concluída.
- Contadores Todas/Pendentes/Atrasadas/Em dia/Concluídas coerentes.

**Contatos:**
- K1: busca enquanto digita.
- K2: filtros de situação de aluno, curso, etiquetas, data de criação e negociação aberta.
- K3: colunas.
- K5: vários telefones e e-mails.
- K6: perfil com negociações.
- K8: transferir carteira leva a negociação aberta e a tarefa pendente (a negociação ganha fica com a vendedora original) e registra no histórico.
- K9: unir contatos e "Desfazer união".
- K10: restaurar da Lixeira.
- K11: a prévia da resolução em massa mostra 3.319 a resolver e 34 para revisão manual. **Não foi aplicada.**

**Rodízio e cobertura:**
- R4: vendedora ausente é pulada (Julia, Cassiano, Renata, Julia).
- R5: gestor moveu negociação da ausente → histórico "em cobertura da carteira de Teste CRM Vendedor"; ao fim da ausência voltou a ser somente leitura.
- R6: contato da ausente continua dela.

**Notificações:**
- A1: sino com contador.
- A2: "sem próxima tarefa" e "tarefa atrasada" abrem as listas filtradas (`alerta=sem-tarefa`, `status=overdue`).
- A3: gestor recebe "duplicados para revisar" e "Resumo do dia".
- "Marcar todas como lidas" funciona.

**Relatórios:**
- L1: período "Hoje" mostrou exatamente as 2 vendas de teste (R$ 22.589), com receita por produto, ticket médio, conversão e tempo de fechamento coerentes.
- L2: meta de R$ 50.000 → "23% atingido, faltam R$ 38.705" (conta correta).
- L4: previsão = valor × chance (R$ 530.849 × 50% = R$ 265.424).

**Configurações:**
- S1: criar funil com playbook e produto vinculado.
- S2: desativar produto.
- S7: coluna "Última alteração" com usuário e data.

**Busca global:**
- B1: Ctrl+K e Enter abrem o resultado.
- B2: telefone completo, parcial, e-mail e nome.
- B4: "Nada encontrado".

## Pontos de atenção (não são falhas de teste, mas afetam o uso)

- **Configuração incompleta no sandbox:**
  - Chance de fechamento vazia em todas as etapas, então a previsão aparece "—".
  - Dias de alerta (amarelo/vermelho), SLA e horário comercial vazios em Regras.
  - Nenhum produto vinculado a funil (causa da F2 e da lista de produtos sem filtro).
  - Nenhuma etiqueta e nenhum pitch cadastrados.
- **Desempenho:** Negociações, Lista e Contatos levam cerca de 6,5 s para carregar por completo; Relatórios, cerca de 4,3 s.
- **Grafia das etapas varia entre funis** ("descoberta" × "Descoberta", "aguardando pagamento").
- **Dados importados do RD:** em Duplicados há telefones colados ("21681827620+5565981222983") e inválidos ("(00) 92447-9860").
- **Ruído no sandbox:** "Negociações que precisam de atenção" mostra 9.811 sem interação e 9.613 sem tarefa (esperado no sandbox, segundo o guia).

## Não testado nesta rodada

- **WhatsApp com celular:** C2, R2, W1–W8, W10–W25. Precisam de alguém mandando mensagens pelo número do Sandbox Twilio.
- **S6 (mudar dias de alerta / cores do Kanban):** não alterei, porque mudaria a regra para todo mundo no sandbox.
- **S5 (pitches e respostas pessoais nas Conversas):** depende das Conversas.
- **A4/A5 (configurar prazo e silenciar):** depende de S6 e das Conversas.
- **K13 (situação de aluno):** depende da integração com a base de alunos.
- **X3 (exportação acima de 50 mil linhas):** não testado.
- **P8b com usuário Marketing sem carteira:** não existe esse usuário de teste.

## Dados criados e limpeza

- **Contatos, negociações e tarefas "TESTE-QA":** excluídos.
- **Funil "TESTE-QA Funil":** arquivado.
- **Produto "TESTE-QA Produto":** desativado.
- **Meta de teste:** removida. O formulário de metas gravou "50 atividades/dia" para as 4 vendedoras em outubro, que parece ser o padrão do formulário.
- **Etiqueta "TESTE-QA tag":** ficou no sistema, porque não existe opção de apagar (F6).
- **Ausência da Teste CRM Vendedor:** desmarcada.
- **Erro meu, já corrigido:** a chance de fechamento da etapa "Sem contato" do **Funil Extensivo real** foi alterada por engano para 50% por cerca de 2 minutos e voltou para vazio. A alteração aparece no registro como "Teste CRM Administrador".
