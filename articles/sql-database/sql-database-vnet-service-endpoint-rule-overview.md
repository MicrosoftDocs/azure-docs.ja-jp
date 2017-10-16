---
title: "Azure SQL Database の Virtual Network サービス エンドポイントと規則 | Microsoft Docs"
description: "サブネットを Virtual Network サービス エンドポイントとしてマークします。 その後、仮想ネットワーク規則としてのエインドポイントを Azure SQL Database の ACL に追加します。 SQL Database では、すべての仮想マシンとサブネット上の他のノードからの通信を許可します。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 09/27/2017
ms.author: genemi
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: e4ee69abe0b3b5d594ee191cc8210d25c325efaa
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Azure SQL Database の Virtual Network サービス エンドポイントと規則の使用

*仮想ネットワーク規則*は、Azure SQL Database サーバーが仮想ネットワーク内の特定のサブネットから送信される通信を許可するかどうかを制御する 1 つのファイアウォール セキュリティ機能です。 この記事では、仮想ネットワーク規則機能が、場合によっては Azure SQL Database への通信を安全に許可するための最善の選択になる理由を説明します。

仮想ネットワーク規則を作成するには、まず、参照する規則の[仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]が必要です。

#### <a name="how-to-create-a-virtual-network-rule"></a>仮想ネットワーク規則の作成方法

仮想ネットワーク規則を作成するだけであれば、途中を読み飛ばして、[この記事で後述](#anchor-how-to-by-using-firewall-portal-59j)している手順と説明に進んでください。






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>用語と説明

**仮想ネットワーク:** ユーザーは、お使いの Azure サブスクリプションに関連付けられた仮想ネットワークを保持できます。

**サブネット:** 仮想ネットワークには**サブネット**が含まれます。 保持している任意の Azure 仮想マシン (VM) がサブネットに割り当てられます。 1 つのサブネットには、複数の VM や他のコンピューティング ノードが含まれる場合があります。 お使いの仮想ネットワークの外部にあるコンピューティング ノードは、アクセスを許可するようにセキュリティを構成しない限り、お使いの仮想ネットワークにはアクセスできません。

**Virtual Network サービス エンドポイント:** [Virtual Network サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]は、プロパティ値に 1 つ以上の Azure サービスの種類名が含まれるサブネットです。 この記事では、"SQL Database" という名前の Azure サービスを参照する **Microsoft.Sql** という種類名に注目します。

**仮想ネットワーク規則:** お使いの SQL Database サーバーの仮想ネットワーク規則は、SQL データベース サーバーのアクセス制御リスト (ACL) に記載されているサブネットです。 SQL Database の ACL 内に記載するためには、サブネットに **Microsoft.Sql** という種類名が含まれている必要があります。

仮想ネットワーク規則は、サブネット上にあるどのノードからの通信も許可するように、お使いの SQL Database サーバーに指示します。







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>仮想ネットワーク規則の利点

操作を実行するまで、サブネット上の VM は SQL Database と通信できません。 通信を許可する方法として仮想ネットワーク規則を選択する根拠については、ファイアウォールが提供している競合するセキュリティ オプションと比較対照して議論する必要があります。

#### <a name="a-allow-access-to-azure-services"></a>A. Azure サービスへのアクセス許可

ファイアウォール ペインには、**[Azure サービスへのアクセスを許可]** とラベル付けされた **[オン/オフ]**  ボタンがあります。 **[オン]** 設定は、すべての Azure IP アドレスと Azure サブネットからの通信を許可します。 これらの Azure IP またはサブネットは、ユーザーが所有していない場合もあります。 この **[オン]** 設定は、おそらくは SQL Database に期待する範囲を超えて開かれています。 仮想ネットワーク規則機能によって、さらにきめ細かい制御が提供されます。

#### <a name="b-ip-rules"></a>B. IP 規則

SQL Database のファイアウォールでは、SQL Database への通信が許可される IP アドレス範囲を指定できます。 この方法は、Azure プライベート ネットワークの外部にある安定した IP アドレスに適しています。 しかし、Azure プライベート ネットワーク内にある多数のノードは、*動的* IP アドレスで構成されています。 動的 IP アドレスは、VM が再起動されたときなどに変更される場合があります。 運用環境では、ファイアウォール規則に動的 IP アドレスを指定することは、賢明ではありません。

お使いの VM 用に*静的* IP アドレスを取得することで、IP のオプションを復旧することができます。 詳細については、「[Azure Portal を使用して仮想マシンのプライベート IP アドレスを構成する][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]」をご覧ください。

ただし、静的 IP の方法は管理が困難になる場合があり、まとめて実行すると負荷がかかります。 仮想ネットワーク規則を確立して管理するほうが簡単です。

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. サブネット上に SQL Database を保持することは、まだできません。

Azure SQL Database サーバーが仮想ネットワーク内のサブネット上のノードになった場合、仮想ネットワークはお使いの SQL Database と通信できます。 この場合、仮想ネットワーク規則や IP 規則がなくても、お使いの VM は SQL Database と通信できます。

しかし、2017 年 9 月時点ではまだ、Azure SQL Database サービスは、サブネットに割り当て可能なサービスの範囲には含まれていません。






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>仮想ネットワーク規則の詳細

このセクションでは、仮想ネットワーク規則に関する詳細について、いくつか説明します。

#### <a name="only-one-geographic-region"></a>geography 型の 1 つのリージョンのみ

各 Virtual Network サービス エンドポイントは、1 つの Azure リージョンだけに適用されます。 エンドポイントが他のリージョンを有効にして、サブネットからの通信を許可することはありません。

どの仮想ネットワーク規則も、その基本となるエンドポイントが適用先のリージョンに制限されます。

#### <a name="server-level-not-database-level"></a>データベース レベルではなく、サーバー レベル

各仮想ネットワーク規則は、サーバー上の 1 つの特定のデータベースだけではなく、お使いの Azure SQL Database サーバー全体に適用されます。 言い換えると、仮想ネットワーク規則はデータベース レベルではなく、サーバー レベルに適用されます。

- 対照的に、IP 規則はどちらのレベルにも適用できます。

#### <a name="security-administration-roles"></a>セキュリティ管理ロール

Virtual Network サービス エンドポイントの管理では、セキュリティ ロールが分離されています。 以下の各ロールでは、次の操作が必要です。

- **ネットワーク管理:** &nbsp; エンドポイントをオンにする。
- **データベース管理:** &nbsp; アクセス制御リスト (ACL) を更新して、指定されたサブネットを SQL Database サーバーに追加する。

*RBAC による代替:* 

ネットワーク管理およびデータベース管理のロールには、仮想ネットワーク規則の管理に必要とされる機能以外もあります。 それらの機能のうち 1 つのサブネットだけが必要になります。

必要な機能のサブネットのみを保持する単一のカスタム ロールを作成するために、Azure には[ロールベースのアクセス制御 (RBAC)][rbac-what-is-813s] を使用するオプションがあります。 ネットワーク管理またはデータベース管理に関連付ける代わりに、カスタム ロールを使用できます。カスタム ロールにユーザーを追加する場合と、他の 2 つの主要な管理者ロールにユーザーを追加する場合では、前者の方がセキュリティ脅威にさらされる領域が少なくなります。

#### <a name="limitations"></a>制限事項

Azure SQL Database の場合、仮想ネットワーク規則機能には以下のような制限事項があります。

- 各 Azure SQL Database サーバーは、指定された仮想ネットワークに対して最大 128 個までの ACL エントリを保持できます。

- 仮想ネットワーク規則は[従来のデプロイメント モデル][arm-deployment-model-568f] ネットワークではなく、Azure Resource Manager の仮想ネットワークのみに適用されます。

- ファイアウォールでは、IP アドレスは以下のネットワーク項目に適用されますが、仮想ネットワーク規則は適用されません。
    - [サイト間 (S2S) 仮想プライベート ネットワーク (VPN)][vpn-gateway-indexmd-608y]
    - [ExpressRoute][expressroute-indexmd-744v] 経由のオンプレミス

#### <a name="expressroute"></a>ExpressRoute

ネットワークが [ExpressRoute][expressroute-indexmd-744v] を使用して Azure ネットワークに接続されている場合、各回線は、Microsoft Edge で 2 つのパブリック IP アドレスを使用して構成されています。 2 つの IP アドレスは、Azure パブリック ピアリングを使用して、Azure Storage などの Microsoft サービスへの接続に使用されます。

回線から Azure SQL Database への通信を許可するには、回線のパブリック IP アドレスに対する IP ネットワーク規則を作成する必要があります。 ExpressRoute 回線のパブリック IP アドレスを見つけるには、Azure Portal を使用して ExpressRoute のサポート チケットを開きます。


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="how-to-create-a-virtual-network-rule-by-using-the-portal"></a>ポータルを使用した仮想ネットワークの作成方法

このセクションでは、[Azure Portal][http-azure-portal-link-ref-477t] を使用して、お使いの Azure SQL Database に*仮想ネットワーク規則*を作成する方法を説明します。 規則では、*Virtual Network サービス エンドポイント*としてタグ付けされた特定のサブネットからの通信を許可するように、お使いの SQL Database に指示します。

#### <a name="powershell-alternative"></a>PowerShell による代替

PowerShell スクリプトでも、仮想ネットワーク規則を作成できます。 重要なコマンドレットは **New-AzureRmSqlServerVirtualNetworkRule** です。 ご興味がある方は、「[PowerShell to create a Virtual Network service endpoint and rule for Azure SQL Database (PowerShell で Azure SQL Database の Virtual Network サービス エンドポイントと規則を作成する)][sql-db-vnet-service-endpoint-rule-powershell-md-52d]」をご覧ください。

#### <a name="prerequisites"></a>前提条件

保持している 1 つのサブネットが、Azure SQL Database に関連する特定の Virtual Network エンドポイントの*種類名*で既にタグ付けされている必要があります。

- 関連するエンドポイントの種類名は **Microsoft.Sql** です。
- サブネットが種類名でタグ付けされていない場合は、「[Verify your subnet is an endpoint (サブネットが 1 つのエンドポイントであることを確認する)][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]」をご覧ください。

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Azure Portal の手順

1. [Azure Portal][http-azure-portal-link-ref-477t] にログインします。

2. ポータルで **[SQL サーバー]** &gt; **[ファイアウォール / 仮想ネットワーク]** の順に移動します。

3. **[Azure サービスへのアクセスを許可]** の制御を [オフ] に設定します。

    > [!IMPORTANT]
    > 制御を [オン] に設定したままの場合、お使いの Azure SQL Database サーバーでは任意のサブネットからの通信を許可するため、セキュリティの観点でアクセス過多になる恐れがあります。 Microsoft Azure Virtual Network サービス エンドポイント機能は、SQL Database の仮想ネットワーク規則機能と共に使用することで、セキュリティ脅威にさらされる領域を減少させることができます。

4. **[仮想ネットワーク]** セクションにある **[既存の追加]** コントロールをクリックします。

    ![[既存の追加] (SQL 規則としてのサブネット エンドポイント) をクリックします。][image-portal-firewall-vnet-add-existing-10-png]

5. 新しい **[作成/更新]** ペインで、お使いの Azure リソース名をコントロールに入力します。
 
    > [!TIP]
    > お使いのサブネットの正しい**アドレス プレフィックス**を含める必要があります。 ポータルで値を確認できます。 **[All resources]\(すべてのリソース\)** &gt; **[All types]\(すべての種類\)** &gt; **[仮想ネットワーク]** の順に移動します。 フィルターにお使いの仮想ネットワークが表示されます。 お使いの仮想ネットワークをクリックし、**[サブネット]** をクリックします。 **ADDRESS RANGE** 列に、必要なアドレス プレフィックスが含まれています。

    ![新しい規則のフィールドを入力します。][image-portal-firewall-create-update-vnet-rule-20-png]

6. ペイン下部付近にある **[OK]** ボタンをクリックします。

7.  ファイアウォール ペインで結果の仮想ネットワーク規則を確認します。

    ![ファイアウォール ペインで新しい規則を確認する][image-portal-firewall-vnet-result-rule-30-png]






<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>関連記事

- [PowerShell を使用して Virtual Network サービス エンドポイントと、Azure SQL Database 用の仮想ネットワークを順に作成する][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Azure 仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database サーバー レベルとデータベース レベルのファイアウォール規則][sql-db-firewall-rules-config-715d]

Microsoft Azure Virtual Network サービス エンドポイント機能、および Azure SQL Database の仮想ネットワーク規則機能は、両方とも 2017 年 9 月末に利用可能になります。





<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles

- PowerShell cmdlets

- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->


