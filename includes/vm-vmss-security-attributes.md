---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 520fd50b2b0864f43c08687f05de377679b36d84
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886920"
---
## <a name="preventative"></a>予防

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | 「[Azure で Linux 仮想マシンを暗号化する方法](/azure/virtual-machines/linux/encrypt-disks)」および「[Windows VM の仮想ディスクを暗号化する](/azure/virtual-machines/windows/encrypt-disks)」を参照してください。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Virtual Machines では、[ExpressRoute](/azure/expressroute) と VNet の暗号化がサポートされています。 「[VM での転送中の暗号化](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | カスタマー マネージド キーは、サポートされている Azure の暗号化シナリオです。「[Azure の暗号化の概要](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS および SSL 経由。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | |
| VNet インジェクションのサポート| はい | 。 |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| はい | 「[Azure Resource Manager デプロイ モデルを使用した強制トンネリングの構成](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)」を参照してください。 |

## <a name="detection"></a>検出

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[Azure で Linux 仮想マシンの監視と更新を行う](/azure/virtual-machines/linux/tutorial-monitoring)」および「[Azure で Windows 仮想マシンの監視と更新を行う](/azure/virtual-machines/windows/tutorial-monitoring)」を参照してください。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい |  |
| Authorization| はい |  |


## <a name="audit-trail"></a>監査証跡

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査 | いいえ |  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  | 
