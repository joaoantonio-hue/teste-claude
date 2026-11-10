# Resultado — Rodada 4 (itens que só tinham sido olhados ou não testados)

Data: 09/11/2026 · Ambiente: sandbox · Execução automatizada (Playwright).

## Falhas novas

| # | Caso | O que aconteceu | Esperado |
|---|---|---|---|
| F11 | T6 | **Exportar tarefas selecionadas exporta a lista inteira**: com 1 e com 2 tarefas marcadas, o arquivo veio com as 6 tarefas da lista. Em Contatos a exportação por seleção funciona (3 marcados, 3 linhas) | Só as linhas selecionadas (guia 6) |
| F12 | T6 | **Horário errado no CSV de tarefas**: a tarefa das 10:00 sai como 07:00 no arquivo (3 horas a menos). Na tela o horário está certo | Mesmo horário da tela |
| F13 | T6 | **Gestor não consegue transferir tarefas** de uma vendedora: "2 tarefa(s) ficaram de fora (de outra carteira…)". O Administrador transfere normalmente | Transferir é de gestor e administrador (guia 6) |

## Divergências novas da especificação

- **N17:** a tela de Negociações não tem os botões "Criar funil" e "Configurar funil". Eles existem só em Configurações.
- **L7:** o relatório não tem indicador de tempo de primeiro contato nem de leads fora do SLA. Depende da entrada automática de leads, que ainda não existe.

## Casos OK

| ID | Caso | Evidência |
|---|---|---|
| T1 | Campos obrigatórios da tarefa | Sem assunto: "Informe o assunto da tarefa". Sem data/hora: "Informe a data e o horário" |
| T2 | Registrar tarefa já realizada | Com a caixa marcada, o botão vira "Registrar" e a tarefa entra como concluída |
| T3 | Filtros de período de Tarefas | Com 6 tarefas em datas conhecidas (hoje = seg 09/11), Hoje, Esta semana, Este mês, 7/14/30 dias, 6 meses e Personalizado (dezembro) trouxeram exatamente as esperadas, com contadores coerentes |
| T6 | Barra de massa do gestor | Concluir, Adiar, Transferir e Exportar aparecem (mas veja F11–F13) |
| K2 | Filtros de Contatos | Aluno atual 1.563 + Ex-aluno 2.829 + Novo 14.731 = 19.123 (total). Com negociação aberta 5.502 + sem 13.621 = 19.123. Curso de interesse e data de criação filtram certo |
| K7b | Exportar contatos por filtro e por seleção | Filtro "criados hoje": 1 linha. Seleção de 3: 3 linhas |
| K13 | Situação de aluno | Integração funcionando: perfil mostra "Aluno atual" ou "Ex-aluno" e os produtos com data e status (ex.: "CACD Basic Anual (legacy) · 16/09/2025 · Ativo") |
| D10 | Bloco do contato na negociação | Nome, telefones, e-mails, dona, escolaridade, formação, curso de interesse, "Aluno Clipping" e outras negociações do contato |
| B3 | Limites da busca global | Busca "Silva": 5 contatos e 5 negociações. "Ver todos os contatos" abre a lista filtrada (1.059) |
| X4 | Gestor por URL direta | Usuários: acesso restrito. Regras, Funis, Produtos e Motivos: somente leitura. Lixeira: vê, mas sem o botão Restaurar |
| S3 | Desativar motivo de perda | O motivo some da janela de perda, mas continua na negociação perdida e no relatório (R$ 11.295) |
| L5 | Números do relatório contra as listas | Etapas do Extensivo no relatório (51 / 257 / 6 / 11 / 7 / 3) iguais ao Kanban. Conversão do mês 0/1 coerente. "Atividades por vendedor" contou a tarefa registrada como feita |
| L6 | Painel da vendedora | Vendedor abre "Painel individual" já filtrado em si mesmo |

## Observação

**Relatórios → "Tarefas por status" (Este mês):** "Criadas" conta pela data de criação, enquanto "Atrasadas" conta pela data de vencimento. Uma tarefa criada hoje e vencida em outubro entra em Criadas, mas não em Atrasadas. Vale definir um critério só.

## A2 (resumo da manhã) — conferido em 10/11/2026

**Resultado: OK.**

A carteira do Teste CRM Vendedor foi montada em 09/11 com tarefas e negociações em datas conhecidas.

| Notificação de 10/11 (de manhã) | Situação real | Link abre |
|---|---|---|
| "1 tarefa para hoje e 3 tarefas atrasadas" | **Correto.** Hoje: "amanha" (10/11, 10:00). Atrasadas: "atrasada 20d", "atrasada 3d" e "hoje" (09/11, 23:00, que venceu de madrugada). A previsão anotada na rodada 4 dizia 2 atrasadas porque esqueci esta última | `/crm/tarefas?status=overdue`, com as tarefas certas |
| "1 negociação sem próxima tarefa" | **Correto:** "TESTE-QA R4 Sem tarefa" | `/crm/negociacoes?vista=lista&alerta=sem-tarefa`, só com ela |

O resumo do dia anterior (09/11, à tarde) também batia: 1 tarefa para hoje e 2 atrasadas.

**Observação:** o link do resumo de tarefas abre só o filtro "Atrasadas". A tarefa de hoje só aparece nele porque, na hora em que a lista foi aberta (10:31), ela também já tinha vencido. Vale o link abrir "hoje + atrasadas".

## Limpeza

- Negociações "TESTE-QA R4" (3), as tarefas delas e o contato "TESTE-QA R4": excluídos.
- Motivo "TESTE-QA motivo": desativado. O sistema não permite apagar.
