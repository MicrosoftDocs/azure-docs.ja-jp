---
title: Azure VM で SQL Server のエディションのインプレース アップグレードを実行する | Microsoft Docs
description: Azure で SQL Server VM のエディションを変更する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: eec2e588b1c2b03e9880dad0848b8213bf5fa449
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100509"
---
# <a name="perform-an-in-place-upgrade-of-a-sql-server-edition-on-an-azure-vm"></a>Azure VM で SQL Server のエディションのインプレース アップグレードを実行する

この記事では、Azure の Windows 仮想マシン上で SQL Server のエディションを変更する方法について説明します。 

SQL Server のエディションは、プロダクト キーによって決定され、インストール プロセスで指定されます。 エディションにより、SQL Server 製品内で使用できる[機能](/sql/sql-server/editions-and-components-of-sql-server-2017)が指定されます。 インストール メディアで SQL Server のエディションを変更し、ダウングレードしてコストを削減したり、アップグレードして機能を増やしたりすることができます。

SQL VM のリソース プロバイダーに登録した後で、インストール メディアを使って SQL Server のエディションを更新した場合、それに応じて Azure の課金を更新するには、SQL VM リソースの SQL Server エディション プロパティを次のように設定する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. SQL Server の仮想マシン リソースに移動します。 
1. **[設定]** の **[構成]** を選択します。 次に、 **[エディション]** のドロップダウン リストから、必要な SQL Server のエディションを選択します。 

   ![エディションのメタデータを変更する](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. SQL Server のエディションを最初に変更する必要があること、およびエディション プロパティが SQL Server のエディションと一致する必要があることを示す警告を確認します。 
1. **[適用]** を選択して、エディション メタデータの変更を適用します。 


## <a name="prerequisites"></a>前提条件

SQL Server のエディションのインプレース変更を行うには、以下のものが必要です。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL VM リソース プロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録された [Windows 上の SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。
- SQL Server の目的のエディションが収められたセットアップ メディア。 [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、目的のエディションを含む Azure Marketplace の SQL Server VM イメージから、セットアップ メディアを使用できます。


## <a name="upgrade-an-edition"></a>エディションをアップグレードする

> [!WARNING]
> SQL Server のエディションをアップグレードすると、SQL Server のサービスと、Analysis Services や R Services などのすべての関連するサービスが、再起動されます。 

SQL Server をアップグレードするには、SQL Server の目的のエディションの SQL Server セットアップ メディアを入手した後、次のようにします。

1. SQL Server のインストール メディアから Setup.exe を開きます。 
1. **[メンテナンス]** に移動し、 **[エディションのアップグレード]** オプションを選択します。 

   ![SQL Server のエディションをアップグレードするための選択](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. **[次へ]** を何回か選択して **[エディションのアップグレードの準備完了]** ページまで進み、 **[アップグレード]** を選択します。 変更が有効になるまで数分間、セットアップ ウィンドウの応答が停止することがあります。 **[完了]** ページで、エディションのアップグレードが完了したことが確認されます。 

SQL Server のエディションをアップグレードした後、前に示したように、Azure portal で SQL Server 仮想マシンのエディション プロパティを変更します。 これにより、この VM に関連付けられているメタデータと課金が更新されます。

## <a name="downgrade-an-edition"></a>エディションをダウングレードする

SQL Server のエディションをダウングレードするには、SQL Server を完全にアンインストールし、目的のエディションのセットアップ メディアでもう一度再インストールする必要があります。

> [!WARNING]
> SQL Server をアンインストールすると、追加のダウンタイムが発生する可能性があります。 

次の手順に従って、SQL Server のエディションをダウングレードできます。

1. システム データベースを含むすべてのデータベースをバックアップします。 
1. システム データベース (master、model、msdb) を新しい場所に移動します。 
1. SQL Server および関連付けられているすべてのサービスを、完全にアンインストールします。 
1. 仮想マシンを再起動します。 
1. SQL Server の目的のエディションが収められたメディアを使って、SQL Server をインストールします。
1. 最新のサービス パックと累積的な更新プログラムをインストールします。  
1. インストールの間に作成された新しいシステム データベースを、前に別の場所に移動したシステム データベースに置き換えます。 

SQL Server のエディションをダウングレードした後、前に示したように、Azure portal で SQL Server 仮想マシンのエディション プロパティを変更します。 これにより、この VM に関連付けられているメタデータと課金が更新されます。

## <a name="remarks"></a>解説

- SQL Server VM のエディション プロパティは、すべての SQL Server 仮想マシン用にインストールされている SQL Server インスタンスのエディションと一致する必要があります。これには、従量課金制とライセンス持ち込みの両方のライセンスの種類が含まれます。
- SQL Server VM リソースを削除した場合は、イメージのハード コーディングされたエディション設定に戻ります。
- エディションを変更する機能は、SQL VM リソース プロバイダーの機能です。 Azure portal を介して Azure Marketplace イメージをデプロイすると、SQL Server VM がリソース プロバイダーに自動的に登録されます。 ただし、SQL Server を自分でインストールするお客様は、手動で [SQL Server VM を登録](virtual-machines-windows-sql-register-with-resource-provider.md)する必要があります。
- SQL Server VM を可用性セットに追加するには、VM を再作成する必要があります。 可用性セットに追加されたすべての VM は既定のエディションに戻るので、エディションをもう一度変更する必要があります。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


