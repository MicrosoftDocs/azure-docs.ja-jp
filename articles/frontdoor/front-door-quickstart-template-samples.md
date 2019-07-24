---
title: Azure Resource Manager テンプレートのサンプル - Azure Front Door Service | Microsoft Docs
description: Azure Front Door Service のための Azure Resource Manager テンプレートのサンプル
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: 1d1f7eeb6f55fa19b94a1eb4d05520c76352a414
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756560"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Front Door のための Azure Resource Manager デプロイ モデル テンプレート

次の表は、Azure Front Door Service のための Azure Resource Manager デプロイ モデル テンプレートのリンク一覧です。 

| | |
| ---| ---|
| [基本的な Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| 1 つのバックエンドを備えた基本的な Front Door 構成を作成します。 |
| [複数のバック エンドおよびバックエンド プールと URL ベースのルーティングを備えた Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| 複数のバックエンドおよびバックエンド プール用に負荷分散が構成され、URL パスに基づくバックエンド プール間の Front Door を作成します。 |
| [Front Door による HTTPS (Front Door が管理する証明書) を備えたカスタム ドメインの配布準備](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Front Door にカスタム ドメインを追加し、DigiCert で生成された Front Door が管理する証明書を使用してこの HTTPS トラフィックを有効にします。 |
| [地域フィルタリングされた Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| 特定の国からのトラフィックの許可/ブロックする Front Door を作成します。 |
| [Front Door のバックエンドの正常性プローブのコントロール](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| プローブ パスおよびプローブ送信間隔を更新することによって、Front Door を更新し、正常性プローブの設定を変更します。 |
| [アクティブ/スタンバイ バックエンド構成を備えた Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| アクティブ/スタンバイ アプリケーション トポロジの優先順位ベースのルーティングをデモする Front Door を作成します。つまり、デフォルトではプライマリ (最高の優先順位) バックエンドが使用できなくなるまですべてのトラフィックをこのバックエンドに送信します。 |
| [特定のルートのキャッシュを有効にした Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| 定義済みのルーティング構成でキャッシュが有効になっている、つまりワークロードの任意の静的資産をキャッシュする Front Door を作成します。 |
| [Front Door のホスト名のセッション アフィニティの構成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Front Door を更新してフロントエンド ホストのセッション アフィニティを有効にすることによって、後続のトラフィックを同じユーザー セッションから同じバックエンドに送信します。 |
| [クライアント IP のホワイトリストまたはブラックリスト用の Front Door の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Front Door を構成して、クライアント IP を使用するカスタム アクセス コントロールを使用して特定のクライアント IP のトラフィックを制限します。 |
| [特定の http パラメーターでアクションを実行する Front Door の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Front Door を構成して、http パラメーターを使用するアクセス コントロールのカスタム ルールを使用して受信要求の http パラメーターに基づいて特定のトラフィックを許可またはブロックします。 |
| [Front Door レート制限の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Front Door を構成して、特定のフロントエンド ホストの受信トラフィックのレート制限を行います。 |
| | |

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。