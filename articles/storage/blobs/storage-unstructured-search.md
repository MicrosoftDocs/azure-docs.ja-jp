---
title: チュートリアル:Azure Blob Storage で非構造化データを検索する
description: チュートリアル:Azure Search を使用して Blob Storage 内の非構造化データを検索する。
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: acf01788a1ac8ed8ca476941c402801abfbd8adf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011303"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>チュートリアル:クラウド ストレージで非構造化データを検索する

このチュートリアルでは、Azure Blob Storage の格納データを使用し、[Azure Search](../../search/search-what-is-azure-search.md) を使用して、非構造化データを検索する方法を説明します。 非構造化データは、事前に定義された方法で編成されていないか、データ モデルがないデータです。 たとえば、.txt ファイルが挙げられます。

このチュートリアルには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * リソース グループの作成
> * ストレージ アカウントの作成
> * コンテナーを作成する
> * データをコンテナーにアップロードする
> * ポータルから検索サービスを作成する
> * 検索サービスをストレージ アカウントに接続する
> * データ ソースを作成する
> * インデックスの構成
> * インデクサーの作成
> * 検索サービスを使用してコンテナーを検索する

## <a name="prerequisites"></a>前提条件

すべてのストレージ アカウントは、Azure リソース グループに属している必要があります。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。 このチュートリアルでは、新しいリソース グループを作成します。

[Azure Portal](https://portal.azure.com) にサインインします。

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

このチュートリアルで利用できるサンプル データ セットが用意されています。 [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) をダウンロードし、独自のフォルダーに解凍します。

サンプルは、[clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) から取得したテキスト ファイルで構成されています。 このチュートリアルでは、それらのテキスト ファイルを例に、Azure Search サービスを使用して検索します。

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーはフォルダーに似ており、BLOB の格納に使用します。

このチュートリアルでは、1 つのコンテナーを使用して、clinicaltrials.gov から取得したテキスト ファイルを格納します。

1. Azure portal で、ストレージ アカウントに移動します。

2. **[Blob service]** で **[BLOB の参照]** を選択します。

3. 新しいコンテナーを追加します。

4. コンテナーに、**data** という名前を付けて、パブリック アクセス レベルに **[Container]\(コンテナー\)** を選択します。

5. **[OK]** を選択してコンテナーを作成します。

   ![非構造化検索](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>サンプル データのアップロード

サンプル データをアップロードできるコンテナーが用意されました。

1. コンテナーを選択し、**[Upload] \(アップロード)** を選択します。

2. **[Files]\(ファイル\)** フィールドの横にある青いフォルダー アイコンを選択し、サンプル データを抽出したローカル フォルダーを参照します。

3. 抽出したすべてのファイルを選択し、**[Open]\(開く\)** を選択します。

4. **[Upload] \(アップロード)** を選択し、アップロード プロセスを開始します。

   ![非構造化検索](media/storage-unstructured-search/upload.png)

アップロード プロセスには、しばらく時間がかかることがあります。

それが完了したら、データ コンテナーに戻り、テキスト ファイルがアップロードされたことを確認します。

  ![非構造化検索](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Search Service の作成

Azure Search は、データ検索機能を追加するための API とツールを開発者に提供する "サービスとしての検索クラウド" ソリューションです。

このチュートリアルでは、検索サービスを使用して、clinicaltrials.gov から取得したテキスト ファイルを検索します。

1. Azure portal で、ストレージ アカウントに移動します。

2. 下へスクロールし、**[Blob service]** で、**[Azure Search の追加]** を選択します。

3. **[Import Data] \(データのインポート)** で、**[Pick your service] \(サービスの選択)** を選択します。

4. **[Click here to create a new search service] \(ここをクリックして新しい検索サービスを作成)** を選択します。

5. **[New Search Service] \(新しい検索サービス)** 内で、**[URL]** フィールドに検索サービスの一意の名前を入力します。

6. **[Resource group]\(リソース グループ\)** で、**[Use existing]\(既存を使用\)** を選択し、先に作成したリソース グループを選択します。

7. **[Pricing tier]\(価格レベル\)** で、**[Free]\(無料\)** レベルを選択し、**[Select]\(選択\)** をクリックします。

8. **[Create] \(作成)** を選択して、検索サービスを作成します。

   ![非構造化検索](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>検索サービスをコンテナーに接続する

Blob Storage に接続できる検索サービスが用意されました。 このセクションでは、データ ソースの選択、インデックスの作成、インデクサーの作成のプロセスについて説明します。

1. ストレージ アカウントに移動します。

2. **[Blob service]** で、**[Azure Search の追加]** を選択します。

3. **[Import Data]\(データのインポート\)** 内で **[Search Service]\(検索サービス\)** を選択し、前のセクションで作成した検索サービスをクリックします。 これにより、**[New data source]\(新しいデータ ソース\)** が開きます。

### <a name="create-a-data-source"></a>データ ソースを作成する

  データ ソースは、インデックスを作成するデータとデータにアクセスする方法を指定します。 データ ソースは、同じ検索サービスで、何回も使用できます。

1. データ ソースの名前を入力します。 **[Data to extract] \(抽出されるデータ)** で、**[Content and Metadata] \(コンテンツとメタデータ)** を選択します。 データ ソースは、インデックスを作成する BLOB の部分を指定します。

2. 使用している BLOB はテキスト ファイルであるため、**[解析モード]** を **[テキスト]** に設定します。

   ![非構造化検索](media/storage-unstructured-search/datasources.png)

3. **[Storage Container] (ストレージ コンテナー)** を選択し、使用可能なストレージ アカウントを一覧表示します。

4. ストレージ アカウントを選択し、以前に作成したコンテナーを選択します。

   ![非構造化検索](media/storage-unstructured-search/datacontainer.png)

5. **[Select]\(選択\)** をクリックして、**[New data source]\(新しいデータ ソース\)** に戻り、**[OK]** を選択して続行します。

### <a name="configure-the-index"></a>インデックスの構成

  インデックスは、検索可能なデータ ソースからのフィールドのコレクションです。 検索サービスが目的のデータをどのように検索すればよいかがわかるよう、それらのフィールドに対してパラメーターを設定して構成することになります。

1. **[Import data]\(データのインポート\)** で、**[対象インデックスをカスタマイズします]** を選択します。

2. **[Index name] \(インデックス名)** フィールドに、インデックスの名前を入力します。

3. **[metadata_storage_name]** で **[Retrievable]** 属性のチェックボックスをオンにします。

   ![非構造化検索](media/storage-unstructured-search/valuestoselect.png)

4. **[OK]** を選択すると、**[インデクサーの作成]** が表示されます。

インデックスのパラメーターとそれらのパラメーターを指定する属性は重要です。 パラメーターは "*どの*" データを格納するか、属性は "*どのように*" そのデータを格納するかを指定します。

**[FIELD NAME] \(フィールド名)** 列に、パラメーターが含まれます。 次の表に、使用可能な属性とその説明の一覧を提供します。

#### <a name="field-attributes"></a>フィールド属性

| 属性 | 説明 |
| --- | --- |
| *キー* |ドキュメント検索に使用される各ドキュメントの一意の ID を提供する文字列です。 各インデックスに、1 つのキーが必要です。 1 つのフィールドだけをキーにすることができ、その型を Edm.String に設定する必要があります。 |
| *Retrievable* |検索結果でフィールドを返すことができるかどうかを設定します。 |
| *Filterable* |フィルター クエリでフィールドを使用できるようにします。 |
| *Sortable* |このフィールドを使ってクエリで検索結果を並べ替えられるようにします。 |
| *Facetable* |ユーザー自律フィルター処理のファセット ナビゲーション構造でフィールドを使用できるようにします。 通常は、ドキュメント (たとえば、1 つのブランドやサービス カテゴリに属する複数のドキュメント) をまとめてグループ化するために使用できる、反復する値があるフィールドが、ファセットとして最適です。 |
| *Searchable* |フィールドをフルテキスト検索可能としてマークします。 |

### <a name="create-an-indexer"></a>インデクサーの作成

  インデクサーはデータ ソースを検索インデックスに接続し、データのインデックスの再作成のスケジュールを提供します。

1. **[Name] \(名前)** フィールドに名前を入力し、**[OK]** を選択します。

   ![非構造化検索](media/storage-unstructured-search/exindexer.png)

2. **[Import Data]\(データのインポート\)** に戻ります。 **[OK]** を選択して、接続プロセスを完了します。

BLOB を検索サービスに正常に接続しました。 ポータルで、インデックスに入力されたことが表示されるまで数分かかります。 ただし、検索サービスは、すぐにインデックス作成を開始するため、ただちに検索を開始できます。

## <a name="search-your-text-files"></a>テキスト ファイルの検索

ファイルを検索するには、新しく作成した検索サービスのインデックス内で Search エクスプローラーを開きます。

次の手順で、Search エクスプローラーがある場所を示し、いくつかのクエリ例を提供します。

1. すべてのリソースに移動し、新しく作成した検索サービスを見つけます。

   ![非構造化検索](media/storage-unstructured-search/exampleurl.png)

2. インデックスを選択して、それを開きます。

   ![非構造化検索](media/storage-unstructured-search/overview.png)

3. **[Search エクスプローラー]** を選択して、Search エクスプローラーを開き、データへのライブ クエリを行うことができます。

   ![非構造化検索](media/storage-unstructured-search/indexespane.png)

4. クエリ文字列フィールドが空の状態で、**[Search] \(検索)** を選択します。 空のクエリは、BLOB から*すべて*のデータを返します。

   ![非構造化検索](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>フルテキスト検索を実行する

**[Query string]\(クエリ文字列\)** フィールドに、「**Myopia**」と入力し、**[Search]\(検索\)** を選択します。 この手順でファイルの内容の検索を開始すると、"Myopia" という単語を含むそれらのサブセットが返されます。

  ![非構造化検索](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>システム プロパティ検索を実行する

フルテキスト検索のほかにも、`$select` パラメーターを使用することで、システム プロパティを検索条件とするクエリを作成できます。

クエリ文字列に「`$select=metadata_storage_name`」と入力して **Enter** キーを押します。 すると、その特定のフィールドのみが返されます。

クエリ文字列は、URL を直接に変更するため、スペースは使用できません。 複数のフィールドを検索するには、`$select=metadata_storage_name,metadata_storage_path` のように、コンマを使用します。

`$select` パラメーターは、インデックスを定義するときに取得可能とマークされたフィールドでのみ使用できます。

  ![非構造化検索](media/storage-unstructured-search/metadatasearchunstructured.png)

このチュートリアルが完了し、非構造化データの検索可能なセットが用意されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成したリソースを削除するには、リソース グループを削除するのが最も簡単です。 リソース グループを削除すると、そのグループに含まれるすべてのリソースも削除されます。 次の例では、リソース グループを削除して、ストレージ アカウントとリソース グループ自体を削除しています。

1. Azure portal で、ご利用のサブスクリプションのリソース グループ一覧に移動します。
2. 削除するリソース グループを選択します。
3. **[リソース グループの削除]** ボタンを選択し、目的のリソース グループの名前を削除フィールドに入力します。
4. **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

Azure Search によるドキュメントのインデックス作成の詳細については、次のリンクをクリックしてください。

> [!div class="nextstepaction"]
> [Azure Blob Storage 内ドキュメントのインデックスを Azure Search で作成する](../../search/search-howto-indexing-azure-blob-storage.md)
