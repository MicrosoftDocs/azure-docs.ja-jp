---
title: Azure Policy 向けにサポートされる VM SKU
description: バックアップによって提供される組み込みの Azure Policy 向けにサポートされている VM SKU (発行者、イメージ プラン、およびイメージ SKU 別) について説明する記事
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979940"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure Policy 向けにサポートされる VM SKU

Azure Backup には、**サブスクリプションまたはリソース グループ内の指定された場所にあるすべての Azure VM** に割り当てることができる組み込みのポリシー (Azure Policy を使用) が用意されています。 このポリシーが特定のスコープに割り当てられると、そのスコープ内に作成されたすべての新しい VM は、**同じ場所およびサブスクリプション内の既存のコンテナー**にバックアップするように自動的に構成されます。 次の表は、このポリシーでサポートされているすべての VM SKU を示しています。

### <a name="supported-vms"></a>**サポートされている VM**

**ポリシー名:** 特定の場所の VM を同じ場所にある既存の中央コンテナーにバックアップするように構成する

イメージ発行者 | イメージ プラン | イメージ SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core with Containers (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Remote Desktop Session Host 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core with Containers (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core with Containers (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter with Containers (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter with Containers (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | すべてのイメージ SKU
MicrosoftSQLServer | SQL2016SP1-WS2016 | すべてのイメージ SKU
MicrosoftSQLServer | SQL2016-WS2016  | すべてのイメージ SKU
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | すべてのイメージ SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | すべてのイメージ SKU
MicrosoftSQLServer | SQL2016-WS2012R2 | すべてのイメージ SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | すべてのイメージ SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | すべてのイメージ SKU
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | すべてのイメージ SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | すべてのイメージ SKU
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | すべてのイメージ SKU
MicrosoftRServer | MLServer-WS2016 | すべてのイメージ SKU
MicrosoftVisualStudio | VisualStudio | すべてのイメージ SKU
MicrosoftVisualStudio | Windows | すべてのイメージ SKU
MicrosoftDynamicsAX | Dynamics  | Pre-Req-AX7-Onebox-U8
microsoft-ads | windows-data-science-vm | すべてのイメージ SKU
MicrosoftWindowsDesktop | Windows-10 | すべてのイメージ SKU
RedHat | RHEL | 6.7、6.8、6.9、6.10、7.2、7.3、7.4、7.5、7.6、7.7
RedHat | RHEL-SAP-HANA | 6.7、7.2、7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-Priority | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-Priority | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-Standard | 12.X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04 LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8、6.9、6.10、7.3、7.4、7.5、7.6
OpenLogic | CentOS | 6.X、7.X
OpenLogic | CentOS–LVM | 6.X、7.X
OpenLogic | CentOS–SRIOV | 6.X、7.X
cloudera | cloudera-centos-os | 7.X
