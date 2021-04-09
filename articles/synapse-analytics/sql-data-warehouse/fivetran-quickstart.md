---
title: 'クイックスタート: Fivetran と専用 SQL プール (旧称 SQL DW)'
description: Fivetran と Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) を使って作業を開始します。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f332c3b0bd53d80d4a8471f53c56ecab611787c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96456373"
---
# <a name="quickstart-fivetran-with-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>クイックスタート: Fivetran と Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) 

このクイック スタートでは、専用 SQL プール (旧称 SQL DW) と連携するように新しい Fivetran ユーザーを設定する方法について説明します。 この記事では、専用 SQL プール (旧称 SQL DW) が既にあることを前提としています。

## <a name="set-up-a-connection"></a>接続を設定する

1. 専用 SQL プール (旧称 SQL DW) に接続するために使用する完全修飾サーバー名とデータベース名を検索します。
    
    この情報を見つける方法については、[専用 SQL プール (旧称 SQL DW) への接続](sql-data-warehouse-connection-strings.md)に関する記事を参照してください。

2. セットアップ ウィザードで、データベースの接続方法として直接接続か SSH トンネルの使用を選択します。

   データベースに直接接続する場合は、アクセスを許可するファイアウォール ルールを作成する必要があります。 この方法は、最も簡単かつ最も安全な方法です。

   SSH トンネルを使用して接続することを選択した場合、Fivetran によってネットワーク上の別のサーバーに接続されます。 このサーバーがデータベースへの SSH トンネルを提供します。 仮想ネットワーク上のアクセスできないサブネット内にデータベースがある場合、この方法を使用する必要があります。

3. IP アドレス **52.0.2.4** をサーバー レベルのファイアウォールに追加し、Fivetran から専用 SQL プール (旧称 SQL DW) インスタンスへの着信接続を許可します。

   詳細については、「[サーバーレベルのファイアウォール規則を作成する](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)」を参照してください。

## <a name="set-up-user-credentials"></a>ユーザー資格情報を設定する

1. SQL Server Management Studio (SSMS) または任意のツールを使用して、専用 SQL プール (旧称 SQL DW) に接続します。 サーバー管理者ユーザーとしてサインインします。 次に、次の SQL コマンドを実行し、Fivetran のユーザーを作成します。

    - マスター データベースで: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - 専用 SQL プール (旧称 SQL DW) データベースで:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. 専用 SQL プール (旧称 SQL DW) に対する次のアクセス許可を Fivetran ユーザーに付与します。

    ```sql
    GRANT CONTROL to fivetran;
    ```

    ユーザーが PolyBase を使用して Azure Blob Storage からファイルを読み込むときに使用されるデータベース スコープ資格情報を作成するには、CONTROL アクセス許可が必要です。

3. 適切なリソース クラスを Fivetran ユーザーに追加します。 使用するリソース クラスは、列ストア インデックスの作成に必要なメモリによって異なります。 たとえば、Marketo や Salesforce などの製品と統合するには、たくさんの列と大量のデータが使用されるため、上位のリソース クラスが必要になります。 上位のリソース クラスは、列ストア インデックスの作成により多くのメモリを必要とします。

    静的リソース クラスを使用することをお勧めします。 `staticrc20` リソース クラスから始めることができます。 `staticrc20` リソース クラスでは、ご利用のパフォーマンス レベルに関係なく、ユーザーごとに 200 MB が割り当てられます。 列ストア インデックスが最初のリソース クラス レベルで失敗した場合、リソース クラスを上げます。

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    詳細については、[メモリとコンカレンシーの制限](memory-concurrency-limits.md)と[リソース クラス](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)のドキュメントをご覧ください。


## <a name="connect-from-fivetran"></a>Fivetran からの接続

Fivetran アカウントから専用 SQL プール (旧称 SQL DW) に接続するには、専用 SQL プール (旧称 SQL DW) へのアクセスに使用する資格情報を入力します。 

* ホスト (自分のサーバーの名前)。
* ポート。
* データベース。
* ユーザー (ユーザー名は **fivetran\@_server_name_** のようになり、*server_name* は Azure ホスト URI に含まれます: **_server\_name_.database.windows.net**)。
* パスワード。
