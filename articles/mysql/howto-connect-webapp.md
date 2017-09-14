---
title: "既存の Azure App Service と Azure Database for MySQL の接続 |Microsoft Docs"
description: "既存の Azure App Service を Azure Database for MySQL に適切に接続する手順"
services: mysql
author: v-chenyh
ms.author: v-chenyh
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 2488a28353f3cfe76dc5aa9f9a9159a37ee9901b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017

---

# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>既存の Azure App Service と Azure Database for MySQL サーバーの接続
このドキュメントでは、既存の Azure App Service を Azure Database for MySQL サーバーに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
[Azure ポータル](https://portal.azure.com)にログインします。 Azure Database for MySQL サーバーを作成します。 詳細については、[Azure Portal を使用した Azure Database for MySQL サーバーの作成方法](quickstart-create-mysql-server-database-using-azure-portal.md)に関するページ、または[Azure CLI を使用した Azure Database for MySQL サーバーの作成方法](quickstart-create-mysql-server-database-using-azure-cli.md)に関するページをご覧ください。

現在、Azure App Service から Azure Database for MySQL へのアクセスを有効にする方法は 2 つあります。 どちらの方法でも、サーバー レベルのファイアウォール ルールの設定を行います。

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>方法 1 - すべての IP アドレスを許可するファイアウォール ルールを作成する
Azure Database for MySQL では、ファイアウォールを使用してデータを保護するアクセス セキュリティが提供されます。 Azure App Service から Azure Database for MySQL サーバーに接続するときには、App Service の送信 IP アドレスが本来動的であることに注意してください。 

Azure App Service の可用性を確保するには、この方法を使用してすべてのIP アドレスを許可することをお勧めします。

> [!NOTE]
> Microsoft では、Azure サービスのすべての IP が Azure Database for MySQL に接続可能とならないようにする長期的な解決策に取り組んでいます。

1. MySQL サーバー ブレードの [設定] で、**[接続のセキュリティ]** をクリックして Azure Database for MySQL の [接続のセキュリティ] ブレードを開きます。

   ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. **ルール名**、**開始 IP**、および**終了 IP** を入力します。 その後、 **[保存]**をクリックします。
   - ルール名: Allow-All-IPs
   - 開始 IP: 0.0.0.0
   - 終了 IP: 255.255.255.255

   ![Azure Portal - すべての IP の追加](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>方法 2 - 送信 IP を明示的に許可するファイアウォール ルールを作成する
Azure App Service のすべての送信 IP を明示的に追加することができます。

1. App Service の [プロパティ] ブレードで、**送信 IP アドレス**を表示します。

   ![Azure Portal - 送信 IP の表示](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. MySQL の [接続のセキュリティ] ブレードで、送信 IP を 1 つずつ追加します。

   ![Azure Portal - IP の明示的な追加](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. ファイアウォール ルールは忘れずに**保存**してください。

Azure App Service では IP アドレスを長時間一定に保とうとしますが、IP アドレスは変更される場合もあります。 たとえば、アプリをリサイクルするときやスケーリング操作が発生したとき、または容量を増やすために Azure のリージョン データ センターに新しいコンピューターを追加するときなどです。 IP アドレスが変更されると、アプリでは、MySQL サーバーに接続できない場合にダウンタイムが発生します。 上記の方法のいずれかを選択する場合は、この可能性を考慮してください。

## <a name="ssl-configuration"></a>SSL の構成
Azure Database for MySQL は、既定で SSL が有効になっています。 アプリケーションがデータベースへの接続で SSL を使用しない場合、MySQL サーバーで SSL を無効にする必要があります。 SSL の構成方法の詳細については、[Azure Database for MySQL での SSL の使用](howto-configure-ssl.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
接続文字列の詳細については、[接続文字列](howto-connection-string.md)に関するページをご覧ください。

