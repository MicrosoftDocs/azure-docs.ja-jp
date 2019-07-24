---
title: サーバーレベルのファイアウォール規則を作成する - Azure SQL Database | Microsoft Docs
description: 単一データベースおよびプールされたデータベースのために、SQL Database のサーバーレベルのファイアウォール規則を作成します
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: f708e5a3cd5bc0f11f8b0cfe79a791347c7a7a2b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108961"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して単一データベースおよびプールされたデータベースに対するサーバーレベルのファイアウォール規則を作成する

このクイック スタートでは、Azure portal を使用して、Azure SQL Database の単一データベースおよびプールされているデータベースに対する[サーバーレベルのファイアウォール規則](sql-database-firewall-configure.md)を作成し、データベース サーバー、単一データベース、エラスティック プールとそのデータベースに接続できるようにする方法を説明します。 他の Azure リソースやオンプレミスのリソースから接続するためには、ファイアウォール規則が必要となります。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、[Azure portal を使用した単一データベースの作成](sql-database-single-database-get-started.md)に関するページで作成したリソースを出発点として使用します。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-server-level-ip-firewall-rule"></a>サーバーレベルの IP ファイアウォール規則を作成する

SQL Database サービスでは、単一データベースおよびプールされたデータベースに対して、データベース サーバー レベルでファイアウォールが作成されます。 このファイアウォールは、ファイアウォールを開くための IP ファイアウォール規則が作成されない限り、クライアント アプリケーションがサーバーまたはその単一データベースやプールされたデータベースに接続できないようにします。 Azure の外部 IP アドレスからの接続については、接続できるようにする特定の IP アドレスまたはアドレス範囲を対象とするファイアウォール規則を作成します。 サーバーレベルおよびデータベースレベルの IP ファイアウォール規則の詳細については、[SQL Database のサーバーレベルおよびデータベースレベルの IP ファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。

> [!NOTE]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
> [!IMPORTANT]
> 0.0.0.0 のファイアウォール規則では、すべての Azure サービスがサーバーレベルのファイアウォール規則をパススルーし、サーバーを介して単一データベースまたはプールされたデータベースへの接続を試行できます。 仮想ネットワーク規則の使用については、「[IP 規則に代わる手段としての仮想ネットワーク規則](sql-database-firewall-configure.md#virtual-network-rules-as-alternatives-to-ip-rules)」を参照してください。

以下の手順に従い、クライアントの IP アドレスに対してサーバーレベルの IP ファイアウォール規則を作成し、その IP アドレスのみが SQL Database ファイアウォールを介して外部接続できるようにします。

1. [前提条件の Azure SQL データベース](#prerequisites)のデプロイが完了したら、左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで **mySampleDatabase** を選択します。 このデータベースの概要ページが開くと、完全修飾サーバー名 (**mynewserver-20170824.database.windows.net** など) や追加の構成オプションが表示されます。

2. この完全修飾サーバー名をコピーします。これは他のクイック スタートでサーバーとそのデータベースに接続するときに使用します。

   ![サーバー名](./media/sql-database-get-started-portal/server-name.png)

3. ツール バーで **[サーバー ファイアウォールの設定]** を選択します。 データベース サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーレベルの IP ファイアウォール規則](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. ツール バーの **[クライアント IP の追加]** を選択し、現在の IP アドレスを新しいサーバーレベルの IP ファイアウォール規則に追加します。 サーバーレベルの IP ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

   > [!IMPORTANT]
   > 既定では、すべての Azure サービスで、SQL Database ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対して無効にするには、このページの **[オフ]** を選択します。
   >

5. **[保存]** を選択します。 SQL Database サーバー上のポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルの IP ファイアウォール規則が作成されます。

6. **[ファイアウォール設定]** ページを閉じます。

SQL Server Management Studio やその他の任意のツールを使用して、SQL Database サーバーとそのデータベースに、前に作成したサーバー管理者アカウントを使用してこの IP アドレスから接続できるようになりました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

データベースに接続してクエリを実行するためのさまざまな方法を紹介した「[次のステップ](#next-steps)」に進む場合は、これらのリソースを保存しておいてください。 一方、このクイック スタートで作成したリソースを削除する場合は、次の手順に従います。

1. Azure portal の左側のメニューで、**[リソース グループ]**、**[myResourceGroup]** の順に選択します。
2. リソース グループのページで **[削除]** を選択し、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

- これで、データベースが作成されたので、任意のツールまたは言語を使用して[接続し、クエリを実行](sql-database-connect-query.md)できます。たとえば、以下が可能です。
  - [SQL Server Management Studio を使用して接続およびクエリを実行する](sql-database-connect-query-ssms.md)
  - [Azure Data Studio を使用して接続およびクエリを実行する](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 初めてのデータベースを設計し、テーブルを作成して、データを挿入する方法については、次のいずれかのチュートリアルを参照してください。
  - [Azure SQL Database で SSMS を使用して最初の単一データベースを設計する](sql-database-design-first-database.md)
  - [Azure SQL Database に単一データベースを設計し、C# と ADO.NET で接続する](sql-database-design-first-database-csharp.md)
