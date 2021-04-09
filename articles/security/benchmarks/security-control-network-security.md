---
title: Azure セキュリティ コントロール - ネットワークのセキュリティ
description: Azure セキュリティ コントロール - ネットワークのセキュリティ
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cc52d1f6eec5f1aedb0db37b3945f7be6d9f62a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595632"
---
# <a name="security-control-network-security"></a>セキュリティ コントロールネットワークのセキュリティ

ネットワークのセキュリティに関する推奨事項では、Azure サービスへのアクセスを許可または拒否する対象のネットワーク プロトコル、TCP/UDP ポート、およびネットワーク接続済みサービスを指定することに重点を置いています。

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.1 | 9.2、9.4、14.1、14.2、14.3 | Customer |

すべての Virtual Network サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループがあることを確認します。 Private Link を使用したプライベート エンドポイントが使用できる場合は使用して、VNet の ID をサービスまで拡張することで、仮想ネットワークに対する Azure サービス リソースをセキュリティで保護します。 プライベート エンドポイントと Private Link が使用できない場合は、サービス エンドポイントを使用します。 サービス固有の要件については、その特定のサービスのセキュリティに関する推奨事項を参照してください。 

または、特定のユース ケースがある場合は、Azure Firewall を実装することで要件を満たすことができます。

- [仮想ネットワーク サービス エンドポイントについて](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Azure Private Link について](../../private-link/private-link-overview.md)

- [仮想ネットワークを作成する方法](../../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall をデプロイして構成する方法](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.2 | 9.3、12.2、12.8 | Customer |

Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure のネットワーク リソースを保護します。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.3 | 9.5 | Customer |

着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイします。 WAF の診断設定を有効にし、ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースにログを取り込みます。

- [Azure WAF をデプロイする方法](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.4 | 12.3 | Customer |

DDoS 攻撃から保護するために、Azure Virtual Network で DDoS Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある IP アドレスとの通信を拒否します。

有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスとともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

Azure Security Center の Just In Time ネットワーク アクセスを使用して、NSG を構成し、限られた期間、承認された IP アドレスへのエンドポイントの公開を制限します。

Azure Security Center のアダプティブ ネットワークのセキュリティ強化を使用して、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限する NSG 構成を推奨します。

- [DDoS 保護を構成する方法](../../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall をデプロイする方法l](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../../security-center/azure-defender.md)

- [Azure Security Center のアダプティブ ネットワークのセキュリティ強化について](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center の Just In Time ネットワーク アクセス制御について](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.5 | 12.5 | Customer |

異常なアクティビティを調査する場合は、Network Watcher パケット キャプチャを有効にします。

- [Network Watcher を有効にする方法](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.6 | 12.6、12.7 | Customer |

ペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からのプランを選択します。  ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

悪意のあるトラフィックを検出および拒否できるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall をデプロイする方法l](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートを構成する方法](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.7 | 12.9、12.10 | Customer |

信頼できる証明書に対して HTTPS/TLS が有効な Web アプリケーションに、Azure Application Gateway をデプロイします。

- [Application Gateway をデプロイする方法](../../application-gateway/quick-create-portal.md)

- [HTTPS を使用するように Application Gateway を構成する方法](../../application-gateway/create-ssl-portal.md)

- [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.8 | 1.5 | Customer |

Virtual Network サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall のネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

また、アプリケーション セキュリティグループを使用して、複雑なセキュリティ構成を簡略化することもできます。 アプリケーション セキュリティ グループを使用すると、ネットワーク セキュリティをアプリケーションの構造の自然な拡張として構成でき、仮想マシンをグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。

- [サービス タグの概要と使用](../../virtual-network/service-tags-overview.md)

- [アプリケーション セキュリティ グループの概要と使用](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.9 | 11.1 | Customer |

Azure Policy を使用して、ネットワーク リソースの標準的なセキュリティ構成を定義し、実装します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を微調整できます。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](../../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint を作成する方法](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.10 | 11.2 | Customer |

ネットワークのセキュリティおよびトラフィック フローに関連する NSG およびその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 1.11 | 11.3 | Customer |

Azure アクティビティ ログを使用してリソース構成を監視し、Azure リソースに対する変更を検出します。 重要なリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../../azure-monitor/alerts/alerts-activity-log.md)

## <a name="next-steps"></a>次のステップ

- 次のセキュリティ コントロールを参照してください。[ログ記録と監視](security-control-logging-monitoring.md)