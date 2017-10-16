---
title: "SQL Data Warehouse での同時実行とワークロード管理 | Microsoft Docs"
description: "ソリューション開発のための Azure SQL Data Warehouse での同時実行とワークロード管理を理解します。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>SQL Data Warehouse での同時実行とワークロード管理
Microsoft Azure SQL Data Warehouse では、予測可能なパフォーマンスを大規模に実現するために、メモリと CPU の優先順位付けのようなリソース割り当てに加えて、同時実行レベルを制御できるようになっています。 この記事では、同時実行とワークロード管理の機能がどのように実装されたか、そしてこれらの機能をデータ ウェアハウスでどのように制御できるかについて説明しながら、両機能の概念を紹介します。 SQL Data Warehouse のワークロード管理は、マルチユーザー環境をサポートすることを目的としています。 マルチテナント ワークロードは想定されていません。

## <a name="concurrency-limits"></a>同時実行の制限
SQL Data Warehouse では、最大 1,024 個の同時接続が可能です。 1,024 のすべての接続でクエリを同時に送信することができます。 ただし、スループットを最適化する目的で、各クエリに与えられるメモリ許可が最小限になるようにするために、一部のクエリがキューに配置される場合があります。 クエリの実行時にキューに配置されます。 同時実行の制限に達したときにクエリをキューに配置することによって、アクティブなクエリが絶対に必要なメモリ リソースに必ずアクセスできるようになるため、全体のスループットが向上します。  

同時実行の制限は、*同時実行クエリ*と*同時実行スロット*の 2 つの概念によって管理されます。 クエリは、クエリの同時実行の制限内および同時実行スロット割り当て内で実行する必要があります。

* 同時実行クエリとは、同時に実行されるクエリを指します。 SQL Data Warehouse では、大きな DWU サイズで最大 32 個の同時実行クエリをサポートしています。
* 同時実行スロットは、DWU に基づいて割り当てられます。 100 DWU ごとに、4 つの同時実行スロットが提供されます。 たとえば、DW100 では 4 つの同時実行スロットが割り当てられ、DW1000 では 40 個の同時実行スロットが割り当てられます。 各クエリは、クエリの [リソース クラス](#resource-classes) に応じて 1 つまたは複数の同時実行スロットを使用します。 smallrc リソース クラスで実行されているクエリは、1 つの同時実行スロットを使用します。 より高いリソース クラスで実行されているクエリは、複数の同時実行スロットを使用します。

次の表に、各 DWU サイズでの同時実行クエリと同時実行スロットの両方の制限を示します。

### <a name="concurrency-limits"></a>同時実行の制限
| DWU | 同時クエリの最大数 | 割り当てられる同時実行スロット数 |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

これらのいずれかのしきい値に達すると、新しいクエリはキューに配置されます。キューに配置されたクエリは、先入れ先出し方式で実行されます。  クエリが完了してクエリとスロットの数が制限値を下回ると、キューに配置されたクエリは解放されます。 

> [!NOTE]  
> *SELECT* クエリは、同時実行の制限を受けることはありません。 ユーザーは、システムで実行されるクエリの数にとらわれずにシステムを監視できます。
> 
> 

## <a name="resource-classes"></a>リソース クラス
リソース クラスにより、クエリに対するメモリ割り当てと CPU サイクルの制御が行われます。 2 つの種類のリソース クラスをデータベース ロールの形式でユーザーに割り当てることができます。 2 つの種類のリソース クラスは次のとおりです。
1. 動的なリソースのクラス (**smallrc、mediumrc、largerc、xlargerc**) は、現在の DWU に応じてメモリの量を割り当てます。 つまり、DWU をスケールアップすると、クエリに対しより多くのメモリが自動的に取得されます。 
2. 静的リソース クラス (**staticrc10、staticrc20、staticrc30、staticrc40、staticrc50、staticrc60、staticrc70、staticrc80**) では、現在の DWU に関係なくの同じ量のメモリが割り当てられます (DWU 自体に十分なメモリが割り当てられていることを前提として)。 つまり、より大きな DWU では、各リソース クラスで同時により多くのクエリを実行できます。

**smallrc** および **staticrc10** のユーザーにはより少ないメモリが割り当てられ、その結果、より多くの同時実行が可能になります。 これに対し、**xlargerc** または **staticrc80** に割り当てられたユーザーには多くのメモリが与えられるため、同時実行が許可されるクエリの数は少なくなります。

既定では、各ユーザーは小規模リソース クラス **smallrc** のメンバーです。 リソース クラスのサイズを大きくするにはプロシージャ `sp_addrolemember` を使用し、リソース クラスのサイズを小さくするには `sp_droprolemember` を使用します。 たとえば、次のコマンドは、ロード ユーザーのリソース クラスをサイズの大きな **largerc** に割り当てます。

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>リソース クラスの割り当てを受け入れないクエリ

クエリの中には、多くのメモリを割り当ててもメリットがない種類のクエリもあります。 リソース クラスの割り当ては無視されますが、その代わり、これらのクエリは常に小規模リソース クラスで実行されます。 これらのクエリが常に小規模リソース クラスで実行されると、同時実行スロットの空きが足りなくてもクエリを実行できます。このため、必要以上にスロットを消費することはなくなります。 詳細については、「[リソース クラスの例外](#query-exceptions-to-concurrency-limits)」をご覧ください。

## <a name="details-on-resource-class-assignment"></a>リソース クラスの割り当てに関する詳細情報


リソース クラスに関する詳細をもう少し以下に示します。

* *ロールの変更* " アクセス許可が必要です。
* ユーザーを 1 つ以上上のリソース クラスに追加できますが、動的リソース クラスは、静的リソース クラスよりも優先されます。 つまり、ユーザーが **mediumrc** (動的) と **staticrc80** (静的) の両方に割り当てられている場合、実際に割り当てられるリソース クラスは、**mediumrc** になります。
 * 特定の種類のリソース クラスがユーザーに複数 (2 つ以上の動的リソース クラスまたは 1 つ以上の静的リソース クラス) 割り当てられた場合、最上位のリソース クラスが割り当てられます。 つまり、mediumrc and largerc の両方がユーザーに割り当てられた場合、より上位のリソース クラス (largerc) が使用されます。 ユーザーが **staticrc20** と **statirc80** の両方に割り当てられている場合、**staticrc80** が使用されます。
* システム管理ユーザーのリソース クラスは変更できません。

詳細な例は、この記事の最後にある「 [ユーザー リソース クラスの変更例](#changing-user-resource-class-example)」を参照してください。

## <a name="memory-allocation"></a>メモリの割り当て
ユーザーのリソース クラスを引き上げることには長所と短所があります。 ユーザーのリソース クラスを引き上げると、そのユーザーのクエリで利用可能なメモリが増え、クエリの実行速度が上がります。  その一方で、リソース クラスを引き上げると、同時実行可能なクエリの数が減ります。 これは、大量のメモリを 1 つのクエリに割り当てるか、同様にメモリの割り当てが必要な他のクエリの同時実行を許可するかのトレードオフです。 あるユーザーにクエリのメモリが大量に割り当てられている場合、他のユーザーがクエリを実行するために同じメモリにアクセスすることはできなくなります。

次の表に、DWU とリソース クラスごとに各ディストリビューションに割り当てられるメモリを示します。

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>動的なリソース クラスの配布あたりのメモリ割り当て (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

次の表に、DWU と静的リソース クラスごとに各ディストリビューションに割り当てられるメモリを示します。 リソースのクラスが高くなるほど、グローバル DWU 制限に従ってメモリ量が減少することに注意してください。

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>静的なリソース クラスの配布あたりのメモリ割り当て (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

この表から、**xlargerc** リソース クラスの DW2000 で実行されているクエリは、60 個の分散データベースそれぞれにある 6,400 MB のメモリにアクセスすることがわかります。  SQL Data Warehouse には、60 個のディストリビューションがあります。 そのため、特定のリソース クラスでのクエリの合計メモリ割り当て量を計算するには、上記の値に 60 を掛ける必要があります。

### <a name="memory-allocations-system-wide-gb"></a>システム全体のメモリ割り当て (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

このシステム全体のメモリ割り当ての表から、xlargerc リソース クラスの DW2000 で実行されているクエリには、SQL Data Warehouse 全体で合計 375 GB のメモリが割り当てられることがわかります (6,400 MB * 60 ディストリビューション / 1,024 (GB 単位))。

静的リソース クラスに同じ計算が適用されます。
 
## <a name="concurrency-slot-consumption"></a>使用される同時実行スロット数  
より上位のリソース クラスで実行されるクエリには、より多くのメモリが与えられます。 メモリは、固定のリソースです。  そのため、1 つのクエリに割り当てられるメモリが多くなるほど、同時に実行できるクエリの数が少なくなります。 次の表で、これまでに説明したすべての概念を 1 つにまとめ、各 DWU で利用可能な同時実行スロットの数と各リソース クラスで使用されるスロットの数を示します。  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>動的リソース クラスの同時実行スロットの割り当てと使用量  
| DWU | 同時クエリの最大数 | 割り当てられる同時実行スロット数 | smallrc で使用されるスロット数 | mediumrc で使用されるスロット数 | largerc で使用されるスロット数 | xlargerc で使用されるスロット数 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>静的リソース クラスの同時実行スロットの割り当てと使用量  
| DWU | 同時クエリの最大数 | 割り当てられる同時実行スロット数 |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

この表から、DW1000 として実行されている SQL Data Warehouse では、最大 32 個の同時実行クエリと合計 40 個の同時実行スロットが提供されることが分かります。 すべてのユーザーが smallrc で実行している場合、クエリごとに 1 つの同時実行スロットが使用されるため、32 個のクエリを同時実行できます。 DW1000 のすべてのユーザーが mediumrc で実行している場合、各クエリには総メモリの 47 GB の割り当てについてディストリビューションあたり 800 MB が割り当てられ、同時実行は 5 ユーザー (40 個の同時実行スロット/mediumrc ユーザーあたり 8 スロット) に制限されます。

## <a name="selecting-proper-resource-class"></a>適切なリソース クラスを選択します。  
お勧めの方法として、ユーザーのリソース クラスを変更するのではなく、ユーザーを永続的にリソース クラスに割り当てます。 たとえば、クラスター化列ストア テーブルへの読み込みでは、より多くのメモリを割り当てることでより高品質のインデックスを作成できます。 読み込み時により多くのメモリにアクセスできるようにするには、データの読み込み専用のユーザーを作成し、このユーザーを永続的に上位のリソース クラスに割り当てます。
ここでベスト プラクティスをいくつか紹介します。 前述のように、SQL DW では、静的リソース クラスと動的リソース クラスの 2 つの種類のリソース クラスをサポートしています。
### <a name="loading-best-practices"></a>読み込みのベスト プラクティス
1.  通常のデータ量読み込みが予想される場合は、静的リソース クラスをお勧めします。 後で、計算能力を向上するために拡張すれば、ロード ユーザーは多くのメモリを消費しないため、データ ウェアハウスは多数の同時実行クエリを既定で実行できます。
2.  大きな負荷が予想される状況では、動的リソース クラスをお勧めします。 後で、計算能力を向上するため拡張すれば、ロード ユーザーはその場でメモリを増やせるため、読み込みを高速で実行できます。

読み込みを効率的に処理を必要とされるメモリは、読み込まれるテーブルおよび処理されるデータ量の性質によって異なります。 たとえば、CCI テーブルにデータを読み込むには、CCI 行グループを最適化するためのメモリが必要です。 詳細については、列ストア インデックスのデータ読み込みのガイダンスを参照してください。

ベスト プラクティスとしては、200 MB のメモリを読み込みに使用することをお勧めします。

### <a name="querying-best-practices"></a>クエリのベスト プラクティス
クエリには、その複雑さによっては異なる要件があります。 クエリ ニーズに応じて、クエリあたりのメモリを増やすことも、同時実行機能の向上を図ることも、全体的なスループットの増加に有効な方法です。
1.  負荷が標準的な場合は、クエリが複雑であり (たとえば、毎日または毎週レポートを生成する)、同時実行が必要ない場合は、動的リソース クラスをお勧めします。 大量のデータを処理する必要がある場合は、クエリを実行するユーザーに割り当てるメモリが自動的に増量されるよう、データ ウェアハウスを拡張することをお勧めします。
2.  負荷が変動する、または同時実行パターンが 1 日のうちに変動することが予想される場合 (たとえばデータベースが、広範にアクセスできる Web UI を通じて照会される場合)、静的リソース クラスをお勧めします。 後でデータ ウェアハウスが拡張されると、静的リソース クラスに関連付けられているユーザーは自動的に複数の同時実行クエリを実行することできます。

照会されるデータの量、テーブル スキーマの性質、さまざまな結合、選択、およびグループ述語など多くの要因が関係するため、クエリのニーズに応じて適切なメモリ割り当てを選択するのは容易ではありません。 一般的な観点からすると、メモリ割り当て量を増やすと、クエリの実行時間が短縮されますが、全体的な同時実行能力は減少します。 同時実行能力が問題でない場合は、メモリは過剰ぎみに割り当てて問題ありません。 スループットを微調整するには、さまざまな種類のリソース クラスを試してみる必要があります。

次のストアド プロシージャを使用して、特定の SLO の同時実行性とリソース クラスあたりのメモリ割り当てを割り出せば、特定のリソース クラスの非分割 CCI テーブルでのメモリ消費量の多い CCI 操作に最適なリソース クラスを特定することができます。

#### <a name="description"></a>説明:  
このストアド プロシージャの目的を次に示します。  
1. 特定の SLO の同時実行性とリソース クラスあたりのメモリ割り当てを割り出す。 次の例で示すように、ユーザーは、このスキーマとテーブル名の両方について NULL を指定する必要があります。  
2. 特定のリソース クラスの非分割 CCI テーブルでのメモリ消費量の多い CCI 操作 (読み込み、テーブルのコピー、インデックスの再構築など) に最適なリソース クラスを特定する。 このストアド プロシージャでは、必要なメモリ割り当てを割り出すために、テーブル スキーマを使用します。

#### <a name="dependencies--restrictions"></a>依存関係と制限事項:
- このストアド プロシージャは、分割型 CCI テーブルのメモリの要件を計算するものではありません。    
- このストアド プロシージャでは、CTAS/INSERT-SELECT の SELECT 部分のメモリ要件は計算に入れられておらず、それが単純な SELECT であると想定ています。
- このストアド プロシージャでは、このストアド プロシージャが作成されたセッションで使用できるよう、一時テーブルを使用しています。    
- このストアド プロシージャは、現在の環境 (ハードウェア構成、DMS 構成など) に依存しており、そのいずれかが変更されると正しく動作しません。  
- このストアド プロシージャは、現在存在する同時実行性制限に依存しており、そのいずれかが変更されると正しく動作しません。  
- このストアド プロシージャは、現在提供されているリソース クラスに依存しており、そのいずれかが変更されると正しく動作しません。  

>  [!NOTE]  
>  指定されたパラメーターを使用してストアド プロシージャを実行しても出力が得られない場合は、次の 2 つの理由が考えられます。 <br />1.DW パラメーターに、無効な SLO 値が含まれている <br />2.または、テーブル名が提供されている場合、CCI 操作に一致するリソース クラスがない。 <br />たとえば、DW100 で可能なメモリの最大割り当て量は 400 MB ですが、テーブル スキーマのサイズが 400 MB の要件に適合する大きさである必要があります。
      
#### <a name="usage-example"></a>使用例:
構文:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU:DW DB から現在の DWU を抽出するため NULL パラメーターを指定するか、'DW100' の形式でサポートされている DWU を指定します。
2. @SCHEMA_NAME:テーブルのスキーマ名を指定します。
3. @TABLE_NAME:必要なテーブル名を指定します。

このストアド プロシージャの実行例:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

上記の例で使用される Table1 は、以下のように作成できます  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>ストアド プロシージャの定義:
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>クエリの重要度
SQL Data Warehouse では、ワークロード グループを使用してリソース クラスを実装します。 各 DWU サイズでのリソース クラスの動作を制御するワークロード グループは合計で 8 つあります。 いずれの DWU でも、使用するのは 8 つのワークロード グループのうち 4 つだけです。 各ワークロード グループは smallrc、mediumrc、largerc、または xlargerc のうちの 1 つに割り当てられるため、これは当然のことです。 ワークロード グループを理解するうえで、これらのワークロード グループの一部は上位の *重要度*に設定されることに注意する必要があります。 重要度は、CPU のスケジュール設定に使用されます。 重要度が "高" のクエリには、重要度が "中" のクエリと比べて 3 倍の CPU サイクルが与えられます。 そのため、同時実行スロットのマッピングにより、CPU の優先度も決まります。 16 個以上のスロットを使用するクエリは、"高" 重要度として実行されます。

次の表に、各ワークロード グループの重要度のマッピングを示します。

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>同時実行スロットおよび重要度に対するワークロード グループのマッピング
| ワークロード グループ | 同時実行スロットのマッピング | MB / ディストリビューション | 重要度のマッピング |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |中 |
| SloDWGroupC01 |2 |200 |中 |
| SloDWGroupC02 |4 |400 |中 |
| SloDWGroupC03 |8 |800 |中 |
| SloDWGroupC04 |16 |1,600 |高 |
| SloDWGroupC05 |32 |3,200 |高 |
| SloDWGroupC06 |64 |6,400 |高 |
| SloDWGroupC07 |128 |12,800 |高 |

**同時実行スロットの割り当てと使用数** の表から、DW500 では smallrc、mediumrc、largerc、および xlargerc のそれぞれで 1 個、4 個、8 個、または 16 個の同時実行スロットが使用されることが分かります。 上の表でこれらの値を調べることで、各リソース クラスの重要度を確認できます。

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 での重要度に対するリソース クラスのマッピング
| リソース クラス | ワークロード グループ | 使用される同時実行スロット数 | MB / ディストリビューション | 重要度 |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |中 |
| mediumrc |SloDWGroupC02 |4 |400 |中 |
| largerc |SloDWGroupC03 |8 |800 |中 |
| xlargerc |SloDWGroupC04 |16 |1,600 |高 |
| staticrc10 |SloDWGroupC00 |1 |100 |中 |
| staticrc20 |SloDWGroupC01 |2 |200 |中 |
| staticrc30 |SloDWGroupC02 |4 |400 |中 |
| staticrc40 |SloDWGroupC03 |8 |800 |中 |
| staticrc50 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc60 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc70 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc80 |SloDWGroupC03 |16 |1,600 |高 |

次の DMV クエリを使用すると、リソース ガバナーの観点からメモリ リソースの割り当ての違いを詳細に確認できます。また、トラブルシューティングを行うときにワークロード グループのアクティブな使用状況と過去の使用状況を分析することもできます。

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>同時実行の制限が考慮されるクエリ
ほとんどのクエリは、リソース クラスによって管理されます。 これらのクエリは、同時実行クエリと同時実行スロットの両方のしきい値以下である必要があります。 ユーザーは、同時実行スロット モデルからクエリを除外することはできません。

繰り返しになりますが、次のステートメントではリソース クラスが考慮されます。

* INSERT-SELECT
* UPDATE
* 削除
* SELECT (ユーザー テーブルのクエリを実行する場合)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* データの読み込み
* Data Movement Service (DMS) によって実行されるデータ移動操作

## <a name="query-exceptions-to-concurrency-limits"></a>同時実行の制限に対するクエリの例外
クエリには、ユーザーの割り当てられているリソース クラスが考慮されないものがあります。 同時実行の制限に対するこうした例外は特定のコマンドに必要なメモリ リソースが少ない場合に発生します。その理由は多くの場合、コマンドがメタデータ操作となるためです。 これらの例外により、大量のメモリ割り当てを必要としないクエリに対して、多くのメモリが割り当てられる状態を回避できます。 このような場合、ユーザーに割り当てられた実際のリソース クラスに関係なく、既定の小規模リソース クラス (smallrc) が常に使用されます。 たとえば、`CREATE LOGIN` は常に smallrc で実行されます。 この操作の実行に必要なリソースは非常に少ないので、同時実行スロット モデルにクエリを含めても意味はありません。  また、これらのクエリには 32 ユーザーの同時実行の制限は適用されず、1,024 セッションというセッションの上限までクエリを無制限に実行できます。

次のステートメントでは、リソース クラスは考慮されません。

* CREATE または DROP TABLE
* ALTER TABLE ...SWITCH、SPLIT、または MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE、UPDATE、または DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE、ALTER、または DROP USER
* CREATE、ALTER、または DROP PROCEDURE
* CREATE または DROP VIEW
* INSERT VALUES
* システム ビューおよび DMV からの SELECT
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a>ユーザー リソース クラスの変更例
1. **ログインを作成する:** SQL Data Warehouse データベースをホストしている SQL Server の**マスター** データベースへの接続を開き、次のコマンドを実行します。
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Azure SQL Data Warehouse ユーザーの master データベースにユーザーを作成することをお勧めします。 マスターでユーザーを作成すると、ユーザーはデータベース名を指定せずに SSMS のようなツールを使用してログインできます。  また、オブジェクト エクスプ ローラーを使用して、SQL Server のすべてのデータベースを表示することもできます。  ユーザーの作成および管理の詳細については、「[SQL Data Warehouse でのデータベース保護][Secure a database in SQL Data Warehouse]」を参照してください。
   > 
   > 
2. **SQL Data Warehouse ユーザーを作成する:** **SQL Data Warehouse** データベースへの接続を開き、次のコマンドを実行します。
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **アクセス許可を与える:** 次の例では、**SQL Data Warehouse** データベースに対する `CONTROL` を付与します。 データベース レベルので `CONTROL` は、SQL Server での db_owner に相当します。
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **リソース クラスのサイズを大きくする:** ワークロードの高い管理ロールにユーザーを追加するには、次のクエリを使用します。
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **リソース クラスのサイズを小さくする:** ワークロード管理ロールからユーザーを削除するには、次のクエリを使用します。
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > smallrc からユーザーを削除することはできません。
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>キューに配置されたクエリの検出とその他の DMV
`sys.dm_pdw_exec_requests` DMV を使用すると、同時実行キューで待機中のクエリを特定できます。 同時実行スロットを待機しているクエリは、 **中断**状態となります。

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

ワークロード管理ロールを確認するには、 `sys.database_principals`を使用します。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

次のクエリは、各ユーザーが割り当てられているロールを示します。

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse には、次の待機の種類があります。

* **LocalQueriesConcurrencyResourceType**: 同時実行スロットのフレームワークの外に配置されたクエリ。 DMV クエリと、 `SELECT @@VERSION` のようなシステム関数は、ローカル クエリの例です。
* **UserConcurrencyResourceType**: 同時実行スロットのフレームワーク内に配置されたクエリ。 エンドユーザー テーブルに対するクエリは、このリソースの種類を使用した例です。
* **DmsConcurrencyResourceType**: データ移動操作に起因する待機。
* **BackupConcurrencyResourceType**: この待機は、データベースがバックアップ中であることを示します。 この種類のリソースの最大値は 1 です。 同時に複数のバックアップが要求された場合は、他の要求はキューに配置されます。

`sys.dm_pdw_waits` DMV を使用すると、要求がどのリソースを待っているのかを調べることができます。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV では、特定のクエリによって使用されるリソースの待機のみが表示されます。 リソースの待機時間では、リソースが提供されるまでの時間のみが考慮されます。これに対し、シグナルの待機時間は、基になる SQL Server によりクエリが CPU にスケジュール設定されるまでの時間です。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV を使用すると、待機のこれまでの傾向を分析できます。

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>次のステップ
データベース ユーザーの管理とセキュリティの詳細については、「[SQL Data Warehouse でのデータベース保護][Secure a database in SQL Data Warehouse]」を参照してください。 大規模なリソース クラスを使用して、クラスター化された列ストア インデックスの品質を向上させる方法については、「 [セグメントの品質を向上させるためのインデックスの再構築]」を参照してください。

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[セグメントの品質を向上させるためのインデックスの再構築]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
