---
title: "Azure Mobile Engagement トラブルシューティング ガイド"
description: "Azure Mobile Engagement 関連のトラブルシューティング ガイド"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1947600f6167dff6ec4fa104b0f98200bcf2a2e1
ms.lasthandoff: 11/17/2016


---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement - トラブルシューティング ガイド
## <a name="introduction"></a>はじめに
以下のトラブルシューティング ガイドは、よく発生するいくつかの問題について根本原因を理解するのに役立ちます。また、このガイドを参照することで、トラブルシューティングを自分で行うことができます。 

## <a name="general"></a>全般
一般に、次の項目を常に確認してください。

1. 「 [チュートリアルの概要](mobile-engagement-windows-store-dotnet-get-started.md)
2. 最新バージョンのプラットフォーム SDK を使用しています。 
3. エミュレーターのみに特有の問題もあるため、実際のデバイスとエミュレーターの両方でテストを行います。 
4.  [ここ](../azure-subscription-service-limits.md)
5. Mobile Engagement サービス バックエンドに接続できない場合またはデータが継続的に読み込まれないという状況が発生している場合は、 [ここ](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>「監視」に関する問題
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>目的のデバイスが [監視] タブに表示されません。
[監視] タブにはリアルタイムで Mobile Engagement プラットフォームに接続されているデバイスが表示されます。 エミュレーターとデバイスでデバッグしている場合は、ここにセッションが少なくとも 1 つ表示される必要があります。 アプリケーションが配布されると、リアルタイムでプラットフォームに接続されているデバイスがアクティブ セッション ゲージに示されます。 

[監視] タブにデバイスが表示されない場合、おそらくは SDK の統合に問題があります。 トラブルシューティングで一般的に使用する手順を次に示します。

1. モバイル アプリで正しい接続文字列が使用されていること、さらに、その接続文字列が [API キー] セクションからではなく、[SDK キー] セクションからのものであることを確認します。 この接続文字列はモバイル アプリを Mobile Engagement アプリのインスタンスに接続します。これにより、[監視] タブにデバイスが表示されます。 
2. Windows プラットフォームの場合 - ページが `OnNavigatedTo` メソッドより優先される場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。
3. Mobile Engagement を既存のモバイル アプリに統合している場合は、 [ここ](mobile-engagement-windows-store-integrate-engagement.md)
4. 「 [チュートリアルの概要](mobile-engagement-windows-store-dotnet-get-started.md)」に説明しているように、作業しているプラットフォームによっては、ページを EngagementActivity でオーバーライドして少なくとも 1 つ画面/アクティビティを送信していることを確認します。

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>アプリ/エミュレーターを切断または終了しても、[監視] タブにセッションが表示されています。
この時点でプラットフォームに接続しているユーザーが 1 人だけであり、そのユーザーがエミュレーターを使用してアプリを開いている場合、この症状はエミュレーターの Quirks 動作によるものであると考えられます。 一般に、アプリ セッションが正常に切断されるためには、エミュレーターでホーム画面に戻っていることを確認する必要があります。 さらに、Windows プラットフォームで Visual Studio を使用してデバッグを行っている場合は、Visual Studio の **[ライフサイクル イベント]** メニュー バーに移動し、**[中断]** をクリックしてセッションを実際に閉じることが必要な場合があります。 詳細については、「 [Windows チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md) 」を参照してください。 

## <a name="analytics-issues"></a>「分析」に関する問題
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>[分析] タブにデータが何も表示されません。あるいは更新されたデータが表示されません。
分析データは定期的に再計算されます。この更新には最大 24 時間かかる可能性があります。 このデータはリアルタイムのデータではありません。表示されるのは、この 24 時間の期間内に更新されるデータとなります。
ただし、少なくとも 1 つのページを `EngagementActivity` でオーバーライドするか、または `SendActivity` を明示的に呼び出すことによってプラットフォームのバックエンドに少なくとも 1 つの画面またはアクティビティを送信していることを確認してください。 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>[分析] タブに表示されるデバイスのキャプチャされた日付と時刻が正しくありません。
分析の期間は、ユーザーのデバイス設定からの日付に基づいています。 デバイスの日付が正しく設定されていることを確認してください。 

## <a name="segment-issues"></a>「セグメント」に関する問題
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>セグメントを作成しましたが、セグメントがグレー表示となっています。または、セグメントにデータが表示されません。
現時点でセグメントの作成はリアルタイムで行われません。 セグメントの計算は、分析データの集計と同時に行われるので、最大で 24 時間かかる可能性があります。 後で確認する必要がありますが、一方で、セグメントを形成する際に使用するデータがモバイル アプリによって実際に送信されていることも確認してください。 例: ’foo’ というイベントがモバイル デバイスによって送信されていない場合、EventName = foo を条件として作成されるセグメント用のセグメント データは存在しないことになります。 また、SDK 統合をチェックして、モバイル アプリによってデータが正しく送信されていることを確認する必要があります。 

## <a name="reach-or-push-notifications-issues"></a>「リーチ」またはプッシュ通知に関する問題
### <a name="my-push-messages-are-not-being-delivered"></a>プッシュ メッセージが配信されません。
1. すべてのコンポーネント (モバイル アプリ、SDK、およびサービス) が正しく接続されていて、プッシュ通知を配信できる状態にあることを確認するために、まず、テスト デバイスに通知を送信してみてください。 
2. 常に、スケジュールも対象ユーザー設定基準も指定されていないキャンペーンを経由して最も単純な 'アプリ外通知' を最初に送信します。 これは、もう一度、通知接続が正常に機能していることを証明するための作業です。 
3. アプリ内通知の配信に問題が生じている場合は、最初の手順としてまずアプリ外通知を送信してみるのも有効な手順です。 
4. モバイル アプリに対して 'ネイティブ プッシュ' が正しく構成されていることを確認します。 プラットフォームによって異なりますが、ネイティブ プッシュではキー (Android、Windows) または証明書 (iOS) のいずれかが必要となります。 詳細については、「 [ユーザー インターフェイス - 設定](mobile-engagement-user-interface-settings.md)
5. ユーザーがモバイル OS を介してアプリ外通知をブロックしている場合もあります。この状況が発生していないことを確認します。 
6. リーチ キャンペーンの **[キャンペーン]** セクションで *[対象ユーザーを無視して API 経由でユーザーにプッシュを送信する]* オプションが設定されていないことを確認します。この設定を使用すると、プッシュ通知を API 経由でしか送信できません。 
7. 問題の原因になり得るネットワーク接続を排除するために、WIFI 経由で接続されているデバイスと電話オペレーター ネットワークの両方を使用してプッシュ キャンペーンがテストされていることを確認します。
8. 非同期デバイスもプッシュ通知サービスの通知配信機能を妨げるので、デバイス/エミュレーターのシステム日付/時刻が正しいことを確認します。 

さらに、プラットフォーム固有のトラブルシューティングの手順を以下に示します。

1. **iOS** 
   
   * iOS Push Notifications の証明書が有効であり、かつ期限切れになっていないことを確認します。 
   * Mobile Engagement アプリの *運用* 証明書が正しく構成されていることを確認します。 
   * "*実在する物理デバイス*" をテストしていることを確認します。 IOS シミュレーターはプッシュ メッセージを処理できません。
   * モバイル アプリでバンドル識別子が正しく構成されていることを確認します。 手順については、 [ここ](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * テストするときは、モバイル プロビジョニング プロファイルの "アドホック" ディストリビューションを使用します。 アプリが "デバッグ" を使用してコンパイルされている場合、通知は受信できません。
2. **Android**
   
   * モバイル アプリの AndroidManifest.xml ファイル内にプロジェクト番号 (文字 \n の後に続く) が正しく指定されていることを確認します。 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Android マニフェスト ファイルでアクセス許可の不足や不適切な構成がないことを確認します。 
   * クライアント アプリに追加するプロジェクト番号が、GCM サーバー キーを取得したアカウントと同じアカウントのものであることを確認します。 このアカウントが一致していないと、プッシュを送信できなくなります。 
   * システム通知は受信しているがアプリ内通知は受信していないという場合は、Android マニフェスト ファイル内に適切なアイコンが指定されていない可能性があるので、「 [通知セクションのアイコンを指定する](mobile-engagement-android-get-started.md) 」を確認してください。 
   * BigPicture 通知を送信する場合に、外部イメージ サーバーを使用する場合は、それらのサーバーが HTTP の "GET" と "HEAD" をサポートする必要があります。
3. **Windows**
   
   * アプリが有効な Windows ストア アプリに関連付けられていることを確認します。 Visual Studio - プロジェクトを右クリックし、[アプリとストアを関連付ける] オプションを選択し、Windows ストアで作成したアプリを選択する必要があります。 この Windows ストア アプリは、Mobile Engagement ポータルで構成されるネイティブ プッシュ資格情報を取得した場所にあるアプリと同じである必要があります。
   * `EngagementOverlay` 統合でアプリ外プッシュ通知を受信しているがアプリ内通知は受信していない場合は、ページ内にルートの Grid 要素が存在することを確認してください。 EngagementOverlay は、xaml ファイルで見つけた最初の「Grid」要素を使用し、ページに 2 つの Web ビューを追加します。 Web ビューの設定場所を特定する場合は、次のように "EngagementGrid" という名前のグリッドを定義することができます。ただし、通知と次のアナウンスをアプリ内通知として表示する後続の 2 つの Web ビューについて、高さと幅が十分であることを確認する必要があります。
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>プッシュ通知/アナウンス/キャンペーンを作成しましたが、これらは私に通知を送信した後も、'Active' と表示されています。 これはどういうことですか?
Mobile Engagement で作成した**キャンペーン**がそのように呼び出されるのは、キャンペーンが実行時間の長いプッシュ通知であるためです。すなわち、新しいデバイスが Mobile Engagement プラットフォームに接続すると、キャンペーンで設定した条件を新しいデバイスが満足する限り、新しいデバイスにはここで構成した通知が自動的に送信されます。 これは、ワン ショットの単一通知セットアップではありません。 キャンペーンがこれ以上通知を送信しないようにするには、**[完了]** ボタンをクリックしてキャンペーンを終了する必要があります。 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>プッシュ キャンペーンを作成し、通知を正常に受信しています。しかし、アプリを開くたびに、前に受信したにもかかわらず同じ通知が届きます。
この現象は、テスト中で、エミュレーターまたは TestFlight のような何らかのテスト フレームワークを使用している場合に発生すると考えられます。 ここでは次のことが行われています。どのアプリ実行インスタンスでも、インスタンスは新しい DeviceID を取得して、それをバックエンドに送信します。これにより、Mobile Engagement プラットフォームはインスタンスを新しいデバイスとして扱い、通知を送信します。 

## <a name="getting-support"></a>サポートの利用
問題を自分で解決できない場合は、次のことを行うことができます。

1. StackOverflow フォーラムと [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) の既存のスレッドで実際の問題を検索し、ヒットしない場合はフォーラムで質問してください。 
2. 不足している機能が見つかった場合は、 [UserVoice フォーラム](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Microsoft サポートを使用する場合は、次の詳細情報を指定してサポート インシデントを開いてください。 
   * Azure サブスクリプション ID
   * プラットフォーム (例: iOS、Android など)
   * アプリケーション ID
   * キャンペーン ID (プッシュ通知に関する問題の場合)
   * Device ID
   * Mobile Engagement SDK のバージョン (例: Android SDK v2.1.0)
   * 表示されるエラー メッセージとシナリオを含むエラーの詳細


