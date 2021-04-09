---
title: サーバー パラメーターを構成する - Azure portal - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for MySQL フレキシブル サーバーで使用できる MySQL サーバー パラメータを構成する方法について説明します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 7733a6211363b4f1c9e9006f757b4d152c7af7f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489558"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MySQL - フレキシブル サーバーのサーバー パラメータを構成する

> [!IMPORTANT] 
> Azure Database for MySQL フレキシブル サーバーは、現在パブリック プレビュー段階にあります。

サーバー パラメーターを使用して Azure Database for MySQL フレキシブル サーバー構成を管理できます。 このサーバー パラメーターは、サーバーの作成時に既定値と推奨値を使用して構成されます。  

この記事では、Azure portal を使用してサーバー パラメーターを表示および構成する方法について説明します。 Azure portal の [サーバー パラメーター] ブレードには、変更可能および変更不可のサーバー パラメーターの両方が表示されます。 変更不可のサーバー パラメーターはグレーで表示されます。

>[!Note]
> サーバー パラメーターは、[Azure CLI](./how-to-configure-server-parameters-cli.md) または [Azure portal](./how-to-configure-server-parameters-portal.md) を使用して、サーバー レベルでグローバルに更新できます。

## <a name="configure-server-parameters"></a>サーバー パラメーターの構成

1. [Azure portal](https://portal.azure.com) にサインインし、お使いの Azure Database for MySQL フレキシブル サーバーを探します。
2. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL フレキシブル サーバーの [サーバー パラメーター] ページを開きます。
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Azure Portal の [サーバー パラメーター] ページ":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. 調整が必要なサーバー パラメーターを探します。 **[説明]** 列を確認して、目的と許可される値を理解します。
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="列挙ドロップ ダウン":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. **[保存]** をクリックして変更を保存します。
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="変更の保存または破棄":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. 静的パラメーターは、有効にするためにサーバーを再起動する必要があるものです。 静的パラメーターを変更する場合は、 **[今すぐ再起動する]** か、 **[後で再起動する]** かを確認するメッセージが表示されます。
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="静的パラメーターの保存時に再起動":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. パラメーターの新しい値を保存した場合は、 **[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="すべて既定値にリセット":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>変更不可のサーバー パラメーターの設定

更新するサーバー パラメーターが変更不可である場合は、必要に応じて `init_connect` を使用して、接続レベルでパラメーターを設定できます。 これにより、サーバーに接続する各クライアントのサーバー パラメーターが設定されます。 

1. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL サーバーの [サーバー パラメーター] ページを開きます。
2. `init_connect` を検索します
3. 値の列に `SET parameter_name=YOUR_DESIRED_VALUE` の形式でサーバー パラメーターを追加します。

    たとえば、`init_connect` を `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;` に設定すると、サーバーの文字セットを変更できます
4. **[保存]** をクリックして変更を保存します。

>[!Note]
> `init_connect` を使用すると、セッション レベルで SUPER 特権を必要としないパラメーターを変更することができます。 `init_connect` を使用してパラメーターを設定できるかどうかを確認するには、`set session parameter_name=YOUR_DESIRED_VALUE;` コマンドを実行し、"**Access denied; you need SUPER privilege(s)** "(アクセスが拒否されました。SUPER 特権が必要です) エラーが発生する場合は、'init_connect' を使用してパラメーターを設定することはできません。

## <a name="working-with-the-time-zone-parameter"></a>タイム ゾーン パラメーターを使用する

### <a name="populating-the-time-zone-tables"></a>タイム ゾーン テーブルに入力する

サーバーのタイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。

> [!NOTE]
> MySQL Workbench から `mysql.az_load_timezone` コマンドを実行するとき、場合によっては、`SET SQL_SAFE_UPDATES=0;` を利用し、最初にセーフ アップデート モードをオフにする必要があります。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> タイム ゾーン テーブルにデータが正しく入力されるようにするには、サーバーを再起動する必要があります。<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

利用可能なタイム ゾーン値を表示するには、次のコマンドを実行します。

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>グローバル レベルのタイム ゾーンを設定する

Azure Portal の **[サーバー パラメーター]** ページからグローバル レベルのタイム ゾーンを設定できます。 下の画像では、グローバル タイム ゾーンが値 "US/Pacific" に設定されています。

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="タイム ゾーン パラメーターを設定する":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>セッション レベルのタイム ゾーンを設定する

セッション レベルのタイム ゾーンは、MySQL コマンド ラインや MySQL Workbench などのツールから `SET time_zone` コマンドを実行することで設定できます。 下の例では、タイム ゾーンが **US/Pacific** タイム ゾーンに設定されます。

```sql
SET time_zone = 'US/Pacific';
```

[日付と時刻関数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)については MySQL ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure CLI でサーバー パラメーター](./how-to-configure-server-parameters-cli.md)を構成する方法
