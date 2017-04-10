---
title: "Log Analytics アラートから Azure Automation Runbook を呼び出す | Microsoft Docs"
description: "この記事では、Microsoft OMS Log Analytics アラートから Automation Runbook を呼び出す方法の概要を示します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 81cf490eae7f283c0180875cb3a2ed2ffe6333c8
ms.lasthandoff: 03/29/2017

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>OMS Log Analytics アラートから Azure Automation Runbook を呼び出す

結果が特定の条件 (プロセッサ使用率の長期的なスパイクなど) に一致した場合やビジネス アプリケーションの機能にとって重要な特定のアプリケーション プロセスが失敗し、対応するイベントを Windows イベント ログに書き込んだ場合にアラート レコードを作成するように Log Analytics でアラートを構成すると、そのアラートは、問題を自動修復しようとして Automation Runbook を自動的に実行できます。  

アラートを構成するときに Runbook を呼び出すオプションは 2 つあります。  具体的には次のとおりです。

1. webhook を使用する。
   * これは、OMS ワークスペースが Automation アカウントにリンクされていない場合に使用できる唯一のオプションです。
   * Automation アカウントを既に OMS ワークスペースにリンクしている場合も引き続きこのオプションを使用できます。  

2. 直接 Runbook を選択する。
   * このオプションは、OMS ワークスペースが Automation アカウントにリンクされている場合のみ使用できます。  

## <a name="calling-a-runbook-using-a-webhook"></a>webhook を使用して Runbook を呼び出す

webhook を使用することにより、単一の HTTP 要求を通して Azure Automation で特定の Runbook を開始することができます。  アラート アクションとして webhook を使用して Runbook を呼び出すように [Log Analytics アラート](../log-analytics/log-analytics-alerts.md#alert-rules)を構成する前に、まず、この方法を使用して呼び出される Runbook 用に webhook を作成する必要があります。  [webhook の作成](automation-webhooks.md#creating-a-webhook)に関する記事の手順を確認して従い、アラート ルールの構成時に参照できるように webhook URL を忘れずに記録しておいてください。   

## <a name="calling-a-runbook-directly"></a>直接 Runbook を呼び出す

OMS ワークスペースで Automation & Control サービスをインストールして構成してある場合は、アラートの Runbook アクションのオプションを構成するときに、**[Runbook の選択]** ボックスの一覧ですべての Runbook を表示し、そのアラートに対応して実行する特定の Runbook を選択できます。  選択した Runbook は、Azure クラウド内のワークスペース、または Hybrid Runbook Worker で実行できます。  Runbook オプションを使用してアラートを作成すると、その Runbook 用に webhook が作成されます。  その webhook は、Automation アカウントに移動し、選択した Runbook の webhook ブレードに移動すると表示されます。  アラートを削除しても webhook は削除されませんが、ユーザーは手動で webhook を削除できます。  webhook が削除されない場合、これは問題ではありません。単に、構成された Automation アカウントを管理するために最終的に削除する必要がある孤立した項目にすぎません。  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Runbook の特性 (両方のオプションに該当)

Log Analytics アラートから Runbook を呼び出す 2 つの方法には、アラート ルールを構成する前に理解しておく必要があるさまざまな動作の特性があります。  

* **Object** 型の **WebhookData** という Runbook 入力パラメーターが必要です。  これには必須と任意があります。  アラートは、この入力パラメーターを使用して検索結果を Runbook に渡します。

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )

*  WebhookData を PowerShell オブジェクトに変換するためのコードが必要です。

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* はオブジェクトの配列になります。各オブジェクトには、1 つの検索結果からの値を含むフィールドが含まれています。

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>webhook オプションと Runbook オプションの間の WebhookData の不一致

* webhook を呼び出すようにアラートを構成する際は、Runbook 用に作成した webhook URL を入力し、**[Test Webhook (webhook のテスト)]** をクリックします。  結果として Runbook に送信された WebhookData には、*.SearchResult* または *.SearchResults* が含まれていません。

*  そのアラートを保存した場合は、アラートがトリガーされ、webhook を呼び出すと、Runbook に送信された WebhookData には *.SearchResult* が含まれます。
* アラートを作成し、(webhook も作成する) Runbook を呼び出すようにそのアラートを構成した場合は、アラートがトリガーされると、*.SearchResults* を含む Runbook に WebhookData が送信されます。

その結果、上記のコード例では、アラートが webhook を呼び出した場合は *.SearchResult* を取得し、アラートが直接 Runbook を呼び出した場合は *.SearchResults* を取得する必要があります。

## <a name="example-walkthrough"></a>例のチュートリアル

ここでは、次のグラフィカルな Runbook の例を使用してこのしくみを説明します。この Runbook は Windows サービスを開始します。<br><br> ![Windows サービスのグラフィカルな Runbook の開始](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Runbook には、**WebhookData** という **Object** 型の入力パラメーターが 1 つあり、*.SearchResults* を含むアラートから渡された webhook データが含まれています。<br><br> ![Runbook の入力パラメーター](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

この例では、Log Analytics で、2 つのカスタム フィールド (*SvcDisplayName_CF* と *SvcState_CF*) を作成し、システム イベント ログに書き込まれたイベントからサービスの表示名と状態 (つまり、実行中や停止済み) を抽出します。  その後、Windows システムで印刷スプーラー サービスが停止したときに検出できるように `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 検索クエリを含むアラート ルールを作成します。  これは関心のあるサービスならどれでもかまいませんが、この例では、Windows OS に付属している既存のサービスの 1 つを参照しています。  アラート アクションは、この例で使用されている Runbook を実行し、Hybrid Runbook Worker で動作するように構成されています。これは、ターゲット システムで有効になっています。   

Runbook コード アクティビティ **Get Service Name from LA** は、JSON 形式の文字列をオブジェクトの種類に変換し、項目 *SvcDisplayName_CF* をフィルター処理して Windows サービスの表示名を抽出し、これを次のアクティビティに渡します。次のアクティビティは、このサービスが停止していることを確認してから、サービスの再開を試みます。  *SvcDisplayName_CF* は、この例を説明するために Log Analytics に作成された[カスタム フィールド](../log-analytics/log-analytics-custom-fields.md)です。

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

サービスが停止すると、Log Analytics のアラート ルールは、一致を検出して Runbook をトリガーし、アラートのコンテキストを Runbook に送信します。 Runbook は、サービスが停止していることを確認するためのアクションを実行します。停止している場合は、サービスを再開し、正しく開始されたことを確認して、結果を出力します。     

また、Automation アカウントを OMS ワークスペースにリンクしていない場合は、Runbook をトリガーする webhook アクションでアラート ルールを構成し、前に説明したガイダンスに従って JSON 形式の文字列を変換して *.SearchResult* をフィルター処理するよう Runbook を構成します。    

## <a name="next-steps"></a>次のステップ

* Log Analytics のアラートの詳細とアラートの作成方法については、「[Log Analytics のアラート](../log-analytics/log-analytics-alerts.md)」を参照してください。

* webhook を使用して Runbook をトリガーする方法を理解するには、「[Azure Automation Webhook](automation-webhooks.md)」を参照してください。

