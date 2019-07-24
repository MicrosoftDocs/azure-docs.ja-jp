---
title: SQL Database アプリケーションの開発の概要 | Microsoft Docs
description: SQL Database に接続するアプリケーションで使用できる接続ライブラリとベスト プラクティスについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: efb6d932e616ada6b8dfff637af469c16fc2f293
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761823"
---
# <a name="sql-database-application-development-overview"></a>SQL Database アプリケーションの開発の概要

この記事では、Azure SQL Database に接続するコードを記述するときの基本的な考慮事項について説明します。 この記事は、Azure SQL Database のすべてのデプロイ モデル (単一データベース、エラスティック プール、マネージド インスタンス) に適用されます。

> [!TIP]
> Azure SQL Database を設定する必要がある場合は、[単一データベース](sql-database-single-database-quickstart-guide.md)および[マネージド インスタンス](sql-database-managed-instance-quickstart-guide.md)の作業の開始ガイドを確認してください。
>

## <a name="language-and-platform"></a>言語とプラットフォーム

さまざまな[プログラミング言語とプラットフォーム](sql-database-connect-query.md)を使用して、Azure SQL Database に対する接続とクエリを実行することができます。 Azure SQL Database に接続するために使用できる[サンプル アプリケーション](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0)を見つけることができます。

[cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS コード](https://code.visualstudio.com/)などのオープン ソース ツールを活用できます。 さらに、Azure SQL Database は、[Visual Studio](https://www.visualstudio.com/downloads/)、[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) などの Microsoft ツールと連携しています。 また、Azure portal、PowerShell、REST API も使用でき、生産性向上に役立ちます。

## <a name="authentication"></a>Authentication

Azure SQL Database へのアクセスは、ログインとファイアウォールで保護されます。 Azure SQL Database では、SQL Server および [Azure Active Directory (AAD) 認証](sql-database-aad-authentication.md)ユーザーとログインの両方がサポートされています。 AAD ログインは、Managed Instance でのみ使用できます。 

詳細については、[データベース アクセスとログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

## <a name="connections"></a>Connections

クライアント接続ロジックの中で、タイムアウトが 30 秒になるように既定値をオーバーライドします。 既定では 15 秒ですが、インターネットに依存する接続の場合、それでは短すぎます。

[接続プール](https://msdn.microsoft.com/library/8xx3tyca.aspx)を使用している場合は、プログラムで接続をアクティブに使用しておらず、再使用の準備をしていない時間は、接続を必ず閉じてください。

実行時間の長いトランザクションは避けてください。インフラストラクチャまたは接続のエラーにより、トランザクションがロールバックされる可能性があるためです。 可能であれば、トランザクションを複数のより小さなトランザクションに分割し、[バッチ処理を使用してパフォーマンスを向上させます](sql-database-use-batching-to-improve-performance.md)。

## <a name="resiliency"></a>回復性

Azure SQL Database はクラウド サービスであり、基になるインフラストラクチャで、またはクラウド エンティティ間の通信で発生する一時エラーが想定される場合があります。 Azure SQL Database は推移的なインフラストラクチャ エラーに対する回復性がありますが、これらのエラーが接続に影響する場合があります。 SQL Database への接続中に一時エラーが発生した場合は、コードで[呼び出しを再試行する](sql-database-connectivity-issues.md)必要があります。 再試行ロジックでは、複数のクライアントが同時に再試行することで SQL Database に過大な負荷がかかるのを防ぐために、バックオフ ロジックを使用することをお勧めします。 再試行ロジックは、[SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md)によって異なります。

Azure SQL データベースの計画メンテナンス イベントに備える方法の詳細については、「[Azure SQL Database での Azure メンテナンス イベントの計画](sql-database-planned-maintenance.md)」を参照してください。

## <a name="network-considerations"></a>ネットワークに関する考慮事項

- クライアント プログラムをホストするコンピューターのファイアウォールで、ポート 1433 での発信 TCP が許可されていることを確認します。  詳細情報: [Azure SQL Database ファイアウォールの構成](sql-database-configure-firewall-settings.md)。
- クライアントが Azure 仮想マシン (VM) で実行されているときに、クライアント プログラムが SQL Database に接続する場合、VM で特定のポートの範囲を開く必要があります。 詳細情報: 「[ADO.NET 4.5 用の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。
- Azure SQL Database へのクライアント接続はプロキシを使用せずに、データベースに直接やり取りする場合があります。 1433 以外のポートが重要になります。 詳細については、「[Azure SQL Database connectivity architecture](sql-database-connectivity-architecture.md)」 (Azure SQL データベース接続アーキテクチャ) および「[ADO.NET 4.5 用の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。
- マネージド インスタンス向けのネットワーク構成については、[マネージド インスタンス向けのネットワーク構成](sql-database-howto-managed-instance.md#network-configuration)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

[SQL Database の機能](sql-database-technical-overview.md)すべてを確認します。
