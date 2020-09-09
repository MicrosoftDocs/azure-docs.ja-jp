---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 517acc5137d70c722d8defade1e218a3b2e78f86
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052453"
---
VNet は、Resource Manager デプロイ モデルと Azure portal を使用し、以下の手順に従って作成できます。 仮想ネットワークの詳細については、[Virtual Network の概要](../articles/virtual-network/virtual-networks-overview.md)に関するページを参照してください。

>[!NOTE]
>クロスプレミス アーキテクチャの一部として仮想ネットワークを使用する場合は、必ずオンプレミスのネットワーク管理者と調整を行って、この仮想ネットワーク専用に使用できる IP アドレスの範囲を見つけます。 VPN 接続の両側に重複するアドレス範囲が存在する場合、予期しない方法でトラフィックがルーティングされます。 また、この仮想ネットワークを別の仮想ネットワークに接続する場合、アドレス空間を別の仮想ネットワークと重複させることはできません。 この点を踏まえてネットワーク構成を計画してください。
>
>

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Search resources, service, and docs (G+/)]\(リソース、サービス、ドキュメントを検索する (G+/)\)** に「*virtual network*」と入力します。

   ![仮想ネットワークのリソース ページを探す](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "仮想ネットワークのリソース ページを探す")
1. **[Marketplace]** 結果から **[Virtual Network]** を選択します。

   ![仮想ネットワークを選択する](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "仮想ネットワークのリソース ページを探す")
1. **[仮想ネットワーク]** ページで、 **[作成]** を選択します。

   ![仮想ネットワーク ページ](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "[作成] を選択します")
1. **[作成]** を選択すると、 **[仮想ネットワークの作成]** ページが開きます。
1. **[基本]** タブで、 **[プロジェクトの詳細]** および **[インスタンスの詳細]** VNet 設定を構成します。

   ![[基本] タブ](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "[基本] タブ") フィールドへの入力時、各フィールドに入力した文字の有効性が確認されると、緑色のチェック マークが表示されます。 いくつかの値は自動入力されます。実際の値に置き換えてください。

   - **サブスクリプション**:一覧表示されているサブスクリプションが正しいことを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
   - **[リソース グループ]** :既存のリソース グループを選択するか、 **[新規作成]** をクリックして新しく作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/management/overview.md#resource-groups)」を参照してください。
   - **Name**:仮想ネットワークの名前を入力します。
   - **[リージョン]** :VNet の場所を選択します。 この場所の設定によって、この VNet にデプロイしたリソースの配置先が決まります。

1. **[IP アドレス]** タブで、値を構成します。 次の例に示す値は、デモンストレーションを目的としています。 必要な設定に応じて、これらの値を調整してください。

   ![[IP アドレス] タブ](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "[IP アドレス] タブ")  
   - **[IPv4 アドレス空間]** : 既定では、アドレス空間が自動的に作成されます。 アドレス空間をクリックして、独自の値が反映されるように調整できます。 また、新しいアドレス空間を追加することもできます。
   - **サブネット**:既定のアドレス空間を使用すると、既定のサブネットが自動的に作成されます。 アドレス空間を変更する場合は、サブネットを追加する必要があります。 **[+ サブネットの追加]** を選択して、 **[サブネットの追加]** ウィンドウを開きます。 次の設定を構成し、 **[追加]** を選択して値を追加します。
      - **[サブネット名]** : この例では、サブネットに "FrontEnd" という名前が付いています。
      - **[サブネットのアドレス範囲]** : このサブネットのアドレス範囲です。

1. 現時点では、 **[セキュリティ]** タブは既定値のままにします。

   - **DDos 防御**:Basic
   - **ファイアウォール**:無効
1. **[確認と作成]** を選択して、仮想ネットワークの設定を検証します。
1. 設定が検証されたら、 **[作成]** を選択します。
