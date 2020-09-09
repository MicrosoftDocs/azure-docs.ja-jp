---
title: Azure Virtual Machine Scale Sets のセキュリティ コントロール
description: Azure Virtual Machine Scale Sets を評価するためのセキュリティ コントロールのチェックリスト
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029418"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets のセキュリティ コントロール

この記事では、Azure Virtual Machine Scale Sets に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| サービス エンドポイントのサポート| はい | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| はい | 「[Azure Resource Manager デプロイ モデルを使用した強制トンネリングの構成](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)」を参照してください。 |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[Azure で Linux 仮想マシンの監視と更新を行う](../virtual-machines/linux/tutorial-monitor.md)」および「[Azure で Windows 仮想マシンの監視と更新を行う](../virtual-machines/windows/tutorial-monitor.md)」を参照してください。 |
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査 | いいえ |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 認証| はい |  |
| 承認| はい |  |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | 「[仮想マシン スケール セットの Azure Disk Encryption](disk-encryption-overview.md)」を参照してください。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Virtual Machines では、[ExpressRoute](../expressroute/index.yml) と VNet の暗号化がサポートされています。 「[VM での転送中の暗号化](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)」を参照してください。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい | カスタマー マネージド キーは、サポートされている Azure の暗号化シナリオです。「[仮想マシン スケール セットの Azure Disk Encryption](disk-encryption-overview.md)」を参照してください|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS および TLS 経由。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  | 

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。
