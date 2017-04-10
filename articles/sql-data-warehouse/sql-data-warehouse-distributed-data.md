---
title: "Azure SQL Data Warehouse での分散データの概要 | Microsoft Docs"
description: "超並列処理 (MPP) でのデータ分散方法および Azure SQL Data Warehouse と Parallel Data Warehouse でのテーブル分散オプションについて説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: 195b78a7f634d01f228c90efb34763e4175708ac
ms.lasthandoff: 01/24/2017


---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>超並列処理 (MPP) 用の分散データと分散テーブル
Azure SQL Data Warehouse および Parallel Data Warehouse でのユーザー データの分散方法について説明します。これらは、Microsoft の超並列処理 (MPP) システムです。 分散データを効率的に使用するようにデータ ウェアハウスを設計すると、MPP アーキテクチャのクエリ処理のメリットを実現するのに役立ちます。 いくつかのデータベース設計の選択は、クエリのパフォーマンス向上に大きな影響を与えます。  

> [!NOTE]
> Azure SQL Data Warehouse と Parallel Data Warehouse は同じ超並列処理 (MPP) デザインを使用しますが、基になっているプラットフォームのためにいくつかの違いがあります。 SQL Data Warehouse は、Azure 上で実行するサービスとしてのプラットフォーム (PaaS) です。 Parallel Data Warehouse は、Windows Server 上で実行するオンプレミスのアプリケーションである Analytics Platform System (APS) で実行します。
> 
> 

## <a name="what-is-distributed-data"></a>分散データとは
SQL Data Warehouse と Parallel Data Warehouse では、分散データとは MPP システムの複数の場所に格納されているユーザー データのことです。 これらの各場所は、独立した記憶域およびその場所にあるデータの部分に対するクエリを実行する処理単位として機能します。 分散データは、クエリを並列実行して高いクエリ パフォーマンスを実現するための基礎です。

データを分散させるため、データ ウェアハウスはユーザー テーブルの各行を&1; つの分散場所に割り当てます。  テーブルの分散方法としては、ハッシュ分散方式とラウンドロビン方式があります。 分散方式は、CREATE TABLE ステートメントで指定します。 

## <a name="hash-distributed-tables"></a>ハッシュ分散テーブル
次の図は、完全な (分散していない) テーブルがハッシュ分散テーブルとして保存されるしくみを示したものです。 確定関数が、各行を&1; つのディストリビューションに割り当てます。 テーブルの定義では、1 つの列をディストリビューション列として指定します。 ハッシュ関数は、ディストリビューション列の値を使用してディストリビューションに各行を割り当てます。

ディストリビューション列の選択については、差異性、データ スキュー、およびシステムで実行されるクエリの種類など、パフォーマンスに関する考慮事項があります。

![分散テーブル](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "分散テーブル")  

* 各行は、1 つのディストリビューションに属しています。  
* 確定ハッシュ アルゴリズムが、各行を&1; つのディストリビューションに割り当てます。  
* ディストリビューションごとのテーブル行の数は、異なるテーブル サイズによって示されるように異なります。

## <a name="round-robin-distributed-tables"></a>ラウンドロビン分散テーブル
ラウンドロビン分散テーブルは、各行をディストリビューションに順番に割り当てることによって、行を分散させます。 ラウンドロビン テーブルへのデータの読み込みはすばやいですが、クエリのパフォーマンスは低下する可能性があります。  通常、ラウンドロビン テーブルを結合するには、クエリが正確な結果を生成できるように行を再シャッフルする必要があり、これには時間がかかります。

## <a name="distributed-storage-locations-are-called-distributions"></a>分散される記憶域の場所はディストリビューションと呼ばれる
分散される各場所は、ディストリビューションと呼ばれます。 クエリが並列で実行するとき、各ディストリビューションはデータの一部分に対する SQL クエリを実行します。 SQL Data Warehouse は、SQL Database を使用してクエリを実行します。 Parallel Data Warehouse は、SQL Server を使用してクエリを実行します。 このシェアード ナッシング アーキテクチャの設計は、スケールアウト並列コンピューティングを実現するための基礎です。

### <a name="can-i-view-the-distributions"></a>ディストリビューションを見ることはできますか?
各ディストリビューションはディストリビューション ID を持っており、SQL Data Warehouse および Parallel Data Warehouse に関するシステム ビューで表示できます。 ディストリビューション ID を使用して、クエリのパフォーマンスやその他の問題を解決できます。 システム ビューの一覧については、[MPP のシステム ビューに関する記事](sql-data-warehouse-reference-tsql-statements.md)をご覧ください。

## <a name="difference-between-a-distribution-and-a-compute-node"></a>ディストリビューションとコンピューティング ノードの違い
ディストリビューションは、分散データの格納および並列クエリの処理に関する基本的な単位です。 ディストリビューションをグループ化したものがコンピューティング ノードです。 各コンピューティング ノードは、1 つまたは複数のディストリビューションを追跡します。  

* Analytics Platform System は、ハードウェア アーキテクチャおよびスケールアウト機能の中心的なコンポーネントとしてコンピューティング ノードを使用します。 ハッシュ分散テーブルの図で示されているように、常に、コンピューティング ノードあたり&8; 個のディストリビューションを使用します。 コンピューティング ノードの数、したがってディストリビューションの数は、アプライアンスに対して購入するコンピューティング ノードの数によって決まります。 たとえば、8 個のコンピューティング ノードを購入した場合、ディストリビューションは 64 個です (8 コンピューティング ノード x 8 ディストリビューション/ノード)。 
* SQL Data Warehouse では、ディストリビューションは 60 個に固定されており、コンピューティング ノードの数は可変です。 コンピューティング ノードは、Azure のコンピューティングおよびストレージ リソースで実装されます。 コンピューティング ノードの数は、バックエンド サービスの作業負荷と、ユーザーがデータ ウェアハウスに対して指定する計算能力 (DWU) に従って変化します。 コンピューティング ノードの数が変わると、コンピューティング ノードあたりのディストリビューションの数も変更されます。 

### <a name="can-i-view-the-compute-nodes"></a>コンピューティング ノードを見ることはできますか?
各コンピューティング ノードはノード ID を持っており、SQL Data Warehouse および Parallel Data Warehouse に関するシステム ビューで表示できます。  名前が sys.pdw_nodes で始まるシステム ビューで node_id 列を検索することにより、コンピューティング ノードを見ることができます。 システム ビューの一覧については、[MPP のシステム ビューに関する記事](sql-data-warehouse-reference-tsql-statements.md)をご覧ください。

## <a name="Replicated"></a>Parallel Data Warehouse のレプリケート テーブル
適用対象: Parallel Data Warehouse

分散テーブルを使用する以外に、Parallel Data Warehouse にはテーブルをレプリケートするオプションがあります。 "*レプリケート テーブル*" とは、各コンピューティング ノードに全体が格納されるテーブルです。 テーブルをレプリケートすると、結合または集計でテーブルを使用する前にコンピューティング ノード間でテーブルの行を転送する必要がなくなります。 各コンピューティング ノードに完全なテーブルを格納するために余分な記憶域が必要になるため、レプリケート テーブルは小さなテーブルにのみ使用できます。  

次の図は、各コンピューティング ノードに格納されるレプリケート テーブルを示したものです。 レプリケート テーブルは、コンピューティング ノードに割り当てられているすべてのディスクに格納されます。 このディスク戦略は、SQL Server のファイル グループを使用して実装されます。  

![レプリケート テーブル](media/sql-data-warehouse-distributed-data/replicated-table.png "レプリケート テーブル") 

## <a name="next-steps"></a>次のステップ
分散テーブルを効果的に使用する方法については、「[SQL Data Warehouse のテーブルの分散](sql-data-warehouse-tables-distribute.md)」を参照してください。  


