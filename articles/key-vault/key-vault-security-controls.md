---
title: Azure Key Vault のセキュリティ コントロール
description: Azure Key Vault を評価するためのセキュリティ コントロールのチェックリスト
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886284"
---
# <a name="security-controls-for-azure-key-vault"></a>Azure Key Vault のセキュリティ コントロール

この記事では、Azure Key Vault に組み込まれているセキュリティ コントロールについて説明します。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | Virtual Network (VNet) サービス エンドポイントを使用。 |
| VNet インジェクションのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| はい | VNet ファイアウォール規則を使用。 |
| 強制トンネリングのサポート| いいえ |  |

## <a name="monitoring--logging"></a>監視とログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics を使用します。 |
| コントロール/管理プレーンのログ記録と監査| はい | Log Analytics を使用します。 |
| データ プレーンのログ記録と監査| はい | Log Analytics を使用します。 |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | Key Vault アクセス ポリシーを使用します。 |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | すべてのオブジェクトが暗号化されます。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい | ユーザーが Key Vault 内のすべてのキーを制御します。 ハードウェア セキュリティ モジュール (HSM) バックアップ キーが指定されている場合、FIPS レベル 2 HSM により、キー、証明書、またはシークレットが保護されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | すべての通信が暗号化された API 呼び出しを介して行われます。 |
| API 呼び出しの暗号化| はい | HTTPS を使用します。 |

## <a name="access-controls"></a>アクセス制御

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 制御/管理プレーンのアクセス制御 | はい | Azure Resource Manager のロールベースのアクセス制御 (RBAC) |
| データ プレーンのアクセス制御 (すべてのサービス レベル) | はい | Key Vault アクセス ポリシー |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。