---
title: Azure Linux Virtual Machines のセキュリティ コントロール - Linux
description: Azure Linux Virtual Machines を評価するためのセキュリティ コントロールのチェックリスト
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 00753d885985e2734f0d87fdad9f219f44277d5a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828347"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Linux Virtual Machines のセキュリティ コントロール

この記事では、Linux Virtual Machines に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| はい | 「[Azure Resource Manager デプロイ モデルを使用した強制トンネリングの構成](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)」を参照してください。 |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure での Linux 仮想マシンの監視と更新](/azure/virtual-machines/linux/tutorial-monitoring)に関する記事を参照してください。 |
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査 | いいえ |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい |  |
| Authorization| はい |  |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | 「[Linux VM に対する Azure Disk Encryption](disk-encryption-overview.md)」を参照してください。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Virtual Machines では、[ExpressRoute](/azure/expressroute) と VNet の暗号化がサポートされています。 「[VM での転送中の暗号化](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい | カスタマー マネージド キーは、サポートされている Azure の暗号化シナリオです。「[Azure の暗号化の概要](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS および SSL 経由。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  | 

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../../security/fundamentals/security-controls.md)について学習します。
