---
title: Azure Database for PostgreSQL - Single Server で TOAST テーブル ストレージ戦略を使用してクエリ時間を最適化する
description: この記事では、Azure Database for PostgreSQL - Single Server で TOAST テーブル ストレージ戦略を使用して、クエリ時間を最適化する方法について説明します。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86116184"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>TOAST テーブル ストレージ戦略を使用してクエリ時間を最適化する 
この記事では、TOAST (The Oversized-Attribute Storage Technique) テーブル ストレージ戦略を使用して、クエリ時間を最適化する方法について説明します。

## <a name="toast-table-storage-strategies"></a>TOAST テーブル ストレージ戦略
TOAST を利用できるディスク上の列を格納するために、4 つの異なる戦略が使用されます。 これらは、圧縮と行外の格納のさまざまな組み合わせを表します。 データ型のレベルと列のレベルで戦略を設定できます。
- **Plain** では、圧縮や行外の格納が阻止されます。 varlena 型に対するシングル バイト ヘッダーの使用が無効になります。 Plain は、TOAST を利用できないデータ型の列に対して使用できる唯一の戦略です。
- **Extended** では、圧縮と行外の格納が許可されます。 Extended は、TOAST を使用できるほとんどのデータ型の既定値です。 最初に圧縮が試行されます。 それでも行が大きすぎる場合は、行外の格納が試行されます。
- **External** では、行外の格納は許可されますが、圧縮は許可されません。 External を使用すると、幅の広い text と bytea 列に対する部分文字列操作が高速化されます。 この高速化には、記憶域スペースが増加するという欠点があります。 これらの操作は、圧縮されていない行外の値から必要な部分のみをフェッチするように最適化されます。
- **Main** では、圧縮は許可されますが、行外の格納は許可されません。 このような列でも行外の格納は行われますが、最後の手段としてのみです。 これは、行を 1 ページに収まるくらい十分小さくする他の方法がない場合に行われます。

## <a name="use-toast-table-storage-strategies"></a>TOAST テーブル ストレージ戦略を使用する
クエリで TOAST を利用できるデータ型にアクセスする場合は、既定の Extended オプションではなく、Main 戦略を使用してクエリ時間を短縮することを検討します。 Main では、行外の格納は除外されません。 クエリで TOAST を使用できるデータ型にアクセスしない場合は、Extended オプションのままにするほうが有用である可能性があります。 メイン テーブルの行の大部分が共有バッファ キャッシュに収まるため、パフォーマンスが向上します。

幅の広いテーブルと文字数が多いスキーマを使うワークロードがある場合は、PostgreSQL TOAST テーブルの使用を検討します。 サンプルの顧客テーブルには 350 個を超える列があり、いくつかの列では文字数が 255 文字に及んでいました。 TOAST テーブルの Main 戦略に変換された後、ベンチマーク クエリ時間は 4,203 秒から 467 秒に短縮されました。 これは 89 パーセント向上したことになります。

## <a name="next-steps"></a>次のステップ
上記の特性を考慮して、ワークロードを見直します。 

次の PostgreSQL のドキュメントを確認します。 
- [Chapter 68, Database physical storage (第 68 章、データベースの物理ストレージ)](https://www.postgresql.org/docs/current/storage-toast.html) 