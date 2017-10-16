---
title: "Azure Mobile Engagement の Android SDK の統合"
description: "Android アプリで Azure Mobile Engagement SDK を統合する方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Azure Mobile Engagement の Android SDK の統合
> [!div class="op_single_selector"]
> * [ユニバーサル Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

このドキュメントでは、Azure Mobile Engagement Android SDK で使用できる統合と構成のすべてのオプションについて説明します。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>高度な機能
### <a name="reporting-features"></a>レポート機能
次の機能を追加できます。

1. [詳細なレポート オプション](mobile-engagement-android-advanced-reporting.md)
2. [場所レポート オプション](mobile-engagement-android-location-reporting.md)
3. [詳細な構成オプション](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>通知:
[Engagement Reach を Android に統合する方法](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [GCM を Mobile Engagement に統合する方法](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [ADM を Mobile Engagement に統合する方法](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>タグ付けプランの実装:
[Engagement API を Android で使用する方法](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>リリース ノート

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* `EngagementAgentUtils.isInDedicatedEngagementProcess` を呼び出すときに稀に発生するクラッシュを修正します。これは、`EngagementApplication` クラスでも使用されます。

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Android 8 のサポート (以前のバージョンの SDK は Android 8 では動作しません)。
* サポート ライブラリの依存関係がなくなりました。
* `EngagementFragmentActivity` クラスが削除されました。
* Android 8 における[バックグラウンドでの実行の制限](https://developer.android.com/preview/features/background.html)のため、ユーザーがデバイスを操作するまで、バック グラウンドのログが遅延する可能性があります。このため、デバイスがスリープ状態の場合は、プッシュ キャンペーンの**配信**や**システム通知**の表示に関する統計情報の遅延に影響が生じます (通知に伴う画面表示や着信音、バイブレーションなどの処理は、リアルタイムで問題なく行われます)。
* [バックグラウンドでの位置情報の制限](https://developer.android.com/preview/features/background-location-limits.html)のため、バックグラウンドでのリアルタイムの位置情報は、Android 8 では頻繁には更新されません。

すべてのバージョンについては、 [完全なリリース ノート](mobile-engagement-android-release-notes.md)をご覧ください。

## <a name="upgrade-procedures"></a>アップグレードの手順
SDK の旧バージョンをアプリケーションに既に統合している場合、 [アップグレード手順](mobile-engagement-android-upgrade-procedure.md)を参照してください。

