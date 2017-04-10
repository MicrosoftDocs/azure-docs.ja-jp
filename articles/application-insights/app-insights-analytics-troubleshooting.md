---
title: "Azure Application Insights の Analytics のトラブルシューティング | Microsoft Docs"
description: "Application Insights Analyticsで問題が発生しましたか? ここから開始します。 "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: 6533b1e6a40b7cbf7d3d6961523144b3d0e5263b


---
# <a name="troubleshoot-analytics-in-application-insights"></a>Application Insights の Analytics のトラブルシューティング
[Application Insights Analytics](app-insights-analytics.md)で問題が発生しましたか? ここから開始します。 Analytics は、Azure Application Insights の強力な検索ツールです。

## <a name="limits"></a>制限
* 現時点では、クエリの結果は過去&1; 週間のデータだけに制限されます。
* テストを実施したブラウザーは、Chrome、Microsoft Edge、および Internet Explorer の最新のエディションです。

## <a name="known-incompatible-browser-extensions"></a>既知の互換性のないブラウザーの拡張機能
* Ghostery

拡張機能を無効にするか、別のブラウザーを使用します。

## <a name="a-namee-aa-unexpected-error"></a><a name="e-a"></a> 「予期しないエラー」
![予期しないエラーの画面](./media/app-insights-analytics-troubleshooting/010.png)

ポータルの実行時に内部エラー (未処理の例外) が発生しました。

* ブラウザーのキャッシュを削除します。 

## <a name="a-namee-ba403--please-try-to-reload"></a><a name="e-b"></a>403 ... 再読み込みしてください
![403 ... 再読み込みしてください](./media/app-insights-analytics-troubleshooting/020.png)

認証に関連するエラーが (認証中またはアクセス トークンの生成中に) 発生しました。 ポータルでは、ブラウザーの設定を変更しないと回復できない可能性があります。

* ブラウザーで [サード パーティの Cookie が有効](#cookies) になっていることを確認します。 

## <a name="a-nameauthenticationa403--verify-security-zone"></a><a name="authentication"></a>403 ... セキュリティ ゾーンを確認します
![403 ... セキュリティ ゾーンを確認します](./media/app-insights-analytics-troubleshooting/030.png)

認証に関連するエラーが (認証中またはアクセス トークンの生成中に) 発生しました。 ポータルでは、ブラウザーの設定を変更しないと回復できない可能性があります。

1. ブラウザーで [サード パーティの Cookie が有効](#cookies) になっていることを確認します。 
2. お気に入り、ブックマーク、または保存したリンクを使用して Analytics ポータルを開きましたか? リンクを保存したときに使用した資格情報とは異なる資格情報でサインインしていますか?
3. (このようなウィンドウをすべて閉じてから) InPrivate または Incognito のブラウザー ウィンドウを使用してみてください。 資格情報を指定する必要があります。 
4. 別の (通常の) ブラウザー ウィンドウを開いて [Azure](https://portal.azure.com)に移動します。 サインアウトします。 次に、リンクを開き、適切な資格情報を使用してサインインします。
5. Microsoft Edge および Internet Explorerのユーザーも、信頼済みゾーンの設定がサポートされていないと、このエラーが表示される場合があります。
   
    [Analytics ポータル](https://analytics.applicationinsights.io)と [Azure Active Directory ポータル](https://portal.azure.com)の両方が同じセキュリティ ゾーン内にあることを確認します。
   
   * Internet Explorer で、**[インターネット オプション]**、**[セキュリティ]**、**[信頼済みサイト]**、**[サイト]** の順に開きます。
     
     ![信頼済みサイトにサイトを追加するときの [インターネット オプション] ダイアログ](./media/app-insights-analytics-troubleshooting/033.png)
     
     Web サイトの一覧で、次の URL のいずれかが含まれている場合、他の URL も追加します。
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="a-namee-da404--resource-not-found"></a><a name="e-d"></a>404 ...リソースが見つかりません
![404 ... リソースが見つかりません](./media/app-insights-analytics-troubleshooting/040.png)

アプリケーションのリソースが Application Insights から削除されており、使用できません。 これは、Analytics ページへの URL を保存していると発生する場合があります。

## <a name="a-namee-ea403--no-authorization"></a><a name="e-e"></a>403 ...アクセス権限がありません
![403 ... アクセス権限がありません](./media/app-insights-analytics-troubleshooting/050.png)

Analytics でこのアプリケーションを開くためのアクセス許可がありません。

* 他のユーザーからリンクを取得しましたか? 自分が [このリソース グループの閲覧者または投稿者](app-insights-resources-roles-access-control.md)に入っていることを確認するようにそのユーザーに依頼してください。
* 別の資格情報を使用してリンクを保存しましたか? [Azure ポータル](https://portal.azure.com)を開いてサインアウトしてから、適切な資格情報を指定して、もう一度このリンクを試してください。

## <a name="a-namehtml-storagea403--html5-storage"></a><a name="html-storage"></a>403 ...HTML5 のストレージ
当社のポータルは、HTML5 localStorage および sessionStorage を使用します。

* Chrome: 設定、プライバシー、コンテンツの設定
* Internet Explorer: インターネット オプション、詳細設定タブ、セキュリティ、DOM ストレージを有効にする

![403 ... HTML5 のストレージを有効にしてください](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="a-namee-ga404--subscription-not-found"></a><a name="e-g"></a>404 ...サブスクリプションが見つかりません
![404 ...サブスクリプションが見つかりません](./media/app-insights-analytics-troubleshooting/070.png)

URL が無効です。 

* [Application Insights ポータル](https://portal.azure.com)でアプリのリソースを開きます。 次に、[分析] ボタンを使用します。

## <a name="a-namee-ha404--page-doesnt-exist"></a><a name="e-h"></a>404 ...ページが存在しません
![404 ...ページが存在しません](./media/app-insights-analytics-troubleshooting/080.png)

URL が無効です。

* [Application Insights ポータル](https://portal.azure.com)でアプリのリソースを開きます。 次に、[分析] ボタンを使用します。

## <a name="a-namecookiesaenable-third-party-cookies"></a><a name="cookies"></a>サード パーティの Cookie の有効化
  「 [How to disable third party cookies](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers)」 (サード パーティの Cookie を無効にする方法) を参照し、サード パーティの Cookie を **有効** にする必要があります。

## <a name="a-namee-xaif-all-else-fails"></a><a name="e-x"></a>すべてうまくいかなかった場合
[お問い合わせください](app-insights-get-dev-support.md)。

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]




<!--HONumber=Jan17_HO4-->


