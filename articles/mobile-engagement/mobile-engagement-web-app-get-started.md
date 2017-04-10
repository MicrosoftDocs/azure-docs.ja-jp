---
title: "Web Apps 用 Azure Mobile Engagement の使用 | Microsoft Docs"
description: "Web Apps の分析とプッシュ通知で Azure Mobile Engagement を使用する方法について説明します。"
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 555342e88c912a3f43c578a40dc34933996ade4c
ms.openlocfilehash: ef75623a0b8c2a4d38f527a26cbc2cf070765302


---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Web Apps 用 Azure Mobile Engagement の使用
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用して Web アプリの使用状況を把握する方法について説明します。

このチュートリアルには、次のものが必要です。

* Visual Studio 2015 またはその他の任意のエディター
* [Web SDK](http://aka.ms/P7b453) 

この Web SDK はプレビュー段階です。現時点でのサポート対象は分析のみで、ブラウザーまたはアプリ内のプッシュ通知の送信にはまだ対応していません。 

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started)を参照してください。
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>Web アプリ用に Mobile Engagement を設定する
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnecting-appaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します
このチュートリアルでは、データを収集するために必要な最小限のセットである "基本的な統合" について説明します。

統合方法を示すために Visual Studio で基本的な Web アプリを作成しますが、Visual Studio 以外で作成された Web アプリケーションを使用して手順を進めることもできます。 

### <a name="create-a-new-web-app"></a>新しい Web アプリの作成
次の手順では、以前のバージョンの Visual Studio と類似した手順で、Visual Studio 2015 を使用します。 

1. Visual Studio を起動し、**ホーム**画面で **[新しいプロジェクト]** を選択します。
2. ポップアップ ウィンドウで、**[Web]** -> **[ASP.Net Web アプリケーション]** の順に選択します。 アプリの **[名前]**、**[場所]**、**[ソリューション名]** を入力し、**[OK]** をクリックします。
3. **[テンプレートを選択する]** ポップアップで、**[ASP.Net 4.5 Templates]** の下にある **[Empty]** を選択し、**[OK]** をクリックします。 

これで、Azure Mobile Engagement Web SDK を統合する新しい空の Web アプリ プロジェクトが作成されました。

### <a name="connect-your-app-to-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続する
1. ソリューションに **javascript** という名前の新しいフォルダーを作成し、そのフォルダーに Web SDK JS ファイルの **azure-engagement.js** を追加します。 
2. この javascript フォルダーに、次のコードを含む **main.js** という名前の新しいファイルを追加します。 接続文字列を忘れずに更新してください。 この `azureEngagement` オブジェクトは Web SDK メソッドへのアクセスに使用されます。 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Visual Studio with js files][1]

## <a name="enable-real-time-monitoring"></a>リアルタイム監視を有効にする
データの送信を開始してユーザーがアクティブであることを確認するには、少なくとも 1 つのアクティビティを Mobile Engagement のバックエンドに送信する必要があります。 Web アプリにおけるアクティビティとは Web ページです。 

1. ソリューションに **home.html** という名前の新しいページを作成し、それを Web アプリの開始ページとして設定します。 
2. 次のコードを body タグ内に追加し、前の手順で追加した 2 つの javascript をこのページに含めます。 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. EngagementAgent の `startActivity` メソッドを呼び出すように body タグを更新します。
   
        <body onload="engagement.agent.startActivity('Home')">
4. **home.html** は次にようになります。
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>分析を拡張する
現在 Web SDK で提供されており、分析に使用できるすべてのメソッドを以下に示します。

1. アクティビティ/Web ページ:
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. イベント
   
        engagement.agent.sendEvent(name, extras);
3. エラー
   
        engagement.agent.sendError(name, extras);
4. ジョブ
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png




<!--HONumber=Nov16_HO2-->


