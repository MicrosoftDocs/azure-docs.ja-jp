---
title: マッピング データ フローの新しい分岐変換
description: Azure Data Factory マッピング データ フローの新しい分岐変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: b4617689fe1ab14856bde9a4e8134b12aa6d815b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930314"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory マッピング データ フローの新しい分岐変換

![分岐オプション](media/data-flow/menu.png "メニュー")

分岐では、データ フローから現在のデータ ストリームを取得し、それを別のストリームにレプリケートします。 新しい分岐を使って、同じデータ ストリームに対して複数の操作と変換を行います。

例:データ フローにソース変換があり、選択した列セットとデータ型変換を使用しているとします。 ここで、そのソースの直後に派生列を配置します。 派生列では、姓と名を結合して新しい "氏名" フィールドを作る新しいフィールドを作成しました。

この新しいストリームを一連の変換と 1 行のシンクで処理し、新しい分岐を使ってこのストリームのコピーを作成して、同じデータを別の変換セットを使って変換できます。 別の分岐にコピーしたデータを変換することで、そのデータを後から別の場所で受け取ることができます。

> [!NOTE]
> [New Branch]\(新しい分岐\) は、分岐しようとしている現在の場所に続けて変換が行われる場合に、[+ 変換] メニューのアクションとして表示されます。 つまり、選択の後に別の変換を追加しない限り、ここに [New Branch]\(新しい分岐\) オプションは表示されません。

![分岐](media/data-flow/branch2.png "分岐 2")
