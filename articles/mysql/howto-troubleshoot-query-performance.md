---
title: Azure Database for MySQL でのクエリのパフォーマンスをトラブルシューティングする方法
description: この記事では、EXPLAIN を使って Azure Database for MySQL でのクエリのパフォーマンスをトラブルシューティングする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 819e2393619766d46385cdd6fe550fff1e1a7631
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537666"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>EXPLAIN を使用して Azure Database for MySQL でのクエリのパフォーマンスをプロファイリングする方法
**EXPLAIN** は、クエリを最適化するための便利なツールです。 EXPLAIN ステートメントを使うと、SQL ステートメントの実行状況に関する情報を取得できます。 EXPLAIN ステートメントを実行したときの出力の例を次に示します。

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

この例からわかるように、*key* の値が NULL になっています。 この出力は、MySQL がクエリ用に最適化されたインデックスを見つけることができず、フル テーブル スキャンを実行していることを意味します。 **ID** 列にインデックスを追加することで、このクエリを最適化してみます。

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

新しい EXPLAIN では、MySQL がインデックスを使って行数を 1 に制限するようになったため、検索時間が大幅に短縮されたことが示されています。
 
## <a name="covering-index"></a>カバリング インデックス
カバリング インデックスは、データ テーブルからの値の取得を減らすため、インデックスのクエリのすべての列で構成されます。 ここでは、次のような **GROUP BY** ステートメントについて説明します。
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

出力からわかるように、適切なインデックスを利用できないため、MySQL はインデックスを使っていません。 また、*Using temporary; Using file sort* と表示されていますが、これは MySQL が一時テーブルを作成して **GROUP BY** 句を満たしていることを意味します。
 
**c2** 列だけにインデックスを作成しても違いはなく、MySQL はまだ一時テーブルを作成する必要があります。

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

この場合、**c1** と **c2** の両方に対して **カバリング インデックス** を作成することができ、それによりインデックスに **c2** の値を直接追加してさらにデータの参照を減らすことができます。

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

上の EXPLAIN が示すように、MySQL はカバリング インデックスを使うことで、一時テーブルを作成しなくて済むようになっています。 

## <a name="combined-index"></a>結合インデックス
結合インデックスは、複数の列の値で構成され、インデックス付き列の連結値により並べ替えられた行の配列と見なすことができます。 この方法は、**GROUP BY** ステートメントで役に立ちます。

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL の "*ファイル並べ替え*" 操作の実行はかなり遅く、多くの行を並べ替える必要があるときは特にそうです。 このクエリを最適化するには、並べ替えられる両方の列に対して結合インデックスを作成します。

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

EXPLAIN では、MySQL が結合インデックスを使うようになり、インデックスが既に並べ替えられているので、さらに並べ替えを行う必要がないことが示されています。
 
## <a name="conclusion"></a>まとめ
 
EXPLAIN と異なる種類のインデックスを使うと、パフォーマンスを大幅に向上させることができます。 テーブルにインデックスを作成しただけでは、必ずしも MySQL がそれを使ってクエリを実行できるわけではありません。 常に、EXPLAIN を使って想定を検証し、インデックスを使ってクエリを最適化する必要があります。


## <a name="next-steps"></a>次の手順
- 最も気になる質問への回答を探したり、新しい質問/回答を投稿したりするには、[MSDN フォーラム](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL)または [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql) をご覧ください。
