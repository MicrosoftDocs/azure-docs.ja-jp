---
title: Linux ベースの Azure 仮想マシン上で実行される DH2i DxEnterprise を使用して Always On 可用性グループを設定する
description: Linux Azure 仮想マシン上でクラスター マネージャーに DH2i DxEnterprise を使用し、SQL Server の可用性グループによって高可用性を確保します
ms.date: 03/04/2021
ms.service: virtual-machines-linux
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 0500f4143ad7cbdaaa8406af2b242e0d40b1caa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219284"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>チュートリアル - Linux ベースの Azure 仮想マシン上で実行される DH2i DxEnterprise を使用して 3 ノードの Always On 可用性グループを設定する

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

このチュートリアルでは、Linux ベースの Azure 仮想マシン (VM) 上で実行される DH2i DxEnterprise を使用して、SQL Server Always On 可用性グループを構成する方法について説明します。 

DxEnterprise の詳細については、[DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/) に関するページを参照してください。

> [!NOTE]
> Microsoft では、データ移動、可用性グループ、および SQL Server コンポーネントをサポートしています。 クラスターとクォーラムの管理については、DH2i DxEnterprise クラスターのドキュメントに関するサポートについて、DH2i にお問い合わせください。
 

このチュートリアルは、以下の手順から構成されています。

> [!div class="checklist"]
> * 可用性グループに含めるすべての Azure 仮想マシン (VM) に SQL Server をインストールします。
> * すべての VM に DxEnterprise をインストールし、DxEnterprise クラスターを構成します。
> * フェールオーバーのサポートと高可用性を提供する仮想ホストを作成し、可用性グループを追加して、その可用性グループにデータベースを追加します。
> * 可用性グループ リスナーの内部 Azure ロード バランサーを作成します (省略可)。
> * 手動フェールオーバーまたは自動フェールオーバーを実行します。

このチュートリアルでは、[DxAdmin Client UI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/) を使用して DxEnterprise クラスターを設定します。 必要に応じて、[DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) コマンド ライン インターフェイスを使用してクラスターを設定することもできます。 この例では、4 つの VM を使用しています。 これらの VM のうち 3 つは Ubuntu 18.04 を実行し、3 ノード クラスターに参加します。 4 つ目の VM では、クラスターの管理と構成を行うための DxAdmin ツールがインストールされた Windows 10 を実行します。

## <a name="prerequisites"></a>前提条件

- Azure に 4 つの VM を作成します。 [Azure portal に Linux 仮想マシンを作成するクイックスタート](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)の記事に従って Linux ベースの仮想マシンを作成します。 同様に、Windows ベースの仮想マシンを作成する場合は、記事「[クイックスタート: Azure portal で Windows 仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)」を参照してください。
- クラスターに参加する Linux ベースの VM すべてに .NET 3.1 をインストールします。 選択した Linux オペレーティング システムに応じて、[こちら](https://docs.microsoft.com/dotnet/core/install/linux)にまとめられている手順に従ってください。
- 可用性グループ管理機能に対応した有効な DxEnterprise ライセンスが必要になります。 詳細については、[DxEnterprise 無料試用版](https://dh2i.com/trial/)に関するページで無料試用版の入手方法をご確認ください。

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>可用性グループに含めるすべての Azure VM に SQL Server をインストールする

このチュートリアルでは、可用性グループを実行する 3 ノードの Linux ベース クラスターを設定します。 選択した Linux プラットフォームに応じて、[Linux への SQL Server のインストール](https://docs.microsoft.com/sql/linux/sql-server-linux-overview#install)に関するドキュメントに従ってください。 このチュートリアルでは、[SQL Server ツール](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)もインストールするようお勧めします。
 
> [!NOTE]
> 選択した Linux OS が、[DH2i DxEnterprise (「最小システム要件」セクションを参照)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) と [Microsoft SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-release-notes-2019#supported-platforms) の両方でサポートされる一般的なディストリビューションであることを確認してください。
>
> この例では、DH2i DxEnterprise と Microsoft SQL Server の両方でサポートされている Ubuntu 18.04 を使用します。

このチュートリアルでは、Windows VM に SQL Server はインストールしません。このノードはクラスターには参加せず、DxAdmin を使用してクラスターを管理する目的でのみ使用されるためです。

この手順が完了した時点で、可用性グループに参加することになる Linux ベースの 3 つの VM すべてに SQL Server と [SQL Server ツール](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) (任意) がインストールされているはずです。
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>すべての VM に DxEnterprise をインストールしてクラスターを構成する

この手順では、DH2i DxEnterprise for Linux を 3 つの Linux VM にインストールします。 次の表では、各サーバーがクラスターで果たすロールについて説明します。

| [Number of VMs]\(VM の数\) | DH2i DxEnterprise ロール | Microsoft SQL Server 可用性グループのレプリカ ロール |
|--|--|--|
| 1 | クラスター ノード - Linux ベース | プライマリ |
| 1 | クラスター ノード - Linux ベース | セカンダリ - 同期コミット |
| 1 | クラスター ノード - Linux ベース | セカンダリ - 同期コミット |
| 1 | DxAdmin クライアント | NA |


Linux ベースの 3 つのノードに DxEnterprise をインストールする方法については、選択した Linux オペレーティング システムに応じて、DH2i DxEnterprise のドキュメントに従ってください。 次のいずれかの方法を使用して DxEnterprise をインストールします。

- **Ubuntu**
    - [リポジトリ インストールのクイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [拡張機能のクイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Marketplace イメージのクイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [リポジトリ インストールのクイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [拡張機能のクイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Marketplace イメージのクイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Windows VM に DxAdmin クライアント ツールだけをインストールする場合は、[DxAdmin Client UI クイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/)に従ってください。

この手順を終えた時点で、Linux VM 上には DxEnterprise クラスターが作成され、Windows クライアント マシンには DxAdmin クライアントがインストールされているはずです。 

> [!NOTE]
> いずれか 1 つのノードを "*構成のみモード*" ([こちら](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes)を参照) として追加して自動フェールオーバーを有効にした 3 ノード クラスターを作成することもできます。 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>フェールオーバーのサポートと高可用性を提供する仮想ホストを作成する

この手順では、仮想ホストと可用性グループを作成した後、データベースを追加します。いずれの作業も DxAdmin UI を使用して行います。   

> [!NOTE]
> この手順の途中で、Always On を有効にするために SQL Server インスタンスが再起動されます。 

DxAdmin を実行する Windows クライアント マシンに接続し、前の手順で作成したクラスターに接続します。 「[DxAdmin での MSSQL 可用性グループ](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/)」に記載の手順に従って Always On を有効にし、仮想ホストと可用性グループを作成します。 

> [!TIP]
> データベースを追加する前に、SQL Server のプライマリ インスタンスにデータベースが作成されてバックアップされていることを確認してください。  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>リスナー用の内部 Azure ロード バランサーを作成する (省略可)

この手順 (省略可) では、可用性グループ リスナーの IP アドレスを保持する Azure ロード バランサーを作成して構成できます。 Azure Load Balancer の詳細については、[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) に関するページを参照してください。 DxAdmin を使用して Azure ロード バランサーと可用性グループ リスナーを構成する場合は、DxEnterprise の [Azure Load Balancer クイック スタート ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)に従ってください。

この手順を終えた時点で、可用性グループ リスナーが作成されて、内部 Azure ロード バランサーにマップされているはずです。

## <a name="test-manual-or-automatic-failover"></a>手動フェールオーバーまたは自動フェールオーバーをテストする

自動フェールオーバーのテストを行うために、プライマリ レプリカを停止してみましょう (Azure portal から仮想マシンをオフにします)。 これにより、プライマリ ノードが突然利用できなくなった状態を再現されます。 予期される動作は次のとおりです。
- クラスター マネージャーにより、可用性グループ内のいずれかのセカンダリ レプリカがプライマリに昇格します。
- 失敗したプライマリ レプリカは、バックアップされた後に自動的にクラスターに参加します。 これは、クラスター マネージャーによってセカンダリ レプリカに昇格します。

 
以下に記載の手順に従って手動フェールオーバーを実行することもできます。

1. DxAdmin からクラスターに接続します   
1. 可用性グループの仮想ホストを展開します
1. ターゲット ノードまたはセカンダリ レプリカを右クリックし、 **[Start Hosting on Member]\(メンバーでのホスティングを開始する\)** を選択してフェールオーバーを開始します 

DxEnterprise 内での操作について詳しくは、[DxEnterprise 管理者ガイド](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf)および [DxEnterprise DxCLI ガイド](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)を参照してください。

## <a name="next-steps"></a>次の手順

- [Linux 上の可用性グループ](https://docs.microsoft.com/sql/linux/sql-server-linux-availability-group-overview)の詳細
- [クイックスタート: Azure portal で Linux 仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)
- [クイック スタート:Azure Portal で Windows 仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)
- [SQL Server 2019 on Linux でサポートされるプラットフォーム](https://docs.microsoft.com/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)