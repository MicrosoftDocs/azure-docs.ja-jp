---
title: Azure Data Factory - よく寄せられる質問
description: Azure データ ファクトリについてよく寄せられる質問です。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 450a12ae5ae1e13c9cc9b5599a41b6c29d740a9f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785327"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - よく寄せられる質問
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory のよく寄せられる質問](../frequently-asked-questions.md)に関するページを参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>一般的な質問
### <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。
Data Factory は、 **データの移動や変換を自動化する** クラウドベースのデータ統合サービスです。 原材料を機械で加工して最終製品を作成する工場と同じように、Data Factory は生データを収集してすぐに使用できる情報に変換する既存のサービスを調整します。

Data Factory を使用すると、データ ドリブンのワークフローを作成して、オンプレミスとクラウドのデータ ストアの間でデータを移動したり、Azure HDInsight や Azure Data Lake Analytics などのコンピューティング サービスを使用してデータを処理/変換したりすることができます。 必要なアクションを実行するパイプラインを作成した後で、それを定期的に実行するようにスケジュールすることができます (毎時、毎日、毎週など)。   

詳細については、「[概要と主要な概念](data-factory-introduction.md)」を参照してください。

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Azure Data Factory の料金の詳細はどこで確認できますか。
Azure Data Factory の価格の詳細については、[Data Factory の価格の詳細][adf-pricing-details]に関するページを参照してください。  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Azure Data Factory の利用はどのように開始するのですか。
* Azure Data Factory の概要については、 [Azure Data Factory サービスの概要](data-factory-introduction.md)に関するページをご覧ください。
* コピー アクティビティを使用して **データのコピー/移動** を行う方法に関するチュートリアルについては、 [Azure Blob Storage から Azure SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するページを参照してください。
* HDInsight Hive アクティビティを使用する **データの変換** 方法に関するチュートリアルについては、 「[チュートリアル: 初めての Data Factory の作成 (概要)](data-factory-build-your-first-pipeline.md)」を参照してください。

### <a name="what-is-the-data-factorys-region-availability"></a>Data Factory を利用可能なリージョンはどこですか。
Data Factory は、**米国西部** と **北ヨーロッパ** で使用できます。 Data Factory で使用されるコンピューティング サービスとストレージ サービスは、その他のリージョンでも利用できます。 「 [サポートされているリージョン](data-factory-introduction.md#supported-regions)」を参照してください。

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Data Factory/パイプライン/アクティビティ/データセットの数の制限値はいくつですか。
詳細については、記事「 **Azure サブスクリプションとサービスの制限、クォータ、制約** 」の [Azure Data Factory の制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) に関するセクションを参照してください。

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Azure Data Factory サービスではどのような作成エクスペリエンスまたは開発者エクスペリエンスが提供されますか。
データ ファクトリの生成または作成は、次のツール/SDK を使用して行う事ができます。

* **Visual Studio** : Visual Studio を使用して、Azure Data Factory を作成することができます。 詳細については、「 [Visual Studio を使用した初めての Azure Data Factory パイプラインの作成](data-factory-build-your-first-pipeline-using-vs.md) 」を参照してください。
* **Azure PowerShell** : PowerShell を使用する Data Factory の作成に関するチュートリアルについては、 [Azure PowerShell を使用した Azure Data Factory の作成と監視](data-factory-build-your-first-pipeline-using-powershell.md) に関するページを参照してください。 Data Factory コマンドレットの包括的なドキュメントについては、MSDN ライブラリの [Data Factory コマンドレット リファレンス][adf-powershell-reference] コンテンツをご覧ください。
* **.NET クラス ライブラリ** : Data Factory .NET SDK を使用すると、プログラムでデータ ファクトリを作成できます。 .NET SDK を使用したデータ ファクトリの作成のチュートリアルについては、 [.NET SDK を使用したデータ ファクトリの作成、監視、管理](data-factory-create-data-factories-programmatically.md) に関するページをご覧ください。 Data Factory .NET SDK の包括的なドキュメントについては、[Data Factory クラス ライブラリ リファレンス][msdn-class-library-reference]をご覧ください。
* **REST API** : Azure Data Factory サービスで公開されている REST API を使用して、データ ファクトリを作成およびデプロイすることもできます。 Data Factory REST API の包括的なドキュメントについては、[Data Factory REST API リファレンス][msdn-rest-api-reference]をご覧ください。
* **Azure Resource Manager テンプレート** 詳細については、「[チュートリアル: Azure Resource Manager テンプレートを使用した初めての Azure データ ファクトリの作成](data-factory-build-your-first-pipeline-using-arm.md)」をご覧ください。

### <a name="can-i-rename-a-data-factory"></a>Data Factory の名前を変更できますか。
いいえ。 他の Azure のリソースと同様に、Azure Data Factory の名前は変更できません。

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Azure サブスクリプション間でデータ ファクトリを移動できますか。
はい。 次の図に示されているように、データ ファクトリ ブレードの **[移動]** ボタンを使用します。

![データ ファクトリの移動](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Data Factory ではどのようなコンピューティング環境がサポートされますか。
次の表は、Data Factory でサポートされているコンピューティング環境と、その環境で実行できるアクティビティの一覧です。

| Compute 環境 | activities |
| --- | --- |
| [On-demand HDInsight クラスター](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)または[独自の HDInsight クラスター](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md)、[Hive](data-factory-hive-activity.md)、[Pig](data-factory-pig-activity.md)、[MapReduce](data-factory-map-reduce.md)、[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (クラシック)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[スタジオ (クラシック) アクティビティ:バッチ実行とリソースの更新](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service)、[Azure Synapse Analytics](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service)、[SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[ストアド プロシージャ](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Azure Data Factory と SQL Server Integration Services (SSIS) はどのように違いますか。 
MVP (Most Valued Professional) の 1 人である Reza Rad の「[Azure Data Factory vs.SSIS (Azure Data Factory とSSIS)](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS)」プレゼンテーションを参照してください。 Data Factory の最近の変更点の一部は、スライド デッキに表示されない場合があります。 Azure Data factory には、継続的に機能が追加されています。 Azure Data factory には、継続的に機能が追加されています。 これらの更新は、今年中に、マイクロソフトのデータ統合テクノロジの比較に組み込まれる予定です。   

## <a name="activities---faq"></a>アクティビティ - FAQ
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Data Factory パイプラインで使用できるアクティビティには、どのような種類がありますか。
* [データ移動アクティビティ](data-factory-data-movement-activities.md) で、データを移動します。
* [データ変換アクティビティ](data-factory-data-transformation-activities.md) で、データを処理/変換します。

### <a name="when-does-an-activity-run"></a>アクティビティはいつ実行されますか。
アクティビティが実行されるタイミングは、出力データ テーブルの **availability** 構成設定によって決定されます。 入力データセットが指定される場合、アクティビティは、実行を開始する前に、入力データのすべての依存関係が満たされている (つまり、 **[準備完了]** 状態になっている) かどうかを確認します。

## <a name="copy-activity---faq"></a>コピー アクティビティ - FAQ
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>1 つのパイプラインに複数のアクティビティを設定する方法とアクティビティごとに別個のパイプラインを使用する方法ではどちらの方法がよいですか。
パイプラインでは、関連する複数のアクティビティをまとめることが想定されています。 複数のアクティビティを接続するデータセットがパイプラインの外部の他のアクティビティによって使用されない場合は、1 つのパイプラインにそれらのアクティビティを保持できます。 これにより、パイプラインのアクティブな期間を揃えるためにこれを連結する必要はありません。 また、パイプライン内部のテーブルのデータの整合性が、パイプラインを更新するときに、よりうまく保持されるようになります。 パイプラインを更新するとき、基本的にパイプライン内のすべてのアクティビティが停止、削除された後、もう一度作成されます。 作成操作の観点では、パイプラインの 1 つの JSON ファイルで関連するアクティビティ内のデータのフローを簡単に見ることができるようになる可能性があります。

### <a name="what-are-the-supported-data-stores"></a>どのようなデータ ストアがサポートされますか。
Data Factory のコピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。 Data Factory は次のデータ ストアをサポートしています。 また、任意のソースのデータを任意のシンクに書き込むことができます。 データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> \* が付いたデータ ストアは、オンプレミスと Azure IaaS のどちらでもサポートされます。ただし、オンプレミス/Azure IaaS のコンピューターに [Data Management Gateway](data-factory-data-management-gateway.md) をインストールする必要があります。

### <a name="what-are-the-supported-file-formats"></a>どのようなファイル形式がサポートされますか。
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>コピー操作はどこで実行されますか。
詳細については、「 [グローバルに使用できるデータの移動](data-factory-data-movement-activities.md#global) 」を参照してください。 簡単に言うと、オンプレミスのデータ ストアが関係する場合、コピー操作はオンプレミス環境内の Data Management Gateway で実行されます。 また、2 つのクラウド ストア間でデータの移動を行う場合、コピー操作は同じ地理的場所のシンクの場所に最も近いリージョンで実行されます。

## <a name="hdinsight-activity---faq"></a>HDInsight アクティビティ - FAQ
### <a name="what-regions-are-supported-by-hdinsight"></a>HDInsight でサポートされているリージョンはどこですか。
[HDInsight の価格詳細][hdinsight-supported-regions]に関する記事で、利用可能な地域に関するセクションをご覧ください。

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>オンデマンドの HDInsight クラスターはどのリージョンで使用されますか。
オンデマンドの HDInsight クラスターは、クラスターで使用するように指定したストレージが存在するのと同じリージョンに作成されます。    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>追加のストレージ アカウントを HDInsight クラスターに関連付けるにはどうすればよいですか。
独自の HDInsight クラスター (BYOC - Bring Your Own Cluster: クラスターを自分で用意する) を使用している場合は、次のトピックをご覧ください。

* [代替のストレージ アカウントとメタストアでの HDInsight クラスターの使用][hdinsight-alternate-storage]
* [HDInsight Hive での追加ストレージ アカウントの使用][hdinsight-alternate-storage-2]

Data Factory サービスで作成されたオンデマンド クラスターを使用している場合は、Data Factory で自動的に登録できるように、HDInsight のリンクされたサービス用に追加のストレージ アカウントを指定します。 オンデマンドのリンクされたサービスの JSON 定義内で、次の JSON のスニペットに示すように、 **additionalLinkedServiceNames** プロパティを使用して、代替のストレージ アカウントを指定します。

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
上記の例において、otherLinkedServiceName1 と otherLinkedServiceName2 は、HDInsight クラスターが代替ストレージ アカウントにアクセスするために必要な資格情報がその定義に含まれているリンクされたサービスを表します。

## <a name="slices---faq"></a>スライス - FAQ
### <a name="why-are-my-input-slices-not-in-ready-state"></a>入力スライスが準備完了状態にならないのはなぜですか。
一般的なミスとして、入力データがデータ ファクトリの外部に存在する (データ ファクトリによって生成されたものでない) ときに、入力データセットの **external** プロパティが **true** に設定されていないことが挙げられます。

次の例では、**dataset1** のみ、**external** を true に設定する必要があります。  

**DataFactory1** パイプライン 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 パイプライン 2: dataset3-> activity3 -> dataset4

別のデータ ファクトリに dataset4 (データ ファクトリ 1 のパイプライン 2 で生成) を受け取るパイプラインがある場合、dataset4 は、外部のデータセットとして指定します。dataset4 は異なるデータ ファクトリ (DataFactory2 ではなく DataFactory1) によって生成されたものであるためです。  

**DataFactory2**    
パイプライン 1: dataset4->activity4->dataset5

external プロパティが適切に設定されている場合は、入力データセットの定義で指定された場所に入力データが存在しているかどうかを確認してください。

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>スライスを毎日生成している場合、午前 0 時以外の時刻にスライスを実行するにはどうすればよいですか。
**offset** プロパティを使用して、スライスを生成する時刻を指定します。 このプロパティの詳細については、「 [データセットの可用性](data-factory-create-datasets.md#dataset-availability) 」セクションをご覧ください。 以下に簡単な例を示します。

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
既定の設定である真夜中ではなく、毎日 **午前 6 時** にスライスを開始する例です。     

### <a name="how-can-i-rerun-a-slice"></a>スライスを再実行するにはどうすればよいですか。
スライスを再実行するには、次のどちらかの方法を使用します。

* 監視と管理アプリを使用して、アクティビティ ウィンドウまたはスライスを再実行します。 「 [選択したアクティビティ ウィンドウを再実行する](data-factory-monitor-manage-app.md#perform-batch-actions) 」を参照してください。   
* Azure Portal のスライスの **[データ スライス]** ブレードで、コマンド バーの **[実行]** をクリックします。
* スライスの状態を **Waiting** に設定して、**Set-AzDataFactorySliceStatus** コマンドレットを実行します。   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  コマンドレットの詳細については、[Set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] に関するページをご覧ください。

### <a name="how-long-did-it-take-to-process-a-slice"></a>スライスの処理にかかった時間を調べるにはどうすればよいですか。
データ スライスの処理に要した時間は、監視と管理アプリのアクティビティ ウィンドウ エクスプローラーを使用して調べます。 詳細については、「 [アクティビティ ウィンドウ エクスプローラー](data-factory-monitor-manage-app.md#activity-window-explorer) 」を参照してください。

同じことは、Azure ポータルから次の手順で行うこともできます。  

1. データ ファクトリの **[データ ファクトリ]** ブレードの **[データセット]** タイルをクリックします。
2. **[データセット]** ブレードの特定のデータセットをクリックします。
3. **[テーブル]** ブレードの **[最近使用したスライス]** ボックスの一覧で、目的のスライスを選択します。
4. **[データ スライス]** ブレードの **[アクティビティの実行]** ボックスの一覧で、[アクティビティの実行] をクリックします。
5. **[アクティビティの実行の詳細]** ブレードで、 **[プロパティ]** タイルをクリックします。
6. **[期間]** フィールドに表示されている値を確認します。 この値が、スライスの処理にかかった時間です。   

### <a name="how-to-stop-a-running-slice"></a>実行中のスライスを停止するにはどうすればよいですか
パイプラインの実行を停止する必要がある場合は、[Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) コマンドレットを使用できます。 現時点では、パイプラインを中断しても、進行中のスライスの実行は停止しません。 進行中の実行が完了すると、追加のスライスは取得されません。

すべての実行をすぐに停止するには、パイプラインをいったん削除した後で再作成するしかありません。 パイプラインを削除する場合は、パイプラインによって使用されているテーブルとリンクされたサービスを削除する必要はありません。

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive