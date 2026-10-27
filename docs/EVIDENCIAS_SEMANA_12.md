# Evidências de Conclusão — Semana 12

Este documento reúne todas as evidências de auditoria da atividade da **Semana 12 — Issues, Projects, Pull Requests e Quality Gates** para o repositório `matheusboffz/TODO-APP_BOFF`.

---

## 1. Cadeia de Rastreabilidade

```text
Issue #2 ➜ Branch feature/2 ➜ Commits ➜ CI Quality Gate ➜ PR #3 ➜ Falha Controlada (PR #5) ➜ Merge na main ➜ Issue #2 Concluída
```

---

## 2. Evidências no GitHub

| Item | Descrição | Link |
| :--- | :--- | :--- |
| **Issue Principal** | Cobrir rejeição de título vazio ou composto por espaços | [#2](https://github.com/matheusboffz/TODO-APP_BOFF/issues/2) *(Status: Fechada)* |
| **Pull Request Real** | Implementação do teste e configuração do CI Quality Gate | [#3](https://github.com/matheusboffz/TODO-APP_BOFF/pull/3) *(Status: Merged)* |
| **Issue de Falha Controlada** | Simulação e demonstração do bloqueio de CI | [#4](https://github.com/matheusboffz/TODO-APP_BOFF/issues/4) *(Status: Fechada)* |
| **PR de Falha Controlada** | Prova de bloqueio pelo check e posterior recuperação | [#5](https://github.com/matheusboffz/TODO-APP_BOFF/pull/5) *(Status: Fechada sem merge)* |
| **Workflow Runs (CI)** | Execuções completas do pipeline no GitHub Actions | [Actions CI Runs](https://github.com/matheusboffz/TODO-APP_BOFF/actions) |

---

## 3. Resumo Técnico das Alterações

### 3.1 Teste de Integração
- **Arquivo modificado:** `tests/integration/test_routes.py`
- **Novo teste:** `test_api_criar_tarefa_com_titulo_apenas_espacos_deve_retornar_400`
- **Comportamento validado:**
  - Requisição `POST /api/todos` com `json={"title": "   "}` retorna `HTTP 400`.
  - Corpo da resposta JSON contém a chave `"error"`.
  - Nenhuma tarefa inválida é inserida na listagem (`GET /api/todos`).

### 3.2 Pipeline de CI como Quality Gate
- **Arquivo modificado:** `.github/workflows/ci.yml`
- **Gatilhos configurados:** `push: [main]` e `pull_request: [main]`
- **Etapas obrigatórias executadas:**
  1. `actions/checkout@v4` e `actions/setup-python@v5` (Python 3.11 com cache de pip);
  2. Instalação de dependências (`requirements.txt`);
  3. Análise estática com Flake8 (`flake8 app tests --count --select=E9,F63,F7,F82`);
  4. Testes unitários (`pytest tests/unit`);
  5. Testes de integração (`pytest tests/integration`);
  6. Testes E2E (`pytest tests/e2e`);
  7. Quality Gate de cobertura mínima de 80% (`pytest --cov=app --cov-fail-under=80 tests/`).

---

## 4. Resultados da Validação Local

```text
============================= test session starts =============================
platform win32 -- Python 3.14.2, pytest-8.2.0, pluggy-1.7.0
rootdir: C:\Users\matheus boff\Desktop\TodoApp_N2
plugins: cov-5.0.0
collected 21 items

tests/unit/test_models.py::test_criar_tarefa_com_sucesso PASSED          [ 10%]
tests/unit/test_models.py::test_tarefa_deve_iniciar_como_nao_concluida_por_padrao PASSED [ 20%]
tests/unit/test_models.py::test_tarefa_com_titulo_vazio_deve_lancar_erro PASSED [ 30%]
tests/unit/test_services.py::test_service_criar_tarefa PASSED            [ 40%]
tests/unit/test_services.py::test_service_listar_todas_as_tarefas PASSED [ 50%]
tests/unit/test_services.py::test_service_buscar_tarefa_por_id_existente PASSED [ 60%]
tests/unit/test_services.py::test_service_buscar_tarefa_por_id_inexistente PASSED [ 70%]
tests/unit/test_services.py::test_service_atualizar_tarefa_existente PASSED [ 80%]
tests/unit/test_services.py::test_service_remover_tarefa_existente PASSED [ 90%]
tests/unit/test_services.py::test_service_remover_tarefa_inexistente PASSED [100%]
tests/integration/test_routes.py::test_api_health_check PASSED           [ 11%]
...
tests/integration/test_routes.py::test_api_criar_tarefa_com_titulo_apenas_espacos_deve_retornar_400 PASSED [ 55%]
...
tests/e2e/test_todo_lifecycle_e2e.py::test_jornada_completa_usuario_crud_e2e PASSED [ 50%]
tests/e2e/test_todo_lifecycle_e2e.py::test_resiliencia_e_recuperacao_de_erros_e2e PASSED [100%]

---------- coverage: platform win32, python 3.14.2-final-0 -----------
Name              Stmts   Miss  Cover   Missing
-----------------------------------------------
app\__init__.py      11      0   100%
app\models.py        10      0   100%
app\routes.py        45      2    96%   51-52
app\services.py      36      1    97%   28
-----------------------------------------------
TOTAL               102      3    97%

Required test coverage of 80% reached. Total coverage: 97.06%
============================= 21 passed in 0.18s ==============================
```

---

## 5. Demonstração de Bloqueio e Recuperação (Passo 8)

1. **Introdução da Falha:** Na branch `feature/4-demonstrar-bloqueio-ci`, foi alterada a asserção do health check de 200 para 201.
2. **Abertura da PR #5:** O GitHub Actions rodou e o job `Testes e Quality Gate` falhou (`Conclusion: failure`).
3. **Bloqueio Comprovado:** O status da PR no GitHub retornou `mergeable_state: blocked` devido ao check obrigatório não satisfeito.
4. **Recuperação:** O commit de reversão foi enviado à branch; o CI rodou novamente e obteve `Conclusion: success`, passando o estado da PR para `mergeable_state: clean`.
5. **Encerramento Seguro:** A PR #5 e a Issue #4 foram fechadas sem merge e a branch descartável foi removida.
