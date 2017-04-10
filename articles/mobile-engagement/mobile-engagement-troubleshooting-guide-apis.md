---
title: "Azure モバイル エンゲージメント関連のトラブルシューティング ガイド- APIs"
description: "Azure Mobile Engagement 関連のトラブルシューティング ガイド - API"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a7ae0a83046f2d67b790f672dcd3ae261987357a


---
# <a name="troubleshooting-guide-for-api-issues"></a>API の問題のトラブルシューティング ガイド
次に説明するのは、管理者が、API を使用して Azure モバイル エンゲージメントを操作する方法に関して発生する可能性のある問題です。

## <a name="syntax-issues"></a>構文の問題
### <a name="issue"></a>問題
* 構文エラーは API を使用して (または予期しない動作によって) 発生します。

### <a name="causes"></a>原因
* 構文の問題:
  * 使用している特定の API の構文を調べて、オプションを使用できることをご確認ください。
  * API の使用方法に関する一般的な問題は、リーチ API とプッシュ API を混同することです (多くのタスクは、プッシュ API の代わりにリーチ API を使用して実行する必要があります)。 
  * もう 1 つの SDK の統合と API の使用方法に関する一般的な問題は、SDK キーと API キーを混同することです。
  * API に接続するスクリプトは、少なくとも 10 分ごとにデータを送信する必要があります。これを行わないと、接続はタイムアウトします (特に、データを待機している監視 API では一般的です)。 タイムアウトを回避するために、スクリプトは 10 分ごとに XMPP ping を送信してサーバーとのセッションを維持します。

### <a name="see-also"></a>関連項目
* [API ドキュメント][Link 4]
* [XMPP プロトコル情報](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Azure モバイル エンゲージメント UI で使用できるアクションと同じアクションを、API を使用して実行できません
### <a name="issue"></a>問題
* Azure モバイル エンゲージメント UI から実行するアクションは、関連する Azure モバイル エンゲージメント API では機能しません。

### <a name="causes"></a>原因
* Azure モバイル エンゲージメント UI から同じアクションを実行できることを確認すると、Azure モバイル エンゲージメントのこの機能と SDK が正しく統合されていることが分かります。

### <a name="see-also"></a>関連項目
* [UI ドキュメント][Link 1]

## <a name="error-messages"></a>エラー メッセージ
### <a name="issue"></a>問題
* エラー コードは、実行時またはログ中に表示される API を使用します。

### <a name="causes"></a>原因
* 次に、参照と事前のトラブルシューティングのために、一般的な API 状態コード番号の一覧を示します。
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>関連項目
* [API ドキュメント - 特定の API に関する詳細なエラー情報][Link 4]

## <a name="silent-failures"></a>サイレント障害
### <a name="issue"></a>問題
* API のアクションは、実行時またはログ中にエラー メッセージが表示されずに失敗します。

### <a name="causes"></a>原因
* 正しく統合されていない場合は、Azure モバイル エンゲージメント UI の多くの項目が無効になりますが、API からエラー メッセージが表示されません。実行していることを確認するには、UI から同じ機能をテストしてください。
* Azure モバイル エンゲージメントと、使用する Azure モバイル エンゲージメントの多くの高度な機能を、事前に別の手順で SDK を使用して個別にアプリと統合する必要があります。

### <a name="see-also"></a>関連項目
* [トラブルシューティング ガイド - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md




<!--HONumber=Dec16_HO2-->


