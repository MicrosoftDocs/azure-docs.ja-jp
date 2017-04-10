---
title: "Azure Mobile Engagement デモ アプリ | Microsoft Docs"
description: "Azure Mobile Engagement デモ アプリのダウンロード場所、使用方法、および使用した場合の利点について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3


---
# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile Engagement デモ アプリ
有用なリソースを検索し、Azure Mobile Engagement について理解を深めることができるように、**iOS**、**Android**、**Windows** の各プラットフォーム用の Azure Mobile Engagement デモ アプリを発行しました。

アプリは次の用途に役立ちます。

* ビデオ、ドキュメント、サポート フォーラム、機能に関する要望の投稿先など、Mobile Engagement のリソースへの便利なリンクを簡単に検索する。
* Mobile Engagement でサポートされているサンプルの通知を経験し、独自のモバイル アプリケーションの発想を得る。
* 参照実装を使用して、独自のアプリに Mobile Engagement を実装する方法を習得する。 次のことを習得できます。
  
  * 分析データを収集する。
  * "*全画面スポット ビュー*" や "*ポップアップ*" などの種類の高度な通知シナリオを実装する。
  * アンケートや投票を実装する。
  * サイレント プッシュ データとプッシュ シナリオを実装する。   

## <a name="app-installation"></a>アプリのインストール
このアプリは、次のアプリ ストアで入手できます。

* **Windows ユニバーサル デモ アプリの場合**:
  
  * [Windows ストア](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2)でアプリをダウンロードします。
  * アプリは、Windows 10 ユニバーサル アプリとして開発されました。 ソース コードは [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows)から入手できます。
* **iOS デモ アプリの場合:**
  
  * [Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090)でアプリをダウンロードします。
  * このアプリは、iOS Swift で開発されました。 ソース コードは [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios)から入手できます。
* **Android デモ アプリの場合:**
  
  * [Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)でアプリをダウンロードします。
  * ソース コードは [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android)から入手できます。

![Windows ユニバーサル デモ アプリの場合][1]

![iOS デモ アプリの場合][2]
![Android デモ アプリの場合][3]

## <a name="usage"></a>使用法
このアプリは、次の方法で使用できます。

**(前述の) アプリケーション ストアのリンクからデバイスにアプリをダウンロードします。**

> [!IMPORTANT]
> Azure アカウントは不要です。またバックエンドにアプリを接続する必要もありません。 アプリは独立して動作します。
> 
> 

* デバイスにアプリを実装すると、左側のメニューにあるリンクにアクセスして、Mobile Engagement に関する有用なリソースを検索できます。
* 製品の最新の更新プログラムについては常に最新の状態にしておくために、このアプリケーションに [サービスの RSS フィード](https://aka.ms/azmerssfeed) が追加されました。
* また、サンプルの通知シナリオに進み、プラットフォームごとに Mobile Engagement でサポートされている種類の通知を発生させることもできます。 これらの通知はローカルで発生させることができます。つまり、画面上のボタンをクリックすると、Mobile Engagement プラットフォームから通知を送信する場合と同じ通知を表示することができます。

![App menu for Windows][4]

![iOS のアプリ メニュー][5]
![Android のアプリ メニュー][6]

**(前述の) Github のリンクからソース コードをダウンロードします。**

* ソース コードのダウンロードが完了したら、コードをそれぞれの開発環境 (iOS の場合は XCode、Android の場合は Android Studio、Windows の場合は Visual Studio) で開きます。
* 次に、 [基本的な SDK の統合手順](mobile-engagement-windows-store-dotnet-get-started.md) に従って、独自の Mobile Engagement バックエンド インスタンスにこのアプリを接続できるようにする必要があります。
  * アプリで接続文字列を構成する必要があります。
  * アプリのプッシュ通知プラットフォームも構成する必要があります。
* アプリ自体が Mobile Engagement を使用してインストルメント化されていることに注意してください。 したがって、アプリをバックエンドに接続した後に開くと、 **[Monitor]** (モニター) タブに、ユーザー セッション、アクティビティ、イベントなどを表示できるようになります。
* ローカル通知を使用する代わりに、独自の Mobile Engagement インスタンスからこのアプリに通知を送信することもできます。
  
  * ここでは、アプリの **[Get the Device ID]** (デバイス ID の取得) メニュー項目を使用して、お使いのデバイスをテスト デバイスとして追加できます。 これにより、後でテスト デバイスとしてプラットフォームのバックエンド インスタンスに登録するデバイス ID が付与されます。
    
    ![Device ID on Windows][7]
    
    ![iOS のデバイス ID][8]
    ![Android のデバイス ID][9]

## <a name="key-features-of-the-demo-app"></a>デモ アプリの主な機能
* 既に説明したように、このアプリを使用すると、Mobile Engagement の主要なリソースを理解できます。 左側のメニューにあるリンクにアクセスしてください。
* 各プラットフォームでアプリ外通知を発生させることができます。 これらの通知は、"**通知のみ**" として配信できます。ここで、通知をクリックするとアプリケーションのネイティブ画面 (**ディープ リンクの設定**を使用) が単に開きます。また、"**Web のアナウンス**" として配信することもできます。ここでは、通知をクリックしたときに表示される Mobile Engagement バックエンドから追加の HTML コンテンツを配信できます。
  
    ![Out-of-app notifications][29]
* iOS では、アプリを閉じて、アプリ外通知またはシステムのプッシュ通知を確認する必要があります。 このアプリ外通知に追加される *[Feedback]* (フィードバック) と *[Share]* (共有) のボタンのように、**動作設定ボタン**の追加の実装をここで確認できます (これにより、ユーザーは通知自体から操作を行うことが可能になります)。
  
    ![Out-of-app notifications on iOS][11] ![Out-of-app notification display on iOS][14]
* Android では、(iOS でサポートされているように) **動作設定ボタン**と共に、複数行のテキスト (**テキスト大**) または通知イメージ (**大きい画像**) を通知に追加するオプションがサポートされています。
  
    ![Out-of-app notifications on Android][12] ![Out-of-app notification display on Android][15]
* Windows 10 では、通知の外観を PC で確認できます。 この通知は Windows 10 の **通知センター**にも表示されます。 現時点で、Windows SDK への **動作設定ボタン** の追加はサポートされていません。
  
    ![Out-of-app notifications on Windows][10] ![Out-of-app display on Windows][13]
* 各プラットフォームで既定の "アプリ内" 通知を発生させることができます。 これは 2 段階で発生します。最初に、**通知**ウィンドウが表示され、 最初に、 **アナウンス**が開きます。 このアナウンスの内容は、Mobile Engagement バックエンド インスタンスから取得されます。 SDK には、通知とアナウンス両方のテンプレートが用意されています。 これらは、このデモ アプリで示すように、ロゴや色の追加によって簡単にカスタマイズできます。  
  
    ![In-app notifications on Windows][16]
  
    ![In-app notifications on iOS][17]  ![In-app notifications on Android][18]
  
    **iOS**、**Android**
* Mobile Engagement の **[カテゴリ]** 機能を使用して、この既定の発生をカスタマイズすることもできます。 デモ アプリでは、通知の発生を変更するための一般的な方法を 2 つ説明してきました。 Windows SDK では、[カテゴリ] 機能がまだサポートされていないことに注意してください。
  
    **全画面スポット ビュー:**
  
    ![In-app notification - Interstitial category][30]
  
    ![Interstitial category on iOS][21]     ![Interstitial category on Android][22]
  
    **ポップアップ通知:**
  
    ![In-app notification - Pop-up category][31]
  
    ![Pop-up notification on iOS][19]    ![Pop-up notification on Android][20]

**iOS**、**Android**

* Mobile Engagement では、 **ポーリング**と呼ばれる、特殊な種類のアプリ内通知もサポートしています。 これにより、簡単なアンケートをセグメント化されたアプリ ユーザーに送信することができます。 次のスクリーンショットに示すように、複数の質問と各質問に対する選択肢を追加することができます。 その後、これは、アプリ内通知としてアプリ ユーザーに表示されます。   
  
    ![Poll notifications][32]
  
    ![Survey on Windows][26]
  
    ![Survey on iOS][27]   ![Survey on Android][28]

**iOS**、**Android**

* Mobile Engagement では、サイレントな**データ プッシュ**通知の送信もサポートしています。 この通知を使用すると、サービスのデータ (次の例の JSON データなど) を送信できます。このデータは、アプリで処理して、なんらかの処理を行うことができます。 たとえば、データ プッシュ通知を使用することでどのように品物の価格を選択的に変更しているかを示します。
  
    ![Data push notification][33]
  
    ![Data push notification on Windows][23]
  
    ![Data push notification on iOS][24]  ![Data push notification on Android][25]

**iOS**、**Android**

> [!NOTE]
> サンプル通知画面の **[Click here for instructions on how to send these notifications from Mobile Engagement platform]** (Mobile Engagement プラットフォームからこれらの通知を送信する手順についてはここをクリック) をクリックすると、これらの通知の詳細な手順を確認できます。
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png



<!--HONumber=Nov16_HO3-->


