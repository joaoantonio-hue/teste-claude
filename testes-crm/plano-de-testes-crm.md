# Plano de testes — CRM Comercial e Conversas (Clipping)

Ambiente: sandbox `https://clipping-26-sandbox.tailbc897a.ts.net/crm`
Fontes:
- **[ESP-CRM]** Especificação do CRM Comercial — Letícia, 30/09/2026
- **[ESP-WPP]** Especificação do Conversas (WhatsApp) — Letícia, 01/10/2026
- **[GUIA]** CRM_GUIA_DE_TESTES, versão de 28/10/2026 (o que o time diz que já foi construído)

Critério: cada caso nasce de um requisito das especificações. O resultado esperado é o da
especificação; quando o guia diz que algo ainda não existe (cap. 14), o caso fica marcado
**[FORA — não construído]** e não conta como defeito, só como pendência de escopo.

Legenda de perfil: V = vendedor, G = gestor, A = administrador, M = marketing.
Convenção de dados: tudo que for criado leva "TESTE-QA" no nome, para limpar no fim.

---

## 0. Pré-requisitos

| Item | Necessário para |
|---|---|
| 1 usuário de teste com perfil alternável (A → G → V → M) e um segundo usuário V | Permissões, carteira, cobertura, @menção |
| Celular com o número aderido ao Sandbox do Twilio (código "join …" com o João) | Bloco 9 (Conversas) |
| Planilha CSV pequena (10 linhas: 5 novos, 3 já existentes, 2 com erro) | Importação |
| Um PDF e uma imagem < 10 MB e um arquivo > 10 MB (e > 16 MB para WhatsApp) | Comprovante e anexos |

---

## 1. Os 5 pontos críticos do resumo executivo (prioridade máxima)

| # | Requisito [ESP-CRM] | Passos | Resultado esperado |
|---|---|---|---|
| C1 | Carteira fixa: negociação nova de contato com dona vai para a dona, nunca para o rodízio | Escolher contato com dona = Renata. Logado como outra pessoa, Criar negociação para ele (em outro funil) | Responsável = Renata. Rodízio não avança |
| C2 | Idem, entrando pelo WhatsApp | Do celular de teste (contato com dona), mandar mensagem | Conversa atribuída à dona; nenhum contato novo criado |
| C3 | Deduplicação por telefone: mesmo telefone + e-mail novo → mesmo contato, guarda os 2 e-mails, mantém a dona | Criar contato com telefone de um existente e e-mail diferente | Sistema avisa e mostra o existente; ao unir, perfil fica com 2 e-mails |
| C4 | Caso inverso: mesmo e-mail, telefone diferente | Idem com e-mail igual | Mesmo comportamento |
| C5 | Normalização de telefone: espaços, traços, parênteses, +55, nono dígito | Tentar criar o mesmo telefone como `(61) 99999-0000`, `+55 61 99999 0000`, `6199990000` (sem o 9) | Todos reconhecidos como duplicado |
| C6 | Normalização de e-mail: maiúsculas/espaços | `  Fulano@Gmail.COM ` contra `fulano@gmail.com` | Reconhecido como duplicado |
| C7 | Conflito com donas diferentes não une sozinho, vai para o gestor | Importar CSV com linha cujo telefone é do contato da Renata e e-mail é de um contato do Cassiano | Linha vai para Duplicados; nada é unido automaticamente |
| C8 | Tarefas sem troca de tela (referência Pipedrive) | Em Tarefas, clicar numa tarefa | Painel lateral abre; URL/tela de lista continua; dá para concluir, anotar, criar próxima tarefa, mudar etapa, ver histórico resumido, abrir WhatsApp e abrir negociação completa |
| C9 | WhatsApp com atalhos para tarefa e etapa sem sair da conversa | Na conversa, painel do contato | Criar tarefa, anotação e mudar etapa sem sair da tela de Conversas |

## 2. Perfis e permissões

| # | Requisito | Perfil | Esperado |
|---|---|---|---|
| P1 | Vendedor vê todos os contatos/negociações | V | Seletor de carteira permite "todas" |
| P2 | Padrão ao abrir = carteira da própria vendedora [ESP-CRM] | V | Contatos (e Negociações) abrem filtrados na própria carteira |
| P3 | Vendedor edita só a própria carteira | V | Mover/editar negociação de outra: bloqueado. Própria: funciona |
| P4 | Transferir contato/negociação: só G/A | V | Botão Transferir não aparece/é negado (testar também pela lista em massa) |
| P5 | Cadastrar metas: só G/A | V | Sem acesso a Metas |
| P6 | Convidar usuário por e-mail, escolher perfil, pessoa cria a senha [ESP-CRM] | A | Convite chega por e-mail e funciona (o guia só fala em "adicionar" — anotar se for diferente) |
| P7 | Gestor: Usuários oculto, Funis só leitura [GUIA] | G | Confirmar |
| P8 | Marketing: só leitura, sem Conversas [GUIA] | M | Sem menu Conversas; sem botões de alteração; tentar abrir `/crm/conversas` direto pela URL → negado |
| P9 | Histórico não pode ser apagado pela vendedora [ESP-CRM] | V | Nenhuma opção de apagar item de histórico, anotação ou tarefa registrada |
| P10 | Bloqueio também no servidor | V | Repetir P3 abrindo duas abas (uma ainda com botão visível) ou pelo arraste no Kanban: ação negada com mensagem, sem alterar dados |

## 3. Negociações — funil (Tela 1)

| # | Requisito [ESP-CRM] | Esperado |
|---|---|---|
| N1 | 3 funis (Extensivo, Intensivo, PEA); etapas Sem contato → Contatado → Descoberta → Interesse → Negociação → Aguardando pagamento → Ganho/Perda | Conferir nomes e ordem nos três funis |
| N2 | Negociação nova entra em "Sem contato" | Criar negociação manual e conferir a etapa |
| N3 | Valor padrão = pacote completo do produto ("melhor jogar para cima") | Criar com produto Extensivo → valor = valor cheio cadastrado |
| N4 | Topo da coluna: quantidade e soma | Conferir com a Lista filtrada na mesma etapa |
| N5 | Card: nome, valor, dias parado, próxima tarefa; "sem tarefa" visível | Card sem tarefa mostra claramente que não há tarefa |
| N6 | Usuário escolhe quais informações aparecem no card | Procurar a opção. Se não existir → divergência da especificação |
| N7 | Cor de dias parado a partir de 2 dias (configurável) | Alterar em Regras e conferir cores amarelo/vermelho |
| N8 | Arrastar card e seletor de etapa sincronizados | Arrastar no Kanban → abrir detalhe (etapa certa); mudar no detalhe → voltar ao Kanban |
| N9 | Arrastar para Ganho/Perda abre a mesma janela de confirmação | Verificar se o Kanban tem colunas/alvos de Ganho/Perda; se não tiver, registrar divergência |
| N10 | Filtros: responsável, status, **etapa, produto, campanha, nome, faixa de valor, data de criação, data do último contato, tags** | Testar cada um; o guia só cita responsável, status e busca → anotar os que faltam |
| N11 | Ordenação: alfabética, criadas por último/primeiro, data da próxima tarefa, interação mais recente/antiga | Testar cada uma; anotar as que faltam |
| N12 | Lista: colunas escolhidas pelo usuário, persistem no endereço [GUIA] | Ligar Campanha, recarregar, Voltar, abrir link em outra aba |
| N13 | Ações em massa respeitam permissão | Selecionar mistas (próprias + de outra vendedora + encerradas) e mudar etapa → aviso com a contagem correta das excluídas |
| N14 | Mudar filtro limpa seleção [GUIA] | Selecionar, trocar filtro, barra some |
| N15 | Exportar: CSV abre no Excel com acentos; Kanban = em andamento do funil; Lista = o que a lista mostra; seleção = só as linhas | Conferir número de linhas contra a tela |
| N16 | Playbook do funil (texto e link) | Texto abre janela; link abre nova aba; ícone some quando não há playbook |
| N17 | Criar funil / Configurar funil a partir da tela (A) | Botões existem para A e não para V/G |

## 4. Detalhe da negociação (Tela 2) e Ganho/Perda

| # | Requisito | Esperado |
|---|---|---|
| D1 | Ganho e perda exigem produto e valor | Negociação sem produto: Marcar ganho/perda bloqueado com mensagem. (Testar também a **perda** — o guia só cita o ganho) |
| D2 | Janela de ganho confirma produtos e valor; comprovante opcional (imagem/PDF ≤ 10 MB) | Anexar PDF ok; arquivo de 11 MB recusado; formato .exe recusado |
| D3 | Ganho entra nos relatórios (receita, conversão, metas) | Conferir no Relatório Comercial do dia |
| D4 | Comprovante só visível para dona, G e A | Outro vendedor vê só "Comprovante anexado"; tentar abrir a URL do arquivo copiada → negado |
| D5 | Anexar depois / Substituir — tudo no histórico | Conferir as duas entradas no histórico |
| D6 | Perda: motivo obrigatório da lista, anotação opcional, Confirmar/Cancelar | Sem motivo não confirma; Cancelar não altera nada |
| D7 | Reabrir volta **exatamente para a etapa em que estava** e registra no histórico | Perder em "Interesse", reabrir → volta para "Interesse" |
| D8 | Produtos: adicionar/excluir recalcula o total | Somar 2 produtos, remover 1 |
| D9 | Bloco Negociação editável: nome, funil, campanha, data de criação (só G/A), responsável, valor, tags | Vendedor não consegue editar data de criação; G/A conseguem |
| D10 | Contato no bloco: telefones, e-mails, dona, escolaridade, formação, curso de interesse, situação de aluno, produtos que tem/teve | Conferir campos presentes |
| D11 | Trocar de produto: antiga perdida com "Migrou para outro produto", nova no outro funil; **esse motivo não entra na taxa de perda** [ESP-WPP] | Conferir as duas negociações e depois o gráfico/taxa de perda no relatório |
| D12 | Excluir negociação com confirmação; vai para lixeira recuperável pelo A [ESP-CRM, requisitos não funcionais] | Excluir (A) → procurar como restaurar. O guia só cita Lixeira de contatos → anotar se negociação não for recuperável |
| D13 | Histórico: criação e origem, etapa, funil, valor, produto, tarefas, anotações, transferências, coberturas, ganho/perda com motivo, com quem e quando, do mais recente para o mais antigo | Executar várias ações e conferir cada entrada e a ordem |
| D14 | Anotação com @menção gera notificação | Mencionar o 2º usuário; chega no sino dele na hora |
| D15 | E-mail dentro do CRM | **[FORA — não construído]** botão deve aparecer desabilitado |

## 5. Tarefas (Tela 3)

| # | Requisito | Esperado |
|---|---|---|
| T1 | Campos obrigatórios: negociação ou contato, assunto, responsável (padrão = quem cria), tipo, data, horário; descrição opcional | Tentar salvar sem cada campo obrigatório |
| T2 | Registrar tarefa já realizada ("chamei agora no WhatsApp") entra concluída no histórico | Procurar a opção; se não existir → divergência |
| T3 | Filtros: responsável, status, tipo, período (hoje, semana, mês, 7/14/30 dias, 6 meses, personalizado) | Contadores batem com a lista |
| T4 | Colunas: status clicável, assunto, contato, negociação, data e hora com ano, responsável, valor | Conferir |
| T5 | Painel lateral (ver C8) | Fazer o fluxo completo: concluir → anotar → criar próxima → mudar etapa → passar para a próxima tarefa, sem sair da lista |
| T6 | Massa: concluir, adiar, transferir, exportar (respeitando permissão) | Vendedor: transferir/exportar indisponíveis; tarefas de outra pessoa ficam de fora com aviso |
| T7 | Tarefa atrasada aparece em vermelho no card do Kanban | Criar tarefa com data de ontem |
| T8 | Reabrir tarefa concluída [GUIA] | Funciona e fica no histórico |

## 6. Contatos (Tela 4)

| # | Requisito | Esperado |
|---|---|---|
| K1 | Busca enquanto digita por nome, telefone, e-mail | Resultado atualiza a cada tecla |
| K2 | Filtros: carteira, situação de aluno, produto/curso de interesse, tags, data de criação, tem negociação aberta | Testar cada um |
| K3 | Colunas: nome, telefone, e-mail, dona, negociações (abertas/total), última interação, situação de aluno | Conferir |
| K4 | Obrigatórios: nome + (telefone ou e-mail); telefone em formato brasileiro com DDD | Salvar sem os dois → bloqueado; telefone sem DDD → bloqueado |
| K5 | Vários telefones e e-mails, um marcado como principal | Adicionar segundo, trocar o principal |
| K6 | Perfil: todas as negociações (todos os funis, abertas e fechadas) e histórico consolidado | Conferir num contato com negociações em 2 funis |
| K7 | Importação CSV com prévia (novos / existentes / com problema) antes de gravar; aplica a mesma deduplicação | Usar a planilha do pré-requisito; números da prévia batem com o resultado |
| K8 | Transferir carteira leva negociações abertas, tarefas pendentes e conversas em andamento; registra no histórico | Conferir os três itens e o histórico após transferir |
| K9 | Mesclar e desfazer mesclagem (A) | Após desfazer, os dois contatos voltam com os dados originais |
| K10 | Excluir → Lixeira → restaurar (A) | Restaurado com negociações e histórico |
| K11 | Duplicados: definir dona, mesclar, anexar, descartar; resolução em massa só com prévia | **Não aplicar a resolução em massa** no sandbox (3.300 conflitos, dados compartilhados) — só abrir a prévia |
| K12 | LGPD: excluir os dados de um contato a pedido dele | Verificar se existe exclusão definitiva (além da Lixeira) |
| K13 | Situação de aluno e produtos vindos da base de alunos | Conferir num aluno conhecido (pode ser fase 2 — registrar o estado) |

## 7. Rodízio e cobertura

| # | Requisito | Esperado |
|---|---|---|
| R1 | Contato novo sem dona entra no rodízio, um para cada, em sequência | Criar 4 contatos seguidos (como V): donas alternam |
| R2 | Mesmo rodízio para importação e WhatsApp | Importar 3 novos; mandar mensagem de número novo → contato criado no CRM e atribuído pelo rodízio |
| R3 | G/A escolhem a dona ao criar | Campo aparece só para G/A |
| R4 | Ausência: rodízio pula a ausente | Marcar ausência e repetir R1 |
| R5 | Cobertura: outro vendedor trabalha a carteira da ausente, histórico "cobrindo Fulana"; ao fim da ausência volta a ser só dela | Mover negociação durante e depois do período |
| R6 | Contato existente da ausente continua sendo dela (não muda de dona) | Criar negociação para contato da ausente → responsável continua sendo ela |

## 8. Notificações e alertas

| # | Requisito | Esperado |
|---|---|---|
| A1 | Sino sempre visível, contador de não lidas | Em todas as telas |
| A2 | Vendedora: paradas ≥ 2 dias, sem próxima tarefa, tarefas de hoje e atrasadas — link abre lista filtrada que bate com o número | Abrir cada uma e comparar contagem |
| A3 | Gestor: resumo diário por vendedora; conflitos de duplicação | Conferir no fim do dia / de manhã |
| A4 | Prazo de 2 dias configurável pelo A | Alterar em Regras e ver efeito no alerta |
| A5 | Notificação no computador com som, e silenciar | Testar com o silenciar ligado e desligado |
| A6 | Lead novo na carteira e alertas de SLA | **[FORA — não construído]** (depende da entrada automática de leads) |

## 9. Conversas (WhatsApp) [ESP-WPP]

Prioridade da especificação: **nenhuma mensagem pode se perder**.

| # | Requisito | Passos | Esperado |
|---|---|---|---|
| W1 | Toda mensagem recebida aparece na conversa certa em tempo real | Celular manda texto | Aparece sem recarregar, com notificação |
| W2 | Rajada / simultâneas / fora de ordem | Mandar 20 mensagens seguidas rápido; depois 2 celulares ao mesmo tempo | Todas as 20 aparecem, na ordem, sem duplicar; conversas não se misturam |
| W3 | Mídia recebida: imagem, áudio, vídeo, PDF | Enviar cada uma do celular | Todas abrem no CRM |
| W4 | Envio: texto, emoji, anexo (≤ 16 MB), resposta pronta | Conferir chegada no celular; arquivo > 16 MB recusado com aviso |
| W5 | Status da mensagem: enviada, entregue, lida, **falhou** (com reenviar) | Mandar para número que não aderiu ao Twilio | Fica marcada como falha, com opção de reenviar |
| W6 | Nome de quem enviou cada mensagem do lado Clipping | Dois usuários respondem a mesma conversa | Cada mensagem mostra o autor |
| W7 | Janela de 24 h: fechada → só template aprovado; indicador visível | Usar conversa sem mensagem do cliente há > 24 h | Caixa de texto trocada por seleção de modelo |
| W8 | Iniciar conversa: exige contato cadastrado e template | Nova conversa com número não cadastrado | Pede o cadastro antes de enviar |
| W9 | Roteamento pela carteira | Ver C2 e R2 | Conversa vai para a dona; contato novo criado e rodiziado |
| W10 | Responder conversa de outra pessoa pede confirmação de cobertura; não atribuída é assumida [GUIA] | Testar os dois | Confirmação aparece; histórico registra |
| W11 | Transferir: só gestor; registrado no histórico | Como V, botão indisponível; como G, transferir com e sem carteira |
| W12 | Encerrar atendimento oferece agendar próxima tarefa | Encerrar conversa de contato com negociação aberta | Oferta aparece; tarefa criada vai para Tarefas |
| W13 | Status da conversa: Aberta / Em andamento / Encerrada | Acompanhar uma conversa nos três estados e nos filtros | |
| W14 | Atalhos: Não lidas, Aguardando minha resposta, Aguardando o cliente, Sem resposta há X horas, Urgente (SLA de 10 min) | Deixar uma conversa 11 min sem resposta (em horário comercial) | Entra em Urgente |
| W15 | Busca por nome, telefone **ou texto da mensagem** | Buscar uma palavra que só aparece numa mensagem | |
| W16 | Filtros: setor, carteira, canal, funil, etiqueta | Testar cada um | |
| W17 | Nota interna: destacada, não vai ao cliente, entra no histórico do contato, @menção notifica | Conferir no celular que nada chegou | |
| W18 | Painel do contato: alterar aqui altera no CRM | Editar e-mail no painel e abrir o contato no CRM | |
| W19 | Ganho e Perda pela conversa, com as mesmas regras (produto e valor; motivo obrigatório) | Marcar perda sem motivo → bloqueado | |
| W20 | Trocar produto e valor e criar negociação pelo painel, sem ir ao CRM | | |
| W21 | Mensagem enviada entra no histórico mas **não conta como tarefa concluída** | Enviar mensagem e conferir que nenhuma tarefa foi concluída | |
| W22 | Conversa conta como atividade para o alerta de negociação parada | Negociação parada há 3 dias: responder pelo WhatsApp → "dias parado" zera | |
| W23 | Botão "Abrir conversa" no card e no detalhe da negociação | | |
| W24 | Quem mais está vendo/digitando [GUIA] | Dois usuários na mesma conversa | |
| W25 | Descadastro: SAIR, PARAR, STOP, CANCELAR, DESCADASTRAR bloqueiam; VOLTAR/START reativam; "quero sair do grupo" não conta | Testar cada palavra (minúsculas e com espaço também) | Confirmação ao cliente; faixa na conversa; só notas internas |
| W26 | Áudio: gravar e transcrição; mensagens agendadas; pitch automático; disparos em massa; chat do site; fora do horário; painel de dados | — | **[FORA — não construído]** registrar apenas |

## 10. Relatórios e metas

| # | Requisito | Esperado |
|---|---|---|
| L1 | Filtros de período, produto, funil e vendedora | Números mudam de forma coerente |
| L2 | Realizado × meta com percentual e quanto falta; meta do time e individual | Cadastrar meta e conferir a conta |
| L3 | Metas por semana e por produto, além da mensal; metas de atividade (ex.: 50 contatos/dia) | Verificar se é possível cadastrar; anotar o que falta |
| L4 | Previsão = valor por etapa × probabilidade configurável | Conferir a conta numa etapa com valores conhecidos |
| L5 | Receita por produto, mais vendidos, ticket médio, conversão (geral, produto, vendedora, entre etapas), tempo médio de fechamento, motivos de perda com valor perdido | Conferir 2 ou 3 números contra a Lista de Negociações |
| L6 | Painel individual: vendedora vê os próprios números | Abrir como V |
| L7 | Tempo de primeiro contato / leads fora do SLA (30 min, relógio pausa fora do horário) | Provavelmente depende da entrada automática de leads — registrar o estado |

## 11. Configurações

| # | Requisito | Esperado |
|---|---|---|
| S1 | Funis: criar, renomear, arquivar, reordenar; etapas e probabilidade | Funil "TESTE-QA" (arquivar ao final) |
| S2 | Produtos: nome, forma de pagamento, valor, funil; desativar preserva histórico e relatórios | Desativar produto usado numa negociação ganha → relatório continua mostrando |
| S3 | Motivos de perda: desativar em vez de apagar | Idem |
| S4 | Etiquetas com cor (G/A); vendedor só usa existentes | |
| S5 | Pitches oficiais (um por produto) e respostas pessoais por vendedora | Resposta pessoal de uma vendedora não aparece para outra |
| S6 | Regras: rodízio, dias de alerta, SLA, horário comercial | |
| S7 | Toda alteração registrada (quem e quando) | Conferir o registro após S1–S6 |
| S8 | Campos personalizados, modelos de e-mail, cadências, automações | **[FORA — não construído]** |

## 12. Busca global

| # | Esperado |
|---|---|
| B1 | Ctrl+K abre; setas/Enter/Esc funcionam |
| B2 | Telefone completo, parcial (≥ 6 dígitos), com máscara diferente, e-mail, nome |
| B3 | Até 5 contatos e 5 negociações; em andamento primeiro; "Ver todos" abre lista filtrada |
| B4 | Termo inexistente → "Nada encontrado" |

## 13. Não funcionais

| # | Requisito | Como testar |
|---|---|---|
| X1 | Rastreabilidade: etapa, valor, dono, ganho, perda, exclusão com usuário, data e hora | Coberto em D13/K8; conferir também exclusão |
| X2 | Desempenho com ~19 mil contatos / 35 mil negociações | Tempo de abrir Kanban, Lista "todas", Contatos sem filtro e exportação grande (anotar segundos) |
| X3 | Exportação > 50 mil linhas pede para filtrar | |
| X4 | Acessos diretos por URL respeitam perfil | Repetir P8/P10 com URLs de Configurações → Usuários e Regras |

---

## Divergências já visíveis só comparando os documentos

Antes de testar, estes pontos diferem entre a especificação e o guia, ou dentro da própria especificação. Vale levar para a Letícia e para o time:

1. **Perfis:** a especificação tem 3 perfis; o sistema tem 4 (entrou Marketing, só leitura). Parece uma decisão boa, mas não está documentada na especificação.
2. **SLA:** a especificação do CRM diz 30 min em "SLA de primeiro contato" e 15 min em "Volumes e requisitos"; a do WhatsApp usa 10 min para "Urgente". Precisa de um número só.
3. **Card do Kanban configurável pelo usuário** (especificação) — o guia não cita.
4. **Filtros e ordenação do funil:** a especificação lista 10 filtros e 6 ordenações; o guia cita só responsável, status e busca.
5. **Ganho/Perda arrastando o card** (especificação) — o guia só fala em arrastar entre etapas.
6. **Lixeira de negociações:** a especificação exige recuperar negociações excluídas; o guia só fala da Lixeira de contatos.
7. **Registrar tarefa já realizada** (especificação) — o guia não cita.
8. **Convite de usuário por e-mail** (especificação) × "adicionar" (guia).
9. **Busca nas Conversas pelo texto da mensagem** (especificação) — o guia não deixa claro.
10. **Transferir conversa:** a especificação diz "inicialmente só o gestor"; o guia diz gestor/administrador (coerente, só confirmar que vendedor não consegue).
11. **Status de mensagem (enviada/entregue/lida/falhou com reenvio)** é critério obrigatório de qualidade na especificação e não aparece no guia.
12. **Automações** (motor "quando X, fazer Y"), cadências, mensagens agendadas, pitch automático e disparos estão na especificação e marcados como não construídos — confirmar se ficam para uma próxima fase.

---

## Registro de resultados

Preencher para cada caso: `ID | Perfil | Resultado (OK / FALHA / BLOQUEADO / FORA) | URL da tela | O que aconteceu | Print`.
