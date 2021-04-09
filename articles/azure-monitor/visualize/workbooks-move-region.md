---
title: Azure Monitor ブック - リージョンを移動する
description: ブックを別のリージョンに移動する方法
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d58d9ab48fa16fb5258f097ed4567e539e21c72c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100601131"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Azure ブックを別のリージョンに移動する

この記事では、Azure ブック リソースを別の Azure リージョンに移動する方法について説明します。 さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、新しい Azure リージョンを利用するため、特定のリージョンでのみ利用可能な機能やサービスをデプロイするため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。

## <a name="prerequisites"></a>前提条件

* ターゲット リージョンでブックがサポートされていることを確認します。

* これらの手順は、Azure Monitor およびほとんどの種類のリソースに保存されている共有ブック (`microsoft.insights/workbooks`) とプライベート ブック (`microsoft.insights/myworkbooks`) の両方に適用されます。

  ただし、Application Insights のリソースの種類専用にリンクされたブックの場合、それらのブックは、Application Insights リソースが保存されている Azure リージョンに格納されます。

  これらのブックを別のリージョンに個別に移動することはできません。

## <a name="move"></a>詳細ビュー

Azure ブックを移動する最も簡単な方法は、ポータル UI のブック ツールで [名前を付けて保存] を使用することです。

1. ブック ビューアーでターゲットのブックを開きます。
2. ツールバーの [編集] ボタンを使用して、編集モードに移行します。
3. ツールバーの [名前を付けて保存] ボタンを使用します。
4. [保存] フォームで、ブックの名前と目的のリージョンを選択します。 サブスクリプション、リソース グループ、および共有のその他のフィールドが適切であることを確認します。

   > [!NOTE]
   > 場合によっては、名前の重複を避けるためにブックに新しい名前を使用する必要があります。

5. 保存します。 

## <a name="verify"></a>確認

Azure ブックの参照 UI を使用して、新しいブックを検索します。 その場所がターゲットの場所であることを確認します。

## <a name="clean-up"></a>クリーンアップ

新しいリージョンにブック作成したら、以前のリージョンにある元のブックを削除します。
1. ブック ビューアーで元のブックを開きます。
2. ツールバーの [編集] ボタンを使用して、編集モードに移行します。
3. ツールの編集ドロップダウン (鉛筆アイコン) で、[ブックの削除] を選択します。

ブックの名前を変更して新しいリージョンにインポートした場合は、[編集モード] ツールバーにあるツールの編集ドロップダウンの [名前の変更] 項目を使用して元のブックを削除した後で、ブックの名前を以前の名前に変更できます。

## <a name="next-steps"></a>次の手順

ブックの代わりにブック テンプレートを移動する必要がある場合は、 [Azure ブック テンプレートを別のリージョンに移動する](./workbook-templates-move-region.md)方法を参照してください。

ARM テンプレートを使用して[ブックとブック テンプレートをデプロイする](../visualize/workbooks-automate.md)方法を参照してください。
