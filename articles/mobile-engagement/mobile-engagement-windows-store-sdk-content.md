---
title: "Windows ユニバーサル アプリ SDK コンテンツ"
description: "Azure Mobile Engagement 向け Windows ユニバーサル アプリ SDK の内容詳細"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ea47cba55c9689e23eb3592ae2559649d3377055
ms.lasthandoff: 11/17/2016


---
# <a name="windows-universal-apps-sdk-content"></a>Windows ユニバーサル アプリ SDK コンテンツ
このドキュメントでは、アプリケーションで SDK により展開されるコンテンツについて説明します。

## <a name="the-resources-folder"></a>`/Resources` フォルダー
このフォルダーには、Mobile Engagement が必要とするすべてのリソースが含まれています。 アプリに適合するように、これらをカスタマイズすることもできます。

* `EngagementConfiguration.xml` : Mobile Engagement の構成ファイルであり、エンゲージメントの設定をカスタマイズできます (Mobile Engagement 接続文字列、クラッシュ レポートなど)。

### <a name="html-folder"></a>/html フォルダー
* `EngagementNotification.html`: アプリ内バナー用の `Notification` Web ビュー html デザイン。
* `EngagementAnnouncement.html`: アプリ内スポット ビュー用の `Announcement` Web ビュー html デザイン。

### <a name="images-folder"></a>/images フォルダー
* `EngagementIconNotification.png`: 通知の左側に表示されるブランド アイコン。これは自分のブランド アイコンに換えます。
* `EngagementIconOk.png` : Reach コンテンツ ページの [`Ok`] アイコン (アクション ボタンまたは検証ボタンとして使用)。
* `EngagementIconNOK.png` : Reach コンテンツ ページの検証ボタンが無効の場合に使用する [`NOK`] アイコン。
* `EngagementIconClose.png` : Reach 通知やコンテンツの [`Close`] アイコン (閉じるボタンとして使用)。

### <a name="overlay-folder"></a>/overlay フォルダー
* `EngagementPageOverlay.cs` : Engagement Reach アプリ内 UI を子に追加するためのオーバーレイ ページ。


