---
title: "SQL Server のバックアップと復元 | Microsoft Docs"
description: "Azure Virtual Machines で実行されている SQL Server データベースのバックアップと復元に関する考慮事項について説明します。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 7402249aa87ffe985ae13f28a701e22af3afd450
ms.openlocfilehash: 0d2310748c65a25ead71f7c0859919b7241d9f6c


---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines おける SQL Server のバックアップと復元
## <a name="overview"></a>概要
SQL Server データベースのデータのバックアップは、アプリケーション エラーやユーザー エラーによるデータ損失から保護する戦略の重要な要素です。 これは、Azure Virtual Machines (VM) で実行されている SQL Server にも同様に当てはまります。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM で実行されている SQL Server では、バックアップ ファイルの保存先として接続されたディスクを使用するネイティブなバックアップおよび復元手法を使用できます。 ただし、[仮想マシンのサイズ](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に基づいて、Azure 仮想マシンに接続できるディスク数には制限があります。 また、ディスク管理のオーバーヘッドも考慮する必要があります。

SQL Server 2014 以降では、Microsoft Azure BLOB ストレージへのバックアップと復元が可能です。 SQL Server 2016 には、このオプションの拡張機能も用意されています。 また、データベース ファイルが Microsoft Azure BLOB ストレージに保存されている場合、SQL Server 2016 では、Azure のスナップショットを使用して、ほぼ瞬時にバックアップを作成し、迅速に復元できます。 この記事では、これらのオプションの概要を説明します。追加情報については、「[Windows Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](https://msdn.microsoft.com/library/jj919148.aspx)」をご覧ください。

> [!NOTE]
> 大規模なデータベースのバックアップのオプションについては、「 [Multi-Terabyte SQL Server Database Backup Strategies for Azure Virtual Machines (Azure Virtual Machines の数 TB の SQL Server データベースのバックアップ戦略)](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx)」をご覧ください。
> 
> 

以降のセクションには、Azure 仮想マシンでサポートされているさまざまなバージョンの SQL Server に固有の情報が記載されています。

## <a name="sql-server-virtual-machines"></a>SQL Server 仮想マシン
SQL Server インスタンスが Azure 仮想マシンで実行されている場合、Azure のデータ ディスク上にデータベース ファイルが既にあります。 これらのディスクは Azure BLOB ストレージに配置されています。 そのため、データベースをバックアップする理由と使用するアプローチが少し変わります。 次の例を検討してみてください。 

* Microsoft Azure では、Microsoft Azure サービスの一部として、ハードウェアやメディアの障害からの保護が提供されるため、この保護のためにデータベースのバックアップを実行する必要はなくなりました。
* ユーザー エラーからの保護、保存目的、規制上の理由、または管理目的で、データベースのバックアップを引き続き実行する必要があります。
* バックアップ ファイルを直接 Azure に保存できます。 詳細については、各種バージョンの SQL Server に関するガイダンスを示す以降のセクションをご覧ください。

## <a name="sql-server-2016"></a>SQL Server 2016
Microsoft SQL Server 2016 では、SQL Server 2014 の [Azure BLOB を使用したバックアップと復元](https://msdn.microsoft.com/library/jj919148.aspx) 機能をサポートしています。 ただし、次の拡張機能も用意されています。

| 2016 の拡張機能 | 詳細 |
| --- | --- |
| **ストライピング** |Microsoft Azure Blob ストレージにバックアップする場合、SQL Server 2016 では、複数の BLOB へのバックアップをサポートしているので、最大 12.8 TB の大規模なデータベースをバックアップできます。 |
| **スナップショット バックアップ** |Azure のスナップショットを使用することで、SQL Server ファイル スナップショット バックアップでは、Azure Blob ストレージ サービスを使用して保存されたデータベース ファイルをほぼ瞬時にバックアップし、迅速に復元できます。 この機能により、バックアップと復元のポリシーを簡素化することができます。 ファイル スナップショット バックアップでは、ポイントインタイム リストアもサポートしています。 詳細については、「 [Snapshot Backups for Database Files in Azure (Azure でのデータベース ファイルのスナップショット バックアップ)](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx)」をご覧ください。 |
| **マネージ バックアップのスケジュール設定** |Azure への SQL Server マネージ バックアップでカスタム スケジュールがサポートされるようになりました。 詳細については、「 [SQL Server Managed Backup to Microsoft Azure (Microsoft Azure への SQL Server マネージ バックアップ)](https://msdn.microsoft.com/library/dn449496.aspx)」をご覧ください。 |

Azure BLOB ストレージを使用する場合の、SQL Server 2016 の機能のチュートリアルについては、「 [Tutorial: Using the Microsoft Azure Blob storage service with SQL Server 2016 databases (チュートリアル: SQL Server 2016 データベースでの Microsoft Azure BLOB ストレージ サービスの使用)](https://msdn.microsoft.com/library/dn466438.aspx)」をご覧ください。

## <a name="sql-server-2014"></a>SQL Server 2014
SQL Server 2014 には、次の拡張機能が用意されています。

1. **Azure へのバックアップと復元**:
   
   * *SQL Server Backup to URL* がサポートされるようになりました。 SQL Server Management Studio で Backup タスクまたは復元タスクを使用するときや、メンテナンス プラン ウィザードを使用するときに、Azure にバックアップするオプションを利用できるようになりました。 詳細については、「 [SQL Server Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)」をご覧ください。
   * *Azure への SQL Server マネージ バックアップ* は、バックアップ管理の自動化を可能にする新機能です。 この機能は、Azure マシンで実行されている SQL Server 2014 インスタンスのバックアップ管理を自動化する際に特に役立ちます。 詳細については、「 [SQL Server Managed Backup to Microsoft Azure (Microsoft Azure への SQL Server マネージ バックアップ)](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)」をご覧ください。
   * *自動バックアップ*は、Azure の SQL Server VM の既存のデータベースと新しいデータベースのすべてを対象とした *Azure への SQL Server マネージ バックアップ*を自動的に有効にする別の自動化機能です。 詳細については、「 [Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-windows-sql-automated-backup.md)」をご覧ください。
   * Azure に SQL Server 2014 をバックアップする際のすべてのオプションの概要については、「 [SQL Server Backup and Restore with Microsoft Azure Blob Storage Service (Microsoft Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元)](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)」をご覧ください。
2. **暗号化**: SQL Server 2014 では、バックアップの作成時におけるデータの暗号化をサポートしています。 複数の暗号化アルゴリズムと、証明書または非対称キーの使用をサポートしています。 詳細については、「 [バックアップの暗号化](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx)」をご覧ください。

## <a name="sql-server-2012"></a>SQL Server 2012
SQL Server 2012 での SQL Server のバックアップと復元の詳細については、「 [SQL Server データベースのバックアップと復元](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)」(SQL Server 2012) をご覧ください。

SQL Server 2012 SP1 Cumulative Update 2 以降では、Azure BLOB ストレージ サービスとの間でバックアップと復元を実行できます。 この拡張機能を使用して、Azure 仮想マシンで実行されている SQL Server 上またはオンプレミスのインスタンス上の SQL Server データベースをバックアップできます。 詳細については、「 [Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)」をご覧ください。

Azure BLOB ストレージ サービスを使用する利点として、接続されるディスク数の上限 (16 個) を回避できること、管理しやすいこと、移行または障害復旧のために、Azure 仮想マシンで実行されている SQL Server インスタンスの別のインスタンスまたはオンプレミスのインスタンスでバックアップ ファイルを直接利用できることなどがあります。 SQL Server バックアップに Azure BLOB ストレージ サービスを使用する利点の詳細については、「 *Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元* 」の [利点](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)に関するセクションをご覧ください。

ベスト プラクティスの推奨事項とトラブルシューティング情報については、「 [バックアップと復元に関するベスト プラクティス (Azure BLOB ストレージ サービス)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx)」をご覧ください。

## <a name="sql-server-2008"></a>SQL Server 2008
SQL Server 2008 R2 での SQL Server のバックアップと復元については、「 [SQL Server でのデータベースのバックアップおよび復元](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)」(SQL Server 2008 R2) をご覧ください。

SQL Server 2008 での SQL Server のバックアップと復元については、「 [SQL Server でのデータベースのバックアップおよび復元](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)」(SQL Server 2008) をご覧ください。

## <a name="next-steps"></a>次のステップ
Azure VM に SQL Server をデプロイすることを計画している場合、プロビジョニングのガイダンスについては、[Azure Resource Manager を使用した Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)に関するチュートリアルをご覧ください。

バックアップと復元を使用してデータを移行できますが、Azure VM の SQL Server へのより簡単なデータ移行パスが存在する可能性があります。 移行オプションと推奨事項の詳細については、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)」をご覧ください。

[Azure Virtual Machines で SQL Server を実行するための他のリソース](virtual-machines-windows-sql-server-iaas-overview.md)を確認します。




<!--HONumber=Jan17_HO2-->


