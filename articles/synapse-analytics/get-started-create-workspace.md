---
title: チュートリアル:Synapse ワークスペース作成の概要
description: このチュートリアルでは、Synapse ワークスペース、SQL プール、Apache Spark プールを作成する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 08/27/2020
ms.openlocfilehash: 56292d3e8ba4c9ec89d73f10640264c178f8a9a7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255020"
---
# <a name="create-a-synapse-workspace"></a>Synapse ワークスペースを作成する

このチュートリアルでは、Synapse ワークスペース、SQL プール、Apache Spark プールを作成する方法について説明します。 

## <a name="create-a-synapse-workspace"></a>Synapse ワークスペースを作成する

1. [Azure portal](https://portal.azure.com) を開き、上部で **Synapse** を検索します。
1. 検索結果の **[サービス]** で、 **[Azure Synapse Analytics (ワークスペース プレビュー)]** を選択します。
1. **[追加]** を選択し、これらの設定を使用してワークスペースを作成します。

    |タブ|設定 | 推奨値 | 説明 |
    |---|---|---|---|
    |基本|**ワークスペース名**|任意の名前を付けることができます。| このドキュメントでは、**myworkspace** を使用します。|
    |基本|**リージョン**|ストレージ アカウントのリージョンに一致させます。|

1. ワークスペースを作成するには、ADLSGEN2 アカウントが必要です。 最も簡単な方法は、新しいものを作成することです。 既存のものを再利用する場合は、追加の構成を行う必要があります。 
1. オプション 1: 新しい ADLSGEN2 アカウントの作成 
    1. **[Data Lake Storage Gen 2 の選択]** で、 **[新規作成]** をクリックして、「**contosolake**」という名前を指定します。
    1. **[Data Lake Storage Gen 2 の選択]** で、 **[ファイル システム]** をクリックして、「**users**」という名前を指定します。
1. オプション 2: このドキュメントの下部にある「**ストレージ アカウントを準備する**」の手順を参照してください。
1. Azure Synapse ワークスペースは、このストレージ アカウントを "プライマリ" ストレージ アカウントとして使用し、ワークスペース データを格納するコンテナーを使用します。 このワークスペースでは、データが Apache Spark テーブルに格納されます。 **/synapse/workspacename** というフォルダーに Spark アプリケーション ログが格納されます。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 ワークスペースの準備は数分で完了します。


## <a name="open-synapse-studio"></a>Synapse Studio を開く

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開きます。 **[概要]** セクションの上部にある **[Synapse Studio の起動]** を選択します。
* `https://web.azuresynapse.net` にアクセスし、ワークスペースにサインインします。

## <a name="create-a-sql-pool"></a>SQL プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[SQL プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**SQL プール名**| **SQLDB1**|
    |**パフォーマンス レベル**|**DW100C**|
    |||

1. **[確認と作成]**  >  **[作成]** の順に選択します。 SQL プールの準備は数分で完了します。 SQL プールは、**SQLDB1** とも呼ばれる SQL プール データベースに関連付けられます。

SQL プールがアクティブである限り、課金対象のリソースが消費されます。 コストを削減するために、後でプールを一時停止できます。

## <a name="create-an-apache-spark-pool"></a>Apache Spark プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[Apache Spark プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**[Apache Spark pool name]\(Apache Spark プール名\)**|**Spark1**
    |**ノード サイズ**| **Small**|
    |**[Number of nodes]\(ノードの数\)**| 最小数を 3 に、最大数を 3 に設定します|

1. **[確認と作成]**  >  **[作成]** の順に選択します。 Apache Spark プールの準備は数秒で完了します。

> [!NOTE]
> 名前に反して、Apache Spark プールは SQL プールに似ていません。 これは、Azure Synapse ワークスペースに対して Spark との対話方法を指示するために使用する基本的なメタデータにすぎません。

メタデータであるため、Spark プールを開始したり停止したりすることはできません。

Azure Synapse で Spark アクティビティを実行する際は、使用する Spark プールを指定します。 そのプールから Azure Synapse に対して、使用する Spark リソースの数が指示されます。 支払いの対象となるのは、使用したリソースの分だけです。 プールの使用を能動的に停止すると、リソースは自動的にタイムアウトし、リサイクルされます。

> [!NOTE]
> Spark データベースは Spark プールから独立して作成されます。 ワークスペースには必ず、**既定**と呼ばれる Spark データベースが存在します。 追加の Spark データベースを作成することができます。

## <a name="the-sql-on-demand-pool"></a>SQL オンデマンド プール

各ワークスペースには、**SQL オンデマンド**というあらかじめ構築されたプールが付属しています。 このプールを削除することはできません。 SQL オンデマンド プールを使用すると、Azure Synapse で SQL プールを作成したり、その管理を検討したりすることなく、SQL を操作できます。

他の種類のプールとは異なり、SQL オンデマンドの課金は、クエリの実行に使用されたリソースの数ではなく、クエリを実行するためにスキャンされたデータの量に基づきます。

* SQL オンデマンドには、SQL オンデマンド プールとは別に存在する独自の SQL オンデマンド データベースもあります。
* ワークスペースには必ず、**SQL オンデマンド**という名前の SQL オンデマンド プールが 1 つだけあります。

## <a name="prepare-a-storage-account"></a>ストレージ アカウントを準備する

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 次の設定で新しいストレージ アカウントを作成します。

    |タブ|設定 | 推奨値 | 説明 |
    |---|---|---|---|
    |基本|**Storage account name \(ストレージ アカウント名\)**| 任意の名前を選択します。| このドキュメントでは、**contosolake** という名前を使用します。|
    |基本|**アカウントの種類**| **StorageV2** ||
    |基本|**場所**|任意の場所を選択します。| Azure Synapse Analytics ワークスペースと Azure Data Lake Storage Gen2 アカウントは同じリージョンに配置することをお勧めします。|
    |詳細設定|**Data Lake Storage Gen2**|**有効**| Azure Synapse は、この設定が有効になっているストレージ アカウントでのみ機能します。|
    |||||

1. ストレージ アカウントの作成後、左ペインの **[アクセス制御 (IAM)]** を選択します。 次に、以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。
    * **所有者**ロールに自分自身を割り当てます。
    * **ストレージ BLOB データ所有者**ロールに自分自身を割り当てます。
1. 左ペインで **[コンテナー]** を選択し、コンテナーを作成します。
1. コンテナーには任意の名前を付けることができます。 このドキュメントでは、このコンテナーに **users** という名前を付けます。
1. 既定の設定である **[パブリック アクセス レベル]** をそのまま使用し、 **[作成]** を選択します。

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>ワークスペースからストレージ アカウントへのアクセスを構成する

Azure Synapse ワークスペースのマネージド ID には、既にストレージ アカウントへのアクセス権がある可能性があります。 確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) を開き、ワークスペース用に選択したプライマリ ストレージ アカウントを開きます。
1. 左ペインから **[アクセス制御 (IAM)]** を選択します。
1. 以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。 ここでは、ワークスペース ID とワークスペース名に同じ名前を使用します。
    * ストレージ アカウントの **[ストレージ BLOB データ共同作成者]** ロールに、ワークスペース ID として **myworkspace** を割り当てます。
    * ワークスペース名として **myworkspace** を割り当てます。

1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [SQL プールを使用して分析する](get-started-analyze-sql-pool.md)
