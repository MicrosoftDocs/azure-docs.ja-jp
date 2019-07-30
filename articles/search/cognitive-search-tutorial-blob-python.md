---
title: 'Python のチュートリアル: インデックス パイプラインで Cognitive Services を呼び出す - Azure Search'
description: Jupyter Python ノートブックを使用した Azure Search でのデータ抽出、自然言語、画像の AI 処理の例を順に確認します。 抽出されたデータにはインデックスが付けられ、クエリで簡単にアクセスできるようになります。
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: b7f1baa473ca28db696835a7b0895f1603c74770
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359393"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Python のチュートリアル: Azure Search のインデックス パイプラインで Cognitive Services API を呼び出す

このチュートリアルでは、*コグニティブ スキル*を使用した Azure Search でのデータ エンリッチメントのプログラミングのしくみを学習します。 複数のスキルが、Cognitive Services の自然言語処理 (NLP) と画像分析機能によって支えられています。 スキルセットを複合および構成することで、画像やスキャンされたドキュメント ファイルのテキストとテキスト表現を抽出できます。 また、言語、エンティティ、キーフレーズなども検出できます。 結果として、インデックス パイプライン内に作成された AI エンリッチメントを備える豊富な追加コンテンツが、Azure Search インデックスに追加されます。 

このチュートリアルでは、Python を使用して以下のタスクを行います。

> [!div class="checklist"]
> * インデックスへのルートでサンプル データをエンリッチする、インデックス作成パイプラインを作成する
> * 組み込みのスキル (エンティティの認識、言語の検出、テキスト操作、キー フレーズ抽出) を適用する
> * スキルセットの出力に入力をマップして、スキルをまとめて連結する方法を学習する
> * 要求を実行し、結果を確認する
> * 将来の開発のためにインデックスとインデクサーをリセットする

出力は、Azure Search のフルテキスト検索可能なインデックスです。 インデックスは、[シノニム](search-synonyms.md)、[スコアリング プロファイル](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[アナライザー](search-analyzers.md)、および[フィルター](search-filters.md)などの他の標準的な機能を使って強化できます。 

このチュートリアルは無料のサービスで実行されますが、無料のトランザクションの数は 1 日あたり 20 のドキュメントまでに制限されます。 このチュートリアルを同じ日に複数回実行する場合は、より小さなファイル セットを使用して、より多くの実行が制限内に収まるようにします。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Search のドキュメントクラッキング段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Search の価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、次のサービス、ツール、およびデータを使用します。 

+ サンプル データの格納のための [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。 ストレージ アカウントが Azure Search と同じリージョンにあることを確認します。

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)。これによって、Python 3.x と Jupyter Notebooks が提供されます。

+ [サンプル データ](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)は、さまざまなタイプの小さいファイル セットで構成されています。 

+ [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

Azure Search サービスを使用するには、サービスの URL とアクセス キーが必要になります。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-postman/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーにより、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼が確立されます。

## <a name="prepare-sample-data"></a>サンプル データの準備

エンリッチメント パイプラインは、Azure データ ソースから取得されます。 ソース データは、サポートされているデータ ソースの種類の [Azure Search インデクサー](search-indexer-overview.md)から取得する必要があります。 Cognitive Search では Azure Table Storage はサポートされていません。 この演習では、BLOB ストレージを使用して複数のコンテンツ タイプを示します。

1. [Azure portal にサインインし](https://portal.azure.com)、Azure ストレージ アカウントに移動して **[BLOB]** をクリックし、 **[+ コンテナー]** をクリックします。

1. [BLOB コンテナーを作成](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)してサンプル データを含めます。 パブリック アクセス レベルは、有効な任意の値に設定できます。

1. コンテナーの作成後、それを開いてコマンド バーの **[アップロード]** を選択し、前の手順でダウンロードしたサンプル ファイルをアップロードします。

   ![Azure Blob Storage 内のソース ファイル](./media/cognitive-search-quickstart-blob/sample-data.png)

1. サンプル ファイルが読み込まれたら、BLOB ストレージのコンテナー名と接続文字列を取得します。 これは、Azure portal でストレージ アカウントに移動して実行できます。 **[アクセス キー]** をクリックし、 **[接続文字列]** フィールドをコピーします。

この接続文字列の形式は次のとおりです: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

接続文字列は手元に記録しておいてください。 後の手順で必要になります。

共有アクセス署名の提供など、接続文字列を指定する方法は他にもあります。 データ ソースの資格情報の詳細については、「[Azure Blob Storage のインデックスを Azure Search で作成する](search-howto-indexing-azure-blob-storage.md#Credentials)」をご覧ください。

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook の作成

> [!Note]
> この記事では、一連の Python スクリプトを使用して、データ ソース、インデックス、インデクサー、およびスキルセットを構築する方法を示します。 ノートブックの完全な例をダウンロードするには、[Azure-Search-python-samples リポジトリ](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook)に移動してください。

Jupyter Notebook を起動して新しい Python 3 ノートブックを作成するには、Anaconda Navigator を使用します。

## <a name="connect-to-azure-search"></a>Azure Search への接続

ノートブックでこのスクリプトを実行し、JSON を操作して HTTP 要求を作成するために使用するライブラリを読み込みます。

```python
import json
import requests
from pprint import pprint
```

次に、データ ソース、インデックス、インデクサー、およびスキルセットの名前を定義します。 このスクリプトを実行して、このチュートリアル用の名前を設定します。

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> 無料サービスでは、3 つのインデックス、インデクサー、およびデータソースに制限されます。 このチュートリアルでは、それぞれ 1 つずつ作成します。 先に進む前に、新しいオブジェクトを作成するための領域があることを確認してください。

次のスクリプトでプレースホルダーをご利用の Search サービス (YOUR-SEARCH-SERVICE-NAME) と管理者 API キー (YOUR-ADMIN-API-KEY) に置き換えたうえで、スクリプトを実行して、Search サービスのエンドポイントを設定します。

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>データ ソースを作成する

これでサービスとソース ファイルの準備ができましたので、インデックス作成パイプラインのコンポーネントのアセンブルを開始します。 Azure Search に外部ソース データの取得方法を指示するデータ ソース オブジェクトから始めます。

次のスクリプトで、YOUR-BLOB-RESOURCE-CONNECTION-STRING というプレースホルダーを、前の手順で作成した BLOB 用の接続文字列に置き換えます。 次に、スクリプトを実行して、`cogsrch-py-datasource` という名前のデータ ソースを作成します。

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
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

この要求に対し、成功を確認する状態コード 201 が返されます。

Azure portal 内の Search サービスのダッシュボード ページに戻り、cogsrch-py-datasource が **[データ ソース]** 一覧内に出現していることを確認します。 **[最新の情報に更新]** をクリックしてページを更新します。

![Portal の [データ ソース] タイル](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Portal の [データ ソース] タイル")

## <a name="create-a-skillset"></a>スキルセットを作成する

この手順では、データに適用するエンリッチメント ステップのセットを定義します。 それぞれのエンリッチメント ステップを*スキル*と呼び、エンリッチメント ステップのセットを*スキルセット*と呼びます。 このチュートリアルでは、スキルセット用に次の[ビルトイン コグニティブ スキル](cognitive-search-predefined-skills.md)を使用します。

+ [言語検出](cognitive-search-skill-language-detection.md)。コンテンツの言語を識別します。

+ [テキスト分割](cognitive-search-skill-textsplit.md)。キー フレーズの抽出スキルを呼び出す前に、大きいコンテンツを小さいチャンクに分割します。 キー フレーズ抽出は、50,000 文字以下の入力を受け入れます。 いくつかのサンプル ファイルは、分割してこの制限内に収める必要があります。

+ [エンティティの認識](cognitive-search-skill-entity-recognition.md)。BLOB コンテナーのコンテンツから組織の名前を抽出します。

+ [キー フレーズ抽出](cognitive-search-skill-keyphrases.md)。上位のキー フレーズを抜き出します。 

### <a name="python-script"></a>Python スクリプト
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
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
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
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
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

各スキルは、ドキュメントのコンテンツで実行されます。 処理中に、Azure Search が各ドキュメントを解読して、さまざまなファイル形式からコンテンツを読み取ります。 ソース ファイルで見つかったテキストは、ドキュメントごとに 1 つずつ、`content` フィールドに配置されます。 そのため、入力を `"/document/content"` として設定します。

スキルセットのグラフィカル表示を以下に示します。

![スキルセットを理解する](media/cognitive-search-tutorial-blob/skillset.png "スキルセットを理解する")

出力は、言語コードと同様に、インデックスにマッピングするか、ダウンストリーム スキルへの入力として使用できます (その両方も可能です)。 インデックスでは、言語コードはフィルター処理に役立ちます。 入力としての言語コードは、テキスト分析スキルによって、単語区切りに基づく言語学的規則を通知するために使用されます。

スキルセットの基礎の詳細については、[スキルセットを定義する方法](cognitive-search-defining-skillset.md)に関するページをご覧ください。

## <a name="create-an-index"></a>インデックスを作成する

このセクションでは、検索可能なインデックスがフィールドに含まれるように指定し、各フィールドに検索属性を設定することで、インデックス スキーマを定義します。 フィールドには型があり、フィールドの使用方法 (検索可能、並べ替え可能など) を決定する属性を取ることができます。 インデックス内のフィールド名は、ソース内のフィールド名と完全に一致する必要はありません。 後のほうの手順では、インデクサーにフィールド マッピングを追加して、ソースとターゲットのフィールドを接続します。 この手順で、検索アプリケーションに関連するフィールドの名前付け規則を使用してインデックスを定義します。

この演習では、次のフィールドとフィールドの型を使用します。

| field-names: | id         | content   | languageCode | keyPhrases         | organizations     |
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

インデックスの定義の詳細については、[インデックスの作成 (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) に関するページをご覧ください。

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>インデクサーを作成し、フィールドをマップし、変換を実行する

ここまでで、データ ソース、スキルセット、およびインデックスを作成しました。 これら 3 つのコンポーネントが、各要素を複数フェーズにわたる 1 つの操作にまとめて入れる[インデクサー](search-indexer-overview.md)の一部になります。 これらのオブジェクトをまとめてインデクサーに結び付けるには、フィールド マッピングを定義する必要があります。

+ スキルセットの前に fieldMappings が処理されて、データ ソースのソース フィールドがインデックス内のターゲット フィールドにマッピングされます。 フィールド名と型が両側で共通していれば、マッピングは必要ありません。

+ outputFieldMappings は、スキルセットの後に処理されます。sourceFieldNames がドキュメント解析またはエンリッチメントによって作成されるまでは、存在しない sourceFieldNames が参照されます。 targetFieldName は、インデックス内のフィールドです。

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

インデクサー プロセスがいつ完了したかを判断するには、次のセクションの[インデクサーの状態を確認する](#check-indexer-status)スクリプトを使用します。

> [!TIP]
> インデクサーを作成すると、パイプラインが呼び出されます。 データへのアクセス、入力と出力のマッピング、操作の順序のいずれかに問題がある場合は、この段階で表示されます。 コードまたはスクリプトに変更を加えてパイプラインを再実行するには、最初にオブジェクトを削除しなければならない場合があります。 詳細については、「[リセットして再実行する](#reset)」をご覧ください。

#### <a name="explore-the-request-body"></a>要求本文を調べる

スクリプトで `"maxFailedItems"` を -1 に設定します。これにより、インデックス作成エンジンに、データのインポート中のエラーを無視するよう指示します。 デモのデータ ソースにはドキュメントがほとんどないため、これは便利です。 大きいデータ ソースの場合は、この値を 0 より大きい値に設定します。

また、構成パラメーター内の `"dataToExtract":"contentAndMetadata"` ステートメントにも注意してください。 このステートメントは、さまざまなファイル形式と、各ファイルに関連するメタデータからコンテンツを抽出するようにインデクサーに指示します。

コンテンツが抽出されるときに、`imageAction` を設定して、データ ソース内にある画像からテキストを抽出することができます。 `"imageAction":"generateNormalizedImages"` 構成は、OCR スキルおよびテキスト マージ スキルと組み合わされて、イメージからテキスト (たとえば、一時停止の道路標識から "stop" という単語) を抽出し、それをコンテンツ フィールドの一部として埋め込むよう、インデクサーに指示します。 この動作は、ドキュメントに埋め込まれている画像 (PDF 内の画像など) と、データ ソース内にある画像 (JPG ファイルなど) の両方に適用されます。

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>インデクサーの状態を確認する

インデクサーを定義すると、要求を送信するときに自動的に実行されます。 定義したコグニティブ スキルによっては、インデックス作成に予想よりも時間がかかる場合があります。 インデクサーの処理が完了したかどうかを確認するには、次のスクリプトを実行します。

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

応答内で、"lastResult" の "status" と "endTime" の値を確認します。 状態を確認するため、定期的にスクリプトを実行します。 インデクサーが完了すると、状態は "success" に設定され、"endTime" が指定されます。また、応答にはエンリッチメント中に発生したエラーや警告が含まれるようになります。

![インデクサーが作成された](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "インデクサーが作成された")

警告は、一部のソース ファイルとスキルの組み合わせではよく見られ、必ずしも問題を示すわけではありません。 このチュートリアルでは、警告は無害です。 たとえば、このスクリーンショットでは、テキストのない JPEG ファイルの 1 つについて、警告が表示されています。

![インデクサーの警告の例](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "インデクサーの警告の例")

## <a name="query-your-index"></a>インデックスの照会

インデックス作成が完了したら、個々 のフィールドの内容を返すクエリを実行します。 既定では、Azure Search によって上位 50 件の結果が返されます。 サンプル データは小さいため、既定値で問題なく動作します。 ただし、より大きなデータ セットを使用する場合は、より多くの結果が返されるよう、クエリ文字列にパラメーターを含める必要がある場合があります。 手順については、「[Azure Search でのページ検索結果の表示方法](search-pagination-page-layout.md)」をご覧ください。

検証手順として、すべてのフィールドのインデックスのクエリを実行します。

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果は次の例のようになります。 スクリーンショットに表示されているのは、応答の一部のみです。

![すべてのフィールドのインデックスの照会](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "すべてのフィールドのインデックスの照会")

出力は、インデックス スキーマと、各フィールドの名前、型、および属性です。

`"*"` の 2 番目のクエリを送信して、`organizations` などの 1 つのフィールドのすべての内容を返します。

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果は次の例のようになります。 スクリーンショットに表示されているのは、応答の一部のみです。

![組織のコンテンツのインデックスの照会](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "組織のコンテンツのインデックスの照会")

その他のフィールド (この演習では、content、languageCode、keyPhrases、および organizations) でも同様に繰り返します。 コンマ区切りリストを使用して、`$select` を介して複数のフィールドを返すことができます。

クエリ文字列の複雑さと長さによっては、GET や POST を使用できます。 詳細については、[REST API を使用したクエリ](https://docs.microsoft.com/rest/api/searchservice/search-documents)に関するページをご覧ください。
<a name="reset"></a>

## <a name="reset-and-rerun"></a>リセットして再実行する

パイプライン開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

新しい定義でドキュメントのインデックスを再作成するには、以下の操作を行います。

1. インデックスを削除して、保持されているデータを削除します。 インデクサーを削除して、サービスに再作成します。
2. スキルセットとインデックスの定義を変更します。
3. サービスにインデックスとインデクサーを再作成して、パイプラインを実行します。

ポータルを使用して、インデックス、インデクサー、およびスキルセットを削除できます。 インデクサーを削除するときは、必要に応じて、インデックス、スキルセット、およびデータ ソースを選択して同時に削除できます。

![検索オブジェクトを削除する](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "ポータル内で検索オブジェクトを削除する")

それらはスクリプトを使用して削除することもできます。 次のスクリプトによって、作成したスキルが削除されます。 インデックス、インデクサー、およびデータ ソースを削除するための要求は簡単に変更できます。

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

コードが成熟したら、リビルド戦略を改善することもできます。 詳細については、[インデックスをリビルドする方法](search-howto-reindex.md)に関するページをご覧ください。

## <a name="takeaways"></a>ここまでのポイント

このチュートリアルでは、構成要素 (データ ソース、スキルセット、インデックス、およびインデクサー) の作成によってエンリッチされたインデックス作成パイプラインを作成するための、基本的な手順を示します。

[定義済みのスキル](cognitive-search-predefined-skills.md)については、スキルセットの定義のほか、入力と出力を介したスキルの連結方法と共に説明しました。 また、Azure Search サービスでエンリッチされた値をパイプラインから検索可能なインデックス内にルーティングするには、インデクサーの定義に `outputFieldMappings` が必要であることも学習しました。

最後に、結果をテストし、今後のイテレーションのためにシステムをリセットする方法について学習しました。 インデックスに対するクエリを発行すると、エンリッチされたインデックス作成パイプラインによって作成された出力が返されることを学習しました。 このリリースには、内部構造 (システムによって作成されるエンリッチされたドキュメント) を表示するためのメカニズムがあります。 また、インデクサーの状態を確認する方法と、パイプラインを再実行する前に削除すべきオブジェクトについても学習しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスと Azure BLOB サービスが含まれているリソース グループを削除することです。 両方のサービスを同じグループに配置している場合は、リソース グループを削除すると、このチュートリアル用に作成したサービスと保存されたコンテンツを含めた、そのリソース グループ内のすべてのものが完全に削除されます。 Portal では、リソース グループ名は各サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

カスタム スキルを使ってパイプラインをカスタマイズまたは拡張します。 カスタム スキルを作成してスキルセットに追加すると、自分で作成したテキストまたは画像分析をオンボードできます。

> [!div class="nextstepaction"]
> [例:コグニティブ検索用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)
