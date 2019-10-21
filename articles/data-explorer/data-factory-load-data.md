---
title: Azure Data Factory から Azure Data Explorer にデータをコピーする
description: この記事では、Azure Data Factory のコピー ツールを使用して Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300594"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Explorer にデータをコピーする 

Azure Data Explorer は、フル マネージドの高速データ分析サービスです。 アプリケーション、Web サイト、IoT デバイスなど、さまざまなソースからストリーム配信される大量のデータをリアルタイムに分析することができます。 Azure Data Explorer を使用すると、データを繰り返し探査してパターンや異常を特定することにより、製品の改良、カスタマー エクスペリエンスの強化、デバイスの監視、操作の向上を実現できます。 これは、数分で新たな疑問を調査し、回答を得る際に役立ちます。 

Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 これを使用して、既存のシステムから Azure Data Explorer データベースにデータを設定することができます。 分析ソリューションの構築にかかる時間を短縮する効果もあります。

Azure Data Explorer にデータを読み込むと、Data Factory には次の利点があります。

* **簡単なセットアップ**: 5 つの手順からなる直感的なウィザードが表示されます。スクリプト作成は必要ありません。
* **豊富なデータ ストアのサポート**:オンプレミスとクラウドベースの豊富なデータ ストアに対するサポートが組み込まれています。 詳しい一覧については、[サポートされるデータ ストア](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**:データは HTTPS または Azure ExpressRoute 経由で転送されます。 グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **ハイ パフォーマンス**: Azure Data Explorer へのデータ読み込み速度は最大 1 GB/秒 (GBps) です。 詳細については、[コピー アクティビティのパフォーマンス](/azure/data-factory/copy-activity-performance)を参照してください。

この記事では、Data Factory のデータ コピー ツールを使用して Amazon Simple Storage Service (S3) から Azure Data Explorer にデータを読み込みます。 同様のプロセスに従って、次のようなその他のデータ ストアからデータをコピーすることもできます。
* [Azure BLOB Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [ファイル システム](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)。
* データのソース。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. [Azure Portal](https://ms.portal.azure.com) にサインインします。

1. 左側のウィンドウで、 **[リソースの作成]**  >  **[分析]**  >  **[Data Factory]** の順に選択します。

   ![Azure portal でデータ ファクトリを作成する](media/data-factory-load-data/create-adf.png)

1. **[新しいデータ ファクトリ]** ウィンドウで、次の表にあるフィールドの値を指定します。

   ![[新しいデータ ファクトリ] ウィンドウ](media/data-factory-load-data/my-new-data-factory.png)  

   | Setting  | 入力する値  |
   |---|---|
   | **Name** | このボックスには、実際のデータ ファクトリのグローバルに一意の名前を入力します。 "*データ ファクトリ名 \"LoadADXDemo\" は利用できません*" というエラーが発生する場合は、データ ファクトリ用に別の名前を入力します。 Data Factory アーティファクトの名前付けに関する規則については、[Data Factory の名前付け規則](/azure/data-factory/naming-rules)に関する記事を参照してください。|
   | **サブスクリプション** | ドロップダウン リストで、データ ファクトリを作成する Azure サブスクリプションを選択します。 |
   | **リソース グループ** | **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 既にリソース グループがある場合は、 **[既存のものを使用する]** を選択します。 |
   | **バージョン** | ドロップダウン リストで、 **[V2]** を選択します。 |  
   | **Location** | ドロップダウン リストで、データ ファクトリの場所を選択します。 サポートされている場所のみがこのリストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 |

1. **作成** を選択します。

1. 作成プロセスを監視するには、ツール バーの **[通知]** を選択します。 データ ファクトリの作成後、それを選択します。
   
   **[データ ファクトリ]** ウィンドウが表示されます。

   ![[データ ファクトリ] ウィンドウ](media/data-factory-load-data/data-factory-home-page.png)

1. このアプリケーションを別のウィンドウで開くには、 **[Author & Monitor]\(作成と監視\)** タイルを選択します。

## <a name="load-data-into-azure-data-explorer"></a>Azure Data Explorer へのデータの読み込み

多くの種類の[データ ストア](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)から Azure Data Explorer にデータを読み込むことができます。 この記事では、Amazon S3 からデータを読み込む方法について説明します。

データは、次のいずれかの方法で読み込むことができます。

* Azure Data Factory のユーザー インターフェイス。[Azure Data Factory UI を使用したデータ ファクトリの作成](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)に関する記事の「データ ファクトリの作成」セクションで示されているように、 **[作成]** アイコンを選択します。
* Azure Data Factory のデータ コピー ツール ([データ コピー ツールを使用したデータのコピー](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)に関する記事を参照)。

### <a name="copy-data-from-amazon-s3-source"></a>Amazon S3 (コピー元) からデータをコピーする

1. **[Let's get started]\(始めましょう\)** ウィンドウで、 **[データ コピー]** を選択してデータ コピー ツールを開きます。

   ![[データ コピー] ツール ボタン](media/data-factory-load-data/copy-data-tool-tile.png)

1. **[プロパティ]** ウィンドウの **[タスク名]** ボックスで、名前を入力し、 **[次へ]** を選択します。

    ![データ コピーの [プロパティ] ウィンドウ](media/data-factory-load-data/copy-from-source.png)

1. **[ソース データ ストア]** ウィンドウで、 **[新しい接続の作成]** を選択します。

    ![データ コピーの [ソース データ ストア] ウィンドウ](media/data-factory-load-data/source-create-connection.png)

1. **[Amazon S3]** を選択し、 **[続行]** を選択します。

    ![[New Linked Service]\(新しいリンクされたサービス\) ウィンドウ](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. **[New Linked Service (Amazon S3)]\(新しいリンクされたサービス (Amazon S3)\)** ページで、以下を実行します。

    ![Amazon S3 のリンクされたサービスの指定](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. **[名前]** ボックスに、リンクされた新しいサービスの名前を入力します。

    b. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** ドロップダウン リストで値を選択します。

    c. **[Access Key ID]\(アクセス キー ID\)** ボックスに値を入力します。
    
    > [!NOTE]
    > Amazon S3 で、お使いのアクセス キーを見つけるには、ナビゲーション バーでご自身の Amazon ユーザー名を選択し、 **[My Security Credentials]\(自分のセキュリティ資格情報\)** を選択します。
    
    d. **[Secret Access Key]\(シークレット アクセス キー\)** ボックスに値を入力します。

    e. リンクされたサービスの作成済みの接続をテストするには、 **[Test Connection]\(接続のテスト\)** を選択します。

    f. **[完了]** を選択します。
    
      **[ソース データ ストア]** ウィンドウには、新しい AmazonS31 接続が表示されます。 

1. **[次へ]** を選択します。

   ![ソース データ ストアの作成された接続](media/data-factory-load-data/source-data-store-created-connection.png)

1. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ウィンドウで、以下を実行します。

    a. コピーするファイルまたはフォルダーを参照し、それを選択します。

    b. 目的のコピー動作を選択します。 **[Binary copy]\(バイナリ コピー\)** チェック ボックスがオフになっていることを確認します。

    c. **[次へ]** を選択します。

    ![入力ファイルまたはフォルダーの選択](media/data-factory-load-data/source-choose-input-file.png)

1. **[File format settings]\(ファイル形式設定\)** ウィンドウで、対象のファイルの関連設定を選択します。 その後、 **[次へ]** を選択します。

   ![[File format settings]\(ファイル形式設定\) ウィンドウ](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Azure Data Explorer (コピー先) にデータをコピーする

このセクションで指定する Azure Data Explorer のコピー先テーブル (シンク) にデータをコピーするために、Azure Data Explorer のリンクされたサービスが新しく作成されます。

#### <a name="create-the-azure-data-explorer-linked-service"></a>Azure Data Explorer のリンクされたサービスを作成する

Azure Data Explorer のリンクされたサービスを作成するには、以下を実行します。

1. 既存のデータ ストア接続を使用したり、新しいデータ ストアを指定したりするには、 **[Destination data store]\(コピー先データ ストア\)** ウィンドウで **[新しい接続の作成]** を選択します。

    ![[Destination data store]\(コピー先データ ストア\) ウィンドウ](media/data-factory-load-data/destination-create-connection.png)

1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、 **[Azure Data Explorer]** を選択し、 **[続行]** を選択します。

    ![[New Linked Service]\(新しいリンクされたサービス\) ウィンドウ](media/data-factory-load-data/adx-select-new-linked-service.png)

1. **[New Linked Service (Azure Data Explorer)]\(新しいリンクされたサービス (Azure Data Explorer)\)** ウィンドウで、以下を実行します。

    ![Azure Data Explorer の [New Linked Service]\(新しいリンクされたサービス\) ウィンドウ](media/data-factory-load-data/adx-new-linked-service.png)

   a. **[名前]** ボックスに、Azure Data Explorer のリンクされたサービスの名前を入力します。

   b. **[Account selection method]\(アカウントの選択方法\)** で、次のいずれかを実行します。 

    * **[From Azure subscription]\(Azure サブスクリプションから\)** を選択し、ドロップダウン リストでお使いの **Azure サブスクリプション**と**クラスター**を選択します。 

        > [!NOTE]
        > **[クラスター]** ボックスの一覧には、お使いのサブスクリプションに関連付けられているクラスターのみが表示されます。

    * **[手動で入力]** を選択し、お使いの**エンドポイント**を入力します。

   c. **[テナント]** ボックスにテナント名を入力します。

   d. **[サービス プリンシパル ID]** ボックスに、サービス プリンシパル ID を入力します。

   e. **[Service principal key]\(サービス プリンシパル キー\)** を選択し、 **[Service principal key]\(サービス プリンシパル キー\)** ボックスにキーの値を入力します。

   f. **[データベース]** ボックスの一覧で、お使いのデータベース名を選択します。 または、 **[編集]** チェック ボックスをオンにし、データベース名を入力します。

   g. リンクされたサービスの作成済みの接続をテストするには、 **[Test Connection]\(接続のテスト\)** を選択します。 リンクされたサービスに接続できると、そのウィンドウには緑のチェックマークと "**接続成功**" メッセージが表示されます。

   h. **[完了]** を選択して、リンクされたサービスの作成を完了します。

    > [!NOTE]
    > このサービス プリンシパルは、Azure Data Explorer サービスにアクセスするために Azure Data Factory によって使用されます。 サービス プリンシパルを作成するには、[Azure Active Directory (Azure AD) のサービス プリンシパルの作成](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)に関する記事にアクセスします。 Azure Key Vault のメソッドは使用しないでください。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Azure Data Explorer データ接続を構成する

リンクされたサービスの接続を作成したら、 **[Destination data store]\(コピー先データ ストア\)** ウィンドウが表示され、作成した接続が使用できるようになります。 この接続を構成するには、以下を実行します。

1. **[次へ]** を選択します。

    ![Azure Data Explorer の [Destination data store]\(コピー先データ ストア\) ウィンドウ](media/data-factory-load-data/destination-data-store.png)

1. **[テーブル マッピング]** ウィンドウで、コピー先テーブル名を設定し、 **[次へ]** を選択します。

    ![コピー先データセットの [テーブル マッピング] ウィンドウ](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. **[列マッピング]** ウィンドウで、次のマッピングが行われます。

    a. 最初のマッピングは、[Azure Data Factory のスキーマ マッピング](/azure/data-factory/copy-activity-schema-and-type-mapping)に関する記事に従って実行されます。 以下の手順を実行します。

    * Azure Data Factory のコピー先テーブルの **[列マッピング]** を設定します。 既定では、ソースから Azure Data Factory のコピー先テーブルへのマッピングが表示されます。

    * 列マッピングを定義する必要がない列の選択はキャンセルします。

    b. 2 つ目のマッピングは、Azure Data Explorer にこの表形式データが取り込まれたときに行われます。 マッピングは [CSV のマッピング規則](/azure/kusto/management/mappings#csv-mapping)に従って実行されます。 ソース データが CSV 形式でない場合でも、Azure Data Factory ではそのデータが表形式に変換されます。 したがって、この段階では、CSV マッピングは唯一の関連のあるマッピングになります。 以下の手順を実行します。

    * (省略可能) **[Azure Data Explorer (Kusto) sink properties]\(Azure Data Explorer (Kusto) シンク プロパティ\)** で、列マッピングを使用できるように、関連する **[Ingestion mapping name]\(インジェスト マッピング名\)** を追加します。

    * **[Ingestion mapping name]\(インジェスト マッピング名\)** が指定されていない場合は、 **[列マッピング]** セクションで定義されている *by-name* マッピング順序が使用されます。 *by-name* マッピングが失敗すると、Azure Data Explorer では、*by-column position*順 (つまり、既定では位置によるマップ) でデータの取り込みが試行されます。

    * **[次へ]** を選択します。

    ![コピー先データセットの [列マッピング] ウィンドウ](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. **[設定]** ウィンドウで、以下を実行します。

    a. **[Fault tolerance settings]\(フォールト トレランスの設定\)** で、関連する設定を入力します。

    b. **[Performance settings]\(パフォーマンスの設定\)** で、 **[Enable staging]\(ステージングの有効化\)** が適用されていないため、 **[詳細設定]** にはコストに関する考慮事項が含まれます。 特定の要件がない場合は、これらの設定はそのままにしておきます。

    c. **[次へ]** を選択します。

    ![データ コピーの [設定] ウィンドウ](media/data-factory-load-data/copy-data-settings.png)

1. **[Summary]\(概要\)** ウィンドウで設定を確認し、 **[次へ]** を選択します。

    ![データ コピーの [Summary]\(概要\) ウィンドウ](media/data-factory-load-data/copy-data-summary.png)

1. **[Deployment complete]\(デプロイ完了\)** ウィンドウで、以下を実行します。

    a. **[監視]** タブに切り替えてパイプラインの状態 (つまり、進行状況、エラー、データ フロー) を確認するには、 **[監視]** を選択します。

    b. リンクされたサービス、データセット、パイプラインを編集するには、 **[パイプラインの編集]** を選択します。

    c. **[完了]** を選択してデータ コピー タスクを完了します。

    ![[Deployment complete]\(デプロイ完了\) ウィンドウ](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>次の手順

* Azure Data Factory 内の [Azure Data Explorer コネクタ](/azure/data-factory/connector-azure-data-explorer)について学習する。
* [Data Factory UI](/azure/data-factory/quickstart-create-data-factory-portal) でのリンクされたサービス、データセット、およびパイプラインの編集の詳細について学習する。
* データのクエリのための [Azure Data Explorer のクエリ](/azure/data-explorer/web-query-data)について確認する。
