---
title: Azure Database for PostgreSQL への接続に関する問題のトラブルシューティング
description: Azure Database for PostgreSQL への接続に関する問題のトラブルシューティング方法について説明します。
keywords: postgresql 接続, 接続文字列, 接続の問題, 一時的なエラー, 接続エラー
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: a78167e9b143a4c8d424947b489043dd45dfe2db
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886424"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql"></a>Azure Database for PostgreSQL への接続に関する問題のトラブルシューティング

接続の問題は、次をはじめとするさまざまなにことよって発生する可能性があります。

* ファイアウォールの設定
* 接続のタイムアウト
* 不適切なログイン情報
* Azure Database for PostgreSQL リソースの一部が上限に達している
* サービスのインフラストラクチャの問題
* サービスで実行中のメンテナンス
* サーバーのコンピューティング割り当てが、仮想コアの数の拡大縮小、または異なるサービス階層への移動によって変更された

一般的に、Azure Database for PostgreSQL への接続の問題は、次のカテゴリに分類されます。

* 一時的なエラー (短期間または断続的)
* 永続的または一時的でないエラー (定期的に繰り返されるエラー)

## <a name="troubleshoot-transient-errors"></a>一時的なエラーのトラブルシューティング

一時的なエラーは、メンテナンスの実行中や、システムのハードウェアまたはソフトウェアでのエラーの発生、またはサーバーの仮想コアまたはサービス階層の変更によって発生します。 Azure Database for PostgreSQL サービスには、HA が組み込まれており、この種の問題を自動的に緩和するように設計されています。 ただし、アプリケーションは、通常最大で 60 秒未満という短い間、サーバーへの接続を失います。 大規模なトランザクションによって復旧の実行に時間がかかるなど、一部のイベントは、場合によっては緩和に時間がかかることがあります。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>一時的な接続の問題を解決する手順

1. アプリケーションによってエラーが報告された時間帯に発生した既知の障害については、[Microsoft Azure サービス ダッシュボード](https://azure.microsoft.com/status)のページを参照してください。
2. Azure Database for PostgreSQL など、クラウド サービスに接続するアプリケーションでは、一時的なエラーを想定し、これらをアプリケーション エラーとしてユーザーに示すのではなく、再試行ロジックを実装して処理するようにしてください。 一時的なエラーを処理するためのベスト プラクティスと設計のガイドラインについては、「[Azure Database for PostgreSQL の一時的な接続エラーに対処する](concepts-connectivity.md)」を参照してください。
3. サーバーがリソースの制限に近づくと、エラーが一時的な接続の問題に見える場合があります。 「[Azure Database for PostgreSQL の制限事項](concepts-limits.md)」を参照してください。
4. 接続の問題が解消されない場合、アプリケーションでのエラーの継続時間が 60 秒を超えた場合、または 1 日にエラーが複数回発生した場合は、 **Azure サポート** サイトの [[サポートの要求]](https://azure.microsoft.com/support/options) を選択して、サポート要求を送信してください。

## <a name="troubleshoot-persistent-errors"></a>永続的なエラーのトラブルシューティング

アプリケーションが Azure Database for PostgreSQL への接続に引き続き失敗する場合は、一般的に、次のいずれかの問題が考えられます。

* サーバーのファイアウォールの構成:Azure Database for PostgreSQL サーバーのファイアウォールが、プロキシ サーバーやゲートウェイなど、クライアントからの接続を許可するように構成されていることを確認します。
* クライアントのファイアウォールの構成:クライアント上のファイアウォールで、データベース サーバーへの接続を許可する必要があります。 一部のファイアウォールでは、PostgreSQL などのアプリケーション名だけでなく、自分に権限のないサーバーの IP アドレスとポートも許可されている必要があります。
* ユーザー エラー:接続文字列のサーバー名の間違いや、ユーザー名に *\@servername* サフィックスがないなど、接続パラメーターを誤って入力している可能性があります。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>永続的な接続の問題を解決する手順

1. クライアントの IP アドレスを許可するには、 [ファイアウォール規則](howto-manage-firewall-using-portal.md) を設定します。 一時的なテストのためのみには、0.0.0.0 を開始 IP アドレス、255.255.255.255 を終了 IP アドレスとするファイアウォール規則を設定します。 これにより、サーバーがすべての IP アドレスに開かれます。 これによって接続の問題が解決する場合は、この規則を削除した後、IP アドレスまたはアドレス範囲を適切に制限するファイアウォール規則を作成します。
2. クライアントとインターネットの間のすべてのファイアウォールで、ポート 3306 が送信接続用に開かれていることを確認します。
3. 接続文字列およびその他の接続設定を確認します。
4. ダッシュ ボードでサービスの正常性を確認します。 リージョンで停止が起きていると考えられる場合は、新しいリージョンに復旧する手順について「[Azure Database for PostgreSQL によるビジネス継続性の概要](concepts-business-continuity.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Database for PostgreSQL の一時的な接続エラーに対処する](concepts-connectivity.md)
