---
title: "Azure Mobile Engagement の Windows Phone Silverlight SDK 概要 | Microsoft Docs"
description: "Azure Mobile Engagement の Windows Phone Silverlight SDK の概要"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 51858d6daca27a75e7f87af507600c7a193c874f
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22
ms.lasthandoff: 02/09/2017


---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Azure Mobile Engagement の Windows Phone Silverlight SDK 概要
ここから Azure Mobile Engagement を Windows Phone Silverlight アプリに統合する方法についての説明を開始します。 まず試してみる場合は、「 [15 分間チュートリアル](mobile-engagement-windows-phone-get-started.md)」を完了してください。

[SDK コンテンツ](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>統合手順
1. ここから開始: [Windows Phone Silverlight アプリに Mobile Engagement を統合する方法](mobile-engagement-windows-phone-integrate-engagement.md)
2. 通知: [リーチ (通知) を Windows Phone Silverlight アプリに統合する方法](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. タグ付けプランの実装: [Windows Phone Silverlight で高度な Mobile Engagement タグ付け API を使用する方法](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>リリース ノート
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
*MicrosoftAzure.MobileEngagement* NuGet パッケージ **v3.4.1** の一部

* 安定性が向上しました。

以前のバージョンについては、「 [完全リリース ノート](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>アップグレードの手順
既に古いバージョンの SDK をアプリケーションに統合している場合は、SDK をアップグレードする際に次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。 完全な [アップグレード手順](mobile-engagement-windows-phone-upgrade-procedure.md)を参照してください。 たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

### <a name="from-200-to-330"></a>2.0.0 から 3.3.0 に移行
#### <a name="test-logs"></a>テスト ログ
SDK によって生成されるコンソール ログを有効化/無効化/フィルター処理できるようになりました。 これをカスタマイズするには、次の例のように `EngagementAgent.Instance.TestLogEnabled` プロパティを `EngagementTestLogLevel` 列挙型の使用可能な値の&1; つに更新します。

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>古いバージョンからのアップグレード
[Upgrade Procedures](mobile-engagement-windows-phone-upgrade-procedure.md)


