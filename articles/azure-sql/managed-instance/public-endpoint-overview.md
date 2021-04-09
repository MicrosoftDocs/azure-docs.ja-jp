---
title: Azure SQL Managed Instance のパブリック エンドポイントを保護する
description: マネージド インスタンスを使用して Azure SQL Managed Instance のパブリック エンドポイントを安全に使用する
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: d9c1828732b9a4e0e85c3af2263f097edd54437d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91332850"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>パブリック エンドポイントで安全に Azure SQL Managed Instance を使用する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance は、[パブリック エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)経由でユーザーに接続性を提供できます。 この記事では、この構成をより安全にする方法について説明します。

## <a name="scenarios"></a>シナリオ

Azure SQL Managed Instance では、その仮想ネットワーク内から接続できるようにするために、プライベート エンドポイントが提供されます。 最大の分離を提供するのが既定のオプションです。 ただし、パブリック エンドポイントの接続を提供する必要があるシナリオがいくつかあります。

- マネージド インスタンスを、マルチテナント専用のサービスとしてのプラットフォーム (PaaS) 製品と統合する必要がある。
- VPN を使用している場合よりも高いデータ交換スループットが必要である。
- 会社のポリシーで企業ネットワーク内の PaaS が禁止されている。

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>パブリック エンドポイント アクセスに対してマネージド インスタンスをデプロイする

必須ではありませんが、パブリック エンドポイント アクセスを使ったマネージド インスタンス用の共通デプロイ モデルは、専用の分離された仮想ネットワークでインスタンスを作成するためのものです。 この構成では、仮想ネットワークは仮想クラスターの分離のためだけに使用されます。 マネージド インスタンスの IP アドレス空間が企業ネットワークの IP アドレス空間と重複していても、問題ありません。

## <a name="secure-data-in-motion"></a>移動中のデータをセキュリティで保護する

クライアント ドライバーで暗号化をサポートする場合、SQL Managed Instance のデータ トラフィックは常に暗号化されます。 マネージド インスタンスとその他の Azure 仮想マシンまたは Azure サービスとの間で送信されるデータは、Azure のバックボーンから移動されることはありません。 マネージド インスタンスとオンプレミス ネットワーク間の接続がある場合は、Azure ExpressRoute を使用することをお勧めします。 ExpressRoute を使用すると、パブリック インターネット経由でのデータの移動を防ぐことができます。 マネージド インスタンス プライベート接続の場合、プライベート ピアリングのみを使用できます。

## <a name="lock-down-inbound-and-outbound-connectivity"></a>インバウンド接続とアウトバウンド接続をロック ダウンする

次の図は、推奨されるセキュリティ構成を示しています。

![インバウンド接続とアウトバウンド接続をロック ダウンするためのセキュリティ構成](./media/public-endpoint-overview/managed-instance-vnet.png)

マネージド インスタンスには、[専用パブリック エンドポイント アドレス](management-endpoint-find-ip-address.md)があります。 クライアント側の送信ファイアウォールとネットワーク セキュリティ グループの規則で、このパブリック エンドポイント IP アドレスを、送信接続を制限するように設定します。

マネージド インスタンスへのトラフィックが、信頼できる送信元から送られてくるようにするには、既知の IP アドレスを持つ送信元と接続することをお勧めします。 ネットワーク セキュリティ グループを使用して、ポート 3342 でのマネージド インスタンス パブリック エンドポイントへのアクセスを制限します。

クライアントがオンプレミスのネットワークから接続を開始する必要がある場合は、送信元アドレスが既知の IP アドレスのセットに変換されるようにします。 そうできない場合 (たとえば、モバイル従業員が一般的なシナリオである場合) は、[ポイント対サイト VPN 接続とプライベート エンドポイント](point-to-site-p2s-configure.md)を使用することをお勧めします。

接続が Azure から開始される場合、トラフィックは既知で割り当て済みの[仮想 IP アドレス](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) (たとえば、仮想マシン) からのものであることが推奨されます。 仮想 IP (VIP) アドレスの管理を容易にするために、[パブリック IP アドレス プレフィックス](../../virtual-network/public-ip-address-prefix.md)を使用したい場合があります。

## <a name="next-steps"></a>次のステップ

- マネージド インスタンスのパブリック エンドポイントの構成方法について学習します。[パブリック エンドポイントの構成](public-endpoint-configure.md)
