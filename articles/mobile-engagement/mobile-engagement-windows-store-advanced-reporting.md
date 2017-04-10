---
title: "MobileApps Engagement を使用した Windows ユニバーサルの詳細なレポート"
description: "Windows ユニバーサル アプリと Azure Mobile Engagement を統合する方法"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876


---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Windows ユニバーサル アプリ Engagement SDK を使用した詳細なレポート
> [!div class="op_single_selector"]
> * [ユニバーサル Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

このトピックでは、Windows ユニバーサル アプリケーションでの他のレポート シナリオについて説明します。 これらのシナリオには、 [作業開始](mobile-engagement-windows-store-dotnet-get-started.md) チュートリアルで作成されたアプリに適用できるオプションがあります。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

このチュートリアルを開始する前に、意図的に直接的かつシンプルな「 [作業開始](mobile-engagement-windows-store-dotnet-get-started.md) 」チュートリアルを完了する必要があります。 このチュートリアルでは、選択できる追加のオプションについて説明します。

## <a name="specifying-engagement-configuration-at-runtime"></a>実行時の Engagement の構成の指定
Engagement の構成は、「[作業の開始](mobile-engagement-windows-store-dotnet-get-started.md)」トピックで指定されたプロジェクトの `Resources\EngagementConfiguration.xml` ファイルに一元化されています。

ただし、Engagement エージェントを初期化する前に次のメソッドを呼び出して、実行時に指定することもできます。

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>推奨される方法: `Page` クラスをオーバーロードします
ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関する統計情報を計算するために、Engagement が必要とするすべてのログのレポートを有効にするには、すべての `Page` サブクラスが `EngagementPage` クラスから継承されるようにします。

アプリケーションのページの例を示します。 同じことを、アプリケーションのすべてのページに対して実行できます。

### <a name="c-source-file"></a>C# ソース ファイル
ページの `.xaml.cs` ファイルを変更します。

* 次の内容を `using` ステートメントに追加します。
  
      using Microsoft.Azure.Engagement;
* `Page` を `EngagementPage` に置き換えます。

**Engagement を使用しない場合:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**エンゲージメントを使用する場合:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> ページが `OnNavigatedTo` メソッドをオーバーライドする場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。 そうしないと、アクティビティが報告されません (`EngagementPage` は、`OnNavigatedTo` メソッド内で `StartActivity` を呼び出します)。
> 
> 

### <a name="xaml-file"></a>XAML ファイル
ページの `.xaml` ファイルを変更します。

* 次の内容を名前空間宣言に追加します。
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* `Page` を `engagement:EngagementPage` に置き換えます。

**Engagement を使用しない場合:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**エンゲージメントを使用する場合:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>既定の動作をオーバーライドする
既定では、ページのクラス名は、何も付加しないアクティビティ名として報告されます。 クラスが「Page」サフィックスを使用している場合、Engagement はそれを削除します。

名前の既定の動作をオーバーライドするには、次のコードを追加します。

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

アクティビティに関する追加の情報を報告するには、次のコードを追加します。

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

これらのメソッドは、ページの `OnNavigatedTo` メソッドから呼び出されます。

### <a name="alternate-method-call-startactivity-manually"></a>代替の方法: 手動で `StartActivity()` を呼び出す
`Page` クラスをオーバーロードできない、またはしない場合は、代わりに `EngagementAgent` メソッドを直接呼び出して、アクティビティを開始できます。

ページの `OnNavigatedTo` メソッドの中から `StartActivity` を呼び出すことをお勧めします。

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> セッションは正常に終了してください。
> 
> Windows ユニバーサル SDK は、アプリケーションを終了する際に、自動的に `EndActivity` メソッドを呼び出します。 そのため、ユーザーのアクティビティが変化するごとに `StartActivity` メソッドを呼び出し、`EndActivity` メソッドは**決して**呼び出さないことを**強く**お勧めします。 このメソッドは、現在のユーザーがアプリケーションを終了したと Engagement サーバーに通知するため、すべてのアプリケーション ログが影響を受けるからです。
> 
> 

## <a name="advanced-reporting"></a>詳細な報告
オプションとして、アプリケーション特有のイベント、エラー、ジョブについて報告できます。そのためには、`EngagementAgent` クラスの別のメソッドを使用します。 Engagement API により、Engagement のすべての高度な機能を使用できます。

詳細については、「 [How to use the advanced Mobile Engagement tagging API in your Windows Universal app (Windows ユニバーサル アプリで高度なモバイル エンゲージメント タグ付け API を使用する方法)](mobile-engagement-windows-store-use-engagement-api.md)」をご覧ください




<!--HONumber=Nov16_HO3-->


