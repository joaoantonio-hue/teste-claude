# Resultado — Rodada 3 (casos pendentes e parciais, exceto WhatsApp)

Data: 04/11/2026 · Ambiente: sandbox · Execução automatizada (Playwright).

| ID | Caso | Resultado | Evidência |
|---|---|---|---|
| S6 / N7 | Mudar dias de alerta e conferir cores no Kanban | **OK** | Valores atuais: amarelo 2, vermelho 5. Não há negociações com menos de 6 dias parado no sandbox, então testei com amarelo 7 e vermelho 30: cards abaixo de 7 dias ficaram neutros, de 7 a 29 ficaram amarelos e de 30 ou mais ficaram vermelhos. Valores voltaram para 2 e 5 |
| P8b | Marketing **sem carteira** (Teste CRM Gestor trocado temporariamente para Marketing) | **FALHA (confirma F1)** | Não consegue alterar negociação de outra pessoa (vale a regra de dono). Mas **criou um contato** e tem Anotação, Tarefa e WhatsApp habilitados no detalhe da negociação. Conclusão: o perfil Marketing não está sendo aplicado como somente leitura; o que bloqueia é só a regra de dono |
| P10 | Permissão validada no servidor | **OK** | Requisição de mudança de etapa repetida direto na API (`PATCH /api/crm/v1/deals/{id}/stage`) para negociação de outra carteira → **403** "Só o responsável pela negociação ou um admin pode alterá-la"; etapa não mudou |
| D5b | Substituir comprovante | **OK** | Histórico: "Comprovante anexado" e depois "Comprovante substituído"; o arquivo aberto passou a ser a imagem nova |
| S1b | Criar, reordenar e renomear etapas | **OK** | No funil de teste: criar 2 etapas, Subir/Descer, Editar nome e Excluir (só com a etapa vazia) |
| X3 | Exportação grande | **OK, limite não testável** | Exportar todos os contatos: 19.125 linhas em cerca de 2,7 s, CSV correto. O limite de 50 mil linhas não pode ser atingido com os dados do sandbox (a maior exportação possível é a de contatos) |
| A2 | Resumo da manhã bate com a lista | **Inconclusivo** | Os links abrem as listas filtradas, com faixa "Mostrando negociações sem próxima tarefa". Não dá para comparar a contagem: o resumo é gerado uma vez por manhã e os dados de teste mudaram depois. Para validar, é preciso deixar uma carteira com estado conhecido e conferir na manhã seguinte |
| A4 | Prazo de alerta configurável | **OK** | Coberto pelo S6 |
| A5 | Silenciar notificações | Não testado | A opção fica nas Conversas (depende do bloco de WhatsApp) |
| K13 | Situação de aluno | Não testado | Depende da integração com a base de alunos |

## Observação de usabilidade (nova)

**Configurações → Funis e etapas:** a tela abre com o seletor de etapas no Funil Extensivo. Clicar na linha de outro funil na tabela **não** troca o funil cujas etapas estão sendo editadas: a troca é só pelo seletor no topo. É fácil editar o funil errado.

Isso me aconteceu duas vezes durante os testes, e as duas foram revertidas na hora:
- **Rodada 2:** chance de 50% gravada em "Sem contato" do Extensivo.
- **Rodada 3:** duas etapas "TESTE-QA Etapa …" criadas no Extensivo, sem nenhuma negociação nelas, e excluídas logo em seguida.

Sugestão: destacar qual funil está selecionado e fazer o clique na linha selecionar o funil.

## Limpeza

- Negociações e contatos "TESTE-QA": excluídos.
- Funil "TESTE-QA Etapas": arquivado.
- Regras: de volta a 2 e 5.
- Teste CRM Gestor: de volta ao perfil Gestor.
- Funil Extensivo: 6 etapas originais.
