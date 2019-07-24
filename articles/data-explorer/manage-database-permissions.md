---
title: Azure Data Explorer のデータベース アクセス許可を管理する
description: この記事では、Azure データ エクスプローラーにおけるデータベースとテーブルに対するロールベースのアクセス制御について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 36e1bb77be1e825e42f0e5d25457214a8b5f882d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045670"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Azure データ エクスプローラーのデータベース アクセス許可を管理する

Azure データ エクスプローラーを使用すると、"*ロールベースのアクセス制御*" モデルを使用して、データベースとテーブルへのアクセスを制御することができます。 このモデルでは、"*プリンシパル*" (ユーザー、グループ、およびアプリ) が "*ロール*" にマッピングされます。 プリンシパルは、割り当てられたロールに従ってリソースにアクセスできます。

この記事では、使用できるロールと、Azure portal と Azure Data Explorer の管理コマンドを使用して、これらのロールにプリンシパルを割り当てる方法について説明します。

## <a name="roles-and-permissions"></a>ロールとアクセス許可

Azure データ エクスプローラーには、次のロールがあります。

|Role                       |アクセス許可                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|データベース管理者             |特定のデータベースのスコープ内で何でも実行することができます。|
|データベース ユーザー              |データベース内のすべてのデータとメタデータを読み取ることができます。 さらに、データベース内でテーブルを作成し (そのテーブルのテーブル管理者になり)、機能を作成することができます。|
|データベース表示者            |データベース内のすべてのデータとメタデータを読み取ることができます。|
|データベース取り込み者          |データベース内の既存のすべてのテーブルにデータを取り込むことはできますが、データを照会することはできません。|
|データベース監視者           |データベースおよびその子エンティティのコンテキストで ".show ..." コマンドを実行することができます。|
|テーブル管理者                |特定のテーブルのスコープ内で何でも実行することができます。 |
|テーブル取り込み者             |特定のテーブルのスコープ内でデータを取り込むことはできますが、データを照会することはできません。|

## <a name="manage-permissions-in-the-azure-portal"></a>Azure portal でアクセス許可を管理する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. Azure データ エクスプローラー クラスターに移動します。

1. **[概要]** セクションで、アクセス許可を管理するデータベースを選択します。

    ![データベースの選択](media/manage-database-permissions/select-database.png)

1. **[アクセス許可]** を選択し、**[追加]** を選択します。

    ![データベースのアクセス許可](media/manage-database-permissions/database-permissions.png)

1. **[Add database permissions]\(データベース アクセス許可の追加\)** でプリンシパルを割り当てるロールを選択し、**[Select principals]\(プリンシパルの選択\)** を選択します。

    ![データベースのアクセス許可を追加する](media/manage-database-permissions/add-permission.png)

1. プリンシパルを探して選択し、**[選択]** を選択します。

    ![Azure portal でアクセス許可を管理する](media/manage-database-permissions/new-principals.png)

1. **[保存]** を選択します。

    ![Azure portal でアクセス許可を管理する](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>管理コマンドを使用してアクセス許可を管理する

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインし、クラスターを追加します (まだ利用可能になっていない場合)。

1. 左側のウィンドウで、適切なデータベースを選択します。

1. `.add` コマンドを使用して、`.add database databasename rolename ('aaduser | aadgroup=user@domain.com')` のようにプリンシパルをロールに割り当てます。 ユーザーをデータベース ユーザー ロールに追加するには、次のコマンドを実行します。データベース名とユーザーは実際のものに置き換えます。

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    コマンドの出力は、既存のユーザーと、データベース内でそれらに割り当てられたロールの一覧を示します。

## <a name="next-steps"></a>次の手順

[クエリを作成する](write-queries.md)
