---
title: 'Azure Data Factory で Spark を使用してデータを変換する '
description: このチュートリアルでは、Azure Data Factory で Spark アクティビティを使用してデータを変換するための詳細な手順を説明します。
ms.service: data-factory
ms.topic: tutorial
author: nabhishek
ms.author: abnarain
ms.date: 01/10/2018
ms.openlocfilehash: 2e2a50a96402f01fe914c79d5257fc5bb4dc57a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377790"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Azure Data Factory で Spark アクティビティを使用してクラウドのデータを変換する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Azure Portal を使用して Azure Data Factory パイプラインを作成します。 このパイプラインは、Spark アクティビティとオンデマンドの Azure HDInsight のリンクされたサービスを使用して、データを変換します。 

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * Spark アクティビティを使用するパイプラインを作成します。
> * パイプラインの実行をトリガーする。
> * パイプラインの実行を監視します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure ストレージ アカウント**。 Python スクリプトと入力ファイルを作成し、Azure Storage にアップロードします。 Spark プログラムからの出力は、このストレージ アカウントに格納されます。 オンデマンドの Spark クラスターでは、同じストレージ アカウントがプライマリ ストレージとして使用されます。  

> [!NOTE]
> HDInsight は、Standard レベルで、汎用ストレージ アカウントのみをサポートしています。 アカウントが Premium または BLOB のみのストレージ アカウントでないことを確認してください。

* **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページに記載されている手順に従います。


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Python スクリプトを BLOB ストレージ アカウントにアップロードする
1. 次の内容が含まれた、**WordCount_Spark.py** という名前の Python ファイルを作成します。 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. *&lt;storageAccountName&gt;* を Azure ストレージ アカウントの名前に置き換えます。 その後、ファイルを保存します。 
1. Azure BLOB ストレージで、**adftutorial** という名前のコンテナーを作成します (存在しない場合)。 
1. **spark** という名前のフォルダーを作成します。
1. **spark** フォルダーの下に、**script** という名前のサブフォルダーを作成します。 
1. **WordCount_Spark.py** ファイルを **script** サブフォルダーにアップロードします。 


### <a name="upload-the-input-file"></a>入力ファイルをアップロードする
1. **minecraftstory.txt** という名前のファイルを作成し、任意のテキストを入力しておきます。 このテキストの単語数が Spark プログラムによってカウントされます。 
1. **spark** フォルダーの下に、**inputfiles** という名前のサブフォルダーを作成します。 
1. **inputfiles** サブフォルダーに **minecraftstory.txt** ファイルをアップロードします。 

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューの **[新規]** を選択し、**[データ + 分析]** を選択して、**[Data Factory]** を選択します。 
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. **[新しいデータ ファクトリ]** ウィンドウで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
   ![[新しいデータ ファクトリ] ウィンドウ](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は *グローバルに一意* にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更してください ( **&lt;yourname&gt;ADFTutorialDataFactory** など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
   ![名前が使用できないときのエラー](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。 
1. **[リソース グループ]** で、次の手順のいずれかを行います。
     
   - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
   - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
   このクイックスタートの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
1. **[バージョン]** で、 **[V2]** を選択します。
1. **[場所]** で、データ ファクトリの場所を選択します。 

   現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください。[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) Data Factory で使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

1. **［作成］** を選択します

1. 作成が完了すると、 **[データ ファクトリ]** ページが表示されます。 **[作成と監視]** タイルを選択して、別のタブで Data Factory UI アプリケーションを起動します。

    ![[作成と監視] タイルが表示された、データ ファクトリのホーム ページ](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>リンクされたサービスを作成します
このセクションでは、2 つのリンクされたサービスを作成します。 
    
- Azure ストレージ アカウントをデータ ファクトリにリンクする、**Azure Storage のリンクされたサービス**。 このストレージは、オンデマンドの HDInsight クラスターによって使用されます。 ここには、実行される Spark スクリプトも含まれています。 
- **オンデマンドの HDInsight のリンクされたサービス**。 Azure Data Factory によって自動的に HDInsight クラスターが作成され、Spark プログラムが実行されます。 HDInsight クラスターは、事前に構成された時間だけアイドル状態になったら削除されます。 

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

1. **[Let's get started]\(始めましょう\)** ページで、左側のパネルで **[編集]** タブに切り替えます。 

   ![[Let's get started]\(始めましょう\) ページ](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. ウィンドウの下部にある **[接続]** を選択して、 **[+ 新規]** を選択します。 

   ![新しい接続を作成するためのボタン](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、**[データ ストア]** > **[Azure Blob Storage]** を選択し、**[続行]** を選択します。 

   ![[Azure Blob Storage] タイルの選択](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. **[ストレージ アカウント名]** で一覧から名前を選択し、**[保存]** を選択します。 

   ![ストレージ アカウント名を指定するボックス](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>オンデマンドの HDInsight のリンクされたサービスを作成する

1. **[+ 新規]** ボタンをもう一度選択して、別のリンクされたサービスを作成します。 
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、**[コンピューティング]** > **[Azure HDInsight]** の順に選択し、**[続行]** を選択します。 

   ![[Azure HDInsight] タイルの選択](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を完了します。 

   a. **[名前]** に「**AzureHDInsightLinkedService**」と入力します。
   
   b. **[Type]\(タイプ\)** で **[On-demand HDInsight]\(オンデマンド HDInsight\)** が選択されていることを確認します。
   
   c. **[Azure Storage Linked Service]\(Azure Storage のリンクされたサービス\)** で **[AzureBlobStorage1]** を選択します。 このリンクされたサービスは、前の手順で作成したものです。 別の名前を使用した場合は、ここで適切な名前を指定します。 
   
   d. **[クラスターの種類]** で **[spark]** を選択します。
   
   e. **[サービス プリンシパル ID]** に、HDInsight クラスターを作成するアクセス許可を備えたサービス プリンシパルの ID を入力します。 
   
      このサービス プリンシパルは、サブスクリプションまたはクラスターが作成されるリソース グループの共同作成者ロールのメンバーである必要があります。 詳細については、[Azure Active Directory アプリケーションとサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関するページを参照してください。 **[サービス プリンシパル ID]** は "*アプリケーション ID*" に、 **[サービス プリンシパル キー]** は "*クライアント シークレット*" の値に相当します。
   
   f. **[サービス プリンシパル キー]** に、キーを入力します。 
   
   g. **[リソース グループ]** に、データ ファクトリの作成時に使用したのと同じリソース グループを選択します。 Spark クラスターは、このリソース グループに作成されます。 
   
   h. **[OS の種類]** を展開します。
   
   i. **[クラスター ユーザー名]** に名前を入力します。 
   
   j. そのユーザーの **クラスター パスワード** を入力します。 
   
   k. **[完了]** を選択します。 

   ![HDInsight のリンクされたサービスの設定](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight には、サポートされる各 Azure リージョンで使用できるコアの合計数に制限があります。 オンデマンドの HDInsight のリンクされたサービスの場合、HDInsight クラスターは、プライマリ ストレージとして使用されるのと同じ Azure ストレージの場所に作成されます。 クラスターを正常に作成するための十分なコア クォータがあることを確認します。 詳細については、「[Hadoop、Spark、Kafka などの HDInsight クラスターをセットアップする](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 

## <a name="create-a-pipeline"></a>パイプラインを作成する

1. **+** (正符号) ボタンを選択し、メニューの **[パイプライン]** を選択します。

   ![新しいパイプラインを作成するためのボタン](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. **[アクティビティ]** ツールボックスで **[HDInsight]** を展開します。 **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **Spark** アクティビティをドラッグします。 

   ![Spark アクティビティのドラッグ](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. 下部の **Spark** アクティビティ ウィンドウのプロパティで、次の手順を完了します。 

   a. **[HDI Cluster]\(HDI クラスター\)** タブに切り替えます。
   
   b. (前の手順で作成した) **AzureHDInsightLinkedService** を選択します。 
        
   ![HDInsight のリンクされたサービスの指定](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. **[Script/Jar]\(スクリプト/Jar\)** タブに切り替えて、次の手順を実行します。 

   a. **[Job Linked Service]\(ジョブのリンクされたサービス\)** で **[AzureBlobStorage1]** を選択します。
   
   b. **[ストレージを参照]** を選択します。

   ![[Script/Jar]\(スクリプト/Jar\) タブでの Spark スクリプトの指定](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. **adftutorial/spark/script** フォルダーに移動します。**WordCount_Spark.py** を選択し、**[完了]** を選択します。      

1. パイプラインを検証するために、ツール バーの **[検証]** ボタンを選択します。 **>>** (右矢印) ボタンを選択して、検証ウィンドウを閉じます。 
    
   ![[検証] ボタン](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. **[すべて公開]** を選択します。 Data Factory UI により、エンティティ (リンクされたサービスとパイプライン) が Azure Data Factory サービスに公開されます。 
    
   ![[すべて公開] ボタン](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする
ツール バーの **[トリガーの追加]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。 

![[トリガー] ボタンと [Trigger Now]\(今すぐトリガー\) ボタン](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. **[監視]** タブに切り替えます。パイプラインが実行されていることを確認します。 Spark クラスターの作成には約 20 分かかります。 
   
1. **[最新の情報に更新]** を定期的にクリックして、パイプラインの実行の状態を確認します。 

   ![[最新の情報に更新] ボタンが表示されている、パイプライン実行を監視するためのタブ](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. パイプラインの実行に関連付けられているアクティビティの実行を表示するために、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** を選択します。

   ![パイプラインの実行の状態](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   上部の **[All Pipeline Runs]\(すべてのパイプラインの実行\)** リンクを選択すると、パイプラインの実行ビューに戻ることができます。

   !["アクティビティの実行" ビュー](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>出力を検証する
adftutorial コンテナーの spark/otuputfiles/wordcount フォルダーに出力ファイルが作成されていることを確認します。 

![出力ファイルの場所](./media/tutorial-transform-data-spark-portal/verity-output.png)

このファイルには、入力テキスト ファイルに含まれている単語と、各単語がファイル内に出現する回数が含まれています。 次に例を示します。 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインでは、Spark アクティビティとオンデマンドの HDInsight のリンクされたサービスを使用して、データを変換します。 以下の方法を学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * Spark アクティビティを使用するパイプラインを作成します。
> * パイプラインの実行をトリガーする。
> * パイプラインの実行を監視します。

仮想ネットワークにある Azure HDInsight クラスター上で Hive スクリプトを実行してデータを変換する方法については、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [チュートリアル: Azure Virtual Network で Hive を使用してデータを変換する](tutorial-transform-data-hive-virtual-network-portal.md)。





