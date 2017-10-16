---
title: "Azure Database for PostgreSQL のサーバーをバックアップして復元する方法 | Microsoft Docs"
description: "Azure CLI を使用して、Azure Database for PostgreSQL のサーバーをバックアップおよび復元する方法について説明します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 871887e67d686a965a0648d2c6f0c72b3008db05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-by-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL のサーバーをバックアップして復元する方法

Azure Database for PostgreSQL を使用して、7 日 ～ 35 日にわたって過去の日付のサーバー データべースを復元します。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Azure CLI をローカルにインストールして使用する場合、このハウツー ガイドでは Azure CLI バージョン 2.0 以上を使用する必要があります。 バージョンを確認するには、Azure CLI コマンド プロンプトで「`az --version`」と入力します。 インストールまたはアップグレードする必要には、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」をご覧ください。

## <a name="back-up-happens-automatically"></a>バックアップは自動的に行われる
Azure Database for PostgreSQL を使用するとき、このデータベース サービスは 5 分ごとに自動でサービスのバックアップを行います。 

Basic レベルでは、バックアップは 7 日間有効です。 Standard レベルでは、バックアップは 35 日間有効です。 詳しくは、[Azure Database for PostgreSQL の価格レベル](concepts-service-tiers.md)に関する記事をご覧ください。

自動バックアップ機能を使用すると、サーバーとそのデータベースを過去の日付や特定の時点に復元できます。

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>Azure CLI を使用して過去の特定の時点にデータベースを復元する
Azure Database for PostgreSQL を使用して、過去の特定の時点までサーバーを復元できます。 復元されたデータは新しいサーバーにコピーされ、既存のサーバーはそのまま残されます。 たとえば、今日の正午にテーブルが誤って削除された場合、正午の直前に復元できます。 その後、不足しているテーブルとデータを、サーバーの復元されたコピーから取得できます。 

サーバーを復元するには、Azure CLI コマンド [az postgres server restore](/cli/azure/postgres/server#restore) を使用します。

### <a name="run-the-restore-command"></a>復元コマンドを実行する

サーバーを復元するには、Azure CLI コマンド プロンプトで、次のコマンドを入力します。

```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

`az postgres server restore` コマンドには、次のパラメーターが必要です。
| Setting | 推奨値 | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  ソース サーバーが存在するリソース グループ。  |
| name | mypgserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 復元する特定の時点を選択します。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、`2017-04-13T05:59:00-08:00` など自身のローカル タイム ゾーンを使用できます。 また、`2017-04-13T13:59:00Z` など UTC Zulu 形式も使用できます。 |
| source-server | mypgserver-20170401 | 復元元のソース サーバーの名前または ID。 |

サーバーを過去の特定の時点に復元すると、新しいサーバーが作成されます。 特定の時点における元のサーバーとそのデータベースが新しいサーバーにコピーされます。

復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。 

`az postgres server restore` コマンドは同期的です。 サーバーが復元されたら、それをもう一度使用して別の時点でプロセスを繰り返すことができます。 

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。

## <a name="next-steps"></a>次のステップ
[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)
