---
title: 'Azure ExpressRoute: ExpressRoute Direct を構成する - portal'
description: このページは、portal を使用して ExpressRoute Direct を構成するときに役立ちます。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: b133f1cce4af07d8d5e50e04670741fcf7c936a4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097076"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Azure portal を使用して ExpressRoute Direct を作成する

この記事では、Azure portal を使用して ExpressRoute Direct を作成する方法を示します。
ExpressRoute Direct を使用すると、世界中に戦略的に分散されたピアリングの場所で、Microsoft のグローバル ネットワークに直接接続できます。 詳細については、[ExpressRoute Direct](expressroute-erdirect-about.md) に関するページを参照してください。

## <a name="before-you-begin"></a><a name="before"></a>開始する前に

ExpressRoute Direct を利用する前に、まず、サブスクリプションを登録する必要があります。 登録するには、Azure PowerShell から次の操作を行ってください。
1.  Azure にサインインして、登録するサブスクリプションを選択します。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 次のコマンドを使用して、サブスクリプションをパブリック プレビューに登録します。
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

登録したら、**Microsoft.Network** リソース プロバイダーがサブスクリプションに登録されていることを確認します。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。

1. 「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」の説明に従って、サブスクリプションの設定にアクセスします。
1. サブスクリプションで、**リソース プロバイダー** に関して、**Microsoft.Network** プロバイダーに **登録** 状態が表示されていることを確認します。 登録されているプロバイダーの一覧に、Microsoft.Network リソース プロバイダーが存在しない場合は、それを追加します。

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>ExpressRoute Direct を作成する

1. [Azure portal](https://portal.azure.com) のメニューまたは **[ホーム]** ページから、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、**_[Marketplace の検索]_ *フィールドに「* ExpressRoute Direct**」と入力し、**Enter** キーを押して検索結果を取得します。

1. 結果から **ExpressRoute Direct** を選択します。

1. **ExpressRoute Direct** のページで、 **[作成]** を選択して、 **[Create ExpressRoute Direct]\(ExpressRoute Direct の作成\)** ページを開きます。

1. 最初に、 **[基本]** ページのフィールドを設定します。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="[基本] ページ":::

    * **サブスクリプション**:新しい ExpressRoute Direct の作成に使用する Azure サブスクリプション。 ExpressRoute Direct リソースおよび ExpressRoute 回線は、同じサブスクリプション内にある必要があります。
    * **リソース グループ**:新しい ExpressRoute Direct リソースが作成される Azure リソース グループ。 既存のリソース グループがない場合は、新しいものを作成できます。
    * **[リージョン]** :リソースが作成される Azure パブリック リージョンです。
    * **Name**:新しい ExpressRoute Direct リソースの名前。

1. 次に、 **[構成]** ページのフィールドを設定します。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="[構成] タブが選択されている [ExpressRoute Direct の作成] ページを示すスクリーンショット。":::

    * **[ピアリングの場所]** : ExpressRoute Direct リソースに接続するピアリングの場所。 ピアリングの場所の詳細については、[ExpressRoute の場所](expressroute-locations-providers.md)に関する記事を参照してください。
   * "**帯域幅**": 予約するポート ペアの帯域幅。 ExpressRoute Direct では、10 Gb と 100 Gb の両方の帯域幅オプションがサポートされています。 指定したピアリングの場所で目的の帯域幅が使用できない場合は、[Azure portal でサポート リクエストを開きます](https://aka.ms/azsupt)。
   * **[Encapsulation]\(カブセル化\)** : ExpressRoute Direct では、QinQ と Dot1Q 両方のカプセル化がサポートされます。
     * QinQ を選択した場合、各 ExpressRoute 回線に S-Tag が動的に割り当てられ、ExpressRoute Direct リソース全体で一意になります。
     *  回線上の各 C-Tag はその回線で一意である必要がありますが、ExpressRoute Direct 全体ではありません。
     * Dot1Q カプセル化を選択した場合、ExpressRoute Direct リソース全体で C-Tag (VLAN) が一意になるように管理する必要があります。
     >[!IMPORTANT]
     >ExpressRoute Direct ではカプセル化の種類を 1 つしか選択できません。 ExpressRoute Direct を作成した後でカプセル化を変更することはできません。
     >

1. リソース タグを指定し、 **[確認と作成]** を選択して、ExpressRoute Direct リソースの設定を検証します。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="[確認と作成] タブが選択されている [ExpressRoute の作成] ページを示すスクリーンショット。":::

1. **［作成］** を選択します デプロイが進行中であることを知らせるメッセージが表示されます。 リソースが作成されたときに、状態がこのページに表示されます。 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>承認状 (LOA) を生成する

現時点では、ポータルから承認状を生成することはできません。 **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** を使用して、承認状を取得します。

## <a name="change-admin-state-of-links"></a><a name="state"></a>リンクの管理状態を変更する

このプロセスは、レイヤー 1 のテストを実施して、各相互接続がプライマリとセカンダリの各ルーターに適切に接続されていることを確認するために使用する必要があります。

1. ExpressRoute Direct リソースの **[概要]** ページで、 **[Links]\(リンク\)** セクションの **[link1]\(リンク 1\)** を選択します。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="リンク 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. **[Admin State]\(管理状態\)** 設定を **[有効]** に切り替えて、 **[保存]** を選択します。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="管理状態":::

    >[!IMPORTANT]
    >いずれかのリンクで管理状態を有効にすると、課金が開始されます。
    >

1. **[link2]\(リンク 2\)** についても同じ手順を繰り返します。

## <a name="create-a-circuit"></a><a name="circuit"></a>回線を作成する

既定では、ExpressRoute Direct リソースがあるサブスクリプション内に 10 個の回線を作成できます。 この数はサポートによって増やすことができます。 プロビジョニング済みの帯域幅と使用済みの帯域幅の両方を追跡してください。 プロビジョニングされる帯域幅は、ExpressRoute Direct リソース上のすべての回線の帯域幅の合計です。 使用される帯域幅は、基礎となる物理インターフェイスの実際の使用量です。

* ExpressRoute Direct には、前述したシナリオをサポートするために利用できるだけの追加の回線帯域幅があります。 次のとおりです。40 Gbps と 100 Gbps。

* SkuTier は Local、Standard、または Premium にできます。

* SkuFamily は MeteredData だけにする必要があります。 ExpressRoute Direct では、Unlimited はサポートされていません。

ExpressRoute Direct のワークフローから ExpressRoute 回線を作成するには、次の手順のようにします。 代わりに、通常の回線ワークフローを使用して回線を作成することもできますが、この構成では、通常の回線ワークフローの手順を使用する利点はありません。 [ExpressRoute 回線の作成と変更](expressroute-howto-circuit-portal-resource-manager.md)に関する記事を参照してください。

1. ExpressRoute Direct の **[設定]** セクションで、 **[Circuits]\(回線\)** を選択してから、 **[+ 追加]** を選択します。 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="[Circuits]\(回線\) が選択されて [追加] が強調表示された ExpressRoute 設定を示すスクリーンショット。" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. **[構成]** ページで設定を構成します。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="[構成] ページ - ExpressRoute Direct":::

1. リソース タグを指定します。リソースを作成する前に値を検証するには、 **[確認と作成]** を選択します。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="[確認と作成] - ExpressRoute Direct":::

1. **［作成］** を選択します デプロイが進行中であることを知らせるメッセージが表示されます。 リソースが作成されたときに、状態がこのページに表示されます。 

## <a name="next-steps"></a>次のステップ

ExpressRoute Direct について詳しくは、[概要](expressroute-erdirect-about.md)のページをご覧ください。
