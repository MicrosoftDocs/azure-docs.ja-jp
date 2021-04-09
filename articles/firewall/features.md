---
title: Azure Firewall の機能
description: Azure Firewall の機能について説明します
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: victorh
ms.openlocfilehash: 21bb1856409b7fbea1eeffb8b3769dd63119da50
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612849"
---
# <a name="azure-firewall-features"></a>Azure Firewall の機能

[Azure Firewall](overview.md) は、Azure 仮想ネットワーク リソースを保護する、クラウドベースのマネージド ネットワーク セキュリティ サービスです。

![ファイアウォールの概要](media/overview/firewall-threat.png)

Azure Firewall には次の機能が含まれています。

- 組み込みの高可用性
- 可用性ゾーン
- クラウドによる無制限のスケーラビリティ
- アプリケーションの FQDN のフィルタリング規則
- ネットワーク トラフィックのフィルタリング規則
- FQDN のタグ
- サービス タグ
- 脅威インテリジェンス
- 送信 SNAT サポート
- 受信 DNAT のサポート
- 複数のパブリック IP アドレス
- Azure Monitor ログ記録
- 強制トンネリング
- Web カテゴリ (プレビュー)
- 認定資格

## <a name="built-in-high-availability"></a>組み込みの高可用性

高可用性が組み込まれているため、追加のロード バランサーは必要なく、構成すべきものもありません。

## <a name="availability-zones"></a>可用性ゾーン

Azure Firewall は、デプロイ時に、可用性を高めるために複数の可用性ゾーンにまたがるように構成できます。 Availability Zones を使用すると、可用性が高まり 99.99% のアップタイムが実現します。 詳細については、Azure Firewall の[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) に関するページをご覧ください。 2 つ以上の可用性ゾーンを選択すると、稼働率 99.99% の SLA が提供されます。

サービス標準の 99.95% の SLA を使用して、近接性の理由から Azure Firewall を特定のゾーンに関連付けることもできます。

Availability Zones にデプロイされるファイアウォールについては追加のコストは発生しません。 ただし、Availability Zones に関連する受信および送信データ転送については追加のコストが発生します。 詳細については、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。

Azure Firewall Availability Zones は、Availability Zones をサポートするリージョンで利用できます。 詳細については、「[Azure で Availability Zones がサポートされるリージョン](../availability-zones/az-region.md)」を参照してください

> [!NOTE]
> Availability Zones は、デプロイ時にのみ構成できます。 既存のファイアウォールを構成して Availability Zones を含めることはできません。

Availability Zones の詳細については、「[Azure のリージョンと Availability Zones](../availability-zones/az-overview.md)」を参照してください

## <a name="unrestricted-cloud-scalability"></a>クラウドによる無制限のスケーラビリティ

Azure Firewall では、必要に応じてスケールアップしてネットワーク トラフィック フローの変化に対応できるので、ピーク時のトラフィックを処理するために予算を立てる必要がありません。

## <a name="application-fqdn-filtering-rules"></a>アプリケーションの FQDN のフィルタリング規則

ワイルド カードも含まれる完全修飾ドメイン名 (FQDN) の指定された一覧に、送信 HTTP/S トラフィックまたは Azure SQL トラフィックを制限できます。 この機能に TLS 終了は必要ありません。

## <a name="network-traffic-filtering-rules"></a>ネットワーク トラフィックのフィルタリング規則

送信元と送信先の IP アドレス、ポート、プロトコルを基準として、"*許可*" または "*拒否*" のネットワーク フィルタリング規則を一元的に作成できます。 Azure Firewall は完全にステートフルであるため、各種の接続の正当なパケットを識別できます。 規則は、複数のサブスクリプションと仮想ネットワークにまたがって適用および記録されます。

## <a name="fqdn-tags"></a>FQDN のタグ

[FQDN のタグ](fqdn-tags.md)により、ファイアウォール経由の既知の Azure サービスのネットワーク トラフィックを簡単に許可することができます。 たとえば、ファイアウォール経由の Windows Update のネットワーク トラフィックを許可したいとします。 アプリケーションの規則を作成して、Windows Update のタグを組み込みます。 これで、Windows Update からのネットワーク トラフィックをファイアウォール経由でフローできるようになります。

## <a name="service-tags"></a>サービス タグ

[サービス タグ](service-tags.md)は IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。 独自のサービス タグを作成することも、タグ内に含まれる IP アドレスを指定することもできません。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

## <a name="threat-intelligence"></a>脅威インテリジェンス

ファイアウォール用に[脅威インテリジェンス](threat-intel.md)ベースのフィルター処理を有効にして、既知の悪意のある IP アドレスやドメインとの間のトラフィックの警告と拒否を行うことができます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

## <a name="outbound-snat-support"></a>送信 SNAT サポート

仮想ネットワーク トラフィックの送信 IP アドレスはすべて Azure Firewall パブリック IP に変換されます (送信元ネットワーク アドレス変換)。 仮想ネットワークからインターネット上のリモートの送信先に向かうトラフィックを特定して許可できます。 宛先 IP が [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) のプライベート IP 範囲である場合、Azure Firewall は SNAT を行いません。 

組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。 パブリック IP アドレス範囲の SNAT が **行われない** ように、Azure Firewall を構成することができます。 詳細については、「[Azure Firewall の SNAT プライベート IP アドレス範囲](snat-private-range.md)」を参照してください。

## <a name="inbound-dnat-support"></a>受信 DNAT のサポート

ファイアウォールのパブリック IP アドレスへのインバウンド インターネット ネットワーク トラフィックは、変換され (宛先ネットワーク アドレス変換)、仮想ネットワークのプライベート IP アドレスでフィルター処理されます。

## <a name="multiple-public-ip-addresses"></a>複数のパブリック IP アドレス

[複数のパブリック IP アドレス](deploy-multi-public-ip-powershell.md) (最大 250) をファイアウォールに関連付けることができます。

これにより、次のシナリオが実現します。

- **DNAT** - 複数の標準ポート インスタンスをバックエンド サーバーに変換できます。 たとえば、2 つのパブリック IP アドレスがある場合、両方の IP アドレス用の TCP ポート 3389 (RDP) を変換できます。
- **SNAT** - 送信 SNAT 接続にさらにポートを使用できるので、SNAT ポートが不足する可能性が低減されます。 現時点では、Azure Firewall は、接続に使用する送信元パブリック IP アドレスをランダムに選択します。 ネットワークにダウンストリーム フィルターがある場合、ファイアウォールに関連付けられているすべてのパブリック IP アドレスを許可する必要があります。 この構成を簡略化するには、[パブリック IP アドレス プレフィックス](../virtual-network/public-ip-address-prefix.md)を使用することを検討してください。

## <a name="azure-monitor-logging"></a>Azure Monitor ログ記録

すべてのイベントは Azure Monitor と統合されます。そのため、ログをストレージ アカウントにアーカイブしたり、イベントをイベント ハブにストリーム配信したり、それらを Azure Monitor ログに送信したりできます。 Azure Monitor ログのサンプルについては、「[Azure Firewall の Azure Monitor ログ](./firewall-workbook.md)」をご覧ください。

詳細については、[「Azure Firewall のログとメトリックを監視する](./firewall-diagnostics.md)」を参照してください。 

Azure Firewall ブックにより、Azure Firewall のデータ分析のための柔軟なキャンバスが提供されます。 これを使用して、Azure portal 内で高度な視覚的レポートを作成できます。 詳細については、「[Azure Firewall ブックを使用してログを監視する](firewall-workbook.md)」をご覧ください。

## <a name="forced-tunneling"></a>強制トンネリング

インターネットへのすべてのトラフィックを、インターネットに直接送信するのではなく、指定された次ホップにルーティングするように、Azure Firewall を構成することができます。 たとえば、インターネットに渡す前にネットワーク トラフィックを処理するために、オンプレミスのエッジ ファイアウォールや他のネットワーク仮想アプライアンス (NVA) があるような場合です。 詳細については、「[Azure Firewall 強制トンネリング](forced-tunneling.md)」を参照してください。

## <a name="web-categories-preview"></a>Web カテゴリ (プレビュー)

Web カテゴリでは、管理者は、ギャンブルの Web サイトやソーシャル メディアの Web サイトなどの Web サイト カテゴリへのユーザーのアクセスを許可または拒否できます。 Web カテゴリは Azure Firewall Standard に含まれていますが、Azure Firewall Premium プレビューではさらに細かく調整されています。 Standard SKU の FQDN に基づくカテゴリと一致する Web カテゴリの機能とは異なり、Premium SKU では HTTP と HTTPS の両方のトラフィックの URL 全体に従ってカテゴリと一致します。 Azure Firewall Premium プレビューの詳細については、[Azure Firewall Premium プレビューの機能](premium-features.md)に関するページを参照してください。

たとえば、Azure Firewall が `www.google.com/news` の HTTPS 要求をインターセプトする場合、次のような分類が必要です。 

- Firewall Standard - FQDN 部分のみが検証されるため、`www.google.com` は "*検索エンジン*" として分類されます。 

- Firewall Premium - URL 全体が検証されるため、`www.google.com/news` は "*ニュース*" として分類されます。

カテゴリは、 **[責任]** 、 **[高帯域幅]** 、 **[ビジネス利用]** 、 **[生産性の低下]** 、 **[一般的なネット サーフィン]** 、 **[未分類]** の下で重要度に基づいて整理されています。

### <a name="categorization-change"></a>分類の変更

次の場合に、分類の変更を要求できます。

 - FQDN または URL が別のカテゴリの下にある必要があると思われる 
 
or 

- 分類されていない FQDN または URL に対して推奨されるカテゴリがある

[https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) で要求を送信してください。

### <a name="category-exceptions"></a>カテゴリの例外

Web カテゴリの規則の例外を作成できます。 規則コレクション グループ内で、より優先度の高い個別の許可または拒否の規則コレクションを作成します。 たとえば、優先度が 100 で `www.linkedin.com` を許可する規則コレクションと、優先順位が 200 で **ソーシャル ネットワーキング** を拒否する規則コレクションを構成できます。 これにより、定義済みの **ソーシャル ネットワーキング** Web カテゴリに例外が作成されます。



## <a name="certifications"></a>認定

Azure Firewall は、Payment Card Industry (PCI)、Service Organization Controls (SOC)、国際標準化機構 (ISO)、および ICSA Labs に準拠しています。 詳細については、「[Azure Firewall のコンプライアンス認定資格](compliance-certifications.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall Premium プレビューの機能](premium-features.md)