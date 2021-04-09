---
title: DNS エイリアス
description: アプリケーションは Azure SQL Database のサーバー名のエイリアスに接続することができます。 エイリアスは参照する SQL Database をいつでも変更できるため、テストのスムーズな実施などに役立ちます。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 25c2157a8de237d0ec66caa72d59c810d419ac76
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94989124"
---
# <a name="dns-alias-for-azure-sql-database"></a>Azure SQL Database の DNS エイリアス
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database にはドメイン ネーム システム (DNS) サーバーが用意されています。 PowerShell と REST API では、[論理 SQL サーバー](logical-servers.md)名の [DNS エイリアスの作成や管理を行う呼び出し](#anchor-powershell-code-62x)を使用できます。

"*DNS エイリアス*" はサーバー名の代わりとして使用できます。 クライアント プログラムで接続文字列内にエイリアスを使用することができます。 DNS エイリアスには、クライアント プログラムを別のサーバーにリダイレクトする変換レイヤーがあります。 このレイヤーによって、ユーザーはすべてのクライアントとその接続文字列を特定して編集するという面倒な作業が不要になっています。

DNS エイリアスの一般的な使用法には、次のようなものがあります。

- サーバーに覚えやすい名前を付ける。
- 初期開発の時点ではエイリアスでテスト サーバーを参照する。 アプリケーションの運用を開始するときに運用サーバーを参照するようエイリアスを変更する。 テストから運用に移行するために、サーバーに接続する複数のクライアントの構成に変更を加える必要はありません。
- アプリケーションのデータベースが 1 つだけ別のサーバーに移動された場合、 エイリアスを変更するだけでよく、複数のクライアントの構成を変更する必要はありません。
- リージョンの停止時には、geo リストアを使用して、データベースを別のサーバーおよびリージョンで復旧します。 既存のエイリアスを変更して新しいサーバーを参照することで、既存のクライアント アプリケーションはサーバーに再接続できます。

## <a name="domain-name-system-dns-of-the-internet"></a>インターネットのドメイン ネーム システム (DNS)

インターネットも DNS を使用しています。 この DNS によりフレンドリ名がサーバーの名前に変換されます。

## <a name="scenarios-with-one-dns-alias"></a>1 つの DNS エイリアスを使用するシナリオ

システムを新しいサーバーに切り替える必要がある場合、 以前であれば、クライアント プログラムごとにすべての接続文字列を特定して更新する必要がありました。 しかし、接続文字列で DNS エイリアスを使用していれば、エイリアス プロパティを 1 つ更新するだけで済みます。

Azure SQL Database の DNS エイリアス機能は、次のようなシナリオで役立ちます。

### <a name="test-to-production"></a>テストから運用への移行

クライアント プログラム開発の開始時に、接続文字列で DNS エイリアスを使用します。 エイリアスのプロパティがサーバーのテスト版を参照するよう設定します。

その後、新しいシステムの運用を開始する場合に、運用サーバーを参照するようにエイリアスのプロパティを更新することができます。 クライアント プログラムを変更する必要はありません。

### <a name="cross-region-support"></a>複数のリージョンへの対応

ディザスター リカバリーによって、サーバーが別の地理的リージョンに移動される場合があります。 システムで DNS エイリアスを使用していれば、すべてのクライアントのすべての接続文字列を見つけて更新する必要がなくなります。 代わりに、Azure SQL Database をホストする新しいサーバーを参照するようにエイリアスを更新します。

## <a name="properties-of-a-dns-alias"></a>DNS エイリアスのプロパティ

サーバーの各 DNS エイリアスには次のプロパティが適用されます。

- *一意の名前:* 作成する各エイリアス名は、サーバー名の場合と同様、すべてのサーバーで一意である必要があります。
- *サーバーが必要:* DNS エイリアスは 1 つのサーバーを正確に参照していないと作成できません。また、サーバーはあらかじめ存在している必要があります。 エイリアスを更新した場合も、常に 1 つの既存サーバーを正確に参照していなければなりません。
  - サーバーを削除すると、そのサーバーを参照するすべての DNS エイリアスも Azure システムによって削除されます。
- *どのリージョンにもバインドされない:* DNS エイリアスはリージョンにバインドされません。 どの DNS エイリアスも任意のリージョンに存在する地理的サーバーを参照するよう更新できます。
  - ただし、別のサーバーを参照するようエイリアスを更新する際は、両方のサーバーが同じ Azure *サブスクリプション* に存在する必要があります。
- *権限:* DNS エイリアスを管理するユーザーには、*サーバー共同作成者* 権限以上の権限が必要です。 詳細については、[Azure portal における Azure ロールベースのアクセス制御の基礎](../../role-based-access-control/overview.md)に関するページを参照してください。

## <a name="manage-your-dns-aliases"></a>DNS エイリアスの管理

DNS エイリアスをプログラムによって管理できるように、REST API と PowerShell コマンドレットの両方が用意されています。

### <a name="rest-apis-for-managing-your-dns-aliases"></a>DNS エイリアスを管理するための REST API

REST API に関するドキュメントは、Web 上の次の場所で入手できます。

- [Azure SQL Database REST API](/rest/api/sql/)

また、REST API については、GitHub の次の場所でも確認できます。

- [Azure SQL Database DNS エイリアス REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>DNS エイリアスを管理するための PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

REST API を呼び出す PowerShell コマンドレットを使用できます。

DNS エイリアスを管理するために使用されている PowerShell コマンドレットのコード例が次の場所で確認できます。

- [Azure SQL Database を参照する DNS エイリアス用の PowerShell](dns-alias-powershell-create.md)

コード例で使用されているコマンドレットは次のとおりです。

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias):Azure SQL Database サービス システムに新しい DNS エイリアスを作成します。 このエイリアスはサーバー 1 を参照します。
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias):サーバー 1 に割り当てられているすべての DNS エイリアスを取得して一覧表示します。
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias):エイリアスによって参照されるように構成されているサーバー名を、サーバー 1 から サーバー 2 に変更します。
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias):エイリアスの名前を使用して、サーバー 2 から DNS エイリアスを削除します。

## <a name="limitations-during-preview"></a>プレビュー期間中の制限事項

DNS エイリアスには、現在、次の制限事項があります。

- *最大 2 分の遅延:* DNS エイリアスの更新または削除に最大 2 分かかります。
  - ただし、遅延の有無にかかわらず、エイリアスは元のサーバーへのクライアント接続の参照を直ちに停止します。
- *DNS の参照:* 現時点では、特定の DNS エイリアスが参照しているサーバーを確認する正式な方法は、[DNS の参照](/windows-server/administration/windows-commands/nslookup)の実行だけです。
- _テーブルの監査はサポートされていない:_ データベースで "*テーブル監査*" が有効になっているサーバーでは DNS エイリアスを使用できません。
  - テーブル監査は非推奨になりました。
  - [BLOB 監査](../../azure-sql/database/auditing-overview.md)に移行することをお勧めします。

## <a name="related-resources"></a>関連リソース

- [Azure SQL Database によるビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)。ディザスター リカバリーについても解説しています。
- [Azure REST API リファレンス](/rest/api/azure/)
- [サーバー DNS エイリアス API](/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database を参照する DNS エイリアス用の PowerShell](dns-alias-powershell-create.md)