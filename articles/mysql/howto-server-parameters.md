---
title: サーバー パラメーターの構成 - Azure portal - Azure Database for MySQL
description: この記事では、Azure ポータルを使用して Azure Database for MySQL で使用できる MySQL サーバー パラメータを構成する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/11/2020
ms.openlocfilehash: f592d6fb8fed3f15bd11d5e6ebe6ee358953748c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837230"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MySQL サーバーのサーバー パラメータを構成する

Azure Database for MySQL では、いくつかのサーバー パラメーターの構成をサポートします。 この記事では、Azure Portal を使用してこれらのパラメーターを構成する方法について説明します。 すべてのサーバー パラメーターを調整できるわけではありません。

## <a name="configure-server-parameters"></a>サーバー パラメーターの構成

1. Azure ポータルにサインインし、お使いの Azure Database for MySQL サーバーを探します。
2. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL サーバーの [サーバー パラメーター] ページを開きます。
![Azure Portal の [サーバー パラメーター] ページ](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 調整が必要な設定を探します。 **[説明]** 列を確認して、目的と許可される値を理解します。
![列挙ドロップ ダウン](./media/howto-server-parameters/3-toggle_parameter.png)
4. **[保存]** をクリックして変更を保存します。
![変更の保存または破棄](./media/howto-server-parameters/4-save_parameters.png)
5. パラメーターの新しい値を保存した場合は、 **[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
![すべて既定値にリセット](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>設定パラメーターが一覧に含まれていない

更新するサーバー パラメーターが Azure portal に一覧表示されていない場合は、必要に応じて `init_connect` を使用して、接続レベルでパラメーターを設定できます。 これにより、サーバーに接続する各クライアントのサーバー パラメーターが設定されます。 

1. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL サーバーの [サーバー パラメーター] ページを開きます。
2. `init_connect` を検索します
3. 値の列に `SET parameter_name=YOUR_DESIRED_VALUE` の形式でサーバー パラメーターを追加します。

    たとえば、`init_connect` を `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;` に設定すると、サーバーの文字セットを変更できます
4. **[保存]** をクリックして変更を保存します。

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

![タイム ゾーン パラメーターを設定する](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>セッション レベルのタイム ゾーンを設定する

セッション レベルのタイム ゾーンは、MySQL コマンド ラインや MySQL Workbench などのツールから `SET time_zone` コマンドを実行することで設定できます。 下の例では、タイム ゾーンが **US/Pacific** タイム ゾーンに設定されます。

```sql
SET time_zone = 'US/Pacific';
```

[日付と時刻関数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)については MySQL ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)
