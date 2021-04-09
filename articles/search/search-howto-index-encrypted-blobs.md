---
title: Azure Blob Storage の暗号化されたコンテンツを検索する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search を使用して、Azure Blob Storage の暗号化されたドキュメントのインデックス付けとテキスト抽出を行う方法について説明します。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96530937"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Azure Cognitive Search で BLOB インデクサーとスキルセットを使用して暗号化された BLOB にインデックスを付ける方法

この記事では、以前に [Azure Key Vault](../key-vault/general/overview.md) を使用して前に暗号化されている [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 内のドキュメントに、[Azure Cognitive Search](search-what-is-azure-search.md) を使用してインデックスを付ける方法について示します。 通常、インデクサーを使用すると、暗号化キーにアクセスできないため、暗号化されたファイルからはコンテンツを抽出できません。 しかし、[DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) カスタム スキルを利用した後、[DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) を利用することにより、キーへの制御されたアクセスを提供してファイルを解読した後、そこからコンテンツを抽出することができます。 これにより、格納されているドキュメントの暗号化状態を損なうことなく、これらのドキュメントにインデックスを作成する機能のロックが解除されます。

Azure Blob Storage の PDF、HTML、DOCX、PPTX など、以前に暗号化されたドキュメント全体 (非構造化テキスト) から、このガイドでは Postman と Search REST API を使用して、次のタスクを実行します。

> [!div class="checklist"]
> * ドキュメントを解読し、そこからテキストを抽出するパイプラインを定義する。
> * 出力を格納するためのインデックスを定義する。
> * パイプラインを実行して、インデックスを作成し、データを読み込む。
> * フルテキスト検索と豊富なクエリ構文を使用して結果を探索する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

この例では、既にファイルを Azure Blob Storage にアップロードし、その過程で暗号化されているものとします。 最初にファイルをアップロードして暗号化する方法についてのヘルプが必要な場合は、[このチュートリアル](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)で方法を確認してください。

+ [Azure ストレージ](https://azure.microsoft.com/services/storage/)
+ Azure Cognitive Search と同じサブスクリプションにある [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。 キー コンテナーでは、**論理的な削除** と **消去保護** が有効になっている必要があります。
+ [請求可能なレベル](search-sku-tier.md#tier-descriptions) (Basic 以上、任意のリージョン) の [Azure Cognitive Search](search-create-service-portal.md)
+ [Azure 関数](https://azure.microsoft.com/services/functions/)
+ [Postman デスクトップ アプリ](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 - サービスを作成し、資格情報を収集する

### <a name="set-up-the-custom-skill"></a>カスタム スキルを設定する

この例では、GitHub の [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) リポジトリのサンプル プロジェクト [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) を使用します。 このセクションでは、スキルセットで使用できるようにスキルを Azure 関数にデプロイします。 組み込みのデプロイ スクリプトにより、名前が **psdbf-function-app-** で始まる Azure 関数リソースが作成されて、スキルが読み込まれます。 サブスクリプションとリソース グループを指定するように求められます。 Azure Key Vault インスタンスが存在するのと同じサブスクリプションを選択してください。

運用上、DecryptBlobFile スキルによって、各 BLOB の URL と SAS トークンが入力として受け取られて、Azure Cognitive Search で必要なファイル参照コントラクトを使用して、ダウンロードされて解読されたファイルが出力されます。 解読を実行するには、DecryptBlobFile に暗号化キーが必要であることを思い出してください。 設定の一部として、Azure Key Vault 内の暗号化キーへのアクセスを DecryptBlobFile 関数に許可するアクセス ポリシーも作成します。

1. [DecryptBlobFile のランディング ページ](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment)にある **[Azure に配置する]** ボタンをクリックします。提供された Resource Manager テンプレートが Azure portal で開かれます。

1. **Azure Key Vault のインスタンスが存在するサブスクリプション** を選択し (別のサブスクリプションを選択した場合、このガイドは機能しません)、既存のリソース グループを選択するか、新しいものを作成します (新しいものを作成する場合は、デプロイ先のリージョンも選択する必要があります)。

1. **[確認および作成]** を選択し、使用条件に同意していることを確認した後、 **[作成]** を選択して Azure 関数をデプロイます。

    ![ポータルでの ARM テンプレート](media/indexing-encrypted-blob-files/arm-template.jpg "ポータルでの ARM テンプレート")

1. デプロイが完了するまで待ちます。

1. ポータルで、Azure Key Vault のインスタンスに移動します。 Azure Key Vault で、カスタム スキルへのアクセスをキーに許可する[アクセス ポリシーを作成](../key-vault/general/assign-access-policy-portal.md)します。
 
    1. **[設定]** で **[アクセス ポリシー]** を選択し、 **[アクセス ポリシーの追加]** を選択します
     
       ![Key Vault でアクセス ポリシーを追加する](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Key Vault のアクセス ポリシー")

    1. **[テンプレートからの構成]** で、 **[Azure Data Lake Storage または Azure Storage]** を選択します。

    1. プリンシパルとして、デプロイした Azure 関数インスタンスを選択します。 ステップ 2 でそれを作成するときに使用したリソース プレフィックスを使用して検索できます。既定のプレフィックス値は **psdbf-function-app** です。

    1. [承認されているアプリケーション] オプションでは何も選択しないでください。
     
        ![Key Vault でアクセス ポリシー テンプレートを追加する](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Key Vault のアクセス ポリシー テンプレート")

    1. アクセス ポリシーを実際に追加するために別の場所に移動する前に、アクセス ポリシーのページで **[保存]** を必ずクリックしてください。
     
         ![Key Vault でアクセス ポリシーを保存する](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Key Vault のアクセス ポリシーを保存する")

1. ポータルで **psdbf-function-app** 関数に移動し、次のプロパティを記録しておきます。これらのプロパティはガイドの後半で必要になります。

    1. 関数の URL。これは、関数のメイン ページの **[基本]** にあります。
    
        ![関数の URL](media/indexing-encrypted-blob-files/function-uri.jpg "Azure 関数の URL を探す場所")

    1. ホスト キー コード。 **[アプリ キー]** に移動し、**default** キーをクリックして表示し、値をコピーします。
     
        ![関数のホスト キー コード](media/indexing-encrypted-blob-files/function-host-key.jpg "Azure 関数のホスト キー コードを探す場所")

### <a name="cognitive-services"></a>Cognitive Services

AI エンリッチメントとスキルセットの実行は、自然言語と画像の処理のための Text Analytics や Computer Vision など、Cognitive Services によってサポートされています。 実際のプロトタイプまたはプロジェクトを完成させることが目的であれば、この時点で (Azure Cognitive Search と同じリージョンに) Cognitive Services をプロビジョニングして、インデックス作成操作にアタッチできるようにします。

ただし、この演習では、Azure Cognitive Search がバックグラウンドで Cognitive Services に接続し、インデクサーの実行ごとに 20 個の無料トランザクションを提供できるため、リソースのプロビジョニングをスキップできます。 Cognitive Services キーがスキルセットにアタッチされていない場合、インデクサーは 20 個のドキュメントを処理した後で失敗します。 より大規模なプロジェクトの場合は、従量課金制の S0 レベルで Cognitive Services をプロビジョニングすることを計画してください。 詳細については、[Cognitive Services のアタッチ](cognitive-search-attach-cognitive-services.md)に関するページを参照してください。 選択したコグニティブなスキルがどれも Cognitive Services に接続されていない場合でも、20 個より多くのドキュメントを含むスキルセットを実行するには、Cognitive Services キーが必要であることにご注意ください (スキルが追加されていない場合の、提供されたスキルセットなど)。

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

最後のコンポーネントは Azure Cognitive Search であり、[ポータルで作成](search-create-service-portal.md)できます。 このガイドは Free レベルを使用して完了できます。 

Azure 関数と同様に、管理者キーを収集します。 さらに、要求の構築を始めるときに、各要求の認証に使用されるエンドポイントと管理者 API キーを指定する必要があります。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search のための管理者 API キーと URL を取得する

1. [Azure portal にサインイン](https://portal.azure.com/)し、自分の検索サービスの **[概要]** ページで、自分の検索サービスの名前を確認します。 エンドポイント URL を見ることで、自分のサービス名を確かめることができます。 エンドポイント URL が `https://mydemo.search.windows.net` だったら、自分のサービス名は `mydemo` になります。

2. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   ![サービス名、管理キー、クエリ キーの取得](media/search-get-started-javascript/service-name-and-keys.png)

すべての要求で、自分のサービスに送信される各要求のヘッダーに API キーが必要になります。 有効なキーにより、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼が確立されます。

## <a name="2---set-up-postman"></a>2 - Postman を設定する

Postman をインストールして設定します。

### <a name="download-and-install-postman"></a>Postman をダウンロードしてインストールする

1. [Postman コレクション ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)をダウンロードします。
1. **[File]\(ファイル\)**  >  **[Import]\(インポート\)** を選択して、ソース コードを Postman にインポートします。
1. **[Collections]\(コレクション\)** タブを選択し、 **[...]** (省略記号) ボタンを選択します。
1. **[編集]** を選択します。 
   
   ![ナビゲーションを示す Postman アプリ](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Postman の [Edit]\(編集\) メニューに移動する")
1. **[Edit]\(編集\)** ダイアログ ボックスで、 **[Variables]\(変数\)** タブを選択します。 

**[Variables]\(変数\)** タブには、二重中かっこ内で特定の変数が出現するたびに、Postman でスワップされる値を追加できます。 たとえば、Postman によって、記号 `{{admin-key}}` は `admin-key` に設定した現在の値に置き換えられます。 Postman では、URL、ヘッダー、要求本文などの置き換えが行われます。 

`admin-key` の値を取得するには、先ほど記録した Azure Cognitive Search の管理者 API キーを使用します。 使用している Azure Cognitive Search Service の名前に、`search-service-name` を設定します。 ストレージ アカウントの **[アクセス キー]** タブの値を使用して `storage-connection-string` を設定し、`storage-container-name` を、暗号化されたファイルが格納されているそのストレージ アカウントの BLOB コンテナーの名前に設定します。 前に記録した Azure 関数の URL に `function-uri` を設定し、前に記録した Azure 関数のホスト キー コードに `function-code` を設定します。 その他の値は既定値のままにしておくことができます。

![Postman アプリの [variables]\(変数\) タブ](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Postman の [variables]\(変数\) ウィンドウ")

| 変数    | 情報の入手元 |
|-------------|-----------------|
| `admin-key` | Azure Cognitive Search サービスの **[キー]** ページ上。  |
| `search-service-name` | Azure Cognitive Search サービスの名前。 URL は `https://{{search-service-name}}.search.windows.net` です。 |
| `storage-connection-string` | ストレージ アカウントの **[アクセス キー]** タブで、 **[key1]**  >  **[接続文字列]** を選択します。 |
| `storage-container-name` | インデックスを付ける暗号化されたファイルが含まれる BLOB コンテナーの名前。 |
| `function-uri` |  メイン ページの **[基本]** の下にある Azure 関数内。 |
| `function-code` | Azure 関数で、 **[アプリ キー]** に移動し、**default** キーをクリックして表示し、値をコピー。 |
| `api-version` | **2020-06-30** のままにします。 |
| `datasource-name` | **encrypted-blobs-ds** のままにします。 |
| `index-name` | **encrypted-blobs-idx** のままにします。 |
| `skillset-name` | **encrypted-blobs-ss** のままにします。 |
| `indexer-name` | **encrypted-blobs-ixr** のままにします。 |

### <a name="review-the-request-collection-in-postman"></a>Postman の要求コレクションの確認

このガイドを実行するときは、4 つの HTTP 要求を発行する必要があります。

- **インデックスを作成するための PUT 要求**:このインデックスでは、Azure Cognitive Search で使用され、返されるデータが保持されます。
- **データソースを作成するための POST 要求**:このデータソースにより、Azure Cognitive Search Service がストレージ アカウント、したがって暗号化された BLOB ファイルに接続されます。 
- **スキルセットを作成するための PUT 要求**:スキルセットにより、BLOB ファイルのデータを解読する Azure 関数のカスタム スキル定義と、解読された後の各ドキュメントからテキストを抽出するための [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) が指定されます。
- **インデクサーを作成するための PUT 要求**:インデクサーを実行すると、データの読み取り、スキルセットの適用、結果の格納が行われます。 この要求は最後に実行する必要があります。

[ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)には、4 つの要求を保持する Postman コレクションと、いくつかの便利なフォローアップ要求が含まれています。 要求を発行するには、Postman で要求のタブを選択し、それぞれに対して **[送信]** を選択します。

## <a name="3---monitor-indexing"></a>3 - インデックス付けを監視する

インデックス作成とエンリッチメントは、インデクサー作成要求を送信するとすぐに開始されます。 ストレージ アカウントに含まれるドキュメントの数によっては、インデックス付けに時間がかかることがあります。 インデクサーがまだ実行されているかどうかを確認するには、Postman コレクションの一部として提供されている **インデクサー状態の取得** 要求を使用し、応答を確認して、インデクサーが実行されているかどうかを確認するか、エラーと警告の情報を表示します。  

Free レベルを使用している場合は、次のメッセージが表示されます: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"`。 このメッセージが表示されるのは、Free レベルでの BLOB のインデックス作成には、[文字の抽出に 32K の制限](search-limits-quotas-capacity.md#indexer-limits)があるためです。 より上位のレベルでは、このデータ セットに対してこのメッセージは表示されません。 

## <a name="4---search"></a>4 - 検索する

インデクサーの実行が完了したら、クエリを実行して、正常にデータが解読され、インデックスが付けられたことを確認できます。 ポータルで Azure Cognitive Search Service に移動し、[Search エクスプローラー](search-explorer.md)を使用して、インデックスが付けられたデータに対してクエリを実行します。

## <a name="next-steps"></a>次のステップ

暗号化されたファイルに正常にインデックスを付けられたので、[コグニティブなスキルをさらに追加して、このパイプラインを反復処理する](cognitive-search-defining-skillset.md)ことができます。 これにより、データをエンリッチし、追加の分析情報を得ることができます。

二重に暗号化されたデータを使用している場合は、Azure Cognitive Search で利用可能なインデックス暗号化機能の調査が必要かもしれません。 インデクサーでインデックスを付けるには解読されたデータが必要ですが、インデックスを付けた後は、カスタマー マネージド キーを使用してそれを暗号化することができます。 これにより、データは保存時に常に暗号化されることが保証されます。 詳細については、「[Azure Cognitive Search のデータ暗号化のためにカスタマー マネージド キーを構成する](search-security-manage-encryption-keys.md)」を参照してください。