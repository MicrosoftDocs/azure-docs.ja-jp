---
title: Azure Cosmos DB への MongoDB アプリケーションの接続
description: MongoDB アプリを Azure Cosmos DB に接続する方法について説明します。
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 737e179c2c16937d00bc9b6601f12ebe392c1906
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040321"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Azure Cosmos DB への MongoDB アプリケーションの接続
MongoDB 接続文字列を使用して、MongoDB アプリを Azure Cosmos DB に接続する方法について説明します。 MongoDB アプリのデータ ストアとして Azure Cosmos DB データベースを使用できるようになります。 

このチュートリアルでは、接続文字列情報を取得する 2 とおりの方法を説明します。

- [クイック スタートを使う方法](#QuickstartConnection)。.NET、Node.js、MongoDB シェル、Java、Python のドライバー向けの方法です
- [カスタム接続文字列を使う方法](#GetCustomConnection)。その他のドライバー向けの方法です

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 Azure アカウントがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を今すぐ作成できます。 
- Cosmos アカウント。 手順については、「[Azure Cosmos DB の MongoDB 用 API と .NET SDK を使用して Web アプリを構築する](create-mongodb-dotnet.md)」を参照してください。

## <a id="QuickstartConnection"></a> クイック スタートを使用した MongoDB 接続文字列の取得
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、API を選択します。 
3. [アカウント] ブレードの左側のウィンドウで、**[クイック スタート]** をクリックします。 
4. プラットフォームを選択します (**.NET**、 **Node.js**、 **MongoDB シェル**、 **Java**、 **Python**)。 ドライバーまたはツールの一覧が表示されなくても、接続コード スニペットは継続的に文書化されますので心配はいりません。 表示を希望するものを下部のコメント欄に記入してください。 また、独自の接続を行う方法については、[アカウントの接続文字列情報の取得](#GetCustomConnection)に関するセクションを参照してください。
5. コード スニペットを MongoDB アプリにコピーして貼り付けます。

    ![クイック スタート ブレード](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> MongoDB 接続文字列を取得してカスタマイズする
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、API を選択します。 
3. [アカウント] ブレードの左側のウィンドウで、**[接続文字列]** をクリックします。 
4. **[接続文字列]** ブレードが開きます。 MongoDB のドライバーを使用してこのアカウントに接続するために必要なすべての情報 (あらかじめ構築された接続文字列も含む) が表示されます。

    ![[接続文字列] ブレード](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>接続文字列の要件
> [!Important]
> Azure Cosmos DB には、厳密なセキュリティ要件と基準があります。 Azure Cosmos DB アカウントでは、*SSL* による認証とセキュリティで保護された通信が要求されます。 
>
>

Azure Cosmos DB は、標準の MongoDB 接続文字列の URI 形式をサポートしていますが、固有の要件がいくつかあります。Azure Cosmos DB アカウントには、認証と、SSL 経由のセキュリティで保護された通信が必要です。 そのため接続文字列は次の形式となります。

    mongodb://username:password@host:port/[database]?ssl=true

この文字列の値は、先ほど示した **[接続文字列]** ブレードで確認できます。

* ユーザー名 (必須):Cosmos アカウント名。
* パスワード (必須):Cosmos アカウントのパスワード。
* ホスト (必須):Cosmos アカウントの FQDN。
* ポート (必須):10255。
* データベース (省略可):接続で使用されるデータベース。 データベースを指定しない場合、既定のデータベースは "test" です。
* ssl=true (必須)

たとえば、**[接続文字列]** ブレードに表示されているアカウントでは次のようになります。 有効な接続文字列は次のようになります。

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>次の手順

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。
