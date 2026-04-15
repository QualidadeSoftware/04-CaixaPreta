# Exemplo — Tabela de Decisão

> **Como usar este arquivo:** Leia o exemplo completo abaixo para entender a lógica da técnica e o formato de entrega. Depois, aplique o mesmo raciocínio à regra de renovação (RN05) na Etapa 3 do roteiro.

---

## Contexto do Exemplo

Vamos aplicar Tabela de Decisão a uma regra **mais simples** — a **RN01 combinada com RN04**: "Um usuário pode emprestar um livro?"

### Regras combinadas

Para realizar um empréstimo, **todas** as condições abaixo devem ser satisfeitas:

| # | Condição                                              |
|---|-------------------------------------------------------|
| C1 | Usuário não está bloqueado (RN04)                    |
| C2 | Usuário não atingiu seu limite de empréstimos (RN01) |

---

## Passo 1 — Enumerar Combinações

Com **2 condições binárias**, temos 2² = **4 combinações** possíveis:

|                         | R1  | R2  | R3  | R4  |
|-------------------------|-----|-----|-----|-----|
| **Condições**           |     |     |     |     |
| C1: Não está bloqueado? | Sim | Sim | Não | Não |
| C2: Abaixo do limite?   | Sim | Não | Sim | Não |
| **Ações**               |     |     |     |     |
| Conceder empréstimo?    | ✅  | ❌  | ❌  | ❌  |
| Mensagem exibida        | —   | "Limite atingido." | "Usuário bloqueado." | "Usuário bloqueado." |

> **Leitura da tabela:** Cada coluna (R1, R2, ...) é uma **regra** — uma combinação específica de condições. A parte inferior mostra o que o sistema deve fazer para cada regra.

---

## Passo 2 — Derivar um Caso de Teste por Regra

Cada regra da tabela gera ao menos um caso de teste:

| ID     | Regra | Técnica          | Pré-condição                                                    | Ação              | Resultado Esperado          |
|--------|-------|------------------|-----------------------------------------------------------------|-------------------|-----------------------------|
| CT-D01 | R1    | Tabela de Decisão | Aluno ativo, com 1 empréstimo (abaixo do limite de 3)          | Solicitar livro   | Empréstimo concedido        |
| CT-D02 | R2    | Tabela de Decisão | Aluno ativo, com 3 empréstimos (no limite)                     | Solicitar livro   | "Limite de empréstimos atingido." |
| CT-D03 | R3    | Tabela de Decisão | Aluno **bloqueado**, com 1 empréstimo (abaixo do limite)       | Solicitar livro   | "Usuário bloqueado."        |
| CT-D04 | R4    | Tabela de Decisão | Aluno **bloqueado**, com 3 empréstimos (no limite)             | Solicitar livro   | "Usuário bloqueado."        |

> **Observação sobre R3 e R4:** Ambas negam o empréstimo pela mesma razão (bloqueio). Nesses casos, as regras podem ser **mescladas** em uma só, reduzindo os testes sem perder cobertura.

---

## Passo 3 — Tabela Simplificada (com regras mescladas)

Quando múltiplas regras levam à **mesma ação** independentemente do valor de alguma condição, usamos o símbolo **"–"** (indiferente):

|                         | R1  | R2  | R3     |
|-------------------------|-----|-----|--------|
| **Condições**           |     |     |        |
| C1: Não está bloqueado? | Sim | Sim | Não    |
| C2: Abaixo do limite?   | Sim | Não | **–**  |
| **Ações**               |     |     |        |
| Conceder empréstimo?    | ✅  | ❌  | ❌     |
| Mensagem exibida        | —   | "Limite atingido." | "Usuário bloqueado." |

> **"–" significa:** o valor desta condição não importa — o resultado é o mesmo independentemente dela.

---

## Como Aplicar à RN05 (Etapa 3 do Roteiro)

A regra de renovação tem **4 condições**, gerando até 2⁴ = **16 combinações**. O raciocínio é o mesmo:

1. Liste as 4 condições booleanas (bloqueado? reserva pendente? já renovado? em atraso?).
2. Preencha todas as 16 colunas com "Sim/Não".
3. Defina a ação para cada coluna (permitir ou negar renovação, com o motivo).
4. Simplifique colunas com a mesma ação usando "–".
5. Derive um caso de teste por regra (ou por grupo de regras simplificadas).

---

## Template para Preenchimento

**Tabela de Decisão:**

```
|                    | R1  | R2  | R3  | R4  | ... |
|--------------------|-----|-----|-----|-----|-----|
| C1: <condição>?    | S   | S   | N   | N   | ... |
| C2: <condição>?    | S   | N   | S   | N   | ... |
| ...                | ... | ... | ... | ... | ... |
| Ação               | <resultado> | ... | ... | ... | ... |
```

**Caso de teste derivado:**

```
| ID     | Regra | Técnica          | Pré-condição           | Ação           | Resultado Esperado  |
|--------|-------|------------------|------------------------|----------------|---------------------|
| CT-D?? | R?    | Tabela de Decisão | <descrever o cenário> | <operação>     | <saída esperada>    |
```
