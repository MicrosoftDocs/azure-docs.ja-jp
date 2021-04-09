---
title: トランスポート層セキュリティ (TLS) - Hyperscale (Citus) - Azure Database for PostgreSQL
description: TLS 接続を正しく使用するように Azure Database for PostgreSQL - Hyperscale (Citus) と関連アプリケーションを構成するための手順および情報。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87071456"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) での TLS の構成
Hyperscale (Citus) コーディネーター ノードでは、クライアント アプリケーションはトランスポート層セキュリティ (TLS) を使用して接続する必要があります。 データベース サーバーとクライアント アプリケーションの間に TLS を適用すれば、転送中のデータを機密状態に保つのに役立ちます。 以下で説明する追加の検証設定を行えば、中間者攻撃からも保護されます。

## <a name="enforcing-tls-connections"></a>TLS 接続の適用
アプリケーションは "接続文字列" を使用して、接続先のデータベースと接続の設定を識別します。 クライアントが異なれば、異なる設定が必要です。 一般的なクライアントで使用される接続文字列の一覧を確認するには、Azure portal で、サーバー グループの **[接続文字列]** セクションを参照してください。

TLS パラメーターの `ssl` と `sslmode` は、コネクタの機能によって変わり、たとえば `ssl=true`、`sslmode=require`、`sslmode=required` になります。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>アプリケーションまたはフレームワークが TLS 接続をサポートしているかどうかの確認
一部のアプリケーション フレームワークでは、PostgreSQL 接続に対して、既定で TLS が有効にされません。 ただし、セキュリティで保護された接続がないと、アプリケーションは Hyperscale (Citus) コーディネーター ノードに接続できません。 TLS 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 接続で証明書検証を必要とするアプリケーション
安全に接続するために、信頼された証明機関 (CA) 証明書ファイル (.cer) から生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 Azure Database for PostgreSQL - Hyperscale (Citus) に接続するための証明書は、 https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem にあります。 証明書ファイルをダウンロードし、希望の場所に保存します。

> [!NOTE]
>
> 証明書の信頼性を確認する場合は、OpenSSL コマンド ライン ツールを使用して SHA-256 フィンガープリントを検証できます。
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>psql を使用した接続
次の例は、psql コマンド ライン ユーティリティを使用して Hyperscale (Citus) コーディネーター ノードに接続する方法を示しています。 `sslmode=verify-full` 接続文字列設定を使用して、TLS 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `sslrootcert` パラメーターに渡します。

psql の接続文字列の例を次に示します。
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> `sslrootcert` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

## <a name="next-steps"></a>次のステップ
[Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則](concepts-hyperscale-firewall-rules.md)を使用して、セキュリティをさらに強化します。
