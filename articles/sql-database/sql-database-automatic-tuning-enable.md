---
title: "Azure SQL Database での自動チューニングの有効化 | Microsoft Docs"
description: "Azure SQL Database で自動チューニングを簡単に有効にすることができます。"
services: sql-database
documentationcenter: 
author: vvasic
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/19/2016
ms.author: vvasic
ms.openlocfilehash: 378e5a6a8cea80a551666c3692f8aa650620f66d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-automatic-tuning"></a>自動チューニングの有効化

Azure SQL Database は自動的に管理されるデータ サービスです。常にクエリを監視し、ワークロードのパフォーマンスを向上させるために実行できるアクションを識別します。 推奨事項を確認し、手動で適用できます。また、Azure SQL Database で自動的に是正措置を適用することもできます (**自動チューニング モード**ともいう)。 サーバーまたはデータベース レベルで自動チューニングを有効にすることができます。

## <a name="enable-automatic-tuning-on-server"></a>サーバーでの自動チューニングの有効化

Azure SQL Database サーバーで自動チューニングを有効にするには、Azure Portal でサーバーに移動し、メニューで **[自動チューニング]** を選択します。 次のように、有効にする自動チューニング オプションを選択し、**[適用]** を選択します。

![サーバー](./media/sql-database-automatic-tuning-enable/server.png)

サーバーの自動チューニング オプションは、サーバー上のすべてのデータベースに適用されます。 既定では、すべてのデータベースがその親サーバーから構成を継承しますが、これをオーバーライドし、各データベースに対して個別に指定することができます。

## <a name="configure-automatic-tuning-on-database"></a>データベースで自動チューニングを構成する

Azure Portal では、各データベースで自動チューニング構成を個別に指定することができます。

> [!NOTE]
> 一般的な推奨事項は、すべてのデータベースで同じ構成設定を自動的に適用できるように、サーバー レベルで自動チューニング構成を管理することです。 データベースが同じサーバーの他のデータベースとは異なる場合は、個々のデータベースで自動チューニングを構成します。
>

単一のデータベースで自動チューニングを有効にするには、Azure Portal でデータベースに移動し、**[自動チューニング]** を選択します。 オプションを選択して、サーバーから設定を継承するように単一のデータベースを構成することも、個別にデータベースの構成を指定することもできます。

![データベース](./media/sql-database-automatic-tuning-enable/database.png)

適切な構成を選択したら、**[適用]** をクリックします。

## <a name="next-steps"></a>次のステップ
* [自動チューニングに関する記事](sql-database-automatic-tuning.md)を読み、自動チューニングと、パフォーマンスの向上にいかに役立つかを確認します。
* Azure SQL Database のパフォーマンスに関する推奨事項の概要については、「[パフォーマンスに関する推奨事項](sql-database-advisor.md)」を参照してください。
* よく使用されるクエリによるパフォーマンスへの影響を確認する方法については、[クエリ パフォーマンスの洞察](sql-database-query-performance.md)に関する記事をご覧ください。