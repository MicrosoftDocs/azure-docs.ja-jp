---
title: App Service Mobile Apps との統合
description: Azure Notification Hubs と Azure App Service Mobile Apps がどのように連携するかについて説明します。
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d6747193b8c82119e45a24e3e4bffc065db14e51
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212162"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps との統合

> [!NOTE]
> Microsoft は、最新の OS リリースのサポート、バグの修正、ドキュメントの改善、コミュニティの PR レビューなど、Azure App Service Mobile Apps を完全にサポートするために尽力しています。 製品チームは現在 Azure Mobile Apps 向けの新機能に投資していないことに注意してください。 Azure Mobile Apps のすべての領域に対するコミュニティの協力に非常に感謝しています。

Azure のサービス間でシームレスかつ同一のエクスペリエンスを実現するために、 [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) には Notification Hubs を使用したプッシュ通知のサポートが組み込まれています。 [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) は、拡張性が高くグローバルに使用可能な、企業の開発者やシステム インテグレーター向けのモバイル アプリ開発プラットフォームで、モバイル開発者に豊富な機能のセットを提供します。

Mobile Apps の開発者は、次のワークフローで Notification Hubs を使用します。

1. デバイスの PNS ハンドルを取得する
2. 便利な Mobile Apps クライアント SDK 登録 API を介して Notification Hubs でデバイスを登録する

    > [!NOTE]
    > Mobile Apps ではセキュリティの目的で登録時にすべてのタグを除去することに注意してください。 タグをデバイスに関連付けるには、Notification Hubs をバックエンドから直接操作します。

3. Notification Hubs を使用してアプリのバックエンドから通知を送信する

この統合は、開発者にいくつかの便利な機能を提供します。

- **Mobile Apps クライアント SDK**:  これらの複数プラットフォームの SDK は登録用のシンプルな API を提供し、モバイル アプリに自動的にリンクされている通知ハブと対話します。 開発者は Notification Hubs の資格情報を掘り下げて追加のサービスを操作する必要はありません。
  - *ユーザーへのプッシュ*: SDK は自動的に指定のデバイスを Mobile Apps の認証済みユーザー ID にタグ付けしてユーザー シナリオへのプッシュを有効にします。
  - *デバイスへのプッシュ*: SDK は自動的に Mobile Apps のインストール ID を Notification Hubs に登録する GUID として使用し、開発者が複数サービスの GUID を維持する手間を省きます。
- **インストール モデル**:  Mobile Apps は Notification Hubs の最新のプッシュ モデルと簡単に連携し、プッシュ通知サービスと連携する JSON インストール内のデバイスに関連付けられたすべてのプッシュ プロパティを表します。
- **柔軟性**:  開発者は統合中でも常に Notification Hubs を直接操作することを選択できます。
- **[Azure portal](https://portal.azure.com)の統合されたエクスペリエンス**:  機能としてのプッシュが Mobile Apps に視覚的に表示され、開発者は Mobile Apps を通じて関連付けられた通知ハブを簡単に操作できます。
