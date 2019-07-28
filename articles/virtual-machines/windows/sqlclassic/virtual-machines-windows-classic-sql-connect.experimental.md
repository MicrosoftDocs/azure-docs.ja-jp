---
title: Azure で SQL Server 仮想マシンに接続する (クラシック) | Microsoft Docs
description: Azure の仮想マシンで実行されている SQL Server に接続する方法について説明します。 このトピックでは、クラシック デプロイ モデルを使用します。 シナリオは、ネットワーク構成とクライアントの場所によって異なります。
services: virtual-machines-windows
documentationcenter: na
author: mashamsft
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 51694ca085e131150217ffb3fbac9830980108cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108443"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Azure での SQL Server 仮想マシンへの接続 (クラシック デプロイ)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [クラシック](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>概要
このトピックでは、Azure 仮想マシンで実行されている SQL Server インスタンスへ接続する方法について説明します。 ここでは、[一般的な接続のシナリオ](#connection-scenarios)をいくつか紹介してから、[Azure VM での SQL Server への接続を構成する手順の詳細](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)について説明します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用している場合は、[Resource Manager を使用した Azure での SQL Server 仮想マシンへの接続](../sql/virtual-machines-windows-sql-connect.md)に関する記事をご覧ください。

## <a name="connection-scenarios"></a>接続のシナリオ
クライアントから仮想マシンで実行されている SQL Server に接続する方法は、クライアントの場所と、マシンやネットワークの構成によって異なります。 これらのシナリオは、次のとおりです。

* [同一クラウド サービス内の SQL Server に接続する方法](#connect-to-sql-server-in-the-same-cloud-service)
* [インターネット経由で SQL Server に接続する方法](#connect-to-sql-server-over-the-internet)
* [同一仮想ネットワーク内で SQL Server に接続する方法](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> これらの方法のいずれかで接続する前に、 [この記事の接続の構成手順](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)を完了する必要があります。
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>同一クラウド サービス内の SQL Server に接続する方法
同じクラウド サービス内で複数の仮想マシンを作成できます。 このような仮想マシンのシナリオについては、「 [仮想マシンを仮想ネットワークまたはクラウド サービスと接続する方法](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service)」を参照してください。 このシナリオでは、ある仮想マシン上のクライアントから、同じクラウド サービス内の別の仮想マシンで実行されている SQL Server に接続しようとする場合を取り上げます。

このシナリオでは、VM の**名前** (ポータル内では **[コンピューター名]** または **[ホスト名]** とも表示されます) を使用して接続できます。 この名前は、作成時に VM に指定した名前です。 たとえば、SQL VM に **mysqlvm**という名前を付けた場合、同じクラウド サービス内のクライアント VM では次のような文字列を使用して接続できます。

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>インターネット経由で SQL Server に接続する
インターネットから、SQL Server データベース エンジンに接続する場合は、着信 TCP 通信用の仮想マシンのエンドポイントを作成する必要があります。 この Azure 構成手順により、仮想マシンからアクセスできる TCP ポートに、着信する TCP ポート トラフィックが送信されます。

インターネット経由で接続するには、VM の DNS 名と VM エンドポイント ポート番号 (この記事の後半で構成します) を使用する必要があります。 DNS 名を確認するには、Azure Portal に移動し、 **[仮想マシン (クラシック)]** を選択します。 その後、仮想マシンを選択します。 **DNS 名**は、 **[概要]** セクションに表示されます。

たとえば、DNS 名が **mysqlvm7777.cloudapp.net** であり、VM エンドポイントが **57500** である **mysqlvm** という名前のクラシック仮想マシンがあるとします。 適切に構成された接続がある場合は、次の接続文字列を使用して、インターネット上の任意の場所から仮想マシンにアクセスできます。

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

この接続文字列でクライアントからインターネット経由での接続は有効になりますが、すべてのユーザーが SQL Server に接続できるわけではありません。 外部のクライアントは、ユーザー名とパスワードを修正する必要があります。 セキュリティを強化するために、既知のポート 1433 を仮想マシンのパブリック エンドポイントに使用しないでください。 また可能であれば、エンドポイントに ACL を追加して、許可されたクライアントのみにトラフィックを制限することを検討してください。 エンドポイントで ACL を使用する手順については、「 [エンドポイントの ACL の管理](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)」を参照してください。

> [!NOTE]
> この手法を使用して SQL Server と通信すると、Azure データセンターから送信されるすべてのデータには、通常の [送信データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/)が適用されます。
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>同一仮想ネットワーク内で SQL Server に接続する
[Virtual Network](../../../virtual-network/virtual-networks-overview.md) では、さらにシナリオが追加されます。 同じ仮想ネットワーク内の VM が異なるクラウド サービスに存在する場合でも、それらの VM に接続できます。 また [サイト間 VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)を使うと、VM をオンプレミスのネットワークおよびマシンと接続するハイブリッド アーキテクチャを作成できます。

仮想ネットワークを使うと、Azure VM をドメインに参加させることもできます。 ドメインに参加することが、SQL Server で Windows 認証を使用する唯一の方法です。 その他の接続シナリオでは、ユーザー名とパスワードによる SQL 認証が必要です。

ドメイン環境と Windows 認証を構成する場合は、パブリック エンドポイントまたは SQL 認証とログインを構成する必要はありません。 このシナリオでは、接続文字列で SQL Server VM 名を指定することによって、SQL Server インスタンスに接続できます。 次の例は、Windows 認証が構成されていることと、ユーザーが SQL Server インスタンスへのアクセスを許可されていることを前提としています。

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Azure VM で SQL Server への接続を構成する手順
次の手順で、SQL Server Management Studio (SSMS) を使用してインターネット経由で SQL Server インスタンスに接続する方法を説明します。 ただし、同じ手順が、アプリケーションのアクセスが可能な SQL Server 仮想マシンを作成し、オンプレミスと Azure の両方で実行するときに適用されます。

別の VM またはインターネットから SQL Server のインスタンスに接続するには、次のタスクを完了している必要があります。

* [仮想マシン用の TCP エンドポイントを作成する](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Windows ファイアウォールで TCP ポートを開く](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [TCP プロトコルでリッスンするように SQL Server を構成する](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [混合モード認証用に SQL Server を構成する](#configure-sql-server-for-mixed-mode-authentication)
* [SQL Server 認証ログインを作成する](#create-sql-server-authentication-logins)
* [仮想マシンの DNS 名を特定する](#determine-the-dns-name-of-the-virtual-machine)
* [別のコンピューターからデータベース エンジンに接続する](#connect-to-the-database-engine-from-another-computer)

次の図は、接続パスの概要を示したものです。

![SQL Server 仮想マシンに接続する](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>次の手順
高可用性と障害復旧のために AlwaysOn 可用性グループを使用する予定もある場合、リスナーの実装を検討してください。 データベース クライアントは、SQL Server インスタンスの 1 つに直接接続せずに、リスナーに接続します。 その上でリスナーがクライアントを可用性グループのプライマリ レプリカにルーティングします。 詳細については、「 [Azure での AlwaysOn 可用性グループの ILB リスナーの構成](../classic/ps-sql-int-listener.md)」を参照してください。

Azure の仮想マシンで実行されている SQL Server のセキュリティに関するベスト プラクティスをすべて確認することが重要です。 詳細については、「 [Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項](../sql/virtual-machines-windows-sql-security.md)」をご覧ください。

[ラーニング パスをご覧ください](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) 。 

Azure VM での SQL Server の実行に関するその他のトピックについては、「 [Azure Virtual Machines における SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

