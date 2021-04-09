---
title: チュートリアル:Azure Traffic Manager を使用した優先順位によるトラフィック ルーティングの構成
description: このチュートリアルでは、Traffic Manager で優先順位によるトラフィック ルーティング方法を構成する方法について説明します
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92208058"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>チュートリアル:Traffic Manager での優先順位によるトラフィック ルーティング方法の構成

このチュートリアルでは、Azure Traffic Manager を使用して、優先順位によるルーティング方法で特定のエンドポイントにユーザー トラフィックをルーティングする方法について説明します。 このルーティング方法では、Traffic Manager プロファイル構成に含まれる各エンドポイントの順序を定義します。 ユーザーからのトラフィックは、一覧に示されている順序でエンドポイントにルーティングされます。 このルーティング方法は、サービスのフェールオーバー用に構成する場合に便利です。 プライマリ エンドポイントは、優先順位番号 "1" となり、すべての受信要求を処理します。 これより優先順位の低いエンドポイントはバックアップとして機能します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - 優先順位によるルーティングを使用して Traffic Manager プロファイルを作成する。
> - エンドポイントを追加する。
> - エンドポイントの優先順位を構成する。
> - Traffic Manager プロファイルを使用する。
> - Traffic Manager プロファイルを削除する。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="to-configure-the-priority-traffic-routing-method"></a>優先順位によるトラフィック ルーティング方法を構成するには
1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側で **[+ リソースの作成]** を選択します。 「**Traffic Manager プロファイル**」を検索し、 **[作成]** を選択します。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="優先順位によって Traffic Manager プロファイルを作成する":::

1. *[Traffic Manager プロファイルの作成]* ページで、次の設定を定義します。

    | 設定         | 値                                              |
    | ---             | ---                                                |
    | 名前            | プロファイルの名前を指定します。 この名前は、trafficmanager.net zone 内で一意である必要があります。 Traffic Manager プロファイルにアクセスするには、ご自分の DNS 名 `<profilename>.trafficmanager.net` を使用します。 |    
    | ルーティング方法  | **[優先順位]** を選択します。 |
    | サブスクリプション    | サブスクリプションを選択します。 |
    | Resource group   | 既存のリソース グループを使用するか、新しいリソース グループを作成して、その下にこのプロファイルを配置します。 新しいリソース グループを作成する場合は、 *[リソース グループの場所]* ドロップダウンを使用して、リソース グループの場所を指定します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。 |

1. **[作成]** を選択し、Traffic Manager プロファイルをデプロイします。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Traffic Manager プロファイルの優先順位を作成する":::

## <a name="add-endpoints"></a>エンドポイントの追加

1. 一覧から Traffic Manager プロファイルを選択します。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Traffic Manager プロファイルの一覧":::

1. *[設定]* の下にある **[エンドポイント]** を選択し、 **[+ 追加]** を選択して新しいエンドポイントを追加します。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Traffic Manager のエンドポイントを追加する":::

1. 以下の設定値を選択するか入力します。 

    | 設定                | 値                                              |
    | ---                    | ---                                                |
    | 種類                   | エンドポイントの種類を選択します。 |    
    | 名前                   | このエンドポイントを識別する名前を指定します。 |
    | ターゲット リソースの種類   | ターゲットのリソースの種類を選択します。 |
    | ターゲット リソース        | 一覧からリソースを選択します。 |
    | 優先度               | このエンドポイントの優先順位番号を指定します。 1 を設定すると、最も優先度が高くなります。 |


1. **[追加]** を選択してエンドポイントを追加します。 手順 2. と 3. を繰り返して、他のエンドポイントを追加します。 必ず適切な優先順位番号を設定してください。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="優先順位 1 のエンドポイントを追加する":::

1. **[エンドポイント]** ページで、エンドポイントの優先順位を確認します。 **優先順位** によるトラフィック ルーティング方法を選択する場合、選択したエンドポイントの順序が重要です。 エンドポイントの優先順位を確認します。  最上位のエンドポイントがプライマリ エンドポイントになります。 表示されている順序を再確認してください。 すべての要求が最初のエンドポイントにルーティングされますが、Traffic Manager によって異常状態が検出された場合は、トラフィックが自動的に次のエンドポイントにフェールオーバーされます。 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="優先順に並んだエンドポイントの一覧":::

1. エンドポイントの優先順位を変更するには、エンドポイントを選択して、優先順位の値を変更し、 **[保存]** を選択してエンドポイントの設定を保存します。

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager プロファイルの使用

1.  ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索し、表示された結果内で Traffic Manager プロファイルを選択します。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager プロファイルを検索する":::

1.  **Traffic Manager プロファイル** の概要ページに、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 これを任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。 この場合、すべての要求が最初のエンドポイントにルーティングされますが、Traffic Manager によって異常状態が検出された場合は、トラフィックが自動的に次のエンドポイントにフェールオーバーされます。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Traffic Manager の DNS 名":::

1. Traffic Manager プロファイルが機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Traffic Manager プロファイルが不要になったら、プロファイルを検索し、 **[プロファイルの削除]** を選択します。

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="優先順位による Traffic Manager プロファイルを削除する":::

## <a name="next-steps"></a>次のステップ

優先順位によるルーティング方法の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [優先順位によるルーティング方法](traffic-manager-routing-methods.md#priority-traffic-routing-method)
