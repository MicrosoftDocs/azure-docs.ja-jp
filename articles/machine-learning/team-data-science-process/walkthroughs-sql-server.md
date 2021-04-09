---
title: R、Python、T-SQL を使用した SQL Server での分析 - Team Data Science Process
description: SQL Server 上で R、Python、T-SQL を使用して予測分析を行う例を示します。
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
ms.openlocfilehash: a8789050257b8bc67649233760baefe7e2ee9adf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93320150"
---
# <a name="sql-server-data-science-walkthroughs-using-r-python-and-t-sql"></a>R、Python、T-SQL を使用した SQL Server データ サイエンスのチュートリアル

ここでは、SQL Server、SQL Server R Services、SQL Server Python Services を使って予測分析を行うチュートリアルを紹介します。 R と Python のコードは、ストアド プロシージャでデプロイします。 Team Data Science Process で概説された手順を踏襲しています。 Team Data Science Process の概要については、[データ サイエンス プロセス](overview.md)に関するページを参照してください。 

Team Data Science Process を実行するデータ サイエンスのチュートリアルは他にも存在し、使用する **プラットフォーム** ごとにグループ化されています。 これらの例の箇条書きについては、[Team Data Science Process を実行するチュートリアル](walkthroughs.md)に関するページをご覧ください。


## <a name="predict-taxi-tips-using-python-and-sql-queries-with-sql-server"></a>Python、SQL クエリ、SQL Server を使ってタクシーのチップを予測する 

[SQL Server の使用](sql-walkthrough.md)に関するチュートリアルでは、機械学習の分類および回帰モデルの構築とデプロイを行う方法を説明します。  データは、一般公開されている NYC タクシーの乗車と料金のデータセットです。


## <a name="predict-taxi-tips-using-microsoft-r-with-sql-server"></a>Microsoft R と SQL Server を使ってタクシーのチップを予測する 

[SQL Server R Services の使用](/sql/machine-learning/tutorials/walkthrough-data-science-end-to-end-walkthrough)に関するチュートリアルでは、R モデルを構築して SQL Server にデプロイする方法を説明します。 このチュートリアルの目的は、R 開発者に R Services (In-Database) を紹介することです。


## <a name="predict-taxi-tips-using-r-from-t-sql-or-stored-procedures-with-sql-server"></a>SQL Server で T-SQL またはストアド プロシージャから R を使ってタクシーのチップを予測する

[R と SQL Server を対象としたデータ サイエンスのチュートリアル](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)は、SQL プログラマ向けに、SQL Server R Services で Transact-SQL を使用した高度な分析ソリューション構築の体験を提供して、R ソリューションを扱えるようにします。 


## <a name="predict-taxi-tips-using-python-in-sql-server-stored-procedures"></a>SQL Server のストアド プロシージャで Python を使ってタクシーのチップを予測する

[SQL Server Python Services で T-SQL を使用する](/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers)に関するチュートリアルでは、SQL プログラマが SQL Server で機械学習ソリューションの構築を体験できます。 Python コードをストアド プロシージャに追加することによって、Python をアプリケーションに組み込む方法を示しています。


## <a name="next-steps"></a>次のステップ

Team Data Science Process を構成する主な要素については、[Team Data Science Process の概要](overview.md)に関するページを参照してください。

データ サイエンス プロジェクトの構築に使用できる Team Data Science Process のライフサイクルについては、「[Team Data Science Process ライフサイクル](lifecycle.md)」を参照してください。 このライフサイクルは、プロジェクトを実行する際に、その開始から終了までにわたって進められる通常のステップを大まかにまとめたものです。