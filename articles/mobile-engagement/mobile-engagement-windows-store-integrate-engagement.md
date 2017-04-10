---
title: "Windows ユニバーサル アプリ Engagement SDK 統合"
description: "Windows ユニバーサル アプリと Azure Mobile Engagement を統合する方法"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: bbd6ddeeeff949afe0cb66adc37982c036fc5c46
ms.lasthandoff: 12/08/2016


---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Windows ユニバーサル アプリ Engagement SDK 統合
> [!div class="op_single_selector"]
> * [ユニバーサル Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

この手順では、Windows ユニバーサル アプリケーションで Engagement の分析機能と監視機能をアクティブ化する最も簡単な方法を説明します。

次の手順は、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計情報を計算するのに必要なログのレポートを有効にするためのものです。 イベント、エラー、ジョブなどの他の統計情報を計算するのに必要なログのレポートについては、これらの統計がアプリケーションに依存しているので、Engagement API を使用して手動で実行する必要があります ( [Windows ユニバーサル アプリで高度な Mobile Engagement  タグ付け API を使用する方法](mobile-engagement-windows-store-use-engagement-api.md) を参照)。

## <a name="supported-versions"></a>サポートされているバージョン
Mobile Engagement SDK for Windows ユニバーサル アプリは次を対象とした Windows ランタイム アプリケーションおよびユニバーサル Windows プラットフォーム アプリケーションにのみ統合できます。

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (デスクトップとモバイル ファミリ)

> [!NOTE]
> Windows Phone Silverlight を対象としている場合は、「 [Windows Phone Silverlight の統合手順](mobile-engagement-windows-phone-integrate-engagement.md)」を参照してください。
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Mobile Engagement ユニバーサル アプリ SDK をインストールします。
### <a name="all-platforms"></a>すべてのプラットフォーム
Mobile Engagement SDK for Windows Universal App は「 *MicrosoftAzure.MobileEngagement*」という名称の Nuget パッケージとして利用できます。 これは Visual Studio Nuget Package Manager からインストールできます。

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x および Windows Phone 8.1
NuGet はアプリケーション プロジェクトのルートにある `Resources` フォルダーに SDK のリソースを自動的にデプロイします。

### <a name="windows-10-universal-windows-platform-applications"></a>Windows 10 ユニバーサル Windows プラットフォーム アプリケーション
NuGet は、まだ UWP アプリケーションで SDK のリソースを自動的にデプロイしません。 NuGet でリソースのデプロイメントが再導入されるまで手動で行う必要があります。

1. ファイル エクスプ ローラーを開きます。
2. 次の場所に移動します (**x.x.x** はインストールする Engagement のバージョンです): *%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3. ファイル エクスプローラーから Visual Studio のプロジェクトのルートに、 **リソース** フォルダーをドラッグ アンド ドロップします。
4. Visual Studio でプロジェクトを選択し、**[ソリューション エクスプローラー]** の一番上にある **[すべてのファイルを表示]** アイコンをアクティブ化します。
5. 一部のファイルは、プロジェクトには含まれません。 一度にインポートするには、**リソース** フォルダーを右クリックして **[プロジェクトから除外]** を選択してから、もう一度**リソース** フォルダーを右クリックして **[プロジェクトに含める]** を選択し、フォルダー全体を再度含めます。 これで、 **リソース** フォルダーのすべてのファイルがプロジェクトに追加されます。

## <a name="add-the-capabilities"></a>機能を追加する
Engagement SDK が適切に機能するには、Windows SDK の機能が一部必要になります。

`Package.appxmanifest` ファイルを開き、次の機能が宣言されていることを確認します。

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Engagement SDK を初期化する
### <a name="engagement-configuration"></a>Engagement の構成
Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して、次の内容を指定します。

* `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、 Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

アプリケーションの接続文字列が Azure クラシック ポータルに表示されます。

### <a name="engagement-initialization"></a>Engagement の初期化
新しいプロジェクトを作成すると、 `App.xaml.cs` ファイルが生成されます。 このクラスは、 `Application` から継承し、多くの重要なメソッドが含まれています。 また、Engagement SDK を初期化するためにも使用されます。

`App.xaml.cs`を変更します。

* 次の内容を `using` ステートメントに追加します。
  
      using Microsoft.Azure.Engagement;
* すべての呼び出しに対して 1 回で Engagement の初期化を共有するメソッドを定義します。
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* `OnLaunched` メソッドで `InitEngagement` を呼び出します。
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* カスタム スキーマ、別のアプリケーション、コマンドラインを使用してアプリケーションが起動されると、`OnActivated` メソッドが呼び出されます。 また、アプリをアクティブ化する際は、 Engagement SDK を初期化する必要があります。 そのために、 `OnActivated` メソッドをオーバーライドします。
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
>  Engagement の初期化は、アプリケーションの別の場所には追加しないことを強くお勧めします。
> 
> 

## <a name="basic-reporting"></a>基本的なレポート
### <a name="recommended-method-overload-your-page-classes"></a>推奨される方法: `Page` クラスをオーバーロードします
ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関する統計情報を計算するために、エンゲージメントが必要とするすべてのログのレポートを有効にするには、単純にすべての `Page` サブクラスが `EngagementPage` クラスから継承されるようにします。

次に、この操作をアプリケーションのページに対して実行する例を示します。 同じことを、アプリケーションのすべてのページに対して実行できます。

#### <a name="c-source-file"></a>C# ソース ファイル
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

#### <a name="xaml-file"></a>XAML ファイル
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

#### <a name="override-the-default-behaviour"></a>既定の動作をオーバーライドする
既定では、ページのクラス名は、何も付加しないアクティビティ名として報告されます。 クラスが「Page」サフィックスを使用している場合、Engagement はそれも削除します。

名前の既定の動作をオーバーライドする場合は、単純に次の内容をコードに追加します。

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

アクティビティに関する追加情報を報告する場合は、次の内容をコードに追加します。

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
> Windows ユニバーサル SDK は、アプリケーションを終了する際に、自動的に `EndActivity` メソッドを呼び出します。 そのため、ユーザーのアクティビティが変更されるたびに `StartActivity` メソッドを呼び出すことを**強く**お勧めします。`EndActivity` メソッドは**決して**呼び出さないでください。このメソッドは、現在のユーザーがアプリケーションを終了したと Engagement サーバーに通知するため、これによってすべてのアプリケーション ログが影響を受けるからです。
> 
> 

## <a name="advanced-reporting"></a>詳細な報告
オプションとして、アプリケーション特有のイベント、エラー、ジョブについて報告できます。そのためには、`EngagementAgent` クラスの別のメソッドを使用します。 Engagement API により、Engagement のすべての高度な機能を使用できます。

詳細については、「 [How to use the advanced Mobile Engagement tagging API in your Windows Universal app (Windows ユニバーサル アプリで高度なモバイル エンゲージメント タグ付け API を使用する方法)](mobile-engagement-windows-store-use-engagement-api.md)」をご覧ください

## <a name="advanced-configuration"></a>詳細な構成
### <a name="disable-automatic-crash-reporting"></a>自動クラッシュ レポートを無効にする
Engagement の自動クラッシュ レポート機能を無効にできます。 その場合、未処理の例外が発生しても、Engagement は何も行いません。

> [!WARNING]
> この機能を無効にする計画がある場合は、アプリで未処理の例外が発生しても、エンゲージメントはクラッシュを報告せず、 **さらに** セッションとジョブを終了しないことに注意してください。
> 
> 

自動クラッシュ レポートを無効にするには、宣言した方法に応じて、構成をカスタマイズするだけです。

#### <a name="from-engagementconfigurationxml-file"></a>`EngagementConfiguration.xml` ファイルを使用する場合
`<reportCrash>` タグと `</reportCrash>` タグの間で、report crash を `false` に設定します。

#### <a name="from-engagementconfiguration-object-at-run-time"></a>実行時に `EngagementConfiguration` オブジェクトを使用する場合
EngagementConfiguration オブジェクトを使用して、report crash を false に設定します。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>バースト モード
既定では、Engagement サービスはログをリアルタイムで報告します。 アプリケーションがログを非常に頻繁に報告する場合、ログをバッファーに格納して、それらすべてを定期的に一度に報告する方が適切です (これを「バースト モード」と呼びます)。

そのためには、次のメソッドを呼び出します。

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引数の値は **ミリ秒**単位です。 リアルタイムのログ報告を再度有効にする場合は常に、パラメーターを指定しないか、値 0 を指定して、このメソッドを呼び出します。

バースト モードではわずかにバッテリーの寿命が延びますが、Engagement の監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。 バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。 保存できるログは 300 項目に制限されていることに気を付けてください。 送信時間が長すぎる場合は、ログがいくつか失われる可能性があります。

> [!WARNING]
> また、バーストのしきい値は、1 秒よりも短い期間に設定することはできません。 それを試みた場合、SDK はエラーのトレースを表示し、自動的に既定値 (0 秒) に再設定します。 これにより、SDK はログをリアルタイムで報告するようになります。
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview


