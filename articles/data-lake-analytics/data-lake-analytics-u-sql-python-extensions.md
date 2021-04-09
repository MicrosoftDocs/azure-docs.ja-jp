---
title: Azure Data Lake Analytics で Python を使用した U-SQL スクリプトの拡張
description: Azure Data Lake Analytics を使用して U-SQL スクリプトで Python コードを実行する方法を説明します
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.custom: devx-track-python
ms.openlocfilehash: d81e529d7073e8200c5a1d1fa8f51504b1dc9259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218444"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics で Python コードを使用した U-SQL スクリプトの拡張

## <a name="prerequisites"></a>前提条件

始める前に、Azure Data Lake Analytics アカウントに Python 拡張機能がインストールされていることを確認します。

* Azure Portal で Data Lake Analytics アカウントに移動します
* 左側のメニューで、 **[作業の開始]** の **[サンプル スクリプト]** をクリックします
* **[U-SQL Extensions をインストールする]** をクリックし、 **[OK]** をクリックします

## <a name="overview"></a>概要

U-SQL 用の Python 拡張機能では、開発者が Python コードの膨大な並列実行を実行できるようにします。 基本的な手順を以下の例で示します。

* `REFERENCE ASSEMBLY` ステートメントを使用して、U-SQL スクリプト用の Python 拡張機能を有効にします
* `REDUCE` 操作を使用してキーの入力データをパーティション化します
* U-SQL 用の Python 拡張機能には、レジューサ (`Extension.Python.Reducer`) が組み込まれており、レジューサに割り当てられている各頂点で Python コードを実行します
* U-SQL スクリプトには、 "`usqlml_main`" という機能を持つ埋め込み型 Python コードが含まれています。この機能は、入力として pandas DataFrame を受け入れ、出力として pandas DataFrame を返します。

```usql
REFERENCE ASSEMBLY [ExtPython];
DECLARE @myScript = @"
def get_mentions(tweet):
    return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )
def usqlml_main(df):
    del df['time']
    del df['author']
    df['mentions'] = df.tweet.apply(get_mentions)
    del df['tweet']
    return df
";
@t  =
    SELECT * FROM
       (VALUES
           ("D1","T1","A1","@foo Hello World @bar"),
           ("D2","T2","A2","@baz Hello World @beer")
       ) AS date, time, author, tweet );
@m  =
    REDUCE @t ON date
    PRODUCE date string, mentions string
    USING new Extension.Python.Reducer(pyScript:@myScript);
OUTPUT @m
    TO "/tweetmentions.csv"
    USING Outputters.Csv();
```

## <a name="how-python-integrates-with-u-sql"></a>Python と U-SQL が統合する方法

### <a name="datatypes"></a>データ型

* U-SQL の文字列と数値列は、Pandas および U SQL のどちらかそのままの状態で変換されます
* U-SQL の Null は Pandas の "`NA`" 値に双方向で変換されます

### <a name="schemas"></a>スキーマ

* U-SQL では、Pandas のインデックス ベクターはサポートされていません。 Python 関数内のすべての入力データ フレームには、常に 0 から、行数から 1 を引いた数までの 64 ビットの数値インデックスがあります。
* U-SQL データセットでは、重複する列名を持つことはできません
* 文字列以外の U-SQL データセットの列名。

### <a name="python-versions"></a>Python のバージョン

Python 3.5.1 (Windows 用にコンパイル) のみサポートされています。

### <a name="standard-python-modules"></a>標準的な Python モジュール

すべての標準的な Python モジュールが含まれます。

### <a name="additional-python-modules"></a>追加の Python モジュール

標準的な Python ライブラリのほかにも、一般的に使用される Python ライブラリの一部が含まれます。

* pandas
* numpy
* numexpr

### <a name="exception-messages"></a>例外メッセージ

現時点では、Python コードの例外は一般的な頂点のエラーとして表示されます。 今後、U-SQL ジョブのエラー メッセージでは、Python の例外メッセージが表示されます。

### <a name="input-and-output-size-limitations"></a>入力と出力のサイズ制限

各頂点には、割り当てられたメモリ量に制限があります。 現在、AU の制限は 6 GB です。 入力と出力の DataFrame は Python コード内のメモリに存在する必要があるため、入力と出力の合計サイズは 6 GB を超えることはできません。

## <a name="next-steps"></a>次のステップ

* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](./data-lake-analytics-u-sql-get-started.md)
* [Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)