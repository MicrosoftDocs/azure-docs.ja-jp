---
title: Azure Cloud Services (延長サポート) に関してよく寄せられる質問
description: Azure Cloud Services (延長サポート) に関してよく寄せられる質問
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3338f7b6bd418cea2bfdbbcd40692b9342f48cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744101"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) に関してよく寄せられる質問
この記事では、Azure Cloud Services (延長サポート) に関してよく寄せられる質問について説明します。

## <a name="general"></a>全般

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Cloud Services (クラシック) と Cloud Services (延長サポート) のリソース名は何ですか?
- Cloud Services (クラシック): `microsoft.classiccompute/domainnames`
- Cloud Services (延長サポート): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Cloud Services (延長サポート) をデプロイできる場所はどこですか?
Cloud Services (延長サポート) は、すべてのパブリック クラウド リージョンで使用できます。

### <a name="how-does-my-quota-change"></a>クォータはどのように変更しますか? 
お客様は、他の Azure Resource Manager 製品と同じプロセスを使用してクォータをリクエストする必要があります。Azure Resource Manager のクォータはリージョン別であり、リージョンごとに個別のクォータ リクエストが必要になります。

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>運用およびステージング スロットが表示されなくなったのはなぜですか?
Cloud Services (延長サポート) では、2 つのスロット (運用とステージング) が含まれていたホステッド サービスの論理的な概念はサポートされません。 各デプロイが、独立した Cloud Service (延長サポート) のデプロイです。 Cloud Service の新しいリリースをテストしてステージングするには、Cloud Service (延長サポート) をデプロイし、別の Cloud Service (延長サポート) との間で VIP スワップ可能としてタグ付けします。

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>空の Cloud Service を作成できなくなったのはなぜですか?
ホステッド サービス名の概念が存在しなくなったため、空の Cloud Service (延長サポート) を作成することはできません。

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Cloud Services (延長サポート) では、リソース正常性チェック (RHC) はサポートされていますか?
いいえ。Cloud Services (延長サポート) では、リソース正常性チェック (RHC) はサポートされていません。

### <a name="how-are-role-instance-metrics-changing"></a>ロール インスタンスのメトリックはどのように変更されますか?
ロール インスタンスのメトリックに変更はありません。 

### <a name="how-are-web--worker-roles-changing"></a>Web および worker ロールはどのように変更されますか?
Web および worker ロールの設計、アーキテクチャ、コンポーネントに変更はありません。 

### <a name="how-are-role-instances-changing"></a>ロール インスタンスはどのように変更されますか?
ロール インスタンスの設計、アーキテクチャ、コンポーネントに変更はありません。 

### <a name="how-will-guest-os-updates-change"></a>ゲスト OS 更新プログラムはどのように変更されますか?
 ロールアウト方法に変更はありません。 Cloud Services (クラシック) と Cloud Services (延長サポート) では、同じ更新プログラムが取得されます。
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Cloud Services (延長サポート) では、停止済み、割り当て済みと停止済み、割り当て解除済みの各状態はサポートされますか?

Cloud Services (延長サポート) のデプロイでは、停止済み、割り当て済み状態のみサポートされます。これは、Azure portal では "停止済み" と表示されます。 停止済み、割り当て解除済み状態はサポートされていません。 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Cloud Services (延長サポート) のデプロイでは、クラスター、可用性ゾーン、リージョン間のスケーリングはサポートされますか?
Cloud Services (延長サポート) のデプロイでは、複数のクラスター、可用性ゾーン、リージョン間のスケーリングはできません。 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Cloud Services (クラシック) と Cloud Services (延長サポート) の間に価格の違いはありますか?
Cloud Services (延長サポート) では、Azure Key Vault と基本 (ARM) パブリック IP アドレスが使用されます。 証明書を必要とするお客様は、証明書の管理に Azure Key Vault を使用する必要があります (Azure Key Vault の価格の[詳細をご確認ください](https://azure.microsoft.com/pricing/details/key-vault/))。 Cloud Services (延長サポート) の各パブリック IP アドレスは個別に課金されます (パブリック IP アドレスの価格の[詳細をご確認ください](https://azure.microsoft.com/pricing/details/ip-addresses/))。 
## <a name="resources"></a>リソース 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Cloud Services (延長サポート) のデプロイにリンクされているリソースの中で、同じリソース グループに配置する必要があるのはどれですか?
ロード バランサー、ネットワーク セキュリティ グループ、ルート テーブルは、同じリージョンおよびリソース グループに配置する必要があります。 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Cloud Services (延長サポート) のデプロイにリンクされているリソースの中で、同じリージョンに配置する必要があるのはどれですか?
Key Vault、仮想ネットワーク、パブリック IP アドレス、ネットワーク セキュリティ グループ、ルート テーブルは、同じリージョンに配置する必要があります。

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Cloud Services (延長サポート) のデプロイにリンクされているリソースの中で、同じ仮想ネットワークに配置する必要があるのはどれですか?
パブリック IP アドレス、ロード バランサー、ネットワーク セキュリティ グループ、ルート テーブルは、同じ仮想ネットワークに配置する必要があります。 

## <a name="deployment-files"></a>デプロイ ファイル 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>テンプレートを使用してデプロイを実行または管理するには、どのようにすればよいですか?
REST、PowerShell、CLI を使用して、テンプレートとパラメーターのファイルをパラメーターとして渡すことができます。 また、Azure portal を使用して、これらをアップロードすることもできます。  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>現在、4 つのファイル (テンプレート、パラメーター、csdef、cscfg) を管理する必要がありますか? (テンプレート、パラメーター、csdef、cscfg)
テンプレートとパラメーターのファイルは、デプロイの自動化にのみ使用されます。 Cloud Services (クラシック) と同様に、最初に依存リソースを手動で作成し、次に PowerShell、CLI コマンドを使用して、またはポータルで既存の csdef、cscfg を使用して、Cloud Services (延長サポート) のデプロイを作成できます。

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Cloud Services (延長サポート) では、アプリケーション コードはどのように変更されますか?
cspkg にパッケージされているアプリケーション コードに必要な変更はありません。 既存のアプリケーションは、以前と同じように引き続き動作します。 


## <a name="migration"></a>移行

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Cloud Services (延長サポート) では、割り当ての失敗による障害は軽減されますか?
いいえ。Cloud Service (延長サポート) のデプロイは、Cloud Services (クラシック) と同様にクラスターに関連付けられています。 そのため、クラスターがいっぱいになった場合、割り当ての失敗は引き続き発生します。 

### <a name="when-do-i-need-to-migrate"></a>いつ移行する必要がありますか? 
移行に必要な時間と複雑さの見積もりは、さまざまな不確定要素によって異なります。 移行の作業範囲、阻害要因、複雑さを理解するためには、計画を立てることが最も効果的な手順です。

## <a name="networking"></a>ネットワーク

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>仮想ネットワークを使用せずにデプロイを作成できないのはなぜですか?
仮想ネットワークは、Azure Resource Manager でのすべてのデプロイに必要なリソースです。 Cloud Services (延長サポート) のデプロイは、仮想ネットワーク内に配置する必要があります。 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>多くのネットワーク リソースが表示されるようになったのはなぜですか? 
Azure Resource Manager では、可視性と制御を向上させるために、お使いの Cloud Services (延長サポート) のデプロイのコンポーネントがリソースとして公開されます。 同じ種類のリソースが Cloud Services (クラシック) で使用されていましたが、非表示になっていました。 このようなリソースの 1 つの例として、パブリック ロード バランサーがあります。これは、現在、プラットフォームによって自動的に作成される明示的な "読み取り専用" リソースです。

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Cloud Services (延長サポート) に対するサブネットにはどのような制限が適用されますか?
Cloud Services (延長サポート) のデプロイを含むサブネットは、Virtual Machines、Virtual Machine Scale Sets、Service Fabric など、他のコンピューティング製品のデプロイとは共有できません。

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Cloud Services (延長サポート) では、どのような IP 割り当て方法がサポートされていますか?
Cloud Services (延長サポート) では、動的および静的 IP 割り当て方法がサポートされています。 静的 IP アドレスは、cscfg ファイルで予約済み IP として参照されます。

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>IP アドレスに対して課金されるのはなぜですか?
お客様は、ユーザーが仮想マシンに関連する IP アドレスについて課金されるのと同じように、Cloud Services (延長サポート) での IP アドレスの使用について課金されます。 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Cloud Services (延長サポート) で DNS 名を使用できますか? 
はい。 Cloud Services (延長サポート) に DNS 名を付けることもできます。 Azure Resource Manager では、DNS ラベルは、Cloud Service に割り当てられるパブリック IP アドレスのオプション プロパティです。 Azure Resource Manager ベースのデプロイの DNS 名の形式は、`<userlabel>.<region>.cloudapp.azure.com` です。

## <a name="certificates--key-vault"></a>証明書と Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Cloud Services (延長サポート) で証明書を管理する必要があるのはなぜですか?
Cloud Services (延長サポート) では、カスタマー マネージド Key Vault 内に証明書が格納される他のコンピューティング オファリングと同じプロセスが採用されています。 これにより、お客様はシークレットと証明書を完全に制御できます。 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>すべてのリージョンのすべてのデプロイに対して 1 つの Key Vault を使用できますか?
いいえ。 Key Vault はリージョン別のリソースであるため、お客様は、リージョンごとに 1 つの Key Vault を使用する必要があります。 ただし、1 つの Key Vault を、特定のリージョン内のすべてのデプロイに使用することは可能です。

## <a name="next-steps"></a>次のステップ
Cloud Services (延長サポート) の使用を開始するには、[PowerShell を使用した Cloud Service (延長サポート) のデプロイ](deploy-powershell.md)に関する記事を参照してください。