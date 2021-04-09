---
title: Azure セキュリティ コントロール - データ保護
description: Azure セキュリティ コントロール データ保護
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d36ed6a795c5fa2241ee71751053a4cb5986aaf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576828"
---
# <a name="security-control-data-protection"></a>セキュリティ コントロールデータ保護

データ保護の推奨事項では、暗号化、アクセス制御リスト、ID ベースのアクセス制御、およびデータ アクセスの監査ログに関連した問題の対処に重点を置いています。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.1 | 13.1 | Customer |

タグを使用して、機密情報を格納または処理する Azure リソースの追跡に役立てます。

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.2 | 13.2、2.10 | Customer |

環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 アプリケーションやエンタープライズ環境で必要とされる Azure リソースへのアクセス レベルを制限できます。 Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを制御できます。 

- [追加の Azure サブスクリプションを作成する方法](../../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.3 | 13.3 | 共有 |

機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする、Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で利用します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.4. | 14.4 | 共有 |

転送中のすべての機密情報を暗号化します。 Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

- [Azure での転送中の暗号化の概要](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.5 | 14.5 | 共有 |

Azure の特定のサービスで利用できる機能がない場合は、サードパーティのアクティブ検出ツールを使用して、組織のテクノロジ システムによって格納、処理、または送信されるすべての機密情報 (オンサイトにあるもの、またはリモート サービス プロバイダーにあるものなど) を特定し、組織の機密情報を更新します。

Microsoft 365 ドキュメント内の機密情報を特定するには、Azure Information Protection を使用します。

Azure SQL Information Protection は、Azure SQL Database に格納されている情報の分類とラベル付けを支援するために使用します。

- [Azure SQL Data Discovery を実装する方法](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [Azure Information Protection を実装する方法](/azure/information-protection/deployment-roadmap)

- [Azure での顧客データの保護について](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.6 | 14.6 | Customer |

Azure ロールベースのアクセス制御 (Azure RBAC) を使用してデータとリソースへのアクセスを制御します。それ以外の場合は、サービス固有のアクセス制御方法を使用します。

- [Azure RBAC を構成する方法](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.7 | 14.7 | 共有 |

コンピューティング リソースでの準拠に必要な場合は、ホストベースの自動データ損失防止ソリューションなどのサードパーティ ツールを実装して、データがシステムからコピーされた場合でもデータへのアクセス制御を実施します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.8 | 14.8 | Customer |

すべての Azure リソースで保存時の暗号化を使用します。 Microsoft では、Azure に暗号化キーの管理を許可することをお勧めしていますが、一部のインスタンスでユーザーが自身のキーを管理するという選択肢もあります。 

- [Azure での保存時の暗号化の概要](../fundamentals/encryption-atrest.md)

- [ユーザーが管理する暗号化キーを構成する方法](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.9 | 14.9 | Customer |

Azure アクティビティ ログで Azure Monitor を使用して、重要な Azure リソースに変更が加えられたときのアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../../azure-monitor/alerts/alerts-activity-log.md)


## <a name="next-steps"></a>次のステップ

- 次のセキュリティ コントロールを参照してください。[脆弱性の管理](security-control-vulnerability-management.md)