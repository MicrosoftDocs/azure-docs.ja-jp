---
title: Azure セキュリティ コントロール - データ保護
description: Azure セキュリティ コントロール データ保護
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d89320807c6322120490db85100453edf593aded
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045537"
---
# <a name="security-control-data-protection"></a>セキュリティ コントロールデータ保護

データ保護の推奨事項では、暗号化、アクセス制御リスト、ID ベースのアクセス制御、およびデータ アクセスの監査ログに関連した問題の対処に重点を置いています。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.1 | 13.1 | Customer |

タグを使用して、機密情報を格納または処理する Azure リソースの追跡に役立てます。

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.2 | 13.2、2.10 | Customer |

環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 アプリケーションやエンタープライズ環境で必要とされる Azure リソースへのアクセス レベルを制限できます。 Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを制御できます。 

- [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.3 | 13.3 | 共有 |

機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする、Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で利用します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.4. | 14.4 | 共有 |

転送中のすべての機密情報を暗号化します。 Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

- [Azure での転送中の暗号化の概要](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.5 | 14.5 | 共有 |

Azure の特定のサービスで利用できる機能がない場合は、サードパーティのアクティブ検出ツールを使用して、組織のテクノロジ システムによって格納、処理、または送信されるすべての機密情報 (オンサイトにあるもの、またはリモート サービス プロバイダーにあるものなど) を特定し、組織の機密情報を更新します。

Office 365 ドキュメント内の機密情報を特定するには、Azure Information Protection を使用します。

Azure SQL Information Protection は、Azure SQL Database に格納されている情報の分類とラベル付けを支援するために使用します。

- [Azure SQL Data Discovery を実装する方法](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Azure Information Protection を実装する方法](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.6 | 14.6 | Customer |

Azure AD RBAC を使用してデータとリソースへのアクセスを制御します。それ以外の場合は、サービス固有のアクセス制御方法を使用します。

- [Azure で RBAC を構成する方法](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.7 | 14.7 | 共有 |

コンピューティング リソースでの準拠に必要な場合は、ホストベースの自動データ損失防止ソリューションなどのサードパーティ ツールを実装して、データがシステムからコピーされた場合でもデータへのアクセス制御を実施します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.8 | 14.8 | Customer |

すべての Azure リソースで保存時の暗号化を使用します。 Microsoft では、Azure に暗号化キーの管理を許可することをお勧めしていますが、一部のインスタンスでユーザーが自身のキーを管理するという選択肢もあります。 

- [Azure での保存時の暗号化の概要](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [ユーザーが管理する暗号化キーを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.9 | 14.9 | Customer |

Azure アクティビティ ログで Azure Monitor を使用して、重要な Azure リソースに変更が加えられたときのアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>次のステップ

- 次のセキュリティ コントロールを参照してください。[脆弱性の管理](security-control-vulnerability-management.md)