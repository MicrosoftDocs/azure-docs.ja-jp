---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328440"
---
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
