---
title: Azure Data Lake Storage Gen1 にデータを読み込む
description: Azure Data Factory を使用して Azure Data Lake Storage Gen1 にデータをコピーします
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 1b1b19814709451bdbbea97462c459149484e71f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415864"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Data Lake Storage Gen1 へのデータの読み込み

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (旧称 Azure Data Lake Store) は、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Azure Data Lake Storage Gen1 では、任意のサイズ、型、および取り込み速度のデータをキャプチャできます。 データは、運用分析や調査分析のために 1 か所でキャプチャされます。

Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 このサービスを使用して、既存のシステムのデータで Azure Data Lake を設定し、分析ソリューションを構築する際の時間を節約できます。

Azure Data Factory には、Data Lake Storage Gen1 にデータを読み込む際に次の利点があります。

* **簡単なセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**:オンプレミスとクラウドベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。 詳しい一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**:データは HTTPS または ExpressRoute 経由で転送されます。 グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **ハイ パフォーマンス**: 最大 1 GB/s の速度で Data Lake Storage Gen1 にデータを読み込みます。 詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して "_Amazon S3 から Data Lake Storage Gen1 にデータを読み込む_" 方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

> [!NOTE]
> 詳しくは、「[Copy data to or from Data Lake Storage Gen1 by using Azure Data Factory](connector-azure-data-lake-store.md)」(Azure Data Factory を使用した Data Lake Storage Gen1 のデータのコピー) をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Lake Storage Gen1 アカウント: Data Lake Storage Gen1 アカウントがない場合は、「[Data Lake Storage Gen1 アカウントを作成する](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)」の手順を参照してください。
* Amazon S3: この記事では、Amazon S3 からデータをコピーする方法を示します。 同様の手順に従うことによって、その他のデータ ストアも使用できます。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[分析]**  >  **[Data Factory]** の順に選択します。
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、次の画像に示されているフィールドの値を指定します。 
      
   ![[新しいデータ ファクトリ] ページ](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Name**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 \"LoadADLSG1Demo\" は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **[場所]** :データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 このようなデータ ストアには、Azure Data Lake Storage Gen1、Azure Storage、Azure SQL Database などがあります。

3. **［作成］** を選択します
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。 
   
   ![データ ファクトリのホーム ページ](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-data-lake-storage-gen1"></a>Data Lake Storage Gen1 にデータを読み込む

1. **[Get started]\(開始\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromAmazonS3ToADLS**」と指定し、 **[次へ]** を選択します。

    ![[プロパティ] ページ](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックします。

    ![[ソース データ ストア] ページ](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    **[Amazon S3]** を選択し、 **[続行]** を選択します。
    
    ![ソース データ ストアの S3 ページ](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. **[Amazon S3 接続の指定]** ページで、次の手順を実行します。 
   1. **[アクセス キー ID]** の値を指定します。
   2. **[シークレット アクセス キー]** の値を指定します。
   3. **[完了]** を選択します。
   
      ![Amazon S3 アカウントの指定](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. 新しい接続が表示されます。 **[次へ]** を選択します。
   
   ![Amazon S3 アカウントの指定](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、コピーするフォルダーとファイルを参照します。 フォルダーまたはファイルを選択し、 **[選択]** 、 **[次へ]** の順に選択します。

    ![入力ファイルまたはフォルダーの選択](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. **[Copy files recursively]\(ファイルを再帰的にコピー\)** オプションと **[バイナリ コピー]** (ファイルをそのままコピー) オプションをオンにして、コピーの動作を選択します。 **[次へ]** を選択します。

    ![出力フォルダーの指定](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. **[配布先データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックし、 **[Azure Data Lake Storage Gen1]** を選択して、 **[続行]** を選択します。

    ![[Destination data store]\(コピー先データ ストア\) ページ](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. **[New Linked Service (Azure Data Lake Storage Gen1)]\(新しいリンク サービス (Azure Data Lake Storage Gen1)\)** ページで、次の手順を実行します。 

   1. **[Data Lake Store アカウント名]** で、Data Lake Storage Gen1 アカウントを選択します。
   2. **[テナント]** を指定し、[完了] を選択します。
   3. **[次へ]** を選択します。
   
   > [!IMPORTANT]
   > このチュートリアルでは、Azuure リソースのマネージド ID を使用して、Data Lake Storage Gen1 アカウントを認証します。 [次の手順](connector-azure-data-lake-store.md#managed-identity)に従って、MSI に Data Lake Storage Gen1 のアクセス許可を適切に付与します。
   
   ![Data Lake Storage Gen1 アカウントの指定](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、出力フォルダー名として「**copyfroms3**」と入力し、 **[次へ]** を選択します。 

    ![出力フォルダーの指定](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. **[設定]** ページで **[次へ]** を選択します。

    ![[設定] ページ](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。

    ![パイプラインの実行を監視する](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 パイプラインの実行ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。 

    ![アクティビティの実行を監視する](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. 各コピー アクティビティの実行状況の詳細を監視するには、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンクを選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視することができます。

    ![アクティビティの実行状況の詳細の監視](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. データが Data Lake Storage Gen1 アカウントにコピーされたことを確認します。 

    ![Data Lake Storage Gen1 出力の確認](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>次のステップ

次の資料に進んで、Data Lake Storage Gen1 のサポートを確認します。 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1 コネクタ](connector-azure-data-lake-store.md)
