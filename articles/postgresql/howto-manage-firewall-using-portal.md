---
title: ファイアウォール規則の管理 - Azure ポータル - Azure Database for PostgreSQL - Single Server
description: Azure portal を使用して Azure Database for PostgreSQL - 単一サーバー用のファイアウォール規則を作成して管理する
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 02bd4927216b6b60d2720e6f32c5768499e310bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022202"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL - 単一サーバー用のファイアウォール規則を作成して管理する
サーバーレベルのファイアウォール規則を使用して、指定した IP アドレスまたは IP アドレス範囲からの Azure Database for PostgreSQL サーバーへのアクセスを管理できます。

仮想ネットワーク (VNet) 規則を使用して、サーバーへのアクセスをセキュリティで保護することもできます。 [Azure portal を使用して仮想ネットワーク サービス エンドポイントと規則の作成および管理](howto-manage-vnet-using-portal.md)する方法を確認してください。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) を作成するサーバー

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal でサーバーレベルのファイアウォール規則を作成する
1. PostgreSQL サーバー ページの [設定] で、 **[接続のセキュリティ]** をクリックして Azure Database for PostgreSQL の [接続のセキュリティ] ページを開きます。

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal - [接続のセキュリティ] のクリック":::

2. ツール バーの **[自分の IP を追加]** をクリックします。 これにより、Azure システムによって認識されたコンピューターのパブリック IP アドレスでファイアウォール規則が自動的に作成されます。

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal - [自分の IP を追加] のクリック":::

3. 構成を保存する前に、IP アドレスを確認します。 場合によっては、Azure Portal で見られる IP アドレスは、インターネットおよび Azure サーバーにアクセスするときに使用する IP アドレスと異なることがあります。 そのため、開始 IP と終了 IP を変更してルール関数を予想どおりにする必要があります。
   検索エンジンまたはその他のオンライン ツールを使用して、自分の IP アドレスを確認します (たとえば、"what is my IP" を検索します)。

   :::image type="content" source="./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="「What is my IP」の Bing 検索":::

4. アドレス範囲を追加します。 Azure Database for PostgreSQL のファイアウォール規則では、単一の IP アドレスまたはアドレス範囲を指定できます。 規則を単一の IP アドレスに限定する場合は、[開始 IP] と [終了 IP] のフィールドに同じアドレスを入力します。 ファイアウォールを開くと、管理者、ユーザー、アプリケーションは、有効な資格情報を持っている PostgreSQL サーバー上の任意のデータベースにアクセスできます。

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal - ファイアウォール規則":::

5. ツール バーの **[保存]** をクリックしてこのサーバーレベルのファイアウォール規則を保存します。 ファイアウォール規則の更新が成功したという確認を待機します。

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal - [保存] のクリック":::

## <a name="connecting-from-azure"></a>Azure からの接続
Azure のアプリケーションが Azure Database for PostgreSQL サーバーに接続できるようにするには、Azure 接続を有効にする必要があります。 たとえば、Azure Web Apps アプリケーションや Azure VM で実行されるアプリケーションをホストしたり、Azure Data Factory データ管理ゲートウェイから接続したりするためです。 ファイアウォール規則でこれらの接続を有効にするために、リソースが同じ仮想ネットワーク (VNet) またはリソース グループに存在する必要はありません。 Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 これらの種類の接続を有効にするには、いくつかの方法があります。 開始アドレスと終了アドレスが 0.0.0.0 であるファイアウォール設定は、これらの接続が許可されていることを示します。 あるいは、ポータルの **[接続のセキュリティ]** ウィンドウから **[Azure サービスへのアクセスを許可]** オプションを **[オン]** に設定し、 **[保存]** をクリックすることもできます。 接続試行が許可されていない場合、この要求は Azure Database for PostgreSQL サーバーに到達しません。

> [!IMPORTANT]
> このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する
ファイアウォール規則を管理する手順を繰り返します。
* 現在のコンピューターを追加するには、 **[自分の IP を追加]** のボタンをクリックします。 **[保存]** をクリックして変更を保存します。
* さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を削除するには、省略記号 [...] をクリックし、 **[削除]** をクリックします。 **[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ
- 同様に、スクリプトを作成し、[Azure CLI を使用して Azure Database for PostgreSQL ファイアウォール規則を作成および管理](howto-manage-firewall-using-cli.md)できます。
- [Azure portal を使用して仮想ネットワーク サービス エンドポイントと規則を作成および管理](howto-manage-vnet-using-portal.md)し、サーバーへのアクセスのセキュリティ保護を強化します。
- Azure Database for PostgreSQL サーバーに接続する方法については、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」をご覧ください。
