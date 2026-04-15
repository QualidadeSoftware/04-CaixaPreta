# Exemplo — Análise de Valor-Limite

> **Como usar este arquivo:** Leia o exemplo completo abaixo para entender a lógica da técnica e o formato de entrega. Depois, aplique o mesmo raciocínio às variáveis identificadas na Etapa 2 do roteiro.

---

## Contexto do Exemplo

Vamos aplicar Análise de Valor-Limite à regra **RN01 — Limite de Empréstimos Simultâneos** para o tipo de usuário **Aluno** (limite = 3 livros).

### Regra a testar (RN01 — Aluno)

- Aluno pode ter **até 3** livros emprestados simultaneamente.
- Com 3 livros ativos → próxima solicitação deve ser **rejeitada**.
- Com menos de 3 livros ativos → solicitação deve ser **aprovada**.

---

## Passo 1 — Identificar as Fronteiras

A variável "quantidade de empréstimos ativos" possui as seguintes fronteiras:

```
      ← Inválido →  ← Abaixo do limite →  [Limite]  ← Acima do limite →
           |               |                  |               |
          -1               0          ...     3               4 ...
```

| Fronteira            | Valor | Descrição                                    |
|----------------------|-------|----------------------------------------------|
| Abaixo do mínimo     | -1    | Valor inválido (não pode ter empréstimos negativos) |
| Mínimo absoluto      | 0     | Nenhum empréstimo ativo                      |
| Um antes do limite   | 2     | Um abaixo do limite → ainda pode emprestar   |
| **No limite**        | **3** | **Exatamente no limite → próximo é negado**  |
| **Um após o limite** | **4** | **Acima do limite → solicitação negada**     |

> **Por que testar nos limites?** A maioria dos erros de programação ocorre exatamente nas bordas: um desenvolvedor pode usar `>` em vez de `>=`, ou `<` em vez de `<=`. Testar o valor **no limite** e o valor **imediatamente adjacente** expõe esses erros.

---

## Passo 2 — Escrever os Casos de Teste de Valor-Limite

| ID     | Técnica              | Pré-condição                              | Entrada (Qtd. ativos) | Ação             | Resultado Esperado                       |
|--------|----------------------|-------------------------------------------|-----------------------|------------------|------------------------------------------|
| CT-L01 | Análise Valor-Limite | Aluno com empréstimos inválidos no sistema | -1 (valor inválido)  | Solicitar livro  | Sistema rejeita / erro de validação      |
| CT-L02 | Análise Valor-Limite | Aluno sem nenhum empréstimo ativo          | 0                     | Solicitar livro  | Empréstimo concedido                     |
| CT-L03 | Análise Valor-Limite | Aluno com 2 empréstimos ativos             | 2 (um abaixo do limite) | Solicitar livro | Empréstimo concedido                   |
| CT-L04 | Análise Valor-Limite | Aluno com 3 empréstimos ativos (no limite) | 3                    | Solicitar livro  | **"Limite de empréstimos atingido."**    |
| CT-L05 | Análise Valor-Limite | Aluno com 4 empréstimos (inconsistência)  | 4 (acima do limite)  | Solicitar livro  | **"Limite de empréstimos atingido."**    |

---

## Passo 3 — Exemplo Adicional: Fronteiras de Multa (RN03)

A mesma lógica se aplica às faixas de multa. Vejamos as fronteiras entre a **faixa 1** (≤7 dias → R$1,00/dia) e a **faixa 2** (8–15 dias → R$2,00/dia):

| ID     | Técnica              | Pré-condição         | Dias de Atraso | Resultado Esperado               |
|--------|----------------------|----------------------|----------------|----------------------------------|
| CT-L06 | Análise Valor-Limite | Livro devolvido      | 6 (um antes do fim da faixa 1) | Multa = R$ 6,00 (6 × R$1,00)  |
| CT-L07 | Análise Valor-Limite | Livro devolvido      | 7 (**fim da faixa 1**)         | Multa = R$ 7,00 (7 × R$1,00)  |
| CT-L08 | Análise Valor-Limite | Livro devolvido      | 8 (**início da faixa 2**)      | Multa = R$ 16,00 (8 × R$2,00) |
| CT-L09 | Análise Valor-Limite | Livro devolvido      | 9 (um após o início da faixa 2)| Multa = R$ 18,00 (9 × R$2,00) |

> **Atenção:** Para cada fronteira entre faixas, teste **pelo menos dois pontos**: o valor no final de uma faixa e o valor no início da faixa seguinte.

---

## Resumo das Fronteiras a Cobrir (RN03 — completo)

| Fronteira                  | Valores Críticos | O que verificar                          |
|----------------------------|------------------|------------------------------------------|
| Sem atraso / Faixa 1       | 0 e 1            | 0 → sem multa; 1 → R$1,00               |
| Fim Faixa 1 / Início Faixa 2 | 7 e 8          | 7 → R$7,00; 8 → R$16,00                 |
| Fim Faixa 2 / Início Faixa 3 | 15 e 16        | 15 → R$30,00; 16 → R$48,00              |
| Fim Faixa 3 / Bloqueio     | 30 e 31          | 30 → R$90,00 sem bloqueio; 31 → bloqueio |

---

## Template para Preenchimento

```
| ID     | Técnica              | Pré-condição          | Entrada (valor-limite) | Resultado Esperado |
|--------|----------------------|-----------------------|------------------------|--------------------|
| CT-L?? | Análise Valor-Limite | <estado do sistema>   | <valor exato>          | <saída esperada>   |
```

**Dica de preenchimento da coluna "Entrada":** sempre especifique o valor numérico exato, não uma faixa. O objetivo desta técnica é testar **pontos precisos** na fronteira.
