---
title: クエリに対応した Azure Table Storage の設計 | Microsoft Docs
description: Azure Table Storage で、クエリに対応したテーブルを設計します。
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 97373f6f0138d3ed8028ed4327b7e6cf90ad76a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325869"
---
# <a name="design-for-querying"></a>クエリに対応した設計
Table service ソリューションでは、読み取り、書き込み、またはその両方の負荷が高くなることがあります。 この記事では、読み取り操作を効率的に行える Table service を設計する際に注意する必要のある事柄を中心に取り上げます。 通常は、読み取り操作を効率的にサポートする設計は、書き込み操作についても効率が高くなります。 ただし、書き込み操作をサポートする設計には、さらに考慮すべき事柄があります。「[データの変更に対応した設計](table-storage-design-for-modification.md)」の記事を参照してください。

データを効率的に読み取ることのできる Table service ソリューションを設計する際には、まず "必要なデータを Table service から取得するには、アプリケーションでどのようなクエリを実行する必要があるか" を考えてみてください。  

> [!NOTE]
> Table service を使用する場合は、事前に正しく設計することが重要です。後で設計を変更するのは難しいだけでなく、コストも高くなるためです。 たとえば、リレーショナル データベースなら、既存のデータベースにインデックスを追加するだけでパフォーマンスの問題に対処できるのが普通ですが、Table service ではそうはいきません。  
> 
> 

このセクションでは、クエリに対応したテーブルを設計する際に対処する必要のある主要な問題を中心に取り上げます。 このセクションで取り上げるトピックは次のとおりです。

* [選択した PartitionKey と RowKey がクエリのパフォーマンスに与える影響](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [適切な PartitionKey の選択](#choosing-an-appropriate-partitionkey)
* [クエリを Table service 向けに最適化する](#optimizing-queries-for-the-table-service)
* [Table service でデータを並べ替える](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>選択した PartitionKey と RowKey がクエリのパフォーマンスに与える影響
次の例は、以下の構造を持った複数の従業員エンティティの格納を想定しています (明確にするためほとんどの例で **タイムスタンプ** プロパティを省略)。  

| *列名* | *データの種類* |
| --- | --- |
| **PartitionKey** (部門名) |string |
| **RowKey** (従業員 ID) |string |
| **FirstName** |string |
| **LastName** |string |
| **Age** |整数 |
| **EmailAddress** |string |

「[Azure Table Storage の概要](table-storage-overview.md)」の記事では、クエリの設計に直接影響を与える Azure Table service の主な機能の一部について説明します。 ここから、Table service のクエリを設計する際には、次のような一般的なガイドラインが考えられます。 以下の例で使用しているフィルター構文は、Table service REST API の構文です。詳細については、「[Query Entities (エンティティの照会)](https://docs.microsoft.com/rest/api/storageservices/Query-Entities)」をご覧ください。  

* ***ポイント クエリ***は、最も効率的な検索です。大量の参照または短い待機時間が求められる参照に使用することをお勧めします。 このようなクエリでは、**PartitionKey** と **RowKey** 値の両方を指定することでインデックスを使用し、個別のエンティティを非常に効率よく検索することができます。 例: $filter (PartitionKey eq 'Sales') = および (RowKey eq '2')  
* 2 番目に良い方法は、**PartitionKey** を使用する***範囲クエリ***と、**RowKey 値**の範囲にフィルターをかけ、1 つ以上のエンティティを返す です。 **PartitionKey** 値は特定のパーティションを識別し、**RowKey** 値はそのパーティション内のエンティティのサブセットを識別します。 例: $filter=PartitionKey eq 'Sales' および RowKey ge 'S' および RowKey lt 'T'  
* 3 番目に良い方法は、**PartitionKey** を使用し、他のキーを持たないプロパティにフィルターをかけ、1 つ以上のエンティティを返すことが可能な***パーティション スキャン***です。 **PartitionKey** 値は特定のパーティションを識別し、プロパティ値はそのパーティション内のエンティティのサブセットを選択します。 例: $filter = PartitionKey eq '販売'、および LastName eq 'Smith'  
* ***Table Scan*** に **PartitionKey** は含まれません。また、一致するエンティティのテーブルを構成するパーティションのすべてを検索するため、非常に非効率的です。 フィルターが **RowKey** を使用する / しないに関わりなく、テーブルのスキャンが実行されます。 例: $filter = LastName eq 'Jones'  
* クエリは複数のエンティティを **PartitionKey** と **RowKey** の順序で並べ替えて返します。 クライアント内でエンティティを再度並べ替えるのを防ぐため、最も一般的な並べ替え順序を定義する **RowKey** を選択します。  

"**or**" を使用して **RowKey** 値に基づいてフィルターを指定した場合はパーティション スキャンが行われます。範囲クエリとしては扱われません。 そのため、$filter = PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322') のようなフィルターを使用するクエリの使用は避ける必要があります。  

ストレージ クライアント ライブラリを使って効率的なクエリを実行するクライアント側コードの例については、  

* [ストレージ クライアント ライブラリを使ってポイント クエリを実行する](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [LINQ を使用して複数のエンティティを取得する](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [サーバー側のプロジェクション](table-storage-design-patterns.md#server-side-projection)  

同じテーブルに格納された複数の種類のエンティティを処理できるクライアント側コードの例については、  

* [異種のエンティティ種類を使用する](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>適切な PartitionKey の選択
選択した **PartitionKey** は複数のパーティションでエンティティを配布するための要件に対し (一貫性を確保するため) EGT の使用を可能にするため、必要性のバランスを取る必要があります (スケーラブルなソリューションの確保のため)。  

すべてのエンティティを 1 つのパーティションに格納することも可能ですが、そうするとソリューションのスケーラビリティが制限され、Table サービスで要求を負荷分散できなくなる可能性があります。 逆に、パーティションごとに 1 つのエンティティを格納することもできます。このようにすると、スケーラビリティが高まり、Table サービスで要求を負荷分散できるようになるものの、エンティティ グループ トランザクションは使用できなくなります。  

理想的な **PartitionKey** とは、効率的なクエリを使用することができ、スケーラブルなソリューションを確保できるパーティションを持つものを指します。 通常、エンティティには、十分な数のパーティションにエンティティを分散できるだけのプロパティがあります。

> [!NOTE]
> たとえば、ユーザーまたは従業員に関する情報を格納するシステムでは、ユーザー ID を PartitionKey として使うと便利です。 パーティション分割キーとして所定の UserID を使用する複数のエンティティを使用できます。 あるユーザーに関するデータを保存するそれぞれのエンティティは 1 つのパーティションにグループ化されるため、高い拡張性を確保しながら、エンティティ グループ トランザクションを介してこれらのエンティティにアクセスできます。
> 
> 

選択した **PartitionKey** についてエンティティの挿入、更新、削除方法に関連する追加の検討事項があります。 詳細については、[データ変更のためのテーブルの設計](table-storage-design-for-modification.md)に関するセクションを参照してください。  

## <a name="optimizing-queries-for-the-table-service"></a>クエリを Table service 向けに最適化する
Table service は 1 つのクラスター化インデックス内の **PartitionKey** と **RowKey** 値を使用して自動的にインデックスを作成します。そのため、そのためポイント クエリが使用するには最も効果的です。 ただし、**PartitionKey** と **RowKey** のクラスター化インデックス以外にはインデックスはありません。

多くの設計は、複数の条件に基づいてエンティティをルックアップできるようにするという要件を満たす必要があります。 たとえば、電子メール、従業員 ID、姓に基づいて従業員エンティティを特定する場合などです。 「[テーブルの設計パターン](table-storage-design-patterns.md)」で説明されているパターンでは、このような要件の種類に対応しています。また、Table service がセカンダリ インデックスを提供しないことへの対処方法について説明します。  

* [パーティション内のセカンダリ インデックス パターン](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -異なる **RowKey** 値 (同じパーティション) を使用して各エンティティの複数のコピーを格納し、異なる **RowKey** 値を使用して効果的な検索と代替的な並べ替え順序を可能にします。  
* [パーティション内のセカンダリ インデックス パターン](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 別個のパーティションまたは別個のテーブルの異なる **RowKey** 値を使用して各エンティティの複数のコピーを格納し、異なる **RowKey** 値を使用して高速で効率的な検索と代替的な並べ替え順序を可能にします。  
* [インデックス エンティティのパターン](table-storage-design-patterns.md#index-entities-pattern) -をエンティティ一覧を返す、効率的な検索を有効にするインデックスのエンティティを管理します。  

## <a name="sorting-data-in-the-table-service"></a>Table service でデータを並べ替える
Table service は、**PartitionKey** 次に **RowKey** に基づいた昇順で並べ替え、エンティティを返します。 これらのキーは文字列値であり、数値を正しく並べ替えるには、固定長の値に変換し、ゼロ パディングを施す必要があります。 たとえば、従業員 ID 値を整数値の **RowKey** として使用する場合、従業員 ID を **123** から **00000123** に変換する必要があります。  

さまざまな順序 (名前、入社日など) で並べ替えられたデータを使う必要のあるアプリケーションは多数あります。 次のパターンでは、エンティティの並べ替え順序を代替する方法に対応しています。  

* [パーティション内のセカンダリ インデックス パターン](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -異なる RowKey 値(同じパーティション) を使用して各エンティティの複数のコピーを格納し、異なる RowKey 値を使用して効果的な検索と代替的な並べ替え順序を可能にします。  
* [パーティション内のセカンダリ インデックス パターン](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -別個のテーブルの別個のパーティションの異なる RowKey 値を使用して各エンティティの複数のコピーを格納し、異なる RowKey 値を使用して効果的な検索と代替的な並べ替え順序を可能にします。
* [ログ テール パターン](table-storage-design-patterns.md#log-tail-pattern) - 逆の日付と時間順で並べ替える *RowKey* 値を使用して、パーティションに最も新しく追加されたエンティティ **n** を取得します。  

## <a name="next-steps"></a>次の手順

- [テーブルの設計パターン](table-storage-design-patterns.md)
- [リレーションシップのモデル化](table-storage-design-modeling.md)
- [テーブル データを暗号化する](table-storage-design-encrypt-data.md)
- [データの変更に対応した設計](table-storage-design-for-modification.md)
