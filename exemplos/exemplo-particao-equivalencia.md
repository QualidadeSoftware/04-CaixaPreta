# Exemplo — Partição de Equivalência

> **Como usar este arquivo:** Leia o exemplo completo abaixo para entender a lógica da técnica e o formato de entrega. Depois, aplique o mesmo raciocínio às variáveis indicadas na Etapa 1 do roteiro.

---

## Contexto do Exemplo

Vamos aplicar Partição de Equivalência à regra **RN03 — Multa por Atraso**, especificamente à variável **"dias de atraso"**.

### Regra a testar (RN03)

| Dias de Atraso   | Valor da Multa               |
|------------------|------------------------------|
| 0 dias           | Sem multa                    |
| 1 a 7 dias       | R$ 1,00/dia                  |
| 8 a 15 dias      | R$ 2,00/dia                  |
| 16 a 30 dias     | R$ 3,00/dia                  |
| Acima de 30 dias | R$ 3,00/dia + bloqueio       |
| Valor negativo   | Inválido (erro de sistema)   |

---

## Passo 1 — Identificar as Classes de Equivalência

O domínio da variável "dias de atraso" pode ser dividido em regiões onde o sistema se comporta **da mesma forma**. Cada região é uma classe:

| Classe | Faixa de Valores | Tipo       | Justificativa                                |
|--------|------------------|------------|----------------------------------------------|
| **C1** | Valores negativos | Inválida  | Dias de atraso não pode ser negativo          |
| **C2** | 0                 | Válida    | Sem atraso → sem multa                        |
| **C3** | 1 a 7             | Válida    | Faixa 1 de multa (R$ 1,00/dia)               |
| **C4** | 8 a 15            | Válida    | Faixa 2 de multa (R$ 2,00/dia)               |
| **C5** | 16 a 30           | Válida    | Faixa 3 de multa (R$ 3,00/dia)               |
| **C6** | Acima de 30       | Válida    | Faixa 3 + bloqueio automático                |

> **Princípio da Técnica:** Qualquer valor dentro de uma mesma classe deve produzir o mesmo comportamento. Portanto, basta **um representante por classe** para cobrir aquele comportamento.

---

## Passo 2 — Escolher um Valor Representativo por Classe

| Classe | Valor Representativo | Raciocínio                        |
|--------|----------------------|-----------------------------------|
| C1     | -5                   | Qualquer negativo serve; escolhemos -5 |
| C2     | 0                    | Único valor possível na classe    |
| C3     | 4                    | Meio da faixa; representativo     |
| C4     | 10                   | Meio da faixa; representativo     |
| C5     | 20                   | Meio da faixa; representativo     |
| C6     | 45                   | Valor claramente acima de 30      |

---

## Passo 3 — Escrever os Casos de Teste

| ID     | Classe | Técnica                  | Pré-condição                                        | Entrada          | Resultado Esperado                        |
|--------|--------|--------------------------|-----------------------------------------------------|------------------|-------------------------------------------|
| CT-P01 | C1     | Partição de Equivalência | Livro devolvido; sistema processa multa              | -5 dias de atraso | Sistema rejeita/erro: valor inválido     |
| CT-P02 | C2     | Partição de Equivalência | Livro devolvido no prazo ou antes                   | 0 dias de atraso | Multa = R$ 0,00; sem bloqueio            |
| CT-P03 | C3     | Partição de Equivalência | Livro devolvido com 4 dias de atraso                | 4 dias de atraso | Multa = R$ 4,00 (4 × R$ 1,00)           |
| CT-P04 | C4     | Partição de Equivalência | Livro devolvido com 10 dias de atraso               | 10 dias de atraso| Multa = R$ 20,00 (10 × R$ 2,00)         |
| CT-P05 | C5     | Partição de Equivalência | Livro devolvido com 20 dias de atraso               | 20 dias de atraso| Multa = R$ 60,00 (20 × R$ 3,00)         |
| CT-P06 | C6     | Partição de Equivalência | Livro devolvido com 45 dias de atraso               | 45 dias de atraso| Multa = R$ 135,00 + usuário **bloqueado** |

---

## O que Observar

- **6 classes → 6 casos de teste** cobrem todo o domínio da variável "dias de atraso".
- Sem esta técnica, seria impossível saber quantos testes são "suficientes".
- A classe **inválida (C1)** é tão importante quanto as válidas — sistemas frequentemente falham em tratar entradas inesperadas.

---

## Template para Preenchimento

Use a estrutura abaixo ao documentar suas próprias partições:

```
| Variável   | Classe | Faixa de Valores | Tipo (Válida/Inválida) | Valor Representativo |
|------------|--------|------------------|------------------------|---------------------|
| <variável> | C?     | <descrever faixa>| <Válida ou Inválida>   | <valor escolhido>   |
```

E para o caso de teste:

```
| ID     | Técnica                  | Pré-condição         | Entrada       | Resultado Esperado   |
|--------|--------------------------|----------------------|---------------|----------------------|
| CT-P?? | Partição de Equivalência | <estado do sistema>  | <valor da C?> | <saída esperada>     |
```
