---
title: "OMS Log Analytics のアラートへの応答 | Microsoft Docs"
description: "Log Analytics のアラートは、OMS リポジトリ内の重要な情報を識別し、問題について事前に通知したり、問題を修正するためのアクションを呼び出したりできます。  この記事では、アラート ルールを作成する方法と、実行できるさまざまなアクションの詳細について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fdf22ff85a3a76be5de50632c4948df44c2312df
ms.openlocfilehash: b8731e1fe48b7d809b113eb5273e3962542b8f34
ms.lasthandoff: 03/01/2017


---

# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Log Analytics のアラート ルールへのアクションの追加
[Log Analytics でアラートを作成する](log-analytics-alerts.md)際に、1 つまたは複数の操作を実行[アラート ルールを構成する](log-analytics-alerts.md)ことができます。  この記事では、使用できるさまざまなアクションと、それぞれの構成に関する詳細を示します。

| アクション | Description |
|:--|:--|
| [電子メール](#email-actions) |    アラートの詳細を記載した電子メールを&1; 人以上の受信者に送信します。 |
| [webhook](#webhook-actions) | 1 つの HTTP POST 要求を使用して外部プロセスを呼び出します。 |
| [Runbook](#runbook-actions) | Azure Automation で Runbook を開始します。 |


## <a name="email-actions"></a>電子メール アクション
電子メール アクションは、アラートの詳細を記載した電子メールを&1; 人以上の受信者に送信します。  電子メールの件名は指定できますが、メールの内容は Log Analytics によって構築された標準の形式となります。  電子メールには、アラートの名前などの概要情報に加えて、ログ検索で返される最大&10; 個のレコードの詳細情報が含まれます。  また、そのクエリに基づくレコードのセット全体を返す Log Analytics のログ検索へのリンクも含まれています。   メールの送信者は、"*Microsoft Operations Management Suite チーム &lt;noreply@oms.microsoft.com&gt;*" となります。 

電子メール アクションには、次の表に示すプロパティが必要です。

| プロパティ | 説明 |
|:--- |:--- |
| [件名] |電子メールの件名。  電子メールの本文を変更することはできません。 |
| Recipients |電子メールのすべての受信者のアドレス。  複数のアドレスを指定する場合は、アドレスをセミコロン (;) で区切ります。 |


## <a name="webhook-actions"></a>Webhook アクション

Webhook アクションは、1 つの HTTP POST 要求を使用して外部のプロセスを呼び出すことができます。  呼び出されるサービスは、Webhook をサポートし、受信したペイロードの使用方法を決定できる必要があります。  また、要求に API で認識される形式を使用すれば、Webhook を明示的にはサポートしない REST API も呼び出すことができます。  アラートに対する応答で webhook を使用する例では、[Slack](http://slack.com) でメッセージを送信したり、[PagerDuty](http://pagerduty.com/) でインシデントを作成しています。  Webhook で Slack を呼び出すアラート ルールを作成する詳細なチュートリアルについては、[Log Analytics のアラートでの Webhook](log-analytics-alerts-webhooks.md)に関する記事を参照してください。

webhook アクションには、次の表に示すプロパティが必要です。

| プロパティ | Description |
|:--- |:--- |
| Webhook URL |Webhook の URL。 |
| Custom JSON payload (カスタム JSON ペイロード) |Webhook と共に送信するカスタム ペイロードです。  詳細については、以下をご覧ください。 |


Webhook には、URL と共に、外部のサービスに送信されるデータである JSON 形式のペイロードが含まれます。  既定では、ペイロードには次の表に示す値が格納されます。  このペイロードは、独自のカスタム ペイロードに置き換えることができます。  その場合は、各パラメーターに対して表に示される変数を使用して、カスタム ペイロードにそれらの値を含めることができます。

| パラメーター | 変数 | 説明 |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |アラート ルールの名前。 |
| AlertThresholdOperator |#thresholdoperator |アラート ルールのしきい値演算子。  "*Greater than*" または "*Less than*" を使用できます。 |
| AlertThresholdValue |#thresholdvalue |アラート ルールのしきい値。 |
| LinkToSearchResults |#LinkToSearchResults |アラートを作成したクエリに基づいてレコードを返す Log Analytics ログ検索へのリンク。 |
| ResultCount |#searchresultcount |検索結果に含まれるレコードの数。 |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |UTC 形式で記述したクエリの終了時刻。 |
| SearchIntervalInSeconds |#searchinterval |アラート ルールの時間枠。 |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |UTC 形式で記述したクエリの開始時刻。 |
| SearchQuery |#SearchQuery |アラート ルールで使用されるログ検索クエリ。 |
| SearchResults |以下を参照 |クエリによって返される JSON 形式のレコード。  最初の 5,000 レコードに制限されます。 |
| WorkspaceID |#WorkspaceID |OMS ワークスペースの ID。 |

たとえば、 *text*という名前の&1; つのパラメーターを含む次のカスタム ペイロードを指定できます。  この Webhook で呼び出すサービスでは、このパラメーターが想定されます。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

この例のペイロードは、Webhook に送信されると、次のような内容に解決されます。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

カスタム ペイロードに検索結果を含めるには、json ペイロードの最上位レベルのプロパティとして、次の行を追加します。  

    "IncludeSearchResults":true

たとえば、アラート名と検索結果だけを含むカスタム ペイロードを作成するには、次のように入力します。 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Webhook で外部サービスを開始するアラート ルールを作成する例の詳細については、[OMS Log Analytics で アラート webhook アクションを作成して、メッセージを Slack に送信する](log-analytics-alerts-webhooks.md)方法に関する記事を参照してください。

## <a name="runbook-actions"></a>Runbook アクション
Runbook アクションは、Azure Automation で Runbook を開始します。  この種類のアクションを使用するためには、OMS ワークスペースに [Automation ソリューション](log-analytics-add-solutions.md) がインストールされ、構成されている必要があります。  Automation ソリューションで構成されているオートメーション アカウントの Runbook から選択できます。

Runbook アクションには、次の表に示すプロパティが必要です。

| プロパティ | Description |
|:--- |:---|
| Runbook | アラートの作成時に開始する Runbook。 |
| Run on (実行先) | Runbook をクラウドで実行する場合は、**[Azure]** を指定します。  **Hybrid Runbook Worker** がインストールされたエージェントで Runbook を実行する場合は、[[ハイブリッド worker]](../automation/automation-hybrid-runbook-worker.md ) を指定します。  |

Runbook アクションは、 [Webhook](../automation/automation-webhooks.md)を使用して Runbook を開始します。  アラート ルールを作成すると、Runbook に対して、" **OMS Alert Remediation** " の後に GUID が付いた名前を持つ新しい Webhook が自動的に作成されます。  

Runbook のパラメーターを直接設定することはできませんが、[$WebhookData パラメーター](../automation/automation-webhooks.md)にアラートの詳細 (それを作成したログ検索の結果を含む) が格納されます。  この Runbook で、アラートのプロパティにアクセスするためのパラメーターとして **$WebhookData** を定義する必要があります。  アラート データは、**$WebhookData** の **RequestBody** プロパティにある **SearchResults** という単一のプロパティから JSON 形式で取得できます。  このデータには、次の表に示したプロパティが存在します。

| ノード | 説明 |
|:--- |:--- |
| id |検索のパスと GUID。 |
| __metadata |アラートに関する情報 (レコードの件数、検索結果の状態を含む)。 |
| 値 |検索結果のレコードごとのエントリ。  エントリの詳細は、レコードのプロパティおよび値と対応します。 |

たとえば、以下の Runbook では、ログの検索から返されたレコードを抽出し、レコードの種類ごとに異なるプロパティを割り当てています。  Runbook ではまず、JSON 形式の **RequestBody** を PowerShell からオブジェクトとして扱うことができるように変換していることに注目してください。

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="next-steps"></a>次のステップ
- アラート ルールに関する [Webhook を構成する](log-analytics-alerts-webhooks.md) チュートリアルを完了します。  
- アラートで識別された問題を修復するために [Azure Automation の Runbook](https://azure.microsoft.com/documentation/services/automation) を作成する方法について学習します。
