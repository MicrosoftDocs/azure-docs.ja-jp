---
title: "ネットワーク セキュリティ グループのトラブルシューティング - ポータル | Microsoft Docs"
description: "Azure Resource Manager デプロイメント モデルで、Azure ポータルを使用してネットワーク セキュリティ グループをトラブルシューティングする方法について説明します。"
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3216868d867f4c840a610c45855d22575ded609c


---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Azure ポータルを使用したネットワーク セキュリティ グループのトラブルシューティング
> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

この記事では、仮想マシン (VM) にネットワーク セキュリティ グループ (NSG) を構成している場合に、VM の接続の問題が発生したときのトラブルシューティングに役立つ NSG の診断機能の概要を説明します。

NSG では、仮想マシン (VM) を出入りするトラフィックのタイプを制御できます。 NSG は、Azure Virtual Network (VNet)、ネットワーク インターフェイス (NIC)、または両方のサブネットに適用できます。 NIC に効果的に規則を適用するには、NSG 内にある規則の集合体を NIC と接続しているサブネットに適用します。 場合によっては、これらのNSG 全体の規則が競合し、VM のネットワーク接続に影響を及ぼすことがあります。  

VM の NIC に適用されると、有効なセキュリティ規則を NSG からすべて表示できます。 この記事では、Azure Resource Manager デプロイメント モデルで、これらの規則を使用して VM の接続問題をトラブルシューティングする方法について説明します。 VNet と NSG の概念については、[仮想ネットワーク](virtual-networks-overview.md)と[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)の概要に関する記事を参照してください。

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>有効なセキュリティ規則を使用した VM トラフィック フローのトラブルシューティング
次のシナリオでは、一般的な接続の問題の例を示します。

*VM1* という名前の VM は、*WestUS-VNet1* という名前の VNet 内にある *Subnet1* という名前のサブネットの一部です。 TCP ポート 3389 経由で RDP を使用して、VM への接続に失敗しました。 NSG は、*VM1-NIC1* という NIC と *Subnet1* というサブネットの両方に適用されています。 TCP ポート 3389 へのトラフィックは、ネットワーク インターフェイス「 *VM1-NIC1*」に関連付けられている NSG で許可されていますが、VM1 のポート 3389 への ping が失敗します。

この例では TCP ポート 3389 を使用していますが、次の手順は任意のポート経由での受信/送信接続の失敗の確認に使用できます。

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>仮想マシンの有効なセキュリティ規則を表示する
VM の NSG のトラブルシューティングを行うには、次の手順を実行します。

NIC の有効なセキュリティ規則の完全な一覧は、VM から閲覧できます。 操作を行う権限を持っていれば、有効な規則のブレードから NIC とサブネット両方の NSG ルールを追加、変更、削除できます。

1. https://portal.azure.com で Azure ポータルにログインします。
2. **[その他のサービス]** をクリックし、表示される一覧で **[仮想マシン]** をクリックします。
3. 表示される一覧からトラブルシューティングを実行する VM を選択すると、VM のブレードとオプションが表示されます。
4. **[問題の診断と解決]** をクリックし、一般的な問題を選択します。 例として、 **[Windows VM に接続できません]** を選択します。 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. 次の図のように、問題の下に手順が表示されます。 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    推奨される手順の一覧で、 *[effective security group rules (有効なセキュリティ グループ規則)]* をクリックします。
6. 次の図のように、 **[Get effective security rules (有効なセキュリティ規則の取得)]** ブレードが表示されます。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    画像の次のセクションにご注目ください。
   
   * **[スコープ]:** 手順 3 で選択した「 *VM1*」に設定されています。
   * **[ネットワーク インターフェイス]:** *VM1-NIC1* を選択します。 VM には複数のネットワーク インターフェイス (NIC) を実装できます。 各 NIC には一意の有効なセキュリティ規則を使用できます。 トラブルシューティングを行うときは、それぞれの NIC の有効なセキュリティの規則の表示が必要になる場合があります。
   * **[Associated NSGs (関連付けられている NSG)]:** NSG は、NIC と NIC が接続されているサブネットの両方に適用できます。 この図では、NIC と接続しているサブネットの両方に NSG が適用されています。 NSG 名をクリックすると、NSG の規則を直接変更できます。
   * **[VM1-nsg] タブ:** 図に表示されている規則の一覧は、NIC に適用された NSG です。 NSG の作成時には、複数の既定の規則が Azure によって作成されます。 既定の規則は削除できませんが、優先順位の高い規則を使ってオーバーライドできます。 既定の規則の詳細については、「 [NSG の概要](virtual-networks-nsg.md#default-rules) 」の記事をご覧ください。
   * **[変換先] 列:** 列にテキストが入る規則と、アドレス プレフィックスが入る規則があります。 テキストは、セキュリティ規則の作成時に適用された既定のタグの名前です。 タグは、システムから提供された識別子で、複数のプレフィックスを表します。 *AllowInternetOutBound*などのタグ付きの規則を選択すると、 **アドレス プレフィックス** ブレードにプレフィックスの一覧が表示されます。
   * **[ダウンロード]:** 規則の一覧には多数の項目が含まれる場合があります。 **[ダウンロード]** をクリックしてファイルを保存すると、規則の .csv ファイルをダウンロードして、オフライン分析に使用できます。
   * **[AllowRDP]** 受信規則: この規則は VM への RDP 接続を許可します。
7. **[Subnet1-NSG]** タブをクリックして、次の図のように NSG からサブネットに適用した有効な規則を表示します。 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
     *denyRDP* **受信** 規則にご注目ください。 サブネットに適用された受信の規則は、ネットワーク インターフェイスに適用された規則よりも先に評価されます。 サブネットに拒否規則が適用されているため、NIC の許可規則は評価されることなく、TCP 3389 への接続要求は失敗します。 
   
    *DenyRDP* 規則が、RDP 接続が失敗した理由です。 これを削除すれば、問題が解決するはずです。
   
   > [!NOTE]
   > NIC に関連付けられている VM が実行状態でないか、NSG が NIC やサブネットに適用されていない場合は、規則は表示されません。
   > 
   > 
8. NSG 規則を編集するには、 *[Associated NSGs (関連付けられた NSG)]* セクションの **[Subnet1-NSG]** をクリックします。
   **Subnet1-NSG** ブレードが開きます。 **[受信セキュリティ規則]**をクリックすると規則を直接編集できます。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. **Subnet1-NSG** で *denyRDP* の受信規則を削除し、*allowRDP* 規則を追加すると、有効な規則の一覧は次の図のようになります。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    VM への RDP 接続を開始するか、PsPing ツールを使用して、TCP ポート 3389 が開いていることを確認します。 PsPing の詳細については、 [PsPing のダウンロード ページ](https://technet.microsoft.com/sysinternals/psping.aspx)をご覧ください。

### <a name="view-effective-security-rules-for-a-network-interface"></a>ネットワーク インターフェイスの有効なセキュリティ規則を表示する
特定の NIC で VM トラフィックが影響を受けている場合は、次の手順を使用してネットワーク インターフェイスのコンテキストから NIC の有効な規則の完全な一覧を表示できます。

1. https://portal.azure.com で Azure ポータルにログインします。
2. **[その他のサービス]** をクリックし、表示される一覧で **[ネットワーク インターフェイス]** をクリックします。
3. ネットワーク インターフェイスを選択します。 次の図では、 *VM1 NIC1* という名前の NIC が選択されています。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    **[スコープ]** が選択したネットワーク インターフェイスに設定されています。 表示されているその他の情報については、この記事の「 **Troubleshoot NSGs for a VM (VM の NSG のトラブルシューティング)** 」セクションの手順 6 をご覧ください。
   
   > [!NOTE]
   > ネットワーク インターフェイスから NSG が削除された場合は、サブネットの NSG がその NIC で引き続き有効になります。 この場合、出力にはサブネットの NSG 規則のみが表示されます。 規則は、NIC が VM にアタッチされている場合にのみ表示されます。
   > 
   > 
4. NIC やサブネットに関連付けられている NSG の規則を直接編集できます。 方法については、この記事の「 **View effective security rules for a virtual machine (仮想マシンの有効なセキュリティ規則を表示する)** 」セクションの手順 8 をご覧ください。

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>ネットワーク セキュリティ グループ (NSG) の有効なセキュリティ規則を表示する
NSG 規則を変更する際、特定の VM に追加された規則による影響を確認できます。 NSG が適用されているすべての NIC の有効なセキュリティ規則の完全な一覧は、NSG ブレードからコンテキストを切り替えることなく確認できます。 NSG 内で有効な規則をトラブルシューティングするには、次の手順を実行します。

1. https://portal.azure.com で Azure ポータルにログインします。
2. **[その他のサービス]** をクリックし、表示される一覧で **[ネットワーク セキュリティ グループ]** をクリックします。
3. NSG を選択します。 次の図では、「VM1-nsg」という名前の NSG が選択されています。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    前の画像の次のセクションにご注目ください。
   
   * **[スコープ]:** 選択したNSG に設定します。
   * **[仮想マシン]:** NSG をサブネットに適用すると、サブネットに接続しているすべての VM にアタッチされたすべてのネットワーク インターフェイスに適用されます。 この一覧に、この NSG が適用されるすべての VM が表示されます。 一覧から、任意の VM を選択できます。
     
     > [!NOTE]
     > NSG が空のサブネットのみに適用された場合、VM は一覧に表示されません。 NSG が VM に関連付けられていない NIC に適用されている場合、一覧には NIC も表示されません。 
     > 
     > 
   * **[ネットワーク インターフェイス]:** VM には複数のネットワーク インターフェイスを実装できます。 選択した仮想マシンにアタッチされているネットワーク インターフェイスを選択できます。
   * **[AssociatedNSGs]:** NIC には、NIC に 1 つ、サブネットに 1 つというように、常に最大 2 つの有効な NSG を適用できます。 スコープに「VM1-nsg」が選択されていても、NIC に有効なサブネット NSG がある場合は、出力には両方の NSG が表示されます。
4. NIC やサブネットに関連付けられている NSG の規則を直接編集できます。 方法については、この記事の「 **View effective security rules for a virtual machine (仮想マシンの有効なセキュリティ規則を表示する)** 」セクションの手順 8 をご覧ください。

表示されているその他の情報については、この記事の「 **View effective security rules for a virtual machine (仮想マシンの有効なセキュリティ規則を表示する)** 」セクションの手順 6 をご覧ください。

> [!NOTE]
> サブネットと NIC にそれぞれ適用できるのは 1 つの NSG のみですが、NSG は複数の NIC とサブネットに関連付けることができます。
> 
> 

## <a name="considerations"></a>考慮事項
接続問題のトラブルシューティングを行う場合は、次の点を検討してください。

* 既定の NSG 規則は、インターネットからの着信アクセスをブロックし、VNet の受信トラフィックのみを許可します。 必要に応じて、インターネットからの着信アクセスを許可する規則を追加します。
* VM のネットワーク接続の失敗を引き起こす NSG セキュリティ規則がない場合は、他の原因が考えられます。
  * VM のオペレーティング システム内で実行されているファイアウォール ソフトウェア
  * 仮想アプライアンスまたはオンプレミスのトラフィック用に構成されたルート。 インターネット トラフィックは、強制トンネリングを使用してオンプレミスにリダイレクトできます。 インターネットから VM への RDP/SSH 接続は、オンプレミスのネットワーク ハードウェアがこのトラフィックを処理する方法によっては、この設定では機能しない場合があります。 VM を出入りするトラフィックのフローを妨げているルートの問題を診断する方法については、「 [Troubleshooting Routes (ルートのトラブルシューティング)](virtual-network-routes-troubleshoot-powershell.md) 」の記事をご覧ください。 
* 既定では、VNet をピアリングした場合、VIRTUAL_NETWORK タグはピアリングされている VNet のプレフィックスを含めるように自動的に拡張されます。 これらのプレフィックスは、 **ExpandedAddressPrefix** の一覧で表示でき、VNet ピアリングの接続に関連する問題をトラブルシューティングする際に使用できます。 
* 有効なセキュリティの規則は、VM の NIC やサブネットに関連付けられている NSG がある場合のみ表示されます。 
* NIC やサブネットに関連付けられている NSG がなく、VM にパブリック IP アドレスを割り当てている場合は、着信/発信アクセス用にすべてのポートが開きます。 VM にパブリック IP アドレスがある場合は、NIC またはサブネットに NSG を適用することを強くお勧めします。




<!--HONumber=Nov16_HO3-->


