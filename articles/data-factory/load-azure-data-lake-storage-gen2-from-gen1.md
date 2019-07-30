---
title: Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーする
description: Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーします
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068987"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーする

Azure Data Lake Storage Gen2 は、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) に組み込まれているビッグ データ分析専用の一連の機能です。 ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。

現在 Azure Data Lake Storage Gen1 をお使いの場合は、Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーすることで、Azure Data Lake Storage Gen2 を評価できます。

Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 このサービスを使用して、オンプレミスとクラウドベースのデータストアの豊富なセットからのデータをレイクに入力し、分析ソリューションをビルドする際の時間を節約できます。 サポートされているコネクタの一覧は、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表を参照してください。

Azure Data Factory では、スケール アウトしたマネージド データ移動ソリューションを提供しています。 Data Factory のスケールアウト アーキテクチャにより、高スループットでデータを取り込むことができます。 詳細については、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)を参照してください。

この記事では、Data Factory のデータのコピー ツールを使用して Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にデータをコピーする方法を紹介しています。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* データを備えた Azure Data Lake Storage Gen1 アカウント。
* Data Lake Storage Gen2 が有効な Azure Storage アカウント。 ストレージ アカウントがない場合、[作成します](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで、 **[リソースの作成]** 、 **[データ + 分析]** 、 **[Data Factory]** の順に選択します。
   
   ![[新規] ウィンドウのデータ ファクトリ選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、次の画像に示されているフィールドの値を指定します。 
      
   ![[新しいデータ ファクトリ] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **[名前]** :Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 \"LoadADLSDemo\" は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、 _**yourname**_ **ADFTutorialDataFactory** という名前を使用します。 データ ファクトリをもう一度作成します。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択します。 **[新規作成]** オプションを選択し、リソース グループの名前を入力することもできます。 リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 
    * **バージョン**: **[V2]** を選択します。
    * **場所**:データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 

3. **作成** を選択します。
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。 
   
   ![データ ファクトリのホーム ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 にデータを読み込む

1. **[開始]** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromADLSGen1ToGen2**」と指定します。 **[次へ]** を選択します。

    ![[プロパティ] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、 **[+ 新しい接続の作成]** を選択します。

    ![[ソース データ ストア] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. コネクタ ギャラリーから **[Azure Data Lake Storage Gen1]** を選択し、 **[続行]** を選択します。
    
    ![ソース データ ストアの [Azure Data Lake Storage Gen1] ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. **[Specify Azure Data Lake Storage Gen1 connection]\(Azure Data Lake Storage Gen1 の接続の指定\)** ページで、次の手順を行います。

   a. アカウント名に対してお使いの Data Lake Storage Gen1 を選択し、 **[テナント]** を指定または確認します。
  
   b. **[接続のテスト]** を選択して設定を検証します。 続けて、 **[完了]** を選択します。
  
   c. 新しい接続が作成されたことがわかります。 **[次へ]** を選択します。
   
   > [!IMPORTANT]
   > このチュートリアルでは、Azure リソースのマネージド ID を使用して、Azure Data Lake Storage Gen1 を認証します。 [次の手順](connector-azure-data-lake-store.md#managed-identity)に従って、マネージド ID に Azure Data Lake Storage Gen1 のアクセス許可を適切に付与します。
   
   ![Azure Data Lake Storage Gen1 アカウントを指定する](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、コピーするフォルダーとファイルを参照します。 フォルダーまたはファイルを選択し、 **[選択]** を選択します。

    ![入力ファイルまたはフォルダーの選択](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. **[Copy files recursively]\(ファイルを再帰的にコピー\)** オプションと **[バイナリ コピー]** オプションを選択し、コピーの動作を指定します。 **[次へ]** を選択します。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. **[Destination data store]\(コピー先データ ストア\)** ページで **[+ 新しい接続の作成]** 、 **[Azure Data Lake Storage Gen2]** 、 **[続行]** の順に選択します。

    ![[Destination data store]\(コピー先データ ストア\) ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. **[Specify Azure Data Lake Storage Gen2 connection]\(Azure Data Lake Storage Gen2 の接続の指定\)** ページで、次の手順を行います。

   a. **[ストレージ アカウント名]** ドロップダウン リストから目的の Data Lake Storage Gen2 に対応するアカウントを選択します。
   
   b. **[完了]** を選択して、接続を作成します。 次に、 **[次へ]** を選択します。
   
   ![Azure Data Lake Storage Gen2 アカウントを指定する](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、出力フォルダー名として「**copyfromadlsgen1**」と入力し、 **[次へ]** を選択します。 対応する Azure Data Lake Storage Gen2 ファイル システムとサブフォルダーが存在しない場合、Data Factory により、コピー中、それらが作成されます。

    ![出力フォルダーの指定](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. **[設定]** ページで、 **[次へ]** を選択して、既定の設定を使用します。

12. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. **[Deployment]\(デプロイ\)** ページで **[Monitor]\(監視\)** を選択してパイプラインを監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。

    ![パイプラインの実行を監視する](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 パイプラインの実行ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。 

    ![アクティビティの実行を監視する](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 各コピー アクティビティの実行状況の詳細を監視するには、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンク (眼鏡のイメージ) を選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視できます。

    ![アクティビティの実行状況の詳細の監視](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. データが Azure Data Lake Storage Gen2 アカウントにコピーされたことを確認します。

## <a name="best-practices"></a>ベスト プラクティス

Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 からへのアップグレードの評価の概要については、「[ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする](../storage/blobs/data-lake-storage-upgrade.md)」をご覧ください。 後続のセクションでは、データ ファクトリを使用して Data Lake Storage Gen1 から Data Lake Storage Gen2 にデータをアップグレードする際のベスト プラクティスについて紹介しています。

### <a name="data-partition-for-historical-data-copy"></a>履歴データ コピーの場合のデータ パーティション

- Data Lake Storage Gen1 の合計データ サイズが 30 TB より小さく、ファイルの数が 100 万個より少ない場合は、1 回のコピー アクティビティの実行ですべてのデータをコピーできます。
- コピーするデータの量が多い場合、あるいはバッチでデータ移行を管理し、それぞれのバッチを特定の概算時間内に完了させたい場合は、データをパーティション分割します。 パーティション分割ではまた、予想外の問題が発生するリスクが減ります。

概念実証を使用し、エンドツーエンド ソリューションを検証し、環境のコピー スループットをテストします。 概念実証の主要な手順: 

1. Data Lake Storage Gen1 から Data Lake Storage Gen2 に数 TB のデータをコピーする 1 回のコピー アクティビティでデータ ファクトリ パイプラインを作成し、コピー パフォーマンスのベースラインを取得します。 最初は[データ統合ユニット (DIU)](copy-activity-performance.md#data-integration-units) を 128 にします。 
2. ステップ 1 で取得したコピー スループットに基づいて、データ移行全体に必要な推定時間を計算します。 
3. (省略可能) 制御テーブルを作成し、移行するファイルをパーティションに分割するファイル フィルターを定義します。 ファイルをパーティションに分割する方法は次のとおりです。 

    - フォルダー名またはワイルドカード フィルターを含むフォルダー名でパーティション分割します。 これがお勧めしているメソッドです。
    - ファイルの最終変更日時でパーティション分割します。

### <a name="network-bandwidth-and-storage-io"></a>ネットワーク帯域幅とストレージ I/O 

Data Lake Storage Gen1 からデータを読み取り、Data Lake Storage Gen2 にデータを書き込むデータ ファクトリ コピー ジョブの同時実行を制御できます。 この方法で、そのストレージの I/O での使用を管理し、移行中、Data Lake Storage Gen1 に対する通常の業務作業に影響を与えることを回避できます。

### <a name="permissions"></a>アクセス許可 

データ ファクトリでは、[Data Lake Storage Gen1 コネクタ](connector-azure-data-lake-store.md)は、Azure リソース認証のために、サービス プリンシパルとマネージド ID がサポートされています。 [Data Lake Storage Gen2 コネクタ](connector-azure-data-lake-storage.md)では、Azure リソース認証のために、アカウント キー、サービス プリンシパル、マネージド ID がサポートされています。 必要なすべてのファイルまたはアクセス制御リスト (ACL) をデータ ファクトリでナビゲートしたり、コピーしたりできるようにするには、提供するアカウントに、ファイルのアクセス、読み取り、書き込み、ACL の設定 (行うことを選択した場合) を行うことができる十分に高いアクセス許可を付与します。 移行期間中、アカウントにスーパーユーザーまたは所有者ロールを付与します。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1 の ACL を保持する

Data Lake Storage Gen1 から Data Lake Storage Gen2 にアップグレードするときに、ACL をデータ ファイルと共にレプリケートする必要がある場合は、「[Data Lake Storage Gen1 の ACL を保持する](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)」をご覧ください。 

### <a name="incremental-copy"></a>増分コピー 

Data Lake Storage Gen1 から新規ファイルまたは更新されたファイルのみを読み込むには、いくつかの方法があります。

- 日時でパーティション分割されたフォルダーまたはファイル名により、新しいファイルまたは更新されたファイルを読み込みます。 たとえば、/2019/05/13/* です。
- LastModifiedDate で新しいファイルまたは更新されたファイルを読み込みます。
- サードパーティ製のツールまたはソリューションで新しいファイルまたは更新されたファイルを特定します。 次に、パラメーター、テーブル、またはファイルを利用し、データ ファクトリ パイプラインにファイルまたはフォルダーの名前を渡します。 

増分読み込みを行う適切な頻度は、Azure Data Lake Storage Gen1 内のファイルの合計数と、毎回読み込まれる新しいファイルまたは更新されたファイルのボリュームに依存します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [コピー アクティビティの概要](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 コネクタ](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 コネクタ](connector-azure-data-lake-storage.md)