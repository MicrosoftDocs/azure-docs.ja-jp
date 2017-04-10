---
title: "Azure DocumentDB で MongoDB に Robomongo を使用する | Microsoft Docs"
description: "DocumentDB: MongoDB 用 API アカウントで Robomongo を使用する方法を説明します"
keywords: robomongo
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
ms.openlocfilehash: a8db7fbde5d6cd01b558ec351719bae361710efd
ms.lasthandoff: 03/08/2017


---
# <a name="use-robomongo-with-a-documentdb-api-for-mongodb-account"></a>DocumentDB: MongoDB 用 API アカウントで Robomongo を使用する
Robomongo を使用して Azure DocumentDB: MongoDB 用 API アカウントに接続するには、以下を実行する必要があります。

* [Robomongo](https://robomongo.org/) をダウンロードしてインストールする
* DocumentDB: MongoDB 用 API アカウントの[接続文字列](documentdb-connect-mongodb-account.md)情報を取得する

## <a name="connect-using-robomongo"></a>Robomongo を使用して接続する
Robomongo MongoDB 接続に DocumentDB: MongoDB 用 API アカウントを追加するには、次の手順を実行します。

1. [ここ](documentdb-connect-mongodb-account.md)に記載されている手順に従って、DocumentDB: MongoDB 用 API アカウントの接続情報を取得します。

    ![Screen shot of the connection string blade](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. *Robomongo.exe* を実行します

3. **[ファイル]** の接続ボタンをクリックして、接続を管理します。 次に、**[MongoDB Connections (MongoDB 接続)]** ウィンドウで **[作成]** をクリックし、**[接続の設定]** ウィンドウを開きます。

4. **[接続の設定]** ウィンドウで、名前を選択します。 次に、手順 1. の接続情報の **[ホスト]** と **[ポート]** に示されている**アドレス**と**ポート**をそれぞれ入力します。

    ![Robomongo 管理接続のスクリーンショット](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. **[認証]** タブで **[Perform authentication (認証の実行)]** をクリックします。 次に、データベース (既定値は*Admin*)、**ユーザー名**、**パスワード**を入力します。
この**ユーザー名**と**パスワード**は両方とも、手順 1 の接続情報に示されています。

    ![[Robomongo Authentication (Robomongo 認証)] タブのスクリーンショット](./media/documentdb-mongodb-robomongo/authentication.png)
6. **[SSL]** タブで、**[Use SSL protocol (SSL プロトコルを使用する)]** をオンにして、**[認証方法]** に **[自己署名証明書]** を指定します。

    ![[Robomongo SSL] タブのスクリーンショット](./media/documentdb-mongodb-robomongo/SSL.png)
7. 最後に、**[テスト]** をクリックして接続できることを確認し、**[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ
* DocumentDB: MongoDB 用 API の[サンプル](documentdb-mongodb-samples.md)を確認します。

