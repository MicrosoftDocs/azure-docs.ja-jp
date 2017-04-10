---
title: "Azure Monitor の CLI クイック スタート サンプル | Microsoft Docs"
description: "Azure Monitor の機能用のサンプル CLI コマンドです。 Azure Monitor は、アラート通知の送信、構成済みのテレメトリ データの値に基づく Web URL の呼び出しに加え、Cloud Services、Virtual Machines、Web Apps の自動スケールを行うことができる Microsoft Azure サービスです。"
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 0e629dac553f576f2dd3059453b00d6b10e48fd7
ms.lasthandoff: 03/21/2017


---
# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Azure Monitor クロスプラットフォーム CLI のクイック スタート サンプル
この記事では、Azure Monitor の機能にアクセスするために役立つコマンド ライン インターフェイス (CLI) のサンプル コマンドを紹介します。 Azure Monitor では、Cloud Services、Virtual Machines、Web Apps を自動スケールできます。また、アラート通知の送信や、構成済みのテレメトリ データの値に基づく Web URL の呼び出しも行うことができます。

> [!NOTE]
> Azure Monitor は、2016 年 9 月 25 日まで "Azure Insights" と呼ばれていたサービスの新しい名前です。 ただし、名前空間と、それに伴って以下のコマンドには引き続き "insights" が含まれています。
> 
> 

## <a name="prerequisites"></a>前提条件
Azure CLI をまだインストールしていない場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md)」を参照してください。 Azure CLI を使い慣れていない場合、その詳細について、「 [Azure Resource Manager での、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)」を確認してください。

Windows で、 [Node.js Web サイト](https://nodejs.org/)から npm をインストールします。 インストールが完了したら、管理者特権で CMD.exe を使用し、npm のインストール フォルダーから次を実行します。

```console
npm install azure-cli --global
```

次に、任意のフォルダー/場所に移動し、次のコマンド ラインを入力します。

```console
azure help
```

## <a name="log-in-to-azure"></a>Azure へのログイン
まず、Azure アカウントにログインします。

```console
azure login
```

このコマンドを実行した後、画面の指示に従ってサインインする必要があります。 サインインすると、アカウント、テナント ID、既定のサブスクリプション ID が表示されます。 すべてのコマンドは、既定のサブスクリプションのコンテキストで動作します。

現在のサブスクリプションの詳細を一覧表示するには、次のコマンドを使用します。

```console
azure account show
```

作業コンテキストを別のサブスクリプションに変更するには、次のコマンドを使用します。

```console
azure account set "subscription ID or subscription name"
```

Azure Resource Manager と Azure Monitor コマンドを使用するには、Azure Resource Manager モードにする必要があります。

```console
azure config mode arm
```

サポートされているすべての Azure Monitor コマンドの一覧を表示するには、次のコマンドを実行します。

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>サブスクリプションのアクティビティ ログの表示
監査ログ イベントの一覧を表示するには、次の手順に従います。

```console
azure insights logs list [options]
```

次のコマンドを実行すると、利用可能なオプションがすべて表示されます。

```console
azure insights logs list -help
```

resourceGroup を指定してログの一覧を表示する例を次に示します。

```console
azure insights logs list --resourceGroup "myrg1"
```

caller を指定してログの一覧を表示する例

```console
azure insights logs list --caller "myname@company.com"
```

caller を指定して、開始日から終了日までの期間のあるリソースの種類のログの一覧を表示する例

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>アラートの操作
このセクションの情報を使用すると、アラートを操作できます。

### <a name="get-alert-rules-in-a-resource-group"></a>リソース グループのアラート ルールの取得
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>メトリックのアラート ルールの作成
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>ログのアラート ルールの作成
```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Web テストのアラート ルールの作成
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>アラート ルールの削除
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>ログ プロファイル
このセクションの情報を使用すると、ログ プロファイルを操作できます。

### <a name="get-a-log-profile"></a>ログ プロファイルの取得
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>保有期間を指定しないログ プロファイルの追加
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>ログ プロファイルの削除
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>保有期間を指定したログ プロファイルの追加
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>保有期間とイベント ハブを指定したログ プロファイルの追加
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>診断
このセクションの情報を使用すると、診断設定を操作できます。

### <a name="get-a-diagnostic-setting"></a>診断設定の取得
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>診断設定の無効化
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>保有期間を指定しない診断設定の有効化
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
このセクションの情報を使用すると、自動スケール設定を操作できます。 これらの例を変更する必要があります。

### <a name="get-autoscale-settings-for-a-resource-group"></a>リソース グループの自動スケール設定の取得
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>名前によるリソース グループの自動スケール設定の取得
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>自動スケール設定の設定
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

