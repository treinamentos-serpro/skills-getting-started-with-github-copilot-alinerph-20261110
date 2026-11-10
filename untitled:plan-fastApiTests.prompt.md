## Plan: Testes FastAPI de Backend

Adicionar uma suíte de testes HTTP com pytest em `tests/`, cobrindo todas as rotas atualmente implementadas no FastAPI. Reutilizar `TestClient`, dependências já presentes e o padrão Arrange-Act-Assert (AAA); manter a alteração focada em testes, sem mudar regras da API.

**Steps**
1. Declarar `pytest` como dependência de desenvolvimento em `requirements.txt`; manter `pytest.ini`, cuja configuração já inclui a raiz do projeto no `pythonpath`.
2. Criar testes em `tests/test_app.py`, importar `app` e `activities` de `src.app` e instanciar `fastapi.testclient.TestClient` para chamadas HTTP sem servidor externo.
3. Criar isolamento por teste para o estado mutável: substituir `activities` por uma cópia profunda com `monkeypatch` (ou fixture equivalente), de modo que inscrições e remoções não dependam da ordem dos testes.
4. Estruturar cada teste explicitamente em AAA: **Arrange** prepara estado e dados; **Act** faz uma chamada HTTP; **Assert** verifica status, corpo/redirecionamento e estado final quando houver mutação. Cobrir: `GET /` redireciona para a página estática; `GET /activities` devolve atividades e campos esperados; inscrição válida inclui participante; atividade inexistente retorna 404; inscrição duplicada retorna 400; remoção válida retira participante; atividade inexistente e participante ausente na remoção retornam 404.
5. Executar a suíte na raiz com `pytest`. Se a inicialização do `TestClient` revelar incompatibilidade entre versões de FastAPI/Starlette/httpx, ajustar apenas as restrições de dependência necessárias para uma combinação compatível e repetir a validação.

**Relevant files**
- `src/app.py` — comportamento sob teste: `root`, `get_activities`, `signup_for_activity`, `unregister_from_activity` e o dicionário global `activities`.
- `tests/test_app.py` — nova suíte e fixture de isolamento do estado.
- `requirements.txt` — incluir pytest; `httpx` já está declarado para o TestClient.
- `pytest.ini` — já configura `pythonpath = .`; não precisa de alteração salvo evidência em execução.

**Verification**
1. Instalar as dependências do projeto, incluindo pytest, em ambiente de desenvolvimento.
2. Executar `pytest` na raiz e confirmar todos os cenários verdes.
3. Confirmar que os casos de mutação deixam o estado independente entre testes e que os códigos HTTP/redirects correspondem ao comportamento atual.

**Decisions**
- Cobrir todas as rotas atuais, incluindo `/`, `DELETE` e cenários de erro, conforme escolha do usuário.
- Usar AAA e testar o comportamento existente; não incluir validações novas, limite de participantes ou mudanças funcionais na API.
- Não fixar versões preventivamente; tratar eventual incompatibilidade do TestClient com base no resultado da execução.
