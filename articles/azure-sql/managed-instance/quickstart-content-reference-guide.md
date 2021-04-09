---
title: 使用開始に関するコンテンツ リファレンス
titleSuffix: Azure SQL Managed Instance
description: 'Azure SQL Managed Instance の使用を開始するのに役立つコンテンツのリファレンス。 '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: c84c81fa81cf4ba9be454eea5782927a09c3252a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690963"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance の使用を開始する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) は、最新の SQL Server (Enterprise Edition) データベース エンジンとの 100% 近い互換性を持つデータベースを作成し、一般的なセキュリティ上の懸念事項に対処するネイティブの[仮想ネットワーク (VNet)](../../virtual-network/virtual-networks-overview.md) の実装と、SQL Server の既存のお客様にとって有利な[ビジネス モデル](https://azure.microsoft.com/pricing/details/sql-database/)を提供します。

この記事では、SQL Managed Instance の構成と作成、およびデータベースの移行を迅速に行う方法を説明したコンテンツへのリファレンスを紹介します。

## <a name="quickstart-overview"></a>クイック スタートの概要

次のクイック スタートを使用すると、SQL Managed Instance をすばやく作成し、クライアント アプリケーション用に仮想マシンやポイント対サイト VPN 接続を構成し、`.bak` ファイルを使用して新しい SQL Managed Instance にデータベースを復元することができます。

### <a name="configure-environment"></a>環境の構成

最初の手順として、最初の SQL Managed Instance をその配置先のネットワーク環境で作成し、クエリを実行するコンピューターまたは仮想マシンから SQL Managed Instance への接続を有効にする必要があります。 次のガイドを使用できます。

- [Azure portal を使用して SQL Managed Instance を作成します](instance-create-quickstart.md)。 Azure portal で、必要なパラメーター (ユーザー名/パスワード、コア数、最大ストレージ容量) を構成します。また、Azure ネットワーク環境を自動的に作成できます。ネットワークの詳細やインフラストラクチャの要件を把握している必要はありません。 現在 SQL Managed Instance の作成が許可されている[サブスクリプションの種類](resource-limits.md#supported-subscription-types)を使用していることを確認します。 使用したい独自のネットワークがある場合、またはネットワークをカスタマイズする場合は、「[Azure SQL Managed Instance の既存の仮想ネットワークを構成する](vnet-existing-add-subnet.md)」または「[Azure SQL Managed Instance の仮想ネットワークを作成する](virtual-network-subnet-create-arm-template.md)」を参照してください。
- SQL Managed Instance は、パブリック エンドポイントを持たない独自の VNet に作成されます。 クライアント アプリケーションのアクセスのために、以下のクイック スタートのいずれかを使用して、**同じ VNet (異なるサブネット) 内に VM を作成する** か、**クライアント コンピューターから VNet へのポイント対サイト VPN 接続を作成する** ことができます。
  - お使いの環境からデータに直接アクセスするために、SQL Managed Instance で[パブリック エンドポイント](public-endpoint-configure.md)を有効にします。
  - SQL Server Management Studio などのクライアント アプリケーション接続のために、[SQL Managed Instance VNet に Azure 仮想マシン](connect-vm-instance-configure.md)を作成します。
  - SQL Server Management Studio とその他のクライアント接続アプリケーションがあるクライアント コンピューターから、[SQL Managed Instance へのポイント対サイト VPN 接続](point-to-site-p2s-configure.md)を設定します。 これは、SQL Managed Instance とその VNet への接続に関する 2 つのオプションのうちの 1 つです。

  > [!NOTE]
  > - ローカル ネットワークから Express Route またはサイト間接続を使用することもできますが、このような方法はこれらのクイック スタートでは扱いません。
  > - リテンション期間を 0 (無制限のリテンション期間) から他の値に変更した場合、リテンション期間は、リテンション期間の値が変更された後に書き込まれたログにのみ適用されることに注意してください (リテンション期間が無制限に設定されている間に書き込まれたログは、リテンション期間が有効になった後も保持されます)。

SQL Managed Instance を手動で作成する代わりに、[PowerShell](scripts/create-configure-managed-instance-powershell.md)、[PowerShell と Resource Manager テンプレート](./create-template-quickstart.md)、または [Azure CLI](/cli/azure/sql/mi#az-sql-mi-create) を使用して、このプロセスをスクリプト化して自動化することもできます。

### <a name="migrate-your-databases"></a>データベースを移行する

SQL Managed Instance を作成してアクセスを構成したら、SQL Server データベースの移行を開始できます。 移行するソース データベースに、サポートされていない機能がある場合、移行が失敗することがあります。 失敗を回避して互換性をチェックするために、[Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) を使用して SQL Server 上のデータベースを分析し、[FileStream](/sql/relational-databases/blob/filestream-sql-server) や複数のログ ファイルの存在など、SQL Managed Instance への移行を妨げる可能性のある問題を見つけることができます。 これらの問題を解決すると、データベースを SQL Managed Instance に移行する準備が整います。 [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) は、SQL Server 上のワークロードを記録し、SQL Managed Instance で再生できるもう 1 つの便利なツールです。これにより、SQL Managed Instance に移行した場合にパフォーマンス上の問題が発生するかどうかを判断できます。

データベースを SQL Managed Instance に移行できることを確認したら、ネイティブ SQL Server の復元機能を使用して、データベースを `.bak` ファイルから SQL Managed Instance に復元できます。 この方法を使用して、オンプレミスまたは Azure Virtual Machines にインストールされている SQL Server データベース エンジンからデータベースを移行することができます。 クイック スタートについては、[バックアップから SQL Managed Instance への復元](restore-sample-database-quickstart.md)に関するページを参照してください。 このクイック スタートでは、`RESTORE` Transact-SQL コマンドを使用して、Azure Blob Storage に格納されている `.bak` ファイルから復元します。

> [!TIP]
> `BACKUP` Transact-SQL コマンドを使用して Azure Blob Storage にデータベースのバックアップを作成する場合は、「[SQL Server Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)」を参照してください。

これらのクイック スタートを使用すると、データベース バックアップを迅速に作成、構成し、SQL Managed Instance に復元することができます。 一部のシナリオでは、SQL Managed Instance と必要なネットワーク環境のデプロイをカスタマイズまたは自動化する必要があります。 ここでは、これらのシナリオについて説明します。

## <a name="customize-network-environment"></a>ネットワーク環境をカスタマイズする

VNet/サブネットは、インスタンスが [Azure portal を使用して作成される](instance-create-quickstart.md)ときに自動的に構成できますが、VNet とサブネットのパラメーターを構成できるため、SQL Managed Instance でインスタンスの作成を開始する前に作成しておくことをお勧めします。 ネットワーク環境を作成して構成する最も簡単な方法は、インスタンスが配置されるネットワークとサブネットを作成して構成する [Azure リソースの展開](virtual-network-subnet-create-arm-template.md)テンプレートを使用することです。 必要な操作は、Azure Resource Manager のデプロイ ボタンを押し、フォームにパラメーターを入力することのみです。

別の方法として、この [PowerShell スクリプト](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)を使用してネットワークの作成を自動化することもできます。

SQL Managed Instance をデプロイする VNet とサブネットが既にある場合は、VNet とサブネットが[ネットワーク要件](connectivity-architecture-overview.md#network-requirements)を満たしていることを確認する必要があります。 この [PowerShell スクリプトを使用して、サブネットが正しく構成されていることを確認](vnet-existing-add-subnet.md)します。 このスクリプトはネットワークを検証し、問題を報告し、VNet/サブネットでどのような変更を行うべきかを伝え、必要な変更を行うことを提示します。 VNet/サブネットを手動で構成しない場合は、このスクリプトを実行します。 ネットワーク インフラストラクチャの大規模な再構成後にも、これを実行できます。 独自のネットワークを作成して構成する場合は、[接続アーキテクチャ](connectivity-architecture-overview.md)に関するページと、こちらの [SQL Managed Instance 環境の作成と構成に関する完全ガイド](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)をお読みください。

## <a name="migrate-to-a-sql-managed-instance"></a>SQL Managed Instance に移行する

前述のクイックスタートを使用すると、SQL Managed Instance を迅速にセットアップし、ネイティブ `RESTORE` 機能を使用してデータベースを移動することができます。 これは、簡単な概念実証を完了し、ソリューションが Managed Instance で動作することを確認する場合の手始めとして適しています。

ただし、運用データベース移行する場合、または何らかのパフォーマンス テストに使用する開発/テスト データベースを移行する場合であっても、次のような追加の手法を使用することを検討する必要があります。

- パフォーマンス テスト - ソース SQL Server インスタンスのベースライン パフォーマンス メトリックを測定し、データベースの移行先である SQL Managed Instance のパフォーマンス メトリックと比較する必要があります。 詳しくは、[best practices for performance comparison (パフォーマンス比較のベスト プラクティス)](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) に関する記事をご覧ください。
- オンライン移行 - この記事で説明されているネイティブの `RESTORE` の場合、データベースが復元される (そして、まだ Azure Blob Storage に格納されていない場合は Azure Blob Storage にコピーされる) まで待つ必要があります。 このため、特に大規模なデータベースでは、アプリケーションに多少のダウンタイムが発生します。 運用データベースを移動するには、[データ移行サービス (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) を使用して、最小限のダウンタイムでデータベースを移行します。 DMS は、ソース データベースで行われた変更を、復元する SQL Managed Instance データベースに増分方式でプッシュすることによってこれを実現します。 この方法であれば、最小限のダウンタイムでアプリケーションをソースからターゲットのデータベースにすばやく切り替えることができます。

[推奨される移行プロセス](migrate-to-instance-from-sql-server.md)の詳細を確認してください。

## <a name="next-steps"></a>次のステップ

- [SQL Managed Instance でサポートされている機能の概要の一覧についてはこちら](../database/features-comparison.md)、[詳細情報と既知の問題についてはこちら](transact-sql-tsql-differences-sql-server.md)を確認します。
- [SQL Managed Instance の技術的特性](resource-limits.md#service-tier-characteristics)について確認します。
- より高度な操作方法については、[SQL Managed Instance の使用方法](how-to-content-reference-guide.md)に関するページを参照してください。
- [オンプレミス データベースの適切な Azure SQL Managed Instance SKU を特定](/sql/dma/dma-sku-recommend-sql-db/)します。