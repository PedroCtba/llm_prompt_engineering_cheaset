# Guia Prático de Prompt Engineering: Otimizando suas Interações com LLM's

Antes de mergulharmos no fascinante mundo do Prompt Engineering, quero que você entenda quando e como aplicá-lo. Nem todos os desafios com IAs podem ser resolvidos apenas ajustando prompts - alguns requerem mudanças mais profundas na arquitetura do sistema. No entanto, para a maioria dos casos, o Prompt Engineering oferece uma solução rápida, eficaz e de fácil manutenção, aproveitando a estrutura existente do modelo de IA.

Importante: O Prompt Engineering eficaz não é um processo de "tentativa e erro" aleatório. É fundamental que você possa medir e comprovar a melhoria do desempenho do modelo após cada ajuste.
A seguir, irei apresentar uma lista de ações para aprimorar a eficácia dos seus prompts, organizadas por ordem de importância. Para tarefas de menor prioridade ou não destinadas à produção, os primeiros passos geralmente são suficientes:
    
# 1. Seja claro, direto e detalhado

Trate todo modelo de LLM como um funcionário brilhante, mas que foi recém-contratado. Portanto, não conhece as normas, estilos, guias ou preferências da empresa.

- Diga para que o resultado será utilizado
- Para quem ele será fornecido
- Em qual fluxo de trabalho essa tarefa está participando, e em qual etapa
- O que caracteriza um resultado "bem-sucedido" dessa tarefa
- Limite o escopo da resposta (Se você quer só código, diga isso)
- Forneça a sequência (Dê a ordem dos passos)

Exemplo:

| **Papel** | **Prompt Vago** | **Prompt Claro** |
| --- | --- | --- |
| Usuário | Por favor, resuma estes comentários de clientes sobre nossa nova linha de produtos naturais: {{DADOS_COMENTARIOS}} | Sua tarefa é analisar os comentários dos clientes sobre nossa nova linha de produtos naturais para uma apresentação à diretoria. Instruções:<br>1. Identifique os 3 produtos mais mencionados.<br>2. Liste os principais pontos positivos e negativos de cada produto.<br>3. Sugira 2 melhorias para cada produto com base nos comentários.<br>4. Resuma o sentimento geral em uma frase.<br>5. Mantenha o tom profissional e objetivo.<br>6. Limite a resposta a 300 palavras.<br>Dados para analisar: {{DADOS_COMENTARIOS}} |
| Assistente (resposta do Claude) | Aqui está um resumo dos comentários dos clientes:<br>- Muitos elogios ao xampu de coco<br>- Alguns acharam o hidratante facial caro<br>- O sabonete de lavanda teve opiniões mistas<br>- No geral, os clientes gostaram da iniciativa de produtos naturais | 1. Produtos mais mencionados:<br>   a) Xampu de coco<br>   b) Hidratante facial<br>   c) Sabonete de lavanda<br>2. Principais pontos:<br>   Xampu de coco:<br>     Positivos: Cheiro agradável, deixa o cabelo macio<br>     Negativos: Embalagem pouco prática<br>   Hidratante facial:<br>     Positivos: Textura leve, boa absorção<br>     Negativos: Preço elevado, pouca duração<br>   Sabonete de lavanda:<br>     Positivos: Aroma relaxante, não resseca a pele<br>     Negativos: Espuma insuficiente<br>3. Sugestões de melhoria:<br>   Xampu de coco: Redesenhar embalagem, aumentar poder de limpeza<br>   Hidratante facial: Oferecer versão econômica, aumentar tamanho<br>   Sabonete de lavanda: Melhorar fórmula para mais espuma, intensificar aroma<br>4. Sentimento geral: Clientes estão entusiasmados com a linha natural, mas esperam ajustes em alguns produtos para justificar o investimento.<br>(Total: 298 palavras) |

É possível notar que o prompt 2 foi muito melhor. Pois ele:

- Disse para que os resultados seriam utilizados
- Para qual audiência iria (diretoria)
- Forneceu os passos sequencialmente
- Fechou o escopo da resposta dizendo que queria uma análise limitada a 300 palavras

# 2. Use exemplos (Multishot prompting)

Fornecendo exemplos bem montados de como você quer sua resposta, a necessidade de muito texto explicativo é dramaticamente reduzida, e a performance do modelo também melhora.

Seus exemplos devem ser:

- Relevantes: Refletem a realidade dos casos de uso que você irá ver
- Diversos: Evitam que modelos peguem padrões não desejados, e mostram casos extremos para que o modelo não simplifique demais a lógica
- Claros: Deixe os exemplos envolvidos por tags de exemplo:

  <exemplo>Exemplo de resposta do modelo</exemplo>

| **Papel** | **Sem Exemplos** | **Com Exemplos** |
| --- | --- | --- |
| Usuário | Analise este feedback de cliente e categorize os problemas. Use estas categorias: Interface, Desempenho, Solicitação de Recurso, Integração, Preço e Outros. Também avalie o sentimento (Positivo/Neutro/Negativo) e a prioridade (Alta/Média/Baixa). Aqui está o feedback: {{FEEDBACK}} | Nossa equipe de atendimento está sobrecarregada com feedbacks não estruturados. Sua tarefa é analisar o feedback e categorizar problemas para nossas equipes de produto e engenharia. Use estas categorias: Interface, Desempenho, Solicitação de Recurso, Integração, Preço e Outros. Também avalie o sentimento (Positivo/Neutro/Negativo) e a prioridade (Alta/Média/Baixa). Aqui está um exemplo:<exemplo>Entrada: O novo aplicativo de entrega é horrível! Demora uma eternidade para carregar o cardápio e não consigo encontrar o botão de rastreamento do pedido. Resolvam isso urgentemente!Categoria: Interface, DesempenhoSentimento: NegativoPrioridade: Alta</exemplo>Agora, analise este feedback: {{FEEDBACK}} |
| Assistente (resposta do Claude) | Aqui está a análise do feedback do cliente:Problema: InterfaceSentimento: NegativoPrioridade: AltaO cliente está frustrado com o novo design do aplicativo, que considera confuso e lento para carregar. Eles mencionam especificamente a falta do botão de "rastreamento", que provavelmente é um recurso crítico. Este problema precisa ser abordado com alta prioridade.Problema: IntegraçãoSentimento: PositivoPrioridade: MédiaO cliente está satisfeito com a integração existente com o iFood, mas gostaria de ver suporte para o Rappi também. Esta é uma solicitação de recurso que poderia ser considerada para uma atualização futura do produto.Problema: PreçoSentimento: NegativoPrioridade: AltaO cliente acha o preço das entregas irrazoável, especialmente para pedidos pequenos. Eles estão ameaçando mudar para um concorrente, então este é um problema de alta prioridade que precisa ser revisto.Problema: DesempenhoSentimento: NegativoPrioridade: AltaO cliente está enfrentando problemas críticos com o aplicativo móvel, onde pedidos grandes resultam consistentemente em travamentos. Este é um problema significativo que afeta a capacidade do cliente de usar o produto efetivamente e deve ser abordado como alta prioridade.Problema: Interface/AtendimentoSentimento: PositivoPrioridade: BaixaO cliente está geralmente satisfeito com o produto, elogiando especificamente a interface intuitiva e o excelente suporte ao cliente. Este é um feedback positivo que não requer ação imediata. |

# 3. Deixe o LLM pensar

O conceito de "chain of thought" (CoT) frequentemente melhora o desempenho dos modelos LLM em tarefas complexas como pesquisa, análise ou resolução de problemas. Isso ocorre quando você dá "espaço para ele pensar", encorajando-o a quebrar o problema em partes menores e considerar as nuances dos resultados.

---

O CoT é uma técnica poderosa, mas com grandes poderes vêm grandes responsabilidades.

Vantagens:
- Acurácia: resolver problemas passo a passo reduz a chance de erro, principalmente em tarefas complexas
- Coerência: quando estruturada e coesa, gera respostas mais organizadas
- Debugging: observar o LLM pensando separadamente sobre partes do prompt pode deixar mais claro quais partes estão nebulosas

Desvantagens:
- Mais custo de tokens
- Ganho marginal baixo: tarefas muito simples não precisam dessa estratégia; utilizá-la nesses casos pode gerar um ganho quase nulo para um custo muito alto

---

## CoT na prática:

Ele pode ser implementado em níveis de complexidade crescente:

### Básico: Inclua "Pense passo a passo" no seu prompt
**Exemplo:**
Calcule o lucro líquido de uma loja que teve receita bruta de R$50.000, custos operacionais de R$30.000 e impostos de 15% sobre o lucro bruto. Pense passo a passo.

### Intermediário: Mostre o passo a passo do "pensamento"
**Exemplo:**
Calcule o lucro líquido de uma loja que teve receita bruta de R$50.000, custos operacionais de R$30.000 e impostos de 15% sobre o lucro bruto. Siga estes passos:

Calcule o lucro bruto (receita bruta - custos operacionais)
Calcule o valor dos impostos (15% do lucro bruto)
Calcule o lucro líquido (lucro bruto - impostos)
Apresente o resultado final

### Avançado: Use tags de pensamento e resposta
**Exemplo:**
Calcule o lucro líquido de uma loja que teve receita bruta de R$50.000, custos operacionais de R$30.000 e impostos de 15% sobre o lucro bruto.
<pensamento>
1. Calcular o lucro bruto:
   Lucro bruto = Receita bruta - Custos operacionais
   Lucro bruto = R$50.000 - R$30.000 = R$20.000

Calcular o valor dos impostos:
Impostos = 15% do lucro bruto
Impostos = 0,15 * R$20.000 = R$3.000
Calcular o lucro líquido:
Lucro líquido = Lucro bruto - Impostos
Lucro líquido = R$20.000 - R$3.000 = R$17.000
</pensamento>


<resposta>
O lucro líquido da loja é de R$17.000.
</resposta>
```

# 4. Utilize tags XML

Quando o prompt tiver muitos componentes como contexto, instruções e exemplos, utilizar tags XML pode ser o "pulo do gato". Elas ajudam o LLM a separar as partes do seu prompt de maneira mais acurada, fornecendo respostas melhores.

Motivos para utilização:

- Clareza: separar bem as partes do seu prompt, deixando-o mais bem estruturado
- Acurácia: faz com que o modelo não entenda parte do seu prompt como coisas que não são (exemplo: confundir o texto com instrução de resposta)
- Flexibilidade: seu prompt fica mais fácil de modificar, tirando a tag, trocando-as de ordem, etc.
- Processamento: quando você utiliza tags XML, a resposta provavelmente também virá em tags, o que deixa o resultado mais fácil de processar via código

Melhores práticas:

- Consistência: utilize sempre os mesmos nomes de tag em todos os seus prompts, e quando quiser referenciar algo que está em uma tag, fale sobre aquele algo usando o mesmo nome que o nome da tag
- Hierarquia: use tags aninhadas se o seu conteúdo for hierárquico

Exemplo de um prompt estruturado com tags XML:

```xml
<contexto>
Você é um assistente virtual especializado em análise de dados de vendas para uma rede de livrarias no Brasil.
</contexto>

<dados>
Vendas do último trimestre:
- Loja A: R$ 150.000
- Loja B: R$ 200.000
- Loja C: R$ 180.000
</dados>

<tarefa>
Analise os dados de vendas e forneça um relatório sucinto com as seguintes informações:
1. Total de vendas da rede
2. Loja com melhor desempenho
3. Sugestão de meta para o próximo trimestre (aumento de 10% sobre o total)
</tarefa>

<formato_resposta>
Por favor, estruture sua resposta usando as seguintes tags:
<total_vendas>
<melhor_loja>
<meta_proxima>
<analise>
</formato_resposta>

<instrucoes_adicionais>
- Use valores em reais (R$)
- Arredonde os valores para o inteiro mais próximo
- Forneça uma breve análise de no máximo 3 frases
</instrucoes_adicionais>
```

# 5. Dê uma "persona" ao LLM

Ao utilizar um LLM, é possível aumentar sua performance de forma significativa utilizando o parâmetro "system". Essa técnica é conhecida como "Role prompting".

Vantagens:

- Melhora da acurácia: Em cenários complexos, dar uma persona pode melhorar muito a performance do modelo, pois a persona adequada gera uma aproximação semântica e contextual imediata com o problema sendo resolvido
- Adequação da linguagem: A persona correta irá produzir outputs na linguagem adequada
- Melhora do escopo: A persona correta tende a manter a resposta dentro dos limites esperados de resposta

Esse tipo de estratégia pode ser utilizada diretamente no parâmetro de "system" da API:

```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-3-5-sonnet-20240620",
    max_tokens=2048,
    system="Você é um cientista de dados experiente em uma empresa do Fortune 500.", # <-- role prompt
    messages=[
        {"role": "user", "content": "Analise este conjunto de dados em busca de anomalias: <dataset>{{DATASET}}</dataset>"}
    ]
)

print(response.content)
```
    
Mas também pode ser utilizado de maneira mais casual, diretamente no texto do prompt. Aqui está um exemplo:

Persona: Você é um especialista em marketing digital com 15 anos de experiência em campanhas para e-commerce no Brasil.

Tarefa: Analise os seguintes dados de uma campanha recente de marketing para uma loja online de eletrônicos:

<dados_campanha>
- Duração: 2 semanas
- Orçamento total: R$ 50.000
- Plataformas utilizadas: Facebook Ads, Google Ads, Instagram
- CTR médio: 2,5%
- Taxa de conversão: 3%
- ROI: 150%
</dados_campanha>

Com base nesses dados, por favor:
1. Avalie o desempenho geral da campanha
2. Identifique pontos fortes e fracos
3. Sugira 3 melhorias específicas para futuras campanhas

Formate sua resposta em tópicos claros e concisos, utilizando termos técnicos de marketing digital quando apropriado.

# 6. "Prefilling", ou Pré-preenchimento

Ao utilizar um modelo LLM, é possível guiar suas respostas dando uma "previsão" de sua resposta. Isso direciona a resposta do modelo para que ela:

1. Fique dentro dos formatos desejados
2. Pule descrições desnecessárias
3. Mantenha a "persona" enquanto o chat continua

Essa técnica pode ser utilizada diretamente na API:

```python
import anthropic

client = anthropic.Anthropic()
response = client.messages.create(
    model="claude-3-5-sonnet-20240620",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Qual é a sua cor favorita?"},
        {"role": "assistant", "content": "Como um assistente de IA, eu não tenho uma cor favorita, mas se eu tivesse que escolher, seria verde porque"}  # Pré-preenchimento aqui
    ]
)
```

A técnica também pode ser utilizada diretamente no chat. Aqui está um exemplo em português:

Usuário: Extraia o nome, tamanho, preço e cor desta descrição de produto como um objeto JSON:

<descricao>
O CasaInteligente Mini é um assistente doméstico compacto disponível nas cores preto ou branco por apenas R$ 299,90. Com apenas 13 cm de largura, ele permite que você controle luzes, termostatos e outros dispositivos conectados por voz ou aplicativo — não importa onde você o coloque em sua casa. Este pequeno hub acessível traz controle conveniente e sem as mãos para seus dispositivos inteligentes.
</descricao>

Assistente (pré-preenchimento): {
Resposta do Assistente:
{ "nome": "CasaInteligente Mini", "tamanho": "13 cm de largura", "preco": "R$ 299,90", "cor": ["preto", "branco"] }

Esse tipo de otimização se torna especialmente importante em cenários produtivos, onde a economia de tokens é crucial.

# 7. "Chaining" ou Encadeamento

Ao lidar com problemas complexos, modelos LLM podem se perder se receberem um prompt muito grande com muitas tarefas. O processo de encadeamento tem o poder de quebrar uma tarefa complexa em tarefas menores, mais fáceis de resolver.

Vantagens:

1. Acurácia: cada tarefa recebe atenção total do modelo, reduzindo os erros
2. Clareza: tarefas menores geram instruções mais claras e outputs mais precisos
3. Rastreabilidade: é mais fácil perceber erros isolados e corrigi-los

Quando utilizar?

- **Análise multi-etapas**: Veja os exemplos jurídicos e de negócios abaixo.
- **Pipelines de criação de conteúdo**: Pesquisa → Esquema → Rascunho → Edição → Formatação.
- **Processamento de dados**: Extração → Transformação → Análise → Visualização.
- **Tomada de decisão**: Coletar informações → Listar opções → Analisar cada uma → Recomendar.
- **Loops de verificação**: Gerar conteúdo → Revisar → Refinar → Re-revisar.

Como fazer?

1. Identificar subtarefas: quebre sua tarefa em tarefas distintas e menores
2. Utilize tags XML para passar outputs de um prompt para outro
3. Faça com que cada tarefa tenha um único retorno, com um objetivo claro
4. Itere: Refine as subtarefas baseado na qualidade das respostas do modelo

---

## Avançado: "Encadeamento auto-corretivo"

É possível encadear um prompt ao outro para fazer com que o modelo revise a própria resposta. Aqui está um exemplo adaptado ao contexto brasileiro:

### Prompt 1

**Usuário**: Resuma este artigo sobre uma nova política pública de saúde. <artigo>{{ARTIGO_POLITICA_SAUDE}}</artigo> Foque na metodologia, resultados e implicações para o SUS.

**Assistente**: [O assistente fornece um resumo do artigo]

### Prompt 2

**Usuário**: Sua tarefa é fornecer feedback sobre um resumo de artigo de política pública de saúde. Aqui está o resumo: <resumo>{{RESUMO}}</resumo> Aqui está o artigo original: <artigo>{{ARTIGO_POLITICA_SAUDE}}</artigo> Revise este resumo quanto à precisão, clareza e completude em uma escala de A a F.

**Assistente**: [O assistente fornece feedback detalhado sobre o resumo]

### Prompt 3

**Usuário**: Sua tarefa é melhorar um resumo de artigo com base no feedback recebido. Aqui está o primeiro rascunho do resumo: <resumo>{{RESUMO}}</resumo> Aqui está o artigo original: <artigo>{{ARTIGO_POLITICA_SAUDE}}</artigo> Aqui está o feedback: <feedback>{{FEEDBACK}}</feedback> Atualize o resumo com base no feedback.

**Assistente**: [O assistente fornece uma versão atualizada e melhorada do resumo]

---

## Exemplo de Encadeamento (CoT)

Vamos ver um exemplo de encadeamento aplicado a um contexto empresarial brasileiro:

### Prompt 1

**Usuário**: Você é nosso Diretor Jurídico. Revise este contrato de fornecimento de software como serviço (SaaS) para riscos, focando em privacidade de dados, SLAs e limites de responsabilidade. <contrato>{{CONTRATO}}</contrato> Apresente suas conclusões em tags <riscos>.

**Assistente**: [O assistente fornece uma análise detalhada dos riscos do contrato]

### Prompt 2

**Usuário**: Redija um e-mail para o fornecedor de SaaS destacando as seguintes preocupações e propondo mudanças. Aqui estão as preocupações: <preocupacoes>{{PREOCUPACOES}}</preocupacoes>

**Assistente**: [O assistente redige um e-mail detalhando as preocupações e propondo mudanças]

### Prompt 3

**Usuário**: Sua tarefa é revisar um e-mail e fornecer feedback. Aqui está o e-mail: <email>{{EMAIL}}</email> Dê feedback sobre o tom, clareza e profissionalismo.

**Assistente**: [O assistente fornece feedback detalhado sobre o e-mail]

Neste exemplo, o pensamento encadeado foi utilizado para que o modelo primeiro identificasse os riscos no contrato, em seguida redigisse um e-mail propondo as mudanças a serem feitas nesse contrato, e por último, revisasse o tom e a formalidade deste e-mail. Em testes onde as tarefas são pedidas todas juntas, o modelo frequentemente esquece algumas delas e acaba não propondo mudanças de contrato.
    
# 8. Dicas para Prompts Extensos
Ao trabalhar com prompts que contêm muitas informações, considere estas estratégias para melhorar os resultados:

- 1. Priorize os Dados
Por quê? As IAs de linguagem atuais tendem a processar melhor as informações colocadas no início do prompt.
Como fazer: Coloque os dados mais importantes ou extensos no topo do seu prompt, seguidos pelas instruções ou perguntas.
Benefício: Aumenta a precisão da resposta em até 30%.

- 2. Estruture com XML
Por quê? Organizar as informações em uma estrutura clara ajuda a IA a entender e processar melhor os dados.
Como fazer: Use tags XML para separar e identificar diferentes partes do seu prompt, especialmente ao lidar com múltiplos documentos ou fontes de dados.
Exemplo:

```xml
<documentos>
  <documento indice="1">
    <fonte>relatorio_anual_2023.pdf</fonte>
    <conteudo>
      {{CONTEÚDO_DO_RELATÓRIO_ANUAL}}
    </conteudo>
  </documento>
  <documento indice="2">
    <fonte>analise_concorrentes_2trim.xlsx</fonte>
    <conteudo>
      {{CONTEÚDO_DA_ANÁLISE_DE_CONCORRENTES}}
    </conteudo>
  </documento>
</documentos>
```

Analise o relatório anual e a análise de concorrentes. Identifique vantagens estratégicas e recomende áreas de foco para o 3º trimestre.


# 9. Dica “Bônus”

Nesse link, é possível encontrar uma grande diversidade de exemplos de prompts bem estruturados, seguindo muitas das práticas aqui descritas, eles podem ser utilizados de inspiração para seus prompts:

https://docs.anthropic.com/en/prompt-library/library
