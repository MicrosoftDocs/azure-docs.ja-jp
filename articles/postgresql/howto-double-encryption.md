---
title: インフラストラクチャの二重暗号化 - Azure portal - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL のインフラストラクチャの二重暗号化を設定し、管理する方法について説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 03/14/2021
ms.openlocfilehash: 0b1079a084072556ae79a18a12f6b35f74c92775
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771653"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のインフラストラクチャの二重暗号化

Azure Database for PostgreSQL のインフラストラクチャの二重暗号化を設定し、管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>インフラストラクチャが二重暗号化された Azure Database for PostgreSQL サーバーの作成 - ポータル

次の手順に従って、Azure portal からインフラストラクチャの二重暗号化を使用して Azure Database for PostgreSQL サーバーを作成します。

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。

2. **[データベース]**  >  **[Azure Database for PostgreSQL]** の順に選択します。 検索ボックスに「PostgreSQL」と入力してサービスを検索することもできます。 **[単一サーバー]** デプロイ オプションを有効にします。

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="メニューの [Azure Database for PostgreSQL]":::

3. サーバーの基本情報を指定します。 **[追加設定]** を選択し、 **[Infrastructure double encryption]** \(インフラストラクチャの二重暗号化\) チェックボックスをオンにしてパラメーターを設定します。

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for PostgreSQL の設定":::

4. **[確認および作成]** を選択して、サーバーをプロビジョニングします。

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for PostgreSQL の概要":::

5. サーバーを作成したら、 **[データ暗号化]** サーバー ブレードで状態を確認することで、インフラストラクチャの二重暗号化を検証できます。

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL の検証":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>インフラストラクチャが二重暗号化された Azure Database for PostgreSQL サーバーの作成 - CLI

次の手順に従って、CLI からインフラストラクチャの二重暗号化を使用して Azure Database for PostgreSQL サーバーを作成します。

次の例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
次の例では、サーバー管理者ログイン `myadmin` を使用して、リソース グループ `myresourcegroup` の `mydemoserver` という名前の PostgreSQL 11 サーバーを米国西部に作成します。 これは、**2 つの仮想コア** を備えた **Gen 4** **汎用** サーバーです。 これにより、作成されたサーバーのインフラストラクチャの二重暗号化も有効になります。 `<server_admin_password>` は独自の値に置き換えます。

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>次のステップ

データ暗号化の詳細については、[Azure Database for PostgreSQL インフラストラクチャの二重暗号化](concepts-Infrastructure-double-encryption.md)に関するページを参照してください。

