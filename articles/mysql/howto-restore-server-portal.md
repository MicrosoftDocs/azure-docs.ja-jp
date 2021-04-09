---
title: バックアップと復元 - Azure portal - Azure Database for MySQL
description: この記事では、Azure Portal を使用して Azure Database for MySQL サーバーを復元する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 9bc31cf8fee2669634ff366caac77cb090baf075
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000302"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure Portal を使用して Azure Database for MySQL サーバーのバックアップと復元を行う方法

## <a name="backup-happens-automatically"></a>自動バックアップ
Azure Database for MySQL サーバーは、復元機能が有効になるように、バックアップが定期的に行われます。 この機能を使用して、新しいサーバー上で、サーバーとそのすべてのデータベースを過去の特定の時点に復元できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MySQL サーバーとデータベース](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>バックアップ構成の設定

サーバーの作成時に、 **[価格レベル]** ウィンドウで、ローカル冗長バックアップまたは地理冗長バックアップのどちらでサーバーを構成するかを選択します。

> [!NOTE]
> サーバーの作成後は、冗長の種類 (地理冗長とローカル冗長) を切り替えることはできません。
>

Azure Portal でサーバーを作成するときに、 **[価格レベル]** ウィンドウで、使用しているサーバーのバックアップとして **[ローカル冗長]** または **[地理冗長]** のいずれかを選択します。 また、このウィンドウの **[バックアップの保有期間]** で、サーバーのバックアップを保存する期間 (日数) を選択します。

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="価格レベル - バックアップ冗長の選択":::

作成中のこれらの値の設定について詳しくは、[Azure Database for MySQL サーバーのクイック スタート](quickstart-create-mysql-server-database-using-azure-portal.md)に関するページをご覧ください。

バックアップのリテンション期間は、以下の手順を使用してサーバーで変更できます。
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Database for MySQL サーバーを選択します。 この操作で、 **[概要]** ページが開きます。
3. **[設定]** で、メニューから **[価格レベル]** を選択します。 スライダーを使用して、**バックアップの保有期間** を 7 ～ 35 日の間で希望の値に変更します。
次のスクリーンショットでは 34 日に変更されています。
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="長くしたバックアップのリテンション期間":::

4. **[OK]** をクリックして変更を確定します。

バックアップのリテンション期間によって、現在からどのくらい遡ってポイントインタイム リストアを取得できるかが管理されます。ポイントインタイム リストアは使用可能なバックアップに基づいているためです。 ポイントインタイム リストアについては、次のセクションで詳しく説明します。 

## <a name="point-in-time-restore"></a>ポイントインタイム リストア
Azure Database for MySQL では、サーバーの過去の特定時点まで遡り、これをサーバーの新しいコピーに復元できます。 この新しいサーバーを使用してデータを復旧したり、クライアント アプリケーションにこの新しいサーバーを参照させたりすることができます。

たとえば、本日正午にテーブルが誤って削除された場合、正午前の時点まで復元し、削除されたテーブルとデータを新しいサーバーのコピーから取得できます。 ポイントインタイム リストアは、データベース レベルではなく、サーバー レベルで行われます。

次の手順では、サンプルのサーバーを特定の時点まで復元します。
1. Azure Portal で、ご利用の Azure Database for MySQL サーバーを選択します。 

2. サーバーの **[概要]** ページのツール バーで **[復元]** を選択します。

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Azure Database for MySQL - [概要] - [復元] ボタン":::

3. [復元] フォームに必要な情報を入力します。

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Azure Database for MySQL - 情報の復元":::
   - **復元ポイント**:復元先の特定の時点を選択します。
   - **対象サーバー**:新しいサーバーの名前を指定します。
   - **[場所]** :リージョンを選択することはできません。 既定では、ソース サーバーと同じになります。
   - **価格レベル**:ポイントインタイム リストアを行うときは、これらのパラメーターを変更することはできません。 ソース サーバーと同じレベルになります。 

4. **[OK]** をクリックして、特定の時点までサーバーを復元します。 

5. 復元が完了したら、作成した新しいサーバーを検索して、想定どおりにデータベースが復元できたかどうかを確認します。

ポイントインタイム リストアによって作成された新しいサーバーには、選択した特定の時点の既存のサーバーに対して有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

さらに、復元操作の終了後には、復元操作後に既定値にリセットされる (およびプライマリ サーバーからコピーで上書きされない) 2 つのサーバー パラメーターがあります
*   time_zone - この値は、既定値 **SYSTEM** に設定されます
*   event_scheduler - event_scheduler は、復元されたサーバーで **OFF** に設定されます

プライマリ サーバーから値をコピーし、[サーバー パラメーター](howto-server-parameters.md)を再構成して、復元されたサーバーでこれを設定する必要があります

復元中に作成される新しいサーバーには、元のサーバーに存在した VNet サービス エンドポイントはありません。 この新しいサーバー用に、これらの規則を個別に設定する必要があります。 元のサーバーのファイアウォール規則は復元されます。

## <a name="geo-restore"></a>geo リストア
地理冗長バックアップを使用するようにサーバーを構成した場合は、新しいサーバーをその既存のサーバーのバックアップから作成できます。 この新しいサーバーは、Azure Database for MySQL を使用できる任意のリージョンに作成できます。  

1. ポータルの左上隅にある **[リソースの作成]** ボタン (+) を選択します。 **[データベース]**  >  **[Azure Database for MySQL]** の順に選択します。

   :::image type="content" source="./media/howto-restore-server-portal/1_navigate-to-mysql.png" alt-text="Azure Database for MySQL に移動します。":::
 
2. 新しいサーバーのサブスクリプション、リソース グループ、および名前を指定します。 

3. **[データ ソース]** として **[バックアップ]** を選択します。 この操作により、geo 冗長バックアップが有効になっているサーバーの一覧を示すドロップダウンが表示されます。
   
   :::image type="content" source="./media/howto-restore-server-portal/3-geo-restore.png" alt-text="データ ソースを選択する。":::
    
   > [!NOTE]
   > サーバーが最初に作成された時点では、すぐには geo リストアで使用できない可能性があります。 必要なメタデータが設定されるまで数時間かかる場合があります。
   >

4. **[バックアップ]** ドロップダウンを選択します。
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore-backup.png" alt-text="[バックアップ] ドロップダウンを選択する。":::

5. 復元元のソース サーバーを選択します。
   
   :::image type="content" source="./media/howto-restore-server-portal/5-select-backup.png" alt-text="バックアップを選択する。":::

6. サーバーでは、**仮想コア** の数、**バックアップ保有期間**、**バックアップ冗長オプション**、**エンジン バージョン**、および **管理者資格情報** が規定値に設定されます。 **[続行]** をクリックします。 
   
   :::image type="content" source="./media/howto-restore-server-portal/6-accept-backup.png" alt-text="バックアップを続行する。":::

7. 必要に応じて、フォームの残りの部分を入力します。 任意の **場所** を選択できます。

    場所を選択したら、 **[サーバーの構成]** を選択して、**コンピューティング世代** (選択したリージョンで利用可能な場合)、**仮想コア** の数、**バックアップ保有期間**、および **バックアップ冗長オプション** を更新できます。 復元中に、**価格レベル** (Basic、汎用、またはメモリ最適化) と **ストレージ** のサイズはいずれも変更できません。

   :::image type="content" source="./media/howto-restore-server-portal/7-create.png" alt-text="フォームに入力する。"::: 

8. **[確認および作成]** を選択して、選択内容を確認します。 

9. **[作成]** を選択して、サーバーをプロビジョニングします。 この操作には数分かかることがあります。

geo リストアによって作成された新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

復元中に作成される新しいサーバーには、元のサーバーに存在した VNet サービス エンドポイントはありません。 この新しいサーバー用に、これらの規則を個別に設定する必要があります。 元のサーバーのファイアウォール規則は復元されます。

## <a name="next-steps"></a>次のステップ
- サービスの[バックアップ](concepts-backup.md)の詳細について確認します
- [レプリカ](concepts-read-replicas.md)について確認します
- [ビジネス継続性](concepts-business-continuity.md)オプションについて確認します