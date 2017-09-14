---
title: "Azure Database for MySQL に安全に接続するために SSL 接続を構成する | Microsoft Docs"
description: "SSL 接続を正しく使用するために Azure Database for MySQL と関連するアプリケーションを適切に構成する方法について"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 07/28/2017
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 77e1b6266a2cf47949fa06358ec003f6b6b38065
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する
Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

## <a name="step-1-obtain-ssl-certificate"></a>手順 1: SSL 証明書を取得する
Azure Database for MySQL サーバーで SSL 経由で通信するために必要な証明書を [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) からダウンロードし、その証明書ファイルをローカル ドライブ (このチュートリアルでは c:\ssl を使用) に保存します。
**Microsoft Internet Explorer と Microsoft Edge の場合:** ダウンロードが完了したら、証明書の名前を BaltimoreCyberTrustRoot.crt.pem に変更します。

## <a name="step-2-bind-ssl"></a>手順 2: SSL をバインドする
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>MySQL Workbench による SSL 経由でのサーバーへの接続
SSL 経由で安全に接続するように MySQL Workbench を構成します。 MySQL workbench で、[新しい接続のセットアップ] ダイアログの **[SSL]** タブに移動します。 **BaltimoreCyberTrustRoot.crt.pem** ファイルの場所を **[SSL CA File:]\(SSL CA ファイル:\)** フィールドに入力します。
![カスタマイズしたタイルの保存](./media/howto-configure-ssl/mysql-workbench-ssl.png)

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>MySQL CLI による SSL 経由でのサーバーへの接続
MySQL コマンド ライン インターフェイスを使用して、次のコマンドを実行します。
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>手順 3: Azure で SSL 接続を適用する 
### <a name="using-azure-portal"></a>Azure Portal の使用
Azure Porta から Azure Database for MySQL サーバーにアクセスし、**[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、**[Enforce SSL connection] \(SSL 接続の適用)** 設定を有効または無効にします。 その後、 **[保存]**をクリックします。 Microsoft では、セキュリティ強化のため **[Enforce SSL connection] \(SSL 接続の適用)** 設定は常に有効にしておくことをお勧めします。
![SSL の有効化](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI の使用
**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ Enabled または Disabled の値を使用します。
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-ssl-connection"></a>手順 4: SSL 接続を確認する
MySQL の **status** コマンドを実行して、SSL 経由で MySQL サーバーに接続していることを確認します。
```dos
mysql> status
```
出力を確認することで、接続が暗号化されていることを確認します。 以下のように表示されます: **SSL: 使用している暗号は AES256-SHA です** 

## <a name="sample-code"></a>サンプル コード
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>次のステップ
[Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)に従って、さまざまなアプリケーション接続オプションを確認する

