---
title: Azure HDInsight のエンタープライズ セキュリティの概要
description: Azure HDInsight でエンタープライズ セキュリティを確保するためのさまざまな方法について説明します。
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: 713559a747b58e65d04c8310755baedc93db929e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863158"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Azure HDInsight のエンタープライズ セキュリティの概要

Azure HDInsight には、エンタープライズ セキュリティ ニーズに対応するためのさまざまな方法が用意されています。 これらのソリューションのほとんどは、既定ではアクティブ化されていません。 この柔軟性により、ユーザーにとって最も重要なセキュリティ機能を選択することができ、不要な機能に支払いを行う必要がありません。 つまり、セットアップと環境で適切なソリューションが有効になっていることをユーザーが責任を持って確認する必要があるということでもあります。

この記事では、セキュリティ ソリューションについて、境界セキュリティ、認証、承認、暗号化という従来からある 4 つの柱に分けて説明します。

この記事では、**Azure HDInsight Enterprise セキュリティ パッケージ (ESP)** についても説明します。ESP は、Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御を HDInsight のクラスターに提供します。

## <a name="enterprise-security-pillars"></a>エンタープライズ セキュリティの柱

エンタープライズ セキュリティを確認する 1 つの方法は、コントロールの種類に基づいて、セキュリティ ソリューションを 4 つの主要なグループに分けることです。 セキュリティの柱とも呼ばれるこれらのグループは、境界セキュリティ、認証、承認、および暗号化に分けられます。

### <a name="perimeter-security"></a>境界セキュリティ

HDInsight の境界セキュリティは、[仮想ネットワーク](../hdinsight-plan-virtual-network-deployment.md)を使用して実現されます。 エンタープライズ管理者は、仮想ネットワーク (VNET) 内にクラスターを作成し、ネットワーク セキュリティ グループ (NSG) を使用して仮想ネットワークへのアクセスを制限できます。 HDInsight クラスターと通信できるのは、NSG の受信規則で許可されている IP アドレスだけです。 この構成では、境界セキュリティを提供します。

VNET に配置されているすべてのクラスターにも、プライベート エンドポイントがあります。 このエンドポイントは、クラスター ゲートウェイへのプライベート HTTP アクセスが可能になるように、VNET 内のプライベート IP に解決されます。

### <a name="authentication"></a>認証

HDInsight の [Enterprise セキュリティ パッケージ](apache-domain-joined-architecture.md) では、Active Directory ベースの認証、マルチユーザー サポート、およびロールベースのアクセス制御が提供されます。 Active Directory 統合は、[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) を使用して実現されます。 これらの機能を使用して、Active Directory ドメインに参加する HDInsight クラスターを作成できます。 その後、クラスターに対して認証できる企業従業員の一覧を構成します。

この設定により、企業の従業員は、ドメイン資格情報を使用してクラスター ノードにサインインできます。 また、ドメイン資格情報を使用して、他の承認済みエンドポイント ( Apache Ambari Views、ODBC、JDBC、PowerShell、REST API など) で認証して、クラスターと対話することもできます。

### <a name="authorization"></a>承認

ほとんどの企業では、すべての従業員がすべてのエンタープライズ リソースにフル アクセスできるわけではないというベスト プラクティスに従っています。 同様に、管理者はクラスター リソースのロールベースのアクセス制御ポリシーを定義できます。 この操作は、ESP クラスターでのみ使用できます。

Hadoop 管理者は、ロールベースのアクセス制御 (RBAC) を構成できます。 この構成では、Apache Ranger プラグインを使用して、Apache [Hive](apache-domain-joined-run-hive.md)、[HBase](apache-domain-joined-run-hbase.md)、および [Kafka](apache-domain-joined-run-kafka.md) をセキュリティで保護します。 RBAC ポリシーを構成すると、組織内のロールにアクセス許可を関連付けることができます。 この抽象化レイヤーを使用すると、より簡単に、ユーザーが作業の責任を果たすために必要なアクセス許可のみを持つようにできます。 また、Ranger により、従業員のデータ アクセスとアクセス制御ポリシーに加えられた変更を監査できます。

たとえば、管理者は [Apache Ranger](https://ranger.apache.org/) を構成して Hive のアクセス制御ポリシーを設定できます。 この機能により、行レベルおよび列レベルのフィルター処理 (データ マスキング) が実現されます。 また、未承認のユーザーがアクセスできないように、機密データがフィルター処理されます。

### <a name="auditing"></a>監査

リソースへの許可されていないアクセスや意図しないアクセスを追跡するには、クラスター リソースへのアクセスを監査する必要があります。 これは、許可されていないアクセスからクラスター リソースを保護するのと同じくらい重要です。

管理者は HDInsight クラスター リソースとデータへのすべてのアクセスを表示し、レポートを作成できます。 また、管理者はアクセス制御ポリシーへの変更を表示して報告することができます。

Apache Ranger および Ambari 監査ログと ssh アクセス ログにアクセスするには、[Azure Monitor を有効](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing)にし、監査レコードが記載されたテーブルを表示します。

### <a name="encryption"></a>暗号化

データの保護は、組織のセキュリティとコンプライアンス要件を満たすために重要です。 許可されていない従業員からデータへのアクセスを制限すると共に、暗号化する必要があります。

HDInsight では、プラットフォーム マネージド キーと[カスタマー マネージド キー](../disk-encryption.md)の両方による保存時のデータ暗号化がサポートされます。 転送中のデータの暗号化は、TLS と IPSec の両方で処理されます。 詳細については、「[Azure HDInsight での転送中の暗号化](encryption-in-transit.md)」を参照してください。

### <a name="compliance"></a>コンプライアンス

Azure コンプライアンス認証は、正式な認定資格を含むさまざまな種類の保証に基づいています。 また、構成証明、検証、承認にも基づいています。 さらに、独立したサードパーティの監査会社による評価や、 Microsoft によって作成された契約の修正、自己評価、顧客向けのガイダンス ドキュメントにも基づきます。 HDInsight のコンプライアンス情報については、[Microsoft Trust Center](https://www.microsoft.com/trust-center)と [Microsoft Azure コンプライアンスの概要](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)を参照してください。

## <a name="shared-responsibility-model"></a>共同責任モデル

次の図は、主要なシステム セキュリティ領域と、それぞれで使用できるセキュリティ ソリューションをまとめたものです。 また、どのセキュリティ領域がお客様の責任となり、 どの領域がサービス プロバイダーである HDInsight の責任になるかも示しています。

:::image type="content" source="./media/hdinsight-security-overview/hdinsight-shared-responsibility.png" alt-text="HDInsight の共有責任図" border="false":::

次の表に、セキュリティ ソリューションの種類ごとにリソースへのリンクを示します。

| セキュリティ領域 | 使用可能なソリューション | 責任者 |
|---|---|---|
| データ アクセス セキュリティ | Azure Data Lake Storage Gen1 および Gen2 対象の[アクセス制御リスト ACL](../../storage/blobs/data-lake-storage-access-control.md) を構成する  | Customer |
|  | ストレージ アカウントで [[安全な転送が必須]](../../storage/common/storage-require-secure-transfer.md) プロパティを有効にします。 | Customer |
|  | [Azure Storage ファイアウォール](../../storage/common/storage-network-security.md)および仮想ネットワークを構成する | Customer |
|  | Cosmos DB と [Azure SQL DB](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) 用に [Azure 仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)を構成する | Customer |
|  | クラスター内通信に TLS と IPSec を使用するには、[転送中の暗号化](./encryption-in-transit.md)の機能が有効になっていることを確認します。 | Customer |
|  | Azure Storage 暗号化用に[顧客管理のキー](../../storage/common/customer-managed-keys-configure-key-vault.md)を構成する | Customer |
|  | [カスタマー ロックボックス](../../security/fundamentals/customer-lockbox-overview.md)を使用し、Azure サポートによりデータ アクセスを制御する | Customer |
| アプリケーションとミドルウェアのセキュリティ | AAD-DS と統合して [ESP を構成](apache-domain-joined-configure-using-azure-adds.md)するか、[HIB による OAuth 認証](identity-broker.md)を使用する| Customer |
|  | [Apache Ranger 認証](apache-domain-joined-run-hive.md)ポリシーを構成する | Customer |
|  | [Azure Monitor ログ](../hdinsight-hadoop-oms-log-analytics-tutorial.md)を使用する | Customer |
| オペレーティング システムのセキュリティ | 最新の安全な基本イメージを使用してクラスターを作成する | Customer |
|  | [OS の修正プログラム](../hdinsight-os-patching.md)が定期的に適用されるようにする | Customer |
|  | [VM に対し、CMK によるディスク暗号化](../disk-encryption.md)を行う | Customer |
| ネットワークのセキュリティ | [仮想ネットワーク](../hdinsight-plan-virtual-network-deployment.md)を構成する |
|  | [ネットワーク セキュリティ グループ (NSG) の受信規則](../control-network-traffic.md)または[プライベート リンク](../hdinsight-private-link.md)を構成する | Customer |
|  | ファイアウォールを使用して[送信トラフィックの制限](../hdinsight-restrict-outbound-traffic.md)を構成する | Customer |
|  | クラスター ノード間で、[転送中の IPsec 暗号化](encryption-in-transit.md)を構成する | Customer |
| 仮想化インフラストラクチャ | 該当なし | HDInsight (クラウド プロバイダー) |
| 物理インフラのセキュリティ | 該当なし | HDInsight (クラウド プロバイダー) |

## <a name="next-steps"></a>次のステップ

* [ESP を使用する HDInsight クラスターを計画する](apache-domain-joined-architecture.md)
* [ESP を使用する HDInsight クラスターを構成する](./apache-domain-joined-configure-using-azure-adds.md)
* [ESP を使用する HDInsight クラスターを管理する](apache-domain-joined-manage.md)
