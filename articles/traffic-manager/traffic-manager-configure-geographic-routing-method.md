---
title: チュートリアル:Azure Traffic Manager を使用した地理的トラフィック ルーティングの構成
description: このチュートリアルでは、Azure Traffic Manager を使用して地理的トラフィック ルーティング方法を構成する方法について説明します
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96188668"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>チュートリアル:Traffic Manager を使用した地理的トラフィック ルーティング方法の構成

地理的トラフィック ルーティング方法を使用すると、要求の送信元の地理的な場所に基づいて、特定のエンドポイントにトラフィックを転送することができます。 このチュートリアルでは、このルーティング方法を使用して Traffic Manager プロファイルを作成し、特定の地域からのトラフィックを受信するようにエンドポイントを構成する方法を説明します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - 地理的ルーティングを使用して Traffic Manager プロファイルを作成する。
> - 入れ子になったエンドポイントを構成する。
> - Traffic Manager プロファイルを使用する。
> - Traffic Manager プロファイルを削除する。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側で **[+ リソースの作成]** を選択します。 「**Traffic Manager プロファイル**」を検索し、 **[作成]** を選択します。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Traffic Manager プロファイルの作成":::

1. *[Traffic Manager プロファイルの作成]* ページで、次の設定を定義します。

    | 設定         | 値                                              |
    | ---             | ---                                                |
    | 名前            | プロファイルの名前を指定します。 この名前は、trafficmanager.net zone 内で一意である必要があります。 Traffic Manager プロファイルにアクセスするには、ご自分の DNS 名 `<profilename>.trafficmanager.net` を使用します。 |    
    | ルーティング方法  | **[地域]** を選択します。 |
    | サブスクリプション    | サブスクリプションを選択します。 |
    | Resource group   | 既存のリソース グループを使用するか、新しいリソース グループを作成して、その下にこのプロファイルを配置します。 新しいリソース グループを作成する場合は、 *[リソース グループの場所]* ドロップダウンを使用して、リソース グループの場所を指定します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。 |

1. **[作成]** を選択し、Traffic Manager プロファイルをデプロイします。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="[Traffic Manager プロファイルの作成] ページ":::

## <a name="add-endpoints"></a>エンドポイントの追加

1. 一覧から Traffic Manager プロファイルを選択します。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Manager の地理的な一覧":::

1. *[設定]* の下にある **[エンドポイント]** を選択し、 **[+ 追加]** を選択して新しいエンドポイントを追加します。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="エンドポイントの追加":::

1. 以下の設定値を選択するか入力します。 

    | 設定                | 値                                              |
    | ---                    | ---                                                |
    | 種類                   | エンドポイントの種類を選択します。 実稼働環境で使用する地理的ルーティング プロファイルの場合、1 つ以上のエンドポイントを持つ子プロファイルを含む入れ子になったエンドポイントの種類を使用することを強くお勧めします。 詳細については、[地理的トラフィック ルーティング方法に関してよく寄せられる質問](traffic-manager-FAQs.md)を参照してください。 |    
    | 名前                   | このエンドポイントを識別する名前を指定します。 |
    | ターゲット リソースの種類   | ターゲットのリソースの種類を選択します。 |
    | ターゲット リソース        | 一覧からリソースを選択します。 |

    > [!Note]
    > このページ内の特定のフィールドは、追加するエンドポイントの種類によって決まります。
    > 1. Azure エンドポイントを追加する場合は、**[ターゲット リソースの種類]** と、トラフィックの転送先のリソースに基づいて **[ターゲット]** を選択します。
    > 1. **外部** エンドポイントを追加する場合、エンドポイントの **完全修飾ドメイン名 (FQDN)** を指定します。
    > 1. **入れ子になったエンドポイント** を追加する場合、使用する子プロファイルに対応する **ターゲット リソース** を選択し、**[Minimum child endpoints count (最小子エンドポイント数)]** を指定します。

1. *[Geo-mapping]\(geo マッピング\)* セクションで、ドロップダウンを使用して、このエンドポイントにトラフィックを送信するリージョンを追加します。 少なくとも 1 つのリージョンを追加する必要があります。 複数のリージョンをマップできます。

1. このプロファイルの下に追加するすべてのエンドポイントについて最後の手順を繰り返してから、 **[保存]** を選択します。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Traffic Manager エンドポイントの追加":::

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager プロファイルの使用

1.  ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索し、表示された結果内で Traffic Manager プロファイルを選択します。
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager プロファイルを検索する":::

1. **[Traffic Manager プロファイル]** に、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 この名前を任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。 地理的ルーティングの場合、Traffic Manager は、受信要求の発信元 IP を確認して、発信元のリージョンを判断します。 そのリージョンがエンドポイントにマッピングされている場合、トラフィックはそこにルーティングされます。 このリージョンがエンドポイントにマッピングされていない場合、Traffic Manager は NODATA クエリ応答を返します。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="地理的 Traffic Manager の概要":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Traffic Manager プロファイルが不要になったら、プロファイルを検索し、 **[プロファイルの削除]** を選択します。

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Traffic Manager プロファイルを削除する":::

## <a name="next-steps"></a>次のステップ

地理的トラフィック ルーティング方法の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [地理的トラフィック ルーティング方法](traffic-manager-routing-methods.md#geographic)
