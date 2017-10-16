---
title: "Unity iOS デプロイでの Azure Mobile Engagement の使用"
description: "iOS デバイスにデプロイする Unity アプリの分析やプッシュ通知で Azure Mobile Engagement を使用する方法について説明します。"
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Unity iOS デプロイでの Azure Mobile Engagement の使用
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握する方法と、iOS デバイスへのデプロイ時に Unity アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、出発点として、従来の Unity Roll a Ball チュートリアルを使用します。 以下のチュートリアルで紹介する Mobile Engagement の統合に進む前に、この [チュートリアル](mobile-engagement-unity-roll-a-ball.md) の手順を実行しておく必要があります。 

このチュートリアルには、次のものが必要です。

* [Unity エディター](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* XCode エディター

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started)」をご覧ください。
> 
> 

## <a id="setup-azme"></a>iOS アプリ用に Mobile Engagement を設定する
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します
### <a name="import-the-unity-package"></a>Unity パッケージのインポート
1. [Mobile Engagement Unity パッケージ](https://aka.ms/azmeunitysdk) をダウンロードし、ローカル コンピューターに保存します。 
2. **[Assets (アセット)]、[Import Package (パッケージのインポート)]、[Custom Package (カスタム パッケージ)]** の順に移動し、前の手順でダウンロードしたパッケージを選択します。 
   
    ![][70] 
3. すべてのファイルを選択し、 **[Import]** (インポート) ボタンをクリックします。 
   
    ![][71] 
4. インポートが成功すると、プロジェクトにインポートされた SDK ファイルが表示されます。  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>EngagementConfiguration の更新
1. SDK フォルダーの **EngagementConfiguration** スクリプト ファイルを開き、先ほど Azure Portal で取得した接続文字列を使って **IOS\_CONNECTION\_STRING** を更新します。  
   
    ![][73]
2. ファイルを保存します。 

### <a name="configure-the-app-for-basic-tracking"></a>基本的な追跡を行うためのアプリの構成
1. Player オブジェクトに関連付けられた **PlayerController** スクリプトを編集するために開きます。 
2. 次の using ステートメントを追加します。
   
        using Microsoft.Azure.Engagement.Unity;
3. 次の行を `Start()` メソッドに追加します。
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>アプリのデプロイと実行
1. iOS デバイスをコンピューターに接続します。 
2. **[File (ファイル)]、[Build Settings (設定をビルド)]** の順に開きます。 
   
    ![][40]
3. **[iOS]** を選択し、**[Switch Platform (プラットフォームを切り替える)]** をクリックします。
   
    ![][41]
   
    ![][42]
4. **[Player settings]** (プレイヤーの設定) をクリックし、有効なバンドル識別子を入力します。 
   
    ![][53]
5. 最後に、 **[Build And Run]**
   
    ![][54]
6. iOS パッケージを格納するフォルダー名の入力を求められる場合があります。 
   
    ![][43]
7. すべての手順が正常に行われると、プロジェクトがコンパイルされ、XCode アプリケーション上で開かれます。 
8. プロジェクトで **バンドル識別子** が正しいことを確認してください。  
   
    ![][75]
9. XCode でアプリを実行すると、接続されているデバイスにパッケージがデプロイされ、スマートフォンに Unity ゲームが表示されます。 

## <a id="monitor"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする
Mobile Engagement により、ユーザーと通信を行い、キャンペーンに関するプッシュ通知とアプリ内メッセージングを届けることができます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
通知を受け取るためにさらにアプリの構成を行う必要はありません。設定は既に完了しています。

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
