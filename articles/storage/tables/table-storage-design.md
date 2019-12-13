---
title: Azure Table Storage でスケーラビリティとパフォーマンスに優れたテーブルを設計する | Microsoft Docs
description: Azure Table Storage でスケーラビリティとパフォーマンスに優れたテーブルを設計する
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848284"
---
# <a name="design-scalable-and-performant-tables"></a>高性能で拡張性の高いテーブルの設計

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

スケーラビリティとパフォーマンスに優れたテーブルを設計する場合、パフォーマンス、スケーラビリティ、コストなどの要素を考慮する必要があります。 過去にリレーショナル データベースのスキーマを設計した経験がある方なら、こうした考慮事項はご存じでしょうが、Azure Table service ストレージ モデルとリレーショナル モデルには類似点もあれば、重要な違いもあります。 こうした相違点は、リレーショナル データベースの扱いに慣れた方には直感的にわかりづらかったり、扱いづらかったりする設計につながりがちですが、Azure Table service などの NoSQL キー/値ストアを設計する場合には好都合です。 設計の相違点の多くは、Table service が何十億ものデータ エンティティ (リレーショナル データベース用語では行) を含むクラウド アプリケーションをサポートするためのデータや、大量のデータトランザクションをサポートするよう設計されている点を考慮します。 そのため、データの格納方法や Table service の動作方法について違った考え方が必要になります。 NoSQL データ ストアを適切に設計すれば、リレーショナル データベースを使うソリューションよりも、ソリューションのスケーラビリティが大幅に高まり、コストも抑えられます。 このガイドでは、これらのトピックについて説明します。  

## <a name="about-the-azure-table-service"></a>Azure Table service
このセクションでは、パフォーマンスとスケーラビリティを重視した設計に関連する Table service の主要機能を取り上げます。 初めて Azure Storage と Table service を利用する場合は、「[Microsoft Azure Storage の概要](../../storage/common/storage-introduction.md)」と「[.NET を使用して Azure Table Storage を使用する](../../cosmos-db/table-storage-how-to-use-dotnet.md)」を読んでから、この記事を読み進めてください。 このガイドで主に取り上げるのは Table service についてですが、Azure のキューや Blob service と、それらのサービスを Table service と共に使用する方法についても触れます。  

Table service とは 名前が示すとおり、Table service ではデータの格納にテーブル形式を使います。 名前からおわかりのように、Table service は表形式を使用してデータを格納します。 各エンティティは、それ自体を一意に識別するためのキーのペアと、Table サービスがエンティティの最終更新日時をトラッキングするためのタイムスタンプ列を持ちます (エンティティの更新は自動的に行われます。 すべてのエンティティには、エンティティを一意に識別するキーと、エンティティの最終更新時を追跡するために Table service が使用するタイムスタンプ列のペアがあります。 タイムスタンプは自動的に適用されます。タイムスタンプを手動で任意の値に上書きすることはできません。 Table service では、この最終更新日時のタイムスタンプ (LMT) を使ってオプティミスティック コンカレンシーを管理します。  

> [!NOTE]
> Table service REST API 操作により、LMT から派生する **ETag** 値も返されます。 このドキュメントでは、ETag と LMT という用語を区別なく使います。基となる同じデータを表しているためです。  
> 
> 

次の例は、従業員と部署のエンティティを格納する、シンプルなテーブル設計を示しています。 このガイドで紹介する例の多くは、このシンプルな設計が基になっています。  

<table>
<tr>
<th>パーティション キー</th>
<th>行キー</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>部署</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


今のところ、データはリレーショナル データベースのテーブルと似ており、異なるキーが必須の列となっていて、同じテーブルに複数の種類のエンティティを格納できます。 また、**FirstName** や **Age** などのユーザー定義プロパティには、リレーショナル データベース内の列のように、整数や文字列などのデータ型があります。 ただし、リレーショナル データベースとは違って、Table service にはスキーマがないため、エンティティごとにプロパティのデータ型は同じである必要ありません。 1 つのプロパティに複雑なデータ型を格納するには、JSON や XML などのシリアル化された形式を使う必要があります。 サポート対象のデータ型と日付の範囲、名付け規則、サイズ制限などの Table サービスの詳細については、「 [Table サービス データ モデルについて](https://msdn.microsoft.com/library/azure/dd179338.aspx)」を参照してください。

テーブル デザインの基盤には、**PartitionKey** と **RowKey** の選択が適しています。 テーブルに格納されている各エンティティは、**PartitionKey** と **RowKey** の一意の組み合わせである必要があります。 リレーショナル データベース テーブル内のキーと同様に、**PartitionKey** と **RowKey** の値には、高速検索を可能にするクラスター化インデックスを作成するためのインデックスが作成されています。 ただし、Table service はセカンダリ インデックスを作成しないため、**PartitionKey** と **RowKey** のみがインデックス付きプロパティになります。 「[テーブルの設計パターン](table-storage-design-patterns.md)」で説明されているいくつかのパターンから、この明確な制限を回避する方法がわかります。  

テーブルは 1 つ以上のパーティション から構成されており、設計に関する決定事項の多くは、ソリューションを最適化するために適切な **PartitionKey** と **RowKey** を選択することになります。 ソリューションによっては、すべてのエンティティがパーティションを使って整理された 1 つのテーブルで構成されるものもありますが、通常は複数のテーブルが含まれます。 テーブルを使えばエンティティを論理的に整理できるほか、アクセス制御リストを使ってデータへのアクセスを管理できます。また、1 回のストレージ操作でテーブル全体を削除できます。  

## <a name="table-partitions"></a>テーブルのパーティション
アカウント名、テーブル名、**PartitionKey** を組み合わせ、テーブル サービスがエンティティを格納する場所をストレージ サービス内のパーティションに特定します。 エンティティのアドレス指定スキームの一部であると同時に、パーティションはトランザクションのスコープが定義 し(下記の [エンティティ グループ トランザクション](#entity-group-transactions) を参照してください)、テーブル サービスのスケーラビリティを形成します。 パーティションの詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../../storage/common/storage-scalability-targets.md)」を参照してください。  

Table service では、個々のノードが 1 つ以上の完全なパーティションを提供し、サービスのスケーリングはノード間でパーティションの負荷を動的に分散させることで行われます。 ノードに負荷がかかる場合は、テーブル サービスのパーティション範囲を 別のノードと*分割*し、トラフィックが少ないときにサービスを*マージ*して、パーティション範囲を複数のトラフィックの少ないノードから 1 つのノードに戻すことができます。  

Table service の内部詳細、特に、サービスのパーティション管理方法については、ホワイト ペーパー [Microsoft Azure のストレージ: 強力な一貫性を備えた高使用可能なクラウド ストレージ サービス](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)を参照してください。  

## <a name="entity-group-transactions"></a>エンティティ グループ トランザクション
エンティティ グループ トランザクション (EGT) は、Table service で複数のエンティティ間でアトミックな更新を行うための唯一の組み込みのメカニズムです。 EGT は、*バッチ トランザクション*とも呼ばれています。 EGT では、同じパーティションに格納されたエンティティしか処理できません (つまり、特定のテーブルで同じパーティション キーを共有しています)。 そのため、複数のエンティティにまたがるアトミックなトランザクションが必要な場合は、それらのエンティティを同じパーティションに格納する必要があります。 これが、異なる種類のエンティティに複数のテーブルを使わずに、異なる種類のエンティティを同じテーブル (とパーティション) に格納する主な理由です。 単一の EGT で最大 100 個のエンティティを処理できます。  複数の並行処理 EGT を送信する場合は、それらの EGT が EGT 間の共通であるエンティティには動作しないことを確認することが重要です。さもないと、処理が遅くなる可能性があります。

また、EGT によって、設計で評価が必要なトレードオフが生じる可能性があります。 つまり、使用するパーティションが増えると、ノード間で要求を負荷分散しやすくなるため、アプリケーションのスケーラビリティが向上します。 一方、使用するパーティションが増えると、アプリケーションでアトミックなトランザクションを実行し、データの強力な一貫性を維持する能力が制限される可能性があります。 さらに、1 つのノードの予想されるトランザクションのスループットを制限する可能性がある、パーティション レベルの特定のスケーラビリティ ターゲットがあります。 Azure Storage アカウントと Table service のスケーラビリティ ターゲットの詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../../storage/common/storage-scalability-targets.md)」を参照してください。   

## <a name="capacity-considerations"></a>容量に関する考慮事項
次の表に、Table service ソリューションの設計時に考慮する必要のある主要な値をまとめます。  

| Azure ストレージ アカウントの合計容量 | 500 TB |
| --- | --- |
| Azure のストレージ アカウントのテーブルの数 |ストレージ アカウントの容量のみによる制限 |
| テーブルのパーティションの数 |ストレージ アカウントの容量のみによる制限 |
| パーティション内のエンティティの数 |ストレージ アカウントの容量のみによる制限 |
| 個別のエンティティのサイズ |1 MBまでの最大 255 個のプロパティ (**PartitionKey**、**RowKey**、**タイムスタンプ**を含む) |
| **PartitionKey** |最大 1 KB の文字列 |
| **RowKey** |最大 1 KB の文字列 |
| エンティティ グループ トランザクションのサイズ |トランザクションには最大で 100 個のエンティティを含めることができ、ペイロードは 4 MB 未満にする必要があります。 EGT では 1 回に 1 つのエンティティしか更新できません。 |

詳細については、「 [Table サービス データ モデルについて](https://msdn.microsoft.com/library/azure/dd179338.aspx)」を参照してください。  

## <a name="cost-considerations"></a>コストに関する考慮事項
テーブル ストレージは比較的安価ですが、Table service ソリューションの評価の一環として、容量の使用とトランザクションの量を踏まえてコストを見積もる必要があります。 ただし、多くのシナリオでは、ソリューションのパフォーマンスとスケーラビリティを向上させるために、非正規化されたデータまたは重複するデータを格納するのも有効です。 価格の詳細については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。  

## <a name="next-steps"></a>次の手順

- [テーブルの設計パターン](table-storage-design-patterns.md)
- [リレーションシップのモデル化](table-storage-design-modeling.md)
- [クエリに対応した設計](table-storage-design-for-query.md)
- [テーブル データの暗号化](table-storage-design-encrypt-data.md)
- [データの変更に対応した設計](table-storage-design-for-modification.md)
