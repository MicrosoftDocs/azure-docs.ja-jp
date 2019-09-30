---
title: Azure Data Factory から Azure Data Explorer にデータをコピーする
description: このトピックでは、Azure Data Factory のコピー ツールを使用した、Azure Data Explorer へのデータの取り込み (読み込み) について説明します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b3bd9b800da4f096639d02c78b718216441621a9
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803985"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Explorer にデータをコピーする 

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどの多くのソースからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 データを繰り返し探査し、パターンと異常を識別することにより、製品の改良、カスタマー エクスペリエンスの強化、デバイスの監視、操作の向上を実現できます。 新たな質問を試して、数分で回答を得ることができます。 Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 このサービスを使用して、既存のシステムからのデータで Azure Data Explorer データベースを設定し、分析ソリューションを構築する際の時間を節約できます。

Azure Data Factory には、Azure Data Explorer にデータを読み込む際に次の利点があります。

* **簡単なセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**:オンプレミスとクラウドベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。 詳しい一覧については、[サポートされるデータ ストア](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**:データは HTTPS または ExpressRoute 経由で転送されます。 グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **ハイ パフォーマンス**: 最大 1 GB/秒の速度で Azure Data Explorer にデータを読み込みます。 詳しくは、[コピー アクティビティのパフォーマンス](/azure/data-factory/copy-activity-performance)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して Amazon S3 から Azure Data Explorer にデータを読み込む方法を示します。 同様の手順に従って、[Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)、[Azure SQL Database](/azure/data-factory/connector-azure-sql-database)、[Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)、[GoogleBigQuery](/azure/data-factory/connector-google-bigquery)、[Oracle](/azure/data-factory/connector-oracle)、[ファイル システム](/azure/data-factory/connector-file-system)などの他のデータ ストアからデータをコピーできます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)
* データのソースです。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. ポータルの左上隅にある **[リソースの作成]** ボタン (+) > **[分析]**  >  **[Data Factory]** を選択します。

   ![新しいデータ ファクトリの作成](media/data-factory-load-data/create-adf.png)

1. **[新しいデータ ファクトリ]** ページで、次のフィールドの値を指定し、 **[作成]** を選択します。

    ![[新しいデータ ファクトリ] ページ](media/data-factory-load-data/my-new-data-factory.png)

    **設定**  | **フィールドの説明**
    |---|---|
    | **Name** | データ ファクトリのグローバルに一意の名前を入力します。 "*データ ファクトリ名 \"LoadADXDemo\" は利用できません*" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](/azure/data-factory/naming-rules)に関する記事をご覧ください。|
    | **サブスクリプション** | データ ファクトリを作成する Azure サブスクリプションを選択します。 |
    | **リソース グループ** | **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 既存のリソース グループがある場合は、 **[既存のものを使用]** を選択します。 |
    | **バージョン** | **[V2]** を選択します。 |
    | **Location** | データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 |
    | | |

1. 作成プロセスを監視するには、ツール バーの [通知] を選択します。 作成が完了したら、作成したデータ ファクトリに移動します。 **Data Factory** のホーム ページが開きます。

   ![データ ファクトリのホーム ページ](media/data-factory-load-data/data-factory-home-page.png)

1. **[作成と監視]** タイルを選択して、別のタブにアプリケーションを起動します。

## <a name="load-data-into-azure-data-explorer"></a>Azure Data Explorer へのデータの読み込み

多くの種類の[データ ストア](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)から Azure Data Explorer にデータを読み込むことができます。 このトピックでは、Amazon S3 からのデータの読み込みについて詳しく説明します。

Azure Data Factory を使用して Azure Data Explorer にデータを読み込む方法は 2 つあります。

* Azure Data Factory ユーザー インターフェイス - [ **[作成者]** タブ](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* この記事で使用している [Azure Data Factory の**データのコピー** ツール](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

### <a name="copy-data-from-amazon-s3-source"></a>Amazon S3 (ソース) からデータをコピーする

1. **[Let's get started]\(始めましょう\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。

   ![データのコピー ツールのタイル](media/data-factory-load-data/copy-data-tool-tile.png)

1. **[プロパティ]** ページで、 **[タスク名]** を指定し、 **[次へ]** を選択します。

    ![ソースのプロパティからコピーする](media/data-factory-load-data/copy-from-source.png)

1. **[ソース データ ストア]** ページで、 **[新しい接続の作成]** をクリックします。

    ![ソース データの接続の作成](media/data-factory-load-data/source-create-connection.png)

1. **[Amazon S3]** を選択し、 **[続行]** を選択します

    ![新規のリンクされたサービス](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. **[New Linked Service (Amazon S3)]\(新しいリンクされたサービス (Amazon S3)\)** ページで、次の手順を実行します。

    ![Amazon S3 のリンクされたサービスの指定](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * 新しいリンクされたサービスの **[名前]** を指定します。
    * ドロップダウンから **[Connect via integration runtime]\(統合ランタイム経由で接続\)** 値を選択します。
    * **[アクセス キー ID]** の値を指定します。
    * **[シークレット アクセス キー]** の値を指定します。
    * **[Test Connection]\(接続のテスト\)** を選択して、作成したリンクされたサービスの接続をテストします。
    * **[完了]** を選択します。

1. **[ソース データ ストア]** ページに、新しい AmazonS31 接続が表示されます。 **[次へ]** を選択します。

   ![ソース データ ストアの作成された接続](media/data-factory-load-data/source-data-store-created-connection.png)

1. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで次の操作を実行します。

    1. コピーするフォルダー/ファイルを参照します。 フォルダー/ファイルを選択します。
    1. 必要に応じてコピー動作を選択します。 **[Binary copy]\(バイナリ コピー\)** はオフのままにします。
    1. **[次へ]** を選択します。

    ![入力ファイルまたはフォルダーの選択](media/data-factory-load-data/source-choose-input-file.png)

1. **[file formats settings]\(ファイル形式設定\)** ページで、ファイルの関連設定を選択し、 **[次へ]** をクリックします。

   ![入力ファイルまたはフォルダーの選択](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Azure Data Explorer (コピー先) にデータをコピーする

以下で指定する Azure Data Explorer のコピー先テーブル (シンク) にデータをコピーする、Azure Data Explorer の新しいリンクされたサービスが作成されます。

#### <a name="create-the-azure-data-explorer-linked-service"></a>Azure Data Explorer のリンクされたサービスを作成する

1. **[コピー先データ ストア]** ページで、既存のデータ ストア接続を使用するか、 **[新しい接続の作成]** をクリックして新しいデータ ストアを指定できます。

    ![[Destination data store]\(コピー先データ ストア\) ページ](media/data-factory-load-data/destination-create-connection.png)

1. **[New Linked Service]\(新しいリンクされたサービス\)** ページで **[Azure Data Explorer]** を選択し、 **[続行]** を選択します

    ![Azure Data Explorer - 新しいリンクされたサービスの選択](media/data-factory-load-data/adx-select-new-linked-service.png)

1. **[New Linked Service (Azure Data Explorer)]\(新しいリンクされたサービス (Azure Data Explorer)\)** ページで、次の手順を実行します。

    ![ADX の新しいリンクされたサービス](media/data-factory-load-data/adx-new-linked-service.png)

   * Azure Data Explorer のリンクされたサービスの **[名前]** を選択します。
   * **[Account selection method]\(アカウントの選択方法\)** で: 
        * **[From Azure subscription]\(Azure サブスクリプションから\)** ラジオ ボタンを選択し、 **[Azure サブスクリプション]** アカウントを選択します。 次に、自分の **[クラスター]** を選択します。 ドロップダウンには、ユーザーに属するクラスターのみが一覧表示されることに注意してください。
        * または、 **[手動で入力]** ラジオ ボタンを選択し、自分の **[エンドポイント]** を入力します。
    * **[テナント]** を指定します。
    * **[サービス プリンシパル ID]** を入力します。
    * **[サービス プリンシパル キー]** ボタンを選択し、 **[サービス プリンシパル キー]** を入力します。
    * ドロップダウン メニューから **[データベース]** を選択します。 または、 **[編集]** チェックボックスをオンにし、データベース名を入力します。
    * **[Test Connection]\(接続のテスト\)** を選択して、作成したリンクされたサービスの接続をテストします。 セットアップに接続できる場合は、緑色のチェックマーク (**接続成功**) が表示されます。
    * **[完了]** を選択して、リンクされたサービスの作成を完了します。

    > [!NOTE]
    > このサービス プリンシパルは、Azure Data Explorer サービスにアクセスするために Azure Data Factory によって使用されます。 サービス プリンシパルの場合は、[Azure Active Directory (Azure AD) サービス プリンシパルを作成](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)します。 **Azure Key Vault** のメソッドは使用しないでください。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Azure Data Explorer データ接続を構成する

1. **[コピー先データ ストア]** が開きます。 作成した Azure Data Explorer データ接続が使用可能です。 **[次へ]** を選択して接続を構成します。

    ![ADX コピー先データ ストア](media/data-factory-load-data/destination-data-store.png)

1. **[テーブル マッピング]** で、コピー先テーブル名を設定し、 **[次へ]** を選択します。

    ![コピー先データセット テーブルのマッピング](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. **[列マッピング]** ページで:
    * 最初のマッピングは、[ADF スキーマ マッピング](/azure/data-factory/copy-activity-schema-and-type-mapping)に従って ADF によって実行されます
        * Azure Data Factory のコピー先テーブルの **[列マッピング]** を設定します。 既定のマッピングは、ソースから ADF のコピー先テーブルです。
        * 列マッピングを定義する必要がない列の選択はオフにします。
    * 2 つ目のマッピングは、Azure Data Explorer にこの表形式データが取り込まれたときに行われます。 マッピングは [CSV のマッピング規則](/azure/kusto/management/mappings#csv-mapping)に従って実行されます。 ソース データが CSV 形式でなかった場合でも、ADF ではデータが形式に変換されるので、CSV マッピングはこの段階で関連する唯一のマッピングであることに注意してください。
        * **[Azure Data Explorer (Kusto) sink properties]\(Azure Data Explorer (Kusto) シンク プロパティ\)** で、関連する **[Ingestion mapping name]\(インジェスト マッピング名\)** (省略可能) を追加し、列マッピングを使用できるようにします。
        * **[Ingestion mapping name]\(インジェスト マッピング名\)** が指定されていない場合は、 **[列マッピング]** セクションで定義された "by-name" のマッピング順序になります。 "by-name" のマッピングが失敗した場合は、Azure Data Explorer によって "by-column position" 順 (既定では by-position でマップ) でのデータの取り込みが試行されます。
    * **[次へ]** を選択します

    ![対象のデータセット列のマッピング](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. **[設定]** ページで次の操作を行います。
    * 関連する**フォールト トレランス設定**を設定します。
    * **パフォーマンス設定**:ステージングの有効化は適用されません。 **[詳細設定]** にはコストに関する考慮事項が含まれます。 特定のニーズがない場合はそのままにします。
    * **[次へ]** を選択します。

    ![データのコピーの設定](media/data-factory-load-data/copy-data-settings.png)

1. **[サマリー]** で設定を確認し、 **[次へ]** を選択します。

    ![データのコピーのサマリー](media/data-factory-load-data/copy-data-summary.png)

1. **[Deployment]\(デプロイ\) ページ**で:
    * **[モニター]** を選択して **[モニター]** タブに切り替え、パイプライン(進行状況、エラー、データ フロー) の状態 を確認します。
    * **[パイプラインの編集]** を選択して、リンクされたサービス、データセット、パイプラインを編集します。
    * **[完了]** を選択してデータのコピー タスクを完了します

    ![[Deployment]\(デプロイ\) ページ](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>次の手順

* Azure Data Factory 内の [Azure Data Explorer コネクタ](/azure/data-factory/connector-azure-data-explorer)について学習する。

* [Data Factory UI](/azure/data-factory/quickstart-create-data-factory-portal) でのリンクされたサービス、データセット、およびパイプラインの編集の詳細について学習する。

* データのクエリのための [Azure Data Explorer のクエリ](/azure/data-explorer/web-query-data)について確認する。
