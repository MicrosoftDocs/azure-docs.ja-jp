---
title: "OMS Log Analytics のアラートの作成 | Microsoft Docs"
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
ms.date: 03/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: eec118430c6262626728c3156634361c977ccb4b
ms.lasthandoff: 03/29/2017


---
# <a name="working-with-alert-rules-in-log-analytics"></a>Log Analytics のアラート ルールの操作
アラートは、ログ検索を一定の間隔で自動的に実行するアラート ルールによって作成されます。  結果が特定の条件に一致する場合に、アラート レコードが作成されます。  さらに、アラートを事前に通知したり、別のプロセスを呼び出したりするために、1 つ以上のアクションを自動的に実行できます。   

この記事では、OMS ポータルを使用してアラート ルールを作成して編集するプロセスについて説明します。  さまざまな設定と必要なロジックの実装方法の詳細については、[Log Analytics のアラートの概要](log-analytics-alerts.md)に関するページを参照してください。

>[!NOTE]
> 現時点では、Azure Portal を使用してアラート ルールを作成または変更することはできません。 

## <a name="create-an-alert-rule"></a>アラート ルールを作成する

OMS ポータルを使用してアラート ルールを作成するには、まず、アラートを呼び出すレコードに対するログ検索を作成します。  それにより、アラート ルールを作成して構成するための **[Alert]** (アラート) ボタンが使用できるようになります。

1. OMS の [Overview] (概要) ページで、 **[Log Search]**(ログ検索) をクリックします。
2. 新しいログ検索クエリを作成するか、保存されているログ検索を選択します。 
3. ページの上部にある **[アラート]** をクリックして、**[アラート ルールの追加]** 画面を開きます。
4. 後の「[アラート ルールの詳細](#details-of-alert-rules)」の情報を使用して、アラート ルールを構成します。
6. **[Save]** (保存) をクリックして、アラート ルールを完成させます。  すぐに実行が開始されます。


## <a name="edit-an-alert-rule"></a>アラート ルールの編集
Log Analytics の **[Settings (設定)]** の **[Alerts (アラート)]** メニューで、すべてのアラート ルールの一覧を表示することができます。  

![Manage alerts](./media/log-analytics-alerts/configure.png)

1. OMS コンソールで、 **[Settings]** (設定) タイルを選択します。
2. **[Alerts]**(アラート) を選択します。

このビューから複数のアクションを実行できます。

* 横にある **[Off]** (オフ) を選択して、ルールを無効にします。
* その横にある鉛筆アイコンをクリックして、アラート ルールを編集します。
* その横にある **[X]** アイコンをクリックして、アラート ルールを削除します。 

## <a name="details-of-alert-rules"></a>アラート ルールの詳細
OMS ポータルでアラート ルールを作成または編集するときは、**[アラート ルールの追加]** または **[アラート ルールの編集]** ページを使用します。  次の表では、この画面のフィールドについて説明します。

![アラート ルール](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Alert information (アラート情報)
これらは、アラート ルールとそれが作成するアラートの基本設定です。

| プロパティ | Description |
|:--- |:---|
| 名前 | アラート ルールを識別する一意の名前。 この名前は、ルールによって作成されるすべてのアラートに含まれます。  |
| Description | アラート ルールに関する追加の説明。 |
| Severity |このルールによって作成されるアラートの重要度。 |

### <a name="search-query-and-time-window"></a>検索クエリと時間枠
アラートを作成する必要があるかどうかを判断するために評価されるレコードを返す検索クエリと時間枠。

| プロパティ | Description |
|:--- |:---|
| Search query (検索クエリ) | これは、実行されるクエリです。  このクエリによって返されるレコードを使用して、アラートを作成するかどうかを決定します。<br><br>**[Use current search query]** (現在の検索クエリを使用) を選択して現在のクエリを使用するか、または既存の保存した検索条件を一覧から選択します。  クエリの構文がテキスト ボックスに表示され、必要に応じて変更できます。 |
| Time window (時間枠) |クエリの時間範囲を指定します。  クエリでは、現在の時刻に先立つ指定の時間範囲の間に作成されたレコードだけを返します。  5 分から 24 時間までの値を指定できます。  アラートの頻度以上の値を指定する必要があります。  <br><br> たとえば、時間枠が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが返されます。 |

アラート ルールの時間枠を指定すると、その時間枠内で検索条件に一致した既存のレコードの数が表示されます。  これにより、期待する数の結果が得られる頻度を判断できます。

### <a name="schedule"></a>スケジュール
検索クエリを実行する頻度を定義します。

| プロパティ | Description |
|:--- |:---|
| Alert frequency (アラートの頻度) | クエリの実行頻度を指定します。 5 分から 24 時間までの値を指定できます。 この値は、時間枠の値以下にする必要があります。  この値が時間枠の値よりも大きい場合、レコードを見落とすおそれがあります。<br><br>たとえば、時間枠が 30 分、頻度が 60 分であるとします。  クエリが午後 1 時に実行された場合、午後 12 時 30 分から午後 1 時までの間のレコードが返されます。  次回クエリが実行されるのは午後 2 時であり、このときには午後 1 時 30 分から午後 2 時までの間のレコードが返されます。  つまり、午後 1 時から午後 1 時 30 分までの間に作成されたレコードは評価されないことになります。 |


### <a name="generate-alert-based-on"></a>基づくアラートの生成
アラート作成の必要があるかどうかを判断ための、検索クエリ結果を評価する基準を定義します。  これらの詳細は、選択したアラート ルールの種類に応じて異なります。  さまざまなアラート ルールの種類の詳細については、「[アラートを使用した Log Analytics の問題への対応](log-analytics-alerts.md)」をご覧ください。

| プロパティ | 説明 |
|:--- |:---|
| Suppress alerts (アラートの抑制) | アラート ルールの抑制を有効にすると、新しいアラートを作成した後、定義された期間の間、ルールのアクションが無効になります。 ルールは引き続き実行され、条件が満たされればアラート レコードが作成されます。 このオプションは、問題を修正している間、同じアクションが繰り返し実行されるのを防ぐために用意されています。 |

#### <a name="number-of-results-alert-rules"></a>結果の数のアラート ルール

| プロパティ | Description |
|:--- |:---|
| 結果の数 |クエリによって返されるレコード数が、指定した値より**大きい**または**小さい**場合に、アラートが作成されます。  |

#### <a name="metric-measurement-alert-rules"></a>メトリック測定のアラート ルール

| プロパティ | Description |
|:--- |:---|
| 集計値 | 結果の各集計値が、違反と見なされるために超える必要のあるしきい値。 |
| 基づくトリガー アラート | アラートが作成されるための違反の数。  結果セット全体での違反の任意の組み合わせについて**違反総数**を指定するか、または、違反が連続したサンプルで発生しなければならない必要な**連続する違反**を指定できます。 |

### <a name="actions"></a>アクション
しきい値に達すると、アラート ルールは常に[アラート レコード](#alert-records)を作成します。  電子メールの送信、Runbook の開始など、実行される 1 つまたは複数の応答を定義することもできます。



#### <a name="email-actions"></a>電子メール アクション
電子メール アクションは、アラートの詳細を記載した電子メールを 1 人以上の受信者に送信します。

| プロパティ | Description |
|:--- |:---|
| 電子メール通知 |アラートがトリガーされたときに電子メールを送信する場合は、 **[Yes]** (はい) を指定します。 |
| [件名] |電子メールの件名。  電子メールの本文を変更することはできません。 |
| Recipients |電子メールのすべての受信者のアドレス。  複数のアドレスを指定する場合は、アドレスをセミコロン (;) で区切ります。 |

#### <a name="webhook-actions"></a>Webhook アクション
Webhook アクションは、1 つの HTTP POST 要求を使用して外部のプロセスを呼び出すことができます。

| プロパティ | Description |
|:--- |:---|
| Webhook |アラートがトリガーされたときに Webhook を呼び出す場合は、 **[Yes]** (はい) を指定します。 |
| Webhook URL |Webhook の URL。 |
| Include custom JSON payload (カスタム JSON ペイロードを含める) |既定のペイロードをカスタム ペイロードに置き換える場合は、このオプションを選択します。 |
| Enter your custom JSON payload (カスタム JSON ペイロードの入力) |Webhook のカスタム ペイロード。  詳細については、前のセクションを参照してください。 |

#### <a name="runbook-actions"></a>Runbook アクション
Runbook アクションは、Azure Automation で Runbook を開始します。 

>[!NOTE]
> このアクションを有効にするには、ワークスペースに Automation ソリューションをインストールする必要があります。 


| プロパティ | Description |
|:--- |:---|
| Runbook | アラートがトリガーされたときに Azure Automation の Runbook を開始する場合は、 **[Yes]** (はい) を指定します。  |
| Automation アカウント | Runbook を選択した Automation アカウントを指定します。  これは、ワークスペースに関連付けられている Action アカウントです。 |
| Select a runbook (Runbook の選択) | アラートの作成時に開始する Runbook を選択します。 |
| Run on (実行先) | Runbook をクラウドで実行する場合は、**[Azure]** を選択します。  **Hybrid Runbook Worker** がインストールされたエージェントで Runbook を実行する場合は、[[ハイブリッド worker]](../automation/automation-hybrid-runbook-worker.md ) を選択します。  |




## <a name="next-steps"></a>次のステップ
* [Alert Management ソリューション](log-analytics-solution-alert-management.md) をインストールして、Log Analytics で作成されたアラートおよび System Center Operations Manager (SCOM) から収集されたアラートを分析します。
* アラートを生成する [ログ検索](log-analytics-log-searches.md) の詳細を確認します。
* アラート ルールに関する [Webhook を構成する](log-analytics-alerts-webhooks.md) チュートリアルを完了します。  
* アラートで識別された問題を修復するために [Azure Automation の Runbook](https://azure.microsoft.com/documentation/services/automation) を作成する方法について学習します。


