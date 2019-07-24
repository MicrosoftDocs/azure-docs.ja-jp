---
title: Azure Database for MariaDB のサーバーを復元する方法
description: この記事では、Azure portal を使用して Azure Database for MariaDB のサーバーを復元する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 23d683fea494ad0509af359d6e49519f2bc6aa99
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615780"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MariaDB のサーバーのバックアップと復元を行う方法

## <a name="backup-happens-automatically"></a>自動バックアップ
Azure Database for MariaDB サーバーは、復元機能が有効になるように、バックアップが定期的に行われます。 この機能を使用して、新しいサーバー上で、サーバーとそのすべてのデータベースを過去の特定の時点に復元できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MariaDB サーバーとデータベース](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>バックアップ構成の設定

サーバーの作成時に、**[価格レベル]** ウィンドウで、ローカル冗長バックアップまたは地理冗長バックアップのどちらでサーバーを構成するかを選択します。

> [!NOTE]
> サーバーの作成後は、冗長の種類 (地理冗長とローカル冗長) を切り替えることはできません。
>

Azure Portal でサーバーを作成するときに、**[価格レベル]** ウィンドウで、使用しているサーバーのバックアップとして **[ローカル冗長]** または **[地理冗長]** のいずれかを選択します。 また、このウィンドウの **[バックアップの保有期間]** で、サーバーのバックアップを保存する期間 (日数) を選択します。

   ![価格レベル - バックアップ冗長の選択](./media/howto-restore-server-portal/pricing-tier.png)

作成時のこれらの値の設定については、[Azure Database for MariaDB サーバーのクイック スタート](quickstart-create-mariadb-server-database-using-azure-portal.md)に関するページを参照してください。

バックアップのリテンション期間は、以下の手順を使用してサーバーで変更できます。
1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. Azure Database for MariaDB サーバーを選択します。 この操作で、**[概要]** ページが開きます。

3. **[設定]** で、メニューから **[価格レベル]** を選択します。 スライダーを使用して、**バックアップの保有期間**を 7 ～ 35 日の間で希望の値に変更します。
次のスクリーンショットでは 35 日に変更されています。
![長くしたバックアップのリテンション期間](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. **[OK]** をクリックして変更を確定します。

バックアップのリテンション期間によって、現在からどのくらい遡ってポイントインタイム リストアを取得できるかが管理されます。ポイントインタイム リストアは使用可能なバックアップに基づいているためです。 ポイントインタイム リストアについては、次のセクションで詳しく説明します。 

## <a name="point-in-time-restore"></a>ポイントインタイム リストア
Azure Database for MariaDB では、サーバーの過去の特定時点まで遡り、これをサーバーの新しいコピーに復元できます。 この新しいサーバーを使用してデータを復旧したり、クライアント アプリケーションにこの新しいサーバーを参照させたりすることができます。

たとえば、本日正午にテーブルが誤って削除された場合、正午前の時点まで復元し、削除されたテーブルとデータを新しいサーバーのコピーから取得できます。 ポイントインタイム リストアは、データベース レベルではなく、サーバー レベルで行われます。

次の手順では、サンプルのサーバーを特定の時点まで復元します。
1. Azure portal で Azure Database for MariaDB サーバーを選択します。 

2. サーバーの **[概要]** ページのツール バーで **[復元]** を選択します。

   ![Azure Database for MariaDB - [概要] - [復元] ボタン](./media/howto-restore-server-portal/2-server.png)

3. [復元] フォームに必要な情報を入力します。

   ![Azure Database for MariaDB - 情報の復元](./media/howto-restore-server-portal/3-restore.png)
   - **復元ポイント**:復元先の特定の時点を選択します。
   - **対象サーバー**:新しいサーバーの名前を指定します。
   - **場所**:リージョンを選択することはできません。 既定では、ソース サーバーと同じになります。
   - **価格レベル**:ポイントインタイム リストアを行うときは、これらのパラメーターを変更することはできません。 ソース サーバーと同じレベルになります。 

4. **[OK]** をクリックして、特定の時点までサーバーを復元します。 

5. 復元が完了したら、作成した新しいサーバーを検索して、想定どおりにデータベースが復元できたかどうかを確認します。

>[!Note]
>ポイントインタイム リストアによって作成された新しいサーバーには、選択した特定の時点の既存のサーバーに対して有効であったサーバー管理者のログイン名とパスワードが設定されていることに注意してください。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

## <a name="geo-restore"></a>geo リストア
地理冗長バックアップを使用するようにサーバーを構成した場合は、新しいサーバーをその既存のサーバーのバックアップから作成できます。 この新しいサーバーは、Azure Database for MariaDB を使用できる任意のリージョンに作成できます。  

1. **[データベース]** > **[Azure Database for MariaDB]** の順に選択します。 検索ボックスに「**MariaDB**」と入力してサービスを検索することもできます。

   !["Azure Database for MariaDB" のオプション](./media/howto-restore-server-portal/2_navigate-to-mariadb.png)

2. フォームの **[ソースの選択]** ドロップダウンで **[バックアップ]** を選択します。 この操作により、geo 冗長バックアップが有効になっているサーバーの一覧が読み込まれます。 これらのバックアップの中から、新しいサーバーのソースとして使用するものを選択します。
   ![ソースの選択:バックアップと geo 冗長バックアップの一覧](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > サーバーが最初に作成された時点では、すぐには geo リストアで使用できない可能性があります。 必要なメタデータが設定されるまで数時間かかる場合があります。
   >

3. 必要に応じて、フォームの残りの部分を入力します。 任意の**場所**を選択できます。 場所を選択したら、**[価格レベル]** を選択できます。 既定では、復元元の既存のサーバーのパラメーターが表示されます。 これらの設定を継承するには、変更を加えずに **[OK]** をクリックします。 または、**コンピューティング世代** (選択したリージョンで使用できる場合)、**仮想コア**の数、**バックアップのリテンション期間**、および**バックアップ冗長性オプション**を変更することもできます。 復元中に、**価格レベル** (Basic、汎用、またはメモリ最適化) と**ストレージ**のサイズはいずれも変更できません。

>[!Note]
>geo リストアによって作成された新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

## <a name="next-steps"></a>次の手順
- サービスの[バックアップ](concepts-backup.md)の詳細を確認します。
- [ビジネス継続性](concepts-business-continuity.md)オプションについて確認します。
