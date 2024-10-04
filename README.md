# Cheaset Prática de Prompt Engineering

Antes de utilizar essa cheaset, vale uma observação sobre técnicas de Prompt Engineering. Nem todos os desafios envolvendo LLM's podem ser resolvidos apenas com ajuste de prompts - alguns desafions como modelos com baixa aderência a uma língua incomun,  latência na geração de reposta, etc. Requerem mudanças mais "sistemáticas". Porém, esse tipo de desafio é mais incomun, e na maioria dos casos, técnicas Prompt Engineering oferece uma solução rápida, eficaz e de fácil manutenção, que aproveita a estrutura existente.

Importante: É fundamental que você possa medir e comprovar a melhoria do desempenho do modelo após cada ajuste feito com técnicas de Prompt Engineering. Tendo em vista a natureza heurística dos LLM's, onde não necessesariamente técnicas mais complexas geram resultados melhores.

Essa lista de ações é baseada no manual de prompt engineering da anthropic, e estão organizadas por ordem de importância. Para tarefas de menor prioridade ou não destinadas à produção, os primeiros passos geralmente são suficientes:
    
# 1. Seja claro, direto e detalhado

Trate todo modelo de LLM como um funcionário brilhante, mas que foi recém-contratado. Ele não conhece as normas, estilos, guias ou preferências da empresa.

- Diga para que o resultado será utilizado.
- Para quem ele será fornecido.
- O que caracteriza um resultado "bem-sucedido" dessa tarefa.
- Limite o escopo da resposta (Se você quer apenas código, diga)
- Forneça a sequência (ordem dos passos)

Exemplos:
--
**Prompt Vago**:
```markdown
Por favor, resuma estes comentários de clientes sobre nossa nova linha de produtos naturais: {{DADOS_COMENTARIOS}}
```

**Resposta (Prompt Vago)**:
```markdown
Aqui está um resumo dos comentários dos clientes:
- Muitos elogios ao xampu de coco
- Alguns acharam o hidratante facial caro
- O sabonete de lavanda teve opiniões mistas
- No geral, os clientes gostaram da iniciativa de produtos naturais
```


**Prompt Claro**: 
```markdown
Sua tarefa é analisar os comentários dos clientes sobre nossa nova linha de produtos naturais para uma apresentação à diretoria. Instruções:

1. Identifique os 3 produtos mais mencionados.
2. Liste os principais pontos positivos e negativos de cada produto.
3. Sugira 2 melhorias para cada produto com base nos comentários.
4. Resuma o sentimento geral em uma frase.
5. Mantenha o tom profissional e objetivo.
6. Limite a resposta a 300 palavras.

Dados para analisar: {{DADOS_COMENTARIOS}}
```

**Resposta (Prompt Clato)**:
```markdown
1. Produtos mais mencionados:
a) Xampu de coco
b) Hidratante facial
c) Sabonete de lavanda

2. Principais pontos:
Xampu de coco:
  Positivos: Cheiro agradável, deixa o cabelo macio<
  Negativos: Embalagem pouco prática

Hidratante facial:
  Positivos: Textura leve, boa absorção
  Negativos: Preço elevado, pouca duração

Sabonete de lavanda:
  Positivos: Aroma relaxante, não resseca a pele
  Negativos: Espuma insuficiente

3. Sugestões de melhoria:
Xampu de coco: Redesenhar embalagem, aumentar poder de limpeza   Hidratante facial: Oferecer versão econômica, aumentar tamanho.  Sabonete de lavanda: Melhorar fórmula para mais espuma, intensificar aroma

4. Sentimento geral: Clientes estão entusiasmados com a linha natural, mas esperam ajustes em alguns produtos para justificar o investimento
```


É possível notar que o prompt 2 foi melhor. Pois:

- Gerou mais ajuste da resposta com o público alvo do resultado.
- Forneceu os passos sequencialmente.
- Fechou o escopo da resposta dizendo que queria uma análise limitada a 300 palavras.

# 2. Use exemplos (Multishot prompting)

Fornecendo exemplos bem montados de como você quer sua resposta, a necessidade de muito texto explicativo é dramaticamente reduzida, e a performance do modelo melhora.

Seus exemplos devem ser:

- Relevantes: Refletem a realidade dos casos de uso que você irá ver
- Diversos: Evitam que modelos peguem padrões não desejados, e "edge cases" para que o modelo não simplifique demais a lógica
- Claros: Deixe os exemplos envolvidos por tags de exemplo:

  ```markdown
  <exemplo>Exemplo de resposta do modelo</exemplo>
  ```


Exemplos
--
**Prompt Sem Exemplos**: 
```markdown
Analise o feedback do cliente e classifique os problemas. Use estas categorias: 

UI/UX, Desempenho, Solicitação de recurso, Integração, Preços e Outros. 

Avalie também o sentimento (Positivo/Neutro/Negativo) e a prioridade (Alta/Média/Baixa).

Aqui está o feedback: {{FEEDBACK}}
```

**Prompt Com Exemplos**:
```markdown
Nossa equipe de CS está sobrecarregada com feedback não estruturado. Sua tarefa é analisar feedback e categorizar problemas para nossas equipes de produto e engenharia. Use estas categorias: UI/UX, Desempenho, Solicitação de recurso, Integração, Preços e Outros. Avalie também o sentimento (Positivo/Neutro/Negativo) e a prioridade (Alta/Média/Baixa). Aqui está um exemplo:

<exemplo>
Entrada: O novo painel está uma bagunça! Demora uma eternidade para carregar e não consigo encontrar o botão de exportação. Corrija isso o mais rápido possível!
Categoria: UI/UX, Desempenho
Sentimento: Negativo
Prioridade: alta
</exemplo>

Agora, analise este feedback: {{FEEDBACK}}
```

**Resposta (Prompt Sem Exemplos)**: 
```markdown
Aqui está a análise do feedback do cliente:

Problema: UI/UX
Sentimento: Negativo
Prioridade: Alta
O cliente está frustrado com o novo design do painel, que considera confuso e lento para carregar. Eles mencionam especificamente o botão “exportar” ausente, o que provavelmente é um recurso crítico. Esta questão precisa ser abordada como uma alta prioridade.

Problema: Integração
Sentimento: Positivo
Prioridade: Média
O cliente está satisfeito com a integração existente do Salesforce, mas também gostaria de ver suporte para o Hubspot. Esta é uma solicitação de recurso que pode ser considerada para uma atualização futura do produto.

Problema: preços
Sentimento: Negativo
Prioridade: Alta
O cliente considera o preço irracional, especialmente para os recursos básicos. Eles estão ameaçando mudar para um concorrente, então esta é uma questão de alta prioridade que precisa ser revista.

Problema: Desempenho
Sentimento Negativo
Prioridade: Alta
O cliente está enfrentando problemas críticos com o aplicativo móvel, onde uploads de arquivos grandes resultam consistentemente em falhas. Este é um problema significativo que afeta a capacidade do cliente de usar o produto de forma eficaz e deve ser tratado com alta prioridade.

Problema: UI/UX
Sentimento: Positivo
Prioridade: Baixa
O cliente está globalmente satisfeito com o produto, destacando especificamente a interface intuitiva e o excelente suporte ao cliente. Este é um feedback positivo que não requer nenhuma ação imediata.
```

**Resposta (Prompt Com Exemplos)**: 
```markdown
Aqui está a análise do feedback do cliente:

1. O novo painel está uma bagunça! Demora uma eternidade para carregar e não consigo encontrar o botão de exportação. Corrija isso o mais rápido possível!
Categoria: UI/UX, Desempenho
Sentimento: Negativo
Prioridade: Alta

2. Adorei a integração do Salesforce! Mas seria ótimo se você pudesse adicionar o Hubspot também.
Categoria: Integração, Solicitação de Recursos
Sentimento: Positivo
Prioridade: Média

3. Seu preço é ridículo! US$ 99/mês para recursos básicos? Estou mudando para o Concorrente X.
Categoria: Preços
Sentimento: Negativo
Prioridade: Alta

4. O aplicativo móvel continua travando quando tento fazer upload de arquivos grandes (>50 MB). Isso é fundamental para nossos agentes de campo.
Categoria: Desempenho
Sentimento: Negativo
Prioridade: Alta

5. No geral, estou satisfeito com o produto. A IU é intuitiva e o suporte ao cliente é excelente. Mantem!
Categoria: UI/UX, Outros (Suporte ao Cliente)
Sentimento: Positivo
Prioridade: Baixa

```

Observe que na versão “sem exemplos”, o modelo não lista múltiplas opções para cada categoria, apesar de sua análise escrita indicar que existem múltiplas categorias para determinado feedback. Ele também adiciona uma explicação desnecessária.

# 3. Deixe o LLM pensar

O conceito de "chain of thought" (CoT) frequentemente melhora o desempenho dos modelos LLM em tarefas complexas como pesquisa, análise ou resolução de problemas. Isso ocorre quando você dá "espaço para ele pensar", encorajando-o a quebrar o problema em partes menores e considerar as nuances dos resultados.

O CoT é uma técnica poderosa, mas precisa ser utilizada com alguma ponderações

Vantagens:
- Acurácia: resolver problemas passo a passo reduz a chance de erro, principalmente em tarefas complexas
- Coerência: quando estruturada e coesa, gera respostas mais organizadas
- Debugging: observar o LLM pensando separadamente sobre partes do prompt pode deixar mais claro quais partes estão nebulosas

Desvantagens:
- Mais custo de tokens
- Ganho marginal baixo: tarefas muito simples não precisam dessa estratégia; utilizá-la nesses casos pode gerar um ganho quase nulo para um custo muito alto

## CoT na prática:

Ele pode ser implementado em níveis de complexidade crescente

### Básico: Inclua "Pense passo a passo" no seu prompt
**Exemplo:**

```markdown
Calcule o lucro líquido de uma loja que teve receita bruta de R$50.000, custos operacionais de R$30.000 e impostos de 15% sobre o lucro bruto. Pense passo a passo.
```

### Intermediário: Mostre o passo a passo do "pensamento"
**Exemplo:**
```markdown
Calcule o lucro líquido de uma loja que teve receita bruta de R$50.000, custos operacionais de R$30.000 e impostos de 15% sobre o lucro bruto. Siga estes passos:

1. Calcule o lucro bruto (receita bruta - custos operacionais)
2. Calcule o valor dos impostos (15% do lucro bruto)
3. Calcule o lucro líquido (lucro bruto - impostos)
4. Apresente o resultado final
```

### Avançado: Diga para o modelo utilizar tags de "pensamento"
**Exemplo:**

```markdown
Elabore e-mails personalizados para doadores solicitando contribuições para o programa Care for Kids deste ano.

Informações do programa:
<programa>
{{PROGRAM_DETAILS}}
</programa>

Informações do doador:
<doador>
{{DONOR_DETAILS}}
</doador>

Pense antes de escrever o e-mail, em tags de <pensamento>. Primeiro, pense em quais mensagens podem atrair esse doador, considerando seu histórico de doações e quais campanhas ele apoiou no passado. Em seguida, pense em quais aspectos do programa Care for Kids os atrairiam, dada a sua história. Por fim, escreva o e-mail personalizado do doador em tags <email>
```

O uso de tags de pensamento força o modelo a "enxergar" complexidade adicional no problema antes de gerar a resposta.
# 4. Utilize tags XML

Quando o prompt tiver muitos componentes como contexto, instruções, e exemplos, utilizar tags XML podem ser o diferencial do seu prompt. Elas ajudam o LLM a separar as partes do seu prompt de maneira mais acurada, fornecendo respostas melhores.

Motivos para utilização:

- Clareza: separar bem as partes do seu prompt, deixando-o mais bem estruturado
- Acurácia: faz com que o modelo não entenda partes do seu prompt como coisas que não são (exemplo: confundir o texto com instruções com o texto de exemplo)
- Flexibilidade: seu prompt fica mais fácil de modificar, tirando tags, trocando-as de ordem, etc.
- Processamento: Ao utilizar tags XML, o texto fica mais fácil de ser processo via código, facilitando automações

Melhores práticas:

- Consistência: utilize sempre os mesmos nomes de tag em todos os seus prompts, ao referenciar algo que está em uma tag, diga o nome diretamente.
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
- Melhora do escopo: A persona correta tende a manter a resposta dentro dos limites esperados

Esse tipo de estratégia pode ser utilizada diretamente no parâmetro de "system" da API:

```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-3-5-sonnet-20240620",
    max_tokens=2048,
    system="Você é um cientista de dados experiente em uma empresa listada na Bolsa", # <-- role prompt
    messages=[
        {"role": "user", "content": "Analise este conjunto de dados em busca de anomalias: <dataset>{{DATASET}}</dataset>"}
    ]
)

print(response.content)
```
    
Essa técnica também pode ser utilizada diretamente no prompt. Aqui está um exemplo:

```markdown
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
```

# 6. "Prefilling", ou Pré-preenchimento

Ao utilizar um modelo LLM, é possível guiar suas respostas dando uma "previsão" de sua resposta. Isso direciona a resposta do modelo para que ela:

1. Fique dentro dos formatos desejados
2. Pule descrições desnecessárias

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

A técnica também pode ser utilizada diretamente no chat. Aqui está um exemplo:

**Prompt com pré preenchimento:**
```markdown
Usuário: Extraia o nome, tamanho, preço e cor desta descrição de produto como um objeto JSON:

<descricao>
O CasaInteligente Mini é um assistente doméstico compacto disponível nas cores preto ou branco por apenas R$ 299,90. Com apenas 13 cm de largura, ele permite que você controle luzes, termostatos e outros dispositivos conectados por voz ou aplicativo — não importa onde você o coloque em sua casa. Este pequeno hub acessível traz controle conveniente e sem as mãos para seus dispositivos inteligentes.
</descricao>

Assistente (pré-preenchimento): {
```


**Resposta ao prompt com pré preenchimento:**
```markdown
{ "nome": "CasaInteligente Mini", "tamanho": "13 cm de largura", "preco": "R$ 299,90", "cor": ["preto", "branco"] }
```

Esse tipo de otimização é muito importante em cenários produtivos, onde a economia de tokens é crucial.

# 7. "Chaining" ou Encadeamento

Ao lidar com problemas complexos, modelos LLM podem se perder se receberem um prompt muito grande, com muitas tarefas. O processo de encadeamento tem o poder de quebrar uma tarefa complexa em tarefas menores, mais fáceis de resolver.

Vantagens:

1. Acurácia: cada tarefa recebe atenção total do modelo, reduzindo os erros.
2. Clareza: tarefas menores geram instruções mais claras e outputs mais precisos.
3. Rastreabilidade: é mais fácil perceber erros isolados na reposta do modelo e corrigi-los (melhora do prompt).

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



## Exemplo de Encadeamento (CoT)

Vamos ver um exemplo de encadeamento aplicado a um contexto empresarial complexo, de análise contratual:

### Prompt 1
**Usuário**: 
```
Você é nosso Diretor Jurídico. Revise este contrato de fornecimento de software como serviço (SaaS) para riscos, focando em privacidade de dados, SLAs e limites de responsabilidade. 

<contrato>[CONTRATO]</contrato> 

Apresente suas conclusões em tags <riscos>.
```

**Assistente**: 
```
[O assistente fornece uma análise detalhada dos riscos do contrato]
```

### Prompt 2

**Usuário**: 
```
Redija um e-mail para o fornecedor de SaaS destacando as seguintes preocupações e propondo mudanças. 

Aqui estão as preocupações: 

<preocupacoes>[RESPOSTA DO MODELO ANTERIOR]</preocupacoes>
```

**Assistente**: 
```
[O assistente redige um e-mail detalhando as preocupações e propondo mudanças]
```

### Prompt 3
**Usuário**: 
```
Sua tarefa é revisar um e-mail e fornecer feedback. 

Aqui está o e-mail: 
<email>[RESPOSTA DO MODELO ANTERIOR]</email>

Dê feedback sobre o tom, clareza e profissionalismo.
```

**Assistente**: 
```
[O assistente fornece feedback detalhado sobre o e-mail]
```

Neste exemplo, o pensamento encadeado foi utilizado para que o modelo primeiro identificasse os riscos no contrato, em seguida redigisse um e-mail propondo as mudanças a serem feitas nesse contrato, e por último, revisasse o tom e a formalidade deste e-mail. Em testes onde as tarefas são pedidas todas juntas, o modelo frequentemente esquece algum dos passos.


## Avançado: "Encadeamento auto-corretivo"
É possível encadear um prompt ao outro para fazer com que o modelo revise a própria resposta. Aqui está um exemplo adaptado ao contexto brasileiro:

### Prompt 1
```
Resuma este artigo sobre uma nova política pública de saúde. 

<artigo>{{ARTIGO_POLITICA_SAUDE}}</artigo> 

Foque na metodologia, resultados e implicações para o SUS.
```

**Assistente**: 
```
[O assistente fornece um resumo do artigo]
```

### Prompt 2
```
Sua tarefa é fornecer feedback sobre um resumo de artigo de política pública de saúde. 

Aqui está o resumo: 
<resumo>
{{RESUMO}}
</resumo> 

Aqui está o artigo original: 
<artigo>
{{RESPOSTA DO MODELO ANTERIOR}}
</artigo> 

Revise este resumo quanto à precisão, clareza e completude em uma escala de A a F.
```

**Assistente**:
```
[O assistente fornece feedback detalhado sobre o resumo]
```

### Prompt 3
```

Sua tarefa é melhorar um resumo de artigo com base no feedback recebido. 

Aqui está o primeiro rascunho do resumo: 
<resumo>{{RESUMO}}</resumo>

Aqui está o artigo original: 
<artigo>{{ARTIGO_POLITICA_SAUDE}}</artigo> 

Aqui está o feedback: <feedback>{{RESPOSTA DO MODELO ANTERIOR}}</feedback> 

Atualize o resumo com base no feedback.
```

**Assistente**: 
```
[O assistente fornece uma versão atualizada e melhorada do resumo]
```

    
# 8. Dicas para Prompts Extensos
Ao trabalhar com prompts que contêm muitas informações, considere estas estratégias:

1. Priorize os Dados: Até a data de hoje (03/10/2024) LLM's de linguagem tendem a processar melhor as informações colocadas no início do prompt.
2. Estruture com XML: Especialmente em prompts grandes organizar as informações em uma estrutura clara ajuda o modelo a entender e processar melhor os dados. Uma estrutura hierarquica de tags XML também é preferível.

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

<tarefa>
Analise o relatório anual e a análise de concorrentes. Identifique vantagens estratégicas e recomende áreas de foco para o 3º trimestre.
</tarefa>
```

# 9. Dica “Bônus”

Nesse link, é possível encontrar uma grande diversidade de exemplos de prompts bem estruturados, seguindo muitas das práticas aqui descritas, eles podem ser utilizados de inspiração para seus prompts:

https://docs.anthropic.com/en/prompt-library/library


**Referências:**
- https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
