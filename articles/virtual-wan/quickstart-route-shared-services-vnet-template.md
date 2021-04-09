---
title: 'クイックスタート: ARM テンプレートを使用して共有サービスにルーティングする'
titleSuffix: Azure Virtual WAN
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、すべての VNet とブランチがアクセスするワークロードがある共有サービス VNet にアクセスするためのルートを設定する方法を示します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: f325dd445e778bf03049d2c9e2e00fed7a427ccf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444000"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して共有サービス VNet にルーティングする

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、すべての VNet およびブランチ (VPN、ER、P2S) がアクセスするワークロードがある共有サービス VNet にアクセスするためのルートを設定する方法について説明します。 このような共有ワークロードの例には、ドメイン コントローラーやファイル共有などのサービスが含まれる仮想マシンや、[Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md)経由で内部公開される Azure サービスなどがあります。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* この構成には、公開キー証明書データが必要です。 この記事にはサンプル データが用意されています。 ただし、このサンプル データは、あくまで P2S ゲートウェイを作成するためのテンプレートの要件を満たすことを目的としたものです。 この構成が正しく機能するためには、テンプレートを実行してリソースがデプロイされた後、このフィールドを実際の証明書データで更新する必要があります。 [ユーザー VPN 証明書](certificates-point-to-site.md#cer)に関するページを参照してください。

## <a name="review-the-template"></a><a name="review"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/301-virtual-wan-with-route-tables)からのものです。 この記事のテンプレートは長いため、ここでは表示できません。 テンプレートを確認するには、[azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/301-virtual-wan-with-route-tables/azuredeploy.json) を参照してください。

このクイックスタートでは、ゲートウェイや VNet 接続をすべて含む、Azure Virtual WAN のマルチハブ デプロイを作成します。 入力パラメーターの数は、意図的に最小限に抑えています。 IP アドレス スキームは、テンプレート内の変数を編集することで変更できます。 このシナリオについては、[共有サービス VNet のシナリオ](scenario-shared-services-vnet.md)に関する記事で詳しく説明されています。

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="デプロイ アーキテクチャ" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

このテンプレートでは、次のリソースを使用して、完全に機能する Azure Virtual WAN 環境を作成します。

* 異なるリージョンに分かれて存在する 2 つのハブ。
* 4 つの Azure 仮想ネットワーク (VNet)。
* VWAN ハブごとに 2 つの VNet 接続。
* ハブごとに 1 つのポイント対サイト (P2S) VPN ゲートウェイ。
* ハブごとに 1 つのサイト対サイト (S2S) VPN ゲートウェイ。
* ハブごとに 1 つの ExpressRoute ゲートウェイ。
* ハブごとに RT_SHARED カスタム ルート テーブル。
* RT_SHARED ルート テーブルをグループ化するためのラベル LBL_RT_SHARED。

このテンプレートには、次の複数の Azure リソースが定義されています。

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> ハイブリッド接続に必要なユーザー側のリソースは、この ARM テンプレートでは作成されません。 テンプレートをデプロイした後に、P2S VPN クライアントと VPN ブランチ (ローカル サイト) を作成して構成し、ExpressRoute 回線を接続する必要があります。
>

その他のテンプレートについては、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a><a name="deploy"></a>テンプレートのデプロイ

このテンプレートを適切にデプロイするには、[Azure へのデプロイ] ボタンと Azure portal を使用する必要があります。次の理由から、他の方法は使用できません。

* P2S 構成を作成するためには、ルート証明書データをアップロードする必要があります。 PowerShell または CLI を使用する場合、データ フィールドが証明書データを受け付けません。
* このテンプレートは、Cloud Shell を使用すると、証明書データのアップロードが原因で適切に機能しません。
* また、ポータルであれば、IP アドレス範囲やその他の値に合わせてテンプレートとパラメーターを簡単に変更できます。

1. **[Azure へのデプロイ]** をクリックします。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)
1. テンプレートを表示するには、 **[テンプレートの編集]** をクリックします。 このページで、アドレス空間や特定のリソースの名前など、一部の値を調整できます。 **[保存]** をクリックして変更内容を保存するか、 **[破棄]** をクリックしてください。
1. テンプレート ページで値を入力します。 このテンプレートには、P2S パブリック証明書データが必要です。 この記事を練習に使用している場合は、サンプル データとして、次の .cer ファイルのデータを両方のハブにご利用ください。 テンプレートの実行とデプロイが完了した後、この P2S 構成を使用するためには、この情報を、実際のデプロイの公開キー[証明書データ](certificates-point-to-site.md#cer)に置き換える必要があります。

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. 値を入力し終えたら、 **[確認と作成]** を選択します。
1. **[確認と作成]** ページで、検証に問題がなければ、 **[作成]** を選択します。
1. デプロイが完了するまでに約 75 分かかります。 進行状況は、テンプレートの **[概要]** ページで確認できます。 ポータルを閉じても、デプロイは継続されます。

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="デプロイ完了の例":::

## <a name="validate-the-deployment"></a><a name="validate"></a>デプロイの検証

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のウィンドウから **[リソース グループ]** を選択します。
1. 前のセクションで作成したリソース グループを選択します **[概要]** ページに、次の例に示したような項目が表示されます。:::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="リソースの例" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. この仮想 WAN をクリックしてハブを表示します。 仮想 WAN ページでそれぞれのハブをクリックすると、接続情報など、そのハブに関する情報が表示されます。
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="ハブの例" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>ハイブリッド構成を完成させる

ハイブリッド ネットワークに必要なすべての設定がこのテンプレートで構成されるわけではありません。 次の構成と設定は、実際の要件に応じてご自身で行う必要があります。

* [VPN ブランチを構成する - ローカル サイト](virtual-wan-site-to-site-portal.md#site)
* [P2S VPN 構成を完成させる](virtual-wan-point-to-site-portal.md)
* [ExpressRoute 回線を接続する](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースは、不要になったら削除してください。 Virtual WAN リソースのいくつかは、依存関係に応じた特定の順序で削除する必要があります。 削除が完了するまでに 30 分程度かかる場合があります。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [P2S VPN 構成を完成させる](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [VPN ブランチを構成する - ローカル サイト](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [ExpressRoute 回線を接続する](virtual-wan-expressroute-portal.md)
