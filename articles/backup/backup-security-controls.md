---
title: セキュリティ コントロール
description: Azure Backup サービスで使用されるセキュリティ コントロールについて説明します。 これらのコントロールは、このサービスがセキュリティ脆弱性の防止、検出、対応を行う助けとなります。
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: ce1ca0a79476e4985569c677b58892fad91866a5
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892964"
---
# <a name="security-controls-for-azure-backup"></a>Azure Backup のセキュリティ コントロール

この記事では、Azure Backup に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント
|---|---|--|--|
| サービス エンドポイントのサポート| いいえ |  |  |
| VNet インジェクションのサポート| いいえ |  |  |
| ネットワークの分離とファイアウォールのサポート| はい | VM バックアップでは、強制トンネリングがサポートされます。 VM 内で実行されるワークロードでは、強制トンネリングはサポートされません。 |  |
| 強制トンネリングのサポート| いいえ |  |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント
|---|---|--|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics は、リソース ログを通じてサポートされます。 詳細については、[Azure Backup で保護されているワークロードの、Log Analytics を使用した監視](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)に関するページを参照してください。 |  |
| コントロールと管理プレーンのログ記録と監査| はい | Azure Portal からカスタマーがトリガーしたすべてのアクションがアクティビティ ログに記録されます。 |  |
| データ プレーンのログ記録と監査| いいえ | Azure Backup データ プレーンには、直接アクセスできません。  |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント
|---|---|--|--|
| 認証| はい | 認証は Azure Active Directory を介して行われます。 |  |
| 承認| はい | カスタマーが作成したロールと Azure 組み込みロールが使用されます。 詳細については、[ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](./backup-rbac-rs-vault.md)に関するページを参照してください。 |  |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント
|---|---|--|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | ストレージ アカウントに対して Storage Service Encryption を使用します。 |  |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ |  |  |
| 列レベルの暗号化 (Azure Data Services)| いいえ |  |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| いいえ | HTTPS を使用します。 |  |
| API 呼び出しの暗号化| はい |  |  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント
|---|---|--|--|
| 構成管理のサポート (構成のバージョン管理など)| はい|  |  |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。
