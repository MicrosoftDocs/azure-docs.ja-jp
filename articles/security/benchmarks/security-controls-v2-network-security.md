---
title: Azure セキュリティ ベンチマーク V2 - ネットワーク セキュリティ
description: Azure セキュリティ ベンチマーク V2 ネットワーク セキュリティ
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b47f833a3b47dae145aa18eb6eda0c6bd95f6689
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042915"
---
# <a name="security-control-v2-network-security"></a>セキュリティ コントロール V2:ネットワークのセキュリティ

ネットワーク セキュリティの対象は、Azure ネットワークのセキュリティと保護のためのコントロールです。 これには、仮想ネットワークのセキュリティ保護、プライベート接続の確立、外部からの攻撃の防止と軽減、DNS の保護が含まれます。

該当する組み込み Azure Policy を確認するには、「[Azure セキュリティ ベンチマーク規制コンプライアンスの組み込みイニシアチブの詳細: ネットワークのセキュリティ](../../governance/policy/samples/azure-security-benchmark.md#network-security)」を参照してください。

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-1 | 9.2、9.4、14.1、14.2、14.3 | AC-4、CA-3、SC-7 |

すべての Azure 仮想ネットワークが、ビジネス リスクに合わせたエンタープライズ セグメント化の原則に従っていることを確認します。 組織のリスクが高くなる可能性があるシステムは、独自の仮想ネットワーク内に隔離し、ネットワーク セキュリティ グループ (NSG) または Azure Firewall で十分に保護する必要があります。

アプリケーションとエンタープライズのセグメント化戦略に基づき、ネットワーク セキュリティ グループの規則に基づいて、内部リソース間のトラフィックを制限または許可します。 明確に定義された特定のアプリケーション (3 層アプリなど) については、これは高度にセキュリティで保護された "既定で拒否、例外による許可" の方法になります。 相互に対話する多くのアプリケーションとエンドポイントがある場合、これが適切にスケーリングされない可能性があります。 また、多数のエンタープライズ セグメントまたはスポーク (ハブ/スポーク トポロジにおいて) で中央管理が必要な環境では、Azure Firewall を使用することもできます。

Azure Security Center のアダプティブ ネットワーク強化を使用して、外部ネットワーク トラフィック ルールへの参照に基づいてポートとソース IP を制限するネットワーク セキュリティ グループの構成を推奨します。

Azure Sentinel を使用して、不安がある従来のプロトコルである SSL/TLSv1、SMBv1、LM/NTLMv1、wDigest、署名なしの LDAP バインド、Kerberos の弱い暗号の使用を検出します。

- [セキュリティ規則を使用してネットワーク セキュリティ グループを作成する方法](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall をデプロイして構成する方法](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center でのアダプティブ ネットワークのセキュリティ強化機能](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Sentinel の安全でないプロトコルのブック](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-2 | N/A | CA-3、AC-17、MA-4 |

Azure ExpressRoute または Azure 仮想プライベート ネットワーク (VPN) を使用して、コロケーション環境内で Azure のデータセンターとオンプレミスのインフラストラクチャ間のプライベート接続を作成できます。 ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、高速で、待ち時間も短くなります。 ポイント対サイト VPN とサイト間 VPN では、これらの VPN オプションと Azure ExpressRoute の任意の組み合わせを使用して、オンプレミスのデバイスやネットワークを仮想ネットワークに接続できます。

Azure で 2 つ以上の仮想ネットワークを接続するには、仮想ネットワーク ピアリングまたは Private Link を使用します。 ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートであり、Azure のバックボーン ネットワーク上に保持されます。

- [ExpressRoute 接続モデルとは](../../expressroute/expressroute-connectivity-models.md)

- [Azure VPN の概要](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure サービスへのプライベート ネットワーク アクセスを確立する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-3 | 14.1 | AC-4、CA-3、SC-7 |

Azure Private Link を使用して、インターネットを経由せずに、仮想ネットワークから Azure サービスへのプライベート アクセスを有効にします。 Azure Private Link がまだ使用できない場合は、Azure 仮想ネットワーク サービス エンドポイントを使用します。 Azure 仮想ネットワーク サービス エンドポイントにより、Azure のバックボーン ネットワーク上で最適化されたルートを介して、サービスへの安全なアクセスが提供されます。

プライベート アクセスは、Azure サービスによって提供される認証およびトラフィックのセキュリティに加えて、追加の多層防御手段となります。

- [Azure Private Link について](../../private-link/private-link-overview.md)

- [仮想ネットワーク サービス エンドポイントについて](../../virtual-network/virtual-network-service-endpoints-overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 外部ネットワーク攻撃からアプリケーションやサービスを保護する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-4 | 9.5、12.3、12.9 | SC-5、SC-7 |

分散型サービス拒否 (DDoS) 攻撃、アプリケーション固有の攻撃、未承諾で悪意のある可能性のあるインターネット トラフィックなど、外部ネットワークからの攻撃に対して Azure リソースを保護します。 Azure には、これに対するネイティブ機能が含まれています。
-   Azure Firewall を使用して、インターネットや他の外部の場所からの、悪意がある可能性のあるトラフィックから、アプリケーションやサービスを保護します。 

-   Azure Application Gateway、Azure Front Door、Azure Content Delivery Network (CDN) の Web Application Firewall (WAF) 機能を使用して、アプリケーション層の攻撃からアプリケーション、サービス、API を保護します。

-   Azure 仮想ネットワークで DDoS 標準保護を有効にすることで、DDoS 攻撃から資産を保護します。
-   Azure Security Center を使用して、上記に関連する構成ミスのリスクを検出します。

- [Azure Firewall のドキュメント](../../firewall/index.yml)

- [Azure WAF をデプロイする方法](../../web-application-firewall/overview.md)

- [Azure portal を使用した Azure DDoS Protection Standard の管理](../../ddos-protection/manage-ddos-protection.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-5 | 12.6、12.7 | SI-4 |

Azure Firewall の脅威インテリジェンス ベースのフィルター処理を使用して、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックに対してアラートを送信したりブロックしたりします。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 ペイロード検査が必要な場合は、ペイロード検査機能を備えたサードパーティの侵入検出/侵入防止システム (IDS/IPS) を、Azure Marketplace からデプロイできます。 または、ネットワーク ベースの IDS/IPS と組み合わせて、またはその代わりに、ホスト ベースの IDS/IPS またはホスト ベースのエンドポイント検出と応答 (EDR) ソリューションを、使用することもできます。

注:IDS/IPS の使用に関して規制やその他の要件がある場合は、SIEM ソリューションに高品質のアラートが提供されるように、常に調整されていることを確認します。 

- [Azure Firewall をデプロイする方法l](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace にはサードパーティの IDS 機能が含まれる](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender for Endpoint の機能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: ネットワーク セキュリティ規則を簡略化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

サービス タグとアプリケーション セキュリティ グループ (ASG) を利用することにより、ネットワーク セキュリティ規則を簡略化します。

ネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則のソースまたはターゲット フィールドでサービス タグ名を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

また、アプリケーション セキュリティグループを使用して、複雑なセキュリティ構成を簡略化することもできます。 ネットワーク セキュリティ グループの明示的な IP アドレスに基づいてポリシーを定義する代わりに、アプリケーション セキュリティ グループを使用すると、ネットワーク セキュリティをアプリケーションの構造の自然な拡張として構成でき、仮想マシンをグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。

- [サービス タグの概要と使用](../../virtual-network/service-tags-overview.md)

- [アプリケーション セキュリティ グループの概要と使用](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: セキュリティで保護されたドメイン ネーム サービス (DNS)

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| NS-7 | N/A | SC-20、SC-21 |

DNS セキュリティのベスト プラクティスに従って、未解決の DNS、DNS アンプ攻撃、DNS のポイズニングとスプーフィングなどの一般的な攻撃を軽減します。

権限のある DNS サービスとして Azure DNS が使用されている場合は、Azure RBAC とリソース ロックを使用して、DNS ゾーンとレコードが偶発的または悪意のある変更から保護されていることを確認します。

- [Azure DNS の概要](../../dns/dns-overview.md)

- [セキュリティで保護されたドメイン ネーム システム (DNS) デプロイ ガイド](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する](../fundamentals/subdomain-takeover.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
