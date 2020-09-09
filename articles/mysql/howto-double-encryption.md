---
title: インフラストラクチャの二重暗号化 - Azure portal - Azure Database for MySQL
description: Azure Database for MySQL のインフラストラクチャの二重暗号化を設定し、管理する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: d3076f2591718931bdab4dba9510d25fe07b2d02
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118762"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL のインフラストラクチャの二重暗号化

Azure Database for MySQL のインフラストラクチャの二重暗号化を設定し、管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>インフラストラクチャの二重暗号化を使用して Azure Database for MySQL サーバーを作成する - ポータル

次の手順に従って、Azure portal からインフラストラクチャの二重暗号化を使用して Azure Database for MySQL サーバーを作成します。

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。

2. **[データベース]**  >  **[Azure Database for MySQL]** の順に選択します。 検索ボックスに「**MySQL**」と入力してサービスを検索することもできます。

   ![Azure Database for MySQL オプション](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. サーバーの基本情報を指定します。 **[追加設定]** を選択し、 **[Infrastructure double encryption]** \(インフラストラクチャの二重暗号化\) チェックボックスをオンにしてパラメーターを設定します。

    ![Azure Database for MySQL の選択](./media/howto-double-encryption/infrastructure-encryption-selected.png)

4. **[確認および作成]** を選択して、サーバーをプロビジョニングします。

    ![Azure Database for MySQL の概要](./media/howto-double-encryption/infrastructure-encryption-summary.png)

5. サーバーを作成したら、 **[データ暗号化]** サーバー ブレードで状態を確認することで、インフラストラクチャの二重暗号化を検証できます。

    ![Azure Database for MySQL の検証](./media/howto-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>インフラストラクチャの二重暗号化を使用して Azure Database for MySQL サーバーを作成する - CLI

次の手順に従って、CLI からインフラストラクチャの二重暗号化を使用して Azure Database for MySQL サーバーを作成します。

この例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
次の例では、サーバー管理者ログイン `myadmin` を使用して、リソース グループ `myresourcegroup` の `mydemoserver` という名前の MySQL 5.7 サーバーを米国西部に作成します。 これは、**2 つの仮想コア**を備えた **Gen 4** **汎用**サーバーです。 これにより、作成されたサーバーのインフラストラクチャの二重暗号化も有効になります。 `<server_admin_password>` は独自の値に置き換えます。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>次のステップ

 データ暗号化の詳細については、[Azure Database for MySQL データ インフラストラクチャの二重暗号化](concepts-Infrastructure-double-encryption.md)に関するページを参照してください。
