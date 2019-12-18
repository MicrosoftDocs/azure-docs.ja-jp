---
title: Azure Application Gateway と VMware 仮想マシンを使用する
description: Azure Application Gateway を使用して、CloudSimple プライベート クラウド環境内の VMware 仮想マシンで実行されている Web サーバーの受信 Web トラフィックを管理する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576046"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>CloudSimple プライベート クラウド環境内の VMware 仮想マシンで Azure Application Gateway を使用する

Azure Application Gateway を使用して、CloudSimple プライベート クラウド環境内の VMware 仮想マシンで実行されている Web サーバーの受信 Web トラフィックを管理することができます。

パブリックとプライベートのハイブリッド デプロイで Azure Application Gateway を利用することで、アプリケーションへの Web トラフィックを管理し、セキュリティで保護されたフロントエンドを提供し、VMware 環境で実行されているサービスの SSL 処理をオフロードすることができます。 Azure Application Gateway は、構成済みの規則と正常性プローブに従って、VMware 環境に存在するバックエンド プール インスタンスに受信 Web トラフィックをルーティングします。

この Azure Application Gateway ソリューションでは、次のことを行う必要があります。

* Azure サブスクリプションを取得する。
* Azure 仮想ネットワークと、仮想ネットワーク内のサブネットを作成および構成します。
* NSG ルールを作成および構成し、ExpressRoute を使用して vNet を CloudSimple プライベート クラウドにピアリングします。
* プライベート クラウドを作成および構成します。
* Azure Application Gateway を作成および構成します。

## <a name="azure-application-gateway-deployment-scenario"></a>Azure Application Gateway のデプロイのシナリオ

このシナリオでは、Azure Application Gateway は Azure 仮想ネットワーク内で実行されます。 仮想ネットワークは、ExpressRoute 回線経由でプライベートクラウドに接続されています。 プライベート クラウド内のすべてのサブネットは、仮想ネットワークのサブネットから IP で到達できます。

![Azure 仮想ネットワーク内の Azure ロード バランサー](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>ソリューションをデプロイする方法

デプロイのプロセスは、以下のタスクで構成されます。

1. [前提条件が満たされていることを確認する](#1-verify-prerequisites)
2. [Azure 仮想接続をプライベート クラウドに接続する](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Azure アプリケーション ゲートウェイをデプロイする](#3-deploy-an-azure-application-gateway)
4. [プライベート クラウド内で Web サーバー VM プールを作成および構成する](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1.前提条件を確認する

以下の前提条件が満たされていることを確認します。

* Azure Resource Manager と仮想ネットワークが既に作成されている。
* Azure 仮想ネットワーク内に (Application Gateway のための) 専用サブネットが既に作成されている。
* CloudSimple プライベート クラウドが既に作成されている。
* 仮想ネットワーク内の IP サブネットとプライベート クラウド内のサブネットの間で IP の競合がない。

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2.Azure 仮想ネットワークをプライベート クラウドに接続する

Azure 仮想ネットワークをプライベート クラウドに接続するには、次の手順に従います。

1. [CloudSimple ポータルで、ExpressRoute のピアリング情報をコピーします](virtual-network-connection.md)。

2. [Azure 仮想ネットワークの仮想ネットワーク ゲートウェイを構成します](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。

3. [仮想ネットワークを CloudSimple ExpressRoute 回線にリンクします](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)。

4. [コピーしたピアリング情報を使用して、仮想ネットワークを ExpressRoute 回線にリンクします](virtual-network-connection.md)。

## <a name="3-deploy-an-azure-application-gateway"></a>3.Azure アプリケーション ゲートウェイをデプロイする

これを行うための詳細な手順は、「[Azure portal を使用してパス ベースのルーティング規則のあるアプリケーション ゲートウェイを作成する](../application-gateway/create-url-route-portal.md)」で確認できます。 必要な手順の概要は次のとおりです。

1. ご利用のサブスクリプションとリソース グループに仮想ネットワークを作成します。
2. (専用サブネットとして使用する) サブネットを仮想ネットワーク内に作成します。
3. 標準の Application Gateway を作成します (オプションで WAF を有効にします): Azure portal のホーム ページで、ページの左上にある **[リソース]**  >  **[ネットワーク]**  >  **[Application Gateway]** の順にクリックします。 Standard SKU とサイズを選択し、Azure サブスクリプション、リソース グループ、場所の情報を指定します。 必要に応じて、このアプリケーション ゲートウェイ用に新しいパブリック IP を作成し、仮想ネットワークと、アプリケーション ゲートウェイの専用サブネットの詳細を指定します。
4. 仮想マシンを含むバックエンド プールを追加し、それをアプリケーション ゲートウェイに追加します。

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4.プライベート クラウド内で Web サーバー VM プールを作成および構成する

vCenter で、任意の OS と Web サーバー (Windows/IIS、Linux/Apache など) を使用して VM を作成します。 プライベート クラウド内の Web 層に対して指定されているサブネット/VLAN を選択します。 Web サーバー VM の少なくとも 1 つの vNIC が、Web 層のサブネット上にあることを確認します。
