---
title: サーバー パラメーターの構成 - Azure portal - Azure Database for MySQL
description: この記事では、Azure ポータルを使用して Azure Database for MySQL で使用できる MySQL サーバー パラメータを構成する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 363be8b34f230b812bc24276e1f3925faf0cdc1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540843"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MySQL サーバーのサーバー パラメータを構成する

Azure Database for MySQL では、いくつかのサーバー パラメーターの構成をサポートします。 この記事では、Azure Portal を使用してこれらのパラメーターを構成する方法について説明します。 すべてのサーバー パラメーターを調整できるわけではありません。

>[!Note]
> サーバー パラメーターは、[Azure CLI](./howto-configure-server-parameters-using-cli.md)、[PowerShell](./howto-configure-server-parameters-using-powershell.md)、または [Azure portal](./howto-server-parameters.md) を使用して、サーバー レベルでグローバルに更新できます。

## <a name="configure-server-parameters"></a>サーバー パラメーターの構成

1. [Azure portal](https://portal.azure.com) にサインインし、お使いの Azure Database for MySQL サーバーを探します。
2. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL サーバーの [サーバー パラメーター] ページを開きます。
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Azure Portal の [サーバー パラメーター] ページ":::
3. 調整が必要な設定を探します。 **[説明]** 列を確認して、目的と許可される値を理解します。
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="列挙ドロップ ダウン":::
4. **[保存]** をクリックして変更を保存します。
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="変更の保存または破棄":::
5. パラメーターの新しい値を保存した場合は、 **[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="すべて既定値にリセット":::

## <a name="setting-parameters-not-listed"></a>設定パラメーターが一覧に含まれていない

更新するサーバー パラメーターが Azure portal に一覧表示されていない場合は、必要に応じて `init_connect` を使用して、接続レベルでパラメーターを設定できます。 これにより、サーバーに接続する各クライアントのサーバー パラメーターが設定されます。 

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
> タイム ゾーン テーブルにデータが正しく入力されるようにするには、サーバーを再起動する必要があります。 サーバーを再起動するには、[Azure portal](howto-restart-server-portal.md) または [CLI](howto-restart-server-cli.md) を使用します。

利用可能なタイム ゾーン値を表示するには、次のコマンドを実行します。

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>グローバル レベルのタイム ゾーンを設定する

Azure Portal の **[サーバー パラメーター]** ページからグローバル レベルのタイム ゾーンを設定できます。 下の画像では、グローバル タイム ゾーンが値 "US/Pacific" に設定されています。

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="タイム ゾーン パラメーターを設定する":::

### <a name="setting-the-session-level-time-zone"></a>セッション レベルのタイム ゾーンを設定する

セッション レベルのタイム ゾーンは、MySQL コマンド ラインや MySQL Workbench などのツールから `SET time_zone` コマンドを実行することで設定できます。 下の例では、タイム ゾーンが **US/Pacific** タイム ゾーンに設定されます。

```sql
SET time_zone = 'US/Pacific';
```

[日付と時刻関数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)については MySQL ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)
