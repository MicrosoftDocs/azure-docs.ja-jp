---
title: Azure DevTest Labs の企業向け参照アーキテクチャ
description: この記事では、企業内の Azure DevTest Labs の参照アーキテクチャに関するガイダンスを提供します。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 29f739c2fb9dd1cc58bf6c400eeee1bebb6243c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328846"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs の企業向け参照アーキテクチャ
この記事では、企業内の Azure DevTest Labs に基づくソリューションをデプロイするための参照アーキテクチャを提供します。 含まれる内容は次のとおりです。
- Azure ExpressRoute を介したオンプレミス接続
- 仮想マシンにリモート サインインするリモート デスクトップ ゲートウェイ
- プライベート成果物用の成果物リポジトリへの接続
- ラボで使用されるその他の PaaS サービス

![参照アーキテクチャ図](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architecture
参照アーキテクチャの重要な要素は次のとおりです。

- **Azure Active Directory (Azure AD)** :DevTest Labs では、[ID 管理用の Azure AD サービス](../active-directory/fundamentals/active-directory-whatis.md)を使用します。 DevTest ラボに基づく環境へのアクセスをユーザーに許可する場合は、次の 2 つの重要な側面を考慮します。
    - **リソース管理**:リソースを管理 (仮想マシンの作成、環境の作成、成果物の開始、停止、再起動、削除、適用など) するための Azure portal へのアクセス権を付与します。 リソース管理には、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。 ユーザーにロールを割り当て、リソースとアクセス レベルのアクセス許可を設定します。
    - **仮想マシン (ネットワーク レベル)** :既定の構成では、仮想マシンにはローカル管理者アカウントが使用されます。 使用できるドメイン ([Azure AD Domain Services](../active-directory-domain-services/overview.md)、オンプレミスのドメイン、またはクラウドベースのドメイン) がある場合は、マシンをドメインに参加させることができます。 これでユーザーは自分のドメイン ベースの ID を使用して VM に接続できるようになります。
- **オンプレミスの接続**:このアーキテクチャ図では、[ExpressRoute](../expressroute/expressroute-introduction.md) が使用されています。 ただし、[サイト間 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) を使用することもできます。 ExpressRoute は DevTest Labs に必須ではありませんが、企業ではよく使用されています。 ExpressRoute は、企業リソースにアクセスする必要がある場合にのみ必要です。 一般的なシナリオを次に示します。
    - まだクラウドに移行できないオンプレミスのデータがある。
    - ラボの仮想マシンをオンプレミスのドメインに参加させたい。
    - セキュリティ/コンプライアンスのため、クラウド環境との間のすべてのネットワーク トラフィックを強制的にオンプレミスのファイアウォール経由にしたい。
- **ネットワーク セキュリティ グループ**:ソースと宛先の IP アドレスに基づいてクラウド環境への (またはクラウド環境内での) トラフィックを制限する一般的な方法は、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)を使用することです。 たとえば、企業ネットワークからラボのネットワークに送信されたトラフィックのみを許可します。
- **リモート デスクトップ ゲートウェイ**:通常、企業は企業のファイアウォールで発信リモート デスクトップ接続をブロックします。 DevTest Labs でクラウドベースの環境への接続を有効にする方法は、次のようにいくつかあります。
  - [リモート デスクトップ ゲートウェイ](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)を使用し、ゲートウェイ ロード バランサーの静的 IP アドレスを許可します。
  - ExpressRoute/サイト間 VPN 接続経由で[すべての受信 RDP トラフィック を転送](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)します。 企業が DevTest Labs のデプロイを計画する場合、この機能はよく見られる考慮事項です。
- **ネットワーク サービス (仮想ネットワーク、サブネット)** :[Azure のネットワーク](../networking/networking-overview.md) トポロジも、DevTest Labs アーキテクチャの重要な要素です。 これで、オンプレミスやインターネットとの通信やアクセスをラボのリソースに許可するかどうかを制御します。 このアーキテクチャ図には、お客様の最も一般的な DevTest Labs の使用方法が含まれています。すべてのラボは、オンプレミスへの ExpressRoute/サイト間 VPN 接続に[ハブスポーク モデル](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) を使用し、[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を介して接続しています。 ただし、DevTest Labs は Azure Virtual Network を直接使用するため、ネットワーク インフラストラクチャの設定方法に制限はありません。
- **DevTest ラボ**:DevTest Labs は、全体的なアーキテクチャの重要な部分です。 サービスの詳細については、[DevTest Labs](devtest-lab-overview.md) に関する記事を参照してください。
- **仮想マシンとその他のリソース (SaaS、PaaS、IaaS)** :仮想マシンは、DevTest Labs で他の Azure リソースと共にサポートされている主要なワークロードです。 DevTest Labs によって、企業はより速く簡単に Azure のリソース (仮想マシンとその他の Azure リソースを含む) へのアクセス権を付与できます。 [開発者](devtest-lab-developer-lab.md)と[テスト担当者](devtest-lab-test-env.md)の Azure へのアクセス権の詳細をご確認ください。

## <a name="scalability-considerations"></a>スケーラビリティに関する考慮事項
DevTest Labs には、組み込みのクォータや制限はありませんが、ラボの通常の運用で使用されるその他の Azure リソースには、[サブスクリプションレベルのクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)があります。 そのため、一般的なエンタープライズ デプロイでは、DevTest Labs の大規模なデプロイに対応するための複数の Azure サブスクリプションが必要です。 企業が最もよく到達するクォータは次のとおりです。

- **リソース グループ**:既定の構成では、DevTest Labs により、すべての新しい仮想マシン用またはユーザーがサービスを使用して作成する環境用のリソース グループが作成されます。 サブスクリプションには、[最大 980 個のリソース グループ](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)を含めることができます。 つまり、これがサブスクリプション内の仮想マシンと環境の制限です。 検討する必要がある構成は他に 2 つあります。
    - **[すべての仮想マシンが同じリソース グループに入る](resource-group-control.md)** :この設定はリソース グループの制限を満たすために役立ちますが、リソース グループごとのリソースの種類の制限に影響します。
    - **共有パブリック IP を使用**:サイズとリージョンが同じすべての VM が同じリソース グループに入ります。 仮想マシンにパブリック IP アドレスを使用することが許可されている場合、この構成はリソース グループのクォータとリソース グループごとのリソースの種類のクォータの間の '妥協点' です。
- **リソースの種類ごとのリソース グループあたりのリソース数**:[リソースの種類ごとの、リソース グループあたりのリソース数の既定の制限は 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) です。  使用する *すべての VM を同じリソース グループ構成に入れる* 場合、ユーザーがこのサブスクリプション制限に達するのがはるかに早くなります。VM に多くの追加ディスクがある場合には特に早くなります。
- **[ストレージ アカウント]** :DevTest Labs の 1 つのラボには 1 つのストレージ アカウントが付属しています。 [サブスクリプションあたり/リージョンあたりのストレージ アカウント数の Azure のクォータは 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) です。 同じリージョン内の DevTest Labs の最大数も 250 です。
- **ロールの割り当て**:ロールの割り当ては、ユーザーまたはプリンシパルにリソースへのアクセス権を付与する方法です (所有者、リソース、アクセス許可レベル)。 Azure には、[サブスクリプションあたり 2,000 のロールの割り当て制限](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits)があります。 既定で DevTest Labs サービスでは VM ごとに 1 つのリソース グループが作成されます。 所有者には、DevTest Labs VM に対する "*所有者*" アクセス許可と、リソース グループに対する "*閲覧者*" アクセス許可が付与されます。 このようにして、ユーザーにラボへのアクセス許可を付与するときに使用される割り当てに加え、作成する新しい VM ごとに 2 つのロールの割り当てが使用されます。
- **API 読み取り/書き込み**:REST API、PowerShell、Azure CLI、Azure SDK など、Azure と DevTest Labs を自動化するさまざまな方法があります。 自動化によって、API 要求に別の制限が課される可能性があります。各サブスクリプションでは、[1 時間あたり最大 12,000 の読み取り要求と 1,200 の書き込み要求](../azure-resource-manager/management/request-limits-and-throttling.md)が可能です。 DevTest Labs を自動化するときは、この制限に注意してください。

## <a name="manageability-considerations"></a>管理容易性に関する考慮事項
DevTest Labs には、単一のラボ内で使用する場合に優れた管理ユーザー インターフェイスがあります。 ただし、企業には、複数の Azure サブスクリプションと多くのラボがある可能性があります。 すべてのラボに一貫して変更を加えるにはスクリプト/自動化が必要です。 DevTest Labs のデプロイに関するいくつかの例と最適な管理方法を次に示します。

- **ラボ設定への変更**:一般的なシナリオでは、デプロイ内のすべてのラボで特定のラボの設定を更新します。 たとえば、新しい VM インスタンス サイズが使用可能になった場合、それを使用するにはすべてのラボを更新する必要があります。 PowerShell スクリプト、CLI、または REST API を使用してこれらの変更を自動化するのが最適です。  
- **成果物リポジトリの個人用アクセス トークン**:通常、Git リポジトリの個人用アクセス トークンは、90 日、1 年、または 2 年で有効期限が切れます。 継続性を確保するために、個人用アクセス トークンを延長するか、新規に作成することが重要です。 次に、自動化を使用して新しい個人用アクセス トークンをすべてのラボに適用します。
- **ラボ設定への変更の制限**:多くの場合は、特定の設定を制限する必要があります (マーケットプレース画像の使用許可など)。 Azure Policy を使用して、リソースの種類への変更を防ぐことができます。 また、カスタム ロールを作成して、ラボの "*所有者*" ロールではなくそのロールをユーザーに付与することもできます。 これは、ラボのほとんどの設定 (内部サポート、ラボのお知らせ、許可される VM サイズなど) に対して行うことができます。
- **VM が名前付け規則に従う必要がある**:管理者が、クラウドベースの開発およびテスト環境の一部である VM を簡単に特定したいと考えることは一般的です。 これは [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns) を使用して実行できます。

DevTest Labs には、ネットワーク、ディスク、コンピューティングなど、同じ方法で管理される基の Azure リソースが使用されていることに注意してください。 たとえば、Azure Policy は、ラボ内で作成された仮想マシンに適用されます。 Azure Security Center では、VM のコンプライアンスについてレポートできます。 また、Azure Backup サービスでは、ラボ内の VM の定期的なバックアップを提供できます。

## <a name="security-considerations"></a>セキュリティに関する考慮事項
Azure DevTest Labs には、Azure の既存のリソース (コンピューティング、ネットワークなど) が使用されます。 そのため、プラットフォームに組み込まれているセキュリティ機能の恩恵を自動的に利用できます。 たとえば、受信リモート デスクトップ接続の発信元を企業ネットワークのみに制限するには、リモート デスクトップ ゲートウェイ上の仮想ネットワークにネットワーク セキュリティ グループを追加するだけなので簡単です。 その他のセキュリティの考慮事項は、日常的にラボを使用するチーム メンバーに付与するアクセス許可のレベルだけです。 最も一般的なアクセス許可は、[*所有者* と *ユーザー*](devtest-lab-add-devtest-user.md)です。 これらのロールについて詳しくは、「[Azure DevTest Labs での所有者とユーザーの追加](devtest-lab-add-devtest-user.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
このシリーズの次の記事をご覧ください。[Azure DevTest Labs インフラストラクチャをスケールアップする](devtest-lab-guidance-scale.md)。