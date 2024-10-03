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

# 3. Use exemplos (Multishot prompting)

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

# 4. Deixe o LLM pensar

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
Estes exemplos demonstram como o CoT pode ser aplicado em diferentes níveis de complexidade, permitindo que o LLM "pense" de forma mais estruturada e transparente.

**Exemplo:**
- 5. Utilize tags XML
    
    Quando o prompt tiver muitos componentes como contexto instruções e exemplos, utilizar testes ele pode ser ”pulo do gato”. Elas ajudam o LLM a separar as partes do seu prompt de maneira mais acurada fornecendo respostas melhores.
    
    Motivos para utlização:
    
    - Clareza: separar bem as partes do seu prompt deixando ele mais bem estruturado
    - Acurácia: faz com que o modelo não entenda parte do seu prompt como coisas que não são (exemplo: confundir com o texto com instrução de resposta)
    - Flexibilidade: seu prompt fica mais fácil de modificar tirando a tag trocando elas de ordem, etc.
    - Processamento: quando você utiliza tegs xml, a resposta provavelmente também virá em tags, que deixa o resultado mais fácil de processar via código.
    
    Melhores práticas:
    
    - Consistência: utilize sempre os mesmos nomes de tag em todos os seus prompts, e quando quiser referenciar algo que está em uma tag fale sobre aquele algo usando o mesmo nome que o nome da tag.
    - Hierarquia: use tags aninhadas se o seu conteúdo for hierárquico
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea3d8b7a-af83-45a7-ae17-6fe5ca8df043/7d886b2b-8339-423a-a09b-3860e8c08674/image.png)
    
- 6. De uma “persona” ao LLM
    
    Ao utilizar um LLM, é possível aumentar sua performance de forma aguda utilizando o parâmetro “system”, essa técninca é conhecida como “Role prompting”
    
    Vantagens:
    
    - Melhora da acurácia: Em cenário complexos, dar uma persona pode melhorar muito a performance do modelo, pois a persona adequada gera uma aproximação semântica e contextual imediata com o problema sendo resolvido
    - Adequação do linguagar:  A persona correta irá produir otputs no lingugar adequado.
    - Melhora do scopo: A persona correta tende a manter a resposta dentro dos limites esperados de resposta.
    
    Esse tipo de estratégia pode ser utilizada diretamente no parâmetro de “system” da API
    
    ```python
    import anthropic
    
    client = anthropic.Anthropic()
    
    response = client.messages.create(
        model="claude-3-5-sonnet-20240620",
        max_tokens=2048,
        system="You are a seasoned data scientist at a Fortune 500 company.", # <-- role prompt
        messages=[
            {"role": "user", "content": "Analyze this dataset for anomalies: <dataset>{{DATASET}}</dataset>"}
        ]
    )
    
    print(response.content)
    ```
    
    Mas também pode ser utilizado de maneira mais casual, diretamente no texto do prompt
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea3d8b7a-af83-45a7-ae17-6fe5ca8df043/47b27dd1-a4e7-43de-907d-855357ca6305/image.png)
    
- 7. “Prefilling”, ou Pré-preenchimento
    
    Ao utilizar um modelo LLM, é possível guiar suas respostas dando uma “Previsão” de sua resposta, isso enviesa a resposta do modelo para que ela:
    
    1. Fique dentro dos formatos desejados
    2. Pule descrições desnecessárias
    3. Mantenha a “persona” enquanto o chat continua
    
    Essa técnica pode ser utilizada diretamente na API
    
    ```python
    import anthropic
    
    client = anthropic.Anthropic()
    response = client.messages.create(
        model="claude-3-5-sonnet-20240620",
        max_tokens=1024,
        messages=[
            {"role": "user", "content": "What is your favorite color?"},
            {"role": "assistant", "content": "As an AI assistant, I don't have a favorite color, But if I had to pick, it would be green because"}  # Prefill here
        ]
    )
    ```
    
    A técnica também pode ser utilizada diretamente no chat:
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/ea3d8b7a-af83-45a7-ae17-6fe5ca8df043/caf7ac55-bf29-495e-b27c-a292c479f33b/image.png)
    
    Esse tipo de otimização se torna especialmente importante em cenários produtivos, onde a economia de tokens é crucial.
    
- 8. “Chaining” ou, encadeamento
    
    Ao lidar com problemas complexos, modelos LLM’s podem acabar se perdendo se receberem um prompt muito grande com muitas tarefas. O processo de encadeamento, tem o poder de quebrar uma tarefa complexa em tarefas menores, mais fáceis de resolver.
    
    Vantagens:
    
    1. Acurácia: cada tarefa atenção total do modelo, reduzindo os erros
    2. Clareza: tarefas menores geram instruções mais claras e outputs mais claros
    3. Traceability: é mais fácil perceber erros isolados e consertá-los
    
    Quando utilizar?
    
    - **Multi-step analysis**: See the legal and business examples below.
    - **Content creation pipelines**: Research → Outline → Draft → Edit → Format.
    - **Data processing**: Extract → Transform → Analyze → Visualize.
    - **Decision-making**: Gather info → List options → Analyze each → Recommend.
    - **Verification loops**: Generate content → Review → Refine → Re-review.
    
    Como fazer?
    
    1. Identificar sub tarefas: quebre sua tarefa, em tarefas distintas e menores
    2. Utilize tags XML para passar outputs de um prompt para outro
    3. De cada tarefa tenha um único retorno, com um objetivo claro
    4. Itere: Refine sub tarefas baseado na qualidade das respostas do modelo
    
    ---
    
    - Avançado: “Encadeamento auto-corretivo”
        
        É possível encadear um prompt ao outro para fazer com que o modelo revise a própria resposta, por exemplo:
        
        ### **Prompt 1**
        
        | **Role** | **Content** |
        | --- | --- |
        | User | Summarize this medical research paper.<paper>{{RESEARCH_PAPER}}</paper>Focus on methodology, findings, and clinical implications. |
        | Assistant{{SUMMARY}} | Methodology: Double-blind RCT with 500 Type 2 diabetes patients. Group A (n=250) received novel GLP-1/GIP dual agonist; Group B (n=250) received standard GLP-1 agonist. 24-week trial, primary endpoint: HbA1c reduction.Findings:- Group A: 1.8% mean HbA1c reduction- Group B: 1.1% mean HbA1c reduction- 68% of Group A vs. 52% of Group B achieved HbA1c <7%- Group A: 5.2kg mean weight loss- Group B: 3.1kg mean weight lossSafety: Similar adverse event profiles, no serious events attributed to treatment.Clinical Implications: Dual agonist shows superior glycemic control and weight loss compared to standard therapy. Could be a game-changer, especially for obese diabetics. |
        
        ### **Prompt 2**
        
        | **Role** | **Content** |
        | --- | --- |
        | User | Your task is to provide feedback on a research paper summary. Here is a summary of a medical research paper:<summary>{{SUMMARY}}</summary>Here is the research paper:<paper>{{RESEARCH_PAPER}}</paper>Review this summary for accuracy, clarity, and completeness on a graded A-F scale. |
        | Assistant{{FEEDBACK}} | 1. Accuracy: A- Correctly reported key stats: HbA1c reductions, weight loss.- Accurately noted safety profile and lack of serious adverse events.- Assertion of “game-changer” is justified by significantly better outcomes.2. Clarity: A-- Concise and well-structured with clear sections.- Good use of comparative language (e.g., “up 3% from Q1”) for context.- Minor issue: Acronyms like “RCT,” “GLP-1,” and “GIP” not defined. Consider spelling out first use.3. Completeness: B+- Covers all requested areas: methodology, findings, clinical implications.- Missing: Baseline characteristics of groups (were they well-matched?).- Missing: Any secondary endpoints (e.g., blood pressure, lipids).- Consider: Long-term implications or need for further research.Overall, the summary is accurate and clear, providing key insights for a quick grasp of the study. To elevate to A+, define acronyms, mention group baseline stats, note any secondary outcomes, and briefly touch on the need for long-term studies. |
        
        ### **Prompt 3**
        
        | **Role** | **Content** |
        | --- | --- |
        | User | Your task is to improve a paper summary given feedback. Here is the first draft of a medical research paper:<summary>{{SUMMARY}}</summary>Here is the research paper:<paper>{{RESEARCH_PAPER}}</paper>Here is the feedback:<feedback>{{FEEDBACK}}</feedback>Update the summary based on the feedback. |
        | Assistant | Methodology: Double-blind randomized controlled trial (RCT) with 500 Type 2 diabetes patients. Group A (n=250) received a novel glucagon-like peptide-1/glucose-dependent insulinotropic polypeptide (GLP-1/GIP) dual agonist; Group B (n=250) received a standard GLP-1 agonist. The 24-week trial’s primary endpoint was HbA1c reduction. Groups were well-matched for age, BMI, and baseline HbA1c.Findings:- Group A: 1.8% mean HbA1c reduction- Group B: 1.1% mean HbA1c reduction- 68% of Group A vs. 52% of Group B achieved HbA1c <7%- Group A: 5.2kg mean weight loss- Group B: 3.1kg mean weight lossSecondary endpoints showed improvements in blood pressure and lipid profiles for both groups, with Group A showing slightly better results.Safety: Similar adverse event profiles, with no serious events attributed to either treatment.Clinical Implications: The dual GLP-1/GIP agonist demonstrates superior glycemic control and weight loss compared to standard GLP-1 therapy. These results suggest it could be a significant advancement, particularly for obese diabetics. However, longer-term studies are needed to confirm sustained efficacy and safety, as well as to investigate potential benefits on cardiovascular outcomes. |
    
    ---
    
    Exemplo de Encadeamento (CoT)
    
    Note that Claude misses the instruction to provide proposed changes in its email draft.
    
    ### **Prompt 1**
    
    | **Role** | **Content** |
    | --- | --- |
    | User | You’re our Chief Legal Officer. Review this SaaS contract for risks, focusing on data privacy, SLAs, and liability caps.<contract>{{CONTRACT}}</contract>Output your findings in <risks> tags. |
    | Assistant{{CONCERNS}} | 1. Data Privacy (Section 7):- Issue: Vendor has broad rights to use client data for “service improvement.”- Risk: Could lead to unauthorized data sharing or monetization of client data.- Impact: Potential breach of client trust, regulatory fines (GDPR, CCPA).2. SLAs (Section 5):- Issue: 99.5% uptime guarantee is below industry standard (typically 99.99%).- Risk: Up to 43.8 hours of downtime per year vs. 52.6 minutes at 99.99%.- Impact: Operational disruptions, revenue loss, client dissatisfaction.3. Liability (Section 9):- Issue: Liability cap set at 3 months of fees.- Risk: Inadequate for potential data breaches, IP theft, or prolonged service failures.- Impact: Insufficient compensation for damages that could far exceed the cap, like class-action lawsuits or regulatory fines. |
    
    ### **Prompt 2**
    
    | **Role** | **Content** |
    | --- | --- |
    | User | Draft an email to a SaaS product vendor outlining the following concerns and proposing changes. Here are the concerns:<concerns>{{CONCERNS}}</concerns> |
    | Assistant{{EMAIL}} | Dear {{VENDOR}},After a thorough review of the proposed SaaS agreement, our legal team has identified several high-priority concerns that must be addressed before we can proceed:1. Data Privacy (Section 7):- Current: Broad rights to use client data for “service improvement.”- Concern: Potential unauthorized data sharing or monetization.- Proposed Change: Limit data use to direct service provision. Any use for improvement, analytics, or marketing must be opt-in, revocable, and anonymized.2. SLAs (Section 5):- Current: 99.5% uptime guarantee.- Concern: Up to 43.8 hours of downtime per year, risking operational disruptions.- Proposed Change: 99.99% uptime with tiered service credits. E.g., 10% credit for <99.99%, 25% for <99.9%, 50% for <99.5%.3. Liability (Section 9):- Current: Cap at 3 months of fees.- Concern: Grossly inadequate for data breaches, IP theft, or prolonged outages.- Proposed Change: 12 months’ fees for general liability. Uncapped for data breaches, willful misconduct, IP infringement.These changes are non-negotiable for us. They align with industry standards and our commitment to data security, operational excellence, and fiduciary responsibility. We value the potential of our partnership, and addressing these will set a strong foundation.Please have your legal team review and incorporate these changes. We’re happy to discuss further to ensure mutual understanding and agreement.Best regards,{{CLO_NAME}} |
    
    ### **Prompt 3**
    
    | **Role** | **Content** |
    | --- | --- |
    | User | Your task is to review an email and provide feedback. Here is the email:<email>{{EMAIL}}</email>Give feedback on tone, clarity, and professionalism. |
    | Assistant | 1. Tone:- Professional and assertive without being confrontational.- Use of “non-negotiable” is strong but justified given the criticality of the issues.- Collaborative language (“we value,” “happy to discuss”) balances the firmness, maintaining a constructive tone.2. Clarity:- Excellent structuring: Current state, concern, and proposed change for each issue.- Technical terms (e.g., “EBITDA,” “tiered service credits”) are appropriate for a B2B SaaS context.- Action items are clear: vendor’s legal team to review and incorporate changes.3. Professionalism:- Focuses on mutual benefit and industry standards, not just our demands.- Mentions broader responsibilities (data security, operational excellence, fiduciary duty), elevating the discussion.- Offers further discussion, showing commitment to understanding and resolving issues.Overall, the email strikes the right balance of assertiveness and collaboration. It clearly articulates our concerns, proposes specific solutions, and maintains a professional, partnership-oriented tone. No revisions needed; proceed with sending. |
    
    Nesse exemplo, o pensamento encadeado foi utilizado para que o modelo primeiro encontra-se os riscos no contrato em sequência a escrevesse um e-mail propondo as mudanças a serem feitas nesse contrato, e em sequência a última tarefa, foi uma revisão do tom de formalidade deste e-mail. Em testes onde as tarefas são pedidas todas juntas o modelo esquece algumas delas e acaba não propondo mudanças de contrato.
    
- 9. Dicas para grandes prompts
    
    Existem algumas dicas que são específicas a prompt que são muito grandes:
    
    1. Comesse com os dados:
        
        Quando o seu prompt possuir dados extensos, coloque os dados no topo do prompt e em sequência forneço o resto do prompt. Na data atual (02/10/2024) modelos em arquitetura LLM tendem a acertar mais nesse tipo de tarefa quando estes documentos estão no começo do prompt (30% a mais)
        
    2. Estruture com XML
        
        Ao utilizar múltiplos documentos, coloque eles em volta de tags de documento
        
        ```json
        <documents>
          <document index="1">
            <source>annual_report_2023.pdf</source>
            <document_content>
              {{ANNUAL_REPORT}}
            </document_content>
          </document>
          <document index="2">
            <source>competitor_analysis_q2.xlsx</source>
            <document_content>
              {{COMPETITOR_ANALYSIS}}
            </document_content>
          </document>
        </documents>
        
        Analyze the annual report and competitor analysis. Identify strategic advantages and recommend Q3 focus areas.
        ```
        
        Perceba que aqui a dica da stags xml hierárquica e é seguida, todos os documentos estão em volta de uma tag genérica de documentos, o conteúdo e os títulos também estão separados em tags próprias. 
        
    3. Retire “Partes importantes”
        
        Quando os documentos forem demasiadamente longos, peça que o LLM extrai as partes importantes, e depois, analise essas partes, isso é parecido com o que utilizamos no conceito de encadeamento, quando uma tarefa muito grande é quebrada em passos menores, mas aqui, isso ocorre mais na linha de tirar informação não útil da análise.
        
        ```json
        You are an AI physician's assistant. 
        Your task is to help doctors diagnose possible patient illnesses.
        
        <documents>
          <document index="1">
            <source>patient_symptoms.txt</source>
            <document_content>
              {{PATIENT_SYMPTOMS}}
            </document_content>
          </document>
          <document index="2">
            <source>patient_records.txt</source>
            <document_content>
              {{PATIENT_RECORDS}}
            </document_content>
          </document>
          <document index="3">
            <source>patient01_appt_history.txt</source>
            <document_content>
              {{PATIENT01_APPOINTMENT_HISTORY}}
            </document_content>
          </document>
        </documents>
        
        Find quotes from the patient records and appointment history that are relevant to diagnosing the patient's reported symptoms. 
        Place these in <quotes> tags. Then, based on these quotes, list all information that would help the doctor diagnose the patient's symptoms. 
        Place your diagnostic information in <info> tags.
        ```
        
- 10. Dica “Bônus”
    
    Nesse link você pode encontrar vários prompt’s bem estruturados feitos em muitos contextos diversos, eles podem ser usados de inspiração para os seus prompts:
    
    [Library - Anthropic](https://docs.anthropic.com/en/prompt-library/library)
