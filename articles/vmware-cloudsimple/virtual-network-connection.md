---
title: ExpressRoute を使用して Azure 仮想ネットワークを CloudSimple に接続する
description: Azure 仮想ネットワークと CloudSimple 環境間の接続のピアリング情報を取得する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00d49d763dedc5d86557dadd10f5d727e7893dbe
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563063"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>ExpressRoute を使用して Azure 仮想ネットワークを CloudSimple に接続する

プライベート クラウド ネットワークは、Azure 仮想ネットワークと Azure リソースに拡張できます。 ExpressRoute 接続を使用すると、プライベート クラウドから Azure サブスクリプションで実行されているリソースにアクセスできます。

## <a name="request-authorization-key"></a>承認キーをリクエストする

プライベート クラウドと Azure 仮想ネットワーク間の ExpressRoute 接続には承認キーが必要です。 キーを取得するには、<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">サポート</a>にチケットを申請します。  リクエストには次の情報を使用します。

* [問題の種類]\:**テクニカル**
* サブスクリプション:**CloudSimple サービスがデプロイされるサブスクリプションを選択する**
* サービス:**VMware Solution by CloudSimple**
* 問題の種類:**サービス リクエスト**
* 問題のサブタイプ:**Azure VNET 接続の承認キー**
* 件名:**Azure VNET 接続の承認キーのリクエスト**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Azure 仮想ネットワークから CloudSimple 接続へのピアリング情報を取得する

接続を設定するには、Azure 仮想ネットワークと CloudSimple 環境の間にリンクを確立する必要があります。  この手順の一部として、ピア回線の URI と承認キーを指定する必要があります。 [CloudSimple ポータル](access-cloudsimple-portal.md)から URI と承認キーを取得します。  サイド メニューの **[ネットワーク]** を選択し、 **[Azure Network Connection]\(Azure のネットワーク接続\)** を選択します。 または、サイド メニューの **[アカウント]** を選択し、 **[Azure network connection]\(Azure のネットワーク接続\)** を選択します。

各リージョンのピア サーキット URI と承認キーのコピー アイコンに注目してください。 接続するプライベート クラウドごとに、次のことを行います。

* **[コピー]** をクリックして URI をコピーします。 それをファイルに貼り付けて、Azure portal に追加できるようにします。  
* **[コピー]** をクリックして承認キーをコピーし、それをファイルに貼り付けます。

![[Virtual Network Connection]\(仮想ネットワーク接続\) ページ](media/network-virt-conn-page.png)

Azure 仮想ネットワークを CloudSimple リンクに設定する方法の詳細については、「[ExpressRoute を使用して CloudSimple プライベート クラウド環境を Azure 仮想ネットワークに接続する](azure-expressroute-connection.md)」を参照してください。
