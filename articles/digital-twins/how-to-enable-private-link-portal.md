---
title: Private Link を使用してプライベート アクセスを有効にする (プレビュー) - portal
titleSuffix: Azure Digital Twins
description: Private Link を使用した Azure Digital Twins ソリューションのプライベート アクセスを、Azure portal を使用して有効にする方法について説明します。
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100473463"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Private Link を使用してプライベート アクセスを有効にする (プレビュー):Azure portal

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

この記事では、[Azure Digital Twins インスタンス用のプライベート エンドポイントを使用して Private Link を有効にする](concepts-security.md#private-network-access-with-azure-private-link-preview)さまざまな方法について説明します (現在プレビュー段階)。 Azure Digital Twins インスタンス用のプライベート エンドポイントを構成することで、Azure Digital Twins インスタンスをセキュリティで保護して公開されないようにすることに加え、[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) からのデータ流出を回避できます。

この記事では、[**Azure portal**](https://portal.azure.com) を使用したプロセスについて説明します。

この記事では、次の手順を説明します。 
1. Private Link を有効にし、Azure Digital Twins インスタンス用のプライベート エンドポイントを構成します。
1. パブリック ネットワーク アクセス フラグを無効または有効にして、Private Link 接続のみに API アクセスを制限します。

## <a name="prerequisites"></a>前提条件

プライベート エンドポイントをセットアップする前に、エンドポイントをデプロイできる [**Azure Virtual Network (VNet)**](../virtual-network/virtual-networks-overview.md) が必要です。 VNet がまだない場合は、Azure Virtual Network のいずれかの[クイックスタート](../virtual-network/quick-create-portal.md)に従って、これを設定できます。

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンス用のプライベート エンドポイントを追加する 

[Azure portal](https://portal.azure.com) を使用する場合、Azure Digital Twins インスタンスの初期セットアップの一部としてインスタンス用のプライベート エンドポイントを備えた Private Link を有効にすること、または既に存在するインスタンスで後で Private Link を有効にすることを選択できます。 

どちらの作成方法でも、インスタンスに提供される構成オプションと結果は同じです。 このセクションでは、それぞれの方法について説明します。

>[!TIP]
> また、Azure Digital Twins インスタンスを使用するのではなく、Private Link サービスを使用して Private Link エンドポイントを設定することもできます。 その場合も、構成オプションと最終的な結果は同じです。
>
> Private Link リソースの設定の詳細については、[Azure portal](../private-link/create-private-endpoint-portal.md)、[Azure CLI](../private-link/create-private-endpoint-cli.md)、[ARM](../private-link/create-private-endpoint-template.md)、または [PowerShell](../private-link/create-private-endpoint-powershell.md) の Private Link に関するドキュメントを参照してください。

### <a name="add-a-private-endpoint-during-instance-creation"></a>インスタンスの作成時にプライベート エンドポイントを追加する

このセクションでは、現在作成中の Azure Digital Twins インスタンスでプライベート エンドポイントを備えた Private Link を有効にします。 このセクションでは、作成プロセスのネットワーク ステップを中心に説明します。新しい Azure Digital Twins インスタンスの作成に関する完全なチュートリアルについては、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-portal.md)に関するページを参照してください。

Private Link のオプションは、インスタンスのセットアップの **[ネットワーク]** タブにあります。

このタブでは、 **[接続方法]** の **[プライベート エンドポイント]** オプションを選択することにより、プライベート エンドポイントを有効にできます。

これによって追加される **[プライベート エンドポイント接続]** というセクションで、プライベート エンドポイントの詳細を構成できます。 **[+ 追加]** ボタンをクリックして続けます。

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Azure Digital Twins の [リソースの作成] ダイアログの [ネットワーク] タブが表示されている Azure portal のスクリーンショット。タブ名、[接続方法] の [プライベート エンドポイント] オプション、新しいプライベート エンドポイント接続を作成するための [+ 追加] ボタンが強調して示されています。" lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

これにより、新しいプライベート エンドポイントの詳細を入力するページが開きます。

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="[プライベート エンドポイントの作成] ページが表示されている Azure portal のスクリーンショット。以下で説明するフィールドが含まれます。":::

1. **[サブスクリプション]** と **[リソース グループ]** の選択を入力します。 **[場所]** を、使用する VNet と同じ場所に設定します。 エンドポイントの **[名前]** を選択し、 **[Target sub-resources]\(ターゲット サブリソース\)** で *[API]* を選択します。

1. 次に、エンドポイントのデプロイに使用する **[仮想ネットワーク]** と **[サブネット]** を選択します。

1. 最後に、 **[プライベート DNS ゾーンと統合する]** かどうかを選択します。 既定の **[はい]** をそのまま使用できます。または、このオプションの詳細については、ポータルのリンクに従って、[プライベート DNS 統合の詳細を確認](../private-link/private-endpoint-overview.md#dns-configuration)してください。

構成オプションを設定した後、 **[OK]** をクリックして完了します。

これにより、Azure Digital Twins インスタンスのセットアップの **[ネットワーク]** タブに戻ります。そこの [プライベート エンドポイント接続] の下に、新しいエンドポイントが表示されているはずです。

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Azure Digital Twins の [リソースの作成] ダイアログの [ネットワーク] タブが表示されている Azure portal のスクリーンショット。新しいプライベート エンドポイント接続とナビゲーション ボタンが強調して示されています ([確認および作成]、[前へ]、[次: 詳細])。" lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

その後、下部にあるナビゲーション ボタンを使用して、インスタンスの残りのセットアップを続けることができます。

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>既存のインスタンスにプライベート エンドポイントを追加する

このセクションでは、既に存在する Azure Digital Twins インスタンスでプライベート エンドポイントを備えた Private Link を有効にします。

1. まず、ブラウザーで [Azure portal](https://portal.azure.com) に移動します。 ポータルの検索バーで名前を検索して、Azure Digital Twins のインスタンスを表示します。

1. 左側のメニューで、 **[Networking (preview)]\(ネットワーク (プレビュー)\)** を選択します。

1. **[プライベート エンドポイント接続]** タブに切り替えます。

1. **[+ プライベート エンドポイント]** を選択して、 **[プライベート エンドポイントの作成]** の設定を開きます。

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Azure Digital Twins インスタンスの [Networking (preview)]\(ネットワーク (プレビュー)\) ページが表示されている Azure portal のスクリーンショット。[プライベート エンドポイント接続] タブが強調表示され、[+ プライベート エンドポイント] ボタンも強調表示されています。" lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1.  **[基本]**  タブで、プロジェクトの **[サブスクリプション]** と **[リソース グループ]** 、およびエンドポイントの **[名前]** と **[リージョン]** を入力または選択します。 リージョンは、使用している VNet のリージョンと同じにする必要があります。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="[プライベート エンドポイントの作成] ダイアログの最初の ([基本]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。":::

    終わったら、 **[次: リソース >]** ボタンを選択して、次のタブに進みます。

1. **[リソース]** タブで、次の情報を入力または選択します。 
    * **[接続方法]** : **[マイ ディレクトリ内の Azure リソースに接続します]** を選択して、お使いの Azure Digital Twins インスタンスを検索します。
    * **サブスクリプション**:お使いのサブスクリプションを入力します。
    * **[リソースの種類]** :**Microsoft.DigitalTwins/digitalTwinsInstances** を選択します
    * **リソース**:お使いの Azure Digital Twins インスタンスの名前を選択します。
    * **[Target sub-resources]\(ターゲット サブリソース\)** : **[API]** を選択します。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="[プライベート エンドポイントの作成] ダイアログの 2 番目の ([リソース]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。":::

    終わったら、 **[次: 構成 >]** ボタンを選択して、次のタブに進みます。    

1.  **[構成]** タブで、次の情報を入力または選択します。
    * **仮想ネットワーク**:仮想ネットワークを選択します。
    * **サブネット**:仮想ネットワークからサブネットを選択します。
    * **プライベート DNS ゾーンと統合する**: **[プライベート DNS ゾーンと統合する]** かどうかを選択します。 既定の **[はい]** をそのまま使用できます。または、このオプションの詳細については、ポータルのリンクに従って、[プライベート DNS 統合の詳細を確認](../private-link/private-endpoint-overview.md#dns-configuration)してください。
    **[はい]** を選択した場合は、既定の構成情報をそのまま使用できます。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="[プライベート エンドポイントの作成] ダイアログの 3 番目の ([構成]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。":::

    終わったら、 **[確認および作成]** ボタンを選択して、セットアップを完了します。 

1. **[確認および作成]** タブで選択内容を確認し、 **[作成]** ボタンを選択します。 

エンドポイントのデプロイが完了すると、Azure Digital Twins インスタンス用のプライベート エンドポイント接続に表示されます。

>[!TIP]
> エンドポイントは、Azure portal の Private Link センターから表示することもできます。

## <a name="disable--enable-public-network-access-flags"></a>パブリック ネットワーク アクセス フラグを無効または有効にする

Azure Digital Twins インスタンスの構成によって、すべてのパブリック接続を拒否し、プライベート エンドポイント経由の接続のみを許可するようにして、ネットワークのセキュリティを強化できます。 この操作は、**パブリック ネットワーク アクセス フラグ** で行います。 

このポリシーを使用すると、API アクセスを Private Link 接続のみに制限することができます。 パブリック ネットワーク アクセス フラグが "*無効*" に設定されていると、パブリック クラウドから Azure Digital Twins インスタンスのデータ プレーンへのすべての REST API 呼び出しで、`403, Unauthorized` が返されます。 または、ポリシーが "*無効*" に設定されていて、要求がプライベート エンドポイントを通して行われた場合は、API 呼び出しは成功します。

この記事では、[Azure portal](https://portal.azure.com) を使用してネットワーク フラグの値を更新する方法について説明します。 Azure CLI または ARMClient コマンド ツールを使用してこれを行う方法については、この記事の [CLI バージョン](how-to-enable-private-link-cli.md)を参照してください。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

[Azure portal](https://portal.azure.com) でパブリック ネットワーク アクセスを無効または有効にするには、ポータルを開き、お使いの Azure Digital Twins インスタンスに移動します。

1. 左側のメニューで、 **[Networking (preview)]\(ネットワーク (プレビュー)\)** を選択します。

1. **[パブリック アクセス]** タブの **[Allow public network access to]\(パブリック ネットワーク アクセスを許可する\)** を、 **[無効]** または **[すべてのネットワーク]** に設定します。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Azure Digital Twins インスタンスの [Networking (preview)]\(ネットワーク (プレビュー)\) ページが表示されている Azure portal のスクリーンショット。[パブリック アクセス] タブが強調表示され、パブリック ネットワーク アクセスを許可するかどうかを選択するオプションも強調表示されています。" lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure 用 Private Link の詳細を確認します。 
* [*Azure Private Link サービスとは*](../private-link/private-link-service-overview.md)