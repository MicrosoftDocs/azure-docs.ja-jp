---
title: Azure Cosmos DB への MongoDB アプリケーションの接続
description: Azure portal から接続文字列を取得して、MongoDB アプリを Azure Cosmos DB に接続する方法について説明します
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.reviewer: sngun
adobe-target: true
adobe-target-activity: DocsExp-A/B-384740-MongoDB-2.8.2021
adobe-target-experience: Experience B
adobe-target-content: ./connect-mongodb-account-experimental
ms.openlocfilehash: b3e10931307914f1471b8a6fbffd38953ee4717b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659479"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Azure Cosmos DB への MongoDB アプリケーションの接続
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB 接続文字列を使用して、MongoDB アプリを Azure Cosmos DB に接続する方法について説明します。 MongoDB アプリのデータ ストアとして Azure Cosmos データベースを使用できるようになります。

このチュートリアルでは、接続文字列情報を取得する 2 とおりの方法を説明します。

- [クイック スタートを使う方法](#get-the-mongodb-connection-string-by-using-the-quick-start)、.NET、Node.js、MongoDB シェル、Java、Python のドライバー向けの方法です
- [カスタム接続文字列を使う方法](#get-the-mongodb-connection-string-to-customize)。その他のドライバー向けの方法です

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 Azure アカウントがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を今すぐ作成できます。
- Cosmos アカウント。 手順については、「[Azure Cosmos DB の MongoDB 用 API と .NET SDK を使用して Web アプリを構築する](create-mongodb-dotnet.md)」を参照してください。

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>クイック スタートを使用して MongoDB 接続文字列を取得する

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、API を選択します。
3. [アカウント] ブレードの左側のウィンドウで、 **[クイック スタート]** をクリックします。
4. プラットフォームを選択します ( **.NET**、 **Node.js**、 **MongoDB シェル**、 **Java**、 **Python**)。 ドライバーまたはツールの一覧が表示されなくても、接続コード スニペットは継続的に文書化されますので心配はいりません。 表示を希望するものを下部のコメント欄に記入してください。 また、独自の接続を行う方法については、[アカウントの接続文字列情報の取得](#get-the-mongodb-connection-string-to-customize)に関するセクションを参照してください。
5. コード スニペットを MongoDB アプリにコピーして貼り付けます。

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="クイック スタート ブレード":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>MongoDB 接続文字列を取得してカスタマイズする

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、API を選択します。
3. [アカウント] ブレードの左側のウィンドウで、 **[接続文字列]** をクリックします。
4. **[接続文字列]** ブレードが開きます。 MongoDB のドライバーを使用してこのアカウントに接続するために必要なすべての情報 (あらかじめ構築された接続文字列も含む) が表示されます。

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="[接続文字列] ブレード" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>接続文字列の要件

> [!Important]
> Azure Cosmos DB には、厳密なセキュリティ要件と基準があります。 Azure Cosmos DB アカウントには、*TLS* による認証とセキュリティで保護された通信が必要です。

Azure Cosmos DB は、標準の MongoDB 接続文字列の URI 形式をサポートしていますが、固有の要件がいくつかあります。Azure Cosmos DB アカウントには、TLS による認証とセキュリティで保護された通信が必要です。 そのため接続文字列は次の形式となります。

`mongodb://username:password@host:port/[database]?ssl=true`

この文字列の値は、先ほど示した **[接続文字列]** ブレードで確認できます。

* ユーザー名 (必須):Cosmos アカウント名。
* パスワード (必須):Cosmos アカウントのパスワード。
* ホスト (必須):Cosmos アカウントの FQDN。
* ポート (必須):10255。
* データベース (省略可):接続で使用されるデータベース。 データベースを指定しない場合、既定のデータベースは "test" です。
* ssl=true (必須)

たとえば、 **[接続文字列]** ブレードに表示されているアカウントでは次のようになります。 有効な接続文字列は次のようになります。

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="driver-requirements"></a>ドライバーの要件

ワイヤ プロトコル バージョン 3.4 以上をサポートするすべてのドライバーで、MongoDB 用 Azure Cosmos DB API がサポートされます。

具体的には、クライアント ドライバーは、SNI (Service Name Identification) TLS 拡張と appName 接続文字列のどちらかまたは両方のオプションをサポートしている必要があります。 `appName` パラメーターが指定されている場合、Azure portal に見られる接続文字列値と同じように追加されている必要があります。

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。
