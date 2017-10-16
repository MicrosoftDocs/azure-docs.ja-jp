---
title: "Log Analytics のアラートを使用してイベントに応答する | Microsoft Docs"
description: "このチュートリアルでは、OMS リポジトリ内の重要な情報の識別、問題に関する事前の通知、または問題を修正するためのアクションの呼び出しを行うために使用できる Log Analytics のアラートについて理解します。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: magoedte
ms.openlocfilehash: ccf0bd57b7f54b1d8d57b460df9bb381b29ee381
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Log Analytics のアラートを使用してイベントに応答する
Log Analytics のアラートは、Log Analytics リポジトリ内の重要な情報を特定します。  アラートは定期的にログ検索を自動的に実行するアラート ルールによって作成され、ログ検索の結果が特定の条件と一致している場合はアラート レコードが作成され、自動化された応答を実行するように構成できます。  このチュートリアルは、「[Log Analytics データのダッシュボードを作成して共有する](log-analytics-tutorial-dashboards.md)」チュートリアルの続きです。   

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アラート ルールを作成する
> * 電子メール通知を送信するアラート ルールを構成する

このチュートリアルの例を完了するには、[Log Analytics ワークスペースに接続された](log-analytics-quick-collect-azurevm.md)既存の仮想マシンが必要です。  

## <a name="log-in-to-azure-portal"></a>Azure Portal へのログイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。 

## <a name="create-alerts"></a>アラートを作成する

アラートは、ログ検索を一定の間隔で自動的に実行するアラート ルールによって作成されます。  特定のパフォーマンス メトリック、特定のイベントが作成されたとき、イベントが欠如しているとき、または特定の時間枠内に作成されたイベントの数に基づくアラートを作成できます。  たとえば、アラートを使用して、平均 CPU 使用率が特定のしきい値を超えた場合や、特定の Windows サービスまたは Linux デーモンが実行されていないときにイベントが生成された場合に通知を受信することができます。   ログ検索の結果が特定の条件に一致すると、アラート レコードが作成されます。 さらに、アラートを事前に通知したり、別のプロセスを呼び出したりするために、1 つ以上のアクションを自動的に実行できます。 

次の例では、クエリの対象である各コンピューター オブジェクトで、値がしきい値の 90%を超えた場合にアラートを作成するメトリック測定アラート ルールを作成します。

1. Azure ポータルで、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. OMS ポータルを選択して OMS ポータルを起動し、**[概要]** ページで **[ログ検索]** を選択します。  
3. ポータルの上部から **[お気に入り]** を選択し、右側の **[保存された検索]** ウィンドウで "*[Azure VM - プロセッサ使用率]*" クエリを選択します。  
4. ページの上部にある **[アラート]** をクリックして、**[アラート ルールの追加]** 画面を開きます。  
5. 次の情報を指定して、アラート ルールを構成します。  
   a. アラートの**[名前]** を指定します ("*VM 使用率 90 % 超過*" など)。  
   b. **[時間枠]** に、クエリの時間の範囲を指定します ("*[30]*" など)。  クエリでは、現在の時刻に先立つ指定の時間範囲の間に作成されたレコードだけを返します。  
   c. **[アラートの頻度]** には、クエリの実行頻度を指定します。  この例では、"*[5]*" 分を指定します。クエリは、指定した時間枠の間、この間隔で実行されます。  
   d. **[メトリック測定]** を選択し、**[集計値]** として "*[90]*" を入力し、**[アラートをトリガーする基準]**  として "*[3]*" を入力します。  
   e. **[アクション]** の下の [電子メール通知] を無効にします。
6. **[Save]** (保存) をクリックして、アラート ルールを完成させます。 すぐに実行が開始されます。<br><br> ![アラート ルールの例](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Log Analytics のアラート ルールで作成されるアラート レコードは、[Type] が **[Alert]**、[SourceSystem] が **[OMS]** に設定されています。<br><br> ![生成されたアラートのイベントの例](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>アラート アクション
アラートを使用して、高度なアクションを実行できます。たとえば、アラートの条件が満たされたときの応答として、電子メール通知の作成、[Automation Runbook](../automation/automation-runbook-types.md) の起動、Webhook を使用した ITSM インシデント管理システム内へのインシデント レコードの作成、[IT Service Management Connector ソリューション](log-analytics-itsmc-overview.md)での使用などを実行できます。   

電子メール アクションは、アラートの詳細を記載した電子メールを 1 人以上の受信者に送信します。 電子メールの件名は指定できますが、メールの内容は Log Analytics によって構築された標準の形式となります。  先ほど作成したアラート ルールを更新して、ログ検索でアラート レコードをアクティブに監視する代わりに、電子メール通知が送信されるように構成します。     

1. OMS ポータルで、上部メニューの **[設定]** を選択し、**[アラート]** を選択します。
2. アラート ルールの一覧で、先ほど作成したアラートの横にある鉛筆アイコンをクリックします。
3. **[アクション]** セクションの下の [電子メール通知] を有効にします。
4. 電子メールの **[件名]** を指定します ("*プロセッサの使用率がしきい値の 90 % を超過しました*" など)。
5. **[受信者]** フィールドに、電子メールの 1 人以上の受信者のアドレスを追加します。  複数のアドレスを指定する場合は、アドレスをセミコロン (;) で区切ります。
6. **[Save]** (保存) をクリックして、アラート ルールを完成させます。 すぐに実行が開始されます。<br><br> ![電子メール通知を行うアラート ルール](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ログ検索をスケジュールした間隔で実行しているときに、特定の条件に一致した場合は、アラート ルールを使用して、問題を事前に識別して対応する方法を学習しました。  

あらかじめ用意されている Log Analytics のサンプル スクリプトを確認するには、次のリンクをクリックしてください。  

> [!div class="nextstepaction"]
> [Log Analytics のサンプル スクリプト](powershell-samples.md)