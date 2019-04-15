---
title: 異なる Azure Stack Development Kit 環境にある 2 つの仮想ネットワークの間にサイト間 VPN 接続を作成する | Microsoft Docs
description: 2 つのシングル ノード Azure Stack Development Kit 環境の間にクラウド管理者がサイト間 VPN 接続を作成するための詳細な手順。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 432231d7b571cfda9ec4d71d24aa8bd05346c6b2
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803750"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>異なる Azure Stack Development Kit 環境にある 2 つの仮想ネットワークの間にサイト間 VPN 接続を作成する

## <a name="overview"></a>概要

この記事では、2 つの独立した Azure Stack Development Kit (ASDK) 環境にある 2 つの仮想ネットワークの間にサイト間 VPN 接続を作成する方法を説明します。 接続を構成しながら Azure Stack での VPN ゲートウェイのしくみを学習します。

### <a name="connection"></a>接続

次の図は、作業完了後の接続構成を表しています。

![サイト間 VPN 接続構成](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>開始する前に

接続構成を行うには、作業開始前に次のものを用意する必要があります。

* ASDK のハードウェア要件を満たす 2 つのサーバーおよびその他の前提条件 ([Azure Stack Development Kit の評価に関するクイック スタート](azure-stack-deploy-overview.md)を参照)。
* [Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) デプロイ パッケージ。

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Azure Stack Development Kit 環境のデプロイ

接続構成を行うには、2 つの ASDK 環境をデプロイする必要があります。

> [!NOTE]
> デプロイする ASDK ごとに、[デプロイ指示](azure-stack-run-powershell-script.md)に従ってください。 この記事では、ASDK 環境の名称が **POC1** と **POC2** になっています。

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>POC1 と POC2 でプランを用意する

POC1 と POC2 の両方で、ユーザーがプランに加入し、仮想マシンをデプロイできるようにプランを用意します。 オファーの作成方法については、「[Make virtual machines available to your Azure Stack users](azure-stack-tutorial-tenant-vm.md)」 (Azure Stack ユーザーが仮想マシンを使えるようにする) を参照してください。

## <a name="review-and-complete-the-network-configuration-table"></a>ネットワーク構成表を確認し、完成する

次の表は、両方の ASDK 環境のネットワーク構成についてまとめたものです。 表の後に出てくる手順で、自分のネットワークに固有の外部 BGPNAT アドレスを追加します。

### <a name="network-configuration-table"></a>ネットワーク構成表

|   |POC1|POC2|
|---------|---------|---------|
|仮想ネットワーク名     |VNET-01|VNET-02 |
|仮想ネットワークのアドレス空間 |10.0.10.0/23|10.0.20.0/23|
|サブネット名     |Subnet-01|Subnet-02|
|サブネットのアドレス範囲|10.0.10.0/24 |10.0.20.0/24 |
|ゲートウェイ サブネット     |10.0.11.0/24|10.0.21.0/24|
|外部 BGPNAT アドレス     |         |         |

> [!NOTE]
> 環境例の外部 BGPNAT IP アドレスは POC1 が 10.16.167.195 で、POC2 が 10.16.169.131 です。 次の手順で ASDK ホストの外部 BGPNAT IP アドレスを決定し、それを先のネットワーク構成表に追加します。

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>NAT VM の外部アダプターの IP アドレスを取得する

1. POC1 の Azure Stack 物理マシンにサインインします。
2. 次の PowerShell コードを編集して管理者パスワードを変更し、POC ホストでコードを実行します。

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```

3. 先のセクションのネットワーク構成表に IP アドレスを追加します。

4. POC2 でこの手順を繰り返します。

## <a name="create-the-network-resources-in-poc1"></a>POC 1 でネットワーク リソースを作成する

これで、ゲートウェイを設定する POC1 ネットワーク リソースを作成することができます。 次の手順で、Azure Stack ユーザー ポータルを利用してリソースを作成する方法を説明します。 PowerShell コードでリソースを作成することもできます。

![リソース作成に使用されるワークフロー](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>テナントとしてサインインする

サービス管理者は、テナントが使用する可能性があるプラン、オファー、サブスクリプションをテストするためにテナントとしてサインインできます。 まだ持っていない場合は、サインインする前に[テナント アカウントを作成](azure-stack-add-new-user-aad.md)します。

### <a name="create-the-virtual-network-and-vm-subnet"></a>仮想ネットワークと VM サブネットを作成する

1. テナント アカウントを使用してユーザー ポータルにサインインします。
2. ユーザー ポータルで **[+ リソースの作成]** を選択します。
3. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
4. **[仮想ネットワーク]** を選択します。
5. **[名前]**、**[アドレス空間]**、**[サブネット名]**、**[サブネット アドレス範囲]** には、先のネットワーク構成表にあった値を使用します。
6. **[サブスクリプション]** には、先に作成したサブスクリプションが表示されます。
7. **[リソース グループ]** については、リソース グループを作成することも、既存のリソース グループがある場合に **[既存のものを使用]** を選択することもできます。
8. 既定の場所を確認します。
9. **[ダッシュボードにピン留めする]** をオンにします。
10. **作成**を選択します。

### <a name="create-the-gateway-subnet"></a>ゲートウェイ サブネットを作成する

1. ダッシュボードで、先ほど作成した VNET-01 仮想ネットワーク リソースを開きます。
2. **[設定]** ブレードで **[サブネット]** を選択します。
3. ゲートウェイ サブネットを仮想ネットワークに追加するには、**[ゲートウェイ サブネット]** を選択します。

    ![ゲートウェイ サブネットを追加する](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. 既定では、サブネット名は **GatewaySubnet** に設定されます。 ゲートウェイ サブネットは特別です。 正常に機能するために、**GatewaySubnet** という名前を使用する必要があります。
5. **[アドレス範囲]** でアドレスが **10.0.11.0/24** であることを確認します。
6. **[OK]** を選択し、ゲートウェイ サブネットを作成します。

### <a name="create-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

1. Azure portal で **[+ リソースの作成]** を選択します。
2. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
3. ネットワーク リソースの一覧から **[仮想ネットワーク ゲートウェイ]** を選択します。
4. **[名前]** には、「**GW1**」と入力します。
5. **[仮想ネットワーク]** 項目を選択し、仮想ネットワークを選択します。 一覧から **[VNET-01]** を選択します。
6. **[パブリック IP アドレス]** メニュー項目を選択します。 **[パブリック IP アドレスの選択]** ウィンドウが開いたら、**[新規作成]** を選択します。
7. **[名前]** に「**GW1-PiP**」と入力し、**[OK]** を選択します。
8. 既定では、**[VPN の種類]** に **[ルート ベース]** が選択されています。 VPN の種類は **[ルート ベース]** のままにします。
9. **[サブスクリプション]** と **[場所]** が正しいことを確認します。 リソースをダッシュボードにピン留めできます。 **作成**を選択します。

### <a name="create-the-local-network-gateway"></a>ローカル ネットワーク ゲートウェイを作成する

この Azure Stack 評価デプロイでの "*ローカル ネットワーク ゲートウェイ*" の実装は、実際の Azure デプロイとは若干異なります。

Azure デプロイでは、ローカル ネットワーク ゲートウェイは、Azure の仮想ネットワーク ゲートウェイへの接続に使用するオンプレミスの (テナントの) 物理デバイスを表します。 この Azure Stack 評価デプロイでは、接続の両端が仮想ネットワーク ゲートウェイになります。

これをより一般化すると、ローカル ネットワーク ゲートウェイ リソースは、常に接続の反対側の端にあるリモート ゲートウェイを示します。 Azure Stack Development Kit の設計上、ローカル ネットワーク ゲートウェイのパブリック IP アドレスとして、他の ASDK のネットワーク アドレス変換 (NAT) VM で外部ネットワーク アダプターの IP アドレスを指定する必要があります。 その後、NAT VM で NAT マッピングを作成し、両端が正しく接続されるようにします。

### <a name="create-the-local-network-gateway-resource"></a>ローカル ネットワーク ゲートウェイ リソースを作成する

1. POC1 の Azure Stack 物理マシンにサインインします。
2. ユーザー ポータルで **[+ リソースの作成]** を選択します。
3. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
4. リソースの一覧から **[ローカル ネットワーク ゲートウェイ]** を選択します。
5. **[名前]** には、「**POC2-GW**」と入力します。
6. **[IP アドレス]** に、POC2 の外部 BGPNAT アドレスを入力します。 このアドレスは先のネットワーク構成表にありました。
7. **[アドレス空間]** で、後で使用する POC2 VNET のアドレス空間に「**10.0.20.0/23**」と入力します。
8. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** の値がすべて正しいことを確認し、**[作成]** を選択します。

### <a name="create-the-connection"></a>接続の作成

1. ユーザー ポータルで **[+ リソースの作成]** を選択します。
2. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
3. リソースの一覧から **[接続]** を選択します。
4. **[基本]** 設定ブレードで、**[接続の種類]** として **[サイト対サイト (IPsec)]** を選択します。
5. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を選択し、**[OK]** を選択します。
6. **[設定]** ブレードで **[仮想ネットワーク ゲートウェイ]** を選択し、**[GW1]** を選択します。
7. **[ローカル ネットワーク ゲートウェイ]** を選択し、**[POC2-GW]** を選択します。
8. **[接続名]** に **[POC1-POC2]** と入力します。
9. **[共有キー (PSK)]** に「**12345**」と入力し、**[OK]** を選択します。
10. **[概要]** ブレードで、**[OK]** を選択します。

### <a name="create-a-vm"></a>VM の作成

VPN 接続で送信されるデータを検証するには、各 Azure Stack Development Kit でデータを送受信する仮想マシンが必要です。 今から POC1 に仮想マシンを作成し、次に仮想ネットワークで作成し、VM サブネットにそれを配置します。

1. Azure portal で **[+ リソースの作成]** を選択します。
2. **[Marketplace]** に移動し、**[計算]** を選択します。
3. 仮想マシンのイメージの一覧で、**Windows Server 2016 Datacenter Eval** イメージを選択します。
4. **[基本]** ブレードの **[名前]** に「**VM01**」と入力します。
5. 有効なユーザー名とパスワードを入力します。 このアカウントを利用し、作成後の VM にサインインします。
6. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定し、**[OK]** を選択します。
7. **[サイズ]** ブレードで、この場合、仮想マシンのサイズを選択し、**[選択]** を選択します。
8. **[設定]** ブレードで、既定値を選択します。 **VNET-01** 仮想ネットワークが選択されていることを確認します。 サブネットが **10.0.10.0/24** に設定されていることを確認します。 **[OK]** をクリックします。
9. **[サマリー]** ブレードで、設定を確認し、**[OK]** を選択します。

## <a name="create-the-network-resources-in-poc2"></a>POC2 でネットワーク リソースを作成する

次の手順は、POC2 のネットワーク リソースを作成することです。 次の手順は、ユーザー ポータルを利用してリソースを作成する方法です。

### <a name="sign-in-as-a-tenant-again"></a>テナントとして再度サインインする

サービス管理者は、テナントが使用する可能性があるプラン、オファー、サブスクリプションをテストするためにテナントとしてサインインできます。 まだ持っていない場合は、サインインする前に[テナント アカウントを作成](azure-stack-add-new-user-aad.md)します。

### <a name="create-virtual-network-and-vm-subnet"></a>仮想ネットワークと VM サブネットを作成する

1. テナント アカウントでサインインします。
2. ユーザー ポータルで **[+ リソースの作成]** を選択します。
3. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
4. **[仮想ネットワーク]** を選択します。
5. 先のネットワーク構成表にあった情報を利用し、POC2 の **[名前]**、**[アドレス空間]**、**[サブネット名]**、**[サブネット アドレス範囲]** の値を特定します。
6. **[サブスクリプション]** には、先に作成したサブスクリプションが表示されます。
7. **[リソース グループ]** については、新しいものを作成するか、既存のリソース グループがある場合は **[既存のものを使用]** を選択します。
8. 既定の**場所**を確認します。
9. **[ダッシュボードにピン留めする]** をオンにします。
10. **作成**を選択します。

### <a name="create-gateway-subnet"></a>ゲートウェイ サブネットを作成する

1. ダッシュボードから、作成した仮想ネットワーク リソース (**VNET-02**) を開きます。
2. **[設定]** ブレードで **[サブネット]** を選択します。
3. **[ゲートウェイ サブネット]** を選択し、ゲートウェイ サブネットを仮想ネットワークに追加します。
4. サブネットの名前は、既定で **GatewaySubnet** に設定されます。 ゲートウェイ サブネットは特別であり、正しく動作するにはこの特定の名前である必要があります。
5. **[アドレス範囲]** フィールドでアドレスが **10.0.21.0/24** であることを確認します。
6. **[OK]** を選択し、ゲートウェイ サブネットを作成します。

### <a name="create-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

1. Azure portal で **[+ リソースの作成]** を選択します。  
2. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
3. ネットワーク リソースの一覧から **[仮想ネットワーク ゲートウェイ]** を選択します。
4. **[名前]** には、「**GW2**」と入力します。
5. 仮想ネットワークを選択するには、**[仮想ネットワーク]** を選択します。 一覧から **[VNET-02]** を選択します。
6. **[パブリック IP アドレス]** を選択します。 **[パブリック IP アドレスの選択]** ブレードが開いたら、**[新規作成]** を選択します。
7. **[名前]** に「**GW2-PiP**」と入力し、**[OK]** を選択します。
8. 既定では、**[VPN の種類]** に **[ルート ベース]** が選択されています。 VPN の種類は **[ルート ベース]** のままにします。
9. **[サブスクリプション]** と **[場所]** が正しいことを確認します。 リソースをダッシュボードにピン留めできます。 **作成**を選択します。

### <a name="create-local-network-gateway-resource"></a>ローカル ネットワーク ゲートウェイ リソースを作成する

1. POC2 ポータルで **[+ リソースの作成]** を選択します。
2. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
3. リソースの一覧から **[ローカル ネットワーク ゲートウェイ]** を選択します。
4. **[名前]** には、「**POC1-GW**」と入力します。
5. **[IP アドレス]** には、前述のネットワーク構成表にあった POC1 の外部 BGPNAT アドレスを入力します。
6. **[アドレス空間]** には、POC1 から、**VNET-01** のアドレス空間 **10.0.10.0/23** を入力します。
7. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** がすべて正しいことを確認し、**[作成]** を選択します。

## <a name="create-connection"></a>接続を作成する

1. ユーザー ポータルで **[+ リソースの作成]** を選択します。
2. **[Marketplace]** に移動し、**[ネットワーク]** を選択します。
3. リソースの一覧から **[接続]** を選択します。
4. **[基本]** 設定ブレードで、**[接続の種類]** として **[サイト対サイト (IPSec)]** を選択します。
5. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を選択し、**[OK]** を選択します。
6. **[設定]** ブレードで **[仮想ネットワーク ゲートウェイ]** を選択し、**[GW2]** を選択します。
7. **[ローカル ネットワーク ゲートウェイ]** を選択し、**[POC1-GW]** を選択します。
8. **[接続名]** に **[POC2-POC1]** と入力します。
9. **[共有キー (PSK)]** に「**12345**」と入力します。 別の値を選択する場合は、POC1 で作成した共有キーの値と一致する必要があることに注意してください。 **[OK]** を選択します。
10. **[概要]** ブレードを確認し、**[OK]** を選択します。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

ここでは POC2 に仮想マシンを作成し、仮想ネットワーク内の VM サブネットにそれを配置します。

1. Azure portal で **[+ リソースの作成]** を選択します。
2. **[Marketplace]** に移動し、**[計算]** を選択します。
3. 仮想マシンのイメージの一覧で、**Windows Server 2016 Datacenter Eval** イメージを選択します。
4. **[基本]** ブレードの **[名前]** に「**VM02**」と入力します。
5. 有効なユーザー名とパスワードを入力します。 このアカウントを利用し、作成後の仮想マシンにサインインします。
6. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定し、**[OK]** を選択します。
7. **[サイズ]** ブレードで、この場合は仮想マシンのサイズを選択し、**[選択]** を選択します。
8. **[設定]** ブレードは既定値のままでかまいません。 **VNET-02** 仮想ネットワークが選択されていること、サブネットが **10.0.20.0/24** に設定されていることを確認します。 **[OK]** を選択します。
9. **[サマリー]** ブレードで設定を確認し、**[OK]** を選択します。

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>ゲートウェイ通過のために各 Azure Stack Development Kit で NAT 仮想マシンを構成する

ASDK は自己完結型であり、物理ホストがデプロイされるネットワークから分離されているため、ゲートウェイの接続先の*外部* VIP ネットワークは実際は外部にはありません。 VIP ネットワークは、ネットワーク アドレス変換を実行しているルーターの背後に隠れています。

このルーターは **AzS-bgpnat01** という名前の Windows Server 仮想マシンです。この仮想マシンは、ASDK インフラストラクチャでルーティングとリモート アクセス サービス (RRAS) ロールを実行します。 AzS-bgpnat01 仮想マシンでは、両端を結ぶサイト間 VPN 接続を許可するよう NAT を構成する必要があります。

VPN 接続を構成するには、静的 NAT マップ ルートを作成する必要があります。このルートにより、BGPNAT VM の外部インターフェイスがエッジ ゲートウェイ プールの VIP にマップされます。 静的 NAT マップ ルートは、VPN 接続の各ポートに必須です。

> [!NOTE]
> この構成は、Azure Stack Development Kit 環境のみで必要です。
>
>

### <a name="configure-the-nat"></a>NAT を構成する

> [!IMPORTANT]
> この手順は、両方の ASDK 環境で実行する必要があります。

1. 次の PowerShell スクリプトで使用する**内部 IP アドレス**を決定します。 仮想ネットワーク ゲートウェイ (GW1 と GW2) を開き、**[概要]** ブレードで後で使用するために **[パブリック IP アドレス]** の値を保存します。

   ![内部 IP アドレス](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. POC1 の Azure Stack 物理マシンにサインインします。
3. 次の PowerShell スクリプトをコピーし、編集します。 各 Azure Stack Development Kit で NAT を構成するには、管理者特権の Windows PowerShell ISE でこのスクリプトを実行します。 このスクリプトでは、値が `External BGPNAT address` と `Internal IP address` プレースホルダーに追加されます。

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. POC2 でこの手順を繰り返します。

## <a name="test-the-connection"></a>接続をテストする

サイト間接続が確立されたので、トラフィックがこの接続を経由できることをことを検証する必要があります。 検証するには、いずれかの ASDK 環境で作成した仮想マシンの 1 つにサインインします。 次に、もう 1 つの環境で作成した仮想マシンに ping を実行します。

トラフィックがサイト間接続を通過するように、VIP ではなく、リモート サブネットの仮想マシンのダイレクト IP (DIP) に ping を実行します。 これを実行するには、接続の反対側の DIP アドレスを探します。 後で使用するためにアドレスを保存します。

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>POC1 のテナント VM にサインインする

1. POC1 の Azure Stack 物理マシンにサインインし、テナント アカウントを使用してユーザー ポータルにサインインします。
2. 左側のナビゲーション バーで、**[計算]** を選択します。
3. VM の一覧で、先ほど作成した **VM01** を探して選択します。
4. 仮想マシンのブレードで、**[接続]** をクリックし、VM01.rdp ファイルを開きます。

     ![[接続] ボタン](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. 仮想マシンの作成時に構成したアカウントでサインインします。
6. 管理者特権の **Windows PowerShell** ウィンドウを開きます。
7. 「**ipconfig /all**」と入力します。
8. 出力で **IPv4 アドレス**を見つけ、後で使用するためにそのアドレスを保存します。 これは POC2 から ping を実行するアドレスです。 この例の環境ではアドレスは **10.0.10.4** ですが、実際の環境では異なる場合があります。 このアドレスは、先ほど作成した **10.0.10.0/24** サブネット内に含まれています。
9. 仮想マシンが ping に応答することを許可するファイアウォール ルールを作成するには、次の PowerShell コマンドを実行します。

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>POC2 のテナント VM にサインインする

1. POC2 の Azure Stack 物理マシンにサインインし、テナント アカウントを使用してユーザー ポータルにサインインします。
2. 左側のナビゲーション バーで、**[計算]** をクリックします。
3. 仮想マシンの一覧で、先ほど作成した **VM02** を探して選択します。
4. 仮想マシンのブレードで、 **[接続]** をクリックします。
5. 仮想マシンの作成時に構成したアカウントでサインインします。
6. 管理者特権の **Windows PowerShell** ウィンドウを開きます。
7. 「**ipconfig /all**」と入力します。
8. **10.0.20.0/24** の範囲内にある IPv4 アドレスが表示されます。 この例の環境ではアドレスは **10.0.20.4** ですが、実際の環境では異なる場合があります。
9. 仮想マシンが ping に応答することを許可するファイアウォール ルールを作成するには、次の PowerShell コマンドを実行します。

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. POC2 の仮想マシンから、トンネル経由で POC1 の仮想マシンに ping を実行します。 これを行うには、VM01 から記録した DIP に ping を実行します。 この例の環境ではこのアドレスは **10.0.10.4** ですが、必ずラボでメモしたアドレスに ping を実行するようにしてください。 結果は次の例のようになります。

    ![ping 成功](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. リモート仮想マシンからの応答は、テストが成功したことを示します。 仮想マシン ウィンドウを閉じてもかまいません。 ファイル コピーなど、他の種類のデータ転送を試し、接続をテストすることもできます。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>ゲートウェイ接続を使用したデータ転送の統計情報を表示する

サイト間接続を通過したデータの量を把握したい場合、**[接続]** ブレードでこの情報を確認できます。 このテストは、先ほど送信した ping が実際に VPN 接続を通過したことを検証するためのもう 1 つの手段でもあります。

1. POC2 のテナント仮想マシンにサインインしているとき、テナント アカウントを利用してユーザー ポータルにサインインします。
2. **[すべてのリソース]** に移動し、**POC2-POC1** 接続を選択します。 **[接続]** が表示されます。
3. **[接続]** ウィンドウには、**[受信データ]** と **[送信データ]** の統計情報が表示されます。 次のスクリーンショットでは大きな数値を確認できますが、これは追加のファイル転送によるものです。 ここには 0 以外の値が表示されます。

    ![受信データと送信データ](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
