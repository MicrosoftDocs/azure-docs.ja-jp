---
title: "Azure サービス アラートの作成 - クロスプラットフォーム CLI | Microsoft Docs"
description: "指定した条件が満たされたときに電子メール、通知、Websites URL (webhook) の呼び出し、またはオートメーションをトリガーします。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 073075d4c789438cc6dd6aa14027cbe50d6efa11


---
# <a name="create-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Azure Monitorでの Azure サービス アラートの作成 - クロスプラットフォーム CLI
> [!div class="op_single_selector"]
> * [ポータル](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>概要
この記事では、クロスプラットフォーム コマンド ライン インターフェイス (CLI) を使用して Azure アラートを設定する方法について説明します。

> [!NOTE]
> Azure Monitor は、2016 年 9 月 25 日まで "Azure Insights" と呼ばれていたサービスの新しい名前です。 ただし、名前空間と、それに伴って以下のコマンドには引き続き "insights" が含まれています。
>
>

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

* **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。    
* **アクティビティ ログ イベント** - アラートは、" *すべて* " のイベントに対して、または特定数のイベントが発生したときにのみトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービスの管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す
* Azure Runbook の実行を開始する (現時点では Azure ポータルからのみ)

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します

* [Azure ポータル](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

コマンドの末尾に -help を追加して入力すると、そのコマンドのヘルプをいつでも確認することができます。 次に例を示します。

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>CLI を使用してアラート ルールを作成する
1. 前提条件を実行し、Azure にログインします。 [Azure Monitor CLI のサンプル](insights-cli-samples.md)をご覧ください。 簡単に言うと、CLI をインストールし、次のコマンドを実行します。 このコマンドにより、ログインし、使用しているサブスクリプションを表示して、Azure Monitor のコマンドを実行する準備をします。

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

1. リソース グループの既存のルールを一覧表示するには、**azure insights alerts rule list** *[オプション] &lt;resourceGroup&gt;* という形式を使用します

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
2. ルールを作成するには、最初に重要な情報をいくつか入手する必要があります。

   * アラートを設定するリソースの **リソース ID**
   * そのリソースが使用できる **メトリック定義**

     リソース ID は、Azure ポータルを使用して取得できます。 リソースが既に作成されている場合は、ポータルでそのリソースを選択し、 次のブレードで "*設定*" セクションの "*プロパティ*" を選択します。 次のブレードに "*リソース ID*" フィールドがあります。 また、[Azure リソース エクスプローラー](https://resources.azure.com/)を使用することもできます。

     Web アプリのリソース ID の例を次に示します

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     この例のリソースで使用できるメトリック、およびそのメトリックの単位の一覧を取得するには、次の CLI コマンドを使用します。  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* は、利用可能な測定の粒度 (1 分間隔) です。 別の粒度を使用すると、さまざまなメトリック オプションが表示されます。
3. メトリック ベースのアラート ルールを作成するには、次の形式のコマンドを使用します。

    **azure insights alerts rule metric set** *[オプション] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    次の例では、Web サイト リソースにアラートを設定します。 アラートは、5 分間を通してすべてのトラフィックを受け取り続けたときにトリガーされ、その後 5 分間トラフィックを受け取らなくなった時点で再度トリガーされます。

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
4. アラートが発生したときに webhook を作成するか、電子メールを送信するには、最初に電子メールと webhook、またはそのいずれかを作成します。 その後すぐに、ルールを作成します。 CLI を使用して、webhook または電子メールを、作成済みのルールと関連付けることはできません。

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```
5. アクティビティ ログで特定の状況に発生するアラートを作成するには、次の形式を使用します。

    **insights alerts rule log set** *[オプション] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;operationName&gt;*

    たとえば、次のように入力します。

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    operationName は、アクティビティ ログ エントリのイベントの種類に対応します。 *Microsoft.Compute/virtualMachines/delete*、*microsoft.insights/diagnosticSettings/write* などが例として挙げられます。

    PowerShell コマンド [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) を使用すると、考えられる operationNames の一覧を取得できます。 また、Azure ポータルを使用して、アクティビティ ログを照会し、アラートを作成する過去の操作を検索することもできます。 操作は、フレンドリ名のグラフィック ログに表示されます。 JSON でエントリを検索し、OperationName 値を取得します。   
6. アラートが正しく作成されていることを確かめるには、個別のルールを確認します。

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. ルールを削除するには、次の形式のコマンドを使用します。

    **insights alerts rule delete** [オプション] &lt;resourceGroup&gt; &lt;ruleName&gt;

    この記事で作成したルールは、次のコマンドで削除します。

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>次のステップ
* [Azure での監視の概要](monitoring-overview.md) 情報を入手します。
* [アラートでの webhook の構成](insights-webhooks-alerts.md)に関する詳細情報を確認します。
* [Azure Automation Runbooks](../automation/automation-starting-a-runbook.md)の詳細情報を確認します。
* [診断ログ収集の概要](monitoring-overview-of-diagnostic-logs.md) 情報を入手して、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。



<!--HONumber=Jan17_HO5-->


