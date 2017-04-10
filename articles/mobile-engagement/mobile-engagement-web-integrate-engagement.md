---
title: "Azure Mobile Engagement Web SDK の統合 | Microsoft Docs"
description: "Azure Mobile Engagement Web SDK の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7d8eaa180e277741a583522ee62d68f5247b92bb


---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Web アプリケーションへの Azure Mobile Engagement の統合
> [!div class="op_single_selector"]
> * [Windows ユニバーサル](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

この記事では、Web アプリケーションで Azure Mobile Engagement の分析およびモニタリング機能を有効にする最も簡単な方法について説明します。

この手順により、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計情報の計算に必要なログ レポートが有効になります。 イベント、エラー、ジョブなど、アプリケーションに依存する統計については、Azure Mobile Engagement API を使用して、ログ レポートを手動でアクティブ化する必要があります。 詳細については、 [Web アプリケーションで高度な Mobile Engagement タグ付け API を使用する方法](mobile-engagement-web-use-engagement-api.md)に関するページをご覧ください。

## <a name="introduction"></a>はじめに
[Azure Mobile Engagement Web SDK](http://aka.ms/P7b453) をダウンロードします。
Mobile Engagement Web SDK は、1 つの JavaScript ファイル、azure-engagement.js として出荷されます。このファイルを、サイトまたは Web アプリケーションの各ページに追加する必要があります。

> [!IMPORTANT]
> このスクリプトを実行する前に、アプリケーションに必要な Mobile Engagement を構成するスクリプトまたはコード スニペット (ご自身で作成) を実行する必要があります。
> 
> 

## <a name="browser-compatibility"></a>ブラウザーとの互換性
Mobile Engagement Web SDK では、ネイティブの JSON エンコーディングとデコーディング、およびクロス ドメイン AJAX 要求 (W3C CORS 仕様) が使用されています。 これは次のブラウザーに対応しています。

* Microsoft Edge 12 以上
* Internet Explorer 10 以上
* Firefox 3.5 以上
* Chrome 4 以上
* Safari 6 以上
* Opera 12 以上

## <a name="configure-mobile-engagement"></a>Mobile Engagement の構成
次の例のように、グローバルな `azureEngagement` JavaScript オブジェクトを生成するスクリプトを作成します。 サイトには複数のページが含まれる可能性があるため、この例では、すべてのページにこのスクリプトが含まれていることを前提としています。 また、JavaScript オブジェクトには `azure-engagement-conf.js`という名前が付いています。

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

アプリケーションの `connectionString` 値は Azure ポータルに表示されます。

> [!NOTE]
> `appVersionName` と `appVersionCode` は省略可能ですが、 分析でバージョン情報を処理できるように、この 2 つは構成することをお勧めします。
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>ページへの Mobile Engagement スクリプトの挿入
次のいずれかの方法で Mobile Engagement スクリプトをページに追加します。

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

または

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>エイリアス
Mobile Engagement Web SDK スクリプトが読み込まれたら、SDK API にアクセスするために **エンゲージメント** エイリアスが作成されます。 このエイリアスを使用して、SDK の構成を定義することはできません。 このドキュメントでは、このエイリアスを参照として使用します。

既定のエイリアスが、ページ内の別のグローバル変数と競合する場合は、Mobile Engagement Web SDK を読み込む前に、次のように構成で再定義できます。

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>基本的なレポート
Mobile Engagement の基本的なレポートには、ユーザー、セッション、アクティビティ、クラッシュなど、セッション レベルの統計情報が含まれています。

### <a name="session-tracking"></a>セッションの追跡
Mobile Engagement セッションは一連のアクティビティに分割され、それぞれが名前で特定されます。

クラシック Web サイトでは、サイトのページごとに異なるアクティビティを宣言することをお勧めします。 ページ移動のない Web サイトまたは Web アプリケーションについては、ページ内など、さらに小さな範囲でのアクティビティの追跡が必要になる場合があります。

いずれにしても、ユーザー アクティビティを開始したり現在のユーザー アクティビティを変更したりするには、 `engagement.agent.startActivity` 関数を呼び出します。 次に例を示します。

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

アプリケーション ページを閉じてから 3 分以内に、Mobile Engagement サーバーによってオープン セッションが自動的に終了されます。

`engagement.agent.endActivity`を呼び出して、セッションを手動で終了することもできます。 この場合、現在のユーザー アクティビティが "idle" に設定されます。  セッションは、新しい `engagement.agent.startActivity` への呼び出しによって再開されない限り、10 秒後に終了します。

この 10 秒の時間差は、次のように、グローバル エンゲージメント オブジェクトで構成できます。

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> この段階では AJAX 呼び出しを実行できないため、`onunload` コールバックで `engagement.agent.endActivity` を使用することはできません。
> 
> 

## <a name="advanced-reporting"></a>詳細な報告
オプションとして、アプリケーション固有のイベント、エラー、ジョブをレポートする必要がある場合は、Mobile Engagement API を使用します。 Mobile Engagement API には `engagement.agent` オブジェクトを使ってアクセスします。

Mobile Engagement API の Mobile Engagement の高度な機能すべてにアクセスすることができます。 API の詳細については、 [Web アプリケーションで高度な Mobile Engagement タグ付け API を使用する方法](mobile-engagement-web-use-engagement-api.md)に関するページをご覧ください。

## <a name="customize-the-urls-used-for-ajax-calls"></a>AJAX 呼び出しに使用する URL のカスタマイズ
Mobile Engagement Web SDK によって使用される URL はカスタマイズできます。 たとえば、ログ URL (SDK でログに使用されるエンドポイント) を再定義するには、次のように構成をオーバーライドします。

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

URL 関数から返された文字列の先頭が `/`、`//`、`http://`、`https://` のいずれかである場合、既定のスキームは使用されません。 既定では、こうした URL には `https://` スキームが使用されます。 既定のスキームをカスタマイズする必要がある場合は、次のように構成をオーバーライドしてください。

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };



<!--HONumber=Nov16_HO3-->


