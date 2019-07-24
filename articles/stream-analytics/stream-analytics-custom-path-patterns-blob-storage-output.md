---
title: Azure Stream Analytics でのカスタム BLOB 出力のパーティション分割
description: この記事では、Azure Stream Analytics ジョブからの Blob Storage 出力のためのカスタム DateTime パス パターンおよびカスタム フィールドまたは属性機能について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9cdf99884845a9cb83ac26723c3ea0e7a779ebff
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982677"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics でのカスタム BLOB 出力のパーティション分割

Azure Stream Analytics は、カスタム フィールドまたは属性およびカスタム DateTime パス パターンを使用したカスタム BLOB 出力のパーティション分割をサポートしています。 

## <a name="custom-field-or-attributes"></a>カスタム フィールドまたは属性

カスタム フィールドまたは入力属性では、出力をよりきめ細かく制御できるようになるため、ダウンストリームのデータ処理およびレポート ワークフローが機能強化されます。

### <a name="partition-key-options"></a>パーティション キーのオプション

入力データをパーティション分割するために使用されるパーティション キー (または列名) には、ハイフン、アンダースコア、およびスペースを含む英数字を含めることができます。 入れ子になったフィールドは、別名と共に使用されない限り、パーティション キーとして使用できません。

### <a name="example"></a>例

あるジョブが、外部のビデオ ゲーム サービスに接続されたライブ ユーザー セッションから入力データを取得するとします。ここで、取り込まれたデータには、セッションを識別するための列 **client_id** が含まれます。 データを **client_id** でパーティション分割するには、ジョブの作成時に BLOB 出力プロパティ内にパーティション トークン **{client_id}** が含まれるように BLOB パス パターン フィールドを設定します。 さまざまな **client_id** 値を含むデータは Stream Analytics ジョブを通して流れるため、出力データは、フォルダーごとの 1 つの **client_id** 値に基づいて個別のフォルダーに保存されます。

![クライアント ID を含むパス パターン](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

同様に、ジョブ入力が、各センサーに **sensor_id** が割り当てられた数百万のセンサーからのセンサー データであった場合は、各センサー データを異なるフォルダーにパーティション分割するために、パス パターンは **{sensor_id}** になります。  


REST API を使用すると、その要求に使用される JSON ファイルの出力セクションは次のようになります。  

![REST API の出力](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

ジョブが実行を開始すると、*clients* コンテナーは次のようになります。  

![クライアントのコンテナー](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

各フォルダーには、各 BLOB に 1 つ以上のレコードが含まれる複数の BLOB を含めることができます。 上の例では、"06000000" のラベルが付けられたフォルダー内に、次のコンテンツを含む 1 つの BLOB が存在します。

![BLOB コンテンツ](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

出力パス内の出力をパーティション分割するために使用される列は **client_id** であったため、BLOB 内の各レコードにはフォルダー名に一致する **client_id** 列が含まれることに注意してください。

### <a name="limitations"></a>制限事項

1. パス パターンの BLOB 出力プロパティで許可されるカスタム パーティション キーは 1 つだけです。 次のすべてのパス パターンが有効です。

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData}  

2. パーティション キーは大文字と小文字が区別されないため、"John" や "john" などのパーティション キーは同等です。 また、式もパーティション キーとして使用できません。 たとえば、**{columnA + columnB}** は機能しません。  

3. 入力ストリームが 8000 未満のパーティション キーのカーディナリティを含むレコードで構成されている場合、レコードは既存の BLOB に追加され、新しい BLOB は必要な場合にのみ作成されます。 カーディナリティが 8000 を超える場合、既存の BLOB に書き込まれる保証はなく、同じパーティション キーを含む任意数のレコードに対して新しい BLOB は作成されません。  

## <a name="custom-datetime-path-patterns"></a>カスタム DateTime パス パターン

カスタム DateTime パス パターンを使用すると Hive Streaming 規則に合致する出力形式を指定できます。これにより、Azure Stream Analytics は、ダウンストリーム処理するためのデータを Azure HDInsight と Azure Databricks に送信できるようになります。 カスタム DateTime パス パターンは、BLOB 出力の [パス プレフィックス] フィールドで `datetime` キーワードと書式指定子を使用することで、簡単に実装されます。 たとえば、「 `{datetime:yyyy}` 」のように入力します。

### <a name="supported-tokens"></a>サポートされているトークン

次の書式指定子トークンを単独でまたは組み合わせて使用して、カスタム DateTime 形式を指定できます。

|書式指定子   |説明   |サンプル時間 2018-01-02T10:06:08 に対する結果|
|----------|-----------|------------|
|{datetime:yyyy}|4 桁の数値としての年|2018|
|{datetime:MM}|月 (01 ～ 12)|01|
|{datetime:M}|月 (1 ～ 12)|1|
|{datetime:dd}|日 (01 ～ 31)|02|
|{datetime:d}|日 (1 ～ 12)|2|
|{datetime:HH}|24 時間表記での時 (00 ～ 23)|10|
|{datetime:mm}|分 (00 ～ 24)|06|
|{datetime:m}|分 (0 ～ 24)|6|
|{datetime:ss}|秒 (00 ～ 60)|08|

カスタム DateTime パターンを使用しない場合は、パス プレフィックスに {date} トークンと {time} トークンのどちらかまたは両方を追加して、組み込みの DateTime 形式を表示するドロップダウン リストを生成できます。

![Stream Analytics の 古い DateTime 形式](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>拡張性と制限事項

プレフィックス文字数の制限に達するまで、任意の数のトークン `{datetime:<specifier>}` をパス パターン内で使用できます。 日時ドロップダウンに既に一覧されている組み合わせ以外の書式指定子を 1 つのトークンの中で組み合わせて使用することはできません。 

`logs/MM/dd` のパス パーティションの場合:

|有効な式   |無効な式   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

パス プレフィックス内で、同じ書式指定子を複数回使用できます。 トークンは毎回繰り返す必要があります。

### <a name="hive-streaming-conventions"></a>Hive Streaming 規則

BLOB ストレージ用のカスタム パス パターンを Hive Streaming 規則と共に使用して、フォルダー名に `column=` によるラベルが付けられることを想定できます。

たとえば、「 `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}` 」のように入力します。

カスタム出力は、Azure Stream Analytics と Hive 間でデータを移植するためのテーブルの変更と手動でのパーティションの追加という煩わしい操作を排除します。 代わりに、次を使用して、多数のフォルダーを自動的に追加できます。

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>例

[Azure Stream Analytics Portal](stream-analytics-quick-create-portal.md) クイック スタート ガイドに従って、ストレージ アカウント、リソース グループ、Stream Analytics ジョブ、および入力ソースを作成します。 クイック スタート ガイドで使用されているサンプル データを使用します。[GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) から入手することもできます。

次の構成の BLOB 出力シンクを作成します。

![Stream Analytics の BLOB 出力シンクの作成](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

完全なパス パターンは次のようになります。


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


ジョブを開始すると、パス パターンに基づくフォルダー構造が BLOB コンテナー内に作成されます。 日レベルまでドリルダウンできます。

![カスタム パス パターンでの Stream Analytics の BLOB 出力](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics からの出力を理解する](stream-analytics-define-outputs.md)
