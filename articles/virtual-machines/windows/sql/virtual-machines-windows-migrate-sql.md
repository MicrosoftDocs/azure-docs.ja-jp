---
title: VM の SQL Server への SQL Server データベースの移行 | Microsoft Docs
description: オンプレミスのユーザー データベースを Azure 仮想マシンの SQL Server に移行する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5a8b66c181505a617b002d1a45675d4677588b1c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102197"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Azure VM の SQL Server への SQL Server データベースの移行

オンプレミスの SQL Server ユーザー データベースを Azure VM の SQL Server に移行する方法は多数あります。 この記事では、さまざまな方法について簡単に説明し、さまざまなシナリオに最適な方法を紹介します。


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 および SQL Server 2008 R2 のオンプレミス インスタンス向け[サポート ライフ サイクルの終了](https://www.microsoft.com/sql-server/sql-server-2008)が近づいています。 サポートを延長するには、SQL Server インスタンスを Azure VM に移行することも、セキュリティ更新プログラムの延長を購入してオンプレミスを維持することもできます。 詳細については、「[Extend support for SQL Server 2008 and 2008 R2 with Azure (Azure での SQL Server 2008 および 2008 R2 のサポート延長)](virtual-machines-windows-sql-server-2008-eos-extend-support.md)」を参照してください

## <a name="what-are-the-primary-migration-methods"></a>主な移行方法
主な移行方法は次のとおりです。

* 圧縮機能を使用してオンプレミスのバックアップの実行し、そのバックアップ ファイルを Azure 仮想マシンに手動でコピーする
* URL へのバックアップを実行し、その URL から Azure 仮想マシンに復元する
* データとログ ファイルをデタッチしてから、Azure BLOB ストレージにコピーし、その後、URL から Azure VM の SQL Server にアタッチする
* オンプレミスの物理マシンを HYPER-V VHD に変換して Azure BLOB ストレージにアップロードし、アップロードしたその VHD を使用して、新しい VM としてデプロイする
* Windows の Import/Export サービスを使用して、ハード ドライブを発送する
* オンプレミスの AlwaysOn 可用性グループのデプロイがある場合は、 [Azure のレプリカ追加ウィザード](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) を使用して Azure でレプリカを作成した後、フェールオーバーして、ユーザーに Azure データベース インスタンスを参照させる
* SQL Server の [トランザクション レプリケーション](https://msdn.microsoft.com/library/ms151176.aspx) を使用して Azure SQL Server インスタンスをサブスクライバーとして構成した後、レプリケーションを無効にして、ユーザーに Azure データベース インスタンスを参照させる

> [!TIP]
> これらの同じ手法を使用して、Azure の SQL Server VM 間でデータベースを移動することもできます。 たとえば、ギャラリー イメージの SQL Server VM を別のバージョン/エディションにアップグレードする方法はサポートされていません。 この場合、新しいバージョン/エディションの新しい SQL Server VM を作成し、この記事で説明する移行手法のいずれかを使用してデータベースを移動する必要があります。 

## <a name="choosing-your-migration-method"></a>移行方法の選択
最適なデータ転送パフォーマンスを実現するには、圧縮されたバックアップ ファイルを使用して、データベース ファイルを Azure VM に移行します。

データベース移行プロセス中のダウンタイムを最小限に抑えるには、AlwaysOn の方法またはトランザクション レプリケーションの方法を使います。

上記の方法を使用できない場合は、手動でデータベースを移行します。 この方法では、一般に、まずデータベースをバックアップし、データベースのバックアップを Azure にコピーした後に、データベースの復元を実行します。 データベース ファイル自体を Azure にコピーしてからアタッチしてもかまいません。 この手動による Azure VM へのデータベースの移行プロセスは、複数の方法で実行できます。

> [!NOTE]
> 以前のバージョンの SQL Server から SQL Server 2014 または SQL Server 2016 にアップグレードする場合は、変更が必要かどうかを検討する必要があります。 移行プロジェクトの一環として、新しいバージョンの SQL Server でサポートされない機能への依存関係すべてを解決することをお勧めします。 サポートされるエディションとシナリオの詳細については、 [SQL Server へのアップグレード](https://msdn.microsoft.com/library/bb677622.aspx)に関するページを参照してください。

次の表に、主な移行方法の一覧を示します。ここでは、それぞれの方法を使用するタイミングとして最適な状況についても説明します。

| Method | 移行元データベースのバージョン | 移行先データベースのバージョン | 移行元データベースのバックアップ サイズ制限 | メモ |
| --- | --- | --- | --- | --- |
| [圧縮機能を使用してオンプレミスのバックアップの実行し、そのバックアップ ファイルを Azure 仮想マシンに手動でコピーする](#backup-and-restore) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | これは、マシン間でデータベースを移動する際の、十分にテストされた非常にシンプルな手法です。 |
| [URL へのバックアップを実行し、その URL から Azure 仮想マシンに復元する](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 以上 |SQL Server 2012 SP1 CU2 以上 |SQL Server 2016 の場合は 12.8 TBまで、それ以外の場合は 1 TB まで | これは、Azure Storage を使用してバックアップ ファイルを VM に移動するもう 1 つの方法です。 |
| [データとログ ファイルをデタッチしてから、Azure BLOB ストレージにコピーし、その後、URL から Azure 仮想マシンの SQL Server にアタッチする](#detach-and-attach-from-url) |SQL Server 2005 以降 |SQL Server 2014 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |この方法は、特にデータベースのサイズが非常に大きい場合に、 [Azure BLOB ストレージ サービスを使用してこれらのファイルを格納](https://msdn.microsoft.com/library/dn385720.aspx) し、Azure VM で実行されている SQL Server にファイルをアタッチするときに使用します。 |
| [オンプレミスのマシンを HYPER-V VHD に変換して Azure BLOB ストレージにアップロードし、アップロードしたその VHD を使用して、新しい仮想マシンをデプロイする](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |[ご自身の SQL Server ライセンスを使用する](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)場合、古いバージョンの SQL Server で実行するデータベースを移行する場合、または他のユーザー データベースやシステム データベースに応じて、データベースの移行の一環として、データベース システムとユーザー データベースを一緒に移行する場合に使用します。 |
| [Windows の Import/Export サービスを使用して、ハード ドライブを発送する](#ship-hard-drive) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |[Windows の Import/Export サービス](../../../storage/common/storage-import-export-service.md) は、データベースのサイズが非常に大きい場合など、手動でのコピーが遅すぎるときに使用します。 |
| [Azure レプリカの追加ウィザードを使用する](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 以降 |SQL Server 2012 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |ダウンタイムが最小限になります。AlwaysOn のオンプレミスのデプロイがあるときに使います。 |
| [SQL Server のトランザクション レプリケーションを使用する](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |ダウンタイムを最小限にする必要があり、AlwaysOn のオンプレミスのデプロイがないときに使います。 |

## <a name="backup-and-restore"></a>バックアップと復元
圧縮機能を使用してデータベースをバックアップし、バックアップを VM にコピーした後、データベースを復元します。 VM ディスクの最大サイズは 1 TB であるため、バックアップ ファイルが 1 TB を超える場合、そのファイルはストライプする必要があります。 この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1. オンプレミスの場所へのデータベースの完全バックアップを実行します。
2. 必要な SQL Server バージョンを使用して、仮想マシンを作成またはアップロードします。
3. 要件に基づいて接続をセットアップします。 「 [Connect to a SQL Server Virtual Machine on Azure (Resource Manager) (Azure での SQL Server 仮想マシンへの接続 (Resource Manager))](virtual-machines-windows-sql-connect.md)」をご覧ください。
4. リモート デスクトップ、Windows エクスプローラー、またはコマンド プロンプトのコピー コマンドを使用して、バックアップ ファイルを VM にコピーします。

## <a name="backup-to-url-and-restore"></a>URL へのバックアップと復元
ローカル ファイルにバックアップするのではなく、[Backup To URL](https://msdn.microsoft.com/library/dn435916.aspx) を使用し、URL から VM に復元できます。 SQL Server 2016 では、ストライプ バックアップ セットがサポートされています。このバックアップ セットは、パフォーマンスを確保するために使用することが推奨されます。また、BLOB ごとのサイズ制限を超える場合は必須です。 データベースのサイズが非常に大きい場合は、[Windows の Import/Export サービス](../../../storage/common/storage-import-export-service.md)をお勧めします。

## <a name="detach-and-attach-from-url"></a>デタッチして URL からアタッチ
データベースとログ ファイルをデタッチし、[Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx) に転送します。 その後、Azure VM で URL からデータベースをアタッチします。 この方法は、物理データベース ファイルを Blob Storage に格納する場合に使用します。 これは、非常に大規模なデータベースで役立ちます。 この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1. オンプレミスのデータベース インスタンスからデータベース ファイルをデタッチします。
2. デタッチされたデータベース ファイルを、 [AZCopy コマンド ライン ユーティリティ](../../../storage/common/storage-use-azcopy.md)を使用して Azure BLOB ストレージにコピーします。
3. データベース ファイルを、Azure URL から Azure VM の SQL Server のインスタンスにアタッチします。

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>VM に変換して URL にアップロードし、新しい VM としてデプロイ
この方法は、オンプレミスの SQL Server インスタンスのすべてのシステムとユーザー データベースを、Azure 仮想マシンに移行するときに使用します。 この手動による方法を使用して SQL Server インスタンス全体を移行するには、次の一般的な手順を実行します。

1. 物理マシンまたは仮想マシンを Hyper-V VHD に変換します。
2. [Add-AzureVHD コマンドレット](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)を使用して、VHD ファイルを Azure Storage にアップロードします。
3. アップロードした VHD を使用して、新しい仮想マシンをデプロイします。

> [!NOTE]
> アプリケーション全体を移行するには、 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)を使用することを検討します。

## <a name="ship-hard-drive"></a>ハード ドライブの発送
ネットワーク経由のアップロードが実現不可能であるか、非常にコストがかかる場合には、[Windows の Import/Export サービス方法](../../../storage/common/storage-import-export-service.md)を使用して、ファイルの大量のデータを Azure BLOB ストレージに転送します。 このサービスでは、そのデータを含む 1 台以上のハード ドライブを Azure データ センターに発送します。このデータ センターでデータがストレージ アカウントにアップロードされます。

## <a name="next-steps"></a>次の手順
Azure Virtual Machines で SQL Server を実行する方法の詳細については、「 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

> [!TIP]
> SQL Server の仮想マシンに関するご質問については、[よくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)に関するページをご覧ください。

Azure SQL Server 仮想マシンをキャプチャ イメージから作成する手順については、CSS SQL Server Engineers のブログ「[Tips & Tricks on ‘cloning’ Azure SQL virtual machines from captured images](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/)」 (Azure SQL 仮想マシンをキャプチャ イメージから複製するためのヒント) を参照してください。

