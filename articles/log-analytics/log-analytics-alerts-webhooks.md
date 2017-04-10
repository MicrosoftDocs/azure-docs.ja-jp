---
title: "OMS Log Analytics の webhook アラート アクション サンプル| Microsoft Docs"
description: "Log Analytics のアラートに対して実行できるアクションの&1; つが *Webhook* であり、1 つの HTTP 要求を使用して外部プロセスを呼び出すことができます。 この記事では、Slack を使用して、Log Analytics のアラートで Webhook アクションを作成する例を見ていきます。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: c8e67f2dcd061e3cd92eec40d6e6bb3de5d73b67
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.lasthandoff: 03/01/2017


---

# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>OMS Log Analytics で アラート webhook アクションを作成して、メッセージを Slack に送信する
[Log Analytics のアラート](log-analytics-alerts.md) に対して実行できるアクションの&1; つが *Webhook*であり、1 つの HTTP 要求を使用して外部プロセスを呼び出すことができます。  アラートと Webhook の詳細については、「 [Log Analytics のアラート](log-analytics-alerts.md)

この記事では、メッセージング サービスである Slack を使用して、Log Analytics のアラートで Webhook アクションを作成する例を見ていきます。

> [!NOTE]
> このサンプルを完了するには、Slack のアカウントが必要です。  [slack.com](http://slack.com)で、無料のアカウントにサインアップすることができます。
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>手順 1. Slack で Webhook を有効にする
1. [slack.com](http://slack.com)で Slack にサインインします。
2. 左側のウィンドウの **[Channels]** (チャネル) セクションで、チャネルを選択します。  これは、メッセージが送信されるチャネルとなります。  "**general**"、"**random**" などの既定のチャネルを選択できます。  実際のシナリオでは、多くの場合、" **criticalservicealerts**" などの特別なチャネルを作成します。 <br>
   
   ![Slack channels](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. **[Add an app or custom integration]** (アプリまたはカスタム統合を追加する) をクリックして、App Directory を開きます。
4. 検索ボックスに「 *webhooks* 」と入力し、 **[Incoming WebHooks]**(受信 Webhook) を選択します。 <br>
   
   ![Slack channels](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. チーム名の横にある **[Install]** (インストール) をクリックします。
6. **[Add Configuration]**(構成の追加) をクリックします。
7. この例で使用するチャネルを選択し、 **[Add Incoming WebHooks integration]**(受信 Webhook 統合の追加) をクリックします。  
8. **Webhook URL**をコピーします。  後でこれをアラートの構成に貼り付けます。 <br>
   
    ![Slack channels](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>手順 2. Log Analytics でアラート ルールを作成する
1. [アラート ルールを作成](log-analytics-alerts.md) します。
   * クエリ: ```    Type=Event EventLevelName=error ```
   * このアラートのチェック間隔: 5 分
   * 結果の数: 10 より大きい
   * 時間枠: 60 分
   * **[Webhook]** には **[Yes (はい)]**、他のアクションには **[No (いいえ)]** を選択
2. **[Webhook URL]** フィールドに Slack の URL を貼り付けます。
3. **[Include custom JSON payload]**(カスタム JSON ペイロードを含める) オプションを選択します。
4. Slack では、" *text*" という名前のパラメーターを持つ JSON 形式のペイロードが想定されます。  これは、作成されたメッセージに表示されるテキストです。  次の例に示すように、 *#* シンボルを使用して&1; つ以上のアラート パラメーターを指定できます。
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![example JSON payload](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. **[Save]** (保存) をクリックして、アラート ルールを保存します。
6. アラートが作成されるまで十分な時間だけ待ってから、Slack で次のようなメッセージが表示されるかどうかを確認します。
   
   ![example webhook in Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Slack 用の高度な Webhook ペイロード
Slack では、受信メッセージを広範囲にカスタマイズできます。 詳細については、Slack の Web サイトで「 [Incoming Webhooks (受信 Webhook)](https://api.slack.com/incoming-webhooks) 」を参照してください。 書式設定を含むリッチなメッセージを作成する、より複雑なペイロードを次に示します。

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


この場合、Slack に次のようなメッセージが生成されます。

![example message in Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>概要
このアラート ルールを設定すると、条件が満たされるたびに Slack にメッセージが送信されます。  

これは、アラートへの応答として作成できるアクションの一例にすぎません。  別の外部サービスを呼び出す Webhook アクション、Azure Automation で Runbook を開始する Runbook アクション、自分または他の受信者に電子メールを送信する電子メール アクションなども作成できます。   

## <a name="next-steps"></a>次のステップ
* [Log Analytics のその他のアラート アクション](log-analytics-alerts-actions.md)について学習します。



