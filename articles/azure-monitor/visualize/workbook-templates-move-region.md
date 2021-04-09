---
title: Azure Monitor ブック テンプレート - リージョンを移動する
description: ブック テンプレートを別のリージョンに移動する方法
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: 9787118b1912a5697c02e116be99b1f396748937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100600544"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Azure ブック テンプレートを別のリージョンに移動する

この記事では、Azure ブック テンプレートのリソースを別の Azure リージョンに移動する方法について説明します。 さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、新しい Azure リージョンを利用するため、特定のリージョンでのみ利用可能な機能やサービスをデプロイするため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。

現在、ブック テンプレートのリソースを作成するためのポータル UI はありません。現時点では、[Azure Resource Manager テンプレート (ARM テンプレート) のデプロイを使用](../visualize/workbooks-automate.md)するのが唯一の作成方法です。 そのため、テンプレートを移動する最も簡単な方法は、以前の ARM テンプレートを再利用し、それを更新して新しいリージョンにデプロイすることです。

## <a name="prerequisites"></a>前提条件

* ブック テンプレートがターゲット リージョンでサポートされていることを確認します。

## <a name="prepare"></a>準備

* ブック テンプレートとして以前使用した ARM テンプレートを特定します。

## <a name="move"></a>詳細ビュー

1. 以前使用したテンプレートを更新して、新しいリージョンを参照します。

   > [!NOTE]
   > 場合によっては、ブック テンプレートに新しい名前を使用して、名前の重複を避ける必要があります。

2. ARM テンプレートのデプロイを使用して、更新したテンプレートをデプロイし、目的のリージョンに新しいブック テンプレートを作成します。

## <a name="verify"></a>確認

Azure ブックの参照 UI を使用して、新しくデプロイされたブック テンプレートを検索します。 その場所がターゲットの場所であることを確認します。

## <a name="clean-up"></a>クリーンアップ

新しいリージョンにブック テンプレートを作成したら、以前のリージョンにある元のブック テンプレートを削除します。
1. Azure ブックの参照 UI でブック テンプレートを検索します。
2. 削除するブック テンプレートを選択します。
3. [削除] コマンドを選択します。

ブック テンプレートの名前を変更して新しいリージョンにインポートした場合は、Azure ブック テンプレートのリソース ビューで [名前の変更] コマンドを使用して元の項目が削除された後に、そのブック テンプレートの名前を以前の名前に変更することができます。

## <a name="next-steps"></a>次の手順

テンプレートではなく、ブックを移動する必要がある場合は、 [Azure ブックを別のリージョンに移動する](./workbooks-move-region.md)方法に関する記事を参照してください。

