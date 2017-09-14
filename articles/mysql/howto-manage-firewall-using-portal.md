---
title: "Azure Portal を使用した Azure Database for MySQL ファイアウォール規則の作成と管理 | Microsoft Docs"
description: "Azure Portal を使用した Azure Database for MySQL ファイアウォール規則の作成と管理"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 33198e5a6e11df2db3a17fc96a0b3cd4b1a284e8
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017

---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-the-azure-portal"></a>Azure Portal を使用した Azure Database for MySQL ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用すると、管理者は、指定された IP アドレスまたは IP アドレス範囲から Azure Database for MySQL サーバーにアクセスできます。 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal でサーバーレベルのファイアウォール規則を作成する

1. MySQL サーバー ブレードの [設定] で、**[接続のセキュリティ]** をクリックして Azure Database for MySQL の [接続のセキュリティ] ブレードを開きます。

   ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. ツール バーの **[自分の IP を追加]** をクリックして、Azure システムによって認識されたコンピューターの IP アドレスで規則を作成します。

   ![Azure Portal - [自分の IP を追加] のクリック](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. 構成を保存する前に、IP アドレスを確認します。 場合によっては、Azure Portal で見られる IP アドレスは、インターネットおよび Azure サーバーにアクセスするときに使用する IP アドレスと異なることがあります。 そのため、開始 IP と終了 IP を変更してルール関数を予想どおりにする必要があります。

   検索エンジンまたはその他のオンライン ツールを使用して、独自の IP アドレスを確認します (たとえば、「what is my IP address」を検索します)。

   ![Bing での「What is my IP」](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. アドレス範囲を追加します。 Azure Database for MySQL のファイアウォール規則では、単一の IP アドレスまたはアドレスの範囲を指定できます。 規則を単一の IP アドレスに限定する場合は、[開始 IP] と [終了 IP] のフィールドに同じアドレスを入力します。 ファイアウォールを開くと、管理者とユーザーは、有効な資格情報を持っている MySQL サーバー上の任意のデータベースにアクセスできます。

   ![Azure Portal - ファイアウォール規則 ](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)


5. ツール バーの **[保存]** をクリックしてこのサーバーレベルのファイアウォール規則を保存します。 ファイアウォール規則の更新が成功したという確認を待機します。

   ![Azure Portal - [保存] のクリック](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する
ファイアウォール規則を管理する手順を繰り返します。
* 現在のコンピューターを追加するには、**[自分の IP を追加]** をクリックします。
* さらに IP アドレスを追加するには、**規則名**、**開始 IP**、および**終了 IP** を入力します。
* 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。
* 既存の規則を削除するには、省略記号 [...] をクリックして、**[削除]** をクリックします。
* **[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ
- Azure Database for MySQL サーバーに接続する方法のヘルプについては、「[Azure Database for MySQL の接続ライブラリ](./concepts-connection-libraries.md)」をご覧ください。

