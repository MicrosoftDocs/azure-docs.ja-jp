---
title: "Azure Mobile Engagement iOS SDK の統合 | Microsoft Docs"
description: "Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/14/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7c4e962527d0b4cd66f6ca4974851c5e5d523de7


---
# <a name="how-to-integrate-engagement-on-ios"></a>Engagement を iOS に統合する方法
> [!div class="op_single_selector"]
> * [Windows ユニバーサル](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

この手順では、iOS アプリケーションで Engagement の分析機能、監視機能を有効化する最も簡単な方法を説明します。

Engagement SDK には、iOS6 以降と Xcode 8 が必要です。アプリケーションのデプロイ ターゲットは iOS6 以降である必要があります。

> [!NOTE]
> XCode 7 に依存している場合は、 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)を使用できます。 この以前のバージョンの Reach モジュールには、iOS 10 デバイスで実行したときに既知のバグがあります。詳細については、[Reach モジュールの統合](mobile-engagement-ios-integrate-engagement-reach.md)に関する記事をご覧ください。 SDK v3.2.4 を使用する場合は、次の手順で `UserNotifications.framework` のインポートをスキップしてください。
> 
> 

次の手順は、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計情報を計算するのに必要なログのレポートを有効にするためのものです。 イベント、エラー、ジョブなどの他の統計情報をコンピューティングするのに必要なログのレポートについては、これらの統計がアプリケーションに依存しているので、エンゲージメント API を使用して手動で実行する必要があります ([iOS アプリで高度な Mobile Engagement タグ付け API を使用する方法](mobile-engagement-ios-use-engagement-api.md)を参照)。

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Engagement SDK を iOS プロジェクトに埋め込む
* [ここ](http://aka.ms/qk2rnj)から iOS SDK をダウンロードします。
* Engagement SDK の iOS プロジェクトへの追加: Xcode でプロジェクトを右クリックし、**[Add files to ... (... にファイルを追加)]** を選択して、`EngagementSDK` フォルダーを選択します。
* Engagement では追加のフレームワークを動作させる必要がある: プロジェクト エクスプローラーでプロジェクト ペインを開き、適切なターゲットを選択します。 次に、**[Build phases (ビルド フェーズ)]** タブを開き、**[Link Binary With Libraries (バイナリをライブラリにリンクする)]** メニューでこれらのフレームワークを追加します。
  
  * `UserNotifications.framework` - リンクを `Optional` として設定します。
  * `AdSupport.framework` - リンクを `Optional` として設定します。
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> AdSupport フレームワークは削除してもかまいません。 Engagement では、IDFA の収集のためこのフレームワークが必要です。 ただし、IDFA コレクションは、この ID に関する新しい Apple ポリシーに準じて無効にできます \<ios-sdk-engagement-idfa\>。
> 
> 

## <a name="initialize-the-engagement-sdk"></a>Engagement SDK を初期化する
アプリケーション デリゲートを変更する必要がある:

* 実装ファイルの先頭で、Engagement エージェントをインポートします。
  
      [...]
      #import "EngagementAgent.h"
* メソッド '**applicationDidFinishLaunching:**' または '**application:didFinishLaunchingWithOptions:**' 内で Engagement を初期化します。
  
      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>基本的なレポート
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>推奨される方法: `UIViewController` クラスをオーバーロードします
Engagement でのユーザー、セッション、アクティビティ、クラッシュ、技術に関する統計のコンピューティングに必要なすべてのログのレポートを有効化するには、単にすべての `UIViewController` サブクラスを `EngagementViewController` クラスから継承するように設定します (`UITableViewController` -\> `EngagementTableViewController` と同じルール)。

**エンゲージメントを使用しない場合:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**エンゲージメントを使用する場合:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>代替の方法: 手動で `startActivity()` を呼び出す
`UIViewController` クラスをオーバーロードできないか、オーバーロードしたくない場合は、`EngagementAgent` のメソッドを直接呼び出して、活動を開始できます。

> [!IMPORTANT]
> iOS SDK は、アプリケーションが閉じられると、`endActivity()` メソッドを自動的に呼び出します。 そのため、ユーザーのアクティビティが変化するごとに `startActivity` メソッドを呼び出し、`endActivity` メソッドは "*決して*" 呼び出さないようにすることを "*強く*" お勧めします。このメソッドを呼び出すと現在のセッションを強制的に終了します。
> 
> 

## <a name="location-reporting"></a>場所レポート
Apple の規約では、統計の目的に限定されたアプリケーションでの場所の追跡の使用は認められていません。 このため、アプリケーションが他の目的でも場所の追跡機能を使用する場合にのみ、場所レポートを有効にすることをお勧めします。

iOS 8 以降では、アプリケーションの Info.plist ファイルにキー [NSLocationWhenInUseUsageDescription] または [NSLocationAlwaysUsageDescription] の文字列を設定して、アプリケーションで場所サービスをどのように使用するかの説明を提供する必要があります。 Engagement を使って背景で場所をレポートするには、キー NSLocationAlwaysUsageDescription を追加します。 それ以外の場合は、キー NSLocationWhenInUseUsageDescription を追加します。

### <a name="lazy-area-location-reporting"></a>大まかなエリアの位置報告
大まかなエリアの位置報告では、デバイスに関連付けられた国、リージョン、地域をレポートできます。 このタイプの場所レポートでは、セル ID または WIFI に基づいたネットワークの場所のみを使用します。 デバイス エリアがセッションごとに最大 1 回レポートされます。 GPS を使用しないため、このタイプの場所レポートでは、まったくとは言わないまでも、電力消費量にほとんど影響がありません。

報告されたエリアを基に、ユーザー、セッション、イベント、エラーに関する地理的な統計をコンピューティングします。 また、リーチ キャンペーンの条件としても使用されます。 デバイスに関する最新の既知のエリアは [デバイス API]を利用して取得できます。

大まかなエリアの位置報告を有効にするには、Engagement エージェントを初期化した後、次の行を追加します。

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>リアル タイム場所レポート
リアル タイム場所レポートでは、デバイスに関連付けられた緯度と経度をレポートできます。 既定では、このタイプの場所レポートでは (セル ID または WIFI に基づいた) ネットワークの場所のみが使用されます。レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。

リアル タイム場所レポートは、統計のコンピューティングに使用することは *できません* 。 その唯一の目的は、リーチ キャンペーンでリアルタイム ジオフェンシング \<Reach-Audience-geofencing\> 基準の利用を可能にすることです。

リアル タイム場所レポートを有効にするには、Engagement エージェントの初期化後に、次の行を追加します。

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS ベースのレポート
既定では、リアル タイム場所レポートでは、ネットワーク ベースの場所のみを使用します。 GPS ベースの場所 (正確性が格段に優れています) を使用できるようにするには、以下を追加します。

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>背景レポート
既定では、リアル タイム場所レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。 バックグウランドでのレポートも有効にするには、以下を追加します。

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> アプリケーションを背景で実行した場合、GPS を有効にしても、ネットワーク ベースの場所のみがレポートされます。
> 
> 

この機能を実装すると、アプリケーションが背景になったときに、 [startMonitoringSignificantLocationChanges] が呼び出されます。 これにより、新しい場所イベントが発生すると、アプリケーションが背景に自動的に再起動されることに注意してください。

## <a name="advanced-reporting"></a>詳細な報告
オプションとして、アプリケーション固有のイベント、エラー、ジョブをレポートする必要がある場合は、 `EngagementAgent` クラスのメソッドを通じて Engagement API を使用します。 このクラスのオブジェクトを取得するには、 `[EngagementAgent shared]` 静的メソッドを呼び出します。

Engagement API では、Engagement の高度な機能をすべて利用できます。詳細は「Engagement API を iOS で使用する方法」にあります(`EngagementAgent` クラスの技術文書にもあります)。

## <a name="disable-idfa-collection"></a>IDFA コレクションの無効化
既定では、Engagement は [IDFA] を使用して、ユーザーを一意に特定します。 ただし、アプリの他の場所で広告を使用していない場合、App Store のレビュー プロセスで拒否される場合があります。 IDFA コレクションは、プリプロセッサのマクロ `ENGAGEMENT_DISABLE_IDFA` を pch ファイル (またはアプリケーションの `Build Settings`) に追加することによって無効にできます。 これにより、アプリケーション ビルドの `ASIdentifierManager`、`advertisingIdentifier`、`isAdvertisingTrackingEnabled` への参照がないことを確認できます。

**prefix.pch** ファイルでの統合:

    #define ENGAGEMENT_DISABLE_IDFA
    ...

IDFA コレクションがアプリケーション内で適切に無効化されていることを確認するには、Engagement テスト ログをチェックします。 詳細については、統合テスト \<ios-sdk-engagement-test-idfa\> に関するドキュメントを参照してください。

## <a name="disable-log-reporting"></a>ログ レポートの無効化
### <a name="using-a-method-call"></a>メソッド呼び出しを使用した場合
Engagement でログの送信を停止したい場合は、以下を呼び出します。

    [[EngagementAgent shared] setEnabled:NO];

この呼び出しは永続的です: `NSUserDefaults` を使用して情報を格納します。

ログ レポートは、同じ関数を `YES`でもう一度呼び出すことによって有効にすることができます。

### <a name="integration-in-your-settings-bundle"></a>設定バンドルでの統合
この機能を呼び出す代わりに、この設定を既存の `Settings.bundle` ファイルで直接統合することもできます。 文字列 `engagement_agent_enabled` は優先識別子として使用し、トグル スイッチ (`PSToggleSwitchSpecifier`) に関連付ける必要があります。

次の `Settings.bundle` の例は、その実装方法を示しています。

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[デバイス API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier



<!--HONumber=Nov16_HO3-->


