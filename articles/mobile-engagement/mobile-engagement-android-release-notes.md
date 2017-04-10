---
title: "Azure Mobile Engagement Android SDK の統合"
description: "Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af776804affea5abe599594d7b486bbead982f3e
ms.lasthandoff: 11/17/2016


---
# <a name="release-notes"></a>リリース ノート
## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* WiFi ロックを廃止しました。
* Init の前に getDeviceId を呼び出すときのデッドロック (4.2.0 でのバグ) を修正しました。

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* 安定性が向上しました。

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* セキュリティ: Web ビューのローカル ファイル アクセスが無効になりました。
* セキュリティ: 廃止され、セキュリティで保護されていない `PreferenceActivity` クラスを拡張する `EngagementPreferenceActivity` クラスを削除しました。
* セキュリティ: リーチ アクティビティが `exported="false"` を使用するように記述されました。このフラグは、以前のバージョンの SDK でも使用できます。

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* SDK が MIT でライセンスされるようになりました。
* SDK の初期化時にカスタム デバイス識別子を指定できます。

## <a name="415-02012016"></a>4.1.5 (02/01/2016)
* 安定性が向上しました。

## <a name="414-01262016"></a>4.1.4 (01/26/2016)
* 安定性が向上しました。

## <a name="413-1292015"></a>4.1.3 (12/9/2015)
* 安定性が向上しました。

## <a name="412-11252015"></a>4.1.2 (11/25/2015)
* 安定性が向上しました。

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* 安定性が向上しました。

## <a name="410-08252015"></a>4.1.0 (2015 年 8 月 25 日)
* Android M の新しいアクセス許可モデルを処理します。
* `AndroidManifest.xml` を使用する代わりに実行時に場所の機能を構成できるようになりました。
* アクセス許可のバグ修正: `ACCESS_FINE_LOCATION` を使用する場合、`ACCESS_COARSE_LOCATION` は必要なくなります。
* 安定性が向上しました。

## <a name="400-07062015"></a>4.0.0 (07/06/2015)
* 分析を行い、より確実にプッシュするように内部プロトコルを変更しました。
* ネイティブ プッシュ通知 (GCM/ADM) がアプリ内通知にも使用されるようになったので、すべての種類のプッシュ キャンペーンについてネイティブのプッシュの資格情報を設定する必要があります。
* 大きい画像による通知を修正しました。これらの画像は、プッシュ後の 10 秒間のみ表示されていました。
* Web 表示のバグ修正: リンクをクリックすると、既定のアクション URL も実行されていました。
* ローカル ストレージの管理に関連してまれに起きていたクラッシュを修正しました。
* 動的構成文字列の管理を修正しました。
* EULA を更新しました。

## <a name="300-02172015"></a>3.0.0 (02/17/2015)
* Azure Mobile Engagement の最初のリリース
* appId 構成が接続文字列構成に置き換わりました。
* 任意の XMPP エンティティから任意の XMPP メッセージを送受信する API が削除されました。
* デバイス間でメッセージを送受信する API が削除されました。
* セキュリティの強化。
* Google Play と SmartAd の追跡機能が削除されました。


