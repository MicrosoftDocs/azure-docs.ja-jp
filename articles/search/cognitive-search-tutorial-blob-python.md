---
title: チュートリアル:Azure Blob に対する Python と AI
titleSuffix: Azure Cognitive Search
description: Jupyter Python ノートブックと Azure Cognitive Search REST API を使用した、Blob Storage のコンテンツに対するテキスト抽出と自然言語処理の例を、順を追って説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788084"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>チュートリアル:Python と AI を使用して Azure Blob から検索可能なコンテンツを生成する

Azure Blob Storage に非構造化テキストまたは画像がある場合、[AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)で情報を抽出し、フルテキスト検索やナレッジ マイニングのシナリオに役立つ新しいコンテンツを作成することができます。 パイプラインでは画像を処理できますが、この Python チュートリアルではテキストに焦点を当て、言語検出と自然言語処理を適用して、クエリ、ファセット、フィルターで活用できる新しいフィールドを作成します。

このチュートリアルでは、Python と [Search REST API](/rest/api/searchservice/) を使用して次のタスクを実行します。

> [!div class="checklist"]
> * まずは、Azure Blob Storage で、PDF、HTML、DOCX、PPTX などのドキュメント全体 (非構造化テキスト) から始める。
> * テキストの抽出、言語の検出、エンティティの認識、キー フレーズの検出を行うパイプラインを定義する。
> * 出力 (生コンテンツと、パイプラインで生成された名前と値のペア) を格納するためのインデックスを定義する。
> * 変換と分析を開始し、インデックスを作成して読み込むパイプラインを実行する。
> * フルテキスト検索と豊富なクエリ構文を使用して結果を探索する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

+ [Azure ストレージ](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [作成](search-create-service-portal.md)または[既存の検索サービスの用意](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> このチュートリアルには無料のサービスを使用できます。 無料の検索サービスでは、3 つのインデックス、3 つのインデクサー、3 つのデータ ソースという制限があります。 このチュートリアルでは、それぞれ 1 つずつ作成します。 開始する前に、ご利用のサービスに新しいリソースを受け入れる余地があることを確認してください。

## <a name="download-files"></a>ファイルのダウンロード

1. こちらの [OneDrive フォルダー](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)を開き、左上隅の **[ダウンロード]** をクリックして、コンピューターにファイルをコピーします。 

1. ZIP ファイルを右クリックし、 **[すべて展開]** を選択します。 さまざまな種類のファイルが 14 個あります。 この演習では、7 個を使用します。

## <a name="1---create-services"></a>1 - サービスを作成する

このチュートリアルでは、インデックス作成とクエリに Azure Cognitive Search を使用するほか、AI エンリッチメントにはバックエンドで Cognitive Services を、データ提供には Azure Blob Storage を使用します。 このチュートリアルは、Cognitive Services の無料割り当て分 (インデクサーあたり 1 日に 20 トランザクション) を超えないようにしているため、作成する必要のあるサービスは Search と Storage だけです。

可能である場合は、近接性と管理性を高めるために、両方を同じリージョンおよびリソース グループ内に作成してください。 実際には、Azure Storage アカウントは任意のリージョンに置くことができます。

### <a name="start-with-azure-storage"></a>Azure Storage の使用を開始する

1. [Azure portal にサインイン](https://portal.azure.com/)し、 **[+ リソースの作成]** をクリックします。

1. *[ストレージ アカウント]* を検索し、Microsoft のストレージ アカウント オファリングを選択します。

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="ストレージ アカウントの作成" border="false":::

1. [基本] タブでは、次の項目が必要です。 それ以外のすべてのものには、既定値をそのまま使用します。

   + **リソース グループ**。 既存のものを選択するか、新しいものを作成します。ただし、すべてのサービスに同じグループを使用して、それらをまとめて管理できるようにします。

   + **ストレージ アカウント名**。 同じ種類のリソースが複数存在することになると考えられる場合は、名前を使用して、種類とリージョンを基に区別が付くようにします (たとえば、*blobstoragewestus*)。 

   + **場所**。 可能であれば、Azure Cognitive Search と Cognitive Services に使用するのと同じ場所を選択します。 1 つの場所であれば、帯域幅の料金がかかりません。

   + **アカウントの種類**。 既定値の *[StorageV2 (general purpose v2)]\(StorageV2 (汎用 v2)\)* を選択します。

1. **[確認および作成]** をクリックしてサービスを作成します。

1. 作成されたら、 **[Go to the resource]\(リソースに移動\)** をクリックして [概要] ページを開きます。

1. **[BLOB]** サービスをクリックします。

1. **[+ コンテナー]** をクリックしてコンテナーを作成し、*cog-search-demo* という名前を付けます。

1. *[cog-search-demo]* を選択し、 **[アップロード]** をクリックして、ダウンロード ファイルを保存したフォルダーを開きます。 画像以外のすべてのファイルを選択します。 7 個のファイルがあるはずです。 **[OK]** をクリックしてアップロードします。

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="サンプル ファイルをアップロードする" border="false":::

1. Azure Storage を終了する前に、Azure Cognitive Search で接続を作成できるように、接続文字列を取得します。 

   1. 自分のストレージ アカウント (例として *blobstragewestus* を使用しています) の [概要] ページに戻ります。 
   
   1. 左側のナビゲーション ウィンドウで、 **[アクセス キー]** を選択し、いずれかの接続文字列をコピーします。 

   接続文字列は、次の例のような URL です。

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. メモ帳に接続文字列を保存します。 これは、後でデータ ソース接続を設定するときに必要になります。

### <a name="cognitive-services"></a>Cognitive Services

AI エンリッチメントは、自然言語と画像の処理のための Text Analytics や Computer Vision など、Cognitive Services によってサポートされています。 実際のプロトタイプまたはプロジェクトを完成させることが目的であれば、この時点で (Azure Cognitive Search と同じリージョンに) Cognitive Services をプロビジョニングして、インデックス作成操作にアタッチできるようにします。

このチュートリアルで使用するトランザクションは 7 つのみです。Azure Cognitive Search が Cognitive Services に接続して、インデクサーの実行あたり 20 個の無料トランザクションを利用できるため、リソースのプロビジョニングはスキップできます。 無料の割り当てで十分です。 より大規模なプロジェクトの場合は、従量課金制の S0 レベルで Cognitive Services をプロビジョニングすることを計画してください。 詳細については、[Cognitive Services のアタッチ](cognitive-search-attach-cognitive-services.md)に関するページを参照してください。

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

3 番目のコンポーネントは Azure Cognitive Search であり、[ポータルで作成](search-create-service-portal.md)できます。 このチュートリアルは Free レベルを使用して完了できます。 

Azure Blob Storage と同様に、アクセス キーを収集してください。 さらに、要求の構築を始めるときに、各要求の認証に使用されるエンドポイントと管理者 API キーを指定する必要があります。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search のための管理者 API キーと URL を取得する

1. [Azure portal にサインイン](https://portal.azure.com/)し、自分の検索サービスの **[概要]** ページで、自分の検索サービスの名前を確認します。 エンドポイント URL を見ることで、自分のサービス名を確かめることができます。 エンドポイント URL が `https://mydemo.search.windows.net` だったら、自分のサービス名は `mydemo` になります。

2. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   クエリ キーも入手します。 読み取り専用アクセスを使用してクエリ要求を発行することをお勧めします。

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="サービス名、管理キー、クエリ キーの取得" border="false":::

すべての要求で、自分のサービスに送信される各要求のヘッダーに API キーが必要になります。 有効なキーにより、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼が確立されます。

## <a name="2---start-a-notebook"></a>2 - ノートブックを起動する

以降の手順に従ってノートブックを作成するか、完成したノートブックを [Azure-Search-python-samples リポジトリ](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)からダウンロードします。

Jupyter Notebook を起動して新しい Python 3 ノートブックを作成するには、Anaconda Navigator を使用します。

ノートブックでこのスクリプトを実行し、JSON を操作して HTTP 要求を作成するために使用するライブラリを読み込みます。

```python
import json
import requests
from pprint import pprint
```

同じノートブックで、データ ソース、インデックス、インデクサー、スキルセットの名前を定義します。 このスクリプトを実行して、このチュートリアル用の名前を設定します。

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

次のスクリプトでプレースホルダーをご利用の Search サービス (YOUR-SEARCH-SERVICE-NAME) と管理者 API キー (YOUR-ADMIN-API-KEY) に置き換えたうえで、スクリプトを実行して、Search サービスのエンドポイントを設定します。

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 - パイプラインを作成する

Azure Cognitive Search では、AI 処理はインデックス作成 (またはデータ インジェスト) 中に行われます。 チュートリアルのこの部分では、データ ソース、インデックス定義、スキルセット、インデクサーという 4 つのオブジェクトを作成します。 

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

[データ ソース オブジェクト](/rest/api/searchservice/create-data-source)は、ファイルが格納されている BLOB コンテナーへの接続文字列を提供します。

次のスクリプトで、YOUR-BLOB-RESOURCE-CONNECTION-STRING というプレースホルダーを、前の手順で作成した BLOB 用の接続文字列に置き換えます。 コンテナーのプレースホルダー テキストを置き換えます。 次に、スクリプトを実行して、`cogsrch-py-datasource` という名前のデータ ソースを作成します。

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

この要求に対し、成功を確認する状態コード 201 が返されます。

Azure portal 内の Search サービスのダッシュボード ページに戻り、cogsrch-py-datasource が **[データ ソース]** 一覧内に出現していることを確認します。 **[最新の情報に更新]** をクリックしてページを更新します。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="ポータルの [データ ソース] タイル" border="false":::

### <a name="step-2-create-a-skillset"></a>手順 2:スキルセットを作成する

この手順では、データに適用するエンリッチメント ステップのセットを定義します。 それぞれのエンリッチメント ステップを *スキル* と呼び、エンリッチメント ステップのセットを *スキルセット* と呼びます。 このチュートリアルでは、スキルセット用に次の[ビルトイン コグニティブ スキル](cognitive-search-predefined-skills.md)を使用します。

+ [エンティティの認識](cognitive-search-skill-entity-recognition.md)。BLOB コンテナーのコンテンツから組織の名前を抽出します。

+ [言語検出](cognitive-search-skill-language-detection.md)。コンテンツの言語を識別します。

+ [テキスト分割](cognitive-search-skill-textsplit.md)。キー フレーズの抽出スキルを呼び出す前に、大きいコンテンツを小さいチャンクに分割します。 キー フレーズ抽出は、50,000 文字以下の入力を受け入れます。 いくつかのサンプル ファイルは、分割してこの制限内に収める必要があります。

+ [キー フレーズ抽出](cognitive-search-skill-keyphrases.md)。上位のキー フレーズを抜き出します。 

次のスクリプトを実行して、`cogsrch-py-skillset` というスキルセットを作成します。

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

この要求に対し、成功を確認する状態コード 201 が返されます。

キー フレーズ抽出スキルは、各ページで適用されます。 コンテキストを `"document/pages/*"` に設定することで、ドキュメントまたはページの配列のメンバーごとに (ドキュメント内のページごとに) このエンリッチャーを実行します。

各スキルは、ドキュメントのコンテンツで実行されます。 処理中に、Azure Cognitive Search が各ドキュメントを解析して、さまざまなファイル形式からコンテンツを読み取ります。 ソース ファイルで見つかったテキストは、ドキュメントごとに 1 つずつ、`content` フィールドに配置されます。 そのため、入力を `"/document/content"` として設定します。

スキルセットのグラフィカル表示を以下に示します。

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="スキルセットを理解する" border="false":::

出力は、言語コードと同様に、インデックスにマッピングするか、ダウンストリーム スキルへの入力として使用できます (その両方も可能です)。 インデックスでは、言語コードはフィルター処理に役立ちます。 入力としての言語コードは、テキスト分析スキルによって、単語区切りに基づく言語学的規則を通知するために使用されます。

スキルセットの基礎の詳細については、[スキルセットを定義する方法](cognitive-search-defining-skillset.md)に関するページをご覧ください。

### <a name="step-3-create-an-index"></a>手順 3:インデックスを作成する

このセクションでは、検索可能なインデックスがフィールドに含まれるように指定し、各フィールドに検索属性を設定することで、インデックス スキーマを定義します。 フィールドには型があり、フィールドの使用方法 (検索可能、並べ替え可能など) を決定する属性を取ることができます。 インデックス内のフィールド名は、ソース内のフィールド名と完全に一致する必要はありません。 後のほうの手順では、インデクサーにフィールド マッピングを追加して、ソースとターゲットのフィールドを接続します。 この手順で、検索アプリケーションに関連するフィールドの名前付け規則を使用してインデックスを定義します。

この演習では、次のフィールドとフィールドの型を使用します。

| field-names: | ID         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

このスクリプトを実行して、`cogsrch-py-index` という名前のインデックスを作成します。

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

この要求に対し、成功を確認する状態コード 201 が返されます。

インデックスの定義の詳細については、[インデックスの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) に関するページを参照してください。

### <a name="step-4-create-and-run-an-indexer"></a>手順 4:インデクサーの作成と実行

[インデクサー](/rest/api/searchservice/create-indexer)は、パイプラインを駆動するものです。 これまでに作成した 3 つのコンポーネント (データ ソース、スキルセット、インデックス) は、インデクサーへの入力です。 Azure Cognitive Search でのインデクサーの作成は、パイプライン全体の動作を引き起こすイベントです。 

これらのオブジェクトをまとめてインデクサーに結び付けるには、フィールド マッピングを定義する必要があります。

+ スキルセットの前に `"fieldMappings"` が処理されて、データ ソースのソース フィールドがインデックス内のターゲット フィールドにマッピングされます。 フィールド名と型が両側で共通していれば、マッピングは必要ありません。

+ `"outputFieldMappings"` は、スキルセットの後に処理されます。`"sourceFieldNames"` がドキュメント解析またはエンリッチメントによって作成されるまでは、存在しない sourceFieldNames が参照されます。 `"targetFieldName"` は、インデックス内のフィールドです。

入力を出力につなぐことに加え、フィールドのマッピングを使用して、データ構造をフラット化することもできます。 詳細については、「[強化されたフィールドを検索可能なインデックスにマップする方法](cognitive-search-output-field-mapping.md)」を参照してください。

このスクリプトを実行して、`cogsrch-py-indexer` という名前のインデクサーを作成します。

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

この要求に対し、すぐに状態コード 201 が返されますが、処理が完了するまでに数分かかる場合があります。 データ セットが小規模でも、画像分析などの分析スキルは計算を集中的に行うので時間がかかります。

インデクサーが実行中であるか完了したかは、[インデクサーの状態を監視](#check-indexer-status)することで判別できます。

> [!TIP]
> インデクサーを作成すると、パイプラインが呼び出されます。 データへのアクセス、入力と出力のマッピング、操作の順序のいずれかに問題がある場合は、この段階で表示されます。 コードまたはスクリプトに変更を加えてパイプラインを再実行するには、最初にオブジェクトを削除しなければならない場合があります。 詳細については、「[リセットして再実行する](#reset)」をご覧ください。

#### <a name="about-the-request-body"></a>要求本文について

スクリプトで `"maxFailedItems"` を -1 に設定します。これにより、インデックス作成エンジンに、データのインポート中のエラーを無視するよう指示します。 デモのデータ ソースにはドキュメントがほとんどないため、これは便利です。 大きいデータ ソースの場合は、この値を 0 より大きい値に設定します。

また、構成パラメーター内の `"dataToExtract":"contentAndMetadata"` ステートメントにも注意してください。 このステートメントは、さまざまなファイル形式と、各ファイルに関連するメタデータからコンテンツを抽出するようにインデクサーに指示します。

コンテンツが抽出されるときに、`imageAction` を設定して、データ ソース内にある画像からテキストを抽出することができます。 `"imageAction":"generateNormalizedImages"` 構成は、OCR スキルおよびテキスト マージ スキルと組み合わされて、イメージからテキスト (たとえば、一時停止の道路標識から "stop" という単語) を抽出し、それをコンテンツ フィールドの一部として埋め込むよう、インデクサーに指示します。 この動作は、ドキュメントに埋め込まれている画像 (PDF 内の画像など) と、データ ソース内にある画像 (JPG ファイルなど) の両方に適用されます。

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - インデックス作成を監視する

インデクサーを定義すると、要求を送信するときに自動的に実行されます。 定義したコグニティブ スキルによっては、インデックス作成に予想よりも時間がかかる場合があります。 インデクサーの処理が完了したかどうかを確認するには、次のスクリプトを実行します。

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

応答内で、`"lastResult"` の `"status"` と `"endTime"` の値を確認します。 状態を確認するため、定期的にスクリプトを実行します。 インデクサーが完了すると、状態は "success" に設定され、"endTime" が指定されます。また、応答にはエンリッチメント中に発生したエラーや警告が含まれるようになります。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="インデクサーが作成された" border="false":::

警告は、一部のソース ファイルとスキルの組み合わせではよく見られ、必ずしも問題を示すわけではありません。 警告の多くは無害です。 たとえば、テキストを含んでいない JPEG ファイルのインデックスを作成しようとすると、次のスクリーンショットに示すような警告が表示されます。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="インデクサーの警告の例" border="false":::

## <a name="5---search"></a>5 - 検索する

インデックス作成が完了したら、個々 のフィールドの内容を返すクエリを実行します。 既定では、Azure Cognitive Search によって上位 50 件の結果が返されます。 サンプル データは小さいため、既定値で問題なく動作します。 ただし、より大きなデータ セットを使用する場合は、より多くの結果が返されるよう、クエリ文字列にパラメーターを含める必要がある場合があります。 手順については、[Azure Cognitive Search における結果の改ページ位置の自動修正の方法](search-pagination-page-layout.md)に関するページを参照してください。

検証手順として、インデックスの定義を取得して、すべてのフィールドを表示します。

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果は次の例のようになります。 スクリーンショットに表示されているのは、応答の一部のみです。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="すべてのフィールドのインデックスの照会" border="false":::

出力は、インデックス スキーマと、各フィールドの名前、型、および属性です。

`"*"` の 2 番目のクエリを送信して、`organizations` などの 1 つのフィールドのすべての内容を返します。

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果は次の例のようになります。 スクリーンショットに表示されているのは、応答の一部のみです。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="組織のコンテンツを取得するクエリをインデックスに対して実行する" border="false":::

その他のフィールド (この演習では、`content`、`languageCode`、`keyPhrases`、`organizations`) でも同様に繰り返します。 コンマ区切りリストを使用して、`$select` を介して複数のフィールドを返すことができます。

クエリ文字列の複雑さと長さによっては、GET や POST を使用できます。 詳細については、[REST API を使用したクエリ](/rest/api/searchservice/search-documents)に関するページをご覧ください。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>リセットして再実行する

開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Cognitive Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

ポータルを使用して、インデックス、インデクサー、データ ソース、およびスキルセットを削除できます。 インデクサーを削除するときは、必要に応じて、インデックス、スキルセット、およびデータ ソースを選択して同時に削除できます。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="ポータル内で検索オブジェクトを削除する" border="false":::

それらはスクリプトを使用して削除することもできます。 次のスクリプトは、スキルセットを削除する方法を示しています。 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

正常に削除されると、状態コード 204 が返されます。

## <a name="takeaways"></a>重要なポイント

このチュートリアルでは、構成要素 (データ ソース、スキルセット、インデックス、およびインデクサー) の作成によってエンリッチされたインデックス作成パイプラインを作成するための、基本的な手順を示します。

[組み込みのスキル](cognitive-search-predefined-skills.md)については、スキルセットの定義のほか、入力と出力を介したスキルの連結方法と共に説明しました。 また、Azure Cognitive Search サービス上の検索可能なインデックスに対し、エンリッチされた値をパイプラインからルーティングするには、インデクサーの定義に `outputFieldMappings` が必要であることも学習しました。

最後に、結果をテストし、今後のイテレーションのためにシステムをリセットする方法について学習しました。 インデックスに対するクエリを発行すると、エンリッチされたインデックス作成パイプラインによって作成された出力が返されることを学習しました。 このリリースには、内部構造 (システムによって作成されるエンリッチされたドキュメント) を表示するためのメカニズムがあります。 また、インデクサーの状態を確認する方法と、パイプラインを再実行する前に削除すべきオブジェクトについても学習しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

所有するサブスクリプションを使用している場合は、プロジェクトの終了時に、不要になったリソースを削除することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ペインにある [すべてのリソース] または [リソース グループ] リンクを使って、リソースを検索および管理できます。

## <a name="next-steps"></a>次のステップ

AI エンリッチメント パイプラインのオブジェクトをすべて理解したら、スキルセットの定義と個々のスキルについて詳しく見てみましょう。

> [!div class="nextstepaction"]
> [スキルセットを作成する方法](cognitive-search-defining-skillset.md)