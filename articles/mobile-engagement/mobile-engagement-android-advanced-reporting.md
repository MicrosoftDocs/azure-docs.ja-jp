---
title: "Azure Mobile Engagement Android SDK の詳細なレポート オプション"
description: "Azure Mobile Engagement Android SDK の分析をキャプチャするための詳細なレポート作成を実行する方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a1445afa2c2fca1a31ad9c012b9c8a917ebf65c


---
# <a name="advanced-reporting-with-engagement-on-android"></a>Android での Engagement による詳細なレポート
> [!div class="op_single_selector"]
> * [ユニバーサル Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

このトピックでは、Android アプリケーションでの他のレポート シナリオについて説明します。 これらのオプションを、 [作業開始](mobile-engagement-android-get-started.md) チュートリアルで作成されたアプリに適用できます。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

完了したチュートリアルは意図的に直接的かつシンプルになっていましたが、選択できる詳細オプションがあります。

## <a name="modifying-your-activity-classes"></a>`Activity` クラスの変更
[作業開始のチュートリアル](mobile-engagement-android-get-started.md)で必要だったのは、`*Activity` サブクラスが、対応する `Engagement*Activity` クラスから継承されるようにすることだけでした。 (たとえば、レガシー アクティビティが `ListActivity` を拡張した場合は、`EngagementListActivity` も拡張されるようにします)。

> [!IMPORTANT]
> `EngagementListActivity` または `EngagementExpandableListActivity` を使う場合は、`requestWindowFeature(...);` に対するすべての呼び出しが `super.onCreate(...);` に対する呼び出しの前に行われることを確認します。それ以外の場合、クラッシュが発生します。
> 
> 

これらのクラスは `src` フォルダーで見つけることができ、プロジェクトにコピーできます。 クラスは **JavaDoc**にも用意されています。

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>別の方法: `startActivity()` と `endActivity()` を手動で呼び出す
`Activity` クラスをオーバーロードできないか、そうしたくない場合は、代わりに `EngagementAgent` のメソッドを直接呼び出すことによって、アクティビティの開始と終了を実行できます。

> [!IMPORTANT]
> Android SDK は、アプリケーションが閉じられる場合でも `endActivity()` メソッドを呼び出すことはありません (Android では、アプリケーションが閉じられることはありません)。 このため、"*すべて*" のアクティビティの `onResume` コールバック内で `startActivity()` メソッドを呼び出し、"*すべて* のアクティビティの `onPause()` コールバック内で `endActivity()` メソッドを呼び出すことを "*強く*" お勧めします。 これは、セッションがリークしないことを保証する唯一の方法です。 セッションがリークした場合、(セッションが保留中である限り、サービスが接続されたままになるため)、Engagement サービスが Engagement バックエンドから切断されることはありません。
> 
> 

たとえば次のようになります。

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

この例は、`EngagementActivity` クラスとそのバリアントに似ています。ソース コードは `src` フォルダーに提供されています。

## <a name="using-applicationoncreate"></a>Application.onCreate() の使用
`Application.onCreate()` とその他のアプリケーション コールバックに配置したすべてのコードは、Engagement サービスを含む、アプリケーションのプロセスすべてで実行されます。 Engagement のプロセス内の不要なメモリの割り当てとスレッド、重複するブロードキャスト レシーバーやサービスなど、望ましくない副作用が発生する可能性があります。

`Application.onCreate()` をオーバーライドする場合は、次のコード スニペットを `Application.onCreate()` 関数の先頭に追加することをお勧めします。

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

同じことを `Application.onTerminate()`、`Application.onLowMemory()`、`Application.onConfigurationChanged(...)` に対しても行うことができます。

`EngagementApplication` を `Application` の代わりに拡張することもできます。コールバック `Application.onCreate()` は、プロセス チェックを実行し、現在のプロセスが Engagement サービスをホストするプロセスでない場合のみ `Application.onApplicationProcessCreate()` を呼び出します。

## <a name="tags-in-the-androidmanifestxml-file"></a>AndroidManifest.xml ファイルのタグ
AndroidManifest.xml ファイルのサービス タグでは、 `android:label` 属性を使って、エンド ユーザーの電話の "実行中のサービス" 画面に表示される Engagement サービスの名前を選択できます。 この属性には `"<Your application name>Service"` を設定することをお勧めします (例: `"AcmeFunGameService"`)。

`android:process` 属性を指定すると、Engagement サービスが独自のプロセスで実行されることが保証されます (Engagement をアプリケーションと同じプロセス内で実行すると、メイン/UI スレッドの反応が遅くなる可能性があります)。

## <a name="building-with-proguard"></a>ProGuard でのビルド
アプリケーション パッケージを ProGuard でビルドする場合は、いくつかのクラスを保持する必要があります。 次の構成スニペットを使うことができます。

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }



<!--HONumber=Nov16_HO3-->


