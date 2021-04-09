---
title: 監査ログにアクセスする - Azure CLI - Azure Database for MySQL
description: この記事では、Azure CLI から Azure Database for MySQL の監査ログを構成し、それにアクセスする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541999"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Azure CLI での監査ログの構成とアクセス

Azure CLI を使用して、[Azure Database for MySQL の監査ログ](concepts-audit-logs.md)を構成することができます。

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を実行するには、以下が必要です。

- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)が必要です。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="configure-audit-logging"></a>監査ログを構成する

>[!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

次の手順に従って、監査ログを有効にし、構成します。

1. **audit_logs_enabled** パラメーターを "ON" に設定して、監査ログをオンにします。 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. **audit_log_events** パラメーターを更新して、ログに記録する [イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. **audit_log_exclude_users** パラメーターを更新して、ログから除外する MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. **audit_log_include_users** パラメーターを更新して、ログに含める特定の MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>次のステップ
- Azure Database for MySQL の[監査ログ](concepts-audit-logs.md)の詳細について学習します
- [Azure portal](howto-configure-audit-logs-portal.md) で監査ログを構成する方法を学習します
