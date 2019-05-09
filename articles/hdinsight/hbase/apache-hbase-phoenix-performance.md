---
title: Azure HDInsight における Phoenix のパフォーマンス
description: Phoenix のパフォーマンスを最適化するためのベスト プラクティス。
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: da227151dd056dd5e852ae8790b6f20ac3c0c790
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653307"
---
# <a name="apache-phoenix-performance-best-practices"></a>Apache Phoenix のパフォーマンスに関するベスト プラクティス

[Apache Phoenix](https://phoenix.apache.org/) のパフォーマンスにとって最も重要な側面は、その基になる [Apache HBase](https://hbase.apache.org/) を最適化することです。 Phoenix では、HBase に基づくリレーショナル データ モデルを作成し、SQL クエリをスキャンなどの HBase の操作に変換します。 テーブル スキーマの設計、主キーのフィールドの選択および順序、インデックスの使用のいずれも、Phoenix のパフォーマンスに影響を及ぼします。

## <a name="table-schema-design"></a>テーブル スキーマの設計

Phoenix でテーブルを作成すると、そのテーブルは HBase テーブルに格納されます。 HBase テーブルには、同時にアクセスされる列のグループ (列ファミリ) が含まれています。 Phoenix テーブルの行は HBase テーブルの行に対応しています。その各行は、1 つ以上の行に関連付けられた、バージョン管理されているセルで構成されています。 HBase テーブルの各行は、論理的には、同じ rowkey の値を備えたキーと値のペアのコレクションとなっています。 つまり、それぞれのキーと値のペアが rowkey 属性を備えており、その rowkey 属性の値は特定の行と同じです。

Phoenix テーブルのスキーマの設計には、主キーの設計、列ファミリの設計、個々の列の設計、データのパーティション分割方法が含まれます。

### <a name="primary-key-design"></a>主キーの設計

Phoenix のテーブルで定義された主キーによって、基になる HBase テーブルの rowkey へのデータの格納方法が決まります。 HBase で特定の行にアクセスする唯一の方法は、rowkey を使用することです。 また、HBase テーブルに格納されたデータは、rowkey によって並べ替えられます。 Phoenix は、主キーで定義された順序で行内の各列の値を連結して、rowkey 値を構築します。

たとえば、連絡先のテーブルに、同じ列ファミリに属する名、姓、電話番号、住所が含まれているとします。 増分するシーケンス番号に基づいて主キーを定義できます。

|rowkey|       address|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1,000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

ただし、頻繁に lastName でクエリを実行する場合、この主キーはあまり役に立たない可能性があります。各クエリで、すべての lastName の値を読み取るためのフル テーブル スキャンが必要になるためです。 代わりに、lastName、firstName、社会保障番号の列で主キーを定義できます。 この最後の列によって、父と息子などの、住所と名前が同じ 2 人を区別できます。

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1,000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

この新しい主キーを使用して、Phoenix は次のような行キーを生成します。

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

上記の最初の行では、rowkey のデータは次のように表されます。

|rowkey|       key|   value| 
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

これで、この rowkey に、データの重複コピーが格納されるようになります。 この値は、基になる HBase テーブルのすべてのセルに含まれるので、主キーに含める列のサイズと数はよく検討してください。

また、主キーに単調に増加する値が含まれている場合は、"*ソルト バケット*" を使用してテーブルを作成し、書き込みのホットスポットが発生しないようにする必要があります。「[データのパーティション分割](#partition-data)」を参照してください。

### <a name="column-family-design"></a>列ファミリの設計

一部の列が他の列よりも頻繁にアクセスされる場合、複数の列ファミリを作成して、アクセス頻度の低い列からアクセス頻度の高い列を分離する必要があります。

また、同時にアクセスされる傾向がある特定の列は、同じ列ファミリにしてください。

### <a name="column-design"></a>列の設計

* サイズの大きい列では I/O コストも増加するので、VARCHAR 列は約 1 MB 未満で保持します。 クエリを処理する際は、HBase はセルを完全に具体化してからクライアントに送信し、クライアントはそれらを完全に受信してからアプリケーション コードに渡します。
* 列の値は、protobuf、Avro、msgpack、BSON などのコンパクトな形式で格納します。 JSON は、サイズが大きいため推奨されません。
* 格納する前に、待機時間と I/O コストを削減するためにデータを圧縮することを検討してください。

### <a name="partition-data"></a>データのパーティション分割

Phoenix では、データを配布するリージョンの数を制御して、読み取り/書き込みのパフォーマンスを大幅に向上させることができます。 Phoenix テーブルを作成するときは、ソルト処理を行えるほか、事前にデータを分割できます。

作成中のテーブルに対してソルト処理を行う場合は、ソルト バケットの番号を指定します。

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

このソルト処理により、主キーの値に沿って値が自動的に選択され、テーブルが分割されます。 

特定の場所でテーブルを分割するには、分割を発生させる範囲の値を指定して、事前にテーブルを分割します。 たとえば、3 つのリージョンでテーブルを分割するには、次のように処理します。

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>インデックスの設計

Phoenix インデックスは、インデックス付きテーブルのデータの一部またはすべてのコピーを格納する HBase テーブルです。 インデックスを付けることで、特定の種類のクエリのパフォーマンスが向上します。

複数のインデックスを定義してあるテーブルに対してクエリを実行すると、Phoenix はクエリに最適なインデックスを自動的に選択します。 プライマリ インデックスは、選択した主キーに基づいて自動的に作成されます。

クエリが予想できる場合は、列を指定してセカンダリ インデックスを作成することもできます。

インデックスを設計するときは、次のことに注意してください。

* 必要なインデックスだけを作成します。
* 頻繁に更新されるテーブルのインデックスの数を制限します。 テーブルへの更新は、メインのテーブルとインデックス テーブルの両方への書き込みに変換されます。

## <a name="create-secondary-indexes"></a>セカンダリ インデックスの作成

セカンダリ インデックスがあれば、フル テーブル スキャンをせずにポイント参照で済むようになり、記憶域スペースおよび書き込み速度を代償に、読み取りのパフォーマンスを向上させることができます。 セカンダリ インデックスは、テーブル作成後に追加または削除できます。既存のクエリの変更は不要です。単純に、クエリの実行速度が向上します。 必要に応じて、カバリング インデックスか関数インデックス、またはその両方の作成を検討してください。

### <a name="use-covered-indexes"></a>カバリング インデックスの使用

カバリング インデックスとは、インデックス付けされた値だけでなく、行のデータも含むインデックスです。 目的のインデックス エントリが見つかったら、もうプライマリ テーブルにアクセスする必要はありません。

たとえば、例に挙げた連絡先のテーブルでは、socialSecurityNum 列だけでのセカンダリ インデックスを作成できます。 このセカンダリ インデックスにより、socialSecurityNum 値でフィルター処理を行うクエリが高速化します。ただし、他のフィールドの値を取得するには、メイン テーブルに対して別の読み取りを実施する必要があります。

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

ただし、socialSecurityNum を利用して firstName と lastName を検索したい場合は、インデックス テーブルに実際のデータとして firstName と lastName が含まれるカバリング インデックスを作成できます。

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

このカバリング インデックスにより、次のクエリを使用してセカンダリ インデックスを含むテーブルから読み取りを行うだけで、すべてのデータを取得できるようになります。

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>関数インデックスの使用

関数インデックスでは、クエリでの使用が想定される任意の式のインデックスを作成できます。 関数インデックスを作成して配置し、クエリでその式を使用すると、インデックスを使用してデータ テーブルではなく結果を取得できます。

たとえば、ある人の結合された名と姓に対し、インデックスを作成して大文字と小文字を区別しない検索を実行できます。

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>クエリの設計

クエリの設計時の主な考慮事項は次のとおりです。

* クエリ プランを把握し、想定される動作を確認します。
* 効率的に結合します。

### <a name="understand-the-query-plan"></a>クエリ プランの把握

[SQLLine](http://sqlline.sourceforge.net/) では、SQL クエリの前に EXPLAIN を使用すると、Phoenix が実行する操作のプランが表示されます。 プランについて、次のことを確認してください。

* 主キーが適切なタイミングで使用されている。
* データ テーブルではなく、適切なセカンダリ インデックスが使用されている。
* TABLE SCAN ではなく、RANGE SCAN または SKIP SCAN が可能な限り使用されている。

#### <a name="plan-examples"></a>プランの例

たとえば、フライトの遅延情報を格納する FLIGHTS という名前のテーブルがあるとします。

airlineid が主キーにもインデックスにも含まれないフィールドであるとき、airlineid が `19805` であるすべてのフライトを選択する場合は、次のようになります。

    select * from "FLIGHTS" where airlineid = '19805';

次のように explain コマンドを実行します。

    explain select * from "FLIGHTS" where airlineid = '19805';

クエリ プランは次のようになります。

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

このプランでは、FULL SCAN OVER FLIGHTS というフレーズに注意してください。 このフレーズは、このクエリを実行すると、より効率的な RANGE SCAN または SKIP SCAN のオプションを使用せずに、テーブルのすべての行に対して TABLE SCAN が実行されることを示しています。

2014 年 1 月 2 日の、キャリアが `AA`、flightnum が 1 以上のフライトを照会するとします。 year、month、dayofmonth、carrier、flightnum 列が例のテーブル内に存在し、これらすべてが複合主キーの一部であると仮定します。 クエリは次のようになります。

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

このクエリに対するプランを次のように検証します。

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

結果のプランは次のとおりです。

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

角かっこ内の値は、主キーの値の範囲です。 この場合、範囲の値は year が 2014、month が 1、dayofmonth が 2 で固定されていますが、flightnum の値としては 2 以上 (`*` まで) の範囲が認められています。 このクエリ プランにより、主キーが期待どおりに使用されていることを確認できます。

次に、`carrier2_idx` という名前で、FLIGHTS テーブルに carrier フィールドのみのインデックスを作成します。 このインデックスには、カバリングされた (データがインデックス内にも格納された) 列として、flightdate、tailnum、origin、flightnum も含まれています。

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

たとえば、次のクエリのように、flightdate および tailnum と一緒にキャリアも取得する場合を考えてみます。

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

インデックスは次のように使用されます。

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

explain で表示されるプランの結果の項目に関する詳細な一覧については、[Apache Phoenix チューニング ガイド](https://phoenix.apache.org/tuning_guide.html)の「Explain Plans (Explain プラン)」セクションを参照してください。

### <a name="join-efficiently"></a>効率的な結合

一般的に、片方が小規模でない限り、結合は回避します。特に、頻度の高いクエリではそうする必要があります。

必要な場合は、ヒント `/*+ USE_SORT_MERGE_JOIN */` を使用して大規模な結合を行うことができますが、大規模な結合では行数が膨大になり、コストが大きくなります。 右辺の全テーブルの全体的なサイズが、使用可能なメモリを超過する場合に、ヒント `/*+ NO_STAR_JOIN */` を使用してください。

## <a name="scenarios"></a>シナリオ

次のガイドラインでは、いくつかの一般的なパターンについて説明します。

### <a name="read-heavy-workloads"></a>読み取り負荷の高いワークロード

読み取り負荷の高いユース ケースでは、必ずインデックスを使用します。 また、読み取り時間のオーバーヘッドを削減するために、カバリング インデックスの使用を検討してください。

### <a name="write-heavy-workloads"></a>書き込み負荷の高いワークロード

書き込み負荷が高く、主キーが単調に増加するワークロードでは、ソルト バケットを作成します。これにより、必要な追加のスキャンによって全体的な読み取りスループットを低下させる代わりに、書き込みのホットスポットの発生を回避します。 また、UPSERT を使用して大量のレコードを書き込むときは、autoCommit をオフにしてレコードをバッチ処理します。

### <a name="bulk-deletes"></a>一括削除

大きなデータ セットを削除するときは、DELETE クエリを発行する前に autoCommit をオンにして、クライアントがすべての削除行の行キーを保存しなくても済むようにします。 autoCommit によって、DELETE で影響を受ける行をクライアントがバッファー処理することはなくなり、Phoenix はそれらをクライアントに返さずにリージョン サーバー上で直接削除できるようになります。

### <a name="immutable-and-append-only"></a>不変かつ追加専用

データ整合性よりも書き込み速度を優先するシナリオの場合は、テーブルの作成時に先書きログを無効にすることを検討してください。

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

これらのオプションの詳細については、[Apache Phoenix の文法](https://phoenix.apache.org/language/index.html#options)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Apache Phoenix のチューニング ガイド](https://phoenix.apache.org/tuning_guide.html)
* [セカンダリ インデックス](https://phoenix.apache.org/secondary_indexing.html)
