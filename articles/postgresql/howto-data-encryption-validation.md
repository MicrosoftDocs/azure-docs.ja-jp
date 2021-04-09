---
title: Azure Database for PostgreSQL のデータ暗号化を確実に検証する方法
description: カスタマー マネージド キーを使用して、Azure Database for PostgreSQL のデータ暗号化の暗号化を検証する方法を学習します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 7ec27cc4f28151214ca97ffb5113607d6b60ee36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240581"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のデータ暗号化の検証

この記事は、Azure Database for PostgreSQL のカスタマー マネージド キーを使用するデータ暗号化が予期したとおりに動作していることを検証するのに役立ちます。

## <a name="check-the-encryption-status"></a>暗号化の状態を確認する

### <a name="from-portal"></a>ポータルから

1. カスタマーのキーが暗号化に使用されていることを確認するには、これらの手順に従います。

    * Azure portal で、**Azure Key Vault** ->  **[キー]** の順に移動します
    * サーバーの暗号化に使用されるキーを選択します。
    * **[Enabled]\(有効\)** になっているキーの状態を **[いいえ]** に設定します。
  
       しばらくすると (**約 15 分**)、Azure Database for PostgreSQL サーバーの **状態** が **アクセス不可** になります。 サーバーに対して行われた I/O 操作はすべて失敗し、サーバーが実際にカスタマー キーで暗号化されていること、およびキーが現在有効ではないことが検証されます。
    
        サーバーを **利用可能** にするために、キーを再検証できます。 
    
    * Key Vault のキーの状態を **[はい]** に設定します。
    * サーバーの **[データ暗号化]** で、 **[キーの再検証]** を選択します。
    * キーの再検証が正常に完了した後、サーバーの **状態** が **利用可能** に変わります

2. Azure portal では、暗号化キーが設定されていることを保証できる場合、Azure portal で使用されているカスタマー キーを使用してデータが暗号化されます。

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="アクセス ポリシーの概要":::

### <a name="from-cli"></a>CLI から

1. *az CLI* コマンドを使用して、Azure Database for PostgreSQL サーバーに使用されている主要なリソースを検証できます。

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    データ暗号化が設定されていないサーバーの場合、このコマンドの結果は空のセット [] になります。

### <a name="azure-audit-reports"></a>Azure 監査レポート

データ保護の標準と規制の要件の遵守に関する情報を提供する、[監査レポート](https://servicetrust.microsoft.com)を確認することもできます。

## <a name="next-steps"></a>次のステップ

データ暗号化の詳細については、「[カスタマー マネージド キーを使用した Azure Database for PostgreSQL 単一サーバーのデータの暗号化](concepts-data-encryption-postgresql.md)」を参照してください。