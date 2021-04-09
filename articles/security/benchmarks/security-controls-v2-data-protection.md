---
title: Azure セキュリティ ベンチマーク V2 - データ保護
description: Azure セキュリティ ベンチマーク V2 データ保護
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 50358eed580bbd83f25386feb0068a252060672b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037117"
---
# <a name="security-control-v2-data-protection"></a>セキュリティ コントロール V2:データ保護

データ保護は、保存時、転送中、承認されたアクセス メカニズムによるデータ保護の制御を対象としています。 これには、Azure のアクセス制御、暗号化、ログ記録を使用した機密データ資産の検出、分類、保護、監視が含まれます。

該当する組み込み Azure Policy を確認するには、「[Azure セキュリティ ベンチマーク規制コンプライアンスの組み込みイニシアチブの詳細: データ保護](../../governance/policy/samples/azure-security-benchmark.md#data-protection)」を参照してください

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1:機密データを検出、分類、ラベル付けする

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-1 | 13.1、14.5、14.7 | SC-28 |

お使いの機密データを検出、分類、ラベル付けすると、組織の技術システムで機密情報が安全に保存、処理、および転送されるコントロールを適切に設計できます。

Azure、オンプレミス、Office 365 などの場所にある Office ドキュメントの機密情報には、Azure Information Protection (とこれに付随するスキャン ツール) を使用します。

Azure SQL Database に格納されている情報の分類とラベル付けには、Azure SQL Information Protection を使用します。

- [Azure Information Protection を使用して機密情報をタグ付けする](/azure/information-protection/what-is-information-protection) 

- [Azure SQL Data Discovery を実装する方法](../../azure-sql/database/data-discovery-and-classification-overview.md)

**責任**: 共有

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [データ セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2:機密データを保護する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-2 | 13.2、2.10 | SC-7、AC-4 |

機密データを保護するには、Azure ロール ベースのアクセス制御 (Azure RBAC)、ネットワーク ベースのアクセス制御、Azure サービスの特定の制御 (SQL を始めとするデータベースでの暗号化など) を使用してアクセスを制限します。 

一貫したアクセス制御を確保するには、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。 企業のセグメント化戦略では、機密またはビジネスに重要なデータやシステムの場所からも通知される必要があります。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft では顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Microsoft では、Azure 内の顧客データが確実にセキュリティで保護されるように、データ保護コントロールおよび機能をいくつか実装しています。

- [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md)

- [Azure での顧客データの保護について](../fundamentals/protection-customer-data.md)

**責任**: 共有

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [データ セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3:機密データの不正転送を監視する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-3 | 13.3 | AC-4、SI-4 |

企業が可視および制御できる範囲外の場所にデータが不正に転送されるのを監視することができます。 これには通常、不正なデータ流出を示す可能性のある異常な活動 (大規模または異常な転送) の監視が含まれます。 

Azure Storage Advanced Threat Protection (ATP) と Azure SQL ATP によって、機密情報の不正転送を示唆する、異常な情報転送のアラートを送信できます。 

Azure Information Protection (AIP) には、分類およびラベル付けされた情報を監視する機能があります。 

データ損失対策 (DLP) に準拠する必要がある場合、ホスト ベースの DLP ソリューションを使用して、検出および予防コントロールを適用してデータ流出を回避できます。

- [Azure Defender for SQL](../../azure-sql/database/azure-defender-for-sql.md)

- [Azure Defender for Storage](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**責任**: 共有

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security) 

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4:転送中の機密情報を暗号化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

転送中のデータが攻撃者に簡単に読み取られたり変更されたりしないよう、暗号化を使用して "帯域外" 攻撃 (トラフィックのキャプチャなど) から保護し、アクセス制御を補完する必要があります。

これはプライベート ネットワーク上のトラフィックでは省略できますが、外部ネットワークとパブリック ネットワーク上のトラフィックには重要です。 ご自分の Azure リソースに接続するすべてのクライアントの HTTP トラフィックのネゴシエートには、確実に TLS v1.2 以上を使用してください。 リモート管理には、暗号化されていないプロトコルではなく、(Linux の場合) SSH または (Windows の場合) RDP/TLS を使用します。 SSL、TLS、SSH の古いバージョンとプロトコル、および弱い暗号は無効にする必要があります。

既定では Azure によって、Azure のデータ センター間の転送データが暗号化されます。

- [Azure での転送中の暗号化の概要](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS セキュリティに関する情報](/security/engineering/solving-tls1-problem)

- [転送中の Azure データの二重暗号化](../fundamentals/double-encryption.md#data-in-transit)

**責任**: 共有

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [データ セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5:保存データを暗号化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-5 | 14.8 | SC-28、SC-12 |

保存データを "帯域外" 攻撃 (基になっているストレージへのアクセスなど) から保護するには、アクセス制御を補完する暗号化を使用する必要があります。 これにより、攻撃者がデータを簡単に読み取ったり変更したりすることが確実にできなくなります。 

既定では Azure によって保存データが暗号化されます。 機密性の高いデータには、保存されている利用可能なすべての Azure リソースに、オプションで暗号化を追加できます。 既定では Azure によってお使いの暗号化キーが管理されますが、特定の Azure サービスに対して、お使いの独自のキー (カスタマー マネージド キー) が管理するオプションが Azure により提供されています。

- [Azure での保存時の暗号化の概要](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [ユーザーが管理する暗号化キーを構成する方法](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [暗号化モデルとキー管理テーブル](../fundamentals/encryption-models.md)

- [Azure における保存データの二重暗号化](../fundamentals/double-encryption.md#data-at-rest)

**責任**: 共有

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [データ セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-data-security)