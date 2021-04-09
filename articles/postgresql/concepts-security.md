---
title: Azure Database for PostgreSQL のセキュリティ - 単一サーバー
description: Azure Database for PostgreSQL のセキュリティ機能の概要 - 単一サーバー。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be042a0ec076538cf0f0d155667acea6f1ae19cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91710483"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL のセキュリティ - 単一サーバー

Azure Database for PostgreSQL サーバーには、データを保護するために使用できるセキュリティのレイヤーが複数あります。 この記事では、これらのセキュリティ オプションについてまとめています。

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

### <a name="in-transit"></a>転送中
Azure Database for PostgreSQL では、トランスポート層セキュリティを使用して転送中のデータを暗号化することによってデータをセキュリティで保護します。 既定では、暗号化 (SSL/TLS) が適用されます。

### <a name="at-rest"></a>保存
Azure Database for PostgreSQL サービスでは、保存データのストレージ暗号化に FIPS 140-2 認証済みの暗号モジュールが使用されます。 データ (バックアップを含む) は、クエリの実行中に作成される一時ファイルも含めて、ディスク上で暗号化されます。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にすることはできません。


## <a name="network-security"></a>ネットワークのセキュリティ
Azure Database for PostgreSQL サーバーへの接続は、最初にリージョン ゲートウェイ経由でルーティングされます。 サーバーの IP アドレスは保護されていますが、ゲートウェイにはパブリックにアクセス可能な IP があります。 ゲートウェイの詳細については、[接続アーキテクチャに関する記事](concepts-connectivity-architecture.md)をご覧ください。  

新しく作成された Azure Database for PostgreSQL サーバーには、すべての外部接続をブロックするファイアウォールが備わっています。 ゲートウェイに到達しますが、サーバーへの接続は許可されません。 

### <a name="ip-firewall-rules"></a>IP ファイアウォール規則
IP ファイアウォール規則では、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。 詳細については、[ファイアウォール規則の概要](concepts-firewall-rules.md)に関するページを参照してください。

### <a name="virtual-network-firewall-rules"></a>仮想ネットワーク ファイアウォールの規則
仮想ネットワーク サービス エンドポイントにより、Azure バックボーンを介して仮想ネットワーク接続が拡張されます。 仮想ネットワークの規則を使用して、仮想ネットワーク内の選択したサブネットからの接続を許可するように、Azure Database for PostgreSQL サーバーを有効にすることができます。 詳細については、[仮想ネットワーク サービス エンドポイントの概要](concepts-data-access-and-security-vnet.md)に関するページを参照してください。

### <a name="private-ip"></a>プライベート IP
Private Link を使用すると、プライベート エンドポイントを経由して Azure 内の Azure Database for PostgreSQL 単一サーバーに接続できます。 Azure Private Link は基本的に、プライベート仮想ネットワーク (VNet) 内に Azure サービスを提供します。 PaaS リソースには、VNet 内のその他のリソースと同様に、プライベート IP アドレスを使用してアクセスできます。 詳細については、[Private Link の概要](concepts-data-access-and-security-private-link.md)に関するページを参照してください。


## <a name="access-management"></a>アクセス管理

Azure Database for PostgreSQL サーバーを作成するときに、管理者ロールの資格情報を指定します。 この管理者ロールは、追加の [PostgreSQL ロール](https://www.postgresql.org/docs/current/user-manag.html)を作成するために使用できます。

[Azure Active Directory (AAD) 認証](concepts-aad-authentication.md)を使用してサーバーに接続することもできます。


## <a name="threat-protection"></a>脅威の防止

サーバーにアクセスしたり悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出する [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) にオプトインできます。

[監査ログ](concepts-audit.md)を使うと、データベースのアクティビティを追跡できます。 

## <a name="migrating-from-oracle"></a>Oracle からの移行

Oracle では、テーブルおよびテーブルスペースのデータを暗号化するための Transparent Data Encryption (TDE) がサポートされています。 Azure for PostgreSQL では、データはさまざまなレイヤーで自動的に暗号化されます。 このページの「保存」セクションをご覧ください。また、[カスタマー マネージド キー](./concepts-data-encryption-postgresql.md)や[インフラストラクチャの二重暗号化](./concepts-infrastructure-double-encryption.md)などのさまざまなセキュリティのトピックも参照してください。 [Azure for PostgreSQL](./concepts-extensions.md) でサポートされている、[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html) 拡張機能を使用することもできます。

## <a name="next-steps"></a>次のステップ
- [IP](concepts-firewall-rules.md) または[仮想ネットワーク](concepts-data-access-and-security-vnet.md)のファイアウォール規則を有効にする
- Azure Database for PostgreSQL の [Azure Active Directory 認証](concepts-aad-authentication.md)について学習する
