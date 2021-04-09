---
title: Azure portal を使用して Azure 内の SQL Server 仮想マシンを管理する | Microsoft Docs
description: Azure でホストされている SQL Server VM 用の Azure portal 内の SQL 仮想マシン リソースにアクセスする方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 68c7805136a7361a64a6ff6dfbb9e7d910b2ea9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357944"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Azure portal を使用して Azure 内の SQL Server VM を管理する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[Azure portal](https://portal.azure.com) において、[**SQL 仮想マシン**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソースは、Azure VM 上の SQL Server を管理するための独立した管理サービスです。 これを使用すると、すべての SQL Server VM を同時に表示し、SQL Server 専用の設定を変更できます。 

![SQL 仮想マシン リソース](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>解説

- [**SQL 仮想マシン**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソースを使用して Azure 内の SQL Server VM を表示および管理することをお勧めします。 ただし、現在、**SQL 仮想マシン** リソースは、[サポート終了](sql-server-2008-extend-end-of-support.md)の SQL Server VM の管理をサポートしていません。 サポート終了の SQL Server VM 用の設定を管理するには、代わりに、非推奨の [[SQL Server の構成] タブ](#access-the-sql-server-configuration-tab)を使用してください。 
- **SQL 仮想マシン** リソースは、[SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)されている SQL Server VM でのみ使用できます。 


## <a name="access-the-sql-virtual-machines-resource"></a>SQL 仮想マシン リソースへのアクセス
**SQL 仮想マシン** リソースにアクセスするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com)を開きます。 
1. **[すべてのサービス]** を選択します。 
1. 検索ボックスに「**SQL 仮想マシン**」と入力します。
1. (省略可能): **[SQL 仮想マシン]** の横にある星を選択して、このオプションを **[お気に入り]** メニューに追加します。 
1. **[SQL virtual machines]\(SQL 仮想マシン\)** を選択します。 

   ![[すべてのサービス] で SQL Server 仮想マシンを見つける](./media/manage-sql-vm-portal/sql-vm-search.png)

1. このポータルに、サブスクリプション内で使用可能なすべての SQL Server VM が一覧表示されます。 管理したいものを選択して、**SQL 仮想マシン** リソースを開きます。 SQL Server VM が表示されていない場合は、検索ボックスを使用します。 

   ![使用可能なすべての SQL Server VM](./media/manage-sql-vm-portal/all-sql-vms.png)

   対象の SQL Server VM を選択すると、**SQL 仮想マシン** リソースが開きます。 


   ![SQL 仮想マシン リソースを表示する](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 仮想マシン** リソースは専用の SQL Server 設定用のリソースです。 **[仮想マシン]** ボックスで VM の名前を選択し、その VM (ただし、SQL Server に限定されません) に固有の設定を開きます。 

## <a name="access-the-sql-server-configuration-tab"></a>[SQL Server の構成] タブへのアクセス
**[SQL Server の構成]** タブは非推奨になりました。 現時点では、これが[サポート終了](sql-server-2008-extend-end-of-support.md)の SQL Server VM や [SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)されていない SQL Server VM を管理する唯一の方法です。

非推奨の **[SQL Server の構成]** タブにアクセスするには、**仮想マシン** リソースに移動します。 次の手順に従います。

1. [Azure Portal](https://portal.azure.com)を開きます。 
1. **[すべてのサービス]** を選択します。 
1. 検索ボックスに「**virtual machines**」と入力します。
1. (省略可能): **[Virtual Machines]** の横にある星を選択して、このオプションを **[お気に入り]** メニューに追加します。 
1. **[Virtual Machines]** を選択します。 

   ![仮想マシンを検索](./media/manage-sql-vm-portal/vm-search.png)

1. このポータルに、サブスクリプション内のすべての仮想マシンが一覧表示されます。 管理したいものを選択して、**Virtual Machines** リソースを開きます。 SQL Server VM が表示されていない場合は、検索ボックスを使用します。 
1. **[設定]** ウィンドウで **[SQL Server の構成]** を選択して、対象の SQL Server VM を管理します。 

   ![SQL Server の構成](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](doc-changes-updates-release-notes.md)


