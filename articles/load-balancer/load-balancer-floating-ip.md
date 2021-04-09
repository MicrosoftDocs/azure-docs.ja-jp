---
title: Azure Load Balancer のフローティング IP の構成
description: Azure Load Balancer のフローティング IP の概要
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94699408"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer のフローティング IP の構成

ロード バランサーは、UDP と TCP の両方のアプリケーションに対していくつかの機能を提供します。

## <a name="floating-ip"></a>フローティング IP

一部のアプリケーション シナリオでは、バックエンド プール内の 1 つの VM で複数のアプリケーション インスタンスによって同じポートが使用されることを選択するまたは使用されることが求められる場合があります。 ポートを再利用する一般的な例としては、高可用性のためにクラスタリングする、ネットワークの仮想アプライアンス、再暗号化なしに複数の TLS エンドポイントを公開する場合などが挙げられます。 複数の規則でバックエンド ポートを再利用するには、規則の定義で Floating IP を有効にする必要があります。

"**Floating IP**" は、Direct Server Return (DSR) と呼ばれるものの一部に対する Azure の用語です。 DSR は次の 2 つの部分から構成されます。

- フロー トポロジ
- IP アドレスのマッピング スキーム

プラットフォーム レベルでは、Azure Load Balancer は Floating IP が有効かどうかに関係なく、常に DSR フロー トポロジで動作します。 これは、フローの送信部分は常に起点に直接フローするように正しく書き換えられることを意味します。
Floating IP なしでは、Azure は従来の負荷分散 IP アドレスのマッピング スキームを、簡単に使用できるように公開します (VM インスタンスの IP)。 Floating IP を有効にすると、IP アドレス マッピングがロード バランサーのフロントエンド IP に変更され、柔軟性が上がります。 [こちら](load-balancer-multivip-overview.md)をご覧ください。

## <a name="limitations"></a><a name = "limitations"></a>制限事項

- フローティング IP は現在、負荷分散シナリオのセカンダリ IP 構成ではサポートされていません

## <a name="next-steps"></a>次のステップ

- Load Balancer の使用を開始するには、[パブリック Standard Load Balancer の作成](quickstart-load-balancer-standard-public-portal.md)に関するページを参照してください。
- [Azure Load Balancer のアウトバウンド接続](load-balancer-outbound-connections.md)について学習する。
- [Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
- [正常性プローブ](load-balancer-custom-probe-overview.md)について学習する。
- [Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)について学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)の詳細を確認する。