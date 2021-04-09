---
title: Azure HDInsight Hive テーブル内のデータのサンプリング - Team Data Science Process
description: Hive クエリを使用して Azure HDInsight Hive テーブルに格納されているデータをダウンサンプリングし、データを分析で管理しやすいサイズに削減します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321882"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Azure HDInsight Hive テーブル内のデータのサンプリング
この記事では、Hive クエリを使用して Azure HDInsight Hive テーブルに格納されているデータをダウンサンプリングして、分析で管理しやすいサイズに削減する方法について説明します。 一般的に使用されている 3 つのサンプリング方法について説明します。

* 一様ランダム サンプリング
* グループごとのランダム サンプリング
* 階層サンプリング

**データをサンプリングする理由**
分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 ダウンサンプリングすると、データの理解、探索、および特徴エンジニアリングが容易になります。 Team Data Science Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](./index.yml)の 1 ステップです。

## <a name="how-to-submit-hive-queries"></a>Hive クエリを送信する方法
Hive クエリは、Hadoop クラスターのヘッド ノード上の Hadoop コマンド ライン コンソールから送信できます。  Hadoop クラスターのヘッド ノードにログインし、Hadoop コマンド ライン コンソールを開き、そこから Hive クエリを送信します。 Hadoop コマンド ライン コンソールで Hive クエリを送信する手順については、「[Hive クエリを送信する方法](move-hive-tables.md#submit)」をご覧ください。

## <a name="uniform-random-sampling"></a><a name="uniform"></a> 一様ランダム サンプリング
一様ランダム サンプリングとは、データ セットの各行にサンプリングされる機会が均等にあるという意味です。 これは、追加のフィールド rand() を、内部の "select" クエリのデータセットに追加し、そのランダム フィールドについての条件を外部の "select" クエリに追加することで実装できます。

クエリの使用例を次に示します。

```python
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, …, fieldN
from
    (
    select
        field1, field2, …, fieldN, rand() as samplekey
    from <hive table name>
    )a
where samplekey<='${hiveconf:sampleRate}'
```

ここで、 `<sample rate, 0-1>` は、ユーザーがサンプリングするレコードの割合を指定しています。

## <a name="random-sampling-by-groups"></a><a name="group"></a> グループごとのランダム サンプリング
カテゴリ別のデータをサンプリングする場合、カテゴリ変数の一部が何らかの値であるすべてのインスタンスを含めるか除外することができます。 この種のサンプリングは、"グループごとのサンプリング" と呼ばれます。 たとえば、NY、MA、CA、NJ、PA などの値を持つカテゴリ変数 "*State*" がある場合に、サンプリングされているかどうかにかかわらず、各州のレコードをまとめることができます。

グループごとのサンプリングを実行するサンプルのクエリは次のとおりです。

```python
SET sampleRate=<sample rate, 0-1>;
select
    b.field1, b.field2, …, b.catfield, …, b.fieldN
from
    (
    select
        field1, field2, …, catfield, …, fieldN
    from <table name>
    )b
join
    (
    select
        catfield
    from
        (
        select
            catfield, rand() as samplekey
        from <table name>
        group by catfield
        )a
    where samplekey<='${hiveconf:sampleRate}'
    )c
on b.catfield=c.catfield
```

## <a name="stratified-sampling"></a><a name="stratified"></a>階層サンプリング
取得したサンプルに、母集団と同じ比率のカテゴリ別のサンプル値がある場合、ランダム サンプリングは、カテゴリ変数に関して階層化されます。 上記と同じ例で、州ごとに次の件数のデータがあるとします。NJ には 100 件のデータ、NY には 60 件のデータ、WA には 300 件のデータ。 階層のサンプリングの割合を 0.5 に指定すると、取得されるサンプルは NJ、NY、WA それぞれで 50 件、30 件、150 件となります。

クエリの使用例を次に示します。

```hiveql
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, field3, ..., fieldN, state
from
    (
    select
        field1, field2, field3, ..., fieldN, state,
        count(*) over (partition by state) as state_cnt,
          rank() over (partition by state order by rand()) as state_rank
      from <table name>
    ) a
where state_rank <= state_cnt*'${hiveconf:sampleRate}'
```

Hive で使用できるより高度なサンプリング方法については、「 [LanguageManual Sampling (LanguageManual のサンプリング)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)」をご覧ください。