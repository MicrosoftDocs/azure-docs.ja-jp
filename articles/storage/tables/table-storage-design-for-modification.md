---
title: データの変更に対応した Azure Table Storage を設計する | Microsoft Docs
description: Azure Table Storage で、データの変更に対応したテーブルを設計します。 挿入、更新、削除操作を最適化します。 格納されたエンティティの一貫性を確保します。
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236762"
---
# <a name="design-for-data-modification"></a>データの変更に対応した設計
この記事では、挿入、更新、削除の操作を最適化するための設計上の考慮事項を示します。 場合によっては、リレーショナル データベースの設計と同様に、クエリ向けの最適化とデータ変更向けの最適化のトレードオフを評価する必要があります (設計のトレードオフを管理する手法はリレーショナル データベースでは異なります)。 「テーブルの設計パターン」セクションでは、Table service の詳細な設計パターンをいくつか説明し、これらのトレードオフに注目します。 実際のところ、クエリ向けに最適化された設計の多くは、エンティティの変更にも適していることがおわかりになると思います。  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>挿入、更新、削除の操作のパフォーマンスを最適化する
エンティティを更新または削除するには、**PartitionKey** と **RowKey** 値を使用してエンティティを識別する必要があります。 この点で、エンティティの変更のために選択した **PartitionKey** と **RowKey** は、できるだけ効率的にエンティティを識別するため、ポイント クエリをサポートするために選択したものと同様の条件に従う必要があります。 **PartitionKey** と **RowKey** 値の検出のためにエンティティを特定する非効率的なパーティションまたはテーブル スキャンを使用したくない場合は、更新または削除する必要があります。  

「テーブルの設計パターン」セクションの以下のパターンは、パフォーマンスの最適化または挿入、更新、削除操作に対応しています。  

* [大量削除パターン](table-storage-design-patterns.md#high-volume-delete-pattern) -すべてのエンティティを同時削除用に独立したテーブルに格納することで、大量のエンティティを削除できるようにします。  
* [データ系列のパターン](table-storage-design-patterns.md#data-series-pattern) -単一のエンティティで完全なデータ系列を格納し、リクエストの作成を最小限に抑えます。  
* [ワイド エンティティ パターン](table-storage-design-patterns.md#wide-entities-pattern) -複数の物理エンティティを使用し、252 個以上のプロパティを持つ論理エンティティを格納します。  
* [大型エンティティ パターン](table-storage-design-patterns.md#large-entities-pattern) -大規模なプロパティの値を格納する BLOB ストレージの使用します。  

## <a name="ensure-consistency-in-your-stored-entities"></a>格納されたエンティティの一貫性を確保する
データの変更を最適化するためのキーの選択を左右する要因として、アトミックなトランザクションを使って一貫性を確保する方法も挙げられます。 同じパーティションに格納されたエンティティを操作する場合は、EGT しか使用できません。  

記事「[Table design patterns](table-storage-design-patterns.md)」(テーブル設計パターン) の以下のパターンは、一貫性の管理に対応しています。  

* [パーティション内のセカンダリ インデックス パターン](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -異なる **RowKey** 値 (同じパーティション) を使用して各エンティティの複数のコピーを格納し、異なる **RowKey** 値を使用して効果的な検索と代替的な並べ替え順序を可能にします。  
* [パーティション内のセカンダリ インデックス パターン](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 別個のパーティションまたは別個のテーブルの異なる RowKey 値を使用して各エンティティの複数のコピーを格納し、異なる **RowKey** 値を使用して高速で効率的な検索と代替的な並べ替え順序を可能にします。  
* [最終的に一貫性のあるトランザクション パターン](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -Azure キューを使用してパーティションの境界または記憶域システムの境界にまたがる一貫した動作を最終的に有効にします。
* [インデックス エンティティのパターン](table-storage-design-patterns.md#index-entities-pattern) - エンティティ一の一覧を返す効率的な検索ができるように、インデックス エンティティを管理します。  
* [非正規化パターン](table-storage-design-patterns.md#denormalization-pattern) -関連するデータを単一のエンティティに結合し、必要なすべてのデータを1 つのポイント クエリで取得することを可能にします。  
* [データ系列のパターン](table-storage-design-patterns.md#data-series-pattern) -単一のエンティティで完全なデータ系列を格納し、リクエストの作成を最小限に抑えます。  

エンティティ グループ トランザクションについて詳しくは、「[Entity Group Transactions](table-storage-design.md#entity-group-transactions)」(エンティティ グループ トランザクション) セクションをご覧ください。  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>効率的な変更に対応した設計によるクエリの効率化
効率的なクエリに適した設計は変更の効率も高いのが普通ですが、自分のシナリオにもそれが当てはまるかどうかは必ず評価する必要があります。 「[Table Design Patterns](table-storage-design-patterns.md)」(テーブル設計パターン) のパターンの中には、エンティティをクエリして変更する間に明示的にトレードオフを評価するものがあります。常に各操作数を考慮しておく必要があります。  

「[Table Design Patterns](table-storage-design-patterns.md)」(テーブル設計パターン) の以下のパターンは、効率的なクエリの設計と効率的なデータ変更の設計の間のトレードオフに対応しています。  

* [複合キー パターン](table-storage-design-patterns.md#compound-key-pattern) -複合 **RowKey** 値を使用して、1 つのポイント クエリに関連するデータを検索するクライアントを有効にします。  
* [ログ テール パターン](table-storage-design-patterns.md#log-tail-pattern) - 逆の日付と時間順で並べ替える *RowKey* 値を使用して、パーティションに最も新しく追加されたエンティティ **n** を取得します。  
