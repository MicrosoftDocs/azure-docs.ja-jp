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
ms.date: 05/13/2019
ms.openlocfilehash: ff43173823919f25f85d32996b77265a8a5864c3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926838"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen2 にデータを読み込む

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
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、次の画像に示されているフィールドの値を指定します。 
      
   ![[新しいデータ ファクトリ] ページ](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **名前**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 \"LoadADLSDemo\" は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、 _**yourname**_ **ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **場所**:データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 

3. **作成** を選択します。
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。 
   
   ![データ ファクトリのホーム ページ](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 にデータを読み込む

1. **[Get started]\(開始\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromAmazonS3ToADLS**」と指定し、 **[次へ]** を選択します。

    ![[プロパティ] ページ](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックします。

    ![[ソース データ ストア] ページ](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    コネクタ ギャラリーから **[Amazon S3]** を選択し、 **[続行]** を選択します。
    
    ![ソース データ ストアの S3 ページ](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. **[Amazon S3 接続の指定]** ページで、次の手順を実行します。

   1. **[アクセス キー ID]** の値を指定します。
   2. **[シークレット アクセス キー]** の値を指定します。
   3. **[テスト接続]** をクリックして設定を検証し、 **[完了]** を選択します。
   4. 新しい接続が作成されたことを確認します。 **[次へ]** を選択します。
   
      ![Amazon S3 アカウントの指定](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、コピーするフォルダーとファイルを参照します。 フォルダーまたはファイルを選択し、 **[選択]** を選択します。

    ![入力ファイルまたはフォルダーの選択](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. **[Copy files recursively]\(ファイルを再帰的にコピー\)** オプションと **[バイナリ コピー]** オプションをオンにし、コピーの動作を指定します。 **[次へ]** を選択します。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. **[配布先データ ストア]** ページで、 **[+ 新しい接続の作成]** をクリックし、 **[Azure Data Lake Storage Gen2]** を選択して、 **[続行]** を選択します。

    ![[Destination data store]\(コピー先データ ストア\) ページ](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. **[Specify Azure Data Lake Storage connection]\(Azure Data Lake Storage の接続の指定\)** ページで、次の手順を実行します。

   1. [ストレージ アカウント名] ボックスの一覧から目的の Data Lake Storage Gen2 に対応するアカウント名を選択します。
   2. **[完了]** を選択して、接続を作成します。 次に、 **[次へ]** を選択します。
   
   ![Azure Data Lake Storage Gen2 アカウントを指定する](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、出力フォルダー名として「**copyfroms3**」と入力し、 **[次へ]** を選択します。 ADF は、対応する ADLS Gen2 ファイル システムとサブ フォルダーがない場合には、コピー中に作成します。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. **[設定]** ページで、 **[次へ]** を選択して、既定の設定を使用します。

    ![[設定] ページ](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプラインを監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。

    ![パイプラインの実行を監視する](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 パイプラインの実行ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。 

    ![アクティビティの実行を監視する](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. 各コピー アクティビティの実行状況の詳細を監視するには、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンク (眼鏡のイメージ) を選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視することができます。

    ![アクティビティの実行状況の詳細の監視](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. データが Data Lake Storage Gen2 アカウントにコピーされたことを確認します。

## <a name="next-steps"></a>次の手順

* [コピー アクティビティの概要](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 コネクタ](connector-azure-data-lake-storage.md)
