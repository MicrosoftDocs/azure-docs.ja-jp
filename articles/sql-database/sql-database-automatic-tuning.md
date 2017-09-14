---
title: "SQL Database - 自動チューニング | Microsoft Docs"
description: "SQL Database は SQL クエリを分析し、ユーザーのワークロードに自動的に適応します。"
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>自動チューニング

Azure SQL Database は完全に管理されたデータ サービスです。データベースで実行されるクエリを監視し、ワークロードのパフォーマンスを自動的に向上させることができます。 Azure SQL Database にはインテリジェンス メカニズムが組み込まれており、ワークロードにデータベースを動的に適応することで、クエリのパフォーマンスを自動的に調整して向上させることができます。 Azure SQL Database の自動チューニングは、クエリのパフォーマンスを最適化するために Azure SQL Database で有効にできる最も重要な機能の 1 つかもしれません。

こちらの記事で、Azure Portal を使用して[自動チューニングを有効にする](sql-database-automatic-tuning-enable.md)手順を確認してださい。

## <a name="why-automatic-tuning"></a>自動チューニングを行う理由

従来のデータベース管理の主なタスクの 1 つは、ワークロードを監視し、重要な SQL クエリ、パフォーマンスを向上させるために追加する必要があるインデックス、およびほとんど使用されないインデックスを識別することです。 Azure SQL Database は、監視する必要があるクエリとインデックスの詳細な洞察を提供します。 ただし、データベースを常に監視することは厄介で面倒なタスクであり、多数のデータベースを処理する場合は特にそうなります。 膨大な数のデータベースを管理することは、Azure SQL Database と Azure Portal が備えているすべての使用可能なツールとレポートを使用しても、効率的に実行するのは不可能な場合があります。 データベースの監視とチューニングを手動で行う代わりに、自動チューニング機能を使用して、監視とチューニング アクションの一部を Azure SQL Database に委任することを検討できます。 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>自動チューニングのしくみ

Azure SQL Database ではパフォーマンスの監視と分析プロセスが継続的に行われ、ワークロードの特性を常に学習し、潜在的な問題と改善点を特定します。

![自動チューニング プロセス](./media/sql-database-automatic-tuning/tuning-process.png)

このプロセスでは、Azure SQL Database は、ワークロードのパフォーマンスを向上させる可能性のあるインデックスとプラン、およびワークロードに影響するインデックスを検出し、ワークロードに動的に適応できます。 これらの検出結果に基づき、自動チューニングでは、ワークロードのパフォーマンスを向上させるチューニング アクションを適用します。 さらに、Azure SQL Database では、ワークロードのパフォーマンスが向上していることを確認するために、自動チューニングによる変更後のパフォーマンスを継続的に監視します。 パフォーマンスが向上しなかったすべてのアクションは自動的に元に戻されます。 この検証プロセスは、自動チューニングによる変更がワークロードのパフォーマンスを低下させていないことを確認する主要機能です。

Azure SQL Database では、次の 2 つの自動チューニングを使用できます。

 -  **自動インデックス管理**: データベースに追加するインデックスと削除するインデックスを識別します。
 -  **プランの自動修正**: (近日公開予定。SQL Server 2017 では既に利用可能) 問題のあるプランを識別し、SQL プランのパフォーマンスに関する問題を修正します。

## <a name="automatic-index-management"></a>インデックスの自動管理

Azure SQL Database では、インデックスを簡単に管理できます。Azure SQL Database はワークロードについて学習し、常にデータに最適にインデックが付けられていることを確認しているからです。 適切なインデックスの設計は、ワークロードの最適なパフォーマンスにとって不可欠であり、インデックスの自動管理はインデックスを最適化するのに役立ちます。 インデックスの自動管理では、正しくインデックスが付けられていないデータベースでのパフォーマンスの問題を修正することも、既存のデータベース スキーマのインデックスを維持および改善することもできます。 

### <a name="why-do-you-need-index-management"></a>インデックスの管理が必要な理由

インデックスはテーブルからデータを読み取るクエリの一部を高速化しますが、データを更新するクエリの速度が低下する場合があります。 インデックスを作成するタイミングと、インデックスに含める必要がある列を慎重に分析する必要があります。 一部のインデックスはしばらくすると必要でなくなる場合があります。 したがって、利点が得られないインデックスを定期的に識別して削除する必要があります。 未使用のインデックスを無視すると、データを読み取るクエリの利点が得られず、データを更新するクエリのパフォーマンスが低下します。 また、未使用のインデックスはシステムの全体のパフォーマンスに影響します。これは、追加の更新に不要なログが必要になるためです。

テーブルからデータを読み取るクエリのパフォーマンスを向上させ、更新への影響を最小限に抑える最適なインデックスのセットを見つけるには、継続的で複雑な分析が必要になる場合があります。

Azure SQL Database では組み込みのインテリジェンスで高度な規則を使用します。この規則では、クエリを分析し、現在のワークロードに最適なインデックスを識別し、インデックが削除される可能性があります。 Azure SQL Database は、データを読み取るクエリを最適化する必要最小限のインデックス セットがあり、他のクエリへの影響が最小限に抑えられていることを確認します。

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>データベースで変更する必要があるインデックスの識別方法

Azure SQL データベースはインデックスの管理プロセスを簡単にします。 手動のワークロード分析やインデックスの監視といった手間のかかるプロセスの代わりに、Azure SQL Database はワークロードを分析し、新しいインデックスでより迅速に実行できるクエリを識別し、未使用または重複するインデックスを識別します。 変更する必要があるインデックスの識別の詳細については、「[Azure Portal を使用した SQL Database Advisor](sql-database-advisor-portal.md)」参照してください。

### <a name="automatic-index-management-considerations"></a>インデックスの自動管理に関する考慮事項

組み込みの規則でデータベースのパフォーマンスが向上することがわかった場合は、Azure SQL Database でインデックスを自動的に管理することができます。

Azure SQL Database で必要なインデックスを作成するのに必要なアクションはリソースを消費し、一時的にワークロードのパフォーマンスに影響する可能性があります。 インデックスの作成によるワークロードのパフォーマンスへの影響を最小限に抑えるために、Azure SQL Database はインデックス管理操作に適した時間枠を見つけます。 チューニング アクションは、データベースでワークロードを実行するためのリソースが必要な場合は延期され、メンテナンス タスクに使用できる十分な未使用のリソースがデータベースにあるときは開始されます。 インデックスの自動管理の 1 つの重要な機能は、アクションの検証です。 Azure SQL Database がインデックスを作成または削除すると、監視プロセスでワークロードのパフォーマンスが分析され、アクションでパフォーマンスが向上したことが検証されます。 大幅に向上しなかった場合、アクションはすぐに元に戻されます。 このように、Azure SQL Database は、自動アクションがワークロードのパフォーマンスに悪い影響を与えないようにします。 自動チューニングによって作成されたインデックスは、基になるスキーマでのメンテナンス操作に対して透過的です。 自動的に作成されたインデックスがあることで、列の削除や名前変更などのスキーマ変更がブロックされることはありません。 Azure SQL Database で自動的に作成されたインデックスは、関連するテーブルまたは列が削除されるとすぐに削除されます。

## <a name="automatic-plan-choice-correction"></a>自動プラン選択の修正

自動プラン修正は、不適切な動作をする SQL プランを識別する、SQL Server 2017 CTP2.0 で追加された新しい自動チューニング機能です。 この自動チューニング オプションは、Azure SQL データベースで近日公開予定です。 詳細については、[SQL Server 2017 の自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- Azure SQL Database で自動チューニングを有効にして、自動チューニング機能でワークロードを完全に管理するには、「[Enable automatic tuning](sql-database-automatic-tuning-enable.md)」 (自動チューニングを有効にする) を参照してください。
- 手動チューニングを使用するには、[Azure Portal のチューニングに関する推奨事項](sql-database-advisor-portal.md)を確認し、クエリのパフォーマンスを向上させるものを手動で適用します。

