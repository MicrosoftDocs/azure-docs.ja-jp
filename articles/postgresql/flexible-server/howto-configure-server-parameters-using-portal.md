---
title: サーバー パラメーターを構成する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL フレキシブル サーバーの Postgres パラメーターを構成する方法について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4f945c2c7fffb143bdb8324a330775fb072b25c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90931182"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーでサーバー パラメーターを構成する 

Azure portal を使用して、Azure Database for PostgreSQL フレキシブル サーバーの構成パラメーターを一覧表示、表示、更新できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL フレキシブル サーバー](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>パラメーターの編集と表示

1. [Azure Portal](https://portal.azure.com)を開きます。

2. フレキシブル サーバーを選択します。

3. **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。 ページにパラメーター、その値、説明の一覧が表示されます。
![パラメーターの [概要] ページ](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. **ドロップ ダウン** ボタンをクリックして、client_min_messages のような列挙型のパラメーターに指定できる値を表示します。
![列挙ドロップ ダウン](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. **[i]** (情報) ボタンを選択するかマウス ポインターを置いて、cpu_index_tuple_cost のような数値パラメーターに指定できる値の範囲を表示します。
![情報ボタン](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. 必要に応じて、**検索ボックス** を使用して、特定のパラメーターを絞り込みます。 検索は、パラメーターの名前と説明に対して実行されます。
![検索結果](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. 調整するパラメーターの値を変更します。 セッションで行ったすべての変更が紫色で強調表示されます。 値を変更したら、 **[保存]** を選択できます。 または変更を **[破棄]** することができます。
![変更の保存または破棄](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. パラメーターの新しい値を保存した場合は、 **[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
![すべて既定値にリセット](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>次のステップ

内容は次のとおりです。

- [Azure Database for PostgreSQL のサーバー パラメーターの概要](concepts-servers.md)
- [Azure CLI を使用したパラメーターの構成](howto-configure-server-parameters-using-cli.md)
