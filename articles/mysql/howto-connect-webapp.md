---
title: Azure App Serviceへの接続 - Azure Database for MySQL
description: 既存の Azure App Service を Azure Database for MySQL に適切に接続する手順
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: a3f3daa56c782d84cf6ba07223f8cfea15daa8a4
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101626"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>既存の Azure App Service と Azure Database for MySQL サーバーの接続
このトピックでは、既存の Azure App Service を Azure Database for MySQL サーバーに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
[Azure portal](https://portal.azure.com) にサインインする Azure Database for MySQL サーバーを作成します。 詳細については、[Azure Portal を使用した Azure Database for MySQL サーバーの作成方法](quickstart-create-mysql-server-database-using-azure-portal.md)に関するページ、または[Azure CLI を使用した Azure Database for MySQL サーバーの作成方法](quickstart-create-mysql-server-database-using-azure-cli.md)に関するページをご覧ください。

現在、Azure App Service から Azure Database for MySQL へのアクセスを有効にする方法は 2 つあります。 どちらの方法でも、サーバー レベルのファイアウォール ルールの設定を行います。

## <a name="solution-1---allow-azure-services"></a>解決策 1 - Azure サービスを許可する
Azure Database for MySQL では、ファイアウォールを使用してデータを保護するアクセス セキュリティが提供されます。 Azure App Service から Azure Database for MySQL サーバーに接続するときには、App Service の送信 IP アドレスが本来動的であることに注意してください。 「Azure サービスへのアクセスを許可する」オプションを選択すると、アプリ サービスが MySQL サーバーに接続できるるようにします｡

1. MySQL サーバー ブレードの [設定] で、 **[接続のセキュリティ]** をクリックして Azure Database for MySQL の [接続のセキュリティ] ブレードを開きます。

   ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-connect-webapp/1-connection-security.png)

2. **Azure サービスへのアクセスを許可する** で **ON** を選択して､**保存**を選択します。
   ![Azure portal - Azure アクセスを許可する](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>方法 2 - 送信 IP を明示的に許可するファイアウォール ルールを作成する
Azure App Service のすべての送信 IP を明示的に追加することができます。

1. App Service の [プロパティ] ブレードで、**送信 IP アドレス**を表示します。

   ![Azure Portal - 送信 IP の表示](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. MySQL の [接続のセキュリティ] ブレードで、送信 IP を 1 つずつ追加します。

   ![Azure Portal - IP の明示的な追加](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. ファイアウォール ルールは忘れずに**保存**してください。

Azure App Service では IP アドレスを長時間一定に保とうとしますが、IP アドレスは変更される場合もあります。 たとえば、アプリをリサイクルするときやスケーリング操作が発生したとき、または容量を増やすために Azure のリージョン データ センターに新しいコンピューターを追加するときに、そのような状況が発生します。 IP アドレスが変更されると、アプリでは、MySQL サーバーに接続できない場合にダウンタイムが発生します。 上記の方法のいずれかを選択する場合は、この点を考慮してください。

## <a name="ssl-configuration"></a>SSL の構成
Azure Database for MySQL では、既定で SSL が有効になっています。 アプリケーションがデータベースへの接続で SSL を使用しない場合、MySQL サーバーで SSL を無効にする必要があります。 SSL の構成方法の詳細については、[Azure Database for MySQL での SSL の使用](howto-configure-ssl.md)に関する記事を参照してください。

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
接続文字列の詳細については、[接続文字列](howto-connection-string.md)に関するページをご覧ください。
