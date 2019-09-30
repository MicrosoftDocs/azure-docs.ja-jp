---
title: CloudSimple による Azure VMware ソリューション - vSphere クライアントへのアクセス
description: プライベート クラウドの vCenter にアクセスする方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196865"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>プライベート クラウドの vCenter ポータルへのアクセス

プライベート クラウドの vCenter ポータルは、Azure portal または CloudSimple ポータルから起動できます。  vCenter ポータルでは、プライベート クラウド上の VMware インフラストラクチャを管理できます。

## <a name="before-you-begin"></a>開始する前に

vCenter ポータルにアクセスするには、ネットワーク接続を確立し、DNS 名前解決を有効にする必要があります。  次のいずれかのオプションを使用して、プライベート クラウドへのネットワーク接続を確立できます。

* [ExpressRoute を使用してオンプレミスから CloudSimple に接続する](on-premises-connection.md)
* [CloudSimple プライベート クラウドへの VPN 接続を構成する](set-up-vpn.md)

プライベート クラウドの VMware インフラストラクチャ コンポーネントの DNS 名前解決を設定するには、「[オンプレミスのワークステーションからのプライベート クラウドの vCenter にアクセスするための名前解決用に DNS を構成する](on-premises-dns-setup.md)」を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="access-vcenter-from-azure-portal"></a>Azure Portal から vCenter にアクセスする

プライベート クラウドの vCenter ポータルは Azure portal から起動できます。

1. **[すべてのサービス]** を選択します。

2. **CloudSimple Services** を検索します。

3. 接続するプライベート クラウドの CloudSimple サービスを選択します。

4. **[概要]** ページで、 **[View VMware Private Clouds]\(VMware プライベート クラウドの表示\)** をクリックします

    ![CloudSimple サービスの概要](media/cloudsimple-service-overview.png)

5. プライベート クラウドのリストからプライベート クラウドを選択し、 **[Launch vSphere Client]\(vSphere クライアントを起動\)** をクリックします。

    ![vSphere Client を起動する](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>CloudSimple ポータルから vCenter にアクセスする

プライベート クラウドの vCenter ポータルは CloudSimple ポータルから起動できます。

1. [CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

2. **[リソース]** から、アクセスするプライベート クラウドを選択し、 **[Launch vSphere Client]\(vSphere クライアントを起動\)** をクリックします。

    ![vSphere クライアントの起動 - リソース](media/cloudsimple-portal-resources-launch-vcenter.png)

3. また、プライベート クラウドの概要画面から vCenter ポータルを起動することもできます。

    ![vSphere クライアントの起動 - 概要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>次の手順

* [プライベート クラウドの VLAN/サブネットを作成して管理する](create-vlan-subnet.md)
* [VMware vCenter の CloudSimple プライベート クラウド アクセス許可モデル](learn-private-cloud-permissions.md)