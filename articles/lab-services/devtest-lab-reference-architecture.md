---
title: エンタープライズ向け Azure DevTest Labs の参照アーキテクチャ
description: この記事では、企業内の Azure DevTest Labs の参照アーキテクチャに関するガイダンスを提供します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: bcb154f7cffb92ef23fc2606e1f604bb12f8d1a3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996613"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Azure DevTest Labs - エンタープライズ向け参照アーキテクチャ
この記事では、企業内の Azure DevTest Labs に基づくソリューションをデプロイするための参照アーキテクチャを提供します。 これには、Express Route を介したオンプレミスの接続性、仮想マシンにリモートでサインインするためのリモート デスクトップ ゲートウェイ、プライベート成果物用の成果物リポジトリへの接続性、ラボ内で使用されるその他の PaaS サービスが含まれています。

![参照アーキテクチャ](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>アーキテクチャ
参照アーキテクチャの重要な要素は次のとおりです。

- **Azure Active Directory (AAD)**:Azure DevTest Labs では、[ID 管理用の Azure Active Directory サービス](../active-directory/fundamentals/active-directory-whatis.md)を使用します。 DevTest Labs に基づく環境へのアクセス権をユーザーに付与する際に考慮する 2 つの重要事項があります。
    - **リソース管理**:リソースを管理 (仮想マシンの作成、環境の作成、開始/停止/再起動/削除/applyartifacts など) するための Azure portal へのアクセス権を付与します。 これは、ロール ベースのアクセス制御 (RBAC) を使用し、ユーザーへのロールの割り当て、リソースの設定、およびアクセス レベルのアクセス許可を適用することで、Azure 内で行われます。
    - **仮想マシン (ネットワーク レベル)**:既定の構成では、仮想マシンにはローカル管理者アカウントが使用されます。  使用可能なドメイン ([AAD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)、オンプレミスのドメイン、またはクラウドベースのドメイン) がある場合は、マシンをドメインに参加させることができます。 参加すると、ユーザーはドメインベースの ID を使用して VM に接続します。
- **オンプレミスの接続**:上記のアーキテクチャ図では、[Express Route](../expressroute/expressroute-introduction.md) が使用されていますが、[サイト間 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) を使用することもできます。 これは DevTest Labs では必須ではありませんが、企業ではよく見られます。 会社のリソースに接続する理由がある場合にのみ必須です。 一般的な理由は次のとおりです。 
    - まだクラウドに移行できないオンプレミスのデータ
    - ラボの仮想マシンをオンプレミスのドメインに参加させる優先設定
    - セキュリティ/コンプライアンス上の理由から、クラウド環境との間のすべてのネットワーク トラフィックを強制的にオンプレミスのファイアウォール経由にする
- **ネットワーク セキュリティ グループ**:ソースと宛先の IP アドレスに基づいてクラウド環境への (またはクラウド環境内での) トラフィックを制限する一般的な方法は、[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)を使用することです。 たとえば、企業ネットワークからラボのネットワークに送信されたネットワーク トラフィックのみを許可します。
- **リモート デスクトップ ゲートウェイ**:通常、企業は企業のファイアウォールで発信リモート デスクトップ接続をブロックします。 DevTest Labs 内でクラウドベース環境への接続を有効にするには、[リモート デスクトップ ゲートウェイ](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)の使用 (ゲートウェイ ロード バランサーの静的 IP アドレスをホワイトリストに登録) や、Express Route/サイト間 VPN 接続上に[すべての着信 RDP トラフィックを転送](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)するなどの複数のオプションがあります。 これは、企業内での DevTest Labs のデプロイを計画する際の一般的な考慮事項です。
- **Azure のネットワーク (VNet、サブネット)**:[Azure のネットワーク](../networking/networking-overview.md) トポロジも、DevTest Labs の全体的なアーキテクチャの重要な要素です。 これは、ラボからのリソースが通信できる (またはできない)、オンプレミスにアクセスできる (またはできない)、およびインターネットにアクセスできる (またはできない) ようにします。 アーキテクチャ図には、お客様が DevTest Labs を使用する最も一般的な方法 (すべてのラボが[ハブスポーク モデル](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)を使用して [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)を介して Express Route/サイト間 VPN 接続経由でオンプレミスに接続) が含まれていますが、DevTest Labs では Azure のネットワークが直接使用されているので、ネットワーク インフラストラクチャの設定方法に制限はありません。
- **DevTest ラボ**:DevTest Labs は、全体的なアーキテクチャの重要な部分です。 サービスについて詳しくは、[DevTest Labs](devtest-lab-overview.md) に関する記事をご覧ください。
- **仮想マシンとその他のリソース (SaaS、PaaS、IaaS)**:DevTest Labs でサポートされている主要なワークロードの 1 つは、他の Azure リソースを含む仮想マシンです。  DevTest Labs によって、企業はより速く簡単に Azure のリソース (仮想マシンとその他の Azure リソースを含む) へのアクセス権を付与できます。  [開発者](devtest-lab-developer-lab.md)と[テスト担当者](devtest-lab-test-env.md)の Azure へのアクセス権の詳細をご確認ください。

## <a name="scalability-considerations"></a>スケーラビリティに関する考慮事項
DevTest Labs には、組み込みのクォータや制限はありませんが、ラボの通常の運用で使用されるその他の Azure リソースには、[サブスクリプションレベルのクォータ](../azure-subscription-service-limits.md)があります。 その結果、一般的なエンタープライズ デプロイでは、DevTest Labs の大規模なデプロイに対応するための複数の Azure サブスクリプションが必要です。 企業が最もよく到達するクォータは次のとおりです。

- **リソース グループ**:既定の構成では、DevTest Labs により、すべての新しい仮想マシン用またはユーザーがサービスを使用して作成する環境用のリソース グループが作成されます。 サブスクリプションには、[最大 980 のリソース グループ](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)を含めることができるので、この制限はサブスクリプション内の仮想マシンと環境の上限値になります。 検討する必要がある構成は他に 2 つあります。
    - **[すべての仮想マシンが同じリソース グループに入る](resource-group-control.md)**:これはリソース グループの制限に立ちますが、リソース グループごとのリソースの種類の制限に影響します。
    - **共有パブリック IP を使用**:同じサイズと同じリージョンのすべての VM が同じリソース グループに入ります。 仮想マシンにパブリック IP アドレスを使用することが許可されている場合、これは、リソース グループのクォータとリソース グループごとのリソースの種類のクォータの間の '妥協点' です。 
- **リソースの種類ごとのリソース グループあたりのリソース数**:[リソースの種類ごとの、リソース グループあたりのリソース数の既定の制限は 800](../azure-subscription-service-limits.md#resource-group-limits) です。  使用するすべての VM を同じリソース グループ構成に入れる場合、ユーザーがこのサブスクリプション制限に達するのがはるかに早くなります。VM に多くの追加ディスクがある場合には特に早くなります。
- **[ストレージ アカウント]**:DevTest Labs のラボにはストレージ アカウントが付属し、[サブスクリプションごとのリージョンあたりのストレージ アカウント数の Azure のクォータは 250](../azure-subscription-service-limits.md#storage-limits) です。 これは、同じリージョン内の DevTest Labs の数の上限も 250 であることを意味します。
- **ロールの割り当て**:ロールの割り当ては、ユーザーまたはプリンシパルにリソースへのアクセス権を付与する方法です (所有者、リソース、アクセス許可レベル)。 Azure には、[サブスクリプションあたり 2,000 のロールの割り当て制限](../azure-subscription-service-limits.md#role-based-access-control-limits)があります。 (既定の構成の) DevTest Labs サービスでは、各 VM のリソース グループが作成され、所有者には DevTest Labs VM に対する**所有者**アクセス許可とリソース グループに対する**閲覧者**アクセス許可が付与されます。  このようにして、新規に作成された各 VM では、ユーザーにラボへのアクセス許可を付与するときに作成されたロールの割り当てに対する 2 つの追加のロール割り当てが使用されます。
- **API 読み取り/書き込み**:Azure と DevTest Labs を自動化するさまざまな方法 (REST API、PowerShell、CLI、Azure SDK など) があり、自動化を使用すると API 要求で別の上限に達する可能性はありません。 各サブスクリプションでは、[1 時間あたり最大 12,000 の読み取り要求と 1,200 の書き込み要求](../azure-resource-manager/resource-manager-request-limits.md)が可能です。  これは、DevTest Labs を自動化する際に認識しておくべき制限です。

## <a name="manageability-considerations"></a>管理容易性に関する考慮事項
DevTest Labs には、単一のラボ内で使用する場合に優れた管理ユーザー インターフェイスがあります。 企業には、複数の Azure サブスクリプションと多くのラボがある可能性があります。 これは、すべてのラボに一貫して変更を加えるにはスクリプト/自動化が必要であることを意味します。  DevTest Labs のデプロイの管理のいくつかの例と最適な方法を次に示します。

- **ラボ設定への変更**:一般的なシナリオでは、デプロイ内のすべてのラボで特定のラボの設定を更新します。 たとえば、新しい VM インスタンス サイズが使用可能になった場合、それを使用するにはすべてのラボを更新する必要があります。  Azure PowerShell スクリプト、Azure CLI、または REST API を使用してこれらの変更を自動化するのが最適です。  
- **成果物リポジトリの個人用アクセス トークン**:通常、Git リポジトリの個人用アクセス トークンには期限があります (90 日、1 年、2 年)。 継続性を確保するには、個人用アクセス トークンを拡張するか、または新しく作成し、自動化を使用してすべてのラボに新しい個人用アクセス トークンを適用することが重要です。
- **ラボ設定への変更の制限**:制限する必要がある特定の設定 (たとえば、Marketplace イメージの使用を許可) があるケースがよくあります。 これを行うには、Azure のポリシー (そのリソースの種類への変更を防止) を使用するか、カスタム ロールを作成し、'所有者' の代わりにそのロールをラボに付与します。 これは、ラボのほとんどの設定 (内部サポート、ラボのお知らせ、許可される VM サイズなど) に対して行うことができます
- **VM が名前付け規則に従う必要がある**:クラウドベースの開発およびテスト環境の一部である VM を簡単に識別することは一般的な要求です。 これは [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns) を使用して行うことができます。

Azure 内で同じ方法で管理されている、Azure 内の基になるリソース (ネットワーク、ディスク、コンピューティングなど) が DevTest Labs によって使用されることに注意することが重要です。  たとえば、Azure ポリシーは、ラボ内で作成された仮想マシンに適用されます。 Azure Security Center では、VM のコンプライアンスについてレポートできます。 Azure Backup サービスでは、ラボ内の VM の定期的なバックアップなどを提供できます。 

## <a name="security-considerations"></a>セキュリティに関する考慮事項
Azure DevTest Labs では、Azure 内の既存のリソース (コンピューティング、ネットワークなど) を使用するので、プラットフォームに組み込まれたすべての優れたセキュリティ機能を自動的に利用できます。 たとえば、企業ネットワークからのみ発信するように着信リモート デスクトップ接続をセキュリティで保護するには、リモート デスクトップ ゲートウェイ上の仮想ネットワークにネットワーク セキュリティ グループを追加するだけなので簡単です。 Azure DevTest Labs に関する追加のセキュリティの考慮事項は、毎日ラボを使用するチーム メンバーに提供されるアクセス許可のレベルだけです。  一般に付与されるアクセス許可は ["所有者" と "DevTest Labs ユーザー"](devtest-lab-add-devtest-user.md) です。 これらのロールについて詳しくは、「[Azure DevTest Labs での所有者とユーザーの追加](devtest-lab-add-devtest-user.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
このシリーズの次の記事をご覧ください。[Azure DevTest Labs インフラストラクチャをスケールアップする](devtest-lab-guidance-scale.md)
