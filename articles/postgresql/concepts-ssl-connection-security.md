---
title: Azure Database for PostgreSQL での SSL 接続の構成
description: SSL 接続を適切に使用できるように、Azure Database for PostgreSQL と関連アプリケーションを構成するための手順と情報。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 5a0fc99052b18dc1fa837147aa914a473d27d832
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730010"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL での SSL 接続の構成
Azure Database for PostgreSQL では、クライアント アプリケーションを PostgreSQL サービスに接続する際、Secure Sockets Layer (SSL) の使用が優先されます。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

既定では、PostgreSQL データベース サービスは SSL 接続を要求するように構成されます。 クライアント アプリケーションが SSL 接続をサポートしていない場合は、必要に応じて、データベース サービスに接続するための SSL 要求を無効にできます。 

## <a name="enforcing-ssl-connections"></a>SSL 接続の適用
Azure Portal や CLI を使用してプロビジョニングされたすべての Azure Database for PostgreSQL サーバーで、SSL 接続の適用が既定で有効になります。 

同様に、Azure Portal のサーバーの下にある "接続文字列" 設定で事前定義された接続文字列には、SSL を使用してデータベース サーバーに接続するための一般的な言語の必須パラメーターが含まれます。 SSL パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

## <a name="configure-enforcement-of-ssl"></a>SSL 適用の構成
必要に応じて、SSL 接続の適用を無効にできます。 Microsoft Azure では、セキュリティ強化のため **[Enforce SSL connection (SSL 接続の適用)]** 設定は常に有効にしておくことをお勧めします。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用
Azure Database for PostgreSQL サーバーにアクセスし、**[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、**[Enforce SSL connection] \(SSL 接続の適用)** 設定を有効または無効にします。 その後、**[保存]** をクリックします。 

![接続のセキュリティ ‐ SSL 適用の無効化](./media/concepts-ssl-connection-security/1-disable-ssl.png)

この設定は、**[概要]** ページで **SSL 適用ステータス** インジケーターによって確認できます。

### <a name="using-azure-cli"></a>Azure CLI の使用
**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ `Enabled` 値と `Disabled` 値を使用します。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>アプリケーションまたはフレームワークが SSL 接続をサポートしているかどうかの確認
Drupal、Django など、データベース サービスに PostgreSQL を使用している一般的なアプリケーション フレームワークの中には、インストール時に既定で SSL が有効にならないものが多く存在します。 この場合、インストール後またはアプリケーションに固有の CLI コマンドを使用して、SSL 接続を有効にする必要があります。 PostgreSQL サーバーが SSL 接続を適用している場合、関連付けられているアプリケーションが正しく構成されていないと、そのアプリケーションは、データベース サーバーに接続できない可能性があります。 SSL 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 接続で証明書検証を必要とするアプリケーション
安全に接続するために、信頼された証明機関 (CA) 証明書ファイル (.cer) から生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 .cer ファイルを取得し、証明書をデコードして、アプリケーションにバインドするには、次の手順を参照してください。

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>証明機関 (CA) からの証明書ファイルのダウンロード 
Azure Database for PostgreSQL サーバーとの SSL 経由の通信に必要な証明書は[こちら](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)にあります。 証明書ファイルをローカルにダウンロードしてください。

### <a name="install-a-cert-decoder-on-your-machine"></a>ご自分のマシンへの証明書デコーダーのインストール 
[OpenSSL](https://github.com/openssl/openssl) を使用して、ご自分のアプリケーションがご使用のデータベース サーバーに安全に接続するために必要な証明書ファイルをデコードできます。 OpenSSL のインストール方法については、[OpenSSL のインストール手順](https://github.com/openssl/openssl/blob/master/INSTALL)に関するページを参照してください。 


### <a name="decode-your-certificate-file"></a>証明書ファイルのデコード
ダウンロードしたルート CA ファイルは、暗号化されています。 OpenSSL を使用すると、証明書ファイルをデコードできます。 これを行うには、次の OpenSSL コマンドを実行します。

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>SSL 証明書認証での Azure Database for PostgreSQL への接続
証明書を適切にデコードしたら、SSL 経由でデータベース サーバーに安全に接続できます。 サーバー証明書検証を許可するには、その証明書を、ユーザーのホーム ディレクトリの ~/.postgresql/root.crt ファイルに配置する必要があります  (Microsoft Windows の場合、ファイル名は %APPDATA%\postgresql\root.crt です)。 

#### <a name="connect-using-psql"></a>psql を使用した接続
次の例は、psql コマンド ライン ユーティリティを使用して PostgreSQL サーバーに正常に接続する方法を示しています。 作成した `root.crt` ファイルと、`sslmode=verify-ca` または `sslmode=verify-full`オプションを使用してください。

PostgreSQL コマンド ライン インターフェイスを使用して、次のコマンドを実行します。
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
成功すると、次の出力が表示されます。
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>次の手順
「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」に従って、さまざまなアプリケーション接続オプションをレビューします。
