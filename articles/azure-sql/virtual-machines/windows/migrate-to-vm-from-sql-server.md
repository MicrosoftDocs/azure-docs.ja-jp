---
title: SQL Server データベースを仮想マシン上の SQL Server に移行する | Microsoft Docs
description: オンプレミスのユーザー データベースを Azure 仮想マシン上の SQL Server に移行する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6e9009040d2d02702f8a71c352716491d07d1f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704306"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>SQL Server データベースを Azure 仮想マシン上の SQL Server に移行する

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

オンプレミスの SQL Server ユーザー データベースを Azure 仮想マシン (VM) 上の SQL Server に移行する方法は多数あります。 この記事では、さまざまな方法について簡単に説明し、さまざまなシナリオに最適な方法を紹介します。


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 および SQL Server 2008 R2 のオンプレミス インスタンス向け[サポート ライフ サイクルの終了](https://www.microsoft.com/sql-server/sql-server-2008)が近づいています。 サポートを延長するには、SQL Server インスタンスを Azure VM に移行することも、セキュリティ更新プログラムの延長を購入してオンプレミスを維持することもできます。 詳細については、「[Extend support for SQL Server 2008 and 2008 R2 with Azure (Azure での SQL Server 2008 および 2008 R2 のサポート延長)](sql-server-2008-extend-end-of-support.md)」を参照してください

## <a name="what-are-the-primary-migration-methods"></a>主な移行方法

主な移行方法は次のとおりです。

* 圧縮機能を使用してオンプレミスのバックアップを実行した後、そのバックアップ ファイルを Azure VM に手動でコピーする。
* URL へのバックアップを実行した後、その URL から Azure VM に復元する。
* データ ファイルとログ ファイルをデタッチし、それらを Azure Blob Storage にコピーした後、URL から Azure VM 上の SQL Server にアタッチする。
* オンプレミスの物理マシンを Hyper-V VHD に変換して Azure Blob Storage にアップロードした後、アップロードした VHD を使用する新しい VM としてデプロイする。
* Windows のインポート/エクスポート サービスを使用して、ハード ドライブを発送する。
* オンプレミスの AlwaysOn 可用性グループのデプロイがある場合は、[Azure レプリカ追加ウィザード](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability)を使用して Azure でレプリカを作成し、フェールオーバーし、ユーザーに Azure データベース インスタンスを参照させる。
* SQL Server の[トランザクション レプリケーション](/sql/relational-databases/replication/transactional/transactional-replication)を使用して Azure SQL Server インスタンスをサブスクライバーとして構成し、レプリケーションを無効にし、ユーザーに Azure データベース インスタンスを参照させる。

> [!TIP]
> これらの同じ手法を使用して、Azure の SQL Server VM 間でデータベースを移動することもできます。 たとえば、ギャラリー イメージの SQL Server VM を別のバージョン/エディションにアップグレードする方法はサポートされていません。 この場合、新しいバージョン/エディションの新しい SQL Server VM を作成し、この記事で説明する移行手法のいずれかを使用してデータベースを移動する必要があります。 

## <a name="choose-a-migration-method"></a>移行方法を選択する

データ転送の最高のパフォーマンスを実現するには、圧縮されたバックアップ ファイルを使用して、データベース ファイルを Azure VM に移行します。

データベース移行プロセス中のダウンタイムを最小限に抑えるには、AlwaysOn の方法またはトランザクション レプリケーションの方法を使います。

上記の方法を使用できない場合は、手動でデータベースを移行します。 通常は、データベースのバックアップから始め、データベースのバックアップを Azure にコピーした後、データベースを復元します。 データベース ファイル自体を Azure にコピーしてからアタッチしてもかまいません。 この手動による Azure VM へのデータベースの移行プロセスは、複数の方法で実行できます。

> [!NOTE]
> 以前のバージョンの SQL Server から SQL Server 2014 または SQL Server 2016 にアップグレードする場合は、変更が必要かどうかを検討する必要があります。 移行プロジェクトの一環として、新しいバージョンの SQL Server でサポートされない機能への依存関係すべてを解決することをお勧めします。 サポートされるエディションとシナリオの詳細については、 [SQL Server へのアップグレード](/sql/database-engine/install-windows/upgrade-sql-server)に関するページを参照してください。

次の表に、主な移行方法の一覧を示します。ここでは、それぞれの方法を使用するタイミングとして最適な状況についても説明します。

| Method | 移行元データベースのバージョン | 移行先データベースのバージョン | 移行元データベースのバックアップ サイズ制限 | Notes |
| --- | --- | --- | --- | --- |
| [圧縮を使用してオンプレミスのバックアップを実行し、バックアップ ファイルを Azure 仮想マシンに手動でコピーする](#back-up-and-restore) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](../../../index.yml) | この手法は単純であり、マシン間でデータベースを移動するために十分にテストされています。 |
| [URL へのバックアップを実行し、その URL から Azure 仮想マシンに復元する](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 以上 | SQL Server 2012 SP1 CU2 以上 | SQL Server 2016 の場合は 12.8 TBまで、それ以外の場合は 1 TB まで | これは、Azure Storage を使用してバックアップ ファイルを VM に移動するもう 1 つの方法です。 |
| [データ ファイルとログ ファイルをデタッチして Azure Blob Storage にコピーした後、URL から Azure 仮想マシンの SQL Server にアタッチする](#detach-and-attach-from-a-url) | SQL Server 2005 以降 |SQL Server 2014 以降 | [Azure VM ストレージの制限](../../../index.yml) | この方法は、特にデータベースのサイズが非常に大きい場合に、 [Azure BLOB ストレージ サービスを使用してこれらのファイルを格納](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) し、Azure VM で実行されている SQL Server にファイルをアタッチするときに使用します。 |
| [オンプレミスのマシンを HYPER-V VHD に変換して Azure BLOB ストレージにアップロードし、アップロードしたその VHD を使用して、新しい仮想マシンをデプロイする](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](../../../index.yml) |[ご自身の SQL Server ライセンスを使用する](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)場合、古いバージョンの SQL Server で実行するデータベースを移行する場合、または他のユーザー データベースやシステム データベースに依存しているデータベースの移行の一部として、データベース システムとユーザー データベースを一緒に移行する場合に使用します。 |
| [Windows の Import/Export サービスを使用して、ハード ドライブを発送する](#ship-a-hard-drive) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](../../../index.yml) |[Windows の Import/Export サービス](../../../import-export/storage-import-export-service.md) は、データベースのサイズが非常に大きい場合など、手動でのコピーが遅すぎるときに使用します。 |
| [Azure のレプリカ追加ウィザードの使用](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 以降 |SQL Server 2012 以降 |[Azure VM ストレージの制限](../../../index.yml) |ダウンタイムが最小限になります。AlwaysOn のオンプレミスのデプロイがあるときに使います。 |
| [SQL Server のトランザクション レプリケーションを使用する](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](../../../index.yml) |ダウンタイムを最小限にする必要があり、Always On のオンプレミスのデプロイがないときに使用します。 |

## <a name="back-up-and-restore"></a>バックアップと復元

圧縮機能を使用してデータベースをバックアップし、バックアップを VM にコピーした後、データベースを復元します。 VM ディスクの最大サイズは 1 TB であるため、バックアップ ファイルが 1 TB を超える場合は、ストライプ セットを作成する必要があります。 この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1. オンプレミスの場所へのデータベースの完全バックアップを実行します。
2. 必要な SQL Server バージョンを使用して、仮想マシンを作成またはアップロードします。
3. 要件に基づいて接続をセットアップします。 「 [Connect to a SQL Server Virtual Machine on Azure (Resource Manager) (Azure での SQL Server 仮想マシンへの接続 (Resource Manager))](ways-to-connect-to-sql.md)」をご覧ください。
4. リモート デスクトップ、Windows エクスプローラー、またはコマンド プロンプトからコピー コマンドを使用して、バックアップ ファイルを VM にコピーします。

## <a name="backup-to-url-and-restore-from-url"></a>URL へのバックアップと URL からの復元

ローカル ファイルにバックアップするのではなく、[URL へのバックアップ](/sql/relational-databases/backup-restore/sql-server-backup-to-url)を使用し、後でその URL から VM に復元できます。 SQL Server 2016 では、ストライプ バックアップ セットがサポートされています。 それらはパフォーマンスのために推奨されており、BLOB ごとのサイズ制限を超える場合は必須です。 データベースのサイズが非常に大きい場合は、[Windows の Import/Export サービス](../../../import-export/storage-import-export-service.md)をお勧めします。

## <a name="detach-and-attach-from-a-url"></a>URL からデタッチとアタッチを行う

データベースとログ ファイルをデタッチし、[Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) に転送します。 その後、Azure VM で URL からデータベースをアタッチします。 物理データベース ファイルを BLOB ストレージに格納したい場合は、この方法を使用します。これは、非常に大規模なデータベースで役に立つことがあります。 この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1. オンプレミスのデータベース インスタンスからデータベース ファイルをデタッチします。
2. デタッチされたデータベース ファイルを、[AZCopy コマンド ライン ユーティリティ](../../../storage/common/storage-use-azcopy-v10.md)を使用して Azure Blob Storage にコピーします。
3. データベース ファイルを、Azure URL から Azure VM の SQL Server のインスタンスにアタッチします。

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>VM に変換して URL にアップロードし、新しい VM としてデプロイする

オンプレミスの SQL Server インスタンスのすべてのシステムとユーザー データベースを Azure 仮想マシンに移行するときはこの方法を使用します。 この手動による方法を使用して SQL Server インスタンス全体を移行するには、次の一般的な手順を実行します。

1. 物理マシンまたは仮想マシンを Hyper-V VHD に変換します。
2. [Add-AzureVHD コマンドレット](/previous-versions/azure/dn495173(v=azure.100))を使用して、VHD ファイルを Azure Storage にアップロードします。
3. アップロードした VHD を使用して、新しい仮想マシンをデプロイします。

> [!NOTE]
> アプリケーション全体を移行するには、 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)を使用することを検討します。

## <a name="ship-a-hard-drive"></a>ハード ドライブを発送する

ネットワーク経由のアップロードが実現不可能であるか、非常にコストがかかる場合には、[Windows の Import/Export サービス方法](../../../import-export/storage-import-export-service.md)を使用して、ファイルの大量のデータを Azure BLOB ストレージに転送します。 このサービスでは、データを含む 1 台以上のハード ドライブを Azure データ センターに発送し、そのデータ センターでデータがストレージ アカウントにアップロードされます。

## <a name="next-steps"></a>次のステップ

詳細については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。

> [!TIP]
> SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。

キャプチャ イメージから Azure 仮想マシン上に SQL Server を作成する手順については、CSS SQL Server Engineers ブログの「[Azure SQL 仮想マシンをキャプチャ イメージから "複製する" ためのヒント](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images)」を参照してください。