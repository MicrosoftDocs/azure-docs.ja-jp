---
title: Azure Government Backup | Microsoft Docs
description: "この記事では、Azure Government で使用できる Azure Backup の機能の概要を示します。"
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154
ms.lasthandoff: 01/06/2017


---
# <a name="azure-government-backup"></a>Azure Government Backup

この記事では、Azure Backup サービスの概要と、Azure Government で利用可能なバックアップ機能の一覧を示します。 Azure Backup は、Microsoft Cloud へのデータのバックアップ (または保護) に使用できる、Azure ベースのサービスです。 Azure でのデータ保護では、クラウドへのバックアップだけでなく、クラウドまたはオンプレミス インストールにデータを復元することもできます。 Azure Backup には、次のような主なメリットがあります。

- ストレージ管理の自動化
- 無制限のスケーリング
- 複数のストレージ オプション
- 無制限のデータ転送
- データの暗号化
- アプリケーションの整合性のバックアップ
- 長期保存

Azure Backup を利用するのが初めてであり、使用可能な機能の概要を確認したい方は、「[Azure Backup とは](../backup/backup-introduction-to-azure-backup.md)」をご覧ください。

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Azure Government Backup で使用できる Azure Backup コンポーネント

Azure Backup を使用して保護できるものとしては、ファイル、フォルダー、ボリューム、仮想マシン、アプリケーション、ワークロードがあります。 保護の対象とデータの存在場所に応じて、異なる Azure Backup コンポーネントを使用します。 以下の各セクションに、コンポーネントごとの Azure Backup パブリック ドキュメントの記事へのリンクを示します。 セクションは、クラシック ポータル向けと Azure Portal 向けに分かれています。 Resource Manager デプロイを使用する場合は、Azure Portal バージョンを使用してください。

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>Azure Portal での Windows Server と Windows コンピューターの使用

- [Windows Server および Windows クライアント コンピューターのバックアップ](../backup/backup-configure-vault.md)
- [Windows Server および Windows クライアント コンピューターの復元](../backup/backup-azure-restore-windows-server.md)
- [Windows Server および Windows クライアント コンピューターのバックアップの管理](../backup/backup-azure-manage-windows-server.md)
- [PowerShell を使用した Windows Server のバックアップ](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>クラシック ポータルでの Windows Server と Windows コンピューターの使用

- [Windows Server および Windows クライアント コンピューターのバックアップ](../backup/backup-configure-vault-classic.md)
- [Windows Server および Windows クライアント コンピューターの復元](../backup/backup-azure-restore-windows-server-classic.md)
- [Windows Server および Windows クライアント コンピューターのバックアップの管理](../backup/backup-azure-manage-windows-server-classic.md)
- [PowerShell を使用した Windows Server のバックアップ](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>Azure Portal での Virtual Machines の使用

- [仮想マシン環境の準備](../backup/backup-azure-arm-vms-prepare.md)
- [仮想マシンのバックアップ](../backup/backup-azure-vms-first-look-arm.md)
- [仮想マシンの復元](../backup/backup-azure-arm-restore-vms.md)
- [仮想マシンの管理](../backup/backup-azure-manage-vms.md)
- [PowerShell を使用した仮想マシンのバックアップ](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>クラシック ポータルでの Virtual Machines の使用

- [仮想マシン環境の準備](../backup/backup-azure-vms-prepare.md)
- [仮想マシンのバックアップ](../backup/backup-azure-vms-first-look.md)
- [仮想マシンの復元](../backup/backup-azure-restore-vms.md)
- [仮想マシンの管理](../backup/backup-azure-manage-vms-classic.md)
- [PowerShell を使用した仮想マシンのバックアップ](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>Azure Portal での System Center Data Protection Manager の使用

- [System Center Data Protection Manager のバックアップ](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>クラシック ポータルでの System Center Data Protection Manager の使用

- [System Center Data Protection Manager のバックアップ](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>Azure Portal での Azure Backup Server の使用

Azure Backup Server は、System Center Data Protection Manager (DPM) と同様の働きをする Azure Backup コンポーネントです。ただし、Azure Backup Server ではデータをテープに保存できないという例外があります。 Azure Backup Server では、単一のコンソールから Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange、Windows クライアントなどのアプリケーションのワークロードをクラウドに保護することができます。 Azure Backup Server では、System Center ライセンスは必要ありません。

- [Azure Backup Server](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>クラシック ポータルでの Azure Backup Server の使用

- [Azure Backup Server](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>次のステップ

どこから手を付ければよいかわからない場合は、こちらの「[クラシック デプロイメント モデルを使用して Windows サーバーまたはクライアントを Azure にバックアップする](../backup/backup-configure-vault-classic.md)」からご覧ください。 このチュートリアルでは、Windows Server またはコンピューターでのバックアップ プロジェクトのを設定手順について説明しています。

Azure Backup の使い方は知っているものの料金が気になる場合は、[Backup の価格ページ](http://azure.microsoft.com/pricing/details/backup/)をご覧ください。 このページにはよく寄せられる質問のリストも記載されており、役立つ情報を得ることができます。 また、**[リージョン]** ドロップダウン メニューに Azure Government リージョンは&2; つ含まれています。

