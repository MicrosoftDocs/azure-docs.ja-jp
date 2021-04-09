---
title: アプリケーション開発の概要
description: SQL Database に接続するアプリケーションで使用できる接続ライブラリとベスト プラクティスについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782979"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>アプリケーション開発の概要 - SQL Database & SQL Managed Instance

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

この記事では、Azure 内のデータベースに接続するコードを記述するときの基本的な考慮事項について説明します。 この記事は、Azure SQL Database と Azure SQL Managed Instance に当てはまります。

## <a name="language-and-platform"></a>言語とプラットフォーム

さまざまな[プログラミング言語とプラットフォーム](connect-query-content-reference-guide.md)を使用して、Azure SQL Database に対する接続とクエリを実行することができます。 データベースに接続するために使用できる[サンプル アプリケーション](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0)を見つけることができます。

[cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS コード](https://code.visualstudio.com/)などのオープン ソース ツールを活用できます。 さらに、Azure SQL Database は、[Visual Studio](https://www.visualstudio.com/downloads/)、[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) などの Microsoft ツールと連携しています。 また、Azure portal、PowerShell、REST API も使用でき、生産性向上に役立ちます。

## <a name="authentication"></a>認証

Azure SQL Database へのアクセスは、ログインとファイアウォールで保護されます。 Azure SQL Database では、SQL Server および [Azure Active Directory 認証](authentication-aad-overview.md)ユーザーとログインの両方がサポートされています。 Azure Active Directory ログインは、SQL Managed Instance でのみ使用できます。 

詳細については、[データベース アクセスとログインの管理](logins-create-manage.md)に関するページを参照してください。

## <a name="connections"></a>接続

クライアント接続ロジックの中で、タイムアウトが 30 秒になるように既定値をオーバーライドします。 既定では 15 秒ですが、インターネットに依存する接続の場合、それでは短すぎます。

[接続プール](/dotnet/framework/data/adonet/sql-server-connection-pooling)を使用している場合は、プログラムで接続をアクティブに使用しておらず、再使用の準備をしていない時間は、接続を必ず閉じてください。

実行時間の長いトランザクションは避けてください。インフラストラクチャまたは接続のエラーにより、トランザクションがロールバックされる可能性があるためです。 可能であれば、トランザクションを複数のより小さなトランザクションに分割し、[バッチ処理を使用してパフォーマンスを向上させます](../performance-improve-use-batching.md)。

## <a name="resiliency"></a>回復性

Azure SQL Database はクラウド サービスであり、基になるインフラストラクチャで、またはクラウド エンティティ間の通信で発生する一時エラーが想定される場合があります。 Azure SQL Database は推移的なインフラストラクチャ エラーに対する回復性がありますが、これらのエラーが接続に影響する場合があります。 SQL Database への接続中に一時エラーが発生した場合は、コードで[呼び出しを再試行する](troubleshoot-common-connectivity-issues.md)必要があります。 再試行ロジックでは、複数のクライアントが同時に再試行することでサービスに過大な負荷がかかるのを防ぐために、バックオフ ロジックを使用することをお勧めします。 再試行ロジックは、[SQL Database クライアント プログラムのエラー メッセージ](troubleshoot-common-errors-issues.md)によって異なります。

Azure SQL Database の計画メンテナンス イベントに備える方法の詳細については、「[Azure SQL Database での Azure メンテナンス イベントの計画](planned-maintenance.md)」を参照してください。

## <a name="network-considerations"></a>ネットワークに関する考慮事項

- クライアント プログラムをホストするコンピューターのファイアウォールで、ポート 1433 での発信 TCP が許可されていることを確認します。  詳細情報: [Azure SQL Database ファイアウォールの構成](firewall-configure.md)。
- クライアントが Azure 仮想マシン (VM) で実行されているときに、クライアント プログラムが SQL Database に接続する場合、VM で特定のポートの範囲を開く必要があります。 詳細情報: 「[ADO.NET 4.5 用の 1433 以外のポート](adonet-v12-develop-direct-route-ports.md)」を参照してください。
- Azure SQL Database へのクライアント接続はプロキシを使用せずに、データベースに直接やり取りする場合があります。 1433 以外のポートが重要になります。 詳細については、「[Azure SQL Database connectivity architecture](connectivity-architecture.md)」 (Azure SQL Database 接続アーキテクチャ) および「[ADO.NET 4.5 用の 1433 以外のポート](adonet-v12-develop-direct-route-ports.md)」を参照してください。
- SQL Managed Instance のインスタンスに対するネットワーク構成については、[SQL Managed Instance のネットワーク構成](../managed-instance/how-to-content-reference-guide.md#network-configuration)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[SQL Database](sql-database-paas-overview.md) と [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) の機能すべてを確認します。

作業を開始するには、[Azure SQL Database](quickstart-content-reference-guide.md) および [Azure SQL Managed Instance](../managed-instance/quickstart-content-reference-guide.md) のガイドを参照してください。