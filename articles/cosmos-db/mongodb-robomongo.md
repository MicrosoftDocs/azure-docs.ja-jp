---
title: Robo 3T を使用して Azure Cosmos DB に接続する
description: Robo 3T と Azure Cosmos DB の MongoDB 用 API を使用して Azure Cosmos DB に接続する方法について説明します
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab066fef4bffe35408fdc5f3ad6d66796b4d0818
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443972"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API と共に Robo 3T を使用する

Robo 3T を使用して Cosmos アカウントに接続するには、次のことを行う必要があります。

* [Robo 3T](https://robomongo.org/) をダウンロードしてインストールする
* Cosmos DB [接続文字列](connect-mongodb-account.md)情報を取得する

## <a name="connect-using-robo-3t"></a>Robo 3T を使用して接続する
Robo 3T 接続マネージャーに Cosmos DB アカウントを追加するには、次の手順を行います。

1. [こちら](connect-mongodb-account.md)の手順を使用して、Azure Cosmos DB の MongoDB 用 API で構成された Cosmos アカウントの接続情報を取得します。

    ![[接続文字列] ブレードのスクリーンショット](./media/mongodb-robomongo/connectionstringblade.png)
2. *Robomongo.exe* を実行します

3. **[ファイル]** の接続ボタンをクリックして、接続を管理します。 次に、**[MongoDB Connections (MongoDB 接続)]** ウィンドウで **[作成]** をクリックし、**[接続の設定]** ウィンドウを開きます。

4. **[接続の設定]** ウィンドウで、名前を選択します。 次に、手順 1. の接続情報の **[ホスト]** と **[ポート]** に示されている**アドレス**と**ポート**をそれぞれ入力します。

    ![Robomongo 管理接続のスクリーンショット](./media/mongodb-robomongo/manageconnections.png)
5. **[認証]** タブで **[Perform authentication (認証の実行)]** をクリックします。 次に、データベース (既定値は*Admin*)、**ユーザー名**、**パスワード**を入力します。
この**ユーザー名**と**パスワード**は両方とも、手順 1 の接続情報に示されています。

    ![Robomongo の [認証] タブのスクリーンショット](./media/mongodb-robomongo/authentication.png)
6. **[SSL]** タブで、**[Use SSL protocol (SSL プロトコルを使用する)]** をオンにして、**[認証方法]** に **[自己署名証明書]** を指定します。

    ![Robomongo の [SSL] タブのスクリーンショット](./media/mongodb-robomongo/SSL.png)
7. 最後に、**[テスト]** をクリックして接続できることを確認し、**[保存]** をクリックします。

## <a name="next-steps"></a>次の手順

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調べます。
