---
title: "Azure Database for PostgreSQL サーバーを復元する方法 | Microsoft Docs"
description: "この記事では、Azure Portal を使用して Azure Database for PostgreSQL のサーバーを復元する方法について説明します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 07/20/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 49d1a893f4c7c7d99bf30ac7f7665c05019b02f4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Azure Portal を使用した Azure Database for PostgreSQL サーバーのバックアップと復元方法

## <a name="backup-happens-automatically"></a>自動バックアップ
Azure Database for PostgreSQL を使用するとき、このデータベース サービスは 5 分ごとに自動でサービスのバックアップを行います。 

このバックアップは、Basic レベルでは 7 日間、Standard レベルでは 35 日間使用できます。 詳細については、[Azure Database for PostgreSQL サービス レベル](concepts-service-tiers.md)に関するページをご覧ください。

自動バックアップ機能を使用して、過去の特定の時点までサーバーとそのサーバーのすべてのデータベースを新しいサーバーに復元できます。

## <a name="restore-in-the-azure-portal"></a>Azure Portal で復元する
Azure Database for PostgreSQL では、過去の特定の時点までサーバーのコピーを新しいサーバーに復元できます。 この新しいサーバーを使用して、データを回復できます。 

たとえば、本日正午にテーブルが誤って削除された場合、正午前の時点まで復元し、削除されたテーブルを新しいサーバーのコピーから取得できます。

次の手順では、サンプルのサーバーを特定の時点まで復元します。
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Database for PostgreSQL サーバーを見つけます。 Azure Portal で、左側のメニューの **[すべてのリソース]** をクリックし、サーバー名 (**mypgserver 20170401** など) を入力して、既存のサーバーを検索します。 検索結果に一覧表示されるサーバー名をクリックします。 サーバーの **[概要]** ページが開き、さらに多くの構成オプションが表示されます。

   ![Azure Portal - サーバーを検索して見つける](media/postgresql-howto-restore-server-portal/1-locate.png)

3. サーバーの概要ブレードの上部で、ツールバーの **[復元]** をクリックします。 [復元] ブレードが開きます。

   ![Azure Database for PostgreSQL - 概要 - 復元ボタン](./media/postgresql-howto-restore-server-portal/2_server.png)

4. [復元] フォームに必要な情報を入力します。

   ![Azure Database for PostgreSQL - 情報の復元 ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - **復元ポイント**: サーバーが変更される前の日時を選択します
  - **対象サーバー:** 復元先の新しいサーバー名を指定します
  - **場所:** リージョンを選択することはできません。既定では、ソース サーバーと同じ場所になります
  - **価格レベル:** サーバーを復元するときは、この値を変更することはできません。 ソース サーバーと同じレベルになります。 

5. **[OK]** をクリックして、特定の時点までサーバーを復元します。 

6. 復元が完了したら、作成した新しいサーバーを検索して、想定どおりにデータベースが復元できたかどうかを確認します。

## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)

