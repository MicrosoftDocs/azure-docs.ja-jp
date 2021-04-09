---
title: Azure Database for PostgreSQL - フレキシブル サーバーでの TLS/SSL を使用した暗号化された接続
description: Azure Database for PostgreSQL - フレキシブル サーバーで TLS/SSL を使用して接続する方法に関する手順および情報。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90931219"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでのトランスポート層セキュリティを使用した暗号化された接続

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL - フレキシブル サーバーによって、トランスポート層セキュリティ (TLS) (旧称 Secure Sockets Layer (SSL)) を使用したクライアント アプリケーションの PostgreSQL サービスへの接続がサポートされます。 TLS は、データベース サーバーとクライアント アプリケーションの間の暗号化されたネットワーク接続を保証する業界標準のプロトコルです。これを使用することで、ユーザーはコンプライアンス要件に準拠することができます。

Azure Database for PostgreSQL - フレキシブル サーバーによって、トランスポート層セキュリティ (TLS 1.2 以降) を使用した暗号化された接続がサポートされ、TLS 1.0 と TLS 1.1 を使用した受信接続はすべて拒否されます。 すべてのフレキシブル サーバーで TLS 接続の適用が有効になるため、フレキシブル サーバーに接続するために TLS/SSL を無効にすることはできません。

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>TLS/SSL 接続のために証明書の検証を必要とするアプリケーション
安全に接続するために、信頼された証明機関 (CA) 証明書ファイルから生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 Azure Database for PostgreSQL - フレキシブル サーバーにより、*DigiCert Global Root CA* が使用されます。 [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) から SSL 経由で通信するために必要なこの証明書をダウンロードし、証明書ファイルを希望する場所に保存します。 たとえば、このチュートリアルでは `c:\ssl` を使用します。


### <a name="connect-using-psql"></a>psql を使用した接続
"*プライベート アクセス (VNet 統合)* " を指定してフレキシブル サーバーを作成した場合は、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成し、それをフレキシブル サーバーと共に作成された VNet に追加できます。

"*パブリック アクセス (使用できる IP アドレス)* " を指定してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則のリストにローカル IP アドレスを追加できます。

次の例は、psql コマンド ライン インターフェイスを使用してサーバーに接続する方法を示しています。 `sslmode=verify-full` 接続文字列設定を使用して、TLS/SSL 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `sslrootcert` パラメーターに渡します。

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> `sslrootcert` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>アプリケーションまたはフレームワークが TLS 接続をサポートしているかどうかの確認

データベース サービスに PostgreSQL を使用しているアプリケーション フレームワークの中には、インストール時に既定で TLS が有効にならないものもあります。 PostgreSQL サーバーにより TLS 接続が適用されますが、アプリケーションが TLS 向けに構成されていない場合、アプリケーションがデータベース サーバーに接続できない可能性があります。 TLS 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
- [Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバー仮想ネットワークを作成し、管理する](./how-to-manage-virtual-network-cli.md)。
- [Azure Database for PostgreSQL - フレキシブル サーバーのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure Database for PostgreSQL - フレキシブル サーバーのファイアウォール規則](./concepts-networking.md#public-access-allowed-ip-addresses)について理解を深める
