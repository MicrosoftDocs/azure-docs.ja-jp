---
title: Azure SQL Database との接続に関する一般的な問題のトラブルシューティング
description: Azure SQL Database の一般的な接続エラーを特定して解決する手順。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: cd0ab6d89d88c594d283dc0718c0f58ebb98bf43
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090791"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Azure SQL Database との接続に関する一般的な問題のトラブルシューティング

Azure SQL Database との接続に失敗すると、[エラー メッセージ](sql-database-develop-error-messages.md)が表示されます。 この記事では、Azure SQL Database の接続に関する問題のトラブルシューティングに役立つトピックを紹介します。 ここでは接続の問題の[一般的な原因](#cause)を説明し、問題の特定に役立つ[トラブルシューティング ツール](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues)を紹介しています。また、[一時的なエラー](#troubleshoot-transient-errors)、および[永続的または一時的でないエラー](#troubleshoot-persistent-errors)を解決するトラブルシューティングの手順についてもご紹介します。 

接続の問題が発生したら、この記事のトラブルシューティング手順を実行してみてください。
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>原因

接続の問題が発生した原因としては、次のいずれかが考えられます。

* アプリケーションの設計プロセスで、ベスト プラクティスと設計のガイドラインが適用されていない。  開始するには、「 [SQL Database の開発: 概要](sql-database-develop-overview.md) 」をご覧ください。
* Azure SQL Database の再構成
* ファイアウォールの設定
* 接続のタイムアウト
* 不適切なログイン情報
* Azure SQL Database リソースの一部が上限に達している

一般的に、Azure SQL Database の接続の問題は、次のカテゴリに分類されます。

* [一時的なエラー (短期間または断続的)](#troubleshoot-transient-errors)
* [永続的または一時的でないエラー (定期的に発生するエラー)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Azure SQL Database の接続の問題のトラブルシューティング ツールを試してみる

特定の接続エラーが発生した場合は、[こちらのツール](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)をお試しください。問題の速やかな特定と解決に役立ちます。

## <a name="troubleshoot-transient-errors"></a>一時的なエラーのトラブルシューティング

アプリケーションが Azure SQL データベースに接続するとき、次のエラー メッセージが表示されます。

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> 通常、このエラー メッセージは (短時間の) 一時的なものです。
> 
> 

このエラーは、データベースが移動 (または再構成) されたり、アプリケーションがデータベースへの接続を失ったときに発生します。 データベースの再構成イベントは、計画されたイベント (ソフトウェアのアップグレードなど) または計画されていないイベント (プロセスのクラッシュ、負荷分散など) が原因で発生します。 通常、ほとんどの再構成イベントは一時的であり、長くて 1 分もかかりませんが、 これらのイベントは、大規模なトランザクションによる実行時間の長い復旧など、場合によっては、完了に時間がかかることがあります。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>一時的な接続の問題を解決する手順

1. アプリケーションによって報告されたエラーで発生している既知の障害については、 [Microsoft Azure サービス ダッシュボード](https://azure.microsoft.com/status) を参照してください。
2. Azure SQL Database など、クラウド サービスに接続するアプリケーションは、定期的な再構成イベントを想定し、これらをアプリケーション エラーとしてユーザーに示すのではなく、再試行ロジックを実装してこれらのエラーを処理します。 詳細および一般的な再試行戦略については、[一時エラー](sql-database-connectivity-issues.md)のセクション、および「[SQL Database の開発: 概要](sql-database-develop-overview.md)」のベスト プラクティスや設計ガイドラインをご覧ください。 その後、具体的な内容を「 [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md) 」のコード サンプルで確認してください。
3. データベースがリソースの制限に近づくと、一時的な接続の問題に見える場合があります。 [リソース制限](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached)に関するページを参照してください。
4. 接続の問題が解消されない場合、アプリケーションでのエラーの継続時間が 60 秒を超えた場合、または 1 日にエラーが複数回発生した場合は、 **Azure サポート** サイトの [[サポートの要求]](https://azure.microsoft.com/support/options) を選択して、サポート要求を送信してください。

## <a name="troubleshoot-persistent-errors"></a>永続的なエラーのトラブルシューティング
アプリケーションが Azure SQL Database の接続に引き続き失敗する場合は、一般的に、次のいずれかの問題が考えられます。

* ファイアウォールの構成: Azure SQL データベースまたはクライアント側のファイアウォールにより、Azure SQL Database への接続がブロックされています。
* クライアント側のネットワークの再構成: 新しい IP アドレス、プロキシ サーバーなど。
* ユーザー エラー: 接続パラメーター (接続文字列のサーバー名など) の入力間違いなど。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>永続的な接続の問題を解決する手順
1. クライアントの IP アドレスを許可するには、 [ファイアウォール規則](sql-database-configure-firewall-settings.md) を設定します。 一時的なテストのために、0.0.0.0 を IP アドレス範囲の開始アドレス、255.255.255.255 を終了アドレスとするファイアウォール規則を設定します。 これにより、サーバーがすべての IP アドレスに開かれます。 これによって接続の問題が解決する場合は、この規則を削除した後、IP アドレスまたはアドレス範囲を適切に制限するファイアウォール規則を作成します。 
2. クライアントとインターネットの間のすべてのファイアウォールで、送信接続用のポート 1433 が開いていることを確認します。 Azure Active Directory 認証のために追加で開く必要があるポートに関する詳しい情報は、「[Configure the Windows Firewall to Allow SQL Server Access (SQL Server のアクセスを許可するための Windows ファイアウォールの構成)](https://msdn.microsoft.com/library/cc646023.aspx)」および「[Hybrid Identity Required Ports and Protocols (ハイブリッド ID で必要なポートとプロトコル)](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports)」を参照してください。
3. 接続文字列およびその他の接続設定を確認します。 [接続の問題に関するトピック](sql-database-connectivity-issues.md#connections-to-sql-database)で、接続文字列のセクションを確認します。
4. ダッシュ ボードでサービスの正常性を確認します。 リージョンの障害があると思われる場合は、新しいリージョンへの回復手順を [障害からの回復](sql-database-disaster-recovery.md) のトピックで参照してください。

## <a name="next-steps"></a>次の手順
* [Microsoft Azure ドキュメントの検索](https://azure.microsoft.com/search/documentation/)
* [Azure SQL Database サービスの更新情報](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [一時的なエラーに対処するための一般的なガイダンス](../best-practices-retry-general.md)
* [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)

