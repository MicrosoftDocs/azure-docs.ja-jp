---
title: 接続の問題のトラブルシューティング - Azure Database for MySQL - フレキシブル サーバー
description: Azure Database for MySQL フレキシブル サーバーへの接続に関する問題のトラブルシューティング方法について説明します。
keywords: mysql 接続,接続文字列,接続の問題,永続的なエラー,接続エラー
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: b328da01cd3b2ecb3eb5f183296eab808de27399
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90930872"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL への接続の問題のトラブルシューティング - フレキシブル サーバー

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

接続の問題は、次をはじめとするさまざまなにことよって発生する可能性があります。

* ファイアウォールの設定
* 接続のタイムアウト
* 不適切なログイン情報
* Azure Database for MySQL フレキシブル サーバー リソースの一部が上限に達している

この記事では、いくつかの一般的なエラーのトラブルシューティング方法と、これらのエラーを解決する手順について説明します。

## <a name="troubleshoot-common-errors"></a>一般的なエラーのトラブルシューティング

アプリケーションが Azure Database for MySQL フレキシブル サーバーへの接続に引き続き失敗する場合は、一般的に、次のいずれかの問題が考えられます。

* TLS/SSL を使用した暗号化された接続:フレキシブル サーバーでは、トランスポート層セキュリティ (TLS 1.2) を使用した暗号化された接続のみがサポートされ、**TLS 1.0 と TLS 1.1 を使用した受信接続はすべて拒否されます**。 TLS を無効にしたり、バージョンを変更したりすることはできません。 詳しくは、「[Azure Database for MySQL - フレキシブル サーバーでのトランスポート層セキュリティ (TLS 1.2) を使用した暗号化された接続](./how-to-connect-tls-ssl.md)」を参照してください。
- *プライベート アクセス (VNet 統合)* でのフレキシブル サーバー:フレキシブル サーバーと同じ仮想ネットワーク内から接続していることを確認します。 [Azure Database for MySQL フレキシブル サーバーの仮想ネットワーク] を参照してください<!--(./concepts-networking-virtual-network.md)-->
- *パブリック アクセス (許可された IP アドレス)* を使用したフレキシブル サーバーでは、クライアントからの接続を許可するようにファイアウォールが構成されていることを確認します。 「[Azure portal を使用してフレキシブル サーバーのファイアウォール規則を作成および管理する](./how-to-manage-firewall-portal.md)」を参照してください。
* クライアントのファイアウォールの構成:クライアント上のファイアウォールで、データベース サーバーへの接続を許可する必要があります。 一部のファイアウォールでは、MySQL などのアプリケーション名だけでなく、自分に権限のないサーバーの IP アドレスとポートも許可されている必要があります。
* ユーザー エラー:接続パラメーター (接続文字列のサーバー名など) を間違って入力した可能性があります。

### <a name="resolve-connectivity-issues"></a>接続に関する問題を解決する

* 暗号化された接続の詳細については、「[Azure Database for MySQL - フレキシブル サーバーでのトランスポート層セキュリティ (TLS 1.2) を使用した暗号化された接続](./how-to-connect-tls-ssl.md)」を参照してください。
* **パブリック アクセス (許可された IP アドレス)** を使用している場合は、クライアント IP アドレスを許可するように [ファイアウォール規則](./how-to-manage-firewall-portal.md)を設定します。 一時的なテストのためのみには、0.0.0.0 を開始 IP アドレス、255.255.255.255 を終了 IP アドレスとするファイアウォール規則を設定します。 これにより、サーバーがすべての IP アドレスに開かれます。 これによって接続の問題が解決する場合は、この規則を削除した後、IP アドレスまたはアドレス範囲を適切に制限するファイアウォール規則を作成します。
* クライアントとインターネットの間のすべてのファイアウォールで、ポート 3306 が送信接続用に開かれていることを確認します。
* 接続文字列およびその他の接続設定を確認します。 一般的な言語については、Azure portal のサーバーで使用可能な「**接続文字列**」ページの定義済みの接続文字列を参照してください。

## <a name="next-steps"></a>次のステップ
- [MySQL Workbench を使用して Azure Database for MySQL フレキシブル サーバーに接続し、そのデータにクエリを実行する](./connect-workbench.md)。
- [PHP を使用して Azure Database for MySQL フレキシブル サーバーに接続し、データのクエリを実行する](./connect-php.md)。
- [Python を使用して Azure Database for MySQL フレキシブル サーバーに接続し、データのクエリを実行する](./connect-python.md)。
