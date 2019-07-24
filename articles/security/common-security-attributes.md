---
title: Azure サービスのセキュリティ属性
description: Azure Service Fabric を評価するための一般的なセキュリティ属性のチェックリスト
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007679"
---
# <a name="common-security-attributes-for-azure-services"></a>Azure サービスの一般的なセキュリティ属性

セキュリティは、Azure サービスのすべての要素に統合されています。 この記事では、特定の Azure サービスについて、その一般的なセキュリティ属性をまとめています。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## [<a name="azure-backup"></a>Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | ストレージ アカウントに対して Storage Service Encryption を使用します。 |
| 転送中の暗号化: <ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet-VNet 暗号化</ul>| いいえ  | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ  |  |
| 列レベルの暗号化 (Azure Data Services)| いいえ  |  |
| API 呼び出しの暗号化| はい |  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ  |  |
| VNet インジェクションのサポート| いいえ  |  |
| ネットワークの分離/ファイアウォールのサポート| はい | VM バックアップでは、強制トンネリングがサポートされます。 VM 内で実行されるワークロードでは、強制トンネリングはサポートされません。 |
| 強制トンネリングのサポート | いいえ  |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics は、診断ログを通じてサポートされます。 詳細については、Log Analytics を使用した Azure Backup で保護されているワークロードの監視に関するページ (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)) を参照してください。 |

### <a name="iam-support"></a>IAM サポート

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| アクセス管理 - 認証| はい | 認証は Azure Active Directory を介して行われます。 |
| アクセス管理 - 承認| はい | カスタマーが作成した RBAC ロールとビルトインの RBAC ロールが使用されます。 詳細については、ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理に関するページ (/azure/backup/backup-rbac-rs-vault) を参照してください。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | Azure Portal からカスタマーがトリガーしたすべてのアクションがアクティビティ ログに記録されます。 |
| データ プレーンのログ記録と監査| いいえ  | Azure Backup データ プレーンには、直接アクセスできません。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい|  |

## [<a name="azure-key-vault"></a>Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | すべてのオブジェクトが暗号化されます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | すべての通信が暗号化された API 呼び出しを介して行われます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーが Key Vault のすべてのキーを制御します。 ハードウェア セキュリティ モジュール (HSM) バックアップ キーが指定されている場合、FIPS レベル 2 HSM により、キー、証明書、またはシークレットが保護されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | HTTPS を使用します。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | Virtual Network (VNet) サービス エンドポイントを使用します。 |
| VNet インジェクションのサポート| いいえ  |  |
| ネットワークの分離/ファイアウォールのサポート| はい | Vnet ファイアウォール ルールを使用します。 |
| 強制トンネリングのサポート | いいえ  |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics を使用します。 |

### <a name="iam-support"></a>IAM サポート

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| アクセス管理 - 認証| はい | 認証は Azure Active Directory を介して行われます。 |
| アクセス管理 - 承認| はい | Key Vault アクセス ポリシーを使用します。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | Log Analytics を使用します。 |
| データ プレーンのログ記録と監査| はい | Log Analytics を使用します。 |

### <a name="access-controls"></a>アクセス制御

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 制御/管理プレーンのアクセス制御 | はい | Azure Resource Manager のロールベースのアクセス制御 (RBAC) |
| データ プレーンのアクセス制御 (すべてのサービス レベル) | はい | Key Vault アクセス ポリシー |

## [<a name="azure-service-fabric"></a>Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン (VM) スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい |  |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン (VM) スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | Service Fabric API は Azure Resource Manager によって呼び出されます。 有効な JSON Web トークン (JWT) が必要です。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| はい |  |
| ネットワークの分離 / ファイアウォールのサポート| はい | ネットワーク セキュリティ グループ (NSG) の使用。 |
| 強制トンネリングのサポート | はい | Azure ネットワークでは、強制トンネリングを提供します。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure 監視サポートとサード パーティ サポートの使用。 |

### <a name="iam-support"></a>IAM サポート

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| アクセス管理 - 認証| はい | 認証は Azure Active Directory を介して行われます。 |
| アクセス管理 - 承認| はい | SFRP 経由の呼び出しのための ID およびアクセス管理 (IAM)。 クラスター エンド ポイントは、ユーザーと管理者という 2 つのロールで直接呼び出すことができます。ユーザーは API をいずれかのロールにマッピングできます。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | コントロール プレーン操作はすべて、監査と承認のプロセスを経由して実行されます。 |
| データ プレーンのログ記録と監査| 該当なし | ユーザーはクラスターを所有します。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | サービス構成は Azure Deploy によってバージョン管理され、デプロイされます。 コード (アプリケーションとランタイム) は Azure Build によってバージョン管理されます。
 |

## <a name="azure-storage"></a>Azure Storage

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい |  |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | 標準の HTTPS/TLS 機構をサポートします。  サービスに転送するデータは、ユーザーが事前に暗号化することもできます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | 「[Azure Key Vault で顧客が管理するキーを Storage Service Encryption に使用する](../storage/common/storage-service-encryption-customer-managed-keys.md)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい |  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| 該当なし |  |
| ネットワークの分離/ファイアウォールのサポート| はい | |
| 強制トンネリングのサポート | 該当なし |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Monitor のメトリックが利用できるようになり、ログのプレビューが開始されました |

### <a name="iam-support"></a>IAM サポート

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| アクセス管理 - 認証| はい | Azure Active Directory、共有キー、共有アクセス トークン。 |
| アクセス管理 - 承認| はい | RBAC、POSIX ACL、SAS トークンを使用した承認をサポートします |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査 | はい | Azure Resource Manager アクティビティ ログ |
| データ プレーンのログ記録と監査| はい | サービスの診断ログおよび Azure Monitor のログのプレビューが開始されました  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | Azure Resource Manager API によりリソース プロバイダーのバージョン管理をサポートします |