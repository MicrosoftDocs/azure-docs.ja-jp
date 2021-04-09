---
title: Azure App Configuration ストアからキー値を自動的にバックアップする
description: App Configuration ストア間でキー値の自動バックアップを設定する方法について説明します。
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454245"
---
# <a name="back-up-app-configuration-stores-automatically"></a>App Configuration ストアを自動的にバックアップする

この記事では、プライマリ Azure App Configuration ストアからセカンダリ ストアへのキー値の自動バックアップを設定する方法について説明します。 自動バックアップでは、Azure Event Grid と App Configuration の統合が使用されています。 

自動バックアップを設定した後、構成ストアのキー値に対して行われたすべての変更に対して、App Configuration によって Azure Event Grid にイベントが発行されます。 Event Grid では、キー値が作成、更新、または削除されるたびに生成されるイベントをユーザーがサブスクライブできるさまざまな Azure サービスがサポートされています。

## <a name="overview"></a>概要

この記事では、Azure Queue storage を使用して Event Grid からイベントを受信し、Azure Functions のタイマー トリガーを使用して、キュー内のイベントをバッチで処理します。 

関数がトリガーされると、イベントに基づいて、プライマリ App Configuration ストアから変更されたキーの最新の値がフェッチされ、それに応じてセカンダリ ストアが更新されます。 このセットアップにより、1 回のバックアップ操作で短時間に発生する複数の変更を組み合わせて、App Configuration ストアに対する過剰な要求を回避することができます。

![App Configuration ストアのバックアップのアーキテクチャを示す図。](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>リソースのプロビジョニング

App Configuration ストアのバックアップの目的は、さまざまな Azure リージョン間で複数の構成ストアを使用して、アプリケーションの geo 回復性を高めることです。 これを実現するには、プライマリ ストアとセカンダリ ストアを異なる Azure リージョンに配置する必要があります。 このチュートリアルで作成した他のすべてのリソースは、任意のリージョンにプロビジョニングすることができます。 これは、プライマリ リージョンがダウンした場合、そのプライマリ リージョンに再びアクセスできるようになるまで、新たにバックアップするものがないためです。

このチュートリアルでは、セカンダリ ストアを `centralus` リージョンに作成し、他のすべてのリソースを `westus` リージョンに作成します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>前提条件 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) と Azure 開発ワークロード。

- [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- このチュートリアルには、Azure CLI のバージョン 2.3.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create](/cli/azure/group) コマンドを使ってリソース グループを作成します。

次の例では、`westus` の場所に `<resource_group_name>` という名前のリソース グループを作成します。  `<resource_group_name>` を、リソース グループの一意の名前に置き換えます。

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>App Configuration ストアを作成する

プライマリとセカンダリの App Configuration ストアを異なるリージョンに作成します。
`<primary_appconfig_name>` と `<secondary_appconfig_name>` を、構成ストアの一意の名前に置き換えます。 各ストア名は、DNS 名として使用されるため、一意である必要があります。

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>キューを作成する

Event Grid によって発行されたイベントを受信するためのストレージ アカウントとキューを作成します。

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>App Configuration ストア イベントをサブスクライブする

プライマリ App Configuration ストアから、次の 2 つのイベントをサブスクライブします。

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

次のコマンドによって、キューに送信される 2 つのイベントの Event Grid サブスクリプションが作成されます。 エンドポイントの種類は `storagequeue` に設定され、エンドポイントはキュー ID に設定されています。 `<event_subscription_name>` を、イベント サブスクリプションに選択した名前に置き換えます。

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Queue storage からのイベントを処理するための関数を作成する

### <a name="set-up-with-ready-to-use-functions"></a>すぐに使用できる関数を使用して設定する

この記事では、次のプロパティを持つ C# 関数を使用します。
- ランタイム スタック .NET Core 3.1
- Azure Functions ランタイム バージョン 3.x
- 10 分ごとにタイマーによってトリガーされる関数

データのバックアップを簡単に開始できるようにするために、コードに変更を加えることなく使用できる[関数をテストして公開](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup)しました。 プロジェクト ファイルをダウンロードし、[Visual Studio から独自の Azure 関数アプリに発行します。](../azure-functions/functions-develop-vs.md#publish-to-azure)

> [!IMPORTANT]
> ダウンロードしたコードの環境変数には変更を加えないでください。 次のセクションでは、必要なアプリ設定を作成します。
>

### <a name="build-your-own-function"></a>独自の関数を作成する

前に示したサンプル コードが要件を満たさない場合は、独自の関数を作成することもできます。 ご自分の関数で、バックアップを完了するために次のタスクを実行できる必要があります。
- キューの内容を定期的に読み取り、Event Grid からの通知が含まれているかどうかを確認します。 実装の詳細については、[ストレージ キュー SDK](../storage/queues/storage-quickstart-queues-dotnet.md)に関するページを参照してください。
- キューに [Event Grid からのイベント通知](./concept-app-configuration-event.md#event-schema)が含まれている場合は、イベント メッセージから一意の `<key, label>` の情報をすべて抽出します。 キーとラベルの組み合わせは、プライマリ ストアでのキー値の変更に対する一意識別子です。
- プライマリ ストアからすべての設定を読み取ります。 キューに対応するイベントがあるセカンダリ ストアの設定のみを更新します。 キューには存在しているが、プライマリ ストアには存在していないすべての設定を、セカンダリ ストアから削除します。 [App Configuration SDK](https://github.com/Azure/AppConfiguration#sdks) を使用して、構成ストアにプログラムでアクセスできます。
- 処理中に例外が発生しなかった場合は、キューからメッセージを削除します。
- 必要に応じて、エラー処理を実装します。 処理する必要があるいくつかの一般的な例外については、上記のコード サンプルを参照します。

関数の作成の詳細については、「[Azure でタイマーによってトリガーされる関数を作成する](../azure-functions/functions-create-scheduled-function.md)」および「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md)」を参照してください。


> [!IMPORTANT]
> タイマー スケジュールは、プライマリ構成ストアに変更を加える頻度に基づいて適切に判断してください。 関数の実行頻度が高すぎると、ストアの要求がスロットリングされる可能性があります。
>


## <a name="create-function-app-settings"></a>関数アプリの設定を作成する

提供されている Azure Functions を使用している場合は、関数アプリに次のアプリ設定が必要です。
- `PrimaryStoreEndpoint`:プライマリ App Configuration ストアのエンドポイント。 たとえば `https://{primary_appconfig_name}.azconfig.io` です。
- `SecondaryStoreEndpoint`:セカンダリ App Configuration ストアのエンドポイント。 たとえば `https://{secondary_appconfig_name}.azconfig.io` です。
- `StorageQueueUri`:キューの URI。 たとえば `https://{unique_storage_name}.queue.core.windows.net/{queue_name}` です。

次のコマンドを実行すると、関数アプリに必要なアプリ設定が作成されます。 `<function_app_name>` をお使いの関数アプリの名前に置き換えます。

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>関数アプリのマネージド ID へのアクセスを許可する

次のコマンドまたは [Azure portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) を使用して、システムによって割り当てられたマネージド ID を関数アプリに追加します。

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> 必要なリソース作成およびロール管理を実行するために、お使いのアカウントには、適切な範囲 (サブスクリプションまたはリソース グループ) を対象とする `Owner` アクセス許可が必要です。 ロールの割り当てでお困りの場合、[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)方法をご確認ください。

次のコマンドまたは [Azure portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) を使用して、関数アプリのマネージド ID に App Configuration ストアへのアクセス権を付与します。 これらのロールを使用します。
- プライマリ App Configuration ストアに `App Configuration Data Reader` ロールを割り当てます。
- セカンダリ App Configuration ストアに `App Configuration Data Owner` ロールを割り当てます。

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

次のコマンドまたは [Azure portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) を使用して、関数アプリのマネージド ID にキューへのアクセス権を付与します。 キューに `Storage Queue Data Contributor` ロールを割り当てます。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>App Configuration イベントのトリガー

すべてが機能することをテストするには、プライマリ ストアからキー値を作成、更新、または削除します。 この変更は、タイマーによって Azure Functions がトリガーされた 2 秒後に、セカンダリ ストアに自動的に表示されます。

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

イベントをトリガーしました。 しばらくすると、Event Grid によってイベント通知がキューに送信されます。 *次にスケジュールされた関数の実行後*、セカンダリ ストアの構成設定を表示して、プライマリ ストアからの更新されたキー値が含まれているかどうかを確認します。

> [!NOTE]
> スケジュールされたタイマー トリガーを待たずに、テスト中およびトラブルシューティング中に[関数を手動でトリガーする](../azure-functions/functions-manually-run-non-http.md)ことができます。

バックアップ機能が正常に実行されたことを確認したら、キーがセカンダリ ストアに存在することを確認できます。

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>トラブルシューティング

セカンダリ ストアに新しい設定が表示されない場合は、次の処理を行います。

- プライマリ ストアで設定を作成した *後に*、バックアップ関数がトリガーされたことを確認します。
- Event Grid によって時間内にキューにイベント通知が送信されなかった可能性があります。 プライマリ ストアからのイベント通知がキューにまだ含まれているかどうかを確認します。 存在する場合は、バックアップ関数を再びトリガーします。
- エラーまたは警告がないか [Azure Functions のログ](../azure-functions/functions-create-scheduled-function.md#test-the-function)を確認します。
- [Azure portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) を使用して、Azure 関数アプリに、Azure Functions で読み取ろうとしているアプリケーション設定の正しい値が含まれていることを確実にします。
- また、[Azure Application Insights](../azure-functions/functions-monitoring.md?tabs=cmd) を使用して、Azure Functions の監視とアラートを設定することもできます。 


## <a name="clean-up-resources"></a>リソースをクリーンアップする
引き続きこの App Configuration とイベント サブスクリプションを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

これで、キー値の自動バックアップを設定する方法がわかりました。次は、アプリケーションの geo 回復性を向上させる方法について説明します。

- [回復性とディザスター リカバリー](concept-disaster-recovery.md)
