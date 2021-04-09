---
title: App Service Mobile Apps との Notification Hubs の統合
description: Azure Notification Hubs と Azure App Service Mobile Apps がどのように連携するかについて説明します。
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004059"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps との統合

Azure のサービス間でシームレスかつ同一のエクスペリエンスを実現するために、[App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) には Azure Notification Hubs を使用した通知のサポートが組み込まれています。 [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) は、拡張性が高くグローバルに使用可能な、企業の開発者やシステム インテグレーター向けのモバイル アプリ開発プラットフォームで、モバイル開発者に豊富な機能のセットを提供します。

Mobile Apps の開発者は、次のワークフローで Notification Hubs を使用できます。

1. デバイスの PNS ハンドルを取得します。
2. Mobile Apps クライアント SDK 登録 API を使用して Notification Hubs でデバイスを登録します。

    > [!NOTE]
    > Mobile Apps ではセキュリティの目的で登録時にすべてのタグを除去することに注意してください。 タグをデバイスに関連付けるには、Notification Hubs をバックエンドから直接操作します。

3. Notification Hubs を使用してアプリのバックエンドから通知を送信します。

この統合によってもたらされるいくつかの利点を次に示します。

- **Mobile Apps クライアント SDK**: これらのマルチプラットフォーム SDK は、登録用の API を備え、モバイル アプリにリンクされている通知ハブと通信を行います。 Notification Hubs の資格情報は不要であり、追加のサービスを操作する必要もありません。
  - *ユーザーへのプッシュ*: この SDK は、指定のデバイスに Mobile Apps の認証済みユーザー ID を自動的にタグ付けすることで、"ユーザーへのプッシュ" シナリオを実現します。
  - *デバイスへのプッシュ*: この SDK は、Mobile Apps のインストール ID を GUID として使用して自動的に Notification Hubs への登録を行うので、複数のサービスの GUID を維持する必要はありません。
- **インストール モデル**: Mobile Apps は Notification Hubs の最新のプッシュ モデルと簡単に連携し、プッシュ通知サービスと連携する JSON インストール内のデバイスに関連付けられたすべてのプッシュ プロパティを表します。
- **柔軟性**: 開発者は統合中でも常に Notification Hubs を直接操作することを選択できます。
- **[Azure portal](https://portal.azure.com) の統合されたエクスペリエンス**: 機能としてのプッシュが Mobile Apps に視覚的に表示され、開発者は Mobile Apps を通じて関連付けられた通知ハブを簡単に操作できます。
