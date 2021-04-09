---
title: サーバー パラメーターの構成 - Azure portal - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure portal を介して Azure Database for PostgreSQL の Postgres パラメーターを構成する方法について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: e1b40e3116d56e87a2f397350ef2ba5510e04c0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91707695"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Azure portal を使用した Azure Database for PostgreSQL - Single Server でのサーバー パラメーターの構成 
Azure Portal を使用して、Azure Database for PostgreSQL サーバーの構成パラメーターを一覧表示、表示、更新できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>パラメーターの編集と表示
1. [Azure Portal](https://portal.azure.com)を開きます。

2. Azure Database for PostgreSQL サーバーを選択します。

3. **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。 ページにパラメーター、その値、説明の一覧が表示されます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="パラメーターの [概要] ページ":::

4. **ドロップ ダウン** ボタンをクリックして、client_min_messages のような列挙型のパラメーターに指定できる値を表示します。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="列挙ドロップ ダウン":::

5. **[i]** (情報) ボタンを選択するかマウス ポインターを置いて、cpu_index_tuple_cost のような数値パラメーターに指定できる値の範囲を表示します。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="情報ボタン":::

6. 必要に応じて、**検索ボックス** を使用して、特定のパラメーターを絞り込みます。 検索は、パラメーターの名前と説明に対して実行されます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="検索結果":::

7. 調整するパラメーターの値を変更します。 セッションで行ったすべての変更が紫色で強調表示されます。 値を変更したら、 **[保存]** を選択できます。 または変更を **[破棄]** することができます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="変更の保存または破棄":::

8. パラメーターの新しい値を保存した場合は、 **[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="すべて既定値にリセット":::

## <a name="next-steps"></a>次のステップ
内容は次のとおりです。
- [Azure Database for PostgreSQL のサーバー パラメーターの概要](concepts-servers.md)
- [Azure CLI を使用したパラメーターの構成](howto-configure-server-parameters-using-cli.md)
