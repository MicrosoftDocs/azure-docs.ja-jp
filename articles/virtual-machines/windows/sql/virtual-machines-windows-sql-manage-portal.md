---
title: Azure portal を使用した Azure 内の SQL Server VM の管理 | Microsoft Docs
description: Azure でホストされている SQL Server VM 用の Azure portal 内の SQL 仮想マシン リソースにアクセスする方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59a85e855c9fab9f2a3437c83c867b8076f55049
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607221"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Azure portal を使用した Azure 内の SQL Server VM の管理

[Azure portal](https://portal.azure.com) を使用して Azure 上の SQL Server VM を管理するための新しいアクセス ポイントがあります。 

次の図に示すように、**SQL 仮想マシン** リソースが独立した管理サービスになり、すべての SQL Server VM を同時に表示したり、SQL Server 専用の設定を変更したりできるようになりました。 

![SQL 仮想マシン リソース](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>解説

- **SQL 仮想マシン** リソースは、SQL Server VM を表示および管理するための推奨される方法です。 ただし、現時点では、 **SQL 仮想マシン** リソースは、[サポート終了 (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) の SQL Server VM の管理をサポートしていません。 EOS の SQL Server VM 用の設定を管理するには、非推奨の [SQL Server 構成タブ](#access-sql-server-configuration-tab)を代わりに使用します。 
- **SQL 仮想マシン** リソースは、[SQL VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)されている SQL Server VM でのみ使用できます。 


## <a name="access-sql-virtual-machine-resource"></a>SQL 仮想マシン リソースへのアクセス
SQL 仮想マシン リソースにアクセスするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com)を開きます。 
1. **[すべてのサービス]** を選択します。 
1. 検索ボックスに、「`SQL virtual machines`」と入力します。
1. (省略可能): **[SQL virtual machines]\(SQL 仮想マシン\)** の横にある星を選択して、このオプションを [お気に入り] メニューに追加します。 
1. **[SQL virtual machines]\(SQL 仮想マシン\)** を選択します。 

   ![[すべてのサービス] で SQL VM 仮想マシンを見つける](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. これにより、サブスクリプション内で使用可能なすべての SQL Server VM が一覧表示されます。 管理する SQL Server VM を選択して、**SQL 仮想マシン** リソースを起動します。 SQL Server VM がすぐに見つからない場合は、検索ボックスを使用します。 

![使用可能なすべての SQL VM](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

対象の SQL Server VM を選択すると、**SQL 仮想マシン** リソースが開きます。 


![SQL 仮想マシン リソース](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > **SQL 仮想マシン** リソースは専用の SQL Server 設定用のリソースです。 **[仮想マシン]** フィールドで VM の名前を選択し、その VM (ただし、SQL Server に限定されません) に固有の設定に移動します。 

## <a name="access-sql-server-configuration-tab"></a>SQL Server 構成タブへのアクセス
SQL Server 構成タブは非推奨になりました。 現時点では、これが [サポート終了 (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) の SQL Server VM、および [SQL VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)されていない SQL Server VM を管理する唯一の方法です。

非推奨の SQL server 構成タブにアクセスするには、**仮想マシン** リソースに移動する必要があります。 そのためには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com)を開きます。 
1. **[すべてのサービス]** を選択します。 
1. 検索ボックスに、「`virtual machines`」と入力します。
1. (省略可能): **[仮想マシン]** の横にある星を選択して、このオプションを [お気に入り] メニューに追加します。 
1. **[Virtual Machines]** を選択します。 

   ![仮想マシンを検索](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. これにより、サブスクリプション内のすべての仮想マシンが一覧表示されます。 管理する仮想マシンを選択して、**仮想マシン** リソースを起動します。 SQL Server VM がすぐに見つからない場合は、検索ボックスを使用します。 
1. **[設定]** ウィンドウで、 **[SQL Server の構成]** を選択して、SQL Server を管理します。 

![SQL Server 構成](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


