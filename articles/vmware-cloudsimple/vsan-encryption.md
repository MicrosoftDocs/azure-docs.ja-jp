---
title: Azure VMware Solution by CloudSimple - プライベート クラウドの vSAN 暗号化の構成
description: CloudSimple プライベート クラウドが、Azure 仮想ネットワーク内で実行されているキー管理サーバーと連携できるように、vSAN ソフトウェア暗号化機能を構成する方法について説明します。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5da05c7f3c6878b0804799360e512676b9002d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899050"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドの vSAN 暗号化の構成

CloudSimple プライベート クラウドが、Azure 仮想ネットワーク内で実行されているキー管理サーバーと連携できるように、vSAN ソフトウェア暗号化機能を構成できます。

VMware では、vSAN 暗号化を使用する場合、KMIP 1.1 に準拠した外部のサードパーティ キー管理サーバー (KMS) ツールを使用する必要があります。 VMware によって認定され、Azure で利用可能な、サポートされている KMS を利用できます。

このガイドでは、Azure 仮想ネットワークで実行されている HyTrust KeyControl KMS を使用する方法について説明します。 vSAN 用のその他の認定サードパーティ KMS ソリューションにも同様のアプローチを使用できます。

この KMS ソリューションでは、次のことを行う必要があります。

* Azure 仮想ネットワークで VMware 認定サードパーティ KMS ツールをインストール、構成、管理する。
* KMS ツールの自分のライセンスを提供する。
* Azure 仮想ネットワークで実行されているサードパーティ KMS ツールを使用して、プライベート クラウドで vSAN 暗号化を構成および管理する。

## <a name="kms-deployment-scenario"></a>KMS のデプロイ シナリオ

KMS サーバー クラスターは、Azure 仮想ネットワーク内で実行され、構成済みの Azure ExpressRoute 接続を介してプライベート クラウド vCenter から IP 接続可能です。

![../media/KMS クラスター (Azure 仮想ネットワーク内)](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>ソリューションをデプロイする方法

デプロイ プロセスは、次の手順で行います。

1. [前提条件が満たされていることを確認する](#verify-prerequisites-are-met)
2. [CloudSimple ポータル: ExpressRoute のピアリング情報を取得する](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure portal: 仮想ネットワークをプライベート クラウドに接続する](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure portal: 仮想ネットワークに HyTrust KeyControl クラスターをデプロイする](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: KMIP サーバーを構成する](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI: Azure 仮想ネットワーク内の KMS クラスターを使用するように vSAN 暗号化を構成する](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>前提条件が満たされていることを確認する

デプロイの前に、次のことを確認します。

* 選択した KMS ベンダー、ツール、バージョンが、vSAN 互換性リストに存在する。
* 選択したベンダーが、Azure で実行するツールのバージョンをサポートしている。
* Azure バージョンの KMS ツールが KMIP 1.1 に準拠している。
* Azure Resource Manager と仮想ネットワークが既に作成されている。
* CloudSimple プライベート クラウドが既に作成されている。

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple ポータル: ExpressRoute のピアリング情報を取得する

セットアップを続行するには、ExpressRoute の承認キーとピア回線 URI、および Azure サブスクリプションへのアクセスが必要です。 この情報は、CloudSimple ポータルの仮想ネットワーク接続に関するページで確認できます。 手順については、[プライベート クラウドへの仮想ネットワーク接続の設定](virtual-network-connection.md)に関する記事を参照してください。 情報の取得に問題がある場合は、 [サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてください。

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure portal: 仮想ネットワークをプライベート クラウドに接続する

1. 「[Azure portal を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)」の手順に従って、仮想ネットワークの仮想ネットワーク ゲートウェイを作成します。
2. 「[ポータルを使用して仮想ネットワークを ExpressRoute 回線に接続する](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)」の手順に従って、仮想ネットワークを CloudSimple ExpressRoute 回線にリンクします。
3. CloudSimple からのウェルカム メールで受信した CloudSimple ExpressRoute 回線情報を使用して、仮想ネットワークを Azure 内の CloudSimple ExpressRoute 回線にリンクします。
4. 認証キーとピア回線 URI を入力し、接続に名前を付けて、**[OK]** をクリックします。

![仮想ネットワークの作成時に CS ピア回線 URI を指定する](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure portal: 仮想ネットワーク内の Azure Resource Manager に HyTrust KeyControl クラスターをデプロイする

仮想ネットワーク内の Azure Resource Manager に HyTrust KeyControl クラスターをデプロイするには、以下のタスクを実行します。 詳細については、[HyTrust のドキュメント](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)を参照してください。

1. HyTrust のドキュメントに記載されている手順に従って、指定された受信規則で Azure ネットワーク セキュリティ グループ (nsg-hytrust) を作成します。
2. Azure で SSH キーの組を生成します。
3. Azure Marketplace のイメージから最初の KeyControl ノードをデプロイします。  生成されたキーの組の公開キーを使用し、KeyControl ノードのネットワーク セキュリティ グループとして **nsg-hytrust** を選択します。
4. KeyControl のプライベート IP アドレスを静的 IP アドレスに変換します。
5. そのパブリック IP アドレスと、前述したキーの組の秘密キーを使用して、KeyControl VM に SSH 接続します。
6. SSH シェルで確認を求められたら、`No` を選択して、そのノードを最初の KeyControl ノードとして設定します。
7. この手順の手順 3 から 5 を繰り返し、既存のクラスターへの追加に関して確認を求められたら、`Yes` を選択して、KeyControl ノードをさらに追加します。

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: KMIP サーバーを構成する

https://*public-ip* に移動します。ここで、*public-ip* は KeyControl ノード VM のパブリック IP アドレスです。 [HyTrust のドキュメント](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)に記載されているこれらの手順に従ってください。

1. [KMIP サーバーの構成](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [VMware 暗号化用の証明書バンドルの作成](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI: Azure 仮想ネットワーク内の KMS クラスターを使用するように vSAN 暗号化を構成する

HyTrust の手順に従って、[vCenter で KMS クラスターを作成](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)します。

![vCenter での KMS クラスターの詳細の追加](media/vsan-config01.png)

VCenter で、**[クラスター]、[構成]** の順に移動し、vSAN の **[全般]** オプションを選択します。 暗号化を有効にし、以前に vCenter に追加した KMS クラスターを選択します。

![vCenter での vSAN 暗号化の有効化と KMS クラスターの構成](media/vsan-config02.png)

## <a name="references"></a>References

### <a name="azure"></a>Azure

[Azure portal を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[ポータルを使用して仮想ネットワークを ExpressRoute 回線に接続する](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl と Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[KMIP サーバーの構成](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[VMware 暗号化用の証明書バンドルの作成](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[vSphere での KMS クラスターの作成](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
