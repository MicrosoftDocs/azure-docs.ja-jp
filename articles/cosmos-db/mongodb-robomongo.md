---
title: Robo 3T を使用して Azure Cosmos DB に接続する
description: Robo 3T と Azure Cosmos DB の MongoDB 用 API を使用して Azure Cosmos DB に接続する方法について説明します
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096412"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API と共に Robo 3T を使用する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Robo 3T を使用して Cosmos アカウントに接続するには、次のことを行う必要があります。

* [Robo 3T](https://robomongo.org/) をダウンロードしてインストールする
* Cosmos DB [接続文字列](connect-mongodb-account.md)情報を取得する

> [!NOTE]
> 現時点では、Cosmos DB の MongoDB 用 API では、Robo 3T v1.2 以前のバージョンがサポートされています。

## <a name="connect-using-robo-3t"></a>Robo 3T を使用して接続する

Robo 3T 接続マネージャーに Cosmos DB アカウントを追加するには、次の手順を行います。

1. [こちら](connect-mongodb-account.md)の手順を使用して、Azure Cosmos DB の MongoDB 用 API で構成された Cosmos アカウントの接続情報を取得します。

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="[接続文字列] ブレードのスクリーンショット":::
2. *Robomongo* アプリケーションを実行します。

3. **[ファイル]** の接続ボタンをクリックして、接続を管理します。 次に、 **[MongoDB Connections (MongoDB 接続)]** ウィンドウで **[作成]** をクリックし、 **[接続の設定]** ウィンドウを開きます。

4. **[接続の設定]** ウィンドウで、名前を選択します。 次に、手順 1. の接続情報の **[ホスト]** と **[ポート]** に示されている **アドレス** と **ポート** をそれぞれ入力します。

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Robomongo 管理接続のスクリーンショット":::
5. **[認証]** タブで **[Perform authentication (認証の実行)]** をクリックします。 次に、データベース (既定値は *Admin*)、**ユーザー名**、**パスワード** を入力します。
この **ユーザー名** と **パスワード** は両方とも、手順 1 の接続情報に示されています。

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Robomongo の [認証] タブのスクリーンショット":::
6. **[SSL]** タブで、 **[Use SSL protocol (SSL プロトコルを使用する)]** をオンにして、 **[認証方法]** に **[自己署名証明書]** を指定します。

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Robomongo の [SSL] タブのスクリーンショット":::
7. 最後に、 **[テスト]** をクリックして接続できることを確認し、 **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。
