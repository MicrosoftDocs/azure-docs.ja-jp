---
title: Azure Backup の一般的なセキュリティ属性
description: Azure Backup を評価するための一般的なセキュリティ属性のチェックリスト
services: backup
author: utraghuv
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 595cc4eff70e2df4cb6d7f1d6a0c1a2748b34bf2
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565635"
---
# <a name="security-attributes-for-azure-backup"></a>Azure Backup のセキュリティ属性

この記事では、Azure Backup に組み込まれているセキュリティ属性について説明します。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | ストレージ アカウントに対して Storage Service Encryption を使用します。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| いいえ | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| いいえ |  |
| API 呼び出しの暗号化| はい |  |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ |  |
| VNet インジェクションのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| はい | VM バックアップでは、強制トンネリングがサポートされます。 VM 内で実行されるワークロードでは、強制トンネリングはサポートされません。 |
| 強制トンネリングのサポート| いいえ |  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics は、診断ログを通じてサポートされます。 詳細については、[Log Analytics を使用した Azure Backup で保護されているワークロードの監視](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)に関するページを参照してください。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | カスタマーが作成した RBAC ロールとビルトインの RBAC ロールが使用されます。 詳細については、[ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](/azure/backup/backup-rbac-rs-vault)に関するページを参照してください。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | Azure Portal からカスタマーがトリガーしたすべてのアクションがアクティビティ ログに記録されます。 |
| データ プレーンのログ記録と監査| いいえ | Azure Backup データ プレーンには、直接アクセスできません。  |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい|  |