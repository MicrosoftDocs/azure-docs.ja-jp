---
title: Azure Data Lake Storage Gen2 にデータを読み込む
description: Azure Data Factory を使用して Azure Data Lake Storage Gen2 にデータをコピーします
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/08/2020
ms.openlocfilehash: 8f8cfef5ed98682a1d03f7d36caa2008f4ff03b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660474"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen2 にデータを読み込む

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 は、ビッグ データ分析専用の機能セットであり、[Azure BLOB ストレージ](../storage/blobs/storage-blobs-introduction.md)に組み込まれます。 ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。

Azure Data Factory (ADF) は、フル マネージドのクラウドベース データ統合サービスです。 このサービスを使用して、オンプレミスとクラウドベースのデータストアの豊富なセットからのデータをレイクに入力し、分析ソリューションをビルドする際の時間を節約できます。 サポートされるコネクタの詳細な一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

Azure Data Factory では、スケール アウトしたマネージド データ移動ソリューションを提供しています。 ADF のスケールアウト アーキテクチャにより、高スループットでデータを取り込むことができます。 詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して "_アマゾン ウェブ サービスの S3 サービス_" から _Azure Data Lake Storage Gen2_ にデータを読み込む方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

>[!TIP]
>Azure Data Lake Storage Gen1 から Gen2 へのデータのコピーについては、[こちらのチュートリアル](load-azure-data-lake-storage-gen2-from-gen1.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Lake Storage Gen2 が有効な Azure Storage アカウント:ストレージ アカウントがない場合、[作成します](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。
* データが含まれる S3 バケットを持つ AWS アカウント:この記事では、Amazon S3 からデータをコピーする方法を示します。 同様の手順に従うことによって、その他のデータ ストアも使用できます。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、次のフィールドの値を指定します。
 
    * **Name**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 *YourDataFactoryName* は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **[場所]** :データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 

3. **［作成］** を選択します

4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。 
   
   ![データ ファクトリのホーム ページ](./media/doc-common-process/data-factory-home-page.png)

   **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 にデータを読み込む

1. **[Get started]\(開始\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。

2. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromAmazonS3ToADLS**」と指定し、 **[次へ]** を選択します。

    ![[プロパティ] ページ](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、 **[新しい接続の作成]** をクリックします。 コネクタ ギャラリーから **[Amazon S3]** を選択し、 **[続行]** を選択します。
    
    ![ソース データ ストアの S3 ページ](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. **[新しいリンクされたサービス (Amazon S3)]** ページで、次の手順を実行します。

   1. **[アクセス キー ID]** の値を指定します。
   2. **[シークレット アクセス キー]** の値を指定します。
   3. **[テスト接続]** をクリックして設定を検証し、 **[作成]** を選択します。

      ![Amazon S3 アカウントの指定](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. 新しい AmazonS3 接続が作成されたことを確認します。 **[次へ]** を選択します。 

5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、コピーするフォルダーとファイルを参照します。 フォルダー/ファイルを選択し、 **[選択]** を選択します。

    ![入力ファイルまたはフォルダーの選択](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. **[再帰的]** オプションと **[バイナリ コピー]** オプションをオンにすることで、コピーの動作を指定します。 **[次へ]** を選択します。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. **[配布先データ ストア]** ページで、 **[新しい接続の作成]** をクリックし、 **[Azure Data Lake Storage Gen2]** を選択して、 **[続行]** を選択します。

    ![[Destination data store]\(コピー先データ ストア\) ページ](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. **[新しいリンクされたサービス (Azure Data Lake Storage Gen2)]** ページで、次の手順を実行します。

   1. [ストレージ アカウント名] ボックスの一覧から目的の Data Lake Storage Gen2 に対応するアカウントを選択します。
   2. **[作成]** を選択して接続を作成します。 **[次へ]** を選択します。   

        ![Azure Data Lake Storage Gen2 アカウントを指定する](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、出力フォルダー名として「**copyfroms3**」と入力し、 **[次へ]** を選択します。 対応する ADLS Gen2 ファイル システムとサブ フォルダーが存在しない場合は、コピー中に ADF によって作成されます。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. **[設定]** ページで、 **[次へ]** を選択して、既定の設定を使用します。

    ![[設定] ページ](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。 
 
13. パイプラインの実行が正常に完了すると、手動トリガーによってトリガーされたパイプラインの実行が表示されます。 **[パイプライン名]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

    ![パイプラインの実行を監視する](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、[パイプライン名] 列の **[CopyFromAmazonS3ToADLS]** リンクを選択します。 コピー操作の詳細を確認するには、[アクティビティ名] 列の **[詳細]** リンク (眼鏡アイコン) を選択します。 ソースからシンクにコピーされるデータのボリューム、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視できます。
 
    ![アクティビティの実行を監視する](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![アクティビティの実行状況の詳細の監視](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. 表示を更新するには、[最新の情報に更新] を選択します。 再度パイプラインの実行ビューに移動するには、一番上にある **[すべてのパイプラインの実行]** を選択します。

16. データが Data Lake Storage Gen2 アカウントにコピーされたことを確認します。

## <a name="next-steps"></a>次のステップ

* [コピー アクティビティの概要](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 コネクタ](connector-azure-data-lake-storage.md)
