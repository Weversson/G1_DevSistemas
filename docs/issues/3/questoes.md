# Avaliação de Grau 1: listagem de alunos e risco

## Issue analisada

Issue [#3, endpoint de listagem de alunos e risco](https://github.com/BitStudioLabs/mais-alunos/issues/3), atribuída a Weversson e fechada em 28/08/2026. A issue faz parte da história [#1, Listagem Preditiva de Alunos](https://github.com/BitStudioLabs/mais-alunos/issues/1). O código analisado é o da branch `unstable`, no commit `342f9fe`.

O commit associado à implementação inicial é `a53716069b83d36b5f2248cbfe4510d610c55724`, registrado com autor `Weversson <weverssonlucas@rede.ulbra.br>`. O código desse commit está nas branches remotas `main` e `unstable`. As referências abaixo descrevem o código atual em `unstable`.

Este repositório contém as respostas e os diagramas da avaliação. Os commits citados na matriz pertencem ao repositório do projeto +Alunos e registram a implementação. O commit deste repositório registra somente os arquivos da avaliação. Não localizei um pull request associado diretamente à issue #3; o commit inicial está incorporado em `main` e `unstable`.

## Questão 1: rastreabilidade

| Requisito e critério de aceite | Classe ou método | Teste | Commit | Resultado |
|---|---|---|---|---|
| RF03, RF04 e RF05. A listagem apresenta nome, matrícula e probabilidade de evasão. Critério da história #1: a tabela carrega nome, matrícula e probabilidade processada no back-end. | `list_students`, `backend/app/routers/students.py:52-91`; `fetch_students`, `backend/app/database.py:127-186`; `StudentList` e `StudentListItem`, `backend/app/schemas.py:79-92` | `test_students_list_default`, `backend/tests/test_api.py:119-126` | `a537160` | Implementado. A API retorna probabilidade entre 0 e 1. A interface converte o valor para percentual em `frontend/src/lib/constants.ts:100-102`. A listagem usa os registros de validação salvos no banco, não todos os alunos de um cadastro institucional. |
| RNF01. Processamento e inferência ocorrem no back-end. | Treino e gravação dos resultados, `backend/scripts/train_model.py:45-66,69-88`; consulta da listagem, `backend/app/database.py:176-185` | `test_students_list_default`, `backend/tests/test_api.py:119-126`, verifica a resposta da API, não o cálculo do modelo em cada requisição | `a537160` | Parcial. O modelo é executado no back-end durante o treino. A rota lê probabilidades já gravadas; não executa inferência ao receber cada chamada. |
| RF06. A linha de aluno em faixa crítica recebe indicador visual. Critério da história #1: a linha mostra alerta visual para risco crítico. | Condição e classe da linha, `frontend/src/components/StudentsTable.tsx:306,313` | Não há teste de componente que verifique a classe visual. `test_students_filter_by_risco`, `backend/tests/test_api.py:143-146`, testa o filtro da API | `368f910` | Implementado no código, sem teste específico da apresentação visual. A cadeia requisito, código, teste está quebrada neste ponto. |
| RN02. Aluno com dado incompleto permanece na listagem e recebe risco indisponível. Critério da história #1: não omitir a linha e sinalizar risco inconclusivo. | `fetch_students`, `backend/app/database.py:179-185`; `_risco_item`, `backend/app/routers/students.py:24-29` | `test_students_anomalous_rows_not_omitted`, `backend/tests/test_api.py:266-277`; `test_students_filter_risco_indisponivel`, linhas 280-288; `test_risco_item_maps_inconclusive_to_indisponivel`, linhas 291-305 | `368f910` | Implementado no código atual. Essa regra entrou em commit posterior ao fechamento da issue #3. |
| RF07, RF09, RF10 e RF11. Ordenação, filtro, busca por nome e paginação. | `list_students`, `backend/app/routers/students.py:53-76`; consultas, `backend/app/database.py:142-185` | `test_students_search_by_name`, linhas 129-134; `test_students_filter_by_risco`, linhas 143-146; `test_students_sort_probabilidade_desc`, linhas 164-168 | `a537160` | Implementado e testado na API. |

### Análise da quebra

O código aplica uma classe visual para linhas de risco alto ou muito alto em `frontend/src/components/StudentsTable.tsx:306,313`. Não encontrei teste automatizado de renderização dessa tabela. O teste `test_students_filter_by_risco` verifica quais dados a API retorna, mas não verifica a cor ou a classe CSS.

Classifico a falta do teste visual como débito técnico de cobertura. O código implementa o critério, mas a suíte não protege esse comportamento contra regressões. Um teste de componente poderia verificar a classe aplicada à linha crítica.

Outra diferença de histórico: a regra para registros incompletos está no código atual e nos testes do commit `368f910`, de 04/09/2026. O fechamento inicial da issue #3 ocorreu em 28/08/2026, no commit `a537160`. Portanto, essa regra não deve ser atribuída ao commit inicial.

## Questão 2: classificação dos requisitos

| Tipo | Requisito | Classificação no código atual |
|---|---|---|
| Funcional | RF03: listar alunos processados | Implementado para as linhas gravadas em `validation_data`. Não representa o cadastro completo da instituição. Evidência: `backend/scripts/train_model.py:69-88`. |
| Funcional | RF04 e RF05: exibir nome, matrícula e probabilidade | Implementado pela resposta da rota e pelo esquema Pydantic. Evidência: `backend/app/routers/students.py:77-91` e `backend/app/schemas.py:79-92`. Nome e matrícula são demonstrativos: `students.py:17-21` e `database.py:181-182`. |
| Funcional | RF06: realçar faixa de risco crítica | Implementado na tabela para risco alto ou muito alto. Sem teste automatizado de componente. Evidência: `frontend/src/components/StudentsTable.tsx:306,313`. |
| Funcional | RF07 e RF08: ordenar por risco e nome | A API aceita coluna e direção de ordenação; há teste para probabilidade decrescente. Evidência: `backend/app/routers/students.py:58-66`, `backend/app/database.py:137-140,158`; teste em `backend/tests/test_api.py:164-168`. |
| Funcional | RF09 e RF10: filtrar por risco e pesquisar nome | Implementado pela consulta SQL. Evidência: `backend/app/database.py:142-151`; testes em `backend/tests/test_api.py:129-146`. |
| Funcional | RF11: paginar a listagem | Implementado com `limit` e `offset`. Evidência: `backend/app/routers/students.py:60-61,67-76` e `backend/app/database.py:179-185`. |
| Regra de negócio | RN01: apresentar risco como probabilidade, sem decisão binária de evasão | Implementado pela probabilidade devolvida pela API. Evidência: `backend/app/routers/students.py:24-29,81-88`. |
| Regra de negócio | RN02: manter registros incompletos na lista e sinalizar risco indisponível | Implementado no código atual e coberto por testes. Evidência: `backend/app/routers/students.py:24-29`, `backend/tests/test_api.py:266-305`. Entrou após o fechamento inicial da issue. |
| Não funcional | RNF01: processamento e inferência no back-end | Atendido quanto à execução no back-end. A inferência acontece no treino e é persistida; a rota de listagem não recalcula. Evidência: `backend/scripts/train_model.py:45-66,69-88`. |
| Não funcional | RNF02: consumir o dataset indicado | O treinamento carrega o conjunto de dados configurado e persiste uma amostra de teste. O uso exclusivo da fonte não é verificado por teste nesta issue. Evidência: `backend/scripts/train_model.py:41-47,69-88`. |
| Não funcional | RNF03: usar framework moderno no front-end | Implementado com React. Evidência: `frontend/src/components/StudentsTable.tsx`. |
| Não funcional | RNF04: filtros, busca e ordenação abaixo de 2 segundos | Não verificado. Não localizei benchmark que comprove o limite. |
| Não funcional | RNF05: otimização para desktop a partir de 1366x768 | A interface possui uma tabela, mas não localizei teste de viewport que comprove essa resolução. Não verificado formalmente. |

## Questão 3: sequência e transação

![Diagrama de sequência da rota GET /students](sequencia.png)

Fonte Mermaid: [`sequencia.mmd`](sequencia.mmd).

O navegador envia `GET /students` pela função `fetchStudents` em `frontend/src/lib/api.ts:14-23,51-63`. Essa chamada de rede é assíncrona no navegador, pois usa `fetch` com `await`. No back-end, `list_students` é uma função síncrona e consulta SQLite de forma síncrona, conforme `backend/app/routers/students.py:52-76` e `backend/app/database.py:127-185`. Não há publicação de evento ou tarefa assíncrona nesse fluxo.

1. FastAPI valida os parâmetros de ordenação, direção e paginação em `backend/app/routers/students.py:58-66`. Ordenação ou direção inválida resulta em HTTP 422 antes do acesso ao banco.
2. A rota chama `fetch_students` em `backend/app/routers/students.py:67-76`.
3. `fetch_students` abre a conexão por `connect` em `backend/app/database.py:22-33`, verifica a tabela e executa `SELECT COUNT(*)` e `SELECT` paginado em `database.py:160-185`.
4. A função retorna itens e total. A rota monta o JSON, gera matrícula e normaliza o risco em `backend/app/routers/students.py:24-29,77-91`. O esquema de resposta está em `backend/app/schemas.py:79-92`.
5. A resposta HTTP 200 retorna o JSON ao navegador. O front-end lê o corpo da resposta em `frontend/src/lib/api.ts:18-23`.

### Transação e falhas

- Início da conexão: `sqlite3.connect` em `backend/app/database.py:24`. Não há comando `BEGIN` explícito no fluxo da rota.
- Caminho normal: são executados `PRAGMA` e dois `SELECT`s em `backend/app/database.py:160-185`. Ao sair do contexto, `connect` chama `commit` e fecha a conexão em `database.py:27-33`. A rota não grava dados.
- Erro durante consulta: `connect` chama `rollback`, propaga a exceção e fecha a conexão em `backend/app/database.py:29-33`. O tratador genérico responde HTTP 500 em `backend/app/main.py:64-67`.
- Parâmetro inválido: a rota rejeita a solicitação com HTTP 422 antes de consultar o banco em `backend/app/routers/students.py:63-66`.
- Risco ausente ou inválido: não causa erro na solicitação. `_risco_item` mantém a linha e devolve probabilidade nula e risco `Indisponível`, em `backend/app/routers/students.py:24-29`.
- Dado gravado pela metade ou evento sem registro: não é possível nesse fluxo, pois ele não grava nem publica eventos. Em falha durante as leituras, o código executa rollback, embora não haja escrita a desfazer.
- Consistência entre `total` e `items`: a contagem e a página são consultas separadas em `backend/app/database.py:176-185`; não há `BEGIN` explícito que assegure um único snapshot. Uma alteração concorrente da tabela pode fazer os valores refletirem instantes diferentes.

Declaração: utilizei inteligência artificial como apoio para localizar evidências e organizar este documento. Revisei as referências no código do projeto.
