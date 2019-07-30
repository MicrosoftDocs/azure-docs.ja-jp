---
title: Azure Data Factory Mapping Data Flow のデバッグ モード
description: データ フローの構築時に対話型デバッグ セッションを開始する
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147390"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapping Data Flow のデバッグ モード

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory Mapping Data Flow のデバッグ モードは、デザイン サーフェスの上部にある [Data Flow Debug] (データ フローのデバッグ) ボタンを使って切り替えることができます。 データ フローを設計するときにデバッグ モードを有効にすると、データ フローの構築およびデバッグ時にデータ シェイプの変換を対話的に監視できます。 デバッグ セッションは、Data Flow のデザイン セッションでも、データ フローのパイプライン デバッグ実行中にも使用できます。

![[デバッグ] ボタン](media/data-flow/debugbutton.png "[デバッグ] ボタン")

## <a name="overview"></a>概要
デバッグ モードが有効なときは、アクティブな Spark クラスターを使用して対話的にデータ フローを構築します。 Azure Data Factory でデバッグを無効にすると、セッションは終了します。 デバッグ セッションを有効にしている間に Azure Databricks によって発生する 1 時間あたりの料金を把握しておく必要があります。

ほとんどの場合、デバッグ モードでデータ フローを構築し、ビジネス ロジックを検証してデータ変換を確認してから、Azure Data Factory で作業内容を公開することをお勧めします。 パイプライン パネルの [デバッグ] ボタンを使用して、パイプライン内部のデータ フローをテストします。

> [!NOTE]
> Data Factory ツールバーのデバッグ モード ライトが緑色の間、60 分の Time to Live と 8 コア/時の一般コンピューティングの Data Flow デバッグ レートで課金されます 

> [!NOTE]
>デバッグ モードで Data Flow を実行している場合、データはシンク変換に書き込まれません。 デバッグ セッションは、変換のテスト ハーネスとして機能することを目的としています。 シンクはデバッグ中には必要ではなく、データ フローでは無視されます。 シンクへのデータの書き込みをテストする場合は、Azure Data Factory パイプラインから Data Flow を実行し、パイプラインからデバッグの実行を使用します。

## <a name="debug-settings"></a>デバッグ設定
デバッグ設定は、Data Flow のキャンバス ツール バーにある [Debug Settings] (デバッグ設定) をクリックすることで編集できます。 ここで、各ソース変換に使用する制限やファイル ソースを選択できます。 この設定の行制限の対象となるのは、現在のデバッグ セッションのみです。 SQL DW ソースに使用する、ステージングのリンクされたサービスを選択することもできます。 

![デバッグ設定](media/data-flow/debug-settings.png "デバッグ設定")

## <a name="cluster-status"></a>クラスターの状態
デザイン サーフェスの上部にはクラスター状態インジケーターがあり、クラスターのデバッグの準備が整うと緑色に変わります。 クラスターが既に実行されている場合、緑色のインジケーターはほぼ瞬時に表示されます。 デバッグ モードを開始したときにクラスターがまだ実行されていなかった場合は、クラスターが起動するまで 5 分から 7 分待つ必要があります。 インジケーターは、その準備ができるまでスピンします。

デバッグが終了したら、[デバッグ] スイッチをオフにして、Azure Databricks クラスターを終了できるようにし、デバッグ アクティビティについて課金されないようにします。

## <a name="data-preview"></a>データのプレビュー
デバッグが有効な場合、下部のパネルに [Data Preview]\(データのプレビュー\) タブが表示されます。 デバッグが有効ではない場合、Data Flow では、各変換に含まれる、または含まれない現在のメタデータのみが [Inspect]\(検査\) タブに表示されます。データのプレビューでは、デバッグ設定で制限として設定した行数のみのクエリが実行されます。 データのプレビューを更新するには、[Fetch data]\(データのフェッチ\) をクリックする必要があります。

![データのプレビュー](media/data-flow/datapreview.png "データのプレビュー")

## <a name="data-profiles"></a>データのプロファイル
[Data Preview] (データのプレビュー) タブで個々の列を選択すると、データ グリッドの右端にグラフがポップアップ表示され、各フィールドに関する詳細な統計情報が示されます。 Azure Data Factory では、表示するグラフの種類のデータ サンプリングに基づいて判断が下されます。 カーディナリティが高いフィールドの既定は NULL/NOT NULL グラフです。一方、カーディナリティが低いカテゴリ データや数値データでは、データ値の頻度を示す棒グラフが表示されます。 また、文字列フィールドの最大/長さ、数値フィールドの最小/最大値、標準偏差、百分位、カウント、および平均も表示されます。 

![列の統計](media/data-flow/stats.png "列の統計")

## <a name="next-steps"></a>次の手順

データ フローの構築とデバッグが完了したら、[パイプラインからデータ フローを実行します。](control-flow-execute-data-flow-activity.md)

データ フローでパイプラインをテストする場合は、パイプラインの[デバッグ実行の実行オプションを使用します。](iterative-development-debugging.md)
