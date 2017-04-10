---
title: "インターネットに接続するロード バランサーの作成 - Azure Portal | Microsoft Docs"
description: "Azure ポータルを使用して、Resource Manager でインターネットに接続するロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: db7c328b2ba7008b9d34275341fa4bad9522b028

---

# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Azure ポータルを使用したインターネットに接続するロード バランサーの作成

> [!div class="op_single_selector"]
> * [ポータル](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [従来のデプロイを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

ここでは、ロード バランサーを作成するために実行する必要のある一連の作業を個別に取り上げ、目的を達成するために実行する事柄を詳しく説明します。

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>インターネットに接続するロード バランサーを作成するために必要な項目

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

* フロントエンド IP 構成 - 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。
* バックエンド アドレス プール - ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。
* 負荷分散規則 - ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートにマッピングする規則が含まれます。
* 受信 NAT 規則 - ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。
* プローブ - バックエンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

Azure Resource Manager でのロード バランサー コンポーネントの詳細については、「 [Azure Resource Manager による Load Balancer のサポート](load-balancer-arm.md)」をご覧ください。

## <a name="set-up-a-load-balancer-in-azure-portal"></a>Azure ポータルでロード バランサーを設定する

> [!IMPORTANT]
> この例では、 **myVNet**という名前の仮想ネットワークが存在すことを前提としています。 そのためには、 [仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) に関するページを参照してください。 また、**myVNet** 内に **LB-Subnet-BE** という名前のサブネットがあり、**web1** と **web2** という名前の&2; つの VM が **myVNet** の **myAvailSet** という名前の同じ可用性セット内に存在することも前提としています。 VM を作成するには、 [このリンク](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を参照してください。

1. ブラウザーから Azure ポータル ( [http://portal.azure.com](http://portal.azure.com) ) に移動し、Azure アカウントでログインします。
2. 画面の左上で、**[新規]** > **[ネットワーク]** > **[ロード バランサー]** の順に選択します。
3. **[ロード バランサーの作成]** ブレードで、ロード バランサーの名前を入力します。 ここでは、 **myLoadBalancer**という名前を付けます。
4. **[種類]** から **[パブリック]** を選択します。
5. **[パブリック IP アドレス]** で、**myPublicIP** という名前の新しいパブリック IP を作成します。
6. [リソース グループ] から **[myRG]** を選択します。 次に、適切な **[場所]** を選択し、**[OK]** をクリックします。 ロード バランサーのデプロイが開始されます。デプロイが正常に完了するまでに数分かかります。

    ![ロード バランサーのリソース グループの更新](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)

## <a name="create-a-back-end-address-pool"></a>バックエンド アドレス プールを作成する

1. ロード バランサーが正常にデプロイされたら、リソースからそのロード バランサーを選択します。 [設定] で、[バックエンド プール] を選択します。 バックエンド プールの名前を入力します。 表示されたブレードの上部にある **[追加]** ボタンをクリックします。
2. **[バックエンド プールの追加]** ブレードで **[仮想マシンの追加]** をクリックします。  **[可用性セット]** で **[可用性セットの選択]** を選択し、**[myAvailSet]** を選択します。 次に、このブレードの [仮想マシン] セクションで **[仮想マシンの選択]** を選択し、**[web1]** と **[web2]** (負荷分散用に作成した&2; つのVM) をクリックします。 次の図に示すように、両方の左側に青色のチェック マークが付いていることを確認します。 このブレードで **[選択]** をクリックしてから **[仮想マシンの選択]** ブレードで [OK] をクリックし、**[バックエンド プールの追加]** ブレードで **[OK]** をクリックします。

    ![バックエンド アドレス プールへの追加 ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. 通知のドロップダウン リストが、VM **web1** と **web2** 両方のネットワーク インターフェイスの更新に加えて、ロード バランサーのバックエンド プールの保存に関する更新内容が反映されていることを確認します。

## <a name="create-a-probe-lb-rule-and-nat-rules"></a>プローブ、LB 規則、NAT 規則を作成する

1. 正常性プローブを作成します。

    ロード バランサーの [設定] で [プローブ] を選択します。 次に、ブレード上部の **[追加]** をクリックします。

    プローブは、HTTP と TCP の&2; とおりの方法で構成できます。 この例では HTTP を紹介しますが、TCP も同様の方法で構成できます。
    必要な情報を更新します。 既に説明したように、 **myLoadBalancer** は、ポート 80 のトラフィックを負荷分散します。 選択したパスは HealthProbe.aspx、[間隔] は 15 秒、[異常しきい値] は 2 です。 完了したら、 **[OK]** をクリックしてプローブを作成します。

    これらの個々の構成と、要件に応じてこれらを変更する方法を確認するには、[i] アイコン上にポインターを置いてください。

    ![プローブの追加](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. ロード バランサー規則を作成します。

    ロード バランサーの [設定] セクションで [負荷分散規則] をクリックします。 新しいブレードで **[追加]**をクリックします。 規則に名前を付けます。 ここでは、「HTTP」です。 フロントエンド ポートとバックエンド ポートを選択します。 ここでは、両方に「80」を選択します。 [バックエンド プール] に **[LB-backend]** を選択し、[プローブ] に、前に作成した **[HealthProbe]** を選択します。 その他の構成は、要件に合わせて設定できます。 [OK] をクリックして負荷分散規則を保存します。

    ![負荷分散規則の追加](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. 受信 NAT 規則を作成する

    ロード バランサーの [設定] セクションで [受信 NAT 規則] をクリックします。 新しいブレードで **[追加]**をクリックします。 受信 NAT 規則に名前を付けます。 ここでは、「 **inboundNATrule1**」という名前を付けます。 宛先には、前に作成したパブリック IP を指定する必要があります。 [サービス] で [カスタム] を選択し、使用するプロトコルを選択します。 ここでは [TCP] を選択します。 [ポート] に「3441」を入力し、ここでは [ターゲット ポート] に「3389」を入力します。 [OK] をクリックしてこの規則を保存します。

    最初の規則を作成したら、2 番目の受信 NAT 規則を作成するためにこの手順を繰り返します。2 番目の規則には、「inboundNATrule2」という名前を付けて、[ポート] に「3442」、[ターゲット ポート] に「3389」を指定します。

    ![受信 NAT 規則の追加](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Load Balancer の削除

ロード バランサーを削除するには、削除するロード バランサーを選択します。 *[ロード バランサー]* ブレードをクリックし、ブレードの上部にある **[削除]** をクリックします。 メッセージが表示されたら **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-cli.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Jan17_HO4-->


