---
title: Azure Sentinel ウォッチリストを使用する
description: この記事では、Azure Sentinel ウォッチリストを使用して、脅威の調査、ビジネス データのインポート、許可リストの作成、およびイベント データの強化を行う方法について説明します。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99555637"
---
# <a name="use-azure-sentinel-watchlists"></a>Azure Sentinel ウォッチリストを使用する

> [!IMPORTANT]
> ウォッチリスト機能は現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Sentinel ウォッチリストを使用すると、Azure Sentinel 環境内のイベントと関連付けるために、外部データ ソースからのデータ収集が可能になります。 ウォッチリストを作成すると、検索、検出規則、脅威ハンティング、および応答プレイブックで使用できます。 ウォッチリストは、名前と値のペアとして Azure Sentinel ワークスペースに格納され、最適なクエリ パフォーマンスと待ち時間の短縮のためにキャッシュされます。

ウォッチリストを使用する一般的なシナリオは次のとおりです。

- IP アドレス、ファイル ハッシュ、およびその他のデータを CSV ファイルから短時間でインポートすることで、迅速に **脅威の調査** とインシデントへの対応を行います。 インポート後は、アラート ルール、脅威ハンティング、ブック、ノートブック、および一般的なクエリの結合とフィルターに、ウォッチリストの名前と値のペアを使用できます。

- ウォッチリストとして **ビジネス データをインポート** します。 たとえば、システムの特権アクセスが割り当てられているユーザーリスト、または退職した従業員をインポートした後、ウォッチリストを使用して、これらのユーザーによるネットワークへのログインを検出または防止するために使用される許可リストや拒否リストを作成します。

- **アラート疲れを軽減** します。 通常はアラートをトリガーするタスクを実行する承認済み IP アドレスのユーザーなど、ユーザー グループからのアラートが表示されないように許可リストを作成し、問題のないイベントがアラートにならないようにします。

- **イベント データを強化** します。 ウォッチリストを使用して、外部データ ソースから派生した名前と値の組み合わせでイベント データを強化します。

## <a name="create-a-new-watchlist"></a>新しいウォッチリストを作成する

1. Azure portal で **[Azure Sentinel]**  >  **[構成]**  >  **[ウォッチリスト]** の順に移動し、 **[新規追加]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![新しいウォッチリスト](./media/watchlists/sentinel-watchlist-new.png)

1. **[全般]** ページで、そのウォッチリストの名前、説明、および別名を指定し、 **[次へ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ウォッチリストの [全般] ページ](./media/watchlists/sentinel-watchlist-general.png)

1. **[ソース]** ページで、データセットの種類を選択し、ファイルをアップロードして、 **[次へ]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="ウォッチリストの [ソース] ページ" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 現在、ファイルのアップロードは、最大 3.8 MB のファイルに制限されています。

1. 情報を見直して、それが正しいことを確認してから、 **[作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ウォッチリストの確認ページ](./media/watchlists/sentinel-watchlist-review.png)

    ウォッチリストが作成されると、通知が表示されます。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="ウォッチリストの作成成功の通知" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>クエリでウォッチリストを使用する

1. Azure portal で **[Azure Sentinel]**  >  **[構成]**  >  **[ウォッチリスト]** の順に移動し、使用するウォッチリストを選択して、 **[ログ分析で表示]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="クエリでのウォッチリストの使用" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. ウォッチリストの項目がクエリのために自動的に抽出され、 **[結果]** タブに表示されます。次の例では、**ServerName** および **IpAddress** フィールドの抽出結果を示しています。

    > [!NOTE]
    > クエリのタイムスタンプは、クエリ UI とスケジュールされたアラートの両方で無視されます。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="ウォッチリストのフィールドを使用したクエリ" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. ウォッチリストを結合または検索のテーブルとして扱うことで、あるテーブルに含まれるデータがウォッチリストのデータにないか照合できます。

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="検索としてウォッチリストに対して実行するクエリ":::

## <a name="use-watchlists-in-analytics-rules"></a>分析ルールでウォッチリストを使用する

分析ルールでウォッチリストを使用するには、Azure portal で **[Azure Sentinel]**  >  **[構成]**  >  **[分析]** の順に移動し、クエリで `_GetWatchlist('<watchlist>')` 関数を使用してルールを作成します。

1. この例では、"ipwatchlist" という名前のウォッチリストを次の値で作成します。

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="ウォッチリストの 4 項目の一覧":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="項目が 4 つあるウォッチリストを作成する":::

1. 次に、分析ルールを作成します。  この例では、ウォッチリストに IP アドレスからのイベントのみを含めます。

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="分析ルールでのウォッチリストの使用":::

## <a name="view-list-of-watchlists-aliases"></a>ウォッチリスト エイリアスの一覧を表示する

ウォッチリスト エイリアスの一覧を取得するには、Azure portal で **[Azure Sentinel]**  >  **[全般]**  >  **[ログ]** の順に移動し、`_GetWatchlistAlias` というクエリを実行します。 別名の一覧が **[結果]** タブに表示されます。

> [!div class="mx-imgBorder"]
> ![ウォッチリストの一覧表示](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Sentinel でウォッチリストを使用して、データを強化し、調査を向上させる方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
