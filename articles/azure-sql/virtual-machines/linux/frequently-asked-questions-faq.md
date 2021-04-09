---
title: SQL Server on Linux 仮想マシンに関する FAQ | Microsoft Docs
description: この記事では、SQL Server on Linux 仮想マシンの実行ついてよく寄せられる質問とその回答を示します。
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792550"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>SQL Server on Linux 仮想マシンについてよく寄せられる質問
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

この記事では、[SQL Server on Linux 仮想マシン](sql-server-on-linux-vm-what-is-iaas-overview.md)について特に多く寄せられる質問とその回答を示します。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>イメージ

1. **どのような SQL Server 仮想マシン ギャラリーのイメージを使用できますか?**

   Azure では、Linux と Windows の両方のすべてのエディションで、SQL Server のサポートされているすべてのメジャー リリース用の仮想マシン (VM) イメージを保持します。 詳細については、[Linux VM イメージ](sql-server-on-linux-vm-what-is-iaas-overview.md#create)と [Windows VM イメージ](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)の完全な一覧を参照してください。

1. **既存の SQL Server 仮想マシン ギャラリー イメージは更新されますか?**

   仮想マシン ギャラリー内の SQL Server イメージは、2 か月ごとに最新の Linux と Windows の更新プログラムによって更新されます。 Linux イメージの場合は、システムの最新の更新プログラムが含まれます。 Windows イメージの場合は、重要な SQL Server のセキュリティ更新プログラムや Service Pack など、Windows Update で重要とマークされている更新プログラムが含まれます。 SQL Server の累積更新プログラムは、Linux と Windows では異なる方法で処理されます。 Linux では、SQL Server の累積更新プログラムも更新に含まれます。 ただし、現時点では、Windows VM は、SQL Server または Windows Server の累積更新プログラムでは更新されません。

1. **どのような関連 SQL Server パッケージがインストールされますか?**

   SQL Server on Linux VM に既定でインストールされる SQL Server パッケージを確認するには、「[インストール済みパッケージ](sql-server-on-linux-vm-what-is-iaas-overview.md#packages)」を参照してください。

1. **SQL Server 仮想マシン イメージはギャラリーから削除される可能性がありますか?**

   はい。 Azure では、メジャー バージョンとエディションごとに 1 つのイメージのみを保持します。 たとえば、新しい SQL Server Service Pack がリリースされると、Azure はその Service Pack 用の新しいイメージをギャラリーに追加します。 前の Service Pack 用の SQL Server イメージは、Azure ポータルからただちに削除されます。 ただし、PowerShell からプロビジョニングする場合は、次の 3 か月間は引き続き使用できます。 3 か月が経過すると、前の Service Pack イメージは使用できなくします。 この削除ポリシーは、SQL Server のバージョンがそのライフサイクルの終わりに達した時点でサポートされなくなった場合にも適用されます。

## <a name="creation"></a>作成

1. **SQL Server で Linux 仮想マシンを作成するにはどうすればよいですか?**

   最も簡単な解決方法は、SQL Server を含む Linux 仮想マシンを作成することです。 Azure にサインアップして、ポータルから SQL Server VM を作成するチュートリアルについては、[Azure portal で SQL Server を実行する Linux 仮想マシンをプロビジョニングする方法](sql-vm-create-portal-quickstart.md)に関するページを参照してください。 無料ライセンス版 (Developer または Express) を利用するかオンプレミスのライセンスを再利用して、SQL Server を VM に手動でインストールすることもできます。 現在所有しているライセンスを使用する場合は、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility)が必要です。

1. **RHEL または SLES SQL Server VM を使用制限のある Azure サブスクリプションでプロビジョニングできないのはなぜですか?**

   RHEL および SLES 仮想マシンでは、使用制限のないサブスクリプションと、サブスクリプションに関連付けられている検証済みの支払い方法 (通常はクレジット カード) が必要です。 使用制限を削除せずに RHEL または SLES VM をプロビジョニングした場合、サブスクリプションは無効になり、すべての VM/サービスが停止します。 この状態になった場合、サブスクリプションをもう一度有効にするには、[使用制限を削除](https://account.windowsazure.com/subscriptions)します。 現在の請求サイクルの残りのクレジットは復元されますが、再起動して実行を続けることを選択した場合、クレジット カードに対して RHEL または SLES VM イメージの追加料金が請求されます。

## <a name="licensing"></a>ライセンス

1. **Azure VM に SQL Server のライセンス版をインストールするにはどうすればよいですか?**

   まず、Linux OS 専用の仮想マシンを作成します。 次に、Linux ディストリビューション用の [SQL Server のインストール手順](/sql/linux/sql-server-linux-setup#platforms)を実行します。 SQL Server の無料ライセンス版のいずれかをインストールしていない限り、SQL Server ライセンスと [Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)も必要です。

1. **SQL Server のライセンス持ち込み (BYOL) Linux 仮想マシンのイメージはありますか?**

   現時点では、SQL Server 用 BYOL Linux 仮想マシンのイメージはありません。 ただし、前の質問で説明したように、SQL Server を Linux 専用の VM に手動でインストールできます。

1. **従量課金制のギャラリー イメージから作成した VM を、現在所有している SQL Server ライセンスを使用するように変更できますか。**

   いいえ。 秒単位のライセンスから独自のライセンスの使用に切り替えることはできません。 新しい Linux VM を作成し、SQL Server をインストールし、データを移行する必要があります。 ライセンス持ち込みについて詳しくは、前の質問をご覧ください。

## <a name="administration"></a>管理

1. **SQL Server を実行している Linux 仮想マシンを SQL Server Management Studio (SSMS) で管理できますか?**

   はい。ただし、SSMS は現時点では Windows 限定のツールです。 SQL Server を実行している Linux VM で SSMS を使用するには、Windows マシンからリモートで接続する必要があります。 Linux では、新しい[mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf)ツールでさまざまな管理タスクをローカルに実行できます。 クロスプラットフォームのデータベース管理ツールについては、[Azure Data Studio](/sql/azure-data-studio/what-is) を参照してください。

1. **SQL Server VM から SQL Server を完全に削除できますか?**

   はい。ただし、[SQL Server Azure VM の価格に関するガイダンス](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json)のページで説明されているように、SQL Server VM に対して引き続き課金されます。 SQL Server が不要になった場合は、新しい仮想マシンをデプロイし、データとアプリケーションを新しい仮想マシンに移行してから、 SQL Server 仮想マシンを削除してください。

## <a name="updating-and-patching"></a>更新プログラムと修正プログラムの適用

1. **Azure VM で SQL Server の新しいバージョン/エディションにアップグレードするにはどうすればよいですか?**

   現在、Azure VM で実行している SQL Server のインプレース アップグレードはありません。 目的の SQL Server バージョン/エディションで新しい Azure 仮想マシンを作成し、[標準のデータ移行方法](/sql/linux/sql-server-linux-migrate-overview)を使用して、データベースを新しいサーバーに移行します。

## <a name="general"></a>全般

1. **Azure VM で SQL Server 高可用性ソリューションはサポートされますか?**

   現時点ではありません。 Always On 可用性グループとフェールオーバー クラスタリングの両方で、Pacemaker などの Linux のクラスタリング ソリューションが必要です。 SQL Server でサポートされている Linux ディストリビューションは、クラウドの高可用性アドオンをサポートしていません。

## <a name="resources"></a>リソース

**Linux VM**:

* [Linux VM における SQL Server の概要](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux VM での SQL Server のプロビジョニング](sql-vm-create-portal-quickstart.md)
* [SQL Server on Linux のドキュメント](/sql/linux/sql-server-linux-overview)

**Windows VM**:

* [Windows VM における SQL Server の概要](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM での SQL Server のプロビジョニング](../windows/sql-vm-create-portal-quickstart.md)
* [FAQ (Windows)](../windows/frequently-asked-questions-faq.md)