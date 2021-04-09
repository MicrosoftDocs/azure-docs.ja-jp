---
title: ExpressRoute を使用して Azure 仮想ネットワークを CloudSimple に接続する - Azure VMware Solution by CloudSimple
description: Azure 仮想ネットワークと CloudSimple 環境間の接続のピアリング情報を取得する方法について説明します。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3fedfbe55fd8ea3d2b4cc910df631e40bc74e210
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899067"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>ExpressRoute を使用して Azure 仮想ネットワークを CloudSimple に接続する

プライベート クラウド ネットワークは、Azure 仮想ネットワークと Azure リソースに拡張できます。 ExpressRoute 接続を使用すると、プライベート クラウドから Azure サブスクリプションで実行されているリソースにアクセスできます。

## <a name="request-authorization-key"></a>承認キーをリクエストする

プライベート クラウドと Azure 仮想ネットワーク間の ExpressRoute 接続には承認キーが必要です。 キーを取得するには、<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">サポート</a>にチケットを申請します。  リクエストには次の情報を使用します。

* 問題の種類: **技術**
* サブスクリプション: **CloudSimple サービスがデプロイされるサブスクリプションを選択する**
* サービス: **VMware Solution by CloudSimple**
* 問題の種類: **サービス要求**
* 問題のサブタイプ: **Azure VNET 接続の承認キー**
* 件名: **Azure VNET 接続の承認キーのリクエスト**

## <a name="get-peering-information-from-cloudsimple-portal"></a>CloudSimple ポータルからピアリング情報を取得する

接続を設定するには、Azure 仮想ネットワークと CloudSimple 環境の間に接続を確立する必要があります。  この手順の一部として、ピア回線の URI と承認キーを指定する必要があります。 [CloudSimple ポータル](access-cloudsimple-portal.md)から URI と承認キーを取得します。  サイド メニューの **[ネットワーク]** を選択し、**[Azure Network Connection]\(Azure のネットワーク接続\)** を選択します。 または、サイド メニューの **[アカウント]** を選択し、**[Azure network connection]\(Azure のネットワーク接続\)** を選択します。

"*コピー*" アイコンを使用して、各リージョンのピア サーキット URI と承認キーをコピーします。 接続する CloudSimple リージョンごとに、次のことを行います。

1. **[コピー]** をクリックして URI をコピーします。 それをファイルに貼り付けて、Azure portal に追加できるようにします。  
2. **[コピー]** をクリックして承認キーをコピーし、それをファイルに貼り付けます。

**[Available]\(使用可能\)** 状態のピア サーキット URI と承認キーをコピーします。  **[Used]\(使用済み\)** 状態は、仮想ネットワーク接続を作成する目的で既にキーが使用されていることを示します。

![[Virtual Network Connection]\(仮想ネットワーク接続\) ページ](media/virtual-network-connection.png)

Azure 仮想ネットワークを CloudSimple リンクに設定する方法の詳細については、「[ExpressRoute を使用して CloudSimple プライベート クラウド環境を Azure 仮想ネットワークに接続する](azure-expressroute-connection.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [プライベート クラウドへの Azure 仮想ネットワーク接続](azure-expressroute-connection.md)
* [Azure ExpressRoute を使用してオンプレミスのネットワークに接続する](on-premises-connection.md)
