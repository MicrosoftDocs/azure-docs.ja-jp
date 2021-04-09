---
title: Azure Security Center での追加の脅威の防止
description: Azure Defender を越えて Azure Security Center から利用できる脅威の防止について学習します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 5b9e9f5cc5184a083f45999eaeb031eb83b17c58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754336"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Azure Security Center での追加の脅威の防止
組み込みの [Azure Defender の保護](azure-defender.md)に加えて、Azure Security Center によって次の脅威の防止機能も提供されています。

> [!TIP]
> Security Center の脅威の防止機能を有効にするには、Azure Defender を、適用可能なワークロードを含むサブスクリプションで有効にする必要があります。
>
> **Azure Database for MariaDB、MySQL、PostgreSQL** の脅威の防止は、リソース レベルでのみ有効にできます。


## <a name="threat-protection-for-azure-network-layer"></a>Azure ネットワーク レイヤーの脅威の防止 <a name="network-layer"></a>
Security Center のネットワーク レイヤー分析は、サンプルの [IPFIX データ](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)に基づくものであり、Azure コア ルーターによって収集されるパケット ヘッダーです。 このデータ フィードに基づき、Security Center は機械学習モデルを使用して、悪意のあるトラフィック アクティビティを特定し、それらにフラグを設定します。 IP アドレスを強化するために、Security Center では、Microsoft の脅威インテリジェンス データベースも使用します。

Security Center が疑わしいネットワーク アクティビティに対してアラートを生成することを制限するネットワーク構成もあります。 Security Center でネットワーク アラートを生成するには、次のことを確認してください。
- 仮想マシンにパブリック IP アドレスがある (または、仮想マシンがパブリック IP アドレスを持つロード バランサー上にある)。
- 仮想マシンのネットワーク エグレス トラフィックが、外部 ID ソリューションによってブロックされていない。

Azure ネットワーク レイヤー アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurenetlayer)に関するページを参照してください。


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Azure Resource Manager の脅威の防止 (プレビュー)<a name ="management-layer"></a>
Azure Resource Manager に基づく Security Center の保護レイヤーは、現在プレビュー段階です。

Security Center では、Azure のコントロール プレーンと見なされる Azure Resource Manager イベントを使用することで、追加の保護レイヤーが提供されます。 Security Center では、Azure Resource Manager のレコードを分析することで、Azure サブスクリプション環境での異常な、または害を及ぼす可能性のある操作を検出します。

Azure Defender for Resource Manager のアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-resourcemanager)を参照してください。


>[!NOTE]
> 上記のいくつかの分析では、Microsoft Cloud App Security が利用されています。 これらの分析を活用するには、Cloud App Security ライセンスをアクティブにする必要があります。 Cloud App Security ライセンスをお持ちの場合、これらのアラートは既定で有効になります。 アラートを無効にするには、次のようにします。
>
> 1. Security Center のメニューから、 **[価格と設定]** を選択します。
> 1. 変更するサブスクリプションを選択します。
> 1. **[脅威検出]** を選択します。
> 1. **[私のデータに Microsoft Cloud App Security がアクセスすることを許可します]** チェック ボックスをオフにして、 **[保存]** を選択します。


>[!NOTE]
>Security Center では、そのリソースと同じ地域でセキュリティ関連の顧客データが格納されます。 Microsoft によってまだリソースの地域に Security Center がデプロイされていない場合、米国でデータが格納されます。 Cloud App Security が有効になっている場合、この情報は、Cloud App Security の地域の場所のルールに従って格納されます。 詳細については、[非リージョン サービスのデータ ストレージ](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。

1. エージェントをインストールするワークスペースを設定します。 ワークスペースが存在するサブスクリプションが Security Center で使用しているサブスクリプションと同じであること、またそのワークスペースに対する読み取り/書き込みのアクセス許可があることを確認します。

1. **Azure Defender** を有効にして、 **[保存]** を選択します。


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB の脅威の防止 (プレビュー)<a name="cosmos-db"></a>

Azure Cosmos DB アラートは、通常とは異なる、害を及ぼす可能性がある、Azure Cosmos DB アカウントへのアクセスや悪用が試行された場合に生成されます。

詳細については、次を参照してください。

* [Azure Cosmos DB の Advanced Threat Protection (プレビュー)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 向け脅威保護アラートの一覧 (プレビュー)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>他の Microsoft サービスの脅威の防止 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF の脅威の防止 <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、Open Web Application Security Project のコア ルール セット 3.0 または 2.2.9 に基づいています。 WAF は、新たな脆弱性から保護するために自動的に更新されます。 

Azure WAF のライセンスをお持ちの場合は、WAF アラートが Security Center にストリーミングされます。その際に追加の構成は必要ありません。 WAF によって生成されるアラートの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)に関する記事をご覧ください。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS Protection の脅威の防止 <a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 セキュリティの大きな不安材料になっており、アプリケーションをクラウドに移行する場合は特にそうです。 

DDoS 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる可能性があります。

DDoS 攻撃を防ぐには、Azure DDoS Protection のライセンスを購入し、アプリケーションの設計に関するベスト プラクティスに従っていることを確認します。 DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](../ddos-protection/ddos-protection-overview.md)に関する記事を参照してください。

Azure DDoS Protection アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureddos)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
これらの脅威の防止機能からのセキュリティ アラートの詳細については、次の記事を参照してください。

* [Azure Security Center のすべてのアラートの参照テーブル](alerts-reference.md)
* [Security alerts in Azure Security Center](security-center-alerts-overview.md)
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)
* [継続的に Security Center データをエクスポートする](continuous-export.md)