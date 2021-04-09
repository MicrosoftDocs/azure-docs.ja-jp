---
title: SSL/TLS - Azure Database for PostgreSQL (単一サーバー)
description: Azure Database for PostgreSQL - 単一サーバーの TLS 接続を構成する方法について説明します。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000948"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (単一サーバー) で TLS 接続を構成する

Azure Database for PostgreSQL では、トランスポート層セキュリティ (TLS) (旧称 Secure Sockets Layer (SSL)) を使用してクライアント アプリケーションを PostgreSQL サービスに接続することが推奨されます。 お使いのデータベース サーバーとクライアント アプリケーション間に TLS 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。

既定では、PostgreSQL データベース サービスは TLS 接続を要求するように構成されます。 クライアント アプリケーションで TLS 接続がサポートされていない場合は、TLS の要求を無効にすることもできます。

>[!NOTE]
> お客様からのフィードバックに基づいて、既存の Baltimore Root CA のルート証明書の非推奨を、2021 年 2 月 15 日 (02/15/2021) まで延長しました。

> [!IMPORTANT] 
> SSL ルート証明書は、2021 年 2 月 15 日 (02/15/2021) から期限切れになるように設定されています。 [新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)が使用されるようにアプリケーションを更新してください。 詳細については、[証明書の計画的な更新](concepts-certificate-rotation.md)に関するページをご覧ください


## <a name="enforcing-tls-connections"></a>TLS 接続の適用

Azure portal や CLI を使用してプロビジョニングされたすべての Azure Database for PostgreSQL サーバーで、TLS 接続の適用が既定で有効になります。 

同様に、Azure portal のサーバー下にある [接続文字列] 設定で事前定義された接続文字列には、TLS を使用してデータベース サーバーに接続するための一般的な言語の必須パラメーターが含まれます。 TLS パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

## <a name="configure-enforcement-of-tls"></a>TLS 適用の構成

必要に応じて、TLS 接続の適用を無効にできます。 Microsoft Azure では、セキュリティ強化のため **[Enforce SSL connection (SSL 接続の適用)]** 設定は常に有効にしておくことをお勧めします。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure Database for PostgreSQL サーバーにアクセスし、 **[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、 **[Enforce SSL connection] \(SSL 接続の適用)** 設定を有効または無効にします。 その後、 **[保存]** をクリックします。

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="接続のセキュリティ ‐ TLS/SSL 適用の無効化":::

この設定は、 **[概要]** ページの **SSL 適用ステータス** インジケーターで確認できます。

### <a name="using-azure-cli"></a>Azure CLI の使用

**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ `Enabled` 値と `Disabled` 値を使用します。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>アプリケーションまたはフレームワークが TLS 接続をサポートしているかどうかの確認

データベース サービスに PostgreSQL を使用しているアプリケーション フレームワークの中には、インストール時に既定で TLS が有効にならないものもあります。 PostgreSQL サーバーが TLS 接続を適用していても、アプリケーションで TLS が構成されていない場合、アプリケーションはデータベース サーバーに接続できない可能性があります。 TLS 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 接続で証明書検証を必要とするアプリケーション

安全に接続するために、信頼された証明機関 (CA) 証明書ファイルから生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 Azure Database for PostgreSQL サーバーに接続するための証明書は、 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem にあります。 証明書ファイルをダウンロードし、希望の場所に保存します。 

ソブリン クラウドにおけるサーバーの証明書については、[Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)、[Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)、[Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) の各リンクを参照してください。

### <a name="connect-using-psql"></a>psql を使用した接続

次の例は、psql コマンド ライン ユーティリティを使用して PostgreSQL サーバーに接続する方法を示しています。 `sslmode=verify-full` 接続文字列設定を使用して、TLS/SSL 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `sslrootcert` パラメーターに渡します。

次のコマンドは、psql 接続文字列の例です。

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> `sslrootcert` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL (単一サーバー) での TLS 適用

Azure Database for PostgreSQL (単一サーバー) では、トランスポート層セキュリティ (TLS) を使用してデータベース サーバーに接続するクライアントの暗号化をサポートしています。 TLS は、データベース サーバーとクライアント アプリケーションとの間のセキュリティで保護されたネットワーク接続を確保する業界標準のプロトコルであり、コンプライアンス要件への準拠を可能にします。

### <a name="tls-settings"></a>TLS の設定

Azure Database for PostgreSQL (単一サーバー) には、クライアント接続に TLS バージョンを適用する機能が用意されています。 TLS バージョンを適用するには、 **[TLS の最小バージョン]** オプション設定を使用します。 このオプション設定には次の値を使用できます。

|  TLS の最小設定             | サポートされているクライアント TLS バージョン                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (既定値) | TLS は不要                      |
| TLS1_0                           | Tls 1.0、TLS 1.1、TLS 1.2 以降 |
| TLS1_1                           | TLS 1.1、TLS 1.2 以降          |
| TLS1_2                           | TLS バージョン 1.2 以降           |


たとえば、この最小 TLS バージョンを TLS 1.0 に設定すると、サーバーでは TLS 1.0、1.1、1.2 以上を使用するクライアントからの接続が許可されます。 また、これを 1.2 に設定すると、TLS 1.2+ を使用するクライアントからの接続だけが許可され、TLS 1.0 および TLS 1.1 を使用した接続はすべて拒否されます。

> [!Note] 
> 既定では、Azure Database for PostgreSQL では最小の TLS バージョン (設定 `TLSEnforcementDisabled`) は強制されません。
>
> 最小の TLS バージョンを強制すると、後で最小バージョンの強制を無効にすることはできません。

Azure Database for PostgreSQL (単一サーバー) の TLS 設定を行う方法については、[TLS 設定の構成方法](howto-tls-configurations.md)に関するページを参照してください。

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーでサポートされる暗号

SSL/TLS 通信の一部として、暗号スイートが検証され、サポートされている暗号スイートのみがデータベース サーバーとの通信を許可されます。 暗号スイートの検証は、[ゲートウェイ レイヤー](concepts-connectivity-architecture.md#connectivity-architecture)で制御され、ノード自体では明示的には行われません。 暗号スイートが以下の一覧に示されているスイートのいずれかと一致しない場合、受信クライアント接続は拒否されます。

### <a name="cipher-suite-supported"></a>サポートされている暗号スイート

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>次のステップ

[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)に関する記事で、さまざまなアプリケーション接続オプションを確認します。

- [TLS の構成](howto-tls-configurations.md)方法について