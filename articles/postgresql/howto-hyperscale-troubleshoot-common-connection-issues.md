---
title: 接続のトラブルシューティング - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) への接続に関する問題のトラブルシューティング方法について説明します。
keywords: postgresql 接続, 接続文字列, 接続の問題, 一時的なエラー, 接続エラー
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91295619"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) への接続に関する問題のトラブルシューティング

接続の問題は、次のようないくつかの原因によって発生する可能性があります。

* ファイアウォールの設定
* 接続のタイムアウト
* サインイン情報が正しくない
* サーバー グループの接続制限に達した
* サービスのインフラストラクチャの問題
* サービスのメンテナンス
* コーディネーター ノードが新しいハードウェアにフェールオーバーしている

一般的に、Hyperscale (Citus) に接続できない問題は次のように分類できます。

* 一時的なエラー (短期間または断続的)
* 永続的または一時的でないエラー (定期的に繰り返されるエラー)

## <a name="troubleshoot-transient-errors"></a>一時的なエラーのトラブルシューティング

一時的なエラーは、さまざまな理由で発生します。 最も一般的なものには、システムのメンテナンス、ハードウェアまたはソフトウェアのエラー、コーディネーター ノードの仮想コアのアップグレードなどがあります。

Hyperscale (Citus) サーバー グループ ノードの高可用性を有効にすると、このような種類の問題を自動的に軽減できます。 ただし、アプリケーションでは一時的な接続の切断に備えるようにします。 また、大規模なトランザクションによって復旧の実行に時間がかかるなど、イベントによっては緩和に時間がかかることがあります。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>一時的な接続の問題を解決する手順

1. アプリケーションによってエラーが報告された時間帯に発生した既知の障害については、[Microsoft Azure サービス ダッシュボード](https://azure.microsoft.com/status)のページを参照してください。
2. Hyperscale (Citus) などのクラウド サービスに接続するアプリケーションでは、一時的なエラーを想定し、適切に対応するようにします。 たとえば、アプリケーションでは、このようなエラーをアプリケーション エラーとしてユーザーに表示するのではなく、再試行ロジックを実装して処理するようにします。
3. サーバー グループがリソースの制限に近づくと、一時的な接続の問題のようにエラーが発生する可能性があります。 ノードの RAM を増やすか、ワーカー ノードを追加してデータのバランスを再調整すると、役に立つ場合があります。
4. 接続の問題が続く場合、60 秒を超えて続く場合、または 1 日に複数回発生する場合は、[Azure サポート](https://azure.microsoft.com/support/options) サイトで **[サポートの利用]** を選択して Azure サポート リクエストを提出します。

## <a name="troubleshoot-persistent-errors"></a>永続的なエラーのトラブルシューティング

アプリケーションが引き続き Hyperscale (Citus) に接続できない場合、最も一般的な原因は、ファイアウォールの構成ミスまたはユーザー エラーです。

* コーディネーター ノードのファイアウォール構成:Hyperscale (Citus) サーバーのファイアウォールが、プロキシ サーバーやゲートウェイなど、クライアントからの接続を許可するように構成されていることを確認します。
* クライアントのファイアウォールの構成:クライアント上のファイアウォールで、データベース サーバーへの接続を許可する必要があります。 ファイアウォールによっては、名前を指定してアプリケーションを許可するだけでなく、サーバーの IP アドレスとポートを許可する必要があります。
* ユーザー エラー:接続文字列を再度確認します。 サーバー名など、パラメーターを間違って入力している可能性があります。 Azure portal では、さまざまな言語フレームワークと psql の接続文字列が見つかります。 Hyperscale (Citus) サーバー グループの **[接続文字列]** ページに移動します。 また、Hyperscale (Citus) クラスターにはデータベースが 1 つしかないことと、その事前定義名は **citus** であることにご留意ください。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>永続的な接続の問題を解決する手順

1. クライアントの IP アドレスを許可するには、 [ファイアウォール規則](howto-hyperscale-manage-firewall-using-portal.md) を設定します。 一時的なテストのためのみには、0.0.0.0 を開始 IP アドレス、255.255.255.255 を終了 IP アドレスとするファイアウォール規則を設定します。 この規則により、すべての IP アドレスに対してサーバーが開かれます。 この規則によって接続の問題が解決する場合は、それを削除した後、IP アドレスまたはアドレス範囲を適切に制限するファイアウォール規則を作成します。
2. クライアントとインターネットの間のすべてのファイアウォールで、ポート 5432 が送信接続用に開かれていることを確認します。
3. 接続文字列およびその他の接続設定を確認します。
4. ダッシュ ボードでサービスの正常性を確認します。

## <a name="next-steps"></a>次のステップ

* [Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則](concepts-hyperscale-firewall-rules.md)の概念を確認します
* [Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則を管理する](howto-hyperscale-manage-firewall-using-portal.md)方法を確認します
