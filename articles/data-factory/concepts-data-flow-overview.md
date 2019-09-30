---
title: Azure Data Factory の Mapping Data Flow の概要
description: Azure Data Factory の Mapping Data Flow の概要の説明
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123923"
---
# <a name="what-are-mapping-data-flows"></a>マッピング データ フローについて

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

マッピング データ フローは、Azure Data Factory の視覚的に設計されたデータ変換です。 データ フローを使用すると、データ エンジニアは、コードを記述することなくグラフィカルなデータ変換ロジックを開発できます。 生成されたデータ フローは、スケールアウトされた Azure Databricks クラスターを使用して、Azure Data Factory パイプライン内でアクティビティとして実行されます。

Azure Data Factory のデータ フローの目的は、コーディングなしで完全にビジュアルなエクスペリエンスを得られるようにすることです。 データ フローを実行クラスター上で実行することにより、データ処理がスケールアウトされます。 Azure Data Factory では、コード翻訳、パス最適化、およびデータ フローのジョブ実行のすべてが処理されます。

最初に、変換ロジックを対話的に検証できるようにするため、データ フローをデバッグ モードで作成します。 次に、実行するパイプラインにデータ フロー アクティビティを追加して、パイプライン デバッグでデータ フローをテストするか、またはパイプライン内で [Trigger Now]\(今すぐトリガー\) を使用して、パイプライン アクティビティからデータ フローをテストします。

続いて、データ フロー アクティビティを実行する Azure Data Factory のパイプラインを使用して、データ フローのアクティビティをスケジュールおよび監視します。

データ フロー デザイン サーフェス上にあるデバッグ モードのトグル スイッチにより、データ変換を対話的に作成できます。 デバッグ モードでは、データ フローを作成するためのデータ準備およびデータ プレビュー環境を利用できます。

## <a name="begin-building-your-data-flow-logical-graph"></a>データ フロー論理グラフのビルドを開始する

Factory リソースの下にある + 記号を使用して新しいデータ フローを作成して、データ フローのビルドを開始します。

![新しいデータ フロー](media/data-flow/newdataflow2.png "新しいデータ フロー")

ソース変換を構成して開始し、次に + 記号を使用して後続の各手順にデータ変換を追加します。 論理グラフの構築時には、"グラフの表示" および "グラフの非表示" ボタンを使用してグラフと構成間で切り替えを行えます。

![グラフの表示](media/data-flow/showg.png "グラフの表示")

## <a name="configure-transformation-logic"></a>変換ロジックを構成する

![グラフの非表示](media/data-flow/hideg.png "グラフの非表示")

グラフを非表示にすると、変換ノード間を水平方向に移動できるようになります。

![移動](media/data-flow/showhide.png "移動")

## <a name="next-steps"></a>次の手順

* [ソース変換から開始する](data-flow-source.md)
