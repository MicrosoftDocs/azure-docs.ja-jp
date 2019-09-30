---
title: Azure Backup のセキュリティ コントロール
description: Azure Backup を評価するためのセキュリティ コントロールのチェックリスト
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: f44be7556b6d741df93faeeab1dbdfc15bc8ebfd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211806"
---
# <a name="security-controls-for-azure-backup"></a>Azure Backup のセキュリティ コントロール

この記事では、Azure Backup に組み込まれているセキュリティ コントロールについて説明します。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ | ドキュメント
|---|---|--|
| サービス エンドポイントのサポート| いいえ |  |  |
| VNet インジェクションのサポート| いいえ |  |  |
| ネットワークの分離とファイアウォールのサポート| はい | VM バックアップでは、強制トンネリングがサポートされます。 VM 内で実行されるワークロードでは、強制トンネリングはサポートされません。 |  |
| 強制トンネリングのサポート| いいえ |  |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ| | ドキュメント
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics は、診断ログを通じてサポートされます。 詳細については、[Log Analytics を使用した Azure Backup で保護されているワークロードの監視](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)に関するページを参照してください。 |  |
| コントロールと管理プレーンのログ記録と監査| はい | Azure Portal からカスタマーがトリガーしたすべてのアクションがアクティビティ ログに記録されます。 |  |
| データ プレーンのログ記録と監査| いいえ | Azure Backup データ プレーンには、直接アクセスできません。  |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ| | ドキュメント
|---|---|--|
| 認証| はい | 認証は Azure Active Directory を介して行われます。 |  |
| Authorization| はい | カスタマーが作成した RBAC ロールとビルトインの RBAC ロールが使用されます。 詳細については、[ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](/azure/backup/backup-rbac-rs-vault)に関するページを参照してください。 |  |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ | | ドキュメント
|---|---|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー | はい | ストレージ アカウントに対して Storage Service Encryption を使用します。 |  |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ |  |  |
| 列レベルの暗号化 (Azure Data Services)| いいえ |  |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| いいえ | HTTPS を使用します。 |  |
| API 呼び出しの暗号化| はい |  |  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ| | ドキュメント
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい|  |  |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。