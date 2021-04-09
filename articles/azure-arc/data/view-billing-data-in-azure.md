---
title: 課金データを Azure にアップロードして Azure portal に表示する
description: 課金データを Azure にアップロードして Azure portal に表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 7ef1cd43d2efbc5ab92cc2b4cba4d237805d8921
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202656"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>課金データを Azure にアップロードして Azure portal に表示する

> [!IMPORTANT] 
>  Azure Arc 対応データ サービスの使用コストは、プレビュー期間中は発生しません。 課金システムはエンド ツー エンドで動作しますが、課金メーターは $0 に設定されます。  このシナリオに従った場合、請求書の現在のエントリでは、サービスの名前は **hybrid data services (ハイブリッド データ サービス)** になり、リソースの種類は **Microsoft.AzureArcData/`<resource type>`** という名前になります。 作成した各データ サービス - Azure Arc のレコードを表示できますが、各レコードの請求額は $0 になります。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>接続モード - 課金データに対する影響

将来的に、次の 2 つのモードで Azure Arc 対応データ サービスを実行できるようになります。

- **間接接続** - Azure に直接接続することはできません。 データは、エクスポートとアップロードのプロセスを通してのみ Azure に送信されます。 現在のプレビューでは、Azure Arc データ サービスのすべてのデプロイはこのモードで動作します。
- **直接接続** - このモードでは、Azure Arc 対応 Kubernetes サービスに存在する依存関係により、Azure と、Azure Arc 対応データ サービスが実行されている Kubernetes クラスターとの間に、直接接続が提供されます。 これにより、より多くの機能が有効になり、Azure PaaS でデータ サービスを管理するのと同じように、Azure portal と Azure CLI を使用して Azure Arc 対応データ サービスを管理することもできます。  この接続モードはプレビューではまだ使用できませんが、近日中に公開される予定です。

[接続モード](./connectivity.md)に関するページで、両者の違いの詳細を確認できます。

間接接続モードでの課金データは、Azure Arc データ コントローラーからセキュリティで保護されたファイルに定期的にエクスポートされた後、Azure にアップロードされて処理されます。  将来の直接接続モードでの課金データは、だいたい 1 時間に 1 回 Azure に自動的に送信され、サービスのコストをほぼリアルタイムで見ることができます。 間接接続モードでのデータのエクスポートとアップロードのプロセスは、スクリプトを使用して自動化でき、それを自動的に行うサービスが作成される可能性もあります。

## <a name="upload-billing-data-to-azure"></a>課金データを Azure にアップロードする

課金データを Azure にアップロードするには、まず次のことを行う必要があります。

1. Azure Arc 対応データ サービスがまだない場合は作成します。 たとえば、次のいずれかを作成します。
   - [Azure Arc で Azure SQL Managed Instance を作成する](create-sql-managed-instance.md)
   - [Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md)
1. [リソース インベントリ、使用状況データ、メトリック、およびログを Azure Monitor にアップロードします](upload-metrics-and-logs-to-azure-monitor.md) (まだ実行していない場合)。
1. 課金テレメトリ収集プロセスで課金データを収集できるように、データ サービスが作成されてから少なくとも 2 時間待ちます。

次のコマンドを実行して、課金データをエクスポートします。

```console
azdata arc dc export -t usage -p usage.json
```

現時点では、ファイルは暗号化されていないので、内容を確認することができます。 テキスト エディターで自由に開き、どのような内容か確認してください。

`resources` と `data` の 2 つのデータのセットがあることがわかります。 `resources` は、データ コントローラー、PostgreSQL Hyperscale サーバー グループ、および SQL Managed Instance です。 データ内の `resources` レコードには、作成日時、更新日時、削除日時など、リソースの履歴での関連イベントがキャプチャされます。 `data` レコードには、1 時間ごとに特定のインスタンスで使用できたコアの数がキャプチャされます。

`resource` エントリの例:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

`data` エントリの例:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

次のコマンドを実行して、usage.json ファイルを Azure にアップロードします。

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Azure portal で課金データを表示する

Azure portal で課金データを表示するには、次の手順のようにします。

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 画面の上部にある検索ボックスに「**Cost Management**」と入力し、Cost Management サービスをクリックします。
1. **[Cost Management の概要]** で、 **[Cost Management]** タブをクリックします。
1. 左側の **[コスト分析]** タブをクリックします。
1. ビューの上部にある **[リソースごとのコスト]** ボタンをクリックします。
1. スコープが、データ サービス リソースが作成されたサブスクリプションに設定されていることを確認します。
1. ビューの上部近くにある [スコープ] セレクターの横の [ビュー] ドロップダウンで、 **[リソースごとのコスト]** を選択します。
1. 日付フィルターが、 **[今月]** またはデータ サービス リソースを作成したときのタイミングによって意味のある他の時間範囲に設定されていることを確認します。
1. Azure Arc 対応データ サービスの 1 つの種類にフィルターを絞り込む場合は、 **[フィルターの追加]** をクリックし、 **[リソースの種類]**  = `Microsoft.AzureArcData/<data service type>` でフィルターを追加します。
1. 作成されて Azure にアップロードされたすべてのリソースの一覧が表示されます。 課金メーターは $0 であるため、コストは常に $0 であることがわかります。

## <a name="download-billing-data"></a>課金データをダウンロードする

課金概要データを、Azure portal から直接ダウンロードできます。

1. 上の手順に従って表示される **[コスト分析] のリソース種類別ビュー** で、上部の近くにある [ダウンロード] ボタンをクリックします。
1. ダウンロード ファイルの種類 (Excel または CSV) を選択し、 **[データのダウンロード]** ボタンをクリックします。
1. 選択したファイルの種類に応じて、適切なエディターでファイルを開きます。

## <a name="export-billing-data"></a>課金データをエクスポートする

課金エクスポート ジョブを作成することにより、**詳細な** 使用状況と課金データを Azure Storage コンテナーに定期的にエクスポートすることもできます。 これは、請求期間内に特定のインスタンスが課金された時間数など、課金の詳細を確認したい場合に便利です。

課金エクスポート ジョブを設定するには、次の手順のようにします。

1. 左側の **[エクスポート]** をクリックします。
1. **[追加]** をクリックします。
1. 名前とエクスポートの頻度を入力し、[次へ] をクリックします。
1. 新しいストレージ アカウントの作成または既存の使用を選択し、課金データ ファイルのエクスポート先のストレージ アカウント、コンテナー、ディレクトリ パスの指定をフォームに入力して、[次へ] をクリックします。
1. **Create** をクリックしてください。

課金データのエクスポート ファイルは約 4 時間使用でき、課金エクスポート ジョブを作成するときに指定したスケジュールに基づいてエクスポートされます。

エクスポートされた課金データ ファイルを表示するには、次の手順のようにします。

Azure portal で課金データ ファイルを検証できます。 

> [!IMPORTANT]
> 課金エクスポート ジョブを作成した後、4 時間待ってから次の手順に進んでください。

1. ポータルの上部にある検索ボックスに「**ストレージ アカウント**」と入力し、 **[ストレージ アカウント]** をクリックし ます。
3. 上で課金エクスポート ジョブを作成するときに指定したストレージ アカウントをクリックします。
4. 左側の [コンテナー] をクリックします。
5. 上で課金エクスポート ジョブを作成するときに指定したコンテナーをクリックします。
6. 上で課金エクスポート ジョブを作成するときに指定したフォルダーをクリックします。
7. 生成されたフォルダーとファイルにドリルダウンし、生成された .csv ファイルの 1 つをクリックします。
8. **[ダウンロード]** ボタンをクリックすると、ファイルがローカル環境のダウンロード フォルダーに保存されます。
9. Excel などの .csv ファイル ビューアーを使用してファイルを開きます。
10. 結果をフィルター処理して、**リソースの種類** = `Microsoft.AzureArcData/<data service resource type` の行のみを表示します。
11. 現在の 24 時間の期間中にインスタンスが使用された時間数が、UsageQuantity 列に表示されます。
