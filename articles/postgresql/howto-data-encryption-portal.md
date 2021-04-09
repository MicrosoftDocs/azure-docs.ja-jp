---
title: データの暗号化 - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: Azure portal を使用して Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定および管理する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 98c413f85fe556f5fb413716037163931753e1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240734"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Azure portal を使用した Azure Database for PostgreSQL 単一サーバーのデータ暗号化

Azure portal を使用して Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定および管理する方法について説明します。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI の前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
* Azure Key Vault で、カスタマー マネージド キーに使用するキー コンテナーとキーを作成します。
* カスタマー マネージド キーとして使用するには、キー コンテナーに次のプロパティが必要です。
  * [論理的な削除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* カスタマー マネージド キーとして使用するには、キーに次の属性が必要です。
  * 有効期限がない
  * 無効化されていない
  * [取得]、[キーを折り返す]、[キーの折り返しを解除] の各操作を実行できる

## <a name="set-the-right-permissions-for-key-operations"></a>キー操作に対する適切なアクセス許可を設定する

1. Key Vault で、 **[アクセス ポリシー]**  >  **[アクセス ポリシーの追加]** の順に選択します。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="[アクセス ポリシー] と [アクセスポリシーの追加] が強調表示されている Key Vault のスクリーンショット":::

2. **[キーのアクセス許可]** を選択し、 **[取得]** 、 **[キーを折り返す]** 、 **[キーの折り返しを解除]** 、および PostgreSQL サーバーの名前である **プリンシパル** を選択します。 お使いのサーバー プリンシパルが既存のプリンシパルの一覧に見つからない場合は、登録する必要があります。 初めてデータの暗号化を設定しようとして失敗したときに、サーバー プリンシパルを登録するように求められます。  

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="アクセス ポリシーの概要":::

3. **[保存]** を選択します。

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定する

1. Azure Database for PostgreSQL で、 **[データの暗号化]** を選択して、カスタマー マネージド キーを設定します。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="[データの暗号化] が強調表示されている Azure Database for PostgreSQL のスクリーンショット":::

2. キー コンテナー とキーの組を選択するか、キー識別子を入力することができます。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="データの暗号化のオプションが強調表示されている Azure Database for PostgreSQL のスクリーンショット":::

3. **[保存]** を選択します。

4. 確実にすべてのファイル (一時ファイルを含む) が完全に暗号化されるように、サーバーを再起動します。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>復元サーバーまたはレプリカ サーバーでのデータ暗号化の使用

Key Vault に格納されている顧客のマネージド キーで Azure Database for PostgreSQL 単一サーバーが暗号化されると、そのサーバーの新しく作成されたコピーも暗号化されます。 この新しいコピーは、ローカルまたは geo 復元操作を使用するか、レプリカ (ローカル/リージョン間) 操作を使用して作成できます。 そのため、暗号化された PostgreSQL サーバーの場合は、次の手順を使用して、暗号化済みの復元されたサーバーを作成できます。

1. サーバーで、 **[概要]**  >  **[復元]** の順に選択します。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="[概要] と [復元] が強調表示されている Azure Database for PostgreSQL のスクリーンショット":::

   または、レプリケーションが有効なサーバーでは、 **[設定]** 見出しの下にある **[レプリケーション]** を選択します。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-replica.png" alt-text="[レプリケーション] が強調表示されている Azure Database for PostgreSQL のスクリーンショット":::

2. 復元操作が完了すると、作成された新しいサーバーは、プライマリ サーバーのキーで暗号化されます。 ただし、このサーバーの機能とオプションは無効になっており、サーバーにアクセスできません。 新しいサーバーの ID には、キー コンテナーへのアクセス許可がまだ付与されていないため、これによってデータ操作が抑止されます。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="[アクセス不可] 状態が強調表示されている Azure Database for PostgreSQL のスクリーンショット":::

3. サーバーにアクセスできるようにするには、復元されたサーバーでキーを再検証します。 **[データの暗号化]**  >  **[キーの再検証]** の順に選択します。

   > [!NOTE]
   > 新しいサーバーのサービス プリンシパルにキー コンテナーへのアクセス権を付与する必要があるため、最初の再検証の試行は失敗します。 サービス プリンシパルを生成するには、 **[キーの再検証]** を選択します。これにより、エラーが表示されますが、サービス プリンシパルが生成されます。 その後、この記事で前述した[こちらの手順](#set-the-right-permissions-for-key-operations)を参照してください。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="再検証の手順が強調表示されている Azure Database for PostgreSQL のスクリーンショット":::

   新しいサーバーへのアクセス権をキー コンテナーに付与する必要があります。

4. サービス プリンシパルを登録した後、もう一度キーを再検証すると、サーバーの通常の機能が再開されます。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="復元された機能を示している Azure Database for PostgreSQL のスクリーンショット":::

## <a name="next-steps"></a>次のステップ

 データ暗号化の詳細については、「[カスタマー マネージド キーを使用した Azure Database for PostgreSQL 単一サーバーのデータの暗号化](concepts-data-encryption-postgresql.md)」を参照してください。
