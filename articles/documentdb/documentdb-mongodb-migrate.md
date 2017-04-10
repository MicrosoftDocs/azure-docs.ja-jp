---
title: "Azure DocumentDB で mongoimport と mongorestore を使用する | Microsoft Docs"
description: "mongoimport と mongorestore を使用して、DocumentDB: MongoDB 用 API アカウントにデータをインポートする方法について説明します"
keywords: mongoimport, mongorestore
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 2af8691326550c631e6668890cb0d6b893fa7740
ms.lasthandoff: 03/08/2017


---
# <a name="migrate-data-to-documentdb-by-using-mongoimport-and-mongorestore"></a>mongoimport と mongorestore を使用して DocumentDB にデータを移行する
> [!div class="op_single_selector"]
> * [DocumentDB にインポートする](documentdb-import-data.md)
> * [MongoDB 用 API にインポートする](documentdb-mongodb-migrate.md)
>
>

Azure DocumentDB: MongoDB 用 API アカウントにデータを移行するには、以下を実行する必要があります。

* *mongoimport.exe* または *mongorestore.exe* のいずれかを [MongoDB Download Center](https://www.mongodb.com/download-center) からダウンロードします。
* [MongoDB 対応 DocumentDB 接続文字列](documentdb-connect-mongodb-account.md)を取得します。

## <a name="before-you-begin"></a>開始する前に

* スループットを上げる: データの移行にかかる時間は、コレクションに対して設定したスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 [Azure Portal](https://portal.azure.com) でスループットを上げることの詳細については、「[DocumentDB のパフォーマンス レベルと価格レベル](documentdb-performance-levels.md)」を参照してください。

* SSL を有効にする: DocumentDB には、厳密なセキュリティ要件と基準が存在します。 アカウントを操作するときは、SSL が有効になっていることを確認してください。 この記事で説明する手順の中に、*mongoimport* と *mongorestore*で SSL を有効にする方法が含まれています。

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>接続文字列情報 (ホスト、ポート、ユーザー名、およびパスワード) を見つける

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[NoSQL (DocumentDB)]** エントリをクリックします。
2. **[サブスクリプション]** ウィンドウで、自分のアカウント名を選択します。
3. **[接続文字列]** ブレードで、**[接続文字列]** をクリックします。  
右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。

    ![[接続文字列] ブレード](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>mongoimport を使用した MongoDB 用 API へのデータのインポート

DocumentDB アカウントにデータをインポートするには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。  

テンプレート:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

例:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>mongorestore を使用した MongoDB 用 API へのデータのインポート

DocumentDB アカウントにデータを復元するには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。

テンプレート:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

例:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>次のステップ
* 詳細については、「[DocumentDB: MongoDB 用 API のサンプル](documentdb-mongodb-samples.md)」を参照してください。

