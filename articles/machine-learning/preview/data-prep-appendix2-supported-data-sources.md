---
title: "Azure Machine Learning データ準備で使用可能なサポートされているデータ ソース | Microsoft Docs"
description: "このドキュメントでは、Azure ML データ準備で使用可能なサポートされているデータ ソースの完全な一覧を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 0a77b31e07f118155094fab4d3393ffdb37c9c6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-for-this-release"></a>このリリースでサポートされるデータ ソース 
次のドキュメントは、データ準備において現在サポートされているデータ ソースの一覧を示しています。

このリリースでサポートされているデータ ソースを以下に示します。

## <a name="types"></a>型 
### <a name="directory-vs-file"></a>ディレクトリとファイル
ファイル / ディレクトリ - 単一のファイルを選択し、それをデータ準備に読み込みます。ファイルの種類は解析され、次の画面に示されるファイル接続の既定のパラメーターを決定します。 ディレクトリまたはディレクトリ内のファイル セットを選択します。(ファイル ピッカーは複数選択)。どちらかのアプローチを使用すると、ファイルが互いに付加される単一のデータフローとしてファイルが読み込まれるという結果になります (必要に応じてヘッダーが取り除かれます)

サポートされている種類は次のとおりです。
- 区切り形式ファイル (csv、tsv、txt など)、 
- 固定幅ファイル
- プレーン テキスト ファイル
- JSON ファイル

### <a name="csv-file"></a>CSV ファイル
コンマ区切り値ファイルを記憶域から読み込む

#### <a name="options"></a>オプション
- 区切り記号
- コメント
- headers
- 小数点の記号
- ファイルのエンコード
- スキップする行

### <a name="tsv-file"></a>TSV ファイル
タブ区切り値ファイルを記憶域から読み込む

#### <a name="options"></a>オプション
- コメント
- headers
- ファイルのエンコード
- スキップする行

### <a name="excel-xlsxlsx"></a>Excel (.xls/xlsx)
シート名または番号を指定して一度に 1 シートずつ Excel ファイルを読み取ります

#### <a name="options"></a>オプション
- シート名/番号
- headers
- スキップする行

### <a name="json-file"></a>JSON ファイル
記憶域から JSON ファイルを読み取ります。ファイルは読み取り時に "フラット化" されることに注意してください

#### <a name="options"></a>オプション
- なし

### <a name="parquet"></a>Parquet
単一のファイルまたはフォルダーのどちらかで Parquet データセットを読み取ります。

形式としての Parquet は、記憶域でさまざまなフォームを取ることができます。 小さなデータセットでは多くの場合、単一の ".parquet" ファイルが使用され、単一の ".parquet" ファイルの読み取り/書き込みは、さまざまな python ライブラリでサポートされています。 現時点では、AMLWB は、ローカルで "対話式" に使用している間は、Parquet の読み出しに PyArrow python ライブラリに依存します。 Parquet データセットのほか、単一の ".parquet" ファイルをサポートします (大きなデータセットの一部ではなくそれ自体で書き込まれている限り)。 Parquet データセットは、それぞれが大きなデータセットの小さなパーティションを表す複数の ".parquet" ファイルのコレクションです。 データセットは通常、フォルダーに含まれており、Spark や Hive などの一般的なプラットフォームの既定の parquet 出力形式です。

>[!NOTE]
>複数の ".parquet" ファイルを含むフォルダー内にある Parquet データを読み取る場合、読み取り用のディレクトリを選択し、[Parquet Dataset] "Parquet データセット" オプションをティックすることが最も安全です。 このため、PyArrow は個々のファイルではなくフォルダー全体を読み取り、Parquet をディスクに格納するより複雑な方法を読み取るためのサポートを確保しています**

スケール アウトの実行は、Spark の Parquet 読み取り機能に依存し、ローカルでの対話式と同様に、単一のファイルのほかフォルダーもサポートします。

#### <a name="options"></a>オプション
- Parquet データセット
  - このオプションは、AMLWB が所定のディレクトリを展開し、その中の各ファイルを個別に読み取るか (ティック解除モード)、ディレクトリをデータ セット全体として扱い、PyArrow にファイルを解釈する最善な方法を見つけさせる (ティック モード) かどうかを決定します。


## <a name="locations"></a>場所
### <a name="local"></a>ローカル
ローカルのハード ドライブまたはマッピングされたネットワーク上の保存場所

### <a name="azure-blob"></a>Azure BLOB
Azure Storage (BLOB)、Azure サブスクリプションが必要

