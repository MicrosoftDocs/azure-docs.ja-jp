---
title: SQL Server 仮想マシンへの接続 (リソース マネージャー) | Microsoft Docs
description: Azure 上にあるご自分の SQL Server 仮想マシンに接続する方法を説明します。 このトピックでは、クラシック デプロイ モデルを使用します。 シナリオは、ネットワーク構成とクライアントの場所によって異なります。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37cb8f5a2ff0916f53ae50f5750664204ab1ba75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737491"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Azure 上にある SQL Server 仮想マシンへの接続
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>概要

このトピックでは、Azure 仮想マシン(VM) 上で実行されているご使用の SQL に接続する方法について説明します。 [一般的な接続シナリオ](#connection-scenarios)をいくつか紹介し、[ポータルで接続設定を変更する手順](#change)を説明します。 ポータルの外部で接続のトラブルシューティングや構成を行う必要がある場合は、このトピックの最後の[手動構成](#manual)に関するセクションをご覧ください。 

プロビジョニングと接続の両方に関する完全なチュートリアルについては、[Azure 上での SQL Server 仮想マシンのプロビジョニング](create-sql-vm-portal.md)に関するページをご覧ください。

## <a name="connection-scenarios"></a>接続のシナリオ

クライアントから仮想マシン上で実行されている SQL Server VM に接続する方法は、クライアントの場所とネットワークの構成によって異なります。

Azure Portal で SQL Server VM をプロビジョニングする場合、**SQL 接続** の種類を指定するオプションが用意されています。

![プロビジョニング中のパブリック SQL 接続オプション](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

接続のオプションは次のとおりです。

| オプション | 説明 |
|---|---|
| **パブリック** | インターネット経由で SQL Server に接続します。 |
| **プライベート** | 同一仮想ネットワーク内の SQL Server に接続します。 |
| **ローカル** | 同一仮想マシン内の SQL Server にローカルに接続します。 | 

次のセクションでは、**パブリック** および **プライベート** オプションの詳細について説明します。

## <a name="connect-to-sql-server-over-the-internet"></a>インターネット経由で SQL Server に接続する

インターネットからご使用の SQL Server データベース エンジンに接続する場合は、プロビジョニング時にポータル内で **[SQL 接続]** の種類に **[パブリック]** を選択します。 ポータルは自動的に次の手順を実行します。

* SQL Server の TCP/IP プロトコルを確立します。
* SQL Server の TCP ポート (既定は 1433) を開くファイアウォール規則を構成します。
* パブリック アクセスに必要な SQL Server 認証を有効にします。
* SQL Server ポート上のすべての TCP トラフィックに VM のネットワーク セキュリティ グループを構成します。

> [!IMPORTANT]
> SQL Server Developer および Express エディション用の仮想マシン イメージでは、TCP/IP プロトコルは自動では有効になりません。 Developer または Express エディションでは、VM の作成後に、SQL Server 構成マネージャーを使用して [TCP/IP プロトコルを手動で有効にする](#manualtcp) 必要があります。

インターネットにアクセスできるクライアントは、仮想マシンのパブリック IP アドレスまたはその IP アドレスに割り当てられている任意の DNS ラベルを指定することによって、SQL Server インスタンスに接続できます。 SQL Server のポートが 1433 である場合は、接続文字列でそれを指定する必要はありません。 次の接続文字列は、SQL 認証を使用して (パブリック IP アドレスも使用できます)、`sqlvmlabel.eastus.cloudapp.azure.com` の DNS ラベルで SQL VM に接続します。

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

この接続文字列でクライアントからインターネット経由での接続は有効になりますが、すべてのユーザーがご使用の SQL Server インスタンスに接続できるわけではありません。 外部のクライアントは、正しいユーザー名とパスワードを使用する必要があります。 ただし、セキュリティを強化するために、既知のポート 1433 を使用しないこともできます。 たとえば、ポート 1500 でリッスンするように SQL Server を構成し、適切なファイアウォール規則とネットワーク セキュリティ グループ規則を設定すると、サーバー名にポート番号を追加して接続できます。 次の例では、カスタム ポート番号 **1500** をサーバー名に追加することで、以前のポート番号を変更しています。

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> インターネット経由で VM 上の SQL Server に対してクエリを実行した場合、Azure データセンターから送信されたすべてのデータには、通常の[送信データ転送の価格](https://azure.microsoft.com/pricing/details/data-transfers/)が適用されます。

## <a name="connect-to-sql-server-within-a-virtual-network"></a>仮想ネットワーク内で SQL Server に接続する方法

ポータルで **[SQL 接続]** の種類に **[プライベート]** を選択した場合、Azure では **[パブリック]** とほぼ同一の設定を構成します。 1 つの相違点は、SQL Server ポート (既定は 1433) に外部トラフィックを許可するネットワーク セキュリティ グループの規則がないことです。

> [!IMPORTANT]
> SQL Server Developer および Express エディション用の仮想マシン イメージでは、TCP/IP プロトコルは自動では有効になりません。 Developer または Express エディションでは、VM の作成後に、SQL Server 構成マネージャーを使用して [TCP/IP プロトコルを手動で有効にする](#manualtcp) 必要があります。

プライベート接続は、多くの場合、いくつかのシナリオを可能にする[仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)と共に使用されます。 同じ仮想ネットワーク内の VM が異なるリソース グループに存在する場合でも、それらの VM に接続できます。 また [サイト間 VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md)を使うと、VM をオンプレミスのネットワークおよびマシンと接続するハイブリッド アーキテクチャを作成できます。

仮想ネットワークを使うと、Azure VM をドメインに参加させることもできます。 これは、SQL Server に Windows 認証を使用する唯一の方法です。 その他の接続シナリオでは、ユーザー名とパスワードによる SQL 認証が必要です。

仮想ネットワークで DNS を構成してあると、接続文字列で SQL Server VM コンピューターの名前を指定することによって、SQL Server インスタンスに接続できます。 次の例でも、Windows 認証も構成されていることと、ユーザーが SQL Server インスタンスへのアクセスを許可されていることを前提としています。

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a>SQL 接続の設定を変更する

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Azure Portal で、SQL Server 仮想マシンの接続設定を変更できます。

1. Azure portal で、 **[SQL 仮想マシン]** を選択します。

2. SQL Server VM を選択します。

3. **[設定]** で **[セキュリティ]** を選択します。

4. **SQL 接続レベル** を必要な設定に変更します。 必要に応じて、この領域を使用して、SQL Server ポートまたは SQL 認証の設定を変更できます。

   ![SQL 接続を変更する](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. 更新が完了するまで数分間、待機します。

   ![SQL VM 更新通知](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a>Developer および Express エディションの TCP/IP を有効にする

SQL Server 接続の設定を変更するとき、Azure は SQL Server Developer および Express エディション の TCP/IP プロトコルを自動的に有効にしません。 TCP/IP を手動で有効にして IP アドレスでリモート接続できるようにするための手順を次に示します。

最初に、リモート デスクトップを使用して、SQL Server 仮想マシンに接続します。

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

次に、**SQL Server 構成マネージャー** を使用して、TCP/IP プロトコルを有効にします。

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>SSMS との接続

次の手順では、ご使用の Azure VM の省略可能な DNS ラベルを作成し、SQL Server Management Studio (SSMS) と接続する方法を示します。

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a>手動構成とトラブルシューティング

ポータルには接続を自動的に構成するオプションが用意されていますが、接続を手動で構成する方法も知っておくと便利です。 要件を把握しておくと、トラブルシューティングにも役立ちます。

Azure VM 上の SQL Server に接続するための要件を次の表に示します。

| 要件 | 説明 |
|---|---|
| [SQL Server 認証モードを有効にする](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | 仮想ネットワーク上で Active Directory を構成済みである場合を除き、VM にリモート接続するには SQL Server 認証が必要になります。 |
| [SQL ログインを作成する](/sql/relational-databases/security/authentication-access/create-a-login) | SQL 認証を使用する場合、実際のターゲット データベースへのアクセス許可も持つ、ユーザー名とパスワードによる SQL ログインが必要です。 |
| [TCP/IP プロトコルを有効にする](#manualtcp) | SQL Server では、TCP 経由の接続を許可する必要があります。 |
| [SQL Server ポートのファイアウォール規則を有効にする](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | VM 上のファイアウォールでは、SQL Server ポート (既定では 1433) の受信トラフィックを許可する必要があります。 |
| [TCP 1433 のネットワーク セキュリティ グループの規則を作成する](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | インターネット経由で接続する場合は、VM が SQL Server ポート (既定では 1433) でトラフィックを受信できるようにする必要があります。 ローカル接続と仮想ネットワーク専用の接続では不要です。 これは、Azure Portal で必要な唯一の手順です。 |

> [!TIP]
> ポータル内で接続を構成すると、上記の表の手順が自動的に実行されます。 これらの手順を使用するのは、ご自分の構成を確認する場合または SQL Server の接続を手動で設定する場合だけです。

## <a name="next-steps"></a>次のステップ

これらの接続の手順とプロビジョニングの手順を確認するには、[Azure 上での SQL Server 仮想マシンのプロビジョニング](create-sql-vm-portal.md)に関するページを参照してください。

Azure VM 上での SQL Server の実行に関するその他のトピックについては、[Azure 仮想マシン上の SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) に関するページを参照してください。