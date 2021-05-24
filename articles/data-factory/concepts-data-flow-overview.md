---
title: データ フローのマッピング
description: Azure Data Factory のマッピング データ フローの概要
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/11/2021
ms.openlocfilehash: 53f300adb57eb17e704e7e3323b9829c485bb87a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308984"
---
# <a name="mapping-data-flows-in-azure-data-factory">Azure Data Factory のマッピング データ フロー</a>

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows">マッピング データ フローとは</a>

マッピング データ フローは、Azure Data Factory における視覚的に設計されたデータ変換です。 データ フローを使用すると、データ エンジニアは、コードを記述することなくデータ変換ロジックを開発できます。 生成されたデータ フローは、スケールアウトされた Apache Spark クラスターを使用する Azure Data Factory パイプライン内のアクティビティとして実行されます。 データ フロー アクティビティは、既存の Azure Data Factory のスケジュール設定、制御、フロー、および監視機能を使用して運用可能にすることができます。

マッピング データ フローは、コーディングを必要としない、完全に視覚的なエクスペリエンスを提供します。 データ フローは、スケール アウトされたデータ処理のために、ADF で管理される実行クラスターで実行されます。 コードの翻訳、パスの最適化、データ フロー ジョブの実行はすべて、Azure Data Factory によって処理されます。

## <a name="getting-started">作業の開始</a>

データ フローは、パイプラインやデータセットなどのファクトリ リソースのペインから作成されます。 データ フローを作成するには、 **[Factory Resources]\(Factory リソース\)** の横にあるプラス記号アイコンを選択して、 **[データ フロー]** を選択します。 

![新しいデータ フロー](media/data-flow/new-data-flow.png)

このアクションにより、変換ロジックを作成できるデータ フロー キャンバスに移動します。 **[ソースの追加]** を選択すると、ソース変換の構成が開始します。 詳細については、[ソース変換](data-flow-source.md)に関するページを参照してください。

## <a name="authoring-data-flows&quot;>データ フローの作成</a>

マッピング データ フローには、変換ロジックを簡単に構築するために設計された独自の作成キャンバスがあります。 データ フロー キャンバスは、上部バー、グラフ、および構成パネルの 3 つの部分に分かれています。 

![スクリーンショットには、上部バー、グラフ、および構成パネルにラベルが付いたデータ フロー キャンバスが示されています。](media/data-flow/canvas-1.png &quot;キャンバス")

### <a name="graph">グラフ</a>

グラフには変換ストリームが表示されます。 ここにはソース データが 1 つ以上のシンクに流れるときのソース データの系列が表示されます。 新しいソースを追加するには、 **[ソースの追加]** を選択します。 新しい変換を追加するには、既存の変換の右下にあるプラス記号を選択します。 詳しくは、[データ フロー グラフの管理](concepts-data-flow-manage-graph.md)方法に関するページを参照してください。

![スクリーンショットには、[検索] テキスト ボックスがあるキャンバスのグラフ部分が示されています。](media/data-flow/canvas-2.png)

### <a name="configuration-panel">構成パネル</a>

構成パネルには、現在選択されている変換に固有の設定が表示されます。 変換が選択されていない場合は、データ フローが表示されます。 データフローの全体構成では、 **[パラメーター]** タブを使用してパラメーターを追加できます。詳しくは、「[マッピング データ フローのパラメーター](parameters-data-flow.md)」を参照してください。

各変換には、少なくとも 4 つの構成タブが含まれます。

#### <a name="transformation-settings">変換設定</a>

各変換の構成ウィンドウの最初のタブには、その変換に固有の設定が含まれています。 詳しくは、各変換のドキュメント ページを参照してください。

![[Source Settings] タブ](media/data-flow/source1.png "[Source Settings] タブ")

#### <a name="optimize">最適化</a>

**[最適化]** タブには、パーティション分割を構成するためのオプション設定が含まれています。 データ フローを最適化する方法の詳細については、[マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)に関する記事を参照してください。

![パーティション オプション、パーティションの種類、パーティションの数が含まれる [最適化] タブが示されているスクリーンショット。](media/data-flow/optimize.png)

#### <a name="inspect">検査</a>

**[Inspect]\(検査\)** タブには、変換するデータ ストリームのメタデータのビューが表示されます。 列数、変更された列、追加された列、データ型、列の順序、および列の参照を確認できます。 **[Inspect]\(検査\)** は、メタデータの読み取り専用ビューです。 **[Inspect]\(検査\)** ペインでメタデータを表示するためにデバッグ モードを有効にする必要はありません。

![検査](media/data-flow/inspect1.png "検査")

変換を使ってデータの形状を変更すると、メタデータの変更が **[Inspect]\(検査\)** ペインに反映されます。 ソースの変換に定義済みのスキーマがない場合、メタデータは **[Inspect]\(検査\)** ペインに表示されません。 スキーマの誤差シナリオでは、メタデータがないことは一般的です。

#### <a name="data-preview">データのプレビュー</a>

デバッグ モードがオンの場合、 **[データのプレビュー]** タブには、各変換のデータの対話型スナップショットが表示されます。 詳細については、[デバッグ モードでのデータのプレビュー](concepts-data-flow-debug-mode.md#data-preview)に関するセクションを参照してください。

### <a name="top-bar">上部バー</a>

上部バーには、保存や検証など、データ フロー全体に影響を与えるアクションが含まれています。 基になる JSON コードと、変換ロジックのデータ フロー スクリプトも表示できます。 詳細については、「[データ フロー スクリプト](data-flow-script.md)」を参照してください。

## <a name="available-transformations">使用可能な変換</a>

使用可能な変換の一覧を取得するには、「[マッピング データ フロー変換の概要](data-flow-transformation-overview.md)」を表示してださい。

## <a name="data-flow-activity">データ フロー アクティビティ</a>

マッピング データ フローは、ADF パイプライン内で[データ フロー アクティビティ](control-flow-execute-data-flow-activity.md)を使用して運用可能にすることができます。 ユーザーが行う必要があるのは、使用する統合ランタイムを指定し、パラメーター値を渡すことだけです。 詳細については、「[Azure 統合ランタイム](concepts-integration-runtime.md#azure-integration-runtime)」を参照してください。

## <a name="debug-mode">デバッグ モード</a>

デバッグ モードを使用すると、データ フローを構築してデバッグしながら、各変換ステップの結果を対話形式で表示できます。 デバッグ セッションは、データ フロー ロジックを構築するときと、データ フロー アクティビティでパイプライン デバッグを実行するときの両方で使用できます。 詳細については、[デバッグ モードのドキュメント](concepts-data-flow-debug-mode.md)を参照してください。

## <a name="monitoring-data-flows">データ フローの監視</a>

マッピング データ フローは、既存の Azure Data Factory 監視機能と統合されます。 データ フローの監視出力を理解する方法については、[マッピング データ フローの監視](concepts-data-flow-monitoring.md)に関するページを参照してください。

ビジネス ロジックを構築した後のデータ フローの実行時間を最適化するのに役立つ、[パフォーマンス チューニング ガイド](concepts-data-flow-performance.md)が、Azure Data Factory チームによって作成されています。

## <a name="next-steps">次のステップ</a>

* [ソース変換](data-flow-source.md)を作成する方法について学習します。
* データ フローを[デバッグ モード](concepts-data-flow-debug-mode.md)で構築する方法について学習します。
