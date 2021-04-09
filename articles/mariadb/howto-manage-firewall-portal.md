---
title: ファイアウォール規則の管理 - Azure portal - Azure Database for MariaDB
description: Azure portal を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 895c8e51e16cb0b82ebf3edcf954dabe62b08ff4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665057"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用して、指定した IP アドレスまたは IP アドレス範囲からの Azure Database for MariaDB サーバーへのアクセスを管理できます。

仮想ネットワーク (VNet) 規則を使用して、サーバーへのアクセスをセキュリティで保護することもできます。 [Azure portal を使用して仮想ネットワーク サービス エンドポイントと規則の作成および管理](howto-manage-vnet-portal.md)する方法を確認してください。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal でサーバーレベルのファイアウォール規則を作成する

1. MariaDB サーバー ページの [設定] で、**[接続のセキュリティ]** をクリックして Azure Database for MariaDB の [接続のセキュリティ] ページを開きます。

   ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-manage-firewall-portal/1-connection-security.png)

2. ツール バーの **[自分の IP を追加]** をクリックします。 これにより、Azure システムによって認識されたコンピューターのパブリック IP アドレスでファイアウォール規則が自動的に作成されます。

   ![Azure Portal - [自分の IP を追加] のクリック](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. 構成を保存する前に、IP アドレスを確認します。 場合によっては、Azure Portal で見られる IP アドレスは、インターネットおよび Azure サーバーにアクセスするときに使用する IP アドレスと異なることがあります。 そのため、開始 IP と終了 IP を変更してルール関数を予想どおりにする必要があります。

   検索エンジンまたはその他のオンライン ツールを使用して、自分の IP アドレスを確認します (たとえば、「what is my IP」を検索します)。

4. アドレス範囲を追加します。 Azure Database for MariaDB のファイアウォール規則では、単一の IP アドレスまたはアドレスの範囲を指定できます。 規則を単一の IP アドレスに限定する場合は、[開始 IP] フィールドと [終了 IP] フィールドに同じアドレスを入力します。 ファイアウォールを開くと、管理者、ユーザー、およびアプリケーションは、有効な資格情報を持っている MariaDB サーバー上の任意のデータベースにアクセスできます。

   ![Azure Portal - ファイアウォール規則](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. ツール バーの **[保存]** をクリックしてこのサーバーレベルのファイアウォール規則を保存します。 ファイアウォール規則の更新が成功したという確認を待機します。

   ![Azure Portal - [保存] のクリック](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Azure からの接続
Azure からアプリケーションが Azure Database for MariaDB サーバーに接続できるようにするには、Azure 接続を有効にする必要があります。 たとえば、Azure Web Apps アプリケーションや Azure VM で実行されるアプリケーションをホストしたり、Azure Data Factory データ管理ゲートウェイから接続したりするためです。 ファイアウォール規則でこれらの接続を有効にするために、リソースが同じ仮想ネットワーク (VNet) またはリソース グループに存在する必要はありません。 Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 これらの種類の接続を有効にするには、いくつかの方法があります。 開始アドレスと終了アドレスが 0.0.0.0 であるファイアウォール設定は、これらの接続が許可されていることを示します。 あるいは、ポータルの **[接続のセキュリティ]** ウィンドウから **[Azure サービスへのアクセスを許可]** オプションを **[オン]** に設定し、**[保存]** をクリックすることもできます。 接続試行が許可されていない場合、この要求は Azure Database for MariaDB サーバーに到達しません。

> [!IMPORTANT]
> このオプションでは、他のお客様のサブスクリプションからの接続を含む、Azure からのすべての接続を許可するようにファイアウォールを構成します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Azure portal での既存のファイアウォール規則の管理
ファイアウォール規則を管理する手順を繰り返します。
* 現在のコンピューターを追加するには、 **[自分の IP を追加]** をクリックします。 **[保存]** をクリックして変更を保存します。
* さらに IP アドレスを追加するには、**規則名**、**開始 IP**、および **終了 IP** を入力します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を変更するには、規則内の任意のフィールドをクリックしてから、変更します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を削除するには、省略記号 [...] をクリックして、**削除** をクリックします。 **[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ
 - 同様に、[Azure CLI を使用して Azure Database for MariaDB ファイアウォール規則を作成および管理する](howto-manage-firewall-cli.md)スクリプトを作成できます。
 - [Azure portal を使用して仮想ネットワーク サービス エンドポイントと規則を作成および管理](howto-manage-vnet-portal.md)し、サーバーへのアクセスのセキュリティ保護を強化します。