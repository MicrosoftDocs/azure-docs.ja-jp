---
title: Azure Cosmos DB エクスプローラーを使用してデータを管理する
description: Azure Cosmos DB エクスプローラーは、スタンドアロンの Web ベースのインターフェイスで、Azure Cosmos DB に格納されたデータを表示および管理することができます。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: dech
ms.openlocfilehash: d50bf7d865cb286f2a1421156b477dc92dc978d1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453605"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Azure Cosmos Explorer を使ってデータを操作する 

Azure Cosmos DB エクスプローラーは、スタンドアロンの Web ベースのインターフェイスで、Azure Cosmos DB に格納されたデータを表示および管理することができます。 Azure Cosmos DB エクスプローラーは、Azure Cosmos DB アカウントを作成するときに Azure portal で使用できる既存の **[データ エクスプローラー]** タブと同じです。 既存のデータ エクスプローラーよりも優れた Azure Cosmos DB エクスプローラーの主な利点は、次のとおりです。

* データの表示、クエリの実行、ストアド プロシージャ、トリガー、およびそれらの結果を表示するための全画面表示があります。  

* データベース アカウントとそのコレクションへの一時的または永続的な読み取り/書き込みアクセスを、Azure portal へのアクセスまたはサブスクリプションを持たない他のユーザーに提供することができます。  

* クエリの結果は、Azure portal へのアクセスまたはサブスクリプションを持たない他のユーザーと共有できます。  

## <a name="access-azure-cosmos-db-explorer"></a>Azure Cosmos DB エクスプローラーへのアクセス

1. [Azure portal](https://portal.azure.com/) にサインインします。 

2. **[すべてのリソース]** から、自分の Azure Cosmos DB アカウントを見つけて移動し、キーを選択して、**プライマリ接続文字列**をコピーします。  

3. https://cosmos.azure.com/ にアクセスし、接続文字列を貼り付けて、**[接続]** を選択します。 接続文字列を使用すると、時間制限なく Azure Cosmos DB エクスプローラーにアクセスできます。  

   自分の Azure Cosmos DB アカウントへの一時的なアクセスを他のユーザーに提供するには、読み取り/書き込みアクセス URL および読み取りアクセス URL を使用ます。 

4. **データ エクスプローラー** ブレードを開き、**[全画面表示で開く]** を選択します。 ポップアップ ダイアログから、**読み取り/書き込み**と**読み取り** の 2 つのアクセス URL を表示できます。 これらの URL を使用すると、自分の Azure Cosmos DB アカウントを一時的に他のユーザーと共有することができます。 アカウントへのアクセスの有効期限は 24 時間です。その後は、新しいアクセス URL または接続文字列を使用して再接続できます。 

   **読み取り/書き込み**: 読み取り/書き込み URL を他のユーザーと共有すると、そのユーザーはデータベース、コレクション、クエリ、およびその特定のアカウントに関連付けられているその他のリソースを表示および変更することができます。

   **読み取り**: 読み取り専用の URL を他のユーザーと共有すると、そのユーザーはデータベース、コレクション、クエリ、およびその特定のアカウントに関連付けられているその他のリソースを表示することができます。 たとえば、クエリの結果を Azure portal または Azure Cosmos DB アカウントへのアクセス権を持たない他のチーム メンバーと共有する場合は、この URL で提供することができます。

   アカウントを開くためのアクセスの種類を選択し、**[開く]** をクリックします。 エクスプローラーが開いた後は、Azure portal の [データ エクスプローラー] タブでのエクスペリエンスと同じです。   

   ![Azure Cosmos DB エクスプローラーを開く](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>既知の問題

現在、一時的な読み取り/書き込みまたは読み取りアクセスの共有を可能にする **[全画面表示で開く]** エクスペリエンスは、Azure Cosmos DB Gremlin と Table API アカウントではまだサポートされていません。 ただし、Azure Cosmos DB エクスプローラーに接続文字列を渡すことによって、Gremlin と Table API アカウントを表示することができます。 

## <a name="next-steps"></a>次の手順
Azure Cosmos DB エクスプローラーでのデータの管理方法を学んだので、次の段階に進みましょう。

* SQL 構文を使用して[クエリ](sql-api-query-reference.md)の定義を開始し、ストアド プロシージャ、UDF、トリガーを使用して[サーバー側プログラミング](stored-procedures-triggers-udfs.md)を実行します。 
