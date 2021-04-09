---
title: 停止または開始する - Azure portal - Azure Database for MySQL サーバー
description: この記事では、Azure Database for MySQL で操作を停止または開始する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516874"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Azure Database for MySQL を停止または開始する

> [!IMPORTANT]
>  サーバーを **停止** すると、それ以降連続して 7 日間は、その状態のままになります。 この期間内に手動で **開始** しないと、サーバーは 7 日の終わりに自動的に開始されます。 サーバーを使用していない場合は、再び **停止** することができます。

この記事では、単一サーバーの停止および開始を実行する手順を説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for MySQL 単一サーバーを所有している必要があります。

> [!NOTE]
> [停止と開始](concepts-servers.md#limitations-of-stopstart-operation)の使用に関する制限を参照してください

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Azure portal を使用して Azure Database for MySQL を停止および開始する方法

### <a name="stop-a-running-server"></a>実行中のサーバーを停止する

1.  [Azure portal](https://portal.azure.com/) で、停止する MySQL サーバーを選択します。

2.  **[概要]** ページのツール バーで **[停止]** ボタンをクリックします。

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL サーバーの停止":::

    > [!NOTE]
    > サーバーが停止すると、その単一サーバーに対して他の管理操作は実行できません。

### <a name="start-a-stopped-server"></a>停止したサーバーを起動する

1.  [Azure portal](https://portal.azure.com/) で、開始する単一サーバーを選択します。

2.  **[概要]** ページのツール バーで **[起動]** ボタンをクリックします。

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL サーバーの開始":::

    > [!NOTE]
    > サーバーが起動した後、すべての管理操作を単一サーバーで使用できるようになります。

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>CLI を使用して Azure Database for MySQL を停止および開始する方法

### <a name="stop-a-running-server"></a>実行中のサーバーを停止する

1.  [Azure portal](https://portal.azure.com/) で、停止する MySQL サーバーを選択します。

2.  **[概要]** ページのツール バーで **[停止]** ボタンをクリックします。

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > サーバーが停止すると、その単一サーバーに対して他の管理操作は実行できません。

### <a name="start-a-stopped-server"></a>停止したサーバーを起動する

1.  [Azure portal](https://portal.azure.com/) で、開始する単一サーバーを選択します。

2.  **[概要]** ページのツール バーで **[起動]** ボタンをクリックします。

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > サーバーが起動した後、すべての管理操作を単一サーバーで使用できるようになります。

## <a name="next-steps"></a>次のステップ
[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します。
