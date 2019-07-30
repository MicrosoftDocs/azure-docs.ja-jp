---
title: Azure Database for MariaDB に安全に接続するために SSL 接続を構成する
description: SSL 接続を正しく使用するために Azure Database for MariaDB および関連するアプリケーションを適切に構成する方法について
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/02/2019
ms.openlocfilehash: e57371bb7598a92f35dd4fd0ec22a55fad722987
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360502"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Azure Database for MariaDB に安全に接続するためにご利用のアプリケーション内で SSL 接続を構成する
Azure Database for MariaDB では、Secure Sockets Layer (SSL) を使用して、クライアント アプリケーションにご利用の Azure Database for MariaDB サーバーを接続することがサポートされています。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

## <a name="obtain-ssl-certificate"></a>SSL 証明書を取得する
SSL 経由で Azure Database for MariaDB サーバーと通信するために必要な証明書を [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) からダウンロードし、その証明書ファイルをご利用のローカル ドライブ (このチュートリアルでは例として c:\ssl を使用) に保存します。
**Microsoft Internet Explorer と Microsoft Edge の場合:** ダウンロードが完了したら、証明書の名前を BaltimoreCyberTrustRoot.crt.pem に変更します。

## <a name="bind-ssl"></a>SSL をバインドする

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>MySQL Workbench を使用した SSL 経由でのサーバーへの接続
SSL 経由で安全に接続するように MySQL Workbench を構成します。 

1. [Setup New Connection]\(新しい接続のセットアップ\) ダイアログから、 **[SSL]** タブに移動します。 

1. **[SSL の使用]** フィールドを [必須] に更新します。

1. **[SSL CA File:]\(SSL CA ファイル:\)** フィールドに **BaltimoreCyberTrustRoot.crt.pem** ファイルの場所を入力します。 
    
    ![SSL 構成の保存](./media/howto-configure-ssl/mysql-workbench-ssl.png)

既存の接続の場合、SSL をバインドするには、接続アイコンを右クリックして編集を選択します。 次に **[SSL]** タブに移動して証明書ファイルをバインドします。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>MySQL CLI による SSL 経由でのサーバーへの接続
SSL 証明書をバインドする別の方法として、次のコマンドを実行して MySQL コマンド ライン インターフェイスを使用します。 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Windows 上で MySQL コマンド ライン インターフェイスを使用しているときに、エラー `SSL connection error: Certificate signature check failed` が表示されることがあります。 この場合、`--ssl-mode=REQUIRED --ssl-ca={filepath}` パラメーターを `--ssl` で置き換えます。

## <a name="enforcing-ssl-connections-in-azure"></a>Azure 内で SSL 接続を適用する 
### <a name="using-the-azure-portal"></a>Azure ポータルの使用
Azure portal を使用してご利用の Azure Database for MariaDB サーバーにアクセスし、 **[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、 **[SSL 接続を強制する]** 設定を有効または無効にし、 **[保存]** をクリックします。 Microsoft では、セキュリティ強化のため **[SSL 接続を強制する]** 設定を常に有効にしておくことをお勧めします。
![SSL の有効化](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI の使用
**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ Enabled または Disabled の値を使用します。
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>SSL 接続を確認する
SSL 経由でご利用の MariaDB サーバーに接続されていることを、MySQL の **status** コマンドを実行して確認します。
```sql
status
```
出力を確認することで、接続が暗号化されていることを確認します。次のように表示されます:**SSL:Cipher in use is AES256-SHA (SSL: 使用中の暗号は AES256 SHA です)** 

## <a name="sample-code"></a>サンプル コード
ご利用のアプリケーションから Azure Database for MariaDB へのセキュリティで保護された接続を SSL 経由で確立するためには、次のコード サンプルを参照してください。

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
