---
title: データフローの並べ替え変換のマッピング
description: Azure Data Factory の Mapping Data の並べ替え変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930224"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory Data Flow の並べ替え変換



![並べ替えの設定](media/data-flow/sort.png "並べ替え")

並べ替え変換を使用すると、現在のデータ ストリームで受信した行を並べ替えることができます。 その後、並べ替え変換から送信される行は、設定された順序付けの規則に従います。 各フィールドの横にある矢印インジケーターを使用して、個々の列を選択して昇順または降順で並べ替えることができます。 並べ替えを適用する前に列を変更する必要がある場合は、[計算列] をクリックして式エディターを起動します。 これにより、単に列に並べ替えを適用するのではなく、並べ替え操作用の式を作成できます。

## <a name="case-insensitive"></a>大文字と小文字は区別されない
文字列やテキスト フィールドを並べ替えるときに大文字と小文字を区別したくない場合は、[大文字と小文字を区別しない] を有効にできます。

[Sort Only Within Partitions]\(パーティション内のみの並べ替え\) では、Spark のデータのパーティション分割を利用します。 各パーティション内だけで受信データを並べ替えることにより、Data Flow では、データ ストリーム全体を並べ替えることなく、パーティション分割されたデータを並べ替えることができます。

並べ替え変換の並べ替え条件の順序は変更できます。 そのため、並べ替えの優先順位の上位に列を移動する必要がある場合は、マウスでその行をつかんで、並べ替えの一覧を上または下に移動します。

並べ替えに対するパーティション分割の影響

ADF Data Flow は、複数のノードやパーティションにデータが分散されている、ビッグ データの Spark クラスター上で実行されます。 並べ替え変換によってデータの順序を保持している場合は、データ フローの設計時にこの点に留意することが重要です。 後続の変換でデータのパーティションを再作成すると、データがシャッフルされるため、並べ替えた順序が失われる可能性があります。

## <a name="next-steps"></a>次の手順

並べ替え後に、[集計変換](data-flow-aggregate.md)を使用できます
