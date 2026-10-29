# Resultado — Rodada 1 (pontos críticos + permissões)

Data: 29/10/2026 · Ambiente: sandbox · Execução automatizada (Playwright) com os usuários de teste Administrador, Gestor e Vendedor.
Referências de caso: `plano-de-testes-crm.md`.

## Resumo

| Resultado | Qtde |
|---|---|
| OK | 19 |
| FALHA | 2 |
| OK com ressalva (falta item da especificação) | 2 |
| Pendente (precisa de celular no Sandbox Twilio) | 2 |

## Casos

| ID | Caso | Perfil | Resultado | Evidência |
|---|---|---|---|---|
| C1 | Negociação nova de contato com dona vai para a dona | G | **OK** | Gestor criou negociação para contato da carteira "Teste CRM Vendedor" → responsável = Teste CRM Vendedor, etapa "Sem contato", histórico registra |
| C2 | Idem entrando pelo WhatsApp | — | PENDENTE | Precisa de celular aderido ao Sandbox Twilio |
| C3 | Mesmo telefone, e-mail novo | G | **OK** | "Este contato já existe (TESTE-QA Base), dono: Teste CRM Vendedor" + link para abrir |
| C4 | Mesmo e-mail, telefone novo | G | **OK** | Idem |
| C5 | Telefone com `+55 61 98888 7731`, `61988887731` e sem nono dígito `(61) 8888-7731` | G | **OK** | Os três reconhecidos como o mesmo contato |
| C6 | E-mail com maiúsculas e espaços | G | **OK** | Reconhecido |
| K4 | Sem telefone e e-mail / telefone sem DDD | G | **OK** | Mensagens: "Informe telefone ou e-mail" e "Telefone inválido: use DDD + número" |
| C7 | Conflito com donas diferentes (importação CSV) | G | **OK** | Prévia: "Conflito · Donas diferentes"; após gravar, nada foi unido e o caso apareceu em Duplicados → Dados em conflito, com as opções Unir/Adicionar dados/Manter separados |
| K7 | Prévia da importação antes de gravar | G | **OK** | "Pré-visualização (nada foi salvo)": linhas, novos, unidos, sem mudança, conflitos, erros |
| R1 | Rodízio alterna as donas | V | **OK** | 4 contatos seguidos → Cassiano, Renata, Julia, Teste CRM Vendedor; histórico mostra "rodízio: carteira de …" |
| R3 | Só G/A escolhem a dona ao criar | V | **OK** | Vendedor não vê o campo "Dono da carteira" |
| C8 | Tarefa abre painel lateral sem trocar de tela | V | **OK com ressalva** | Painel abre na mesma lista (só muda `?tarefa=` no endereço). Mudar etapa, anotar e concluir funcionaram e foram para o histórico. **Faltam, segundo a especificação:** telefone e valor da negociação no painel, e atalho para abrir a conversa de WhatsApp |
| T1/T2 | Formulário de tarefa; registrar tarefa já realizada | V | **OK** | Responsável padrão = quem cria; existe a caixa "já realizada" |
| C9 | Atalhos dentro da conversa | G | **OK com ressalva** | Conversa tem seletor de etapa, "Agendar próxima", nota interna, Ganho, Perda, Transferir, Encerrar, aviso de cobertura e janela de 24h. Verificado só na interface (sem enviar mensagem) |
| P2 | Padrão = carteira da própria vendedora | V | **FALHA (leve)** | Contatos abre na carteira própria (OK), mas **Negociações abre em "Todos os responsáveis"**. A especificação diz que o padrão deve ser a própria carteira |
| P3 | Vendedor não altera negociação de outra | V | **OK** | Aviso "Somente leitura: só Julia… ou um admin pode alterar"; botões de etapa desabilitados; arrastar no Kanban não mudou a etapa |
| P3b | Vendedor altera a própria | V | **OK** | Mudança de etapa registrada |
| P4/P5 | Vendedor sem Configurações, Metas, Usuários, Duplicados | V | **OK** | Acesso direto pela URL → "Acesso restrito" |
| P7 | Gestor: sem Usuários, Funis só leitura | G | **OK** | |
| P8 | Marketing: sem Conversas | M | **OK** | Menu sem Conversas; URL direta bloqueada |
| P8b | Marketing: somente leitura | M | **FALHA (grave)** | Com perfil Marketing, na negociação da própria carteira apareceram habilitados Marcar ganho/perda, etapas, Editar e Excluir. **A mudança de etapa foi gravada** e **foi possível criar um contato**. O guia diz que Marketing "não altera dados" |
| P9 | Vendedor não apaga histórico | V | **OK** | Nenhum botão de exclusão no histórico |

Para o P8b, o usuário de teste Vendedor foi trocado temporariamente para Marketing e depois voltou para Vendedor. Ao virar Marketing, ele continuou dono da própria carteira. O bloqueio de Marketing parece não valer quando a pessoa é dona da negociação. Vale testar também com um usuário Marketing sem carteira.

## Outras observações (não são casos do roteiro)

1. **A lista de produtos não é filtrada pelo funil.** Ao criar negociação no Funil Extensivo, aparecem também CNU, PEA, Pós e SaaS. Em Configurações → Funis, a coluna Produtos está vazia ("—") em todos os funis. Há também produtos com o mesmo nome e preços diferentes (ex.: "Extensivo 2026 - Pacote" R$ 12.353,52 e R$ 11.294,65).
2. **As listas de responsável e carteira trazem cerca de 35 pessoas** (todos os administradores da plataforma, além de "Airflow Clipping", "Cursos", "Marketing Clipping" etc.), e não só as vendedoras. Isso polui filtros e seletores de dona.
3. **O rodízio inclui o usuário "Teste CRM Vendedor".** Contatos novos no sandbox podem cair para ele.
4. **O usuário de teste Vendedor vê "Painel do Administrador"** e acessa `/admin/users`, porque é administrador da plataforma. Confirmar que as vendedoras reais não são.
5. **Etapas com grafia inconsistente** no Funil Extensivo: "descoberta" e "interesse" em minúsculas.
6. **Telefone exibido de formas diferentes.** No cabeçalho da conversa aparece "+55 47 9274-1703" (sem o 9); no painel aparece "(47) 99274-1703". Na lista de Contatos aparece "+5561977715511", sem máscara.

## Dados criados e limpeza

- Contatos "TESTE-QA …" (7) → excluídos (estão na Lixeira).
- Negociação "TESTE-QA Neg C1" → excluída.
- Conflito de importação → resolvido como "Manter separados".
- Perfil do usuário Teste CRM Vendedor → de volta a Vendedor.
- Uma negociação de terceiros foi usada só para tentar alterar sem permissão. A tentativa foi bloqueada, nada mudou.
