---
title: "Azure Application Insights からのアラートの送信 | Microsoft Docs"
description: "Azure Application Insights を使用して、アプリケーションのエラーに応じてアラートを送信するためのチュートリアルです。"
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce297476cfdf80564c6c0cb835955a146f7a1c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Azure Application Insights でアプリケーションの正常性を監視してアラートを送信する

Azure Application Insights を使用すると、ご利用のアプリケーションを監視して、アプリケーションを使用できない、障害が発生している、またはパフォーマンスに問題がある場合にアラートを送信できます。  このチュートリアルでは、アプリケーションの可用性を継続的にチェックするテストを作成する方法、および検出した問題に応じてさまざまな種類のアラートを送信する方法を説明します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの応答を継続的にチェックする可用性テストを作成する
> * 問題が発生したときに管理者にメールを送信する
> * パフォーマンス メトリックに基づいてアラートを作成する 
> * ロジック アプリを使用して、集計されたテレメトリをスケジュールに従って送信する。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- 次のワークロードを使って、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールします。
    - ASP.NET および Web の開発
    - Azure の開発
    - .NET アプリケーションを Azure にデプロイし、[Application Insights SDK の有効化](app-insights-asp-net.md)を実行します。 


## <a name="log-in-to-azure"></a>Azure へのログイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。

## <a name="create-availability-test"></a>可用性テストを作成する
Application Insights の可用性テストを使用して、世界中のさまざまな場所から、ご利用のアプリケーションを自動的にテストすることができます。   このチュートリアルでは、簡単なテストを実行してそのアプリケーションが使用できるかどうかを確認します。  アプリケーションの操作を詳細にテストするための完全なチュートリアルを作成することもできます。 

1. **[Application Insights]** を選択してから、ご利用のサブスクリプションを選択します。  
1. **[調査]** メニューの **[可用性]** を選択して、**[テストの追加]** をクリックします。
 
    ![可用性テストを追加する](media/app-insights-tutorial-alert/add-test.png)

2. テストの名前を入力し、その他は既定値のままにします。  このテストは、5 つの異なる地域から 5 分ごとに、そのアプリケーションのホーム ページを要求します。 
3. **[アラート]** を選択して **[アラート]** パネルを開きます。このパネルでは、テストが失敗した場合の応答方法の詳細を定義できます。 アラートの条件を満たした場合のメールの送信先となるメール アドレスを入力します。  Webhook のアドレスを入力して、アラートの条件を満たした場合の連絡先を入力することもできます (任意)。

    ![テストを作成する](media/app-insights-tutorial-alert/create-test.png)
 
4. テストのパネルに戻ります。数分経つと、可用性テストの結果が表示され始めます。  テストの名前をクリックして、各場所の詳細を表示します。  テストが成功したことと各テストの期間が、散布図に表示されます。

    ![テストの詳細](media/app-insights-tutorial-alert/test-details.png)

5.  散布図の各点をクリックすると、特定のテストの詳細を掘り下げることができます。  次の例では、失敗した要求の詳細が表示されています。

    ![テスト結果](media/app-insights-tutorial-alert/test-result.png)
  
6. アラートの条件を満たした場合、指定したアドレス宛てに次の例に類似するメールが送信されます。

    ![アラート メール](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>メトリックに基づくアラートを作成する
可用性テストの結果に基づいたアラートのほかに、ご利用のアプリケーションから収集したパフォーマンス メトリックに基づいたアラートを作成できます。

2. **[構成]** メニューの **[アラート]** を選択します。  Azure Alerts のパネルが開きます。  ここには、他のサービス用に構成したアラート ルールが表示される場合があります。
3. **[メトリック アラートの追加]** をクリックします。  新しいアラート ルールを作成するパネルが開きます。

    ![メトリック アラートを追加する](media/app-insights-tutorial-alert/add-metric-alert.png)

4. アラート ルールの **[名前]** を入力して、**[リソース]** ボックスからアプリケーションを選択します。
5. サンプリングする **[メトリック]** を選択します。  過去 24 時間のこの要求の値を示すグラフが表示されます。  このグラフはメトリックの条件を設定する際に役立ちます。

    ![アラート ルールの追加](media/app-insights-tutorial-alert/add-alert-01.png)

6. アラートの **[条件]** と **[しきい値]** を指定します。 これは、アラートを作成する条件となる、メトリックが上回るべき回数です。 
6. **[通知手段]** で **[所有者、共同作成者、閲覧者に電子メールを送信]** ボックスを確認します。アラートの条件が満たされたときにこれらのユーザーにメールを送信したり、追加でメールを受け取る人のメール アドレスをここに追加したりします。  ここで、条件が満たされた場合に実行する Webhook またはロジック アプリを指定することもできます。  これらは検出された問題の脅威を軽減するために使用できます。 

    ![アラート ルールの追加](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>情報を事前に送信する
アラートは特定の問題のセットがアプリケーションで確認されたときに応答する形で作成され、通常は早急に対応が必要な重大な条件でアラートを予約しておきます。  スケジュールに従って自動的に実行されるロジック アプリを使用し、対象のアプリケーションに関する情報を事前に受信できます。  たとえば、さらなる評価が必要な情報をまとめたメールを、管理者に毎日送信したりできます。

Application Insights でロジック アプリを作成する方法の詳細については、「[Logic Apps を使って Application Insights のプロセスを自動化する](automate-with-logic-apps.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
これで問題が生じた場合のアラートの送信方法について学習できました。次のチュートリアルでは、ユーザーがどのようにアプリケーションを使用しているかを分析する方法を説明します。

> [!div class="nextstepaction"]
> [ユーザーを理解する](app-insights-tutorial-users.md)