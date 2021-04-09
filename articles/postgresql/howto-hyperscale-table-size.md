---
title: テーブル サイズを確認する - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Hyperscale (Citus) サーバー グループで分散テーブルの実際のサイズを確認する方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937498"
---
# <a name="determine-table-and-relation-size"></a>テーブルとリレーションのサイズを確認する

PostgreSQL でテーブル サイズを確認するための通常の方法である `pg_total_relation_size` では、Hyperscale (Citus) での分散テーブルのサイズが極端に小さく報告されます。
この関数は、Hyperscale (Citus) サーバー グループでは、コーディネーター ノード上のテーブルのサイズを明らかにするだけです。  実際には、分散テーブル内のデータはコーディネーター ノードではなく、ワーカー ノード (シャード内) 上にあります。 分散テーブル サイズの実際の値は、シャード サイズの合計として取得されます。 Hyperscale (Citus) には、この情報に対してクエリを実行するためのヘルパー関数が用意されています。

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>機能</th>
<th>戻り値</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size(relation_name)</td>
<td><ul>
<li>テーブル ("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">メインのフォーク</a>") 内の実際のデータのサイズ。</li>
<li>リレーションにはテーブルまたはインデックスの名前を指定できます。</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size(relation_name)</td>
<td><ul>
<li><p>citus_relation_size に加えて次の情報:</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">空き領域マップ</a>のサイズ</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">可視性マップ</a>のサイズ</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size(relation_name)</td>
<td><ul>
<li><p>citus_table_size に加えて次の情報:</p>
<blockquote>
<ul>
<li>インデックスのサイズ</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

これらの関数は、標準の PostgreSQL [オブジェクト サイズ関数](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)のうちの 3 つに似ていますが、ノードに接続できない場合はエラーになる点が異なります。

## <a name="example"></a>例

すべての分散テーブルのサイズを一覧表示する方法を次に示します。

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

出力:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>次のステップ

* より多くのデータを保持するために[サーバー グループをスケーリングする](howto-hyperscale-scale-grow.md)方法を学習する。
* Hyperscale (Citus) サーバー グループで[テーブルの種類](concepts-hyperscale-nodes.md)を識別する。
