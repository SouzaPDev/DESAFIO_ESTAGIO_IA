# Decisões Técnicas

## 1. Objetivo

O objetivo do projeto é construir um fluxo de análise de operações financeiras que separe claramente duas responsabilidades:

- regras determinísticas para cálculos e identificação de sinais;
- modelo de linguagem para interpretação dos sinais e geração de um parecer.

Essa separação foi adotada porque o desafio determina que cálculos como soma, média, mediana, contagem e comparação com limites sejam realizados com pandas, enquanto a LLM deve ser utilizada para interpretação e redação. 

---

## 2. Tratamento dos dados

Foi utilizado pandas para o tratamento dos dados do Nível 1.

As principais decisões foram:

- converter os dados para um DataFrame;
- verificar valores ausentes;
- identificar registros duplicados pelo campo `id`;
- remover duplicidades mantendo a primeira ocorrência;
- converter a coluna `data` para o tipo datetime;
- converter `valor` para um tipo numérico;
- criar a coluna `valor_brl`;
- converter valores em USD utilizando a taxa `taxa_cambio_usd_brl` fornecida no próprio arquivo.

A taxa fornecida pelo desafio foi utilizada sem consulta a serviços externos.

### Trade-off

A remoção de duplicidades foi feita utilizando o `id` da operação como identificador. A decisão foi baseada na premissa de que o identificador representa uma operação única.

Em um sistema real, antes de remover registros, seria necessário verificar a origem da duplicidade e definir uma política de qualidade de dados com o sistema de origem.

---

## 3. Normalização monetária

Todos os valores utilizados nas regras foram convertidos para BRL.

Para operações em BRL, o valor original foi mantido.

Para operações em USD, foi aplicada a taxa fornecida no arquivo:

`valor_brl = valor_usd * taxa_usd_brl`

A decisão evita que as regras determinísticas comparem valores em moedas diferentes.

---

## 4. Regras determinísticas

As regras foram implementadas em pandas.

### Regra 1 — Fracionamento

Foi criada uma agregação por cliente e data contendo:

quantidade de operações;
soma dos valores em BRL;
maior operação em BRL.

A operação é sinalizada quando:

existem pelo menos 3 operações na mesma data;
a soma ultrapassa R$ 50.000;
nenhuma operação individual atinge R$ 20.000.

### Regra 2 — Valor atípico

Foi calculada a mediana dos valores em BRL por cliente.

A operação é sinalizada quando:

o cliente possui pelo menos 4 operações;
o valor da operação é superior a 5 vezes a mediana daquele cliente.

### Decisão

As regras permanecem determinísticas e independentes da LLM.

A LLM não recebe a responsabilidade de calcular soma, mediana, quantidade ou verificar os limites das regras.

---

## 5. Separação entre regras e LLM

Essa foi uma das principais decisões arquiteturais do projeto.

O fluxo adotado é:

Dados
  
-Limpeza com pandas
  
-Normalização monetária
  
-Regras determinísticas
  
-Sinais por cliente
  
-Contexto do cliente
  
-LLM
  
-Interpretação
  
-Parecer estruturado