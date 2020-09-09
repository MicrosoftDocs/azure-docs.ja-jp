---
title: インフラストラクチャの二重暗号化 - Azure portal - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL のインフラストラクチャの二重暗号化を設定し、管理する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 6612fe38adcd3c8002dd4a11122b5bb2e797a4dd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102176"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のインフラストラクチャの二重暗号化

Azure Database for PostgreSQL のインフラストラクチャの二重暗号化を設定し、管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>インフラストラクチャが二重暗号化された Azure Database for PostgreSQL サーバーの作成 - ポータル

次の手順に従って、Azure portal からインフラストラクチャが二重暗号化されている Azure Database for MySQL サーバーを作成します。

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。

2. **[データベース]**  >  **[Azure Database for PostgreSQL]** の順に選択します。 検索ボックスに「PostgreSQL」と入力してサービスを検索することもできます。 **[単一サーバー]** デプロイ オプションを有効にします。

   ![メニューの [Azure Database for PostgreSQL]](./media/quickstart-create-database-portal/1-create-database.png)

3. サーバーの基本情報を指定します。 **[追加設定]** を選択し、 **[Infrastructure double encryption]** \(インフラストラクチャの二重暗号化\) チェックボックスをオンにしてパラメーターを設定します。

    ![Azure Database for PostgreSQL の設定](./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png)

4. **[確認および作成]** を選択して、サーバーをプロビジョニングします。

    ![Azure Database for PostgreSQL の概要](./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png)

5. サーバーを作成したら、 **[データ暗号化]** サーバー ブレードで状態を確認することで、インフラストラクチャの二重暗号化を検証できます。

    ![Azure Database for MySQL の検証](./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>インフラストラクチャが二重暗号化された Azure Database for PostgreSQL サーバーの作成 - CLI

次の手順に従って、CLI からインフラストラクチャが二重暗号化されている Azure Database for MySQL サーバーを作成します。

次の例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
次の例では、サーバー管理者ログイン `myadmin` を使用して、リソース グループ `myresourcegroup` の `mydemoserver` という名前の PostgreSQL 11 サーバーを米国西部に作成します。 これは、**2 つの仮想コア**を備えた **Gen 4** **汎用**サーバーです。 また、作成されたサーバーのインフラストラクチャの二重暗号化も有効になります。 `<server_admin_password>` は独自の値に置き換えます。

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>次のステップ

データ暗号化の詳細については、[Azure Database for PostgreSQL インフラストラクチャの二重暗号化](concepts-Infrastructure-double-encryption.md)に関するページを参照してください。

