---
title: "Windows Phone Silverlight SDK アップグレード手順"
description: "Azure モバイル エンゲージメント向け Windows Phone Silverlight SDK のアップグレード手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 06b2a8b5e12d33c3ade469491b2694dd4a342cf1
ms.lasthandoff: 11/17/2016


---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK アップグレード手順
既に古いバージョンの SDK をアプリケーションに統合している場合は、SDK をアップグレードする際に次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。 たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

## <a name="from-200-to-330"></a>2.0.0 から 3.3.0 に移行
### <a name="test-logs"></a>テスト ログ
SDK によって生成されるコンソール ログを有効化/無効化/フィルター処理できるようになりました。 これをカスタマイズするには、次の例のように `EngagementAgent.Instance.TestLogEnabled` プロパティを `EngagementTestLogLevel` 列挙型の使用可能な値の 1 つに更新します。

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

## <a name="from-111-to-200"></a>1.1.1 から 2.0.0 に移行
Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。 

> [!IMPORTANT]
> Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。 アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。
> 
> 

以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.1.1 に移行し、次の手順を適用してください。

### <a name="nuget-package"></a>NuGet パッケージ
**Capptain.WindowsPhone** を **MicrosoftAzure.MobileEngagement** Nuget パッケージに置き換えます。

### <a name="applying-mobile-engagement"></a>Mobile Engagement の適用
SDK は `Engagement`という用語を使用します。 この変更を一致させるためにプロジェクトをアップグレードする必要があります。

現在の Capptain NuGet パッケージをアンインストールする必要があります。 [Capptain Resources] フォルダー内のすべての変更が削除されることを検討します。 これらのファイルを保持する場合は、コピーを作成します。

そのあと、新しい Microsoft Azure エンゲージメント NuGet パッケージをプロジェクトにインストールします。 [NuGet](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement)の Web サイトまたはここのインデックスで直接検索できます。 この操作は、エンゲージメントによって使用されるすべてのリソース ファイルを置換し、プロジェクトの参照に新しいエンゲージメントの DLL を追加します。

Capptain DLL の参照を削除して、プロジェクトの参照をクリーンアップする必要があります。 これを実行しない場合は、Capptain のバージョンが競合してエラーが発生します。

Capptain リソースをカスタマイズした場合、古いファイルの内容をコピーし、新しい Engagement ファイルに貼り付けます。 xaml と cs ファイルの両方を更新する必要があることにご注意ください。

これらの手順が完了したら、新しい Engagement の参照で古い Capptain の参照を置き換える必要があります。

1. すべての Capptain 名前空間の更新が必要です。
   
    移行前:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    移行後:
   
        using Microsoft.Azure.Engagement;
2. "Capptain" が含まれているすべての Capptain クラスには、"Engagement" が含まれている必要があります。
   
    移行前:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    移行後:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. xaml ファイルの Capptain 名前空間と属性も変更します。
   
    移行前:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    移行後:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Capptain 画像のようなその他のリソースも名前が変更されて、「Engagement」を使用しますので注意してください。

### <a name="application-id--sdk-key"></a>アプリケーション ID / SDK キー
Engagement は、接続文字列を使用します。 Mobile Engagement でアプリケーション ID と SDK キーを指定する必要はありません。指定する必要があるのは接続文字列のみです。 接続文字列は、EngagementConfiguration ファイルで設定できます。

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで設定できます。

このファイルを編集して、次の内容を指定します。

* `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、 Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

アプリケーションの接続文字列が Azure クラシック ポータルに表示されます。

### <a name="items-name-change"></a>項目名の変更
*capptain* という名前の項目はすべて *engagement* という名前に変更されています。 同様に、*Capptain* は *Engagement* に変更されています。

一般的に使用される Capptain 項目の例:

* CapptainConfiguration は EngagementConfiguration という名前になりました
* CapptainAgent は EngagementAgent という名前になりました
* CapptainReach は EngagementReach という名前になりました
* CapptainHttpConfig は EngagementHttpConfig という名前になりました
* GetCapptainPageName は GetEngagementPageName という名前になりました

名前の変更はオーバーライドされたメソッドにも影響することにご注意ください。


