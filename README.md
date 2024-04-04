# Azure Cognitive Search: Utilizando AI Search para indexação e consulta de Dados

## Explore um índice do Azure AI Search (UI)

Vamos imaginar que você trabalha para a Fourth Coffee, uma rede nacional de cafés. Você foi solicitado a ajudar a criar uma solução de mineração de conhecimento que facilite a busca de insights sobre as experiências dos clientes. Você decide criar um índice do Azure AI Search usando dados extraídos de avaliações de clientes.

Neste laboratório você irá:

- Criar recursos do Azure
- Extrair dados de uma fonte de dados
- Enriqueça os dados com habilidades de IA
- Utilize o indexador do Azure no portal do Azure
- Consulte seu índice de pesquisa
- Revise os resultados salvos em uma Knowledge Store

### Recursos do Azure necessários

A solução que você criará para o Fourth Coffee requer os seguintes recursos na sua assinatura do Azure:

- Um recurso **do Azure AI Search**, que gerenciará a indexação e a consulta.
- Um recurso **de serviços de IA do Azure**, que fornece serviços de IA para habilidades que sua solução de pesquisa pode usar para enriquecer os dados na fonte de dados com insights gerados por IA.
- Uma **conta de armazenamento** com contêineres de blobs, que armazenará documentos brutos e outras coleções de tabelas, objetos ou arquivos.

#### **Crie um recurso do _Azure AI Search_**

1. Entre no [portal do Azure](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true).

2. Clique no botão **+ Criar um recurso**, pesquise _Azure AI Search_ e crie um recurso **Azure AI Search** com as seguintes configurações:

   - **Assinatura**: _sua assinatura do Azure_.
   - **Grupo de recursos (Resource group)**: _Selecione ou crie um grupo de recursos com um nome exclusivo_.
   - **Nome do serviço**: _um nome exclusivo_.
   - **Localização**: _Escolha qualquer região disponível_.
   - **Nível de preços**: _Básico_

3. Selecione **Review + create** e depois de ver a resposta **Validation Success**, selecione **Create**.

4. Após a conclusão da implantação, selecione **Ir para o recurso**. Na página de visão geral do Azure AI Search, você pode adicionar índices, importar dados e pesquisar índices criados.

#### **Crie um recurso de serviços de IA do Azure**

Você precisará provisionar um recurso **de serviços de IA do Azure** que esteja no mesmo local que seu recurso do Azure AI Search. Sua solução de pesquisa usará esse recurso para enriquecer os dados no armazenamento de dados com insights gerados por IA.

1. Retorne à página inicial do portal do Azure. Clique no botão **＋ Criar um recurso** e pesquise os _serviços de IA do Azure_. Selecione **criar** um plano **de serviços de IA do Azure**. Você será levado a uma página para criar um recurso de serviços de IA do Azure. Configure-o com as seguintes configurações:
   - **Assinatura**: _sua assinatura do Azure_.
   - **Grupo de recursos (Resource group)**: _O mesmo grupo de recursos que seu recurso do Azure AI Search_.
   - **Região**: _o mesmo local do recurso do Azure AI Search_.
   - **Nome**: _Um nome exclusivo_.
   - **Nível de preços**: Padrão S0
   - **Ao marcar esta caixa, confirmo que li e compreendi todos os termos abaixo**: Selecionado
2. Selecione **Revisar + criar**. Depois de ver a resposta **Validation Passed**, selecione **Create**.

3. Aguarde a conclusão da implantação e visualize os detalhes da implantação.

#### **Crie uma conta de armazenamento**

1. Retorne à página inicial do portal do Azure e selecione o botão **+ Criar um recurso**.
2. Procure _conta de armazenamento_ e crie um recurso **de conta de armazenamento** com as seguintes configurações:
   - **Assinatura**: _sua assinatura do Azure_.
   - **Grupo de recursos (Resource group)**: _O mesmo grupo de recursos que seu recurso do Azure AI Search e dos serviços Azure AI_.
   - **Nome da conta de armazenamento**: _Um nome exclusivo_.
   - **Localização**: _Escolha qualquer região disponível_.
   - **Desempenho**: Standard
   - **Redundância**: armazenamento localmente redundante (LRS)
3. Clique em **Revisar** e em **Criar**. Aguarde a conclusão da implantação e vá para o recurso implantado.
4. Na conta de Armazenamento do Azure que você criou, no painel de menu esquerdo, selecione **Configuration** (em **Settings**).
5. Altere a configuração de _Permitir acesso anônimo de Blob_ para **Habilitado** e selecione **Salvar**.

### Fazer upload de documentos para o armazenamento do Azure

1. No painel do menu esquerdo, selecione **Containers**.
   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/storage-blob-1.png)
2. Selecione **+ Container**. Um painel do seu lado direito é aberto.
3. Insira as seguintes configurações e clique em **Criar**:
   - **Nome**: coffee-reviews
   - **Nível de acesso público**: Container (acesso de leitura anônimo para containers e blobs)
   - **Avançado**: _sem alterações_.
4. Em uma nova guia do navegador, baixe as avaliações de café compactadas em https://aka.ms/mslearn-coffee-reviews e extraia os arquivos para a pasta _de avaliações_.
5. No portal do Azure, selecione o contêiner _de avaliações de café_. No contêiner, selecione **Carregar**.
   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/storage-blob-3.png)
6. No painel **Carregar blob**, selecione **Selecionar um arquivo**.
7. Na janela do Explorer, selecione **todos** os arquivos na pasta _de avaliações_, selecione **Abrir** e, em seguida, selecione **Carregar**.
   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/6a-azure-container-upload-files.png)
8. Depois que o upload for concluído, você poderá fechar o painel **Upload blob**. Seus documentos estão agora em seu contêiner de armazenamento _de avaliações de café_.

### Indexar os documentos

Depois de armazenar os documentos, você poderá usar o Azure AI Search para extrair insights dos documentos. O portal do Azure fornece um _assistente de importação de dados_. Com este assistente, você pode criar automaticamente um índice e um indexador para fontes de dados suportadas. Você usará o assistente para criar um índice e importar seus documentos de pesquisa do armazenamento para o índice do Azure AI Search.

1.  No portal do Azure, navegue até o recurso Azure AI Search. Na página **Visão geral**, selecione **Importar dados**.
    ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/azure-search-wizard-1.png)
2.  Na página **Conectar-se aos seus dados**, na lista **Fonte de Dados**, selecione **Azure Blob Storage**. Preencha os detalhes do armazenamento de dados com os seguintes valores:

    - **Fonte de dados**: Armazenamento de Blobs do Azure
    - **Nome da fonte de dados**: coffee-customer-data
    - **Dados a extrair**: Conteúdo e metadados
    - **Modo de análise**: Default (Padrão)
    - **Cadeia de conexão**: \*Selecione **Escolha uma conexão existente**. Selecione sua conta de armazenamento, selecione o contêiner **de avaliações de café** e clique em **Selecionar**.
    - **Autenticação de identidade gerenciada**: Nenhuma
    - **Nome do contêiner**: _esta configuração é preenchida automaticamente depois que você escolhe uma conexão existente_.
    - **Pasta Blob**: _deixe em branco_.
    - **Descrição**: Avaliações sobre Fourth Coffee Shops.

3.  Selecione **Próximo**: **Adicionar habilidades cognitivas (opcional)**.

4.  Na seção **Anexar Serviços Cognitivos**, selecione o seu recurso de serviços Azure AI.

5.  Na seção **Adicionar enriquecimentos**:

    - Altere o **Skillset** para **coffee-skillset**.
    - Marque a caixa de seleção **Habilitar OCR e mesclar todo o texto no campo merged_content**.
    - Certifique-se de que o **campo Dados de origem** esteja configurado como **merged_content**.
    - Altere o **nível de granularidade de enriquecimento** para **Páginas (blocos de 5.000 caracteres)**.
    - Não selecione _Habilitar enriquecimento incremental_

    - Selecione os seguintes campos enriquecidos:

      | **Habilidade Cognitiva**      | **Parâmetro** | **Nome do campo** |
      | ----------------------------- | ------------- | ----------------- |
      | Extract location names        |               | locations         |
      | Extract key phrases           |               | keyphrases        |
      | Detect sentiment              |               | sentiment         |
      | Generate tags from images     |               | imageTags         |
      | Generate captions from images |               | imageCaption      |

6.  Em **Salvar enriquecimentos em um armazenamento de conhecimento**, selecione:

    - Projeções de imagem
    - Documentos
    - Páginas
    - Frases chave
    - Entidades
    - Detalhes da imagem
    - Referências de imagem

! **Nota** Se aparecer um aviso solicitando uma **cadeia de conexão de conta de armazenamento**.

- Selecione **Escolha uma conexão existente**. Escolha a conta de armazenamento que você criou anteriormente.
- Clique em **+ Container** para criar um novo contêiner chamado **armazenamento de conhecimento** com o nível de privacidade definido como **Private** e selecione **Create**.
- Selecione o contêiner **de armazenamento de conhecimento** e clique em **Selecionar** na parte inferior da tela.

![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/6a-azure-cognitive-search-enrichments-warning.png)

7. Selecione **projeções de blob do Azure: Documento**. Uma configuração para o nome do contêiner com as exibições preenchidas automaticamente do contêiner _de armazenamento de conhecimento_. Não altere o nome do contêiner.

8. Selecione **Próximo: Personalizar índice de destino**. Altere o **nome do índice** para **coffee-index**.

9. Certifique-se de que a **chave** esteja configurada como **metadata_storage_path**. Deixe **o nome do sugeridor** em branco e **o modo de pesquisa** preenchido automaticamente.

10. Revise as configurações padrão dos campos de índice. Selecione **filtrável** para todos os campos que já estão selecionados por padrão.

    ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/6a-azure-cognitive-search-customize-index.png)

11. Selecione **Próximo: Criar um indexador**.

12. Altere o nome do **indexador** para **coffee-indexer**.

13. Deixe o **Schedule** definida como **Once**.

14. Expanda as **opções avançadas**. Certifique-se de que a opção **Base-64 Encode Keys** esteja selecionada, pois as chaves de codificação podem tornar o índice mais eficiente.

15. Selecione **Enviar** para criar a fonte de dados, o conjunto de habilidades, o índice e o indexador. O indexador é executado automaticamente e executa o pipeline de indexação, que:
    - Extrai os campos de metadados do documento e o conteúdo da fonte de dados.
    - Executa o conjunto de habilidades cognitivas para gerar campos mais enriquecidos.
    - Mapeia os campos extraídos para o índice.
16. Volte à página de recursos do Azure AI Search. No painel esquerdo, em **Gerenciamento de pesquisa**, selecione **Indexers**. Selecione o **coffee-indexer** recém-criado. Espere um minuto e selecione **↻ Atualize** até que o **Status** indique sucesso.

17. Selecione o nome do indexador para ver mais detalhes.

    ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/6a-search-indexer-success.png)

### Consultar o índice

Use o Search Explorer para escrever e testar consultas. O explorador de pesquisa é uma ferramenta incorporada no portal do Azure que oferece uma maneira fácil de validar a qualidade do seu índice de pesquisa. Você pode usar o Search Explorer para escrever consultas e revisar resultados em JSON.

1. Na página _Visão geral_ do serviço de pesquisa, selecione **Explorador de pesquisa** na parte superior da tela.
   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/5-exercise-screenshot-7.png)
2. Observe como o índice selecionado é o índice de café que você criou. Abaixo do índice selecionado, altere a visualização para **JSON view**.
   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/search-explorer-query.png)

No campo **do editor de consultas JSON**, copie e cole:

```.json
{
    "search": "*",
    "count": true
}
```

1. Selecione **Pesquisar**. A consulta de pesquisa retorna todos os documentos no índice de pesquisa, incluindo uma contagem de todos os documentos no campo **@odata.count**. O índice de pesquisa deve retornar um documento JSON contendo os resultados da pesquisa.

2. Agora vamos filtrar por localização. No campo **do editor de consultas JSON**, copie e cole:
   ```.json
   {
   "search": "locations:'Chicago'",
   "count": true
   }
   ```
3. Selecione **Pesquisar**. A consulta pesquisa todos os documentos no índice e filtra revisões com localização em Chicago. Você deveria ver **3** no campo **@odata.count**.

4. Agora vamos filtrar por sentimento. No campo **do editor de consultas JSON**, copie e cole:

   ```.json
   {
   "search": "sentiment:'negative'",
   "count": true
   }
   ```

5. elecione **Pesquisar**. A consulta pesquisa todos os documentos no índice e filtra revisões com sentimento negativo. Você deveria ver **1** no campo **@odata.count**.

6. Um dos problemas que podemos querer resolver é por que pode haver certas avaliações. Vamos dar uma olhada nas frases-chave associadas à avaliação negativa. O que você acha que pode ser a causa da revisão?

### Revise o armazenamento de conhecimento

Vamos ver o poder do armazenamento de conhecimento em ação. Ao executar o assistente Importar dados , você também criou um armazenamento de conhecimento. Dentro do armazenamento de conhecimento, você encontrará os dados enriquecidos extraídos pelas habilidades de IA que persistem na forma de projeções e tabelas.

1. No portal do Azure, navegue de volta para a sua conta de armazenamento do Azure.

2. No painel do menu esquerdo, selecione **Containers**. Selecione o contêiner **knowledge-store**.

   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/knowledge-store-blob-0.png)

3. Selecione qualquer um dos itens e clique no arquivo **objectprojection.json**.

   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/knowledge-store-blob-1.png)

4. Selecione **Editar** para ver o JSON produzido para um dos documentos do seu armazenamento de dados do Azure..

   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/knowledge-store-blob-2.png)

5. Selecione a localização atual do blob de armazenamento no canto superior esquerdo da tela para retornar à conta de armazenamento _Containers_.

   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/knowledge-store-blob-4.png)

6. Em _Containers_, selecione o contêiner _coffee-skillset-image-projection_. Selecione qualquer um dos itens.

   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/knowledge-store-blob-5.png)

7. Selecione qualquer um dos arquivos _.jpg_. Selecione **Editar** para ver a imagem armazenada no documento. Observe como todas as imagens dos documentos são armazenadas desta forma.

   ![](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/media/create-cognitive-search-solution/knowledge-store-blob-3.png)

8. Selecione a localização atual do blob de armazenamento no canto superior esquerdo da tela para retornar à conta de armazenamento _Containers_.

9. Selecione **Storage browser** no painel esquerdo e selecione **Tabelas**. Há uma tabela para cada entidade no índice. Selecione a tabela _coffeeSkillsetKeyPhrases_.

Observe as frases-chave que o armazenamento de conhecimento conseguiu capturar do conteúdo das avaliações. Muitos dos campos são chaves, portanto você pode vincular as tabelas como um banco de dados relacional. O último campo mostra as frases-chave que foram extraídas pelo conjunto de habilidades.

#

_Instruções de uso dos laboratórios extraídas e traduzidas para pt-BR do seguinte link:_

*https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/11-ai-search.html*

# O que é a IA do Azure Search?

## Dentro de um serviço de pesquisa

No próprio serviço de pesquisa, as duas principais cargas de trabalho são indexação e consulta.

- Indexação é um processo de entrada que carrega o conteúdo no seu serviço de pesquisa e o torna pesquisável. Internamente, o texto de entrada é processado em tokens e armazenado em índices invertidos, enquanto os vetores de entrada são armazenados em índices de vetor. O formato de documento que a IA do Azure Search pode indexar é o JSON. Você pode carregar documentos JSON que você montou ao usar um indexador para recuperar e serializar seus dados em JSON.
  O enriquecimento de IA por meio de habilidades cognitivas é uma extensão da indexação. Se você tiver imagens ou texto grande não estruturado no documento de origem, poderá anexar habilidades que executam o OCR, descrever imagens, inferir estrutura, traduzir texto e muito mais. Você também pode anexar habilidades que executam agrupamento e vetorização de dados.

- A Consulta pode ocorrer depois que um índice é preenchido com conteúdo pesquisável, quando seu aplicativo cliente envia solicitações de consulta para um serviço de pesquisa e processa as respostas. Todas as execuções de consulta são feitas sobre um índice de pesquisa que você controla.
  A classificação semântica é uma extensão da execução da consulta. Ela adiciona o reconhecimento de linguagem ao processamento dos resultados da pesquisa, promovendo os resultados mais semanticamente relevantes para o topo.

## Por que usar a IA do Azure Search?

A IA do Azure Search é adequada para os seguintes cenários de aplicação:

- Use-a para pesquisa de texto completo tradicional e pesquisa de similaridade de vetor de última geração. Volte seus aplicativos de IA generativos com a recuperação de informações que aproveita a força da palavra-chave e da pesquisa de similaridade.Apoie seus aplicativos de IA generativa com a recuperação de informações que aproveita a capacidade da pesquisa por palavra-chave e similaridade. Use ambas as modalidades para recuperar os resultados mais relevantes.

- Consolide o conteúdo heterogêneo em um índice de pesquisa definido pelo usuário e preenchido composto por vetores e texto. Você possui e controla o que é pesquisável.

- Integrar agrupamento de dados e vetorização para IA generativa e aplicativos RAG.

- Aplicar controle de acesso granular no nível do documento.

- Descarregue as cargas de trabalho de indexação e consulta em um serviço de pesquisa dedicado.

- Implemente facilmente recursos relacionados a pesquisa: ajuste de relevância, faceted navigation, filtros (incluindo pesquisa geoespacial), mapeamento de sinônimos e preenchimento automático.

- Transforme grandes arquivos de texto ou de imagem não diferenciados ou arquivos de aplicativo armazenados no Armazenamento de Blobs do Azure ou no Azure Cosmos DB em partes pesquisáveis. Isso é obtido durante a indexação por meio de habilidades cognitivas que adicionam processamento externo de IA do Azure.

- Adicione análise linguística ou de texto personalizado. Se você tiver conteúdo que não está em inglês, a IA do Azure Search oferecerá suporte aos analisadores Lucene e aos processadores de linguagem natural da Microsoft. Você também pode configurar analisadores para obter processamento especializado de conteúdo bruto, como filtragem de sinais diacríticos ou reconhecimento e preservação de padrões em cadeias de caracteres.

_Conteúdo extraído do seguinte link:_

*https://learn.microsoft.com/pt-br/azure/search/search-what-is-azure-search*
