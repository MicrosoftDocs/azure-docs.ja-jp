---
title: Notification Hubs の TLS の更新
description: Azure Notification Hubs での TLS のサポートについて説明します。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084247"
---
# <a name="transport-layer-security-tls"></a>トランスポート層セキュリティ (TLS)

より高いレベルのセキュリティを確保するために、Notification Hubs では **2020 年 12 月 31 日** に TLS バージョン 1.0 および 1.1 のサポートを無効にします (2020 年 4 月 30 日から延期)。 これらの古いプロトコルは、脆弱な暗号化を行うため、BEAST 攻撃や POODLE 攻撃に対して脆弱です。 Android バージョン 5 以降、または iOS バージョン 5 以降を実行しているデバイスにデプロイされたアプリケーションは、この変更の影響を受けません。これらのオペレーティング システムでは TLS 1.2 がサポートされ、クライアントとサーバーは接続時に相互にサポートされる最新バージョンのプロトコルでネゴシエートします。

Azure Notification Hubs を使用するすべてのアプリケーションを確認して、TLS 1.2 をサポートする最適なライブラリと TLS スタックを確実に使用していることを確認します。

## <a name="update-apps"></a>アプリを更新する

App Transport Security (ATS) と呼ばれる Apple のネットワーク セキュリティ機能を使用して、iOS アプリが TLS 1.2 を使用していることを確認できます。 ATS は、iOS 9.0 または macOS 10.11 より前の SDK には使用できません。[Apple のドキュメント](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)から詳細を確認できます。

SSLSocket インスタンスを使用する Android アプリケーションの場合は、[setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)) に記載されているように、各 SSLSocket インスタンスで有効化されたプロトコルを設定します。

[TLS プロトコルの互換性](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility)サポート ページの表は、オペレーティング システムと互換性のある TLS バージョンのマッピングに役立ちます。

詳細については、[Windows での TLS プロトコルのサポート](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)の概要を参照してください。

## <a name="next-steps"></a>次のステップ

- [Notification Hubs の概要](notification-hubs-push-notification-overview.md)
