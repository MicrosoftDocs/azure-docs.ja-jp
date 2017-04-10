---
title: "Azure における複数の IP 構成での負荷分散 | Microsoft Docs"
description: "プライマリ IP 構成とセカンダリ IP 構成の間の負荷分散。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: cf1e68c7b37b2506de007bdf24eea63a27187a33
ms.lasthandoff: 03/22/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-the-azure-portal"></a>Azure Portal を使用した複数の IP 構成での負荷分散

> [!div class="op_single_selector"]
> * [ポータル](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

この記事では、1 つのセカンダリ ネットワーク インターフェイス (NIC) に複数の IP アドレスがある Azure Load Balancer の使用方法について説明します。このシナリオには、Windows を実行する 2 つの VM があり、各 VM にプライマリ NIC とセカンダリ NIC が 1 つずつ存在します。 セカンダリ NIC には、どちらも 2 つの IP 構成が割り当てられています。 それぞれの VM は、contoso.com と fabrikam.com の両方の Web サイトをホストします。 それぞれの Web サイトは、セカンダリ NIC の IP 構成の 1 つにバインドされています。 ここで、Azure Load Balancer を使用して、それぞれの Web サイト用に 2 つのフロントエンド IP アドレスを公開して、トラフィックを Web サイトのそれぞれの IP 構成に分散します。 このシナリオでは、両方のバックエンド プール IP アドレスに加えて、両方のフロントエンドで同じポート番号を使用します。

![LB シナリオの画像](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

##<a name="prerequisites"></a>前提条件
この例では、次の構成の *contosofabrikam* という名前のリソース グループがあることを前提としています。
 -  *myVNet* という名前の仮想ネットワーク、*VM1* と *VM2* という名前の 2 つの VM が含まれています。VM は、*myAvailset* という名前の同じ可用性セット内に存在します。 
 - 各 VM には、プライマリ NIC とセカンダリ NIC が 1 つずつ存在します。 プライマリ NIC の名前は *VM1NIC1* と *VM2NIC1* で、セカンダリ NIC の名前は *VM1NIC2* と *VM2NIC2* です。 複数の NIC を持つ VM の作成の詳細については、「[PowerShell を使用して複数の NIC を持つ VM を作成する](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)」を参照してください。

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>複数の IP 構成で負荷分散を行うための手順

この記事で紹介するシナリオを実現するには、次の手順に従います。

### <a name="step-1-configure-the-secondary-nics-for-each-vm"></a>手順 1: 各 VM のセカンダリ NIC を構成する

仮想ネットワーク内の各 VM に、次のようにセカンダリ NIC の一連の IP 構成を追加します。  

1. ブラウザーから Azure Portal (http://portal.azure.com) に移動し、Azure アカウントでログインします。
2. 画面の左上にある [リソース グループ] アイコンをクリックし、VM が配置されているリソース グループ (たとえば、*contosofabrikam*) をクリックします。 すべてのリソースと VM のネットワーク インターフェイスの一覧が表示される **[リソース グループ]** ブレードが表示されます。
3. 各 VM のセカンダリ NIC に次のように IP 構成を追加します。
    1. IP 構成を追加するネットワーク インターフェイスを選択します。
    2. 選択した NIC について表示されるブレードで、**[IP 構成]** をクリックします。 表示されたブレードの上部にある **[追加]** をクリックします。
    3. **[Add IP configurations (IP 構成の追加)]** ブレードで、次のように NIC に 2 つ目の IP 構成を追加します。 
        1. セカンダリ IP 構成の名前を入力します (たとえば、VM1 と VM2 の IP 構成にそれぞれ *VM1NIC2-ipconfig2* と *VM2NIC2-ipconfig2* という名前を付けます)。
        2. **[プライベート IP アドレス]** の **[割り当て]** で **[静的]** を選択します。
        3. **[OK]**をクリックします。
        4. セカンダリ NIC の 2 つ目の IP 構成が完了すると、該当する NIC の **[IP 構成]** 設定ブレードに表示されます。

### <a name="step-2-create-a-load-balancer"></a>手順 2: ロード バランサーを作成する

次のようにロード バランサーを作成します。

1. ブラウザーから Azure Portal (http://portal.azure.com) に移動し、Azure アカウントでログインします。
2. 画面の左上で、**[新規]** > **[ネットワーク]** > **[ロード バランサー]** の順にクリックします。 次に、**[作成]** をクリックします。
3. **[ロード バランサーの作成]** ブレードで、ロード バランサーの名前を入力します。 ここでは、*mylb* という名前を付けます。
4. [パブリック IP アドレス] で、**PublicIP1** という名前の新しいパブリック IP を作成します。
5. [リソース グループ] で、VM の既存のリソース グループ (たとえば、*contosofabrikam*) を選択します。 次に、適切な場所を選択し、**[OK]** をクリックします。 ロード バランサーのデプロイが開始されます。デプロイが正常に完了するまでに数分かかります。
6. デプロイすると、ロード バランサーがリソース グループ内のリソースとして表示されます。

### <a name="step-3-configure-the-frontend-ip-pool"></a>手順 3: フロントエンド IP プールを構成する

次のように各 Web サイト (Contoso と Fabrikam) のフロントエンド IP プールを構成します。

1. ポータルで、**[その他のサービス]** をクリックし、フィルター ボックスに「**パブリック IP アドレス**」と入力して、**[パブリック IP アドレス]** をクリックします。 表示されたブレードの上部にある **[追加]** をクリックします。
2. 次のように両方の Web サイト (contoso と fabrikam) の 2 つのパブリック IP アドレス (*PublicIP1* と *PublicIP2*) を構成します。
    1. フロントエンド IP アドレスの名前を入力します。
    2. **[リソース グループ]** で、VM の既存のリソース グループ (たとえば、*contosofabrikam*) を選択します。
    3. **[場所]** で VM と同じ場所を選択します。
    4. **[OK]**をクリックします。
    5. 2 つのパブリック IP アドレスが作成されると、両方が **[パブリック IP アドレス]** ブレードに表示されます。
3. ポータルで、**[その他のサービス]** をクリックし、フィルター ボックスに「**ロード バランサー**」と入力して、**[ロード バランサー]** をクリックします。  
4. フロントエンド IP プールを追加するロード バランサー (*mylb*) を選択します。
5. **[設定]** で、**[Frontend Pools (フロントエンド プール)]** を選択します。 表示されたブレードの上部にある **[追加]** をクリックします。
6. フロントエンド IP アドレスの名前 (*farbikamfe* または **contosofe*) を入力します。
7. **[IP アドレス]** をクリックし、**[パブリック IP アドレスの選択]** ブレードで、フロントエンドの IP アドレス (*PublicIP1* または *PublicIP2*) を選択します。
8. このセクションの手順 3. ～ 7. を繰り返して、2 つ目のフロントエンド IP アドレスを作成します。
9. フロントエンド IP プールの構成が完了すると、両方のフロントエンド IP アドレスがロード バランサーの **[フロントエンド IP プール]** ブレードに表示されます。 
    
### <a name="step-4-configure-the-backend-pool"></a>手順 4: バックエンド プールを構成する   
次のように各 Web サイト (Contoso と Fabrikam) のロード バランサーでバックエンド アドレス プールを構成します。
        
1. ポータルで、**[その他のサービス]** をクリックし、フィルター ボックスに「ロード バランサー」と入力して、**[ロード バランサー]** をクリックします。  
2. バックエンド プールを追加するロード バランサー (*mylb*) を選択します。
3. **[設定]** で、**[バックエンド プール]** を選択します。 バックエンド プールの名前 (たとえば、*contosopool* や *fabrikampool*) を入力します。 表示されたブレードの上部にある **[追加]** ボタンをクリックします。 
4. **[関連付け先]** で **[可用性セット]** を選択します。
5. **[可用性セット]** で **[myAvailset]** を選択します。
6. 次のように両方の VM のターゲット ネットワーク IP 構成を追加します (図 2 を参照)。  
    1. **[ターゲット仮想マシン]** で、バックエンド プールに追加する VM (たとえば、VM1 や VM2) を選択します。
    2. **[Network IP configuration (ネットワーク IP 構成)]** で、その VM のセカンダリ NIC の IP 構成 (たとえば、VM1NIC2-ipconfig2 や VM2NIC2-ipconfig2) を選択します。
    ![LB シナリオの画像](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
            
        **図 2**: バックエンド プールを使用したロード バランサーの構成  
7. **[OK]**をクリックします。
8. バックエンド プールの構成が完了すると、両方のバックエンド アドレス プールがロード バランサーの **[バックエンド プール]** ブレードに表示されます。

### <a name="step-5-configure-a-health-probe-for-your-load-balancer"></a>手順 5: ロード バランサーの正常性プローブを構成する
次のようにロード バランサーの正常性プローブを構成します。
    1. ポータルで、[その他のサービス] をクリックし、フィルター ボックスに「ロード バランサー」と入力して、**[ロード バランサー]** をクリックします。  
    2. バックエンド プールを追加するロード バランサーを選択します。
    3. **[設定]** で、**[正常性プローブ]** を選択します。 表示されたブレードの上部にある **[追加]** をクリックします。
    4. 正常性プローブの名前 (たとえば、HTTP) を入力し、**[OK]** をクリックします。

### <a name="step-6-configure-load-balancing-rules"></a>手順 6: 負荷分散規則を構成する
次のように各 Web サイトの負荷分散規則 (*HTTPc* と *HTTPf*) を構成します。
    
1. **[設定]** で、**[正常性プローブ]** を選択します。 表示されたブレードの上部にある **[追加]** をクリックします。
2. **[名前]** に、負荷分散規則の名前 (たとえば、Contoso には *HTTPc*、Fabrikam には *HTTPf*) を入力します。
3. [フロントエンド IP アドレス] で、フロントエンド IP アドレス (たとえば、*Contosofe* や *Fabrikamfe*) を選択して、
4. **[ポート]** と **[バックエンド ポート]** については、既定値の **80** のままにします。
5. **[フローティング IP (ダイレクト サーバー リターン)]** で、**[有効]** をクリックします。
6. **[OK]**をクリックします。
7. このセクションの手順 1. ～ 6. を繰り返して、2 つ目の負荷分散規則を作成します。
8. 負荷分散規則の構成が完了すると、両方の規則 (*HTTPc* と *HTTPf*) がロード バランサーの **[負荷分散規則]** ブレードに表示されます。

### <a name="step-7-configure-dns-records"></a>手順 7: DNS レコードを構成する
最後に、ロード バランサーの各フロントエンド IP アドレスを指すように DNS リソース レコードを構成する必要があります。 ドメインを Azure DNS でホストする場合もあります。 Azure DNS を Load Balancer で使用する方法の詳細については、「[Azure DNS を他の Azure サービスで使用する](../dns/dns-for-azure-services.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- Azure で負荷分散サービスを組み合わせて使用する方法の詳細については、「[Azure で負荷分散サービスを使用する](../traffic-manager/traffic-manager-load-balancing-azure.md)」を参照してください。
- Azure の各種ログを使用して、ロード バランサーの管理やトラブルシューティングを行う方法については、「[Azure Load Balancer のログ分析](../load-balancer/load-balancer-monitor-log.md)」を参照してください。

