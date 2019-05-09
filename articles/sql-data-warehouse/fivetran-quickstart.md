---
title: Azure SQL Data Warehouse の Fivetran クイック スタート | Microsoft Docs
description: Fivetran と Azure SQL Data Warehouse の使用をすぐに開始します。
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 1d8b6d2df21fe0aabe85754e061db4deb129b32e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895704"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Fivetran と Azure SQL Data Warehouse の使用をすぐに開始する

このクイック スタートでは、Azure SQL Data Warehouse を使用する新しい Fivetran ユーザーを設定する方法について説明します。 この記事では、SQL Data Warehouse インスタンスが既に用意されていることを前提としています。

## <a name="set-up-a-connection"></a>接続を設定する

1. Azure SQL Data Warehouse に接続するために使用する完全修飾サーバー名とデータベース名を検索します。
    
    この情報を見つける方法については、「[Azure SQL Data Warehouse への接続](sql-data-warehouse-connect-overview.md)」を参照してください。

2. セットアップ ウィザードで、データベースの接続方法として直接接続か SSH トンネルの使用を選択します。

   データベースに直接接続する場合は、アクセスを許可するファイアウォール ルールを作成する必要があります。 この方法は、最も簡単かつ最も安全な方法です。

   SSH トンネルを使用して接続を選択する場合は、Fivetran はネットワーク上の別のサーバーに接続します。 このサーバーがデータベースへの SSH トンネルを提供します。 仮想ネットワーク上のアクセスできないサブネット内にデータベースがある場合、この方法を使用する必要があります。

3. IP アドレス **52.0.2.4** をサーバー レベルのファイアウォールに追加し、Fivetran から SQL Data Warehouse インスタンスに入ってくる接続を許可します。

   詳細については、「[サーバーレベルのファイアウォール規則を作成する](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)」を参照してください。

## <a name="set-up-user-credentials"></a>ユーザー資格情報を設定する

1. SQL Server Management Studio または任意のツールを利用し、自分の Azure SQL Data Warehouse に接続します。 サーバー管理者ユーザーとしてサインインします。 次に、次の SQL コマンドを実行し、Fivetran のユーザーを作成します。
    - マスター データベースで: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - SQL Data Warehouse データベースの場合:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. ウェアハウスに対する次のアクセス許可を Fivetran ユーザーに付与します。

    ```
    GRANT CONTROL to fivetran;
    ```

    ユーザーが PolyBase を使用して Azure Blob Storage からファイルを読み込むときに使用されるデータベース スコープ資格情報を作成するには、CONTROL アクセス許可が必要です。

3. 適切なリソース クラスを Fivetran ユーザーに追加します。 使用するリソース クラスは、列ストア インデックスの作成に必要なメモリによって異なります。 たとえば、Marketo や Salesforce などの製品と統合するには、たくさんの列と大量のデータが使用されるため、上位のリソース クラスが必要になります。 上位のリソース クラスは、列ストア インデックスの作成により多くのメモリを必要とします。

    静的リソース クラスを使用することをお勧めします。 `staticrc20` リソース クラスから始めることができます。 `staticrc20` リソース クラスでは、ご利用のパフォーマンス レベルに関係なく、ユーザーごとに 200 MB が割り当てられます。 列ストア インデックスが最初のリソース クラス レベルで失敗した場合、リソース クラスを上げます。

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    詳細については、[メモリとコンカレンシーの制限](memory-and-concurrency-limits.md)と[リソース クラス](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)のドキュメントをご覧ください。


## <a name="sign-in-to-fivetran"></a>Fivetran にサインインする

Fivetran にサインインするには、SQL Data Warehouse へのアクセスに使用する資格情報を入力します。 

* ホスト (自分のサーバーの名前)。
* ポート。
* データベース。
* ユーザー (ユーザー名は **fivetran\@_server_name_** のようになり、*server_name* は Azure ホスト URI に含まれます: ***server_name*.database.windows.net**)。
* パスワード。
