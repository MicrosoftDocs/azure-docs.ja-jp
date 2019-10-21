---
title: Azure Database for PostgreSQL - Hyperscale (Citus) での SSL の構成
description: SSL 接続を正しく使用するように Azure Database for PostgreSQL - Hyperscale (Citus) と関連アプリケーションを構成するための手順および情報。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: be02c9114f30a63fe710cfd2fc56347bfc68fab5
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275297"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) での SSL の構成
Hyperscale (Citus) コーディネーター ノードへのクライアント アプリケーション接続には、Secure Sockets Layer (SSL) が必要です。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、中間者 (man in the middle) 攻撃から保護されます。

## <a name="enforcing-ssl-connections"></a>SSL 接続の適用
Azure portal を使用してプロビジョニングされたすべての Azure Database for PostgreSQL サーバーでは、SSL 接続の適用が既定で有効になります。 

同様に、Azure Portal のサーバー下にある [接続文字列] 設定で事前定義された接続文字列には、SSL を使用してデータベース サーバーに接続するための一般的な言語の必須パラメーターが含まれます。 SSL パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>アプリケーションまたはフレームワークが SSL 接続をサポートしているかどうかの確認
データベース サービスに PostgreSQL を使用しているアプリケーション フレームワークの中には、インストール時に既定で SSL が有効にならないものもあります。 PostgreSQL サーバーが SSL 接続を適用していても、アプリケーションで SSL が構成されていない場合、アプリケーションはデータベース サーバーに接続できない可能性があります。 SSL 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 接続で証明書検証を必要とするアプリケーション
安全に接続するために、信頼された証明機関 (CA) 証明書ファイル (.cer) から生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 Azure Database for PostgreSQL - Hyperscale (Citus) に接続するための証明書は、 https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem にあります。 証明書ファイルをダウンロードし、希望の場所に保存します。

### <a name="connect-using-psql"></a>psql を使用した接続
次の例は、psql コマンド ライン ユーティリティを使用して Hyperscale (Citus) コーディネーター ノードに接続する方法を示しています。 `sslmode=verify-full` 接続文字列設定を使用して、SSL 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `sslrootcert` パラメーターに渡します。

psql の接続文字列の例を次に示します。
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> `sslrootcert` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

## <a name="next-steps"></a>次の手順
[Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則](concepts-hyperscale-firewall-rules.md)を使用して、セキュリティをさらに強化します。
