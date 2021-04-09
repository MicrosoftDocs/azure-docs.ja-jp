---
title: チュートリアル:Azure Traffic Manager を使用した重み付けラウンドロビンによるトラフィック ルーティングの構成
description: このチュートリアルでは、Traffic Manager でラウンドロビン方式を使用してトラフィックの負荷分散を行う方法について説明します
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207841"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>チュートリアル:Traffic Manager の重み付けトラフィック ルーティング方法の構成

トラフィック ルーティング方法の一般的なパターンは、クラウド サービスや Web サイトを含む同一のエンドポイントのセットを提供し、各エンドポイントにトラフィックを均等に送信することです。 次の手順では、この種類のトラフィック ルーティング方法を構成する方法について説明します。

> [!NOTE]
> Azure Web App では、Azure リージョン (複数のデータセンターで構成される場合があります) 内の Web サイトに対するラウンド ロビン負荷分散の機能が既に用意されています。 Traffic Manager を使用すると、さまざまなデータ センター内の Web サイト間でトラフィックを分散できます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - 重み付けによるルーティングを使用して Traffic Manager プロファイルを作成する。
> - Traffic Manager プロファイルを使用する。
> - Traffic Manager プロファイルを削除する。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

## <a name="configure-the-weighted-traffic-routing-method"></a>重み付けによるトラフィック ルーティング方法を構成する

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。

1. ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索し、表示された結果内で Traffic Manager プロファイルを選択します。

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Traffic Manager プロファイルを検索する":::

1. **[構成]** を選択し、次の設定値を選択するか入力します。

    | 設定         | [値]                                              |
    | ---             | ---                                                |
    | ルーティング方法            | **[重み付け]** を選択します。 |    
    | DNS の有効期限 (TTL) | この値は、クライアントのローカル キャッシュ ネーム サーバーが、DNS エントリの更新について Traffic Manager システムにクエリを実行する頻度を制御します。 Traffic Manager で発生した変更 (トラフィック ルーティング方法の変更や追加されたエンドポイントの可用性の変更など) は、この期間ごとに、DNS サーバーのグローバル システム全体で更新されます。 |
    | Protocol    | エンドポイント監視用のプロトコルを選択します。 "*オプション: HTTP、HTTPS、および TCP*" |
    | Port | ポート番号を指定します。 |
    | Path | エンドポイントを監視するには、パスとファイル名を指定する必要があります。 スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。 |
    | カスタム ヘッダーの設定 | host:contoso.com,newheader:newvalue の形式でカスタム ヘッダーを構成します。 サポートされるペアの最大数は 8 です。 HTTP および HTTPS プロトコルに適用されます。 プロファイル内のすべてのエンドポイントに適用されます |
    | 予測される状態コードの範囲 (既定値: 200) | 状態コードの範囲は、200-299、301-301 という形式で構成します。 サポートされる範囲の最大数は 8 です。 HTTP および HTTPS プロトコルに適用されます。 プロファイル内のすべてのエンドポイントに適用されます |
    | プローブ間隔 | エンドポイントの正常性プローブ間の時間間隔を構成します。 10 または 30 秒を選択できます。 |
    | 障害の許容数 | エンドポイントの障害がトリガーされるまで、正常性プローブで許容される障害の数を構成します。 0 から 9 の範囲の数値を入力できます。 | 
    | プローブのタイムアウト | エンドポイントの正常性プローブがタイムアウトするまでに必要な時間を構成します。この値は 5 以上で、かつプローブ間隔の値より小さくなければなりません。 |

1. **[保存]** を選択して構成を完了します。

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Traffic Manager の重み付けの構成"::: 

1. **[エンドポイント]** を選択し、各エンドポイントの重みを構成します。 重みは 1 と 1000 の間にすることができます。 重みが大きくなると、優先順位がそれだけ上がります。  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Traffic Manager のエンドポイントの重み付け構成"::: 

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager プロファイルの使用

**[Traffic Manager プロファイル]** に、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 この名前を任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。 この場合、すべての要求がラウンドロビン方式で各エンドポイントにルーティングされます。

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Traffic Manager の重み付けの概要"::: 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Traffic Manager プロファイルが不要になったら、プロファイルを検索し、 **[プロファイルの削除]** を選択します。

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="重み付けによる Traffic Manager プロファイルを削除する":::

## <a name="next-steps"></a>次のステップ

重み付けによるルーティング方法の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [重み付けによるトラフィック ルーティング方法](traffic-manager-routing-methods.md#weighted)