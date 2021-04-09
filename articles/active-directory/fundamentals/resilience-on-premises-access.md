---
title: アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する
description: オンプレミスのアプリケーションへの回復性のあるアクセスのためにアプリケーション プロキシを使用することに関するアーキテクトと IT 管理者向けのガイド
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724662"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する

アプリケーション プロキシとは、ユーザーがリモート クライアントからオンプレミス Web アプリケーションにアクセスできるようにする Azure AD の機能です。 アプリケーション プロキシには、クラウド内のアプリケーション プロキシ サービスと、オンプレミスのサーバーで実行されるアプリケーション プロキシ コネクタの両方が含まれています。 

ユーザーは、アプリケーション プロキシ経由で公開された URL を使用して、オンプレミスのリソースにアクセスします。 Azure AD サインイン ページにリダイレクトされます。 次に、Azure AD のアプリケーション プロキシ サービスは、企業ネットワーク内のアプリケーション プロキシ コネクタにトークンを送信します。これにより、トークンがオンプレミスの Active Directory に渡され、認証されたユーザーはオンプレミスのリソースにアクセスできるようになります。 次の図では、[コネクタ](../manage-apps/application-proxy-connectors.md)は[コネクタ グループ](../manage-apps/application-proxy-connector-groups.md)内に示されています。

> [!IMPORTANT]
> アプリケーション プロキシ経由でアプリケーションを公開する場合は、[アプリケーション プロキシ コネクタのキャパシティ プランニングと適切な冗長性](../manage-apps/application-proxy-connectors.md#capacity-planning)を実装する必要があります。

![アプリケーション y のアーキテクチャ図](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>アプリケーション プロキシの実装方法

Azure AD アプリケーション プロキシを使用したリモート アクセスを実装するには、次のリソースを参照してください。

* [アプリケーション プロキシのデプロイ計画](../manage-apps/application-proxy-deployment-plan.md)

* [高可用性と負荷分散のベスト プラクティス](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [プロキシ サーバーを構成する](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [回復性のあるアクセス制御戦略を設計する](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>次のステップ
管理者とアーキテクト向けの回復性に関するリソース
 
* [資格情報管理を使用して回復性を強化する](resilience-in-credentials.md)

* [デバイスの状態を使用して回復性を強化する](resilience-with-device-states.md)

* [継続的アクセス評価 (CAE) を使用して回復性を強化する](resilience-with-continuous-access-evaluation.md)

* [外部ユーザー認証の回復性を強化する](resilience-b2b-authentication.md)

* [ハイブリッド認証で回復性を強化する](resilience-in-hybrid.md)

開発者向けの回復性に関するリソース

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [CIAM システムで回復性を強化する](resilience-b2c.md)