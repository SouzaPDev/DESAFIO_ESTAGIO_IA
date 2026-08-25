# Uso de Inteligência Artificial

## Ferramentas utilizadas

### ChatGPT

Utilizado durante o desenvolvimento para:

-esclarecer dúvidas sobre Python e pandas;
-entender erros encontrados durante a execução do notebook;
-revisar a organização do código;
-discutir alternativas de implementação;
-auxiliar na documentação do projeto;
-revisar a estrutura dos arquivos de entrega.

As sugestões foram analisadas e adaptadas ao contexto do projeto antes de serem utilizadas.

### Ollama + Qwen3:8b

Utilizados como modelo de linguagem para a etapa de interpretação dos sinais determinísticos.

O modelo recebe o contexto de um cliente já processado pelas regras em pandas e produz um parecer.

O modelo não é utilizado para calcular:

-soma;
-mediana;
-quantidade de operações;
-conversão monetária;
-comparação dos valores com os limites das regras.

Esses cálculos são realizados deterministicamente com pandas.

---

## Um caso em que a IA levou para um caminho incorreto

Durante o desenvolvimento, inicialmente foi utilizada a abordagem da API da OpenAI com `client.responses.parse()`.

Posteriormente, como a solução passou a utilizar Ollama com o modelo Qwen3:8b, essa abordagem deixou de ser adequada.
