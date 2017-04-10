---
title: "Azure モバイル エンゲージメント関連のトラブルシューティング ガイド - SDK"
description: "Azure のモバイル サービスで SDK の統合に関する問題のトラブルシューティング"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 46a86bf99c1afd09ae3921a205d27532246171c9
ms.lasthandoff: 12/08/2016


---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>SDK の統合の問題のトラブルシューティング ガイド
次に説明するのは、Azure モバイル エンゲージメントをアプリケーションに統合する方法に関して発生する可能性のある問題です。

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>アプリケーションの別領域の障害で検出された SDK に関する問題
### <a name="issue"></a>問題
* UI データ収集エラー (分析、監視、セグメント化、ダッシュ ボード)。
* プッシュ エラー (プッシュはアプリ内、アプリ外、または両方で機能しません)。
* 高度な機能のエラー (追跡、位置情報、プラットフォーム固有のプッシュが機能しません)。
* API エラー (多くの場合、API はエラー メッセージを表示せずに失敗します)。
* サービス エラー (Azure モバイル エンゲージメントでアプリケーションを使用できません)。

### <a name="causes"></a>原因
* Azure モバイル エンゲージメント SDK で解決する必要がある多くの問題が、アプリケーションの障害で検出されます (UI データ収集エラー、プッシュ エラー、高度な機能のエラー、API エラー、アプリケーション クラッシュ、見かけ上のサービス停止など)。  
* Azure モバイル エンゲージメントの特定の機能を、以前にアプリで使用したことがない場合は、統合を完了する必要があります。 
* 動作していた Azure モバイル エンゲージメントの特定の機能が停止した場合は、最新バージョンの Azure モバイル エンゲージメント SDK にアップグレードする必要があります。 Azure Mobile Engagement でサポートされているプラットフォーム (Android、iOS、Windows、Windows Phone) によって、Azure モバイル エンゲージメント SDK のバージョンが異なりますので注意してください。

#### <a name="sdk-integration"></a>SDK の統合
* Azure モバイル エンゲージメントが SDK に正しく統合されていません (分析)。
* リーチが SDK に正しく統合されていません (In App プッシュと Out of App プッシュ)。
* 証明書の期限が切れているか、または本番用と開発用の証明書が正しくありません(iOS のみ)。
* GCM または ADM が SDK に正しく統合されていません (Android のみ - サービス固有のプッシュ)。
* 追跡が SDK に正しく統合されていません (ストア追跡のインストール)。
* 遅延位置または GPS 位置が SDK に正しく統合されていません (位置情報による対象化)。

**関連項目:**

* [SDK ドキュメント - 統合ガイド][Link 5] 
* [トラブルシューティング ガイド - プッシュ][Link 23]

#### <a name="sdk-upgrade"></a>SDK のアップグレード
* 古いバージョンの SDK に関する問題を解決するには、SDK をアップグレードする必要があります (多くの場合、新しいバージョンのデバイス OS に関連します)。
* デバイスが最新バージョンのアプリを使用していることを確認するには、以前のバージョンのアプリをデバイスからすべてアンインストールし、最新バージョンのアプリを再インストールし、Azure モバイル エンゲージメント UI からデバイス ID を再登録します。

**関連項目:**

* [SDK ドキュメント - リリース ノート](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [SDK ドキュメント - アップグレード ガイド](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK その他
* アプリケーション マニフェスト "AndroidManifest.xml" のエラーが原因で、Azure モバイル エンゲージメントが機能しなくなる可能性があります (Android のみ)。
* SDK の統合と API の使用方法に共通する問題は、SDK キーと API キーを混同することです。

**関連項目:**

* [概念 - 用語集][Link 6]

## <a name="advanced-coding-issues"></a>高度なコーディングの問題
### <a name="issue"></a>問題
* Azure モバイル エンゲージメントに直接関連しないプラットフォーム固有のコードが原因で、iOS、Android、Windows Phone で問題が発生する可能性があります。

### <a name="causes"></a>原因
* Azure モバイル エンゲージメントに関する多くの高度なコーディングの問題は、Azure モバイル エンゲージメントに直接関連しない不適切に記述されたプラットフォーム固有のコードが原因です。 Azure モバイル エンゲージメントのドキュメント (Android、iOS、Web、Windows、Windows Phone) だけでなく、開発しているプラットフォーム固有のドキュメントを参照する必要があります。
* 「カテゴリ」を正しく構成しないと、通知からアプリ内外の別の場所にリンクできません (Android のみ)。 
* iOS コードで、[UIKit.framework] を [optional (任意)] に設定しないと、[Symbol not found error (シンボルが見つかりませんでした)] というエラーが表示され、古い iOS デバイスがクラッシュします (iOS のみ)。
* 証明書の期限が切れているか、開発用または本番用の証明書を正しく使用していないと、プッシュ問題を引き起こします (iOS のみ)。
* Azure モバイル エンゲージメントが制御できない、プラットフォーム固有のいくつかの制限があります (システム センターが Android や iOS の Out of App プッシュを操作する方法など)。
* Azure モバイル エンゲージメントは、参照用に iOS と Android の Azure モバイル エンゲージメントで使用される内部パッケージの完全な一覧を発行します。 Azure モバイル エンゲージメントの一部の機能はプラットフォームに固有であることに注意してください (Android、iOS、Web、Windows、Windows Phone)。

### <a name="see-also"></a>関連項目
* [トラブルシューティング ガイド - プッシュ][Link 23] 
* [SDK ドキュメント - リリース ノート][Link 5]
* [SDK ドキュメント - アップグレード ガイド][Link 5]

## <a name="application-crashes"></a>アプリケーションのクラッシュ
### <a name="issue"></a>問題
* エンド ユーザーのデバイスで、アプリケーションがクラッシュします。

### <a name="causes"></a>原因
* クラッシュ情報は、"*分析 UI*" または "*分析 API*" で確認できます。
* エンドユーザーがクラッシュの原因を特定できるように、テスト デバイスのデバイス ID を検索し、アプリケーションがクラッシュする原因となった操作を同様に実行できます。
* アプリケーションをクラッシュさせた Azure モバイル エンゲージメント SDK の既知の問題は、最新バージョンの SDK にアップグレードすることで解決する場合があります。 クラッシュを調査するときは、プラットフォームのリリース ノートを必ず確認してください。

### <a name="see-also"></a>関連項目
* [SDK ドキュメント - リリース ノート][Link 5]
* [SDK ドキュメント - アップグレード ガイド][Link 5]

## <a name="app-store-upload-failures"></a>アプリ ストアのアップロード エラー
### <a name="issue"></a>問題
* 最新バージョンのアプリを Apple、Google、または Windows のアプリ ストアにアップロードする際にエラーが発生します。

### <a name="causes"></a>原因
* アプリ ストアは特定の機能が有効になっているアプリをブロックする場合があります (たとえば、Apple ストアではストア内のアプリでの IDFV の使用を禁止しており、GooglePlay ストアではアプリ間でのアプリケーション情報の共有を禁止しています)。 
* アプリをストアにアップロードできなかった場合は、プラットフォームと現在のバージョンの SDK のリリース ノートをご確認ください。

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
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


