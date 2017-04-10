---
title: "Azure Mobile Engagement iOS SDK のリリース ノート | Microsoft Docs"
description: "Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: da3ef06433d2336159d0ae96f12019fd80dc5d07
ms.openlocfilehash: 6b32e81bb6a714bd0c479c48d6982af4bcc0683d


---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Azure Mobile Engagement iOS SDK リリース ノート
## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* バックグラウンドでのログ配信が改善されました。

## <a name="400-09122016"></a>4.0.0 (2016 年&9; 月&12; 日)
* iOS 10 デバイスで処理されない通知を修正しました。
* XCode 7 が非推奨になりました。

## <a name="324-06302016"></a>3.2.4 (2016 年&6; 月&30; 日)
* 技術的なログと他のログとの間の集計を修正。

## <a name="323-06072016"></a>3.2.3 (2016 年&6; 月&7; 日)
* アプリがバックグラウンドのときに配信フィードバックが報告されないバグを修正。
* 技術的ログの送信を最適化。

## <a name="322-04072016"></a>3.2.2 (2016 年&4; 月&7; 日)
* クラッシュの原因となる場合がある HTTP 要求のキャンセルに関するバグを修正。

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* 新しいアプリケーション インスタンスがディープ リンクを持つ通知によってトリガーされたときの遅延を修正。

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* **Xcode 7**と連携できるように、SDK のビットコードを有効化。
* アプリ内通知に関連するバグを修正。
* バッテリ低下のような状況でのアプリ内通知の信頼性を向上。
* サード パーティ製のライブラリによって生成される余分なコンソール ログの削除。

## <a name="310-08262015"></a>3.1.0 (2015 年&8; 月&26; 日)
* サードパーティ ライブラリと iOS 9 の互換性バグを修正。 ポーリングの結果、アプリケーション情報、追加のデータの送信時にクラッシュを引き起こしていました。

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* Mobile Engagement では、サイレント プッシュ通知が使用されます。
* iOS 4.X のサポートが終了。 このバージョンから、アプリケーションのデプロイ ターゲットは iOS 6 以降である必要があります。

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* iOS 6 より前のデバイスでは、Mobile Engagement のデバイス ID がインストール時に生成された GUID に基づくようになりました。

## <a name="210-04242015"></a>2.1.0 (04/24/2015)
* Swift 互換性を追加しました。
* 通知をクリックすると、アプリケーションの起動直後にアクション URL が実行されるようになりました。
* SDK パッケージに足りないヘッダー ファイルを追加しました。
* Mobile Engagement のクラッシュ レポートが無効になる問題を修正しました。

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Azure Mobile Engagement の最初のリリース
* appId/sdkKey 構成は、接続文字列の構成で置き換えられます。
* 任意の XMPP エンティティから任意の XMPP メッセージを送受信する API が削除されました。
* デバイス間でメッセージを送受信する API が削除されました。
* セキュリティの強化。
* SmartAd の追跡が削除されました。




<!--HONumber=Dec16_HO2-->


