---
title: チュートリアル - オンプレミス環境をプライベート クラウドにピアリングする
description: Azure VMware Solution のプライベート クラウドに対する ExpressRoute Global Reach ピアリングを作成する方法について説明します。
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: e7b1e349f67fe63f63183c0ff6d1522498c65f8c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918723"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>チュートリアル:オンプレミス環境をプライベート クラウドにピアリングする

ExpressRoute Global Reach では、オンプレミス環境を Azure VMware Solution のプライベート クラウドに接続します。 プライベート クラウドの ExpressRoute 回線と、オンプレミス環境への既存の ExpressRoute 接続との間に、ExpressRoute Global Reach 接続が確立されます。 

[Azure 対プライベート クラウドのネットワークを構成](tutorial-configure-networking.md)する際に使用する ExpressRoute 回線では、承認キーを作成して使用する必要があります。  ExpressRoute 回線からの承認キーを既に 1 つ使用していたことを前提に、このチュートリアルでは、オンプレミスの ExpressRoute 回線とピアリングするための 2 つ目の承認キーを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * プライベート クラウドの ExpressRoute 回線である "_回線 2_" のために 2 つ目の承認キーを作成する
> * "_回線 1_" のサブスクリプションで、[Azure portal](#azure-portal-method) を使用するか、[Cloud Shell で Azure CLI を使用する](#azure-cli-in-a-cloud-shell-method)かいずれかの方法で、オンプレミスからプライベート クラウドへの ExpressRoute Global Reach ピアリングを有効にする


## <a name="before-you-begin"></a>開始する前に

ExpressRoute Global Reach を使用して 2 つの ExpressRoute 回線間の接続を有効にする前に、[異なる Azure サブスクリプションで接続を有効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)方法に関するドキュメントを確認してください。  


## <a name="prerequisites"></a>前提条件

- ExpressRoute 回線が Azure 仮想ネットワーク (VNet) 内の ExpressRoute ゲートウェイとピアリングされている Azure VMware Solution プライベート クラウドとの間に確立された接続。これがピアリング手順から "_回線 2_" になります。  
- オンプレミス環境を Azure に接続するために使用される、正常に機能している別個の ExpressRoute 回線。ピアリング手順の観点からこれが "_回線 1_" になります。
- ExpressRoute Global Reach ピアリングのための重複していない /29 [ネットワーク アドレス ブロック](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)。
- ExpressRoute プロバイダーのサービスを含め、すべてのゲートウェイで 4 バイトの自律システム番号 (ASN) がサポートされていることを確認します。 Azure VMware Solution では、4 バイトのパブリック ASN を使用してルートをアドバタイズします。

> [!TIP]
> これらの前提条件の環境では、オンプレミスの ExpressRoute 回線が "_回線 1_" で、プライベート クラウドの ExpressRoute 回線は、別のサブスクリプション内に存在しており、"_回線 2_" というラベルが付けられています。 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>プライベート クラウドに ExpressRoute の承認キーを作成する

1. プライベート クラウドの **[概要]** の [管理] で、 **[接続] > [ExpressRoute] > [Request an authorization key]\(承認キーの要求\)** を選択します。

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="[接続] > [ExpressRoute] > [Request an authorization key]\(認可キーの要求\) を選択して新しい要求を開始する。":::

2. 承認キーの名前を入力し、 **[作成]** を選択します。 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="[作成] を選択して新しい承認キーを作成する。":::

   作成された新しいキーが、プライベート クラウドの承認キーの一覧に表示されます。 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="プライベート クラウドのキー一覧に新しい承認キーが表示されていることを確認する。":::

3. 承認キーと ExpressRoute ID を /29 アドレス ブロックと共に書き留めます。 これらは、次の手順でピアリングを行う際に使用します。 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>承認キーを使用してプライベート クラウドをオンプレミスにピアリングする

プライベート クラウドの ExpressRoute 回線用に承認キーを作成したので、プライベート クラウドの ExpressRoute 回線をオンプレミスの ExpressRoute 回線とピアリングすることができます。  ピアリングは、[Azure portal](#azure-portal-method) で、または [Cloud Shell で Azure CLI](#azure-cli-in-a-cloud-shell-method) を使用して、オンプレミスの ExpressRoute 回線の観点から行われます。 どちらの方法でも、プライベート クラウドの ExpressRoute 回線のリソース ID と承認キーを使用して、ピアリングを完了します。

### <a name="azure-portal-method"></a>Azure portal を使用する方法

1. オンプレミスの ExpressRoute 回線と同じサブスクリプションを使用して [Azure portal](https://portal.azure.com) にサインインします。

1. プライベート クラウドの **[概要]** の [管理] で、 **[接続] > [ExpressRoute Global Reach] > [追加]** を選択します。

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="メニューから、[接続]、[ExpressRoute Global Reach] タブと選択してから、[追加] を選択する。":::

1. オンプレミスのクラウド接続は、次のいずれかの方法で作成できます。

   - 一覧から ExpressRoute 回線を選択します。
   - 回線 ID がある場合は、コピーして貼り付けます。

1. **[接続]** を選択します。 新しい接続が、[オンプレミスのクラウド接続] の一覧に表示されます。  

>[!TIP]
>**[その他]** を選択すると、一覧から接続を削除または切断できます。  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="オンプレミス接続を切断または削除する":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Cloud Shell で Azure CLI を使用する方法

オンプレミス環境と Azure VMware Solution プライベート クラウドとの間で ExpressRoute Global Reach ピアリングを構成する助けとなるように、具体的な詳細や例によって [CLI コマンド](../expressroute/expressroute-howto-set-global-reach-cli.md)が強化されてきています。  

> [!TIP]  
> Azure CLI コマンド出力を簡潔にするために、以下の手順では [`–query` 引数を使用して JMESPath クエリを実行して、必要な結果のみを表示](/cli/azure/query-azure-cli)しています。


1. オンプレミスの ExpressRoute 回線と同じサブスクリプションを使用して Azure portal にサインインし、Cloud Shell を開きます。 シェルは Bash のままにしてください。
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Azure portal にサインインし、Cloud Shell を開く。":::
 
2. Azure CLI コマンドを入力して、ピアリングを作成します。 実際の具体的な情報とリソース ID、承認キー、および /29 CIDR 形式のネットワーク ブロックを使用します。 

   画像は、使用するコマンドの例と、ピアリングの成功を示す出力を示しています。 このコマンド例は、[「異なる Azure サブスクリプションで ExpressRoute 回線間の接続を有効にする」の手順 3](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) で使用したコマンドに基づいています。

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Cloud Shell で Azure CLI コマンドを使用して ExpressRoute Global Reach ピアリングを作成する。":::
 
   オンプレミス環境から ExpressRoute Global Reach ピアリングを介してプライベート クラウドに接続できます。

> [!TIP]
> 作成したピアリングは、「[オンプレミス ネットワーク間の接続を無効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks)」の手順に従って削除することができます。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、プライベート クラウドの ExpressRoute 回線のために 2 つ目の承認キーを作成する方法について説明しました。 また、オンプレミス対プライベート クラウドの ExpressRoute Global Reach ピアリングを有効にする方法についても説明しました。 

次のチュートリアルに進み、Azure VMware Solution プライベート クラウドのために VMware HCX ソリューションをデプロイして構成する方法を学習します。

> [!div class="nextstepaction"]
> [VMware HCX をデプロイして構成する](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->