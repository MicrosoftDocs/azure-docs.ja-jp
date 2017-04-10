---
title: "DocumentDB アカウントの MongoDB 接続文字列 | Microsoft Docs"
description: "MongoDB 接続文字列を使用して MongoDB アプリを Azure DocumentDB アカウントに接続する方法を確認します。"
keywords: "mongodb 接続文字列"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 6d376b9db07a46c467bd772959de3f7acf9c123d
ms.lasthandoff: 03/15/2017


---

# <a name="connect-an-application-to-documentdb-api-for-mongodb"></a>アプリケーションを DocumentDB に接続する: MongoDB 用の API
MongoDB 接続文字列を使用して MongoDB アプリを Azure DocumentDB アカウントに接続する方法を確認します。 MongoDB アプリを Azure DocumentDB データベースに接続することで、DocumentDB データベースを MongoDB アプリのデータ ストアとして使用できます。 

このチュートリアルでは、接続文字列情報を取得する&2; とおりの方法を説明します。

- [クイック スタートを使う方法](#QuickstartConnection)。.NET、Node.js、MongoDB シェル、Java、Python のドライバー向けの方法です。
- [カスタム接続文字列を使う方法](#GetCustomConnection)。その他のドライバー向けの方法です。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 Azure アカウントがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を今すぐ作成できます。 
- DocumentDB アカウント。 手順については、「[MongoDB アプリで使用するための DocumentDB アカウントの作成](documentdb-create-mongodb-account.md)」を参照してください。

## <a id="QuickstartConnection"></a> クイック スタートを使用した MongoDB 接続文字列の取得
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[NoSQL (DocumentDB)]** ブレードで、DocumentDB: MongoDB アカウントの API を選択します。 
3. [アカウント] ブレードの**左側のナビゲーション** バーで、**[クイック スタート]** をクリックします。 
4. プラットフォームを選択します ("*.NET ドライバー*"、"*Node.js ドライバー*"、"*MongoDB シェル*"、"*Java ドライバー*"、"*Python ドライバー*")。 ドライバーまたはツールの一覧が表示されなくても、接続コード スニペットは継続的に文書化されますので心配はいりません。 表示を希望するものを下部のコメント欄に記入してください。また、独自の接続を行う方法については、[アカウントの接続文字列情報の取得](#GetCustomConnection)に関するセクションを参照してください。
5. コード スニペットを MongoDB アプリにコピーして貼り付ければ、準備は完了です。

    ![[クイック スタート] ブレードのスクリーン ショット](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> MongoDB 接続文字列を取得してカスタマイズする
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[NoSQL (DocumentDB)]** ブレードで、DocumentDB: MongoDB アカウントの API を選択します。 
3. [アカウント] ブレードの**左側のナビゲーション** バーで、**[接続文字列]** をクリックします。 
4. **[接続文字列情報]** ブレードが表示され、MongoDB のドライバーを使用してこのアカウントに接続するために必要なすべての情報 (あらかじめ構築された接続文字列も含む) が表示されます。

    ![Screen shot of the connection string blade](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>接続文字列の要件
> [!Important]
> DocumentDB には、厳密なセキュリティ要件や基準が存在します。 DocumentDB アカウントでは、**SSL** による認証と安全な通信が要求されます。
>
>

DocumentDB は、MongoDB の標準的な接続文字列の URI 形式をサポートしていますが、特殊な要件がいくつかあることに注意してください。たとえば DocumentDB アカウントには、SSL による安全な通信と認証が要求されます。  そのため接続文字列は次の形式となります。

    mongodb://username:password@host:port/[database]?ssl=true

この文字列の値は、先ほど示した [接続文字列] ブレードで確認できます。

* ユーザー名 (必須)
  * DocumentDB アカウント名
* パスワード (必須)
  * DocumentDB アカウントのパスワード
* ホスト (必須)
  * DocumentDB アカウントの FQDN
* ポート (必須)
  * 10250
* データベース (省略可)
  * 対応する接続で使用される既定のデータベース (データベースが指定されていない場合は、既定のデータベースは "test" になります)
* ssl=true (必須)

たとえば、上の [接続文字列情報] に表示されているアカウントの場合、  有効な接続文字列は次のようになります。

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>次のステップ
* DocumentDB: MongoDB 用 API アカウントで [MongoChef を使用](documentdb-mongodb-mongochef.md)する方法を説明します。
* DocumentDB: MongoDB 用 API の[サンプル](documentdb-mongodb-samples.md)を確認します。

