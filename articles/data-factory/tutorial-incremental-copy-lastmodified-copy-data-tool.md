---
title: 新しいファイルや更新されたファイルを増分コピーするためのデータ ツール
description: Azure データ ファクトリを作成してから、データのコピー ツールを使用して LastModifiedDate を基に新しいファイルを増分的に読み込みます。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 6/10/2020
ms.openlocfilehash: 402214da75bffd278e12db94f089d64acd62221e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84730147"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>データのコピー ツールを使用し LastModifiedDate に基づいて新しいファイルおよび変更されたファイルを増分コピーする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Azure portal を使用してデータ ファクトリを作成します。 次に、データのコピー ツールを使用して、新しいファイルおよび変更されたファイルのみを Azure Blob Storage から Azure Blob Storage に増分的にコピーするパイプラインを作成します。 `LastModifiedDate` を使用して、どのファイルをコピーするかを決定します。

ここで説明した手順を完了すると Azure Data Factory によってソース ストア内のすべてのファイルがスキャンされ、`LastModifiedDate` によってファイル フィルターが適用されます。また、新しいファイルまたは前回以降に更新されたファイルのみがコピー先ストアにコピーされます。 Data Factory によって多数のファイルがスキャンされる場合は、継続時間が長くなると予想されることに注意してください。 コピーされるデータ量を減らしても、ファイルのスキャンには時間がかかります。

> [!NOTE]
> Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

このチュートリアルでは、以下のタスクを完了します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **Microsoft Azure Storage アカウント**:Blob ストレージをソースおよびシンク データ ストアとして使用します。 Azure Storage アカウントがない場合は、[ストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページの手順に従ってください。

## <a name="create-two-containers-in-blob-storage"></a>Blob ストレージに 2 つのコンテナーを作成する

次の手順を実行して、チュートリアル用の Blob ストレージを準備します。

1. **source** という名前のコンテナーを作成します。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) などのさまざまなツールを使用して実行できます。

2. **destination** という名前のコンテナーを作成します。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のウィンドウで、 **[リソースの作成]** を選びます。 **[分析]**  >  **[データ ファクトリ]** を選択します。

   ![データ ファクトリの選択](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

   データ ファクトリの名前はグローバルに一意にする必要があります。 このエラー メッセージが表示されることもあります。

   ![名前が使用できないことを示すエラー メッセージ](./media/doc-common-process/name-not-available-error.png)

   データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください。 たとえば、 _**yourname**_ **ADFTutorialDataFactory** という名前を使用します。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
3. **[サブスクリプション]** で、新しいデータ ファクトリの作成先となる Azure サブスクリプションを選択します。
4. **[リソース グループ]** で、次の手順のいずれかを行います。

    * **[既存のものを使用]** を選択し、一覧から既存のリソース グループを選択します。

    * **[新規作成]** を選択し、リソース グループの名前を入力します。
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。

5. **[バージョン]** で、 **[V2]** を選択します。
6. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがこの一覧に表示されます。 データ ファクトリで使用されるデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (Azure HDInsight など) は、他の場所やリージョンに存在していてもかまいません。
8. **［作成］** を選択します
9. データ ファクトリが作成されるとデータ ファクトリのホーム ページが表示されます。
10. 別のタブで Azure Data Factory ユーザー インターフェイス (UI) を開くには、 **[Author & Monitor]\(作成と監視\)** タイルを選択します。

    ![データ ファクトリのホーム ページ](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>データのコピー ツールを使用してパイプラインを作成する

1. **[Let's get started]\(始めましょう\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを開きます。

   ![[データのコピー] タイル](./media/doc-common-process/get-started-page.png)

2. **[プロパティ]** ページで、次の手順を行います。

    a. **[タスク名]** に「**DeltaCopyFromBlobPipeline**」と入力します。

    b. **[Task cadence or Task schedule]\(タスクの周期またはスケジュール\)** で、 **[Run regularly on schedule]\(スケジュールに従って定期的に実行する\)** を選択します。

    c. **[トリガーの種類]** で、 **[Tumbling Window]\(タンブリング ウィンドウ\)** を選択します。

    d. **[Recurrence]\(繰り返し\)** で、**15 分**と指定します。

    e. **[次へ]** を選択します。

    指定したタスク名のパイプラインが Data Factory によって作成されます。

    ![[データのコピー] の [プロパティ] ページ](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. **[ソース データ ストア]** ページで、次の手順を実行します。

    a. **[新しい接続の作成]** を選択して、接続を追加します。

    b. ギャラリーから **[Azure Blob Storage]** を選び、 **[続行]** を選択します。

    ![[Azure Blob Storage] を選択する](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. **[New Linked Service(Azure Blob Storage)]\(新しいリンクされたサービス (Azure Blob Storage)\)** ページで、 **[ストレージ アカウント名]** ボックスの一覧からお使いのストレージ アカウントを選択します。 接続をテストし、 **[作成]** を選択します。

    d. 新しいリンクされたサービスを選択し、 **[次へ]** を選択します。

   ![新しいリンクされたサービスを選択する](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、次の手順を実行します。

    a. **source** フォルダーを参照して選択し、 **[選択]** を選択します。

    ![[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. **[File loading behavior]\(ファイル読み込み動作\)** で、 **[Incremental load: LastModifiedDate]\(増分読み込み: LastModifiedDate\)** を選択します。

    c. **[Binary copy]\(バイナリ コピー\)** を選択し、 **[次へ]** を選択します。

     ![[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\) ページ](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. **[配布先データ ストア]** ページで、作成した **AzureBlobStorage** サービスを選択します。 これは、コピー元データ ストアと同じストレージ アカウントです。 **[次へ]** を選択します。

6. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、次の手順を実行します。

    a. **destination** フォルダーを参照して選択し、 **[選択]** を選択します。

    ![[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\) ページ](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. **[次へ]** を選択します。

7. **[設定]** ページで **[次へ]** を選択します。

8. **[Summary]\(概要\)** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. 左側の **[監視]** タブが自動的に選択されたことがわかります。 アプリケーションの **[監視]** タブに切り替えます。パイプラインの状態が表示されます。 **[最新の情報に更新]** を選択して、一覧を更新します。 **[パイプライン名]** の下にあるリンクを選択して、アクティビティの実行の詳細を表示するか、パイプラインを再実行します。

    ![リストを最新の情報に更新してアクティビティの実行の詳細を表示する](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. パイプラインにはアクティビティ (コピー アクティビティ) が 1 つしかないため、エントリが 1 つだけ表示されます。 コピー操作の詳細を確認するには、 **[ACTIVITY NAME]\(アクティビティ名\)** 列の **[詳細]** リンク (眼鏡アイコン) を選択します。 プロパティの詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

    ![パイプラインのコピー アクティビティ](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    BLOB ストレージ アカウントの source コンテナーにはファイルがないので、そのアカウントの destination コンテナーにコピーされたファイルは表示されません。

    ![source コンテナーまたは destination コンテナーにファイルがない](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. 空のテキスト ファイルを作成し、**file1.txt** という名前を付けます。 このテキスト ファイルを、ストレージ アカウントの source コンテナーにアップロードします。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

    ![file1.txt を作成して source コンテナーにアップロードする](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. **[パイプラインの実行]** ビューに戻るために、 **[すべてのパイプラインの実行]** を選択し、同じパイプラインが再び自動的にトリガーされるまで待ちます。  

14. 2 番目のパイプラインの実行が完了したら、前述と同じ手順に従って、アクティビティの実行の詳細を確認します。  

    BLOB ストレージ アカウントの source コンテナーから destination コンテナーに 1 つのファイル (file1.txt) がコピーされたことが表示されます。

    ![file1.txt が source コンテナーから desination コンテナーにコピーされた](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. 空のテキスト ファイルをもう 1 つ作成し、**file2.txt** という名前を付けます。 このテキスト ファイルを BLOB ストレージ アカウントの source コンテナーにアップロードします。

16. 2 つ目のテキスト ファイルに対して手順 13 と 14 を繰り返します。 このパイプライン実行中に、ストレージ アカウントの source コンテナーから destination コンテナーに新しいファイル (file2.txt) だけがコピーされたことがわかります。  

    [Azure Storage Explorer](https://storageexplorer.com/) を使用してファイルをスキャンすることにより、1 つのファイルだけがコピーされたことを確認することもできます。

    ![Azure Storage Explorer を使用してファイルをスキャンする](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>次のステップ
次のチュートリアルに進み、Azure 上の Apache Spark クラスターを使ってデータを変換する方法を学習します。

> [!div class="nextstepaction"]
>[Apache Spark クラスターを使用したクラウドのデータの変換に関するページ](tutorial-transform-data-spark-portal.md)
