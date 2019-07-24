---
title: Azure セキュリティ サービスおよびテクノロジ | Microsoft Docs
description: この記事では、Azure セキュリティ サービスとテクノロジを精選した一覧を示します。
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 13183282e5e607f0052194a474203f97e0160adb
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246136"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Azure で利用できるセキュリティ サービスとテクノロジ

現在の、そしてこの先も含めた Azure のお客様との話し合いで、“Azure で提供する必要があるすべてのセキュリティ関連のサービスとテクノロジのリストはありますか?” という質問を受けることがよくあります。

クラウド サービス プロバイダーのオプションを評価している場合、この情報があると便利です。 そのために、開始時にお使いいただけるリストをご用意しました。

時間の経過と共に、このリストは Azure と同様に変更および増加します。 セキュリティ関連のサービスとテクノロジを最新の状態に維持するために、定期的にこのページを確認してください。

## <a name="general-azure-security"></a>Azure の全般的なセキュリティ
|Service|説明|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](../security-center/security-center-intro.md)| ハイブリッド クラウド ワークロード全体でセキュリティ管理と高度な脅威保護を実現するクラウドのワークロードを保護するソリューションです。|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| パスワード、接続文字列およびアプリの動作に必要なその他の情報を格納するセキュリティで保護された機密データ ストアです。 |
|[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)|アプリとリソースの操作の分析情報を提供する、製品利用統計情報およびその他のデータを収集する、クエリ言語および分析エンジンを提供する監視サービスです。 単独で使用することも、Security Center などのその他のサービスと共に使用することもできます。 |
|[Azure Dev/Test Lab](../devtest-lab/devtest-lab-overview.md)|無駄を最小限に抑え、コストを管理しつつ、Azure で迅速に環境を作成するためのサポートを開発者とテスト担当者に提供するサービスです。  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>ストレージのセキュリティ
|Service|説明|
|------|--------|
| [Azure&nbsp;Storage&nbsp;Service&nbsp;Encryption](../storage/common/storage-service-encryption.md)|Azure ストレージのデータを自動的に暗号化するセキュリティ機能です。   |
|[StorSimple のハイブリッド ストレージの暗号化](../storsimple/storsimple-ova-overview.md)| オンプレミスのデバイスと Azure クラウド ストレージ間のストレージ タスクを管理する統合ストレージ ソリューションです。|
|[Azure のクライアント側の暗号化](../storage/common/storage-client-side-encryption.md)| Microsoft Azure Storage にアップロードする前にクライアント アプリケーション内のデータを暗号化するクライアント側の暗号化ソリューションです。ダウンロード中には、データを復号化します。 |
| [Azure Storage Shared Access Signature](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。  |
|[Azure ストレージ アカウント キー](../storage/common/storage-create-storage-account.md)| ストレージ アカウントへのアクセス時に、Azure ストレージの認証で使用されるアクセス制御メソッドです。 |
|[SMB 3.0 の暗号化を使用した Azure ファイル共有](../storage/files/storage-files-introduction.md)|ネットワークで、サーバー メッセージ ブロック (SMB) ファイル共有プロトコルの自動的な暗号化を有効にするネットワーク セキュリティ テクノロジです。 |
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Storage-Analytics)| ストレージ アカウントのデータのログとメトリックを生成するテクノロジです。 |

<!------>

## <a name="database-security"></a>データベースのセキュリティ
|Service|説明|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](../sql-database/sql-database-firewall-configure.md)|データベースへのネットワーク経由の攻撃から保護するためのネットワークのアクセス制御機能です。 |
|[Azure&nbsp;SQL&nbsp;Cell&nbsp;Level Encryption](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| 詳細なレベルで暗号化を提供するデータベース セキュリティ テクノロジです。  |
| [Azure&nbsp;SQL&nbsp;接続暗号化](../sql-database/sql-database-control-access.md)|SQL Database では、セキュリティを提供するために、IP アドレスで接続を制限するファイアウォール規則、ユーザーに ID の指定を要求する認証メカニズム、およびユーザーを特定の操作とデータに限定する承認メカニズムによって、アクセスを制御します。 |
| [Azure SQL Always Encryption (Azure SQL の常時暗号化)](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Azure SQL Database や SQL Server データベースに格納された、クレジット カード番号や国民識別番号 (米国の社会保障番号など) のような機密データを保護することを目的とした機能です。  |
| [Azure&nbsp;SQL&nbsp;Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| データベース全体のストレージを暗号化するデータベース セキュリティ機能です。 |
| [Azure SQL Database 監査](../sql-database/sql-database-auditing.md)|データベース イベントを追跡し、それを Azure Storage アカウントの監査ログに書き込むデータベース監査機能です。  |


## <a name="identity-and-access-management"></a>ID 管理とアクセス管理
|Service|説明|
|------|--------|
| [Azure&nbsp;Role&nbsp;Based&nbsp;Access Control](../active-directory/role-based-access-control-configure.md)|組織内でのロールに基づき、必要なリソースのみにアクセスすることをユーザーに許可するアクセス制御機能です。  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|マルチテナントのクラウドベースのディレクトリと Azure 内で複数の ID 管理サービスをサポートするクラウド ベースの認証リポジトリです。  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Azure ベースのアプリケーションの使用時に、顧客のサインアップ、サインイン、プロファイル管理を制御する ID 管理サービスです。   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| Active Directory Domain Services のクラウドベースのマネージド バージョンです。 |
| [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| セキュリティで保護された情報へのアクセスを許可する前に、いくつかの異なる認証および検証形式が使用されるセキュリティ対応の機能です。 |

## <a name="backup-and-disaster-recovery"></a>バックアップと障害復旧
|Service|説明|
|------|--------|
| [Azure&nbsp;Backup](../backup/backup-introduction-to-azure-backup.md)| Azure クラウドのデータをバックアップおよび復元するために使用される Azure ベースのサービスです。 |
| [Azure&nbsp;Site&nbsp;Recovery](../site-recovery/site-recovery-overview.md)|物理マシンと仮想マシン (VM) で実行中のワークロードでエラーが発生した場合にその復旧を可能にする、プライマリ サイトからセカンダリ ロケーションにワークロードをレプリケートするオンライン サービスです。 |

## <a name="networking"></a>ネットワーク
|Service|説明|
|------|--------|
| [Network&nbsp;Security&nbsp;Groups](../virtual-network/virtual-networks-nsg.md)| タプルが 5 つ使用された、意思決定を許可または拒否することができるネットワーク ベースのアクセス制御機能です。  |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Azure Virtual Network へのクロスプレミス アクセスを許可する VPN エンドポイントとして使用されるネットワーク デバイスです。  |
| [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)|URL に基づいてルーティングし、SSL オフロードを実行できる、高度な Web アプリケーション ロード バランサーです。 |
|[Web アプリケーション ファイアウォール](../application-gateway/waf-overview.md) (WAF)|一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Application Gateway の機能です。|
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|TCP/UDP アプリケーション ネットワーク ロード バランサーです。 |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| オンプレミス ネットワークと Azure Virtual Network 間の専用 WAN リンクです。 |
| [Azure の Traffic Manager](../traffic-manager/traffic-manager-overview.md)| グローバルな DNS ロード バランサーです。|
| [Azure アプリケーション プロキシ](../active-directory/active-directory-application-proxy-get-started.md)| オンプレミスでホストされている Web アプリケーションのリモート アクセスをセキュリティで保護するための認証フロントエンドです。 |
|[Azure Firewall](../firewall/overview.md)|Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。|
|[Azure DDoS Protection](../virtual-network/ddos-protection-overview.md)|アプリケーション設計のベスト プラクティスと組み合わせることにより、DDoS 攻撃に対する防御が提供されます。|
|[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)|直接接続によって、仮想ネットワークのプライベート アドレス空間と VNet の ID が Azure サービスまで拡張されます。|