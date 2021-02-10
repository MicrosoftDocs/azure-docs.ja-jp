---
title: Azure VMware Solution をデプロイして構成する
description: 計画ステージで収集した情報を使用して、Azure VMware Solution プライベート クラウドをデプロイする方法について説明します。
ms.topic: tutorial
ms.date: 12/24/2020
ms.openlocfilehash: f2b6f3c4ad82117fee96e0c2e5973a7011384d48
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760890"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware Solution をデプロイして構成する

この記事では、[計画セクション](production-ready-deployment-steps.md)の情報を使用して、Azure VMware Solution をデプロイします。 

>[!IMPORTANT]
>情報をまだ定義していない場合は、続行する前に[計画セクション](production-ready-deployment-steps.md)に戻ってください。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Azure VMware Solution のデプロイ

「[Azure VMware Solution のデプロイの計画](production-ready-deployment-steps.md)」という記事で収集した情報を使用します。

>[!NOTE]
>Azure VMware Solution をデプロイするには、サブスクリプションで、少なくとも共同作成者レベルである必要があります。

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>この手順全体の概要については、ビデオ「[Azure VMware Solution: デプロイ](https://www.youtube.com/embed/gng7JjxgayI)」をご覧ください。

## <a name="create-the-jump-box"></a>ジャンプ ボックスを作成する

>[!IMPORTANT]
>**[プライベート クラウドの作成]** 画面の初期プロビジョニング手順で **[Virtual Network]** オプションを空白のままにした場合は、このセクションを続行する **前** に、[VMware プライベート クラウドのネットワークを構成する](tutorial-configure-networking.md)ことに関するチュートリアルを完了してください。  

Azure VMware Solution のデプロイ後に、vCenter および NSX に接続する仮想ネットワークのジャンプ ボックスを作成します。 いったん ExpressRoute 回線と ExpressRoute Global Reach を構成したら、ジャンプ ボックスは必要ありません。  しかし、Azure VMware Solution 内の vCenter や NSX に接続するのに便利です。  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Azure VMware Solution のジャンプ ボックスを作成する" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

[デプロイ プロセスの一部として特定または作成した](production-ready-deployment-steps.md#attach-virtual-network-to-azure-vmware-solution)仮想ネットワーク内に仮想マシン (VM) を作成するには、次の手順に従います。 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>ExpressRoute を使用して仮想ネットワークに接続する

>[!IMPORTANT]
>Azure のデプロイ画面で既に仮想ネットワークを定義した場合は、次のセクションに進んでください。

デプロイ手順で仮想ネットワークを定義しておらず、Azure VMware Solution の ExpressRoute を既存の ExpressRoute ゲートウェイに接続する予定の場合は、次の手順に従います。

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>アドバタイズされるネットワーク ルートを確認する

ジャンプ ボックスは、Azure VMware Solution が ExpressRoute 回線を介して接続する仮想ネットワーク内にあります。  Azure で、ジャンプ ボックスのネットワーク インターフェイスに移動し、[有効なルートを表示](../virtual-network/manage-route-table.md#view-effective-routes)します。

有効なルートの一覧には、Azure VMware Solution のデプロイの一部として作成されたネットワークが表示されるはずです。 この記事の、以前の[デプロイ手順](#deploy-azure-vmware-solution)の間に[定義した `/22` ネットワーク](production-ready-deployment-steps.md#ip-address-segment)から派生した複数のネットワークが表示されます。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Azure VMware Solution から Azure Virtual Network にアドバタイズされたネットワーク ルートを確認する" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

この例では、デプロイ時にネットワークとして 10.74.72.0/22 が入力され、/24 ネットワークが派生しています。  同様の内容が表示される場合は、Azure VMware Solution 内の vCenter に接続できます。

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>VCenter と NSX-T に接続してサインインする

前の手順で作成したジャンプ ボックスにログインします。 ログインしたら Web ブラウザーを開き、vCenter および NSX の両方の管理コンソールに移動してログインします。  

Azure portal で、vCenter と NSX-T の、管理コンソールの IP アドレスと資格情報を識別できます。  お使いのプライベート クラウドを選択してから、 **[概要]** ビューで **[ID]> [既定値]** を選択します。 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Azure VMware Solution 上にネットワーク セグメントを作成する

Azure VMware Solution 環境に新しいネットワーク セグメントを作成するには、NSX-T を使用します。  作成するネットワークは、[計画セクション](production-ready-deployment-steps.md)で定義しました。  それらを定義していない場合は、続行する前に[計画セクション](production-ready-deployment-steps.md)に戻ってください。

>[!IMPORTANT]
>定義した CIDR ネットワーク アドレス ブロックが、Azure 内やオンプレミス環境内のものと重複していないことを確認してください。  

「[Azure VMware ソリューションで NSX-T ネットワーク セグメントを作成する](tutorial-nsx-t-network-segment.md)」というチュートリアルに従って、Azure VMware Solution 内に NSX-T ネットワーク セグメントを作成します。

## <a name="verify-advertised-nsx-t-segment"></a>アドバタイズされる NSX-T セグメントを確認する

「[アドバタイズされるネットワーク ルートを確認する](#verify-network-routes-advertised)」の手順に戻ります。 前の手順で作成したネットワーク セグメントを表す他のルートが一覧に表示されます。  

仮想マシンには、「[Azure VMware Solution 上にネットワーク セグメントを作成する](#create-a-network-segment-on-azure-vmware-solution)」の手順で作成したセグメントを割り当てます。  

DNS が必要なため、どの DNS サーバーを使用するかを特定します。  

- ExpressRoute Global Reach が構成されている場合は、オンプレミスで使用する任意の DNS サーバーを使用します。  
- Azure 内に DNS サーバーがある場合は、それを使用します。  
- どちらも使用していない場合は、ジャンプ ボックスで使用している任意の DNS サーバーを使用します。

>[!NOTE]
>この手順は DNS サーバーの識別用であり、この手順では構成は行われません。

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>(省略可能) DHCP サービスを NSX-T ネットワーク セグメントに提供する

NSX-T セグメントで DHCP を使用する予定の場合は、このセクションを続けます。 その他の場合は、「[NSX-T ネットワーク セグメントに VM を追加する](#add-a-vm-on-the-nsx-t-network-segment)」セクションに進みます。  

NSX-T ネットワーク セグメントを作成したら、Azure VMware Solution で DHCP を作成して管理できます。次の 2 とおりの方法があります。

* NSX-T を使用して DHCP サーバーをホストする場合は、[DHCP サーバーを作成](manage-dhcp.md#create-a-dhcp-server)し、[そのサーバーにリレー](manage-dhcp.md#create-dhcp-relay-service)する必要があります。 
* ネットワークでサード パーティの外部 DHCP サーバーを使用する場合は、[DHCP リレー サービスを作成](manage-dhcp.md#create-dhcp-relay-service)する必要があります。  このオプションの場合、[リレーの構成のみを行います](manage-dhcp.md#create-dhcp-relay-service)。


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>NSX-T ネットワーク セグメントに VM を追加する

Azure VMware Solution vCenter で VM をデプロイし、それを使用して、Azure VMware Solution ネットワークから以下への接続を確認します。

- インターネット
- Azure 仮想ネットワーク
- オンプレミス。  

どの vSphere 環境でもするように VM をデプロイします。  以前に NSX-T 内に作成したネットワーク セグメントのいずれかに VM を接続します。  

>[!NOTE]
>DHCP サーバーをセットアップすると、そこから VM のネットワーク構成が取得されます (スコープを設定するのを忘れないでください)。  静的に構成しようとしている場合は、通常どおりに構成します。

## <a name="test-the-nsx-t-segment-connectivity"></a>NSX-T セグメントの接続性をテストする

前の手順で作成した VM にログインし、接続性を確認します。

1. インターネット上の IP に対して ping を実行します。
2. Web ブラウザーでインターネット サイトに移動します。
3. Azure Virtual Network にあるジャンプ ボックスに ping を実行します。

Azure VMware Solution が稼働しており、Azure Virtual Network およびインターネットとの間に接続が正常に確立されました。

## <a name="next-steps"></a>次のステップ

次のセクションでは、ExpressRoute 経由で Azure VMware Solution をオンプレミス ネットワークに接続します。
> [!div class="nextstepaction"]
> [Azure VMware Solution をオンプレミス環境に接続する](azure-vmware-solution-on-premises.md)
