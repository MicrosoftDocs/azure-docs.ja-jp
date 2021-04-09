---
title: SSL を構成する - Azure Database for MySQL
description: SSL 接続を正しく使用するために Azure Database for MySQL と関連するアプリケーションを適切に構成する方法について
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 1c3e5a44e01f3fa43b82644103066f5a03684ad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94591582"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する

Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

## <a name="step-1-obtain-ssl-certificate"></a>手順 1:SSL 証明書を取得する

Azure Database for MySQL サーバーで SSL 経由で通信するために必要な証明書を [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) からダウンロードし、その証明書ファイルをローカル ドライブ (このチュートリアルでは例として c:\ssl を使用) に保存します。
**Microsoft Internet Explorer と Microsoft Edge の場合:** ダウンロードが完了したら、証明書の名前を BaltimoreCyberTrustRoot.crt.pem に変更します。

>[!NOTE]
> お客様からのフィードバックに基づいて、既存の Baltimore Root CA のルート証明書の非推奨化を、2021 年 2 月 15 日 (02/15/2021) まで延長しました。

> [!IMPORTANT] 
> SSL ルート証明書は、2021 年 2 月 15 日 (02/15/2021) から期限切れになるように設定されています。 [新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)が使用されるようにアプリケーションを更新してください。 詳細については、[証明書の計画的な更新](concepts-certificate-rotation.md)に関するページをご覧ください

ソブリン クラウドにおけるサーバーの証明書については、[Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)、[Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)、[Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) の各リンクを参照してください。

## <a name="step-2-bind-ssl"></a>手順 2:SSL をバインドする

特定プログラミング言語の接続文字列については、下記の[サンプル コード](howto-configure-ssl.md#sample-code)を参照してください。

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>MySQL Workbench を使用した SSL 経由でのサーバーへの接続

SSL 経由で安全に接続するように MySQL Workbench を構成します。

1. [Setup New Connection]\(新しい接続のセットアップ\) ダイアログから、 **[SSL]** タブに移動します。

1. **[SSL の使用]** フィールドを [必須] に更新します。

1. **[SSL CA File:]\(SSL CA ファイル:\)** フィールドに **BaltimoreCyberTrustRoot.crt.pem** ファイルの場所を入力します。

   :::image type="content" source="./media/howto-configure-ssl/mysql-workbench-ssl.png" alt-text="SSL 構成の保存":::

既存の接続の場合、SSL をバインドするには、接続アイコンを右クリックして編集を選択します。 次に **[SSL]** タブに移動して証明書ファイルをバインドします。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>MySQL CLI による SSL 経由でのサーバーへの接続

SSL 証明書をバインドする別の方法として、次のコマンドを実行して MySQL コマンド ライン インターフェイスを使用します。

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Windows 上で MySQL コマンド ライン インターフェイスを使用しているときに、エラー `SSL connection error: Certificate signature check failed` が表示されることがあります。 この場合、`--ssl-mode=REQUIRED --ssl-ca={filepath}` パラメーターを `--ssl` で置き換えます。

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>手順 3:Azure 内で SSL 接続を適用する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure Portal から Azure Database for MySQL サーバーにアクセスし、 **[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、 **[SSL 接続を強制する]** 設定を有効または無効にし、 **[保存]** をクリックします。 Microsoft では、セキュリティ強化のため **[SSL 接続を強制する]** 設定を常に有効にしておくことをお勧めします。

:::image type="content" source="./media/howto-configure-ssl/enable-ssl.png" alt-text="Azure Database for MySQL に SSL 接続を適用する Azure portal のスクリーンショット":::

### <a name="using-azure-cli"></a>Azure CLI の使用

**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ Enabled または Disabled の値を使用します。

```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>手順 4:SSL 接続を確認する

MySQL の **status** コマンドを実行して、SSL 経由で MySQL サーバーに接続していることを確認します。

```dos
mysql> status
```

出力を確認することで、接続が暗号化されていることを確認します。次のように表示されます:**SSL:Cipher in use is AES256-SHA (SSL: 使用中の暗号は AES256 SHA です)**

## <a name="sample-code"></a>サンプル コード

アプリケーションから Azure Database for MySQL へのセキュリティで保護された接続を SSL 経由で確立するためには、次のコード サンプルを参照してください。

Azure Database for MySQL サービスでサポートされている[互換性のあるドライバー](concepts-compatibility.md)の一覧を参照してください。

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (PDO を使用)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

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
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector for Java)

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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector for Java)

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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
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
    SslCa = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>次のステップ

[Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)に従って、さまざまなアプリケーション接続オプションを確認する
