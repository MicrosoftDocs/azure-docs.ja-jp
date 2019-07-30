---
title: Azure Data Factory のトラブルシューティング
description: Azure Data Factory の使用に関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
robots: noindex
ms.openlocfilehash: cc880885777cbca67d6fb39b90feadc889339f76
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836175"
---
# <a name="troubleshoot-data-factory-issues"></a>Data Factory のトラブルシューティング
> [!NOTE]
> この記事は、Azure Data Factory のバージョン 1 に適用されます。 

この記事では、Azure Data Factory を使用する場合の問題に対するトラブルシューティングのヒントを提供します。 ここでは、このサービスを使用する場合に発生する可能性があるすべての問題を取り上げているわけではなく、一部の問題と一般的なトラブルシューティングのヒントについて説明しています。   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>エラー:サブスクリプションが名前空間 'Microsoft.DataFactory' を使用するように登録されていません
このエラー メッセージが表示される場合は、Azure Data Factory のリソース プロバイダーがコンピューターに登録されていません。 以下の手順を実行します。

1. Azure PowerShell を起動します。
2. 次のコマンドを使用して、Azure アカウントにログインします。

    ```powershell
    Connect-AzAccount
    ```
3. 次のコマンドを実行して、Azure Data Factory プロバイダーを登録します。

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>問題:Data Factory コマンドレットを実行する際の未承認エラー
Azure PowerShell で使用する Azure アカウントまたはサブスクリプションが正しくない可能性があります。 次のコマンドレットを使用して、Azure PowerShell で使用する適切な Azure アカウントとサブスクリプションを選択してください。

1. Connect-AzAccount - 適切なユーザー ID とパスワードを使用します
2. Get-AzSubscription - アカウントのサブスクリプションをすべて表示します。
3. Select-AzSubscription &lt;サブスクリプション名&gt; - 適切なサブスクリプションを選択します。 Azure ポータルでデータ ファクトリの作成に使用するのと同じものを使用します。

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>問題:Azure portal から Data Management Gateway の高速セットアップを起動できない
Data Management Gateway の高速セットアップを起動するには、Internet Explorer または Microsoft ClickOnce と互換性のある Web ブラウザーが必要です。 高速セットアップの開始が失敗した場合は、次のいずれかの操作を行います。

* Internet Explorer または Microsoft の ClickOnce と互換性のある Web ブラウザーを使用します。

    Chrome を使用する場合は、 [Chrome Web ストア](https://chrome.google.com/webstore/)に移動し、"ClickOnce" キーワードで検索して、ClickOnce 拡張機能のいずれかを選択してインストールします。

    Firefox についても、同じ操作を実行します (アドインをインストール)。 ツール バーの [メニューを開く] ボタン (右上隅にある 3 本の横線) をクリックして、[アドオン] をクリックし、"ClickOnce" キーワードを使用して検索し、ClickOnce の拡張機能のいずれかを選択してインストールします。
* ポータルの同じブレードに表示される **[手動セットアップ]** リンクを使用します。 この方法を使用して、インストール ファイルをダウンロードし、手動で実行します。 インストールが成功すると、[Data Management Gateway Configuration (Data Management Gateway の構成)] ダイアログ ボックスが表示されます。 ポータル画面の **キー** をコピーし、構成マネージャーでそれを使用して、手動でゲートウェイをサービスに登録します。  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>問題:オンプレミス SQL Server に接続できない
ゲートウェイ コンピューターで **Data Management Gateway 構成マネージャー**を起動し、 **[トラブルシューティング]** タブを使用して、ゲートウェイ コンピューターから SQL Server への接続をテストします。 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、 [ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) に関するセクションをご覧ください。   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>問題:入力スライスの状態が Waiting のままになっている
このスライスの状態は、さまざまな理由から **Waiting** になっている可能性があります。 一般的な理由の 1 つとして、**external** プロパティが **true** に設定されていないことが挙げられます。 Azure Data Factory の範囲外で生成されるデータセットの場合、 **external** プロパティによるマーキングが必要です。 このプロパティは、データが外部データであり、データ ファクトリ内のパイプラインでサポートされていないことを示します。 それぞれのストアでデータが使用可能になると、データ スライスは **Ready** とマーキングされます。

**external** プロパティの使用方法については、次の例を参照してください。 external を true に設定するときに、必要に応じて **externalData*** を指定できます。

このプロパティの詳細については、 [データセット](data-factory-create-datasets.md) に関する記事を参照してください。

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

このエラーを解決するには、入力テーブルの JSON 定義に **external** プロパティと **externalData** セクション (省略可能) を追加し、テーブルをもう一度作成します。

### <a name="problem-hybrid-copy-operation-fails"></a>問題:ハイブリッド コピー操作に失敗する
Data Management Gateway を使用したオンプレミス データ ストアへのコピーや、それからのコピーの問題をトラブルシューティングする手順については、[ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)に関するセクションを参照してください。

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>問題:オンデマンドの HDInsight プロビジョニングが失敗する
種類が HDInsightOnDemand のリンクされたサービスを使用する場合、Azure Blob Storage を指す linkedServiceName を指定する必要があります。 Data Factory サービスは、このストレージを使用して、オンデマンド HDInsight クラスターのログとサポート ファイルを格納します。  たまに、オンデマンド HDInsight クラスターのプロビジョニングが失敗し、次のエラー メッセージが表示されることがあります。

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

通常、このエラーは、linkedServiceName で指定されたストレージ アカウントの場所が、HDInsight のプロビジョニングが行われているのと同じデータ センターの場所でないことを示します。 たとえば、Azure Data Factory の場所が米国西部で、Azure ストレージが米国東部の場合、米国西部でのオンデマンド プロビジョニングは失敗します。

さらに、2 つ目の JSON プロパティ additionalLinkedServiceNames では、オンデマンド HDInsight に追加のストレージ アカウントを指定できます。 これらの追加のリンクされたストレージ アカウントは HDInsight クラスターと同じ場所に存在する必要があります。そうでないと、同じエラーで失敗します。

### <a name="problem-custom-net-activity-fails"></a>問題:カスタム .NET アクティビティが失敗する
詳細な手順については、 [カスタム アクティビティのパイプラインのデバッグ](data-factory-use-custom-activities.md#troubleshoot-failures) に関するセクションを参照してください。

## <a name="use-azure-portal-to-troubleshoot"></a>Azure ポータルを使用したトラブルシューティング
### <a name="using-portal-blades"></a>ポータルのブレードの使用
手順については、 [パイプラインの監視](data-factory-monitor-manage-pipelines.md) に関するセクションを参照してください。

### <a name="using-monitor-and-manage-app"></a>監視と管理アプリの使用
詳細については、 [監視と管理アプリを使用した Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md) に関するページを参照してください。

## <a name="use-azure-powershell-to-troubleshoot"></a>Azure PowerShell を使用したトラブルシューティング
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Azure PowerShell を使用したエラーのトラブルシューティング
詳細については、 [Azure PowerShell を使用した Data Factory パイプラインの監視](data-factory-monitor-manage-pipelines.md) に関するセクションを参照してください。

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
