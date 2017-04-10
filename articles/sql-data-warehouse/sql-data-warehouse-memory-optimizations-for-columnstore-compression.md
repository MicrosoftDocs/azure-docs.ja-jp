---
title: "Azure SQL で列ストア インデックスのパフォーマンスを上げる | Microsoft Docs"
description: "メモリ要件を減らすか、使用可能なメモリを増やして列ストア インデックスが各行グループに圧縮する行の数を最大限にします。"
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/18/2016
ms.author: shigu;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8d189256ed4c876859203406cda95ce0be36c96c
ms.lasthandoff: 03/29/2017


---

# <a name="memory-optimizations-for-columnstore-compression"></a>列ストア圧縮向けのメモリ最適化

メモリ要件を減らすか、使用可能なメモリを増やして列ストア インデックスが各行グループに圧縮する行の数を最大限にします。  これらのメソッドを使用して、列ストア インデックスの圧縮率およびクエリ パフォーマンスを向上させます。

## <a name="why-the-rowgroup-size-matters"></a>行グループのサイズが重要な理由
列ストアインデックスは個別の行グループの列セグメントをスキャンすることでテーブルをスキャンし、各行グループの行の数を最大限にしてクエリ パフォーマンスを向上させます。 行グループに多くの行がある場合、データ圧縮が向上します。つまり、ディスクから読み取るデータが少なくなります。

行グループの詳細については、「[列ストア インデックス ガイド](https://msdn.microsoft.com/library/gg492088.aspx)」を参照してください。

## <a name="target-size-for-rowgroups"></a>行グループのターゲット サイズ
最高のクエリ パフォーマンスを引き出すために、目標は列ストアインデックスの行グループごとの行の数を最大限にすることです。 行グループには、最大で 1,048,576 行を含められます。 行グループごとの行数が最大数ではなくても問題はありません。 列ストア インデックスは、行グループに 100,000 以上の行が含まれると、良好なパフォーマンスを実現します。

## <a name="rowgroups-can-get-trimmed-during-compression"></a>圧縮時の行グループのトリミング

一括読み込み中または列ストアインデックスの再構築中、各行グループに指定された行をすべて圧縮するのに使用可能なメモリが十分ではないことがあります。 メモリの負荷がある場合、列ストアへの圧縮ができるように、列ストア インデックスは行グループのサイズをトリミングします。

それぞれの行グループに 10,000 行以上を圧縮する十分なメモリがない場合、SQL Data Warehouse はエラーを生成します。

一括読み込みの詳細については、「[クラスター化列ストア インデックスへの一括読み込み](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index)」セクションを参照してください。

## <a name="how-to-estimate-memory-requirements"></a>メモリ要件の見積もり方法

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

1 つの行グループを圧縮するために必要なメモリは最大で約

- 72 MB 以上
- \#行\* \#列\* 8 バイト以上
- \#行 \* \#短い文字列の列 \* 32 バイト +
- \#長い文字列の列 \* 圧縮ディクショナリに 16 MB

短い文字列の列は 32 バイト以下の文字列データを使用し、長い文字列の列は 32 バイト超の文字列データを使用します。

長い文字列は、テキストの圧縮に指定されている圧縮方法で圧縮されます。 この圧縮方法では、*ディクショナリ*を使用してテキスト パターンを格納します。 ディクショナリの最大サイズは 16 MB です。 ディクショナリは、行グループ内の長い文字列の列ごとに 1 つだけです。

列ストアのメモリ要件の詳細については、ビデオ「[Azure SQL Data Warehouse scaling: configuration and guidance (Azure SQL Data Warehouse のスケーリング: 構成とガイダンス)](https://myignite.microsoft.com/videos/14822)」をご覧ください。

## <a name="ways-to-reduce-memory-requirements"></a>メモリ要件を軽減する方法

次の方法を使用して、行グループを列ストア インデックスに圧縮するためのメモリ要件を軽減します。

### <a name="use-fewer-columns"></a>より少ない列の使用
可能であれば、より少ない列を含むテーブルを設計します。 行グループが列ストアに圧縮されると、列ストア インデックスは各列セグメントを個別に圧縮します。 したがって、行グループを圧縮するためのメモリ要件は、列数が増えると増加します。


### <a name="use-fewer-string-columns"></a>より少ない文字列の列の使用
文字列データ型の列には、数値および日付のデータ型より多くのメモリが必要です。 メモリ要件を減らすため、ファクト テーブルから文字列の列を削除して、これらをよりディメンションの小さなテーブルに格納することを検討してください。

文字列圧縮の追加のメモリ要件:

- 32 文字以内の文字列データ型には、値ごとに追加で 32 バイトが必要になる可能性があります。
- 32 文字超の文字列データ型は、ディクショナリ メソッドを使用して圧縮されます。  行グループ内の各列には、ディクショナリを構築するため、最大で 16 MB のメモリが追加で必要になる可能性があります。

### <a name="avoid-over-partitioning"></a>過剰なパーティション分割の回避

列ストア インデックスは、パーティションごとに 1 つまたは複数の行グループを作成します。 SQL Data Warehouse では、データが分散されており、それぞれのディストリビューションがパーティション分割されているため、パーティションの数が急速に増加します。 テーブルに多数のパーティションがある場合は、行グループを入力するのに十分な行がない可能性があります。 行の欠如によって、圧縮時にメモリの負荷が発生することはありませんが、行グループの最適な列ストア クエリ パフォーマンスが実現しなくなります。

過剰なパーティション分割を回避する別の理由として、パーティション テーブルには、列ストア インデックスに行を読み込むためのメモリ オーバーヘッドがあります。 読み込み中、各パーティションに圧縮するのに十分な行が格納されるまでメモリで保持されていた行を、多数のパーティションが受信する可能性があります。 パーティションが多すぎると、追加のメモリ負荷が発生します。

### <a name="simplify-the-load-query"></a>クエリの読み込みの簡素化

データベースは、クエリ内のすべての演算子間でクエリのメモリ許可を共有します。 読み込みクエリに複雑な並べ替えや結合がある場合は、圧縮に使用可能なメモリが少なくなります。

クエリの読み込みにのみ重点を置くように、読み込みクエリを設計します。 データに対して変換を実行する必要がある場合は、変換を読み込みクエリとは別に実行します。 たとえば、ヒープ テーブル内のデータをステージングし、変換を実行して、その後列ストア インデックスにステージング テーブルを読み込みます。 最初にデータを読み込んでから、MPP システムを使用してデータを変換することもできます。

### <a name="adjust-maxdop"></a>MAXDOP の調整

各ディストリビューションで、ディストリビューションごとに複数の CPU コアが使用可能な場合、行グループが並列で列ストアに圧縮されます。 並列処理には追加のメモリ リソースが必要となり、これによってメモリ負荷と行グループのトリミングが発生することがあります。

メモリ負荷を減らすために、MAXDOP クエリ ヒントを使用して、各ディストリビューション内において読み込み操作をシリアル モードで強制的に実行できます。

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>より多くのメモリを割り当てる方法

DWU のサイズとユーザー リソースのクラスによって、ユーザー クエリで使用可能なメモリの量が決まります。 読み込みクエリのメモリ許可を増やすために、DWU の数を増やすか、リソース クラスを増やすことができます。

- DWU を増やす方法については、[パフォーマンスのスケーリング方法](sql-data-warehouse-manage-compute-overview.md#scale-compute)に関するセクションを参照してください。
- クエリのリソース クラスを変更する方法については、「[ユーザー リソース クラスの変更例](sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example)」を参照してください。

たとえば、DWU 100 の場合、smallrc リソース クラス内のユーザーはディストリビューションごとに 100 MB のメモリを使用できます。 詳細については、[SQL Data Warehouse での同時実行](sql-data-warehouse-develop-concurrency.md)に関する記事を参照してください。

高品質の行グループのサイズを取得するには 700 MB のメモリが必要と判断したと仮定します。 これらの例では、十分なメモリで読み込みクエリを実行する方法を示します。

- DWU 1000 と mediumrc を使用すると、メモリ許可は 800 MB です。
- DWU 600 と largerc を使用すると、メモリ許可は 800 MB です。


## <a name="next-steps"></a>次のステップ

SQL Data Warehouse でのパフォーマンスを向上させるその他の方法については、[パフォーマンスの概要](sql-data-warehouse-overview-manage-user-queries.md)に関する記事を参照してください。

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

