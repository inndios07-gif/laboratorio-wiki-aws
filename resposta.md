# 📝 Resposta do Laboratório: A Wiki Perdida dos Arquivos Corporativos

> Preencha este arquivo com a sua proposta de solução.
>
> Sua resposta deve explicar como transformar os documentos brutos da pasta `raw/` em uma Wiki Corporativa Inteligente, pesquisável e segura usando apenas serviços da AWS.

---

## 👤 Identificação

**Nome:**  
Arnaldo josé de Oliveira Juníor

**Data:**  
08/10/2026

**Link do repositório:**  
https://github.com/inndios07-gif/laboratorio-wiki-aws

---

# ✅ Quest 1: O Mapa dos Arquivos Perdidos

## 1.1 Formatos encontrados na pasta `raw/`

Descreva quais tipos de arquivos existem dentro da pasta raw/.

Exemplo de como responder, com o formato e o que ele implica:
- <extensao>: <nasce digital ou precisa de OCR?>, <o que da para extrair>
Abra a pasta e liste o que voce encontrou de fato. Esta quest avalia a sua leitura do acervo, entao a resposta certa e a que corresponde aos arquivos.

Sua resposta:
A análise dos três documentos presentes na pasta raw/ evidencia diferentes características quanto à origem, estrutura e necessidade de aplicação de técnicas de Reconhecimento Óptico de Caracteres (OCR).
O primeiro documento, denominado vendas_sa_dados_ficticios_laboratorio.csv, apresenta-se na extensão CSV. Trata-se de um arquivo de origem digital, totalmente estruturado, o que possibilita a leitura e o processamento direto por sistemas computacionais. Por estar em formato tabular e organizado, não há necessidade de aplicação de OCR, visto que os dados já se encontram acessíveis de forma nativa.
O segundo documento, ata_reuniao_vendas_sa.pdf, possui extensão PDF e também tem origem digital. Diferentemente de arquivos digitalizados em imagem, este documento apresenta texto selecionável, permitindo extração direta por meio de código ou ferramentas de manipulação de PDF. Dessa forma, não há demanda por OCR, uma vez que o conteúdo textual pode ser obtido sem etapas adicionais de reconhecimento óptico.
Por fim, o terceiro documento, ata_resultados_vendas_novos_dados.png, encontra-se na extensão PNG e corresponde a um arquivo de imagem. Sua composição simula uma ata impressa em papel, contendo anotações e carimbos. Nesse caso, o conteúdo não está estruturado digitalmente, sendo apenas visual. Assim, torna-se indispensável a aplicação de OCR para viabilizar a extração do texto presente na imagem, permitindo que as informações sejam convertidas em formato manipulável e estruturado.
Em síntese, conclui-se que apenas o arquivo em formato PNG requer processamento via OCR para extração de informações. Os demais arquivos, por serem digitais e estruturados, já oferecem acesso direto ao conteúdo textual ou tabular, dispensando o uso dessa tecnologia.



---

## 1.2 Principais desafios encontrados

Explique quais dificuldades esses documentos podem apresentar.

```md
Exemplo:
- Arquivos sem padrão de nomenclatura
- Documentos escaneados com baixa qualidade
- Textos manuscritos ou parcialmente ilegíveis
- Atas com estruturas diferentes
- Informações importantes espalhadas em vários formatos
```

**Sua resposta:**

```md
Heterogeneidade de Formatos (Múltiplas Camadas de Processamento)
*Arquivos em três naturezas diferentes: .csv (estruturado), .pdf (semiestruturado) e .png (não estruturado). 
*Impacto: Não é possível utilizar um pipeline único de ingestão. É necessário orquestrar três fluxos distintos: 
***Ingestão direta tabular (ex.:  AWS Glue).
***Extração de texto/tabelas vetoriais de PDF (ex.: AWS Textract).
***Pipeline de OCR e visão computacional para imagens.

Elementos Manuscritos, Carimbos e Marcações Visuais no PNG
*Presença de escrita à mão: Anotações como "conferir CRM" (em azul) e "ação prioritária" dentro de um círculo vermelho no item de deliberações.
*Carimbos e assinaturas: O carimbo "DOCUMENTO FICTICIO" e as assinaturas no rodapé podem gerar ruído ou sobreposição de caracteres.
*Impacto: Mecanismos de OCR convencionais (que só leem texto tipográfico) ignoram ou corrompem textos manuscritos (Handwriting Recognition - HWR), perdendo apontamentos humanos importantes.

Divergência de Layout e Estrutura entre as Atas

Inconsistência nos Padrões de Dados e Notações
*Representação de valores monetários e números: 
***No CSV: Valores float decimais sem formatação monetária (ex.: 117700.00, 15 para percentual).
***No PDF: Padrão brasileiro por extenso (ex.: R$ 4.280.000, 91,4%).
***No PNG: Notação abreviada (ex.: R$ 9,85 mi, +1,8 p.p., 41.320).
Formatos de data heterogêneos: 
***ISO (2026-09-25 no CSV e bloco final do PDF), formato brasileiro (08/07/2026 e 28/02/2026) e por extenso (15 de janeiro de 2026 no PNG).
*Impacto: Exige uma etapa pesada de sanitização e normalização antes de qualquer análise quantitativa unificada.

Ausência de Padrão de Nomenclatura e Metadados dos Arquivos
*Nomes dos arquivos: 
*vendas_sa_dados_ficticios_laboratorio.csv
*ata_reuniao_vendas_sa.pdf
*ata_resultados_vendas_novos_dados.png
*Impacto: Os nomes não contêm data de referência (YYYYMMDD), versão ou código do documento. Em um bucket S3, isso dificulta a partição automática por data, ordenação cronológica e governança de catálogo de dados.

```

---

## 1.3 Informações importantes a serem extraídas

Liste quais informações precisam ser identificadas para transformar os documentos em conhecimento pesquisável.

**Sua resposta:**

```md
Para transformar esses documentos em um sistema de busca inteligente (onde qualquer pessoa ou IA possa encontrar respostas rapidamente), precisamos extrair 6 tipos principais de informação:

Dados do Documento (Para saber qual é e de quando é)
*Nome e Código do arquivo: ex.: ata_reuniao_vendas_sa.pdf, Código VSA-COM-2026-07.
*Tipo: se é uma Ata de Reunião, um Resumo Semestral ou uma Planilha de Vendas.
*Data: dia da reunião ou período dos dados (ex.: 08/07/2026, 2º Semestre de 2026).

Pessoas e Responsáveis (Para saber quem é quem)
*Participantes e Cargos: ex.: Mariana Costa (Diretora), Rafael Nunes (Gerente Sudeste).
*Vendedores: nomes dos vendedores da base comercial (ex.: Lucas Ribeiro, Henrique Pires).
*Donos das Tarefas: quem é o responsável por cada entrega.

Vendas e Clientes (Para pesquisar o histórico comercial)
*Nome do Cliente e Segmento: ex.: Orion Digital, empresas de Tecnologia, Varejo, Logística.
*Região: Sudeste, Sul, Nordeste, Centro-Oeste e Norte.
*Produtos e Campanhas: ex.: CRM Profissional, Integração Enterprise, Campanha "Rota 120".
*Status da Venda: se foi Ganha, Perdida (e o motivo da perda) ou se está Em Negociação.

Números e Indicadores (Para consultar métricas e valores)
*Faturamento e Metas: quanto a empresa planejou vs. quanto realmente vendeu (ex.: R$ 9,85 milhões).
*Valores das Vendas: valor bruto, desconto dado e valor final de cada contrato.
*Métricas de Performance: taxa de conversão (%), ticket médio e tempo médio de negociação (dias).

Decisões, Prazos e Riscos (Para acompanhar o que foi combinado)
*Decisões Aprovadas: o que a diretoria decidiu na reunião.
*Plano de Ação: 
***A tarefa a ser feita (ex.: listar as 120 contas da campanha).
***O responsável (ex.: Camila Rocha).
***O prazo limite (ex.: 20/07/2026).
*Riscos Identificados: problemas mapeados e o que fazer para evitá-los.

Anotações Feitas à Mão (Para não perder alertas manuais)
*Recados nas margens: avisos como "conferir CRM".
*Prazos destacados: círculos e notas de "ação prioritária" em datas específicas.
*Assinaturas e Carimbos: confirmação de quem aprovou o documento.

```

---

## 1.4 Estratégia de classificação inicial

Como você classificaria os documentos sem depender de subpastas dentro de `raw/`?

**Sua resposta:**

```md
A melhor prática de engenharia de dados e arquitetura na nuvem é utilizar uma classificação orientada a conteúdo e metadados.

Verifica o formato real do arquivo (não apenas a extensão):
*Se text/csv ou text/plain - Encaminha para validação de colunas.
*Se application/pdf - Encaminha para extração de texto digital.
*Se image/png ou image/jpeg - Encaminha para rota de OCR.

Verifica o conteúdo por IA:
*Para CSV: Lê o cabeçalho. Ao encontrar oportunidade_id, vendedor_ficticio, ..., classifica como tipo_negocio = "crm_oportunidades".
*Para PDF / Imagens: Extrai o texto do cabeçalho da página 1:
***Se contém "ATA DE REUNIÃO" + "Código da ata: VSA-COM-" - Classifica como tipo_negocio = "ata_acompanhamento_mensal".
***Se contém "RESULTADOS DO 2º SEMESTRE" - Classifica como tipo_negocio = "resumo_executivo_semestral".

Classificação Semântica com IA (LLM / AWS Bedrock)
*Para documentos livres ou sem padrão fixo, uma chamada rápida a um modelo de linguagem analisa uma amostra do texto e retorna um JSON padronizado:

Como não usamos pastas, essas classificações são salvas em dois lugares em Tags de Objeto no Armazenamento AWS S3 Object Tagging ou AWS Glue Data Catalog, seria as opções escolhidas.
```

---

# ✅ Quest 2: O Portal de Entrada na AWS

## 2.1 Armazenamento dos arquivos brutos

Explique como os arquivos da pasta `raw/` seriam enviados e armazenados na AWS.

Serviços que você pode considerar:

- Amazon S3
- AWS IAM
- AWS KMS
- Amazon S3 Versioning
- Amazon S3 Lifecycle

**Sua resposta:**

```md
Na AWS, a ingestão e o armazenamento dos arquivos da pasta raw/ seguem o padrão de Data Lake na nuvem, utilizando o Amazon S3 como repositório central e uma arquitetura orientada a eventos. E pesquisando encontrei 3 formas comum de envio:

1. Via Linha de Comando (AWS CLI) — Ideal para administradores
*** aws s3 sync ./raw s3://data-lake-vendas-sa/raw/

2. Via Código Python (boto3) — Ideal para rotinas automatizadas
***import boto3
s3 = boto3.client('s3')
bucket_name = 'data-lake-vendas-sa'
# Exemplo de upload atribuindo Tags de metadados
s3.upload_file(
    Filename='ata_reuniao_vendas_sa.pdf',
    Bucket=bucket_name,
    Key='raw/ata_reuniao_vendas_sa.pdf',
    ExtraArgs={
        'Tagging': 'departamento=comercial&origem=upload_direto'
    }
)

3. Via Interface Web com S3 Presigned URLs — Ideal para usuários finais
*Uma função AWS Lambda gera uma URL temporária e segura (Presigned URL), o navegador do usuário envia o arquivo diretamente para o S3, sem sobrecarregar o servidor web e sem expor credenciais da AWS.

Após o envio o arquivo entra no S3 e o ecossistema do AWS processa os dados automaticamente.
1. Gatilho de Evento (S3 Event Notification): O S3 emite um evento s3:ObjectCreated:*.
2. Triagem Automática (AWS Lambda):
***Lê o arquivo e identifica o formato (CSV, PDF ou PNG).
***Registra os metadados no Amazon DynamoDB (Catálogo de Documentos).
3. Roteamento para o Serviço Correto:
***Se for .csv - Disponibiliza para consulta via Amazon Athena e ingestão no AWS Glue.
*** Se for .pdf ou .png - Envia para o Amazon Textract (extração de texto, tabelas e anotações manuscritas) e posterior indexação vetorial (ex.: Amazon Bedrock / OpenSearch para busca semântica).


```

---

## 2.2 Preservação dos arquivos originais

Explique como garantir que os arquivos originais sejam mantidos intactos e rastreáveis.

**Sua resposta:**

```md
A AWS combinam recursos de segurança de armazenamento, verificação de integridade e registro de linhagem.

1. S3 Object Lock - Bloqueia qualquer tentativa de alteração ou exclusão de um arquivo durante um período determinado (ou indefinido).Nem mesmo o usuário administrador (root) da conta consegue apagar ou sobrescrever o arquivo enquanto a regra de retenção estiver ativa.

2. S3 Versioning - Se um arquivo com o mesmo nome (ata_reuniao_vendas_sa.pdf) for enviado novamente por engano, o S3 não sobrescreve o anterior. Ele cria uma nova versão com um ID único (versionId), preservando o arquivo original intacto no histórico.

3. IAM e Bucket Policies - A pasta raw/ recebe uma regra explícita de Deny (Bloqueio) para ações de exclusão.

4. Checksums SHA-256 - No momento do upload, o S3 calcula e armazena o hash criptográfico (SHA-256) do arquivo. Qualquer verificação futura compara o hash atual com o original. Se um único caractere for modificado, o hash muda e o sistema acusa adulteração ou corrupção de dados.

5. Auditoria Completa de Acessos com AWS CloudTrail - Quem enviou, quando enviou, de onde veio e como o arquivo foi processado.

6.Data Lineage - No momento em que o arquivo aterrissa no S3, uma função Lambda extrai os metadados e grava um registro único em uma tabela de rastreio (ex.: no Amazon DynamoDB):
```

---

## 2.3 Extração de texto dos documentos

Explique como cada tipo de arquivo seria processado.

Considere:

- PDFs escaneados;
- Imagens;
- PDFs digitais;
- Arquivos `.txt`;
- Arquivos `.docx`;
- Arquivos `.md`.

Serviços que você pode considerar:

- Amazon Textract
- AWS Lambda
- AWS Step Functions
- Amazon S3
- Amazon CloudWatch

**Sua resposta:**

```md
PDF Escaneado - Amazon Textract	OCR Assíncrono + Layout	- Velocidade Segundos a minutos - Custo Estimado Médio (por página OCR).
Imagem (.png/.jpg) - Amazon Textract / Bedrock	OCR Síncrono / Visão Computacional - Velocidade	1 a 3 segundos Custo Estimado Baixo a Médio.
PDF Digital - AWS Lambda Extração direta vetorial de texto - Velocidade	Milissegundos - Custo Estimado	Quase zero (Serverless)
Arquivo .txt - AWS Lambda	Leitura direta do buffer - Velocidade Milissegundos - Custo Estimado Quase zero
Arquivo .docx - AWS Lambda (python-docx) Leitura de tags XML/Parágrafos - Velocidade Milissegundos - Custo Estimado	Quase zero
Arquivo .md - AWS Lambda	Divisão estruturada por tópicos - Velocidade Milissegundos	- Custo Estimado Quase zero
```

---

## 2.4 Tratamento de falhas

Explique como sua solução identificaria e registraria erros de processamento.

**Sua resposta:**

```md
De acordo com o aprendizado e minhas pesquisas a solução monitora a qualidade do processamento validando índices de confiança (Confidence Score do OCR) e consistência dos arquivos. Sob uma perspectiva a ocorrência de falhas não é uma exceção, mas uma certeza estatística decorrente do volume, da concorrência e da heterogeneidade dos dados, onde se tem ferramentas que pode antecipar, isolar, reprocessar e notificar anomalias sem interromper o fluxo operacional contínuo. Ciatei minha solução para identificar essas falhas:

1. Detecção Precoce e Validação de Qualidade (Data Quality Gateways) - Antes e durante as etapas de extração, o sistema atua preventivamente por meio de barreiras de validação que impedem que dados corrompidos ou ilegíveis avancem no pipeline. funções serverless detecta arquivo binário corrompido renomeado inadvertidamente como .pdf.

2. Tratamento de Falhas Transitórias: Retries com Backoff Exponencial e Jitter - Nem toda falha representa um problema no arquivo; muitas vezes, decorrem de instabilidades passageiras de rede, limites temporários de requisições por segundo (throttling de APIs como Textract e Bedrock) ou saturação momentânea de concorrência.

3. Isolamento de Falhas Críticas: Dead Letter Queue (SQS DLQ) e Quarentena Lógica - Quando um arquivo atinge o limite máximo de tentativas sem sucesso ou apresenta um erro irrecuperável (como um PDF criptografado por senha ou arquivo irreparavelmente corrompido), o sistema executa o desacoplamento da carga de trabalho.

4. O registro é feito de forma redundante em dois níveis: Logs Técnicos e Catálogo de Negócio.
*Logs Técnicos Detalhados (Amazon CloudWatch Logs) - Cada falha gera um log estruturado em JSON contendo o rastreio completo do erro (stack trace).
}
  "timestamp": "2026-10-08T13:45:00Z",
  "level": "ERROR",
  "arquivo": "s3://data-lake-vendas-sa/raw/ata_resultados_vendas_novos_dados.png",
  "codigo_erro": "TEXTRACT_LOW_CONFIDENCE",
  "mensagem": "Extração abaixo do limite aceitável de confiança (média: 48.2%)",
  "tentativas": 3,
  "requestId": "c1a2b3d4-e5f6-7890-abcd-1234567890ef"
}
*Atualização no Catálogo de Documentos (Amazon DynamoDB) -
{
  "documento_id": "DOC-20261008-002",
  "nome_original": "ata_resultados_vendas_novos_dados.png",
  "status_processamento": "FALHA_QUALIDADE",
  "motivo_falha": "Necessita revisão humana: anotações manuscritas ilegíveis",
  "data_ultima_tentativa": "2026-10-08T13:45:02Z",
  "requer_revisao_manual": true
}
---

# ✅ Quest 3: A Relíquia dos Metadados

## 3.1 Padronização dos textos processados

Explique como os textos extraídos seriam limpos, normalizados e preparados para consulta.

**Sua resposta:**

```md
Logo após a extração dos documentos, o texto passa por um refinamento na AWS para se tornar
verdadeiramente útil. Primeiro, o AWS Lambda realiza uma limpeza geral: corrige falhas de
leitura, remove caracteres soltos e padroniza datas e valores financeiros para que todos os
 arquivos falem a mesma língua. Em seguida, o Amazon Comprehend analisa o conteúdo para
identificar elementos fundamentais — como clientes, vendedores, regiões e produtos —, e no
mascaramento de dados sensíveis (PII) para conformidade com a LGPD. O texto higienizado é
então dividido pelo Lambda em partes menores e bem organizadas, preservando o contexto de
tabelas e tópicos. Por fim, o Amazon Bedrock utiliza inteligência artificial para traduzir
 essas partes em um formato compreensível para o motor de busca, armazenando tudo no
Amazon OpenSearch Service (ou Bedrock Knowledge Bases). Dessa forma, qualquer usuário
consegue fazer perguntas em linguagem comum e obter respostas precisas em segundos.
```

---

## 3.2 Metadados propostos

Defina quais metadados você extrairia de cada documento.

| Metadado | Por que ele é importante? |
|---|---|
| Nome do documento | ata_reuniao_vendas_sa.pdf |
| Tipo do documento | Ata de Reunião Comercial (Acompanhamento Mensal) |
| Data identificada | 8/07/2026 (Horário: 09h00 às 10h35) |
| Tema principal | Revisão de desempenho de junho/2026, análise do funil e definição da campanha "Rota 120". |
| Participantes | Mariana Costa, Rafael Nunes, Camila Rocha, Bruno Almeida, Fernanda Lima, Livia Mendes. |
| Decisões tomadas | D-001 a D-005: Revisão semanal do pipeline; limite de 7 dias sem atividade; campanha Rota 120, etc. |
| Responsáveis | Mariana Costa, Livia Mendes, Rafael Nunes, Fernanda Lima, Camila Rocha e Bruno Almeida.|
| Próximos passos | Ações A-001 a A-006 (prazos: 13/07 a 24/07/2026) e Próxima Reunião em 03/08/2026. |
| Nível de confidencialidade | Uso didático / Dados 100% simulados (Sem valor jurídico). |
| Caminho do arquivo original | raw/ata_reuniao_vendas_sa.pdf |
| Código de Referência | VSA-COM-2026-07 |
| Necessidade de OCR | Não (Documento nasce digital com texto nativo). |
|---|---|
| Nome do documento | ata_resultados_vendas_novos_dados.png |
| Tipo do documento | Ata de Reunião Comercial (Resumo Semestral) |
| Data identificada | 15 de janeiro de 2026 (Horário: 09h00 às 11h10)|
| Tema principal | Resultados do 2º semestre e definição de metas/ações para o novo ciclo. |
| Participantes | Marina Lopes, Paulo Mendes, Carla Ribeiro, Diego Alves, Renata Souza e supervisores. |
| Decisões tomadas | Expansão no Norte, revisão de descontos por margem, campanha de ticket médio e painel de conversão. |
| Responsáveis |Paulo Mendes, Renata Souza, Diego Alves, Carla Ribeiro e Marina Lopes.|
| Próximos passos | Prazos das deliberações (05/02 a 28/02/2026) com foco prioritário na expansão do Norte. |
| Nível de confidencialidade | DOCUMENTO FICTICIO (Carimbo no rodapé). |
| Caminho do arquivo original | raw/ata_resultados_vendas_novos_dados.png |
|Anotações Manuscritas |Recado em azul: "conferir CRM"; Destaque circular em vermelho: "ação prioritária". |
| Necessidade de OCR | Sim (Obrigatório): Requer OCR com suporte a texto e manuscrito (HWR). |
|---|---|
| Nome do documento | vendas_sa_dados_ficticios_laboratorio.csv |
| Tipo do documento | Base de Dados Transacional / CRM de Oportunidades |
| Data identificada | Intervalo entre 01/07/2026 e 03/10/2026 (criação e fechamento)|
| Tema principal | Registro individualizado de oportunidades de vendas, receitas, descontos e motivos de perda. |
| Participantes |Vendedores: Lucas Ribeiro, Henrique Pires, Ana Torres, etc. / Clientes: Orion, Nexo, Atlas, etc. |
| Decisões tomadas | Status de cada oportunidade (Ganha, Perdida, Qualificação, Em negociação, Proposta enviada). |
| Responsáveis |Vendedor atribuído a cada oportunidade na coluna vendedor_ficticio.|
| Próximos passos | Datas e tarefas em proxima_atividade e observacao (ex.: "Proposta em revisão jurídica"). |
| Nível de confidencialidade | Dados Fictícios para Laboratório Prático. |
| Caminho do arquivo original | raw/vendas_sa_dados_ficticios_laboratorio.csv |
|Chave Primária (ID) |oportunidade_id (Padrão: OPP-2026XXXX). |
| Necessidade de OCR | Não (Arquivo tabular 100% estruturado). |
Adicione outros metadados, se necessário.

---

## 3.3 Uso de IA para enriquecimento dos documentos

Explique como o Amazon Bedrock poderia ajudar a identificar temas, decisões, responsáveis, pendências e resumos dos documentos.

**Sua resposta:**

```md
O Amazon Bedrock é o serviço gerenciado da AWS que disponibiliza Modelos de Linguagem e Visão de última geração (como Anthropic Claude, Amazon Nova e Meta Llama).No processamento dos documentos analisados (atas, resumos e planilhas), o Bedrock atua como um analista inteligente automatizado, capaz de ler texto corrido, tabelas e anotações manuscritas para extrair significado e transformá-lo em dados estruturados.

Vantagens do Bedrock nessa Arquitetura
**Elimina Regras Rígidas de Código: Funciona mesmo se o modelo da ata mudar de layout a cada mês.
**Capacidade Multimodal: Interpreta tanto o texto digital quanto marcas visuais e caligrafia em imagens.
**Integração com RAG (Knowledge Bases): Permite cruzar a ata com a planilha CSV para responder perguntas como: "As oportunidades perdidas por preço no CSV batem com o que foi dito na ata?".

Entrada Bruta vs. Saída do Amazon Bedrock

***Texto bruto da Ata(PDFD/PNG) ---- Amazon Bedrock (LLM Multimodal) ---- JSON Estruturado e Enriquecido
{
  "documento": "VSA-COM-2026-07",
  "tema_principal": "Acompanhamento comercial de junho e campanha Rota 120",
  "resumo_executivo": "A receita mensal atingiu R$ 3,91 mi (91,4% da meta). Decidiu-se padronizar motivos de perda no CRM e lançar a campanha Rota 120 para o 3º trimestre visando R$ 6 mi em novo pipeline.",
  "decisoes": [
    "Adotar regra de 7 dias sem atividade para sinalizar oportunidades estagnadas",
    "Implantar revisão semanal de funil às segundas-feiras"
  ],
  "plano_de_acao_pendencias": [
    {
      "acao": "Revisar oportunidades sem atividade há mais de 7 dias",
      "responsavel": "Rafael Nunes",
      "prazo": "2026-07-13",
      "prioridade": "Alta"
    },
    {
      "acao": "Definir lista de contas da campanha Rota 120",
      "responsavel": "Camila Rocha",
      "prazo": "2026-07-20",
      "prioridade": "Alta"
    }
  ]
}


Explicando como o Bedrock atua em cada uma das solicitações:

1. Identificação de Temas - Em vez de apenas contar palavras-chave, os modelos entendem o contexto global do documento.
Exemplo: Ao ler a ata de julho, o Bedrock identifica automaticamente que os tópicos centrais são "Revisão do Funil de Vendas do 2º Trimestre" e "Lançamento da Campanha Estratégica Rota 120", atribuindo categorias e tags temáticas padronizadas (ex.: #gestao_comercial, #planejamento_q3).

2. Extração de Decisões Tomadas - O modelo distingue discussões informais de deliberações oficiais aprovadas pela diretoria, mesmo quando não há uma tabela explícita.
Exemplo: Na ata em imagem (.png), o Bedrock lê o item de deliberações e extrai as decisões estratégicas, como "Aprovação da expansão da equipe comercial na Região Norte" e "Revisão da política de concessão de descontos por margem".

3. Mapeamento de Responsáveis - Através do reconhecimento de entidades relacionais, ele não apenas identifica nomes de pessoas, mas quem é o dono de qual tarefa.
Exemplo: Ao cruzar o texto, ele mapeia com precisão:
    Mariana Costa - Presidência da Reunião.
    Camila Rocha - Responsável por definir a lista das 120 contas da campanha.
    Paulo Mendes - Responsável pela expansão de vendas no Norte.

4. Rastreamento de Pendências, Prazos e Alertas - Identifica itens de ação (Action Items), datas limites (deadlines) e até alertas implícitos ou manuais.
Exemplo: Ao analisar a imagem da ata, o Bedrock "enxerga" o círculo vermelho desenhado à mão e a anotação "ação prioritária", extraindo que a tarefa com prazo em 28/02/2026 possui urgência crítica, além de registrar o recado "conferir CRM".

5. Geração de Resumos Inteligentes em Múltiplos Níveis - Sintetiza documentos extensos (como o PDF de 5 páginas) em resumos objetivos adaptados ao perfil do leitor:
Exemplo:
    Resumo Executivo (para Diretoria): Síntese em um parágrafo destacando que a receita ficou 8,6% abaixo da meta             devido a perdas por preço e postergações, com foco na nova campanha Rota 120.
    Resumo Operacional (para as Equipes): Lista direta de ações pendentes dividida por responsável e data de entrega.
```

---

## 3.4 Armazenamento dos metadados

Explique onde os metadados seriam armazenados e como seriam conectados aos documentos originais.

Serviços que você pode considerar:

- Amazon S3
- Amazon DynamoDB
- AWS Glue Data Catalog
- Amazon Bedrock Knowledge Bases

**Sua resposta:**

```md
todo o ciclo – do arquivo bruto ao metadado estruturado, passando por buscas inteligentes e controle de acesso – fica totalmente integrado dentro do ecossistema AWS, garantindo rastreabilidade, consistência e disponibilidade para quem precisar consultar ou processar os documentos.

1. Amazon S3 (bucket raw/) - Arquivo bruto (PDF, PNG, CSV, …) – camada Bronze do Data Lake. Cada objeto tem um URI (s3://<bucket>/raw/<arquivo>) e, opcionalmente, tags (confidentiality=public, status=raw). O URI é o ponto de ancoragem que os demais serviços usarão para “apontar” de volta ao arquivo.

2. Amazon DynamoDB (tabela DocumentMetadata) - Catálogo de metadados estruturado: nome, tipo, data, tema, participantes, decisões, responsáveis, próximos passos, nível de confidencialidade, etc. Cada linha inclui os campos document_id (chave primária) e s3_uri (e, se o bucket estiver versionado, s3_version). Esses campos criam a relação 1‑para‑1 entre o registro e o objeto S3.

3. AWS Glue Data Catalog - Definição de esquemas (ex.: colunas da CSV) e classificação de tipos de documento (ata, relatório, base). As tabelas apontam para o mesmo bucket S3 usando Location = s3://…/raw/<arquivo>. Assim, consultas em Athena/Glue sabem exatamente qual arquivo ler.

4. Amazon OpenSearch Service (ou Bedrock Knowledge Base) - Índice de busca semântica (texto completo + embeddings). Cada documento indexado contém o campo source_uri que replica o s3_uri de DynamoDB, permitindo que a pesquisa retorne o caminho do arquivo.

5. AWS Lake Formation - Políticas de controle de acesso baseadas em tags e atributos. Usa as mesmas tags do S3 (confidentiality) e os atributos de linha da tabela DynamoDB para aplicar regras de acesso granulares.
```

---

# ✅ Quest 4: O Oráculo da Wiki Inteligente

## 4.1 Estratégia de indexação

Explique como os documentos seriam divididos em trechos menores e preparados para busca semântica.

**Sua resposta:**

```md
AWS Lambda (ou AWS Step Functions) recebe o objeto do S3 raw/.
O texto extraído (Textract, PyMuPDF, python‑docx, etc.) passa por uma rotina de limpeza:
    remoção de quebras de linha excessivas, caracteres de controle e espaços duplicados;
    normalização de datas (ISO 8601) e de valores monetários (números decimais);
    mascaramento de informações pessoais com Amazon Comprehend (PII detection).

Upload → S3 (raw/)
Evento → Lambda (extrai, limpa)
Chunking (títulos, limites de tokens, sobreposição)
Embedding (Bedrock)
Indexação (OpenSearch k‑NN)
Busca semântica (query → Bedrock → OpenSearch → retorno com source_uri)

```

---

## 4.2 Busca semântica e base vetorial

Explique como embeddings seriam gerados e onde seriam armazenados.

Serviços que você pode considerar:

- Amazon Bedrock Knowledge Bases
- Amazon OpenSearch Serverless
- Amazon Aurora PostgreSQL com pgvector
- Amazon S3 Vectors
- Modelos de embeddings no Amazon Bedrock

**Sua resposta:**

```md
Geração de embeddings (vetores semânticos)

Para cada chunk:

O Lambda chama Amazon Bedrock com o modelo de embedding (por exemplo, Titan Text Embeddings).
O serviço devolve um vetor de ~1 024 dimensões.
O vetor é armazenado junto ao chunk_id em Amazon OpenSearch Service (índice document‑chunks) ou em Amazon DynamoDB (campo embedding codificado em base64) – a escolha depende do volume de consultas: OpenSearch oferece k‑NN nativo, DynamoDB fornece busca por chave.

Indexação para busca semântica

No OpenSearch:

json
PUT document-chunks/_doc/c001
{
  "document_id": "VSA-COM-2026-07",
  "source_uri": "s3://data-lake-vendas-sa/raw/ata_reuniao_vendas_sa.pdf",
  "heading": "1. Visão geral",
  "text": "Ata da reunião de 08/07/2026 – Revisão de junho…",
  "embedding": [0.023, -0.112, ...]    // vetor numérico
}
O campo embedding usa o plugin k‑NN de OpenSearch, que permite consultas de similaridade (cosine, dot‑product).
Metadados auxiliares (document_id, heading, type) permanecem como atributos filtráveis.

Consulta semântica
O usuário envia uma pergunta via aplicação (ex.: “Quais foram as decisões sobre a campanha Rota 120?”).
A aplicação gera o embedding da query chamando Bedrock.
O embedding é enviado para o índice OpenSearch com a API _knn_search.
OpenSearch devolve os chunks mais semelhantes; o serviço agrega os resultados e exibe o texto completo ou fornece um resumo.

```

---

## 4.3 Geração de respostas com IA

Explique como a Wiki responderia perguntas em linguagem natural com base nos documentos originais.

Considere explicar:

- Como a pergunta do usuário seria recebida;
- Como os trechos relevantes seriam recuperados;
- Como o Amazon Bedrock geraria a resposta;
- Como a resposta indicaria as fontes utilizadas.

**Sua resposta:**

```md
Pergunta do usuário
O visitante digita a pergunta em português na interface da Wiki (por exemplo, “Qual foi a decisão sobre a campanha Rota 120?”).

Conversão da pergunta em vetor semântico
O texto da pergunta é enviado ao Amazon Bedrock, que usa um modelo de embeddings (Titan Text Embeddings ou Claude 3). O modelo devolve um vetor numérico que representa o sentido da pergunta.

Busca nos blocos de conteúdo (RAG – Retrieval‑Augmented Generation)
Cada documento já foi fragmentado em pequenos trechos (chunks) durante a ingestãoe cada trecho recebeu seu próprio vetor de embeddings. Esses vetores estão armazenados no Amazon OpenSearch Service com o plugin k‑NN (busca por similaridade).

O vetor da pergunta é comparado aos vetores dos trechos usando distância de cosseno.
Os 5‑10 trechos mais semelhantes são devolvidos, juntamente com seus metadados (nome do arquivo, título da seção, página, etc.).

Geração da resposta
O prompt completo é enviado novamente ao Amazon Bedrock, desta vez para um modelo de geração de texto (Claude 3, Titan Text ou outro LLM‑generativo). O modelo combina o conteúdo dos trechos com seu conhecimento geral e produz uma resposta em linguagem natural.

A resposta aparece na tela da Wiki.
Cada informação citada traz um link direto ao arquivo original no Amazon S3 (por exemplo, [Ata de Reunião – Decisões] (s3://data‑lake‑vendas‑sa/raw/ata_reuniao_vendas_sa.pdf)), permitindo que o usuário abra o documento completo se quiser conferir detalhes.
Se houver várias fontes, a UI exibe uma lista de “Referências” ao final da resposta.

Dessa forma, a Wiki age como um assistente de perguntas e respostas que entende a linguagem natural do usuário, busca a informação correta nos documentos originais armazenados no AWS e devolve respostas claras, citando exatamente de onde cada dado foi extraído.

```

---

## 4.4 Interface de consulta

Proponha como os usuários acessariam essa Wiki Inteligente.

Serviços que você pode considerar:

- Amazon Q Business
- AWS Amplify
- Amazon API Gateway
- AWS Lambda
- Amazon Cognito

**Sua resposta:**

```md
Proponho uma interface simples abaixo segue os componentes da interface:
1. Barra de busca - Campo de texto onde o usuário digita a pergunta em linguagem natural. Usaria API Gateway - Lambda - Bedrock (embeddings) - OpenSearch

2. Resultados - Lista de respostas curtas com citações e links para o documento original. Usaria Lambda formata a resposta do Bedrock e inclui s3://…/raw/... como hyperlink.

3. Painel de navegação - Menu lateral com categorias (Atas, Relatórios, Dados CRM) e filtros por data, responsável, confidencialidade. Usaria Dados carregados dinamicamente de DynamoDB via API.

4. Visualizador de documento - Área modal ou nova aba que exibe PDF, PNG ou CSV renderizado (preview) sem download. Usaria Amazon S3 presigned URL (gerada pelo Lambda) - iframe/PDF.js ou SheetJS para CSV.

---------------------------------------------------------
 |  Logo Wiki Inteligente   |  [Login/Logout]            |
 ---------------------------------------------------------
 |  Busca: _______________________________________ [🔍]|
 ---------------------------------------------------------
 |  ⎡ Atas        ]  ⎡ Relatórios ]  ⎡ Dados CRM   ]    |
 |  ⎡ 2026‑07‑08 ]  ⎡ 2026‑01‑15]  ⎡ 2026‑07‑... ]    |
 ---------------------------------------------------------
 |  Resposta:                                           |
 |  "A campanha Rota 120 será lançada no Q3..."          |
 |  Fonte:  ata_reuniao_vendas_sa.pdf (08/07/2026)      |
 |  [ Ver documento ]                                   |
 ---------------------------------------------------------
 |  👍  👎  (avaliação)                                 |
 ---------------------------------------------------------
```

---

## 4.5 Segurança, auditoria e monitoramento

Explique como controlar acesso, proteger dados, auditar consultas e monitorar custos, erros e qualidade das respostas.

Serviços que você pode considerar:

- AWS IAM
- AWS KMS
- Amazon Cognito
- AWS CloudTrail
- Amazon CloudWatch
- Amazon Macie
- AWS Cost Explorer

**Sua resposta:**

```md
Para o controle de acesso quem tem permissão veja ou altere os documentos, a solução usa Amazon Cognito como ponto de entrada de identidade. Cada usuário se autentica (login tradicional ou SSO via Google/Office 365) e recebe um JWT contendo seu grupo (por exemplo, sales, management, auditor). O token é enviado em todas as chamadas ao API Gateway, que verifica as políticas de autorização definidas em IAM. As políticas permitem, por exemplo, que o grupo sales leia arquivos na pasta raw/ mas não copie ou delete; já management tem acesso total e pode gerar relatórios.

*Criptografia em repouso – Todos os objetos do bucket S3 são protegidos com SSE‑KMS (chave gerenciada pelo cliente).
*Criptografia em trânsito – O CloudFront entrega o site via HTTPS e o API Gateway expõe apenas HTTPS.
*Imutabilidade – O bucket está configurado com S3 Object Lock (modo Compliance) e Versionamento, de modo que nenhuma operação de delete ou overwrite possa alterar o conteúdo original, respeitando requisitos de retenção.
*Máscara de PII – Antes de armazenar o texto, um Lambda chama Amazon Comprehend para identificar informações pessoais (CPF, e‑mail) e substituí‑las por placeholders, evitando que dados sensíveis circulem na camada de busca.

Na auditoria de consultas Todo o tráfego de API Gateway, chamadas a Lambda e acessos ao bucket S3 são registrados automaticamente pelo AWS CloudTrail. As entradas de log contêm quem (ARN do usuário), quando (timestamp) e o que (ação, recurso, parâmetros).

Custos, erros e qualidades de respostas usaria métricas listadas abaixo:
    Custos de S3 / Lambda / Bedrock - Dashboard de custo no console.
    Taxa de erro de API/Lambda - Alarmes configurados (Alarm - SNS - Slack) para notificar a equipe.
    Tempo de resposta da busca - Gráficos de tendência no CloudWatch.
    Qualidade da resposta - Visualizado em um painel do Amazon QuickSight, que cruza aprovação com tipos de documento e nível de confidencialidade.
    Uso de Bedrock (tokens consumidos) - Alertas quando o consumo ultrapassa limites orçados.
```

---

# 🧩 Arquitetura Final da Solução

Agora reúna tudo em uma visão única.

## 1. Visão geral

Explique em poucas linhas a ideia central da sua arquitetura.

**Sua resposta:**

```md
A arquitetura centraliza os documentos brutos no Amazon S3 (raw/) e, ao serem ingeridos, um AWS Lambda os limpa, divide‑os em trechos (chunking) e gera vetores semânticos com Amazon Bedrock (Titan Text Embeddings); esses vetores são armazenados em um índice k‑NN no Amazon OpenSearch Service para busca rápida por similaridade. Quando o usuário faz uma pergunta na interface estática hospedada em S3 + CloudFront, o texto da consulta é transformado em embedding (Bedrock) e comparado ao índice, retornando os trechos mais relevantes. O modelo generativo do Bedrock (Claude/Titan) então combina esses trechos e produz uma resposta em linguagem natural, citando a origem (URI S3). O acesso é controlado por Amazon Cognito (autenticação) e políticas IAM/etiquetas de bucket, garantindo que apenas usuários autorizados leiam ou modifiquem arquivos. Todas as chamadas de API passam por API Gateway e são registradas no AWS CloudTrail, permitindo auditoria detalhada de quem consultou qual documento. Métricas de latência, erros, uso de Bedrock e custos são enviadas ao Amazon CloudWatch, com alarmes que notificam a equipe via SNS; relatórios de uso e aprovação de respostas são visualizados em Amazon QuickSight. Assim, o projeto combina ingestão automatizada, busca vetorial, geração IA‑assistida e governança completa usando serviços totalmente gerenciados da AWS.
```

---

## 2. Serviços AWS utilizados

| Serviço AWS | Papel na solução |
|---|---|
|Amazon S3 (bucket raw/) |Armazena os documentos originais (PDF, PNG, CSV, DOCX, MD) de forma imutável e versionada. |
|Amazon S3 Object Lock| Garante que os arquivos nunca sejam excluídos ou sobrescritos (modo WORM). |
|Amazon CloudFront | CDN que entrega o site estático (HTML/JS/CSS) rapidamente ao usuário. |
|Amazon Cognito |Gerencia autenticação (login, SSO) e fornece tokens JWT para controle de acesso. |
|Amazon API Gateway | Expõe endpoints REST/WebSocket que recebem as consultas do front‑end. |
|AWS Lambda |	Orquestra a ingestão (limpeza, chunking), gera embeddings, consulta OpenSearch, chama Bedrock e devolve a resposta; também produz URLs temporárias para visualização de documentos. |
|Amazon Textract | Faz OCR em PDFs escaneados e imagens, extraindo texto e tabelas. |
|Amazon Bedrock | itan Text Embeddings: converte trechos e consultas em vetores semânticos. Modelos generativos (Claude/Titan): gera respostas em linguagem natural a partir dostrechos recuperados.|
|Amazon DynamoDB| Catálogo de metadados (nome, tipo, data, tema, responsáveis etc.) e registro de feedback/consultas.|
|AWS Glue Data Catalog| Descreve esquemas (ex.: CSV) e classifica tipos de documentos; facilita consultas em Athena.|
|Amazon OpenSearch Service| Índice vetorial que armazena os embeddings de cada trecho e permite buscas por similaridade (RAG).|
|Amazon CloudWatch| Coleta métricas (latência, erros, uso de Bedrock, custos) e gera alarmes.|
|AWS CloudTrail| Registra todas as chamadas de API (acessos a S3, Lambda, OpenSearch, etc.) para auditoria.|
|Amazon SNS| Envia notificações (e‑mail, Slack, SMS) quando alarmes de CloudWatch ou eventos críticos ocorrem.|
|Amazon QuickSight| Visualiza dashboards de uso, taxa de aprovação de respostas, custos e performance.|
|AWS Lake Formation|Centraliza políticas de controle de acesso baseadas em tags de confidencialidade.|
---

## 3. Fluxo de dados de ponta a ponta

Descreva o caminho dos dados desde a pasta `raw/` até a Wiki Inteligente.

```md
Exemplo de estrutura:

1. Arquivos estão inicialmente na pasta raw/
2. Arquivos são enviados para o Amazon S3
3. Documentos escaneados passam pelo Amazon Textract
4. Arquivos digitais têm seus textos extraídos
5. Textos são limpos e padronizados
6. Metadados são extraídos
7. Conteúdos são indexados em uma base pesquisável
8. Usuário pesquisa na Wiki
9. IA responde com base nos documentos originais
```

**Sua resposta:**

```md
1. Upload local – Os arquivos (PDF, PNG, CSV, DOCX, MD) são colocados na pasta raw/ do repositório ou enviados diretamente pelo usuário.
2. Sincronização com S3 – Um script (AWS CLI aws s3 sync ou GitHub Actions) copia os arquivos para o bucket Amazon S3 data‑lake‑vendas‑sa/raw/.
3. Detecção de formato – O Lambda lê a assinatura do arquivo; se for imagem ou PDF escaneado, encaminha para Amazon Textract (modo assíncrono) que devolve texto, tabelas e anotações manuscritas.
4. Extração direta – Se o documento já for digital (PDF nativo, CSV, DOCX, MD), o Lambda usa bibliotecas PyMuPDF, python‑docx ou leitura de CSV para extrair o conteúdo puro sem OCR.
5. Limpeza e padronização – O texto bruto passa por rotinas.
6. Extração de metadados – O mesmo Lambda aplica expressões regulares e NER (via Comprehend) para identificar nome do documento, tipo, data, tema, participantes, decisões, responsáveis, próximos passos e nível de confidencialidade; grava tudo em Amazon DynamoDB.
7. Chunking (fragmentação) – O texto limpo é dividido em blocos lógicos (por título, tabela ou limite de ~500 tokens) com sobreposição de 100 tokens, gerando um JSON
8. Geração de embeddings – Cada chunk é enviado ao Amazon Bedrock (modelo Titan Text Embeddings) que devolve um vetor numérico.
9. Indexação vetorial – Os vetores são inseridos no índice k‑NN do Amazon OpenSearch Service.
10. Interface de consulta – O usuário acessa a Wiki (site estático hospedado em S3 + CloudFront) e, ao digitar uma pergunta, o front‑end chama o API Gateway - Lambda - gera o embedding da query (Bedrock) - busca os chunks mais similares em OpenSearch.
11.Geração de resposta – Os chunks recuperados são enviados ao modelo generativo do Amazon Bedrock (Claude/Titan) que elabora uma resposta em linguagem natural, citando a origem (source_uri). A resposta volta ao front‑end, que a exibe com link “Ver documento original”.
```

---

## 4. Diagrama textual da arquitetura

Crie um diagrama simples usando texto.

```md
Exemplo:

raw/ → Amazon S3 → Lambda/Step Functions → Textract → S3 Processado → Bedrock Knowledge Bases → Interface de Consulta → Usuário Final
```

**Sua resposta:**

```md
raw/ → Amazon S3 bucket raw/ → Amazon Textract (OCR) (imagens / PDFs) → Extract metadata → DynamoDB →
Chunking (split in sections) → JSON chunks → Generate embeddings (Bedrock – Titan Text Embeddings) →
Index embeddings & metadata in OpenSearch (k‑NN index) → User (browser)searches Wiki → API Gateway (REST)  →
Lambda Query → Bedrock LLM → UI displays  answer + link → Answer JSON → Lambda returns
```

---

## 5. Riscos e limitações

Liste possíveis desafios da sua solução.

```md
Exemplo:
- Documentos ilegíveis podem prejudicar a extração de texto.
- OCR pode gerar erros em documentos com baixa qualidade.
- Custos podem aumentar conforme o volume de documentos.
- Metadados inferidos por IA podem precisar de validação humana.
- Respostas geradas por IA devem sempre referenciar documentos de origem.
```

**Sua resposta:**

```md
- PDFs escaneados ou imagens podem gerar textos com baixa confiança; o Amazon Textract pode precisar de ajustes de pré‑processamento de imagem para reduzir erros de leitura.
- Trechos longos podem ultrapassar o limite de tokens dos modelos Bedrock; o algoritmo de chunking deve equilibrar tamanho e sobreposição para manter contexto.
- Geração de embeddings e respostas gera consumo de tokens; monitorar via CloudWatch e aplicar limites por usuário é essencial para evitar despesas inesperadas.
- Consultas k‑NN no OpenSearch podem ficar lentas com milhões de chunks; é necessário dimensionar réplicas e usar índices otimizados.
- O Amazon Comprehend pode não detectar todos os dados sensíveis; políticas de revisão manual podem ser necessárias para compliance LGPD.
- Tags de confidencialidade no S3 e políticas IAM devem estar sincronizadas; divergências podem expor documentos restritos.
- Vários usuários disparando consultas simultâneas podem saturar limites de taxa (throttling) do Bedrock ou OpenSearch; usar filas (SQS) ou limitação por token ajuda.
- Modelos gerativos podem “alucinar” fatos não presentes nos textos; inserir instruções de citação (source_uri) e validar com feedback do usuário mitiga o risco.
- O site estático em S3 + CloudFront deve lidar com picos de tráfego; habilitar compressão e cache adequado garante desempenho consistente.
```

---

## 6. Melhorias futuras

Descreva como a solução poderia evoluir.

```md
Exemplo:
- Criar uma interface web para consulta.
- Criar um chat interno para perguntas sobre atas.
- Adicionar controle de acesso por departamento.
- Criar dashboard de decisões e pendências.
- Gerar alertas automáticos sobre ações em aberto.
- Integrar com ferramentas corporativas.
```

**Sua resposta:**

```md
Preencha aqui.
```

---

# 🧠 Checklist Final

Antes de entregar, confirme se sua solução responde:

- [ ] Como transformar documentos escaneados em texto?
- [ ] Como lidar com diferentes formatos dentro da mesma pasta `raw/`?
- [ ] Como armazenar os documentos originais?
- [ ] Como preservar a rastreabilidade entre resposta e documento fonte?
- [ ] Como organizar metadados?
- [ ] Como criar busca semântica?
- [ ] Como usar Amazon Bedrock na solução?
- [ ] Como proteger documentos sensíveis?
- [ ] Como monitorar falhas?
- [ ] Como a empresa usaria essa Wiki no dia a dia?

---

# 🏁 Conclusão

Escreva uma breve conclusão defendendo sua solução como se estivesse apresentando para uma liderança técnica ou de negócio.

**Sua resposta:**

```md
Preencha aqui.
```
