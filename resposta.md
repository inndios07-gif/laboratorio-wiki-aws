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
Preencha aqui.
```

---

## 3.2 Metadados propostos

Defina quais metadados você extrairia de cada documento.

| Metadado | Por que ele é importante? |
|---|---|
| Nome do documento | Preencha aqui |
| Tipo do documento | Preencha aqui |
| Data identificada | Preencha aqui |
| Tema principal | Preencha aqui |
| Participantes | Preencha aqui |
| Decisões tomadas | Preencha aqui |
| Responsáveis | Preencha aqui |
| Próximos passos | Preencha aqui |
| Nível de confidencialidade | Preencha aqui |
| Caminho do arquivo original | Preencha aqui |

Adicione outros metadados, se necessário.

---

## 3.3 Uso de IA para enriquecimento dos documentos

Explique como o Amazon Bedrock poderia ajudar a identificar temas, decisões, responsáveis, pendências e resumos dos documentos.

**Sua resposta:**

```md
Preencha aqui.
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
Preencha aqui.
```

---

# ✅ Quest 4: O Oráculo da Wiki Inteligente

## 4.1 Estratégia de indexação

Explique como os documentos seriam divididos em trechos menores e preparados para busca semântica.

**Sua resposta:**

```md
Preencha aqui.
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
Preencha aqui.
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
Preencha aqui.
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
Preencha aqui.
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
Preencha aqui.
```

---

# 🧩 Arquitetura Final da Solução

Agora reúna tudo em uma visão única.

## 1. Visão geral

Explique em poucas linhas a ideia central da sua arquitetura.

**Sua resposta:**

```md
Preencha aqui.
```

---

## 2. Serviços AWS utilizados

| Serviço AWS | Papel na solução |
|---|---|
| Amazon S3 | Preencha aqui |
| Amazon Textract | Preencha aqui |
| Amazon Bedrock | Preencha aqui |
| Amazon Bedrock Knowledge Bases | Preencha aqui |
| AWS Lambda | Preencha aqui |
| AWS Step Functions | Preencha aqui |
| Amazon CloudWatch | Preencha aqui |
| AWS IAM | Preencha aqui |
| AWS KMS | Preencha aqui |

Adicione, remova ou ajuste os serviços conforme sua proposta.

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
Preencha aqui.
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
Preencha aqui.
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
Preencha aqui.
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
