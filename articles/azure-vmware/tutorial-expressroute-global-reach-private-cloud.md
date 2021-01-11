---
title: オンプレミス環境をプライベート クラウドにピアリングする
description: この Azure VMware Solution チュートリアルでは、Azure VMware Solution のプライベート クラウドに対する ExpressRoute Global Reach ピアリングを作成します。
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750454"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>チュートリアル:オンプレミス環境をプライベート クラウドにピアリングする

ExpressRoute Global Reach は、オンプレミス環境をプライベート クラウドに接続します。 プライベート クラウドの ExpressRoute 回線と、オンプレミス環境への既存の ExpressRoute 接続との間に ExpressRoute Global Reach 接続が確立されます。  Azure CLI と PowerShell を使用して ExpressRoute Global Reach を構成するためのインストラクションが用意されているほか、オンプレミス環境と Azure VMware Solution プライベート クラウドとの間で ExpressRoute Global Reach ピアリングを構成しやすくするために [CLI コマンド](../expressroute/expressroute-howto-set-global-reach-cli.md)を具体的な情報や例で補足しています。   

ExpressRoute Global Reach を使用して 2 つの ExpressRoute 回線間の接続を有効にする前に、[異なる Azure サブスクリプションで接続を有効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)方法に関するドキュメントを確認してください。  [Azure 対プライベート クラウドのネットワークを構成](tutorial-configure-networking.md)する際に使用する ExpressRoute 回線では、ExpressRoute ゲートウェイに対してピアリングを行うときや Global Reach を使用して他の ExpressRoute 回線とピアリングするときに承認キーを作成して使用する必要があります。 既に使用している ExpressRoute 回線からの承認キーとは別に、オンプレミスの ExpressRoute 回線とピアリングするための 2 つ目の承認キーを作成する必要があります。

> [!TIP]
> 以下の手順のコンテキストでは、オンプレミスの ExpressRoute 回線を "_回線 1_" とします。また、プライベート クラウドの ExpressRoute 回線は別のサブスクリプションに存在し、こちらは "_回線 2_" と呼ぶことにします。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ExpressRoute 回線と ExpressRoute Global Reach ピアリングを管理するための既存の手順を活用する
> * プライベート クラウドの ExpressRoute 回線である "_回線 2_" の承認キーを作成する
> * "_回線 1_" のサブスクリプションの Cloud Shell から Azure CLI を使用して、オンプレミス対プライベート クラウドの ExpressRoute Global Reach ピアリングを有効にする

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。
- ExpressRoute 回線が Azure 仮想ネットワーク (VNet) 内の ExpressRoute ゲートウェイとピアリングされているプライベート クラウド。ピアリング手順の観点から、これは "_回線 2_" になります。
- オンプレミス環境を Azure に接続するための、正常に機能している別の ExpressRoute 回線。ピアリング手順の観点から、これは "_回線 1_" になります。
- ExpressRoute Global Reach ピアリングのための重複していない /29 [ネットワーク アドレス ブロック](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)。

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Azure VMware Solution プライベート クラウドに ExpressRoute の認可キーを作成する

1. プライベート クラウドの **[概要]** の [管理] で、 **[接続] > [ExpressRoute] > [Request an authorization key]\(承認キーの要求\)** を選択します。

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="[接続] > [ExpressRoute] > [Request an authorization key]\(認可キーの要求\) を選択して新しい要求を開始する。":::

2. 承認キーの名前を入力し、 **[作成]** を選択します。 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="[作成] をクリックして新しい承認キーを作成する。":::

   作成された新しいキーが、プライベート クラウドの承認キーの一覧に表示されます。 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="プライベート クラウドのキー一覧に新しい承認キーが表示されていることを確認する。":::

3. 承認キーと ExpressRoute ID を /29 アドレス ブロックと共に書き留めます。 これらは、次の手順でピアリングを行う際に使用します。 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>承認キーを使用してプライベート クラウドをオンプレミスにピアリングする

プライベート クラウドの ExpressRoute 回線用に承認キーを作成したら、プライベート クラウドの ExpressRoute 回線をオンプレミスの ExpressRoute 回線とピアリングすることができます。  このピアリングは、オンプレミスの ExpressRoute 回線側から、Cloud Shell で Azure CLI を使用し、プライベート クラウドの ExpressRoute 回線のリソース ID と承認キー (前の手順で作成したもの) を使用して行います。

> [!TIP]  
> Azure CLI コマンド出力を簡潔にするために、以下の手順では [`–query` 引数を使用して JMESPath クエリを実行して、必要な結果のみを表示](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)しています。


1. オンプレミスの ExpressRoute 回線と同じサブスクリプションを使用して Azure portal にサインインし、Cloud Shell を開きます。 シェルは Bash のままにしてください。
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Azure portal にサインインし、Cloud Shell を開く。":::
 
2. コマンド ラインで、Azure CLI コマンドを入力してピアリングを作成します。その際、実際の具体的な情報とリソース ID、承認キー、/29 CIDR 形式のネットワーク ブロックを使用します。 

   以下に、使用するコマンドの例と、ピアリングの成功を示す出力を示します。 このコマンド例は、[「異なる Azure サブスクリプションで ExpressRoute 回線間の接続を有効にする」の手順 3](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) で使用したコマンドに基づいています。

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Cloud Shell で Azure CLI コマンドを使用して ExpressRoute Global Reach ピアリングを作成する。":::
 
   これで、オンプレミス環境から ExpressRoute Global Reach ピアリングを介してプライベート クラウドに接続することができます。

> [!TIP]
> 作成したピアリングは、「[オンプレミス ネットワーク間の接続を無効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks)」の手順に従って削除することができます。


## <a name="next-steps"></a>次のステップ

Hybrid Cloud Extension (HCX) を使用して VM ワークロードをプライベート クラウドに移行する予定がある場合は、「[Azure VMware Solution 用の HCX をインストールする](hybrid-cloud-extension-installation.md)」の手順に従います。


<!-- LINKS - external-->

<!-- LINKS - internal -->
