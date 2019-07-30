---
title: SAP NetWeaver のための Azure Virtual Machines の計画と実装 | Microsoft Docs
description: SAP NetWeaver のための Azure Virtual Machines の計画と実装
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11a54dee653bcfa6c94a861e483183ac39f465bf
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710183"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>SAP NetWeaver のための Azure Virtual Machines の計画と実装

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]: https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure を使用すると、企業は時間のかかる調達サイクルを省略し、最短時間でコンピューティングとストレージのリソースを取得することができます。 Azure Virtual Machines Services により、企業は SAP NetWeaver ベースのアプリケーションのような従来のアプリケーションを Azure にデプロイし、追加のオンプレミスのリソースを必要とせずに信頼性と可用性を高めることができます。 また、Azure Virtual Machine Services ではクロスプレミス接続もサポートしており、これにより企業は Azure Virtual Machines を自社のオンプレミス ドメイン、プライベート クラウド、および SAP システム ランドス ケープに積極的に統合できます。
このホワイト ペーパーでは、Microsoft Azure Virtual Machine の基礎について説明したうえで、Azure 内での SAP NetWeaver インストールの計画と実装に関する考慮事項について説明します。Azure で SAP NetWeaver のデプロイを実際に開始する前に、このドキュメントを読んでください。
特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Note を補足する内容となっています。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>まとめ
クラウド コンピューティングという言葉は、今や多くの人々に認知され、小規模な企業から大企業、多国籍企業にいたるまで、IT 業界における存在感を日増しに高めています。

Microsoft Azure はマイクロソフトが提供する Cloud Services プラットフォームで、多方面にわたって新たな可能性を生み出しています。 アプリケーションをクラウド上のサービスとして迅速にプロビジョニングしたり、プロビジョニング解除したりすることができるようになった今、技術や予算の制約に縛られることはありません。 企業は、ハードウェア インフラストラクチャに貴重な時間と予算を費やすことなく、アプリケーションやビジネス プロセス、そして顧客とユーザーの利益にのみ目を向けることができます。

Microsoft Azure Virtual Machines サービスを通じて、マイクロソフトは包括的な IaaS (Infrastructure as a Service) プラットフォームを提供しています。 SAP NetWeaver ベースのアプリケーションは、Azure Virtual Machines (IaaS) でサポートされます。 このホワイトペーパーでは、SAP NetWeaver ベースのアプリケーションに最適なプラットフォームをプランニングし、Microsoft Azure に導入する方について説明します。

このホワイトペーパーでは、主に次の 2 つの事柄について重点的に説明します。

* 最初の部分では、Azure 上で SAP NetWeaver ベースのアプリケーションをデプロイする方法としてサポートされている、2 つのデプロイ パターンについて説明します。 また、Azure と SAP デプロイの取り扱いに関する一般的な留意事項についても説明します。
* 2 番目の部分では、最初の部分で説明されている 2 つのシナリオの実装について詳しく説明します。

その他のリソースについては、このドキュメントの「[リソース][planning-guide-1.2]」の章をご覧ください。

### <a name="definitions-upfront"></a>用語の定義
このドキュメントでは、次の用語を使用します。

* IaaS:サービスとしてのインフラストラクチャ
* PaaS:サービスとしてのプラットフォーム
* SaaS:サービスとしてのソフトウェア
* SAP コンポーネント: 個々の SAP アプリケーション (ECC、BW、Solution Manager、S/4HANA など)。  SAP コンポーネントは、従来の ABAP または Java テクノロジか、ビジネス オブジェクトなどの非 NetWeaver ベース アプリケーションに基づいて作成できます。
* SAP 環境: 開発、QAS、トレーニング、DR、実稼働などのビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
* SAP ランドスケープ:この用語は、お客様の IT 環境内にある SAP 資産全体を指します。 SAP ランドス ケープには、運用環境と非運用環境のすべてが含まれます。
* SAP システム:DBMS 層とアプリケーション レイヤーを組み合わせたシステム (SAP ERP 開発システム、SAP BW テスト システム、SAP CRM 運用システムなど)。Azure のデプロイでは、オンプレミスと Azure 間でこれら 2 つの層を分割することはできません。 つまり、SAP システムはオンプレミスか Azure のいずれかにデプロイされます。 ただし、SAP ランドス ケープの異なるシステムを Azure かオンプレミスのいずれかにデプロイすることはできます。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイすることは可能です。
* クロスプレミスまたはハイブリッド:オンプレミスのデータ センターと Azure の間で、サイト間接続、マルチサイト接続、または ExpressRoute 接続を使用する Azure サブスクリプションに VM をデプロイするシナリオを指します。 一般的な Azure ドキュメントでは、この種のデプロイはクロスプレミス シナリオまたはハイブリッド シナリオとして説明されていることもあります。 この接続の目的は、オンプレミスのドメイン、オンプレミスの Active Directory/OpenLDAP、およびオンプレミスの DNS を、Azure 内に拡張することです。 オンプレミスのランドスケープが、サブスクリプションの Azure 資産に拡張されます。 この拡張により。VM はオンプレミス ドメインの一部になることができます。 オンプレミス ドメインのドメイン ユーザーは、サーバーにアクセスし、それらの VM (DBMS サービスなど) 上でサービスを実行できます。 オンプレミスにデプロイした VM と Azure にデプロイした VM の間では、通信と名前解決が可能です。 これは、ほぼ Azure に SAP 資産をデプロイする場合のみの最も一般的なケースです。 詳しくは、[こちら][vpn-gateway-cross-premises-options]article and [this][vpn-gateway-site-to-site-create]をご覧ください。

> [!NOTE]
> SAP システムを実行する Azure Virtual Machines がオンプレミス ドメインのメンバーである SAP システムのクロスプレミスまたはハイブリッドのデプロイは、運用 SAP システムの場合にサポートされます。 クロスプレミスまたはハイブリッドの構成は、SAP ランドスケープの一部または全部を Azure にデプロイする場合にサポートされます。 Azure で完全な SAP ランドスケープを実行する場合でも、これらの VM をオンプレミス ドメインと ADS/OpenLDAP の一部に含める必要があります。 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>リソース
Azure ドキュメントでの SAP ワークロードに関するエントリ ポイントは、[こちら](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)にあります。 このエントリ ポイントを起点に、以下のトピックに関する多くの記事が見つかります。

- Azure での SAP NetWeaver と SAP Business One
- Azure でのさまざまな DBMS システム向けの SAP DBMS ガイド
- Azure での SAP ワークロードのための高可用性とディザスター リカバリー
- Azure での SAP HANA の実行に関する具体的なガイダンス
- SAP HANA DBMS 用の Azure HANA Large Instances に固有のガイダンス 


> [!IMPORTANT]
> 可能な限り、参照する SAP インストール ガイドまたは他の SAP ドキュメントへのリンクが使用されています (Reference InstGuide-01、<http://service.sap.com/instguides> を参照してください)。 前提条件、インストール プロセス、または特定の SAP 機能の詳細に関しては、常に SAP のドキュメントとガイドをよくお読みください。Microsoft のドキュメントでは、Microsoft Azure 仮想マシンにインストールされて動作している SAP ソフトウェアに固有のタスクのみが説明されています。
>
>

次の SAP Notes は、SAP on Azure のトピックに関連します。

| Note 番号 | タイトル |
| --- | --- |
| [1928533] |SAP Applications on Azure:Supported Products and Sizing (Azure 上の SAP アプリケーション: サポートされている製品とサイズ) |
| [2015553] |SAP on Microsoft Azure:Support Prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [1999351] |Troubleshooting Enhanced Azure Monitoring for SAP (強化された Azure Monitoring for SAP のトラブルシューティング) |
| [2178632] |Key Monitoring Metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [1409604] |Virtualization on Windows:Enhanced Monitoring (Windows での仮想化: 拡張された監視機能) |
| [2191498] |SAP on Linux with Azure:Enhanced Monitoring (Azure を使用した Linux での仮想化: 拡張された監視機能) |
| [2243692] |Linux on Microsoft Azure (IaaS) VM:SAP license issues (Microsoft Azure (IaaS) VM 上の Linux: SAP ライセンスの問題) |
| [1984787] |SUSE LINUX Enterprise Server 12:インストールに関する注記 |
| [2002167] |Red Hat Enterprise Linux 7.x:Installation and Upgrade (Red Hat Enterprise Linux 7.x: インストールとアップグレード) |
| [2069760] |Oracle Linux 7.x SAP のインストールとアップグレード |
| [1597355] |Linux のスワップ領域に関する推奨事項 |

Linux に関するすべての SAP ノートが記載された、[SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) も参照してください。

Azure サブスクリプションの一般的な既定の制限事項と最大制限については、[こちらの記事][azure-subscription-service-limits-subscription]に記載されています。

## <a name="possible-scenarios"></a>考えられるシナリオ
多くの場合、SAP は、企業内で最もミッション クリティカル度が高いアプリケーションとして考えられています。 このようなアプリケーションのアーキテクチャと運用はほとんどが複雑であるため、可用性とパフォーマンスの要件を確実に満たすことが重要です。

したがって、企業では、そのようなビジネス クリティカルなビジネス プロセスを実行するクラウド プロバイダーを、慎重に検討して選択する必要があります。 Azure は、ビジネス クリティカルな SAP アプリケーションとビジネス プロセスに理想的なパブリック クラウド プラットフォームです。 Azure インフラストラクチャには幅広いバリエーションがあるので、ほぼすべての既存の SAP NetWeaver と S/4HANA システムを、現在の Azure でホストできます。 Azure では、テラバイト単位のメモリと 200 個を超える CPU が VM に提供されます。 さらに、Azure では [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) が提供されており、HANA のデプロイを最大 24 TB まで、また ANA のデプロイを最大 120 TB まで、スケールアウトできます。 


Azure IaaS または一般的な IaaS のどちらかに SAP システムを正常にデプロイするには、従来のアウトソーシングやホスティングのソリューションと、IaaS ソリューションの重要な違いについて理解する必要があります。 従来のホスティングやアウトソーシングでは、お客様がホスティングを希望するワークロードに合わせて、サービス業者側でインフラストラクチャ (ネットワーク、ストレージおよびサーバー タイプ) を適合させますが、IaaS デプロイの場合は、ワークロードの性質を理解し、Azure で VM、ストレージ、ネットワークの適切なコンポーネントを選択するのは、お客様またはパートナーの責任です。

最初の手順として、顧客は次の項目を確認する必要があります。

* SAP がサポートしている Azure の VM タイプ
* SAP がサポートしている Azure 上の製品/リリース
* Azure 内の特定の SAP リリースに対してサポートされている OS および DBMS のリリース
* 各種の Azure SKU で提供される SAPS スループット

これらの質問に対する答えは、SAP ノート [1928533]に記載されています。

2 番目の手順として、Azure でのリソースと帯域幅の制限事項を、オンプレミス システムの実際のリソース消費量と比較する必要があります。 そのためお客様は、SAP でサポートされる Azure の各種機能について理解しておく必要があります。具体的には、次の機能領域について理解が必要です。

* 各種 VM タイプの CPU およびメモリ リソース
* 各種 VM タイプの IOPS 帯域幅
* 各種 VM タイプのネットワーク機能

これらのデータの多くは、[こちら (Linux)][virtual-machines-sizes-linux] and [here (Windows)][virtual-machines-sizes-windows] から確認できます。

前述のリンクでリストした制限事項は、上限値です。 IOPS を含め、いずれのリソースについても、すべての状況で上限値が提供されるとは限りません。 ただし、選択した VM タイプの CPU およびメモリ リソースについては例外です。 SAP でサポートされている VM タイプについては、CPU とメモリのリソースが予約されるため、任意の時点で VM 内から消費できます。

Microsoft Azure Platform はマルチテナント プラットフォームです。 その結果、ストレージ、ネットワークおよびその他のリソースがテナント間で共有されます。 1 つのテナントが別のテナントのパフォーマンスに大きく影響する問題 (ノイジー ネイバー問題) を防ぐために、インテリジェント スロットルやクォータ ロジックが使用されます。 特に、SAP HANA 用の Azure プラットフォームの認定では、Microsoft は、同じホストで複数の VM を実行できる場合のリソースの分離を、SAP に対して定期的に証明する必要があります。 Azure 内のロジックは、お客様に提供される帯域幅の変動を小規模に維持しようとしますが、共有性の高いプラットフォームでは、オンプレミス デプロイの場合よりも、リソース/帯域幅の可用性が大きく変動する傾向があります。 Azure 上の SAP システムで大きなレベル変動が発生する確率を、オンプレミスの場合と比べて考慮しておく必要があります。

最後のステップでは、可用性の要件を評価します。 実装後には、基盤となる Azure インフラストラクチャの更新や、VM を実行しているホストの再起動が必要になることもありえます。 さまざまなケースについては、「[Azure での仮想マシンのメンテナンス](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)」をご覧ください。 VM が強制的に再起動されるまれなケースを軽減するため、しかしさらに重要なのは、ゲスト OS や DBMS コンポーネントに修正プログラムを適用する必要がある場合のため、運用 SAP システムに対する有効な高可用性の概念を開発する必要があります。 この要件は、オンプレミスで直面する要件と異なるものではありません。 Microsoft は、プラットフォームの変更によって発生するダウンタイムの削減に向けて、Azure プラットフォームを着実に進歩させています。 

SAP システムを Azure に正常にデプロイするには、オンプレミスの SAP システムのオペレーティング システム、データベースおよび SAP アプリケーションが、SAP の Azure サポート マトリックス上にあり、Azure インフラストラクチャで提供できるリソースや、Microsoft Azure で提供される可用性 SLA で動作できる範囲内に収まっている必要があります。 それらのシステムを特定したら、次の 2 つのデプロイメント シナリオのうち、どちらを選ぶかを決定する必要があります。





### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>クロスプレミス - オンプレミスのネットワークに完全に統合されることを要件とする 1 つまたは複数の SAP VM の Azure へのデプロイ
![サイト間接続による VPN (クロスプレミス)][planning-guide-figure-300]

このシナリオはクロスプレミスのシナリオで、さまざまなデプロイ パターンが考えられます。 SAP ランドスケープの一部をオンプレミスで実行しながら、SAP ランドスケープの別の一部を Azure 上で実行するシナリオと言えます。 SAP コンポーネントの一部が Azure で実行される事実について、エンドユーザーにあらゆる情報を開示する必要があります。 それにより、SAP Transport Correction System (STMS)、RFC Communication、Printing、Security (SSO など) が、Azure で実行されている SAP システムに対してシームレスに動作するようになります。 ただし、クロスプレミス シナリオは、SAP ランドスケープ全体を Azure 内で実行し、顧客のドメインと DNS を Azure に拡張するシナリオのことも指します。

> [!NOTE]
> これは、運用 SAP システムの実行用としてサポートされているデプロイ シナリオです。
>
>

オンプレミス ネットワークを Microsoft Azure に接続する方法の詳細については、[こちらの記事][vpn-gateway-create-site-to-site-rm-powershell]を参照してください。

> [!IMPORTANT]
> Azure とオンプレミス デプロイ間でのクロスプレミス シナリオについて考える際には、SAP システム全体の粒度に注意する必要があります。 クロスプレミス シナリオの適用対象として *サポートされていない* シナリオは次のとおりです。
>
> * 複数の SAP アプリケーション層が、それぞれ異なるデプロイメント方法で実行される。 DBMS 層はオンプレミスで実行され、VM 内の SAP アプリケーション層は Azure VM としてデプロイされるなど (またはその逆)。
> * SAP 層のコンポーネントの一部が Azure にあり、一部がオンプレミスにある。 SAP アプリケーション層のインスタンスを、オンプレミスと Azure VM の間で分割するなど。
> * 1 つのシステムの SAP インスタンスを実行している VM を、複数の Azure リージョンにまたがって分散する。
>
> これらの制限事項があるのは、1 つの SAP システム内で低遅延かつ高パフォーマンスのネットワークを確保する必要があるためです (特に、アプリケーション インスタンスと SAP システムの DBMS 層の間)。
>
> 高度に統合された複数の SAP システムを使うときは、システムとリージョンについての特別な計画が必要です。 ネットワークの待機時間が最小になるように、システム同士を可能な限り近くデプロイしてください。 次に示すのは高度に統合された SAP システムの例です。
> * ERP、CRM、SRM のような SAP OLTP システムからデータを読み取る SAP BW
> * 複数の SPA システム間、または SAP システムと SAP 以外のシステム間でデータをレプリケートするために使われる SAP SLT
> * SAP ERP システムに接続された SAP S/4


### <a name="supported-os-and-database-releases"></a>サポートされる OS およびデータベースのリリース
* Azure Virtual Machine Services をサポートしている Microsoft サーバー ソフトウェアは、次の記事 <https://support.microsoft.com/kb/2721672> に記載されています。
* Azure Virtual Machine Services と SAP ソフトウェアとの組み合わせでサポートされるオペレーティング システムのリリースとデータベース システムのリリースは、SAP ノート [1928533]に記載されています。
* Azure Virtual Machine Services でサポートされる SAP アプリケーションとリリースは、SAP ノート [1928533]に記載されています。
* SAP  のシナリオでは、Azure でゲスト VM として実行できるのは  64 ビット イメージだけです。 その結果、SAP のアプリケーションとデータベースも 64 ビットのものしかサポートされません。

## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure Virtual Machine Services
Microsoft Azure Platform は、Microsoft データ センターでホストおよび運営される、インターネット規模のクラウド サービス プラットフォームです。 このプラットフォームには、Microsoft Azure Virtual Machine Services (IaaS :Infrastructure as a Service) と、一連のリッチな PaaS (Platform as a Service ) 機能が含まれています。

Azure プラットフォームを使用することで、お客様はテクノロジとインフラストラクチャの初期費用を軽減できます。 Azure プラットフォームでは、Web アプリケーションと接続型アプリケーションをホスト、拡張、および管理するためのコンピューティング リソースとストレージ をオンデマンドで利用できるので、アプリケーションの保守と運用を簡素化できます。 また、インフラストラクチャの管理も自動化できます。プラットフォームは高可用性に配慮して設計されており、使用ニーズに応じて動的にスケーリングできるよう、従量課金制価格モデルのオプションも提供されています。

![Positioning of Microsoft Azure Virtual Machine Services (Microsoft Azure Virtual Machine Services の配置)][planning-guide-figure-400]

Azure Virtual Machine Services では、カスタムのサーバー イメージを IaaS インスタンスとして Azure にデプロイできます (図 4 参照)。 Azure の仮想マシンは Hyper-V 仮想ハード ドライブ (VHD) に基づいており、さまざまなオペレーティング システム上でゲスト OS として実行できます。

運用の観点から言うと、Azure の仮想マシン サービスはオンプレミスにデプロイされた仮想マシンと同様の機能を提供します。 ただし、インフラストラクチャを購入して管理する必要がないという大きな利点があります。 開発者と管理者は、これらの仮想マシン内のオペレーティング システム イメージをフル コントロールで操作できます。 管理者はこれらの仮想マシンにリモートでサインインして、保守やトラブルシューティングのタスクのほか、ソフトウェア デプロイのタスクを実行できます。 デプロイメントに関する唯一の制限事項は、Azure VM のサイズと機能です。 これらのサイズの構成は、オンプレミスで行う場合ほど詳細に行えない場合があります。 VM タイプの選択肢は、次の項目の組合せで決まります。

* vCPU の数
* メモリ
* 接続できる VHD の数
* ネットワークとストレージの帯域幅

提供される各種仮想マシンのサイズと制限事項については、[こちらの記事 (Linux)][virtual-machines-sizes-linux] and [this article (Windows)][virtual-machines-sizes-windows] の表に記載されています。

なお、各 Azure リージョンで、すべての VM シリーズが提供されるわけではありません (Azure リージョンについては、次の章を参照してください)。 また、すべての VM や VM シリーズが SAP に対して動作確認されているわけではありません。

> [!IMPORTANT]
> SAP NetWeaver ベースのアプリケーションを使用する場合については、SAP ノート [1928533] に記載されている VM タイプと構成の一部のみがサポートされています。
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure リージョン
Virtual Machines は、いわゆる *Azure リージョン*にデプロイされます。 Azure リージョンは、近接する場所に配置された、1 つまたは複数のデータ センターです。 Microsoft では、全世界の地理的リージョンのほとんどに、少なくとも 2 つの Azure リージョンを設置しています。 たとえばヨーロッパでは、*北ヨーロッパ*と*西ヨーロッパ*のそれぞれに Azure リージョンがあります。 このように、1 つの地理的リージョン内に 2 つの Azure リージョンがある場合、自然災害や技術的障害による影響が同じ地理的リージョンの両方の Azure リージョンに及ばないように、各リージョンが十分な距離で隔てられます。 Microsoft では、全世界のさまざまな地理的リージョンに新しい Azure リージョンを継続的に構築しており、そのリージョン数は着実に増加しています。2015 年 12 月時点での Azure リージョン数は、既に発表されているその他 のリージョンと合わせて 20 に達しています。 お客様は、中国にある 2 つの Azure リージョンを含め、これらすべてのリージョンに SAP システムをデプロイできます。 Azure リージョンの最新情報については、次の Web サイト <https://azure.microsoft.com/regions/> を参照してください。

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Microsoft Azure Virtual Machine の概念
Microsoft Azure では、オンプレミスの仮想化ソリューションと同様の機能を持った仮想マシンをホストできる、IaaS (Infrastructure as a Service) ソリューションを提供しています。 Azure Portal、PowerShell または CLI 内から Virtual Machines を作成できるほか、デプロイ機能や管理機能も提供されます。

Azure リソース マネージャーでは、宣言型のテンプレートを使用してアプリケーションをプロビジョニングすることができます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用して繰り返しアプリケーションをデプロイします。

Resource Manager テンプレートの使用方法の詳細については、次の資料を参照してください。

* [Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](../../linux/create-ssh-secured-vm-from-template.md)
* [Azure Resource Manager と PowerShell を使用した仮想マシンの管理][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

もう 1 つの面白い機能として、Virtual Machines からイメージを作成する機能があります。この機能を使用すれば、特定のリポジトリを準備して、そこから要件に合った仮想マシンをすばやくデプロイすることができます。

仮想マシンからイメージを作成する方法の詳細については、[こちらの記事 (Linux)][virtual-machines-linux-capture-image-resource-manager] and [this article (Windows)][virtual-machines-windows-capture-image-resource-manager] に記載されています。

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>障害ドメイン
障害ドメインとは、障害の物理的単位のことです。これは、データ センターに含まれる物理インフラストラクチャと密接に関連します。物理的なブレードやラックは障害ドメインと見なすことができますが、2 つの障害ドメイン間に 1 対 1 の直接的なマッピングは存在しません。

Microsoft Azure Virtual Machine Services で複数の仮想マシンを 1 つの SAP システムの一部としてデプロイする場合は、Azure ファブリック コントローラーを通じてアプリケーションを複数の障害ドメインにデプロイすることで、Microsoft Azure SLA の要件に対応することができます。 ただし、Azure スケール ユニット (数百のコンピューティング ノードやストレージ ノードとネットワークをまとめたコレクション) 内で障害ドメインを分散したり、特定の障害ドメインに VM を割り当てることは、お客様が直接制御できる操作ではありません。 Azure ファブリック コントローラーを通じて一連の VM を複数の障害ドメインにデプロイするには、デプロイメント時に、VM に Azure 可用性セットを割り当てる必要があります。 Azure 可用性セットについて詳しくは、このドキュメントの「[Azure 可用性セット][planning-guide-3.2.3]」の章をご覧ください。

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>アップグレード ドメイン
アップグレード ドメインは論理ユニットを表します。これは、複数の VM で実行されている SAP インスタンスで構成された、SAP システム内の VM をどのように更新するかを決定するのに役立ちます。 アップグレードが発生すると、Microsoft Azure ではこれらのアップグレード ドメインが 1 つずつ更新されていきます。 お客様は、デプロイ時に VM を複数のアップグレード ドメインに分散させることで、ダウンタイムの発生時に SAP システムを保護することができます。 SAP システムの VM を複数のアップグレード ドメインに強制的に分散するには、デプロイメント時に各 VM に対して固有の属性を設定する必要があります。 障害ドメインと同様、Azure スケール ユニットは複数のアップグレード ドメインに分割されます。 Azure ファブリック コントローラーを通じて一連の VM を複数のアップグレード ドメインにデプロイするには、デプロイメント時に、VM に Azure 可用性セットを割り当てる必要があります。 Azure 可用性セットについて詳しくは、次の「[Azure 可用性セット][planning-guide-3.2.3]」の章をご覧ください。

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure の可用性セット
1 つの Azure 可用性セット内の Azure Virtual Machines は、Azure ファブリック コントローラーによって複数の障害ドメインとアップグレード ドメインに分散されます。 複数のフォールト ドメインとアップグレード ドメインに分散する目的は、インフラストラクチャの保守が実行される際や、1 つの障害ドメイン内での障害が発生した際に、SAP システムのすべての VM がシャットダウンされるのを防ぐためです。 既定では、VM は可用性セットに含まれていません。 VM を可用性セットに参加させるかどうかは、デプロイ時またはデプロイ後に、VM を再構成し、再デプロイして定義します。

Azure 可用性セットの概念と、可用性セットが障害ドメインやアップグレード ドメインとどう関連するかについて理解するには、[こちらの記事][virtual-machines-manage-availability]をお読みください。

JSON テンプレートを使用して Azure Resource Manager の可用性セットを定義する場合は、[REST-API の仕様](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json)に関するページを参照し、"可用性" を検索してください。

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>ストレージ:Microsoft Azure Storage とデータ ディスク
Microsoft Azure Virtual Machines では、複数のストレージ タイプが使用されます。 Azure Virtual Machine Services で SAP を実装するにあたっては、2 つの主要ストレージ タイプの違いについて理解しておくことが重要です。

* 非永続的 (揮発性) ストレージ。
* 永続的ストレージ。

Azure VM は、VM をデプロイした後に非永続ディスクを提供します。 VM が再起動された場合、非永続ドライブ上のすべてのコンテンツは消去されます。そのため、データ ファイルとデータベースのログ/再実行ファイルが、どんな状況でも、非永続ドライブ上にないことが条件になります。 例外として、データベースによっては、tempdb や temp tablespaces などにこれらの非永続ドライブが適している場合があります。 ただし、非永続ドライブのスループットは、その VM ファミリーで制限されているために、A シリーズ VM にはこれらのドライブを使用しないでください。 詳しくは、「[Understanding the temporary drive on Windows VMs in Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)」(Azure での Windows VM 上の一時ドライブ) をご覧ください

---
> ![Windows][Logo_Windows] Windows
> 
> Azure VM の D:\ ドライブは、Azure の計算ノード上のいくつかのローカル ディスクによってサポートされる非永続ドライブです。 非永続であるということは、VM が再起動されると、D:\ ドライブ上のコンテンツに加えられた変更が失われることを意味します。 "変更" とは、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションなどを指します。
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM は、Azure の計算ノード上のローカル ディスクによってサポートされる非永続ドライブである /mnt/resource ドライブを自動的にマウントします。 非永続であるということは、VM が再起動されると、/mnt/resource ドライブ上のコンテンツに加えられた変更が失われることを意味します。 変更とは、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションなどを指します。
> 
> 

---

Microsoft Azure Storage では、永続的ストレージが提供されます。保護機能は一般的なレベルで、冗長性は SAN ストレージと同様のものです。 Azure Storage に基づくディスクは、Azure Storage Services 内に置かれた仮想ハード ディスク (VHD) です。 ローカル OS ディスク (Windows C:\,Linux /dev/sda1) は Azure Storage に格納され、VM にマウントされたその他のボリューム/ディスクもそこに格納されます。

オンプレミスから既存の VHD をアップロードしたり、Azure 内から空の VHD を作成して、デプロイされた VM に接続することもできます。

VHD の作成や Azure Storage へのアップロードが終わったら、それらを既存の仮想マシンにマウントして接続したり、既存の (マウントされていない) VHD をコピーすることができます。

これらの VHD は永続化され、仮想マシン インスタンスを再起動したり再作成した場合にも、ドライブ内のデータや変更は保護されます。 インスタンスが削除された場合でも、これらの VHD は保護され、再デプロイが可能なだけでなく、非 OS ディスクの場合には、他の VM にマウントすることもできます。

Azure Storage に関する情報は、次のリンクからも参照できます。

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Azure Standard ストレージは、Azure IaaS のリリース時に提供されたストレージ タイプでした。 このストレージでは、1 つのディスクごとに IOPS クォータが適用されていました。 待機時間は、オンプレミスでホストされたハイエンド SAP システム用に通常デプロイされる、SAN/NAS デバイスの水準を下回るものでした。 これに対し、Azure Standard Storage は、Azure にデプロイされた数百個の SAP システムにも十分対応できることが確認されています。

Azure Standard Storage アカウントで保存されているディスクの料金は、保存されている実際のデータ、ストレージ トランザクションの量、送信データの転送量、および選択した冗長性オプションに基づいて請求されます。 多くのディスクは最大サイズの 1 TB で作成できますが、空の状態である限り料金は発生しません。 その後、1 つの VHD に 100 GB ずつデータを保存すると、VHD 作成時の基準サイズではなく、100 GB 保存した分の料金が課金されます。

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Storage
Azure Premium Storage は、次のメリットを提供する目的で導入されました。

* I/O 待機時間の短縮。
* スループットの向上。
* I/O 待機時間の変動性の緩和。

この目的のために、多くの変更が導入されました。特に重要なものは次の 2 点です。

* Azure Storage ノード内での SSD ディスクの使用
* Azure コンピューティング ノードのローカル SSD を使用した新しい読み取りキャッシュ

Standard Storage では、ディスク (または VHD) のサイズによって機能が変わることはありませんでしたが、Premium Storage には、現在、3 つのディスク カテゴリがあります。詳細については、次の記事 <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/> を参照してください。

IOPS/ディスクとディスク スループット/ディスクは、ディスクのサイズのカテゴリによって変わります。

Premium Storage の場合、コスト単位はディスクに保存されている実際のデータ ボリュームではなく、ディスクのサイズ カテゴリで決まります (ディスクに保存されているデータの量には関係しません)。

なお、Premium Storage では、サイズ カテゴリに直接マップされないディスクを作成することもできます。 これは特に、Standard Storage から Premium Storage にディスクをコピーする場合に該当します。 このような場合には、Premium Storage のディスク オプションのうち、次に大きいオプションに対してマッピングが実行されます。

SAP で認定される Azure VM ファミリのほとんどは、Premium Storage で、または Azure Standard と Premium Storage の組み合わせで使用できます。

[こちらの記事 (Linux)][virtual-machines-sizes-linux] and [this article (Windows)][virtual-machines-sizes-windows] の DS シリーズ VM の箇所でも説明されていますが、Premium Storage のディスクには、VM レベルの粒度に関してデータ量の制限があります。 また、DS シリーズや GS シリーズの VM では、マウントできるデータ ディスクの数についても、それぞれ異なる制限事項があります。 これらの制限事項も、上記の記事で説明されています。 ただし基本的に言って、たとえば 32 基の P30 ディスクを 1 つの DS14 VM にマウントした場合に、P30 ディスクの最大スループットが 32 倍になるわけではないということです。 記事でも説明されているように、データのスループットは、VM レベルの最大スループットによって制限されます。

Premium Storage に関する情報は、次のリンク <https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2> から参照できます。

#### <a name="azure-storage-accounts"></a>Azure Storage アカウントについて

Azure にサービスや VM をデプロイする際には、VHD と VM イメージのデプロイを Azure Storage アカウントという単位にまとめることができます。 Azure デプロイメントを計画する際は、Azure の制限事項を慎重に考慮してください。 Azure サブスクリプションあたりの Storage アカウント数には制限があります。 各 Azure Storage アカウントには多数の VHD ファイルを保持することができますが、Storage アカウントあたりの合計 IOPS には制限があります。 DBMS システムを使用した SAP VM を数百台デプロイして大量の IO コールを生成する場合は、高 IOPS の DBMS VM を複数の Azure Storage アカウント間で分散することをお勧めします。 サブスクリプションあたりの Azure Storage アカウントの制限を超えないように注意してください。 ストレージは SAP システム用データベース デプロイの重要な部分であるため、この概念については、前述の [DBMS デプロイ ガイド][dbms-guide]で詳しく説明されています。

Azure ストレージ アカウントの詳細については、[こちらの記事][storage-scalability-targets]で説明されています。 この記事でも説明されていますが、Azure Standard Storage アカウントと Premium Storage アカウントでは制限事項に違いがあります。 主な違いは、ストレージ アカウント内に保存できるデータの量です。 Standard Storage では、データ量が Premium Storage よりも大きく設定されています。 ただし、Standard Storage アカウントでは IOPS が厳しく制限されています (**合計要求レート**列を参照してください)。Azure Premium Storage アカウントでは、このような制限はありません。 これらの違いの詳細とその影響については、SAP システムのデプロイメント (特に DBMS サーバー) に関するセクションで説明します。

ストレージ アカウント内では、さまざまな VHD を整理・分類するために、複数のコンテナーを作成できます。 これらのコンテナーは、VHD を VM ごとに分離する目的などに使用されます。 1 つの Azure Storage アカウントでコンテナーを 1 つだけ使用するか、それとも複数使用するかによって、パフォーマンスが影響を受けることはありません。

Azure 内では、次の名前接続に従って VHD 名を指定します。これにより、Azure 内の VHD に一意の名前を指定します。

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

上記の文字列では、Azure Storage に格納されている VHD を一意に識別する必要があります。

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Managed Disks

Managed Disks は Azure Resource Manager の新しいリソースの種類で、Azure Storage アカウントに格納されている VHD の代わりに使用できます。 Managed Disks は接続されている仮想マシンの可用性セットに自動的に配置され、仮想マシンと仮想マシンで実行されているサービスの可用性を向上させます。 詳細については、[概要についての記事](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)を参照してください。

仮想マシンのデプロイと管理が簡単なので、マネージド ディスクを使うことをお勧めします。
SAP は現在、Premium Managed Disks のみをサポートします。 詳細については、SAP Note [1928533] を参照してください。

#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage の回復性

Microsoft Azure Storage は、ベース VHD (OS を含む) とアタッチされたディスクまたは BLOB を 3 つ以上の個別のストレージ ノードに格納します。 これは、ローカル冗長ストレージ (LRS) と呼ばれます。 LRS は、Azure 内のすべての種類のストレージの既定値です。 

この他にいつくかの冗長方法があり、それらすべてが「[Azure Storage のデータ レプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)」記事で説明されています。

> [!NOTE]
>DBMS VM とデータベースおよび、ログ/再実行ファイルを保存するディスクのストレージとして推奨される種類である Azure Premium Storage で、使用可能な方法は LRS のみです。 このため、他の Azure Region または他の Azure Availability Zone へのデータベース データ レプリケーションを可能にするには、SQL Server Always On、Oracle Data Guard または HANA システム レプリケーションなどのデータベース方法を構成する必要があります。


> [!NOTE]
> DBMS デプロイの場合、Azure Standard Storage で使用可能な Geo Redundant Storage は、パフォーマンスへの影響が大きいことと、VM にアタッチされた複数の VHD にわたる書き込み順序に従わないためお勧めしません。 複数の VHD にわたる書き込み順序に従わないため、ソース VM 側で複数の VHD にわたってデータベースやログ/再実行ファイルが分散している場合 (ほどんどの場合に該当)、レプリケーション ターゲット側のデータベースの整合性が取れない可能性が高くなります。


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure のネットワーク

Microsoft Azure は、SAP ソフトウェアで実現できるすべてのシナリオに対応可能な、ネットワーク インフラストラクチャを提供します。 機能は次のとおりです。

* Windows ターミナル サービスまたは ssh/VNC を使用した、外部から VM への直接アクセス
* VM 内のアプリケーションで使用されるサービスと特定のポートへのアクセス
* Azure VM としてデプロイされた VM グループ間での内部通信と名前解決
* お客様のオンプレミス ネットワークと Azure ネットワーク間のクロスプレミス接続
* Azure サイト間での、Azure リージョンまたはデータ センターをまたいだ接続

詳細については、こちら <https://azure.microsoft.com/documentation/services/virtual-network/> をご覧ください。

Azure での名前解決と IP 解決の構成については、さまざまなケースが考えられます。 独自の DNS サーバーを設定する代わりに使用できる、Azure DNS サービスもあります。 詳細については、[こちらの記事][virtual-networks-manage-dns-in-vnet]と[こちらのページ](https://azure.microsoft.com/services/dns/)で説明されています。

クロスプレミスまたはハイブリッドのシナリオの場合は、オンプレミスの AD/OpenLDAP/DNS は Azure への VPN 接続またはプライベート接続を使用して拡張されているという事実に基づいています。 ここに記載されている特定のシナリオについては、AD/OpenLDAP レプリカを Azure にインストールする必要がある場合があります。

ネットワークと名前解決は SAP システム用データベース デプロイの重要な部分であるため、この概念については、[DBMS デプロイ ガイド][dbms-guide]で詳しく説明されています。

##### <a name="azure-virtual-networks"></a>Azure 仮想ネットワーク

Azure Virtual Network を作成することで、Azure の DHCP 機能によって割り当てられたプライベート IP アドレスのアドレス範囲を定義できます。 クロスプレミス シナリオでは、定義されている IP アドレス範囲が Azure によって DHCP を使用して割り当てられます。 ただし、ドメイン名解決は (VM がオンプレミス ドメインの一部であると想定して) オンプレミスで実行されるため、複数の Azure Cloud Services にまたがってアドレスを解決できます。

Azure 内のすべての仮想マシンは、Virtual Network に接続される必要があります。

詳細については、[こちらの記事][resource-groups-networking]と[こちらのページ](https://azure.microsoft.com/documentation/services/virtual-network/)で説明されています。


> [!NOTE]
> 既定では、VM がデプロイされた後に Virtual Network の構成を変更することはできません。 TCP/IP 設定は、Azure DHCP サーバーのままにしておく必要があります。 既定の動作は、動的 IP 割り当てです。
>
>

仮想ネットワーク カードの MAC アドレスは、変更される可能性があります (たとえば、サイズ変更の後に、Windows または Linux のゲスト OS が新しいネットワーク カードを選択し、DHCP を自動的に使用して IP アドレスと DNS アドレスを割り当てた場合など)。

##### <a name="static-ip-assignment"></a>静的 IP 割り当て
Azure Virtual Network 内の VM には、固定または予約済み IP アドレスを割り当てることができます。 Azure Virtual Network で VM を実行すると、特定のシナリオで必要になった場合に、この機能を使用する可能性が大いに高まります。 IP 割り当ては、VM が実行中かシャットダウン中かにかかわらず、VM が存在しているかぎり有効です。 そのため、Virtual Network 用の IP アドレスの範囲を定義する際には、全体的な VM の数 (実行中の VM と停止中の VM) を考慮に入れる必要があります。 IP アドレスは、VM とそのネットワーク インターフェイスが削除されるか、または IP アドレスの割り当てが再度解除されるまで、割り当てられたままになります。 詳細については、[こちらの記事][virtual-networks-static-private-ip-arm-pportal]をご覧ください。

> [!NOTE]
> Azure を使用して個々の vNIC に静的 IP アドレスを割り当てる必要があります。 ゲスト OS 内の静的 IP アドレスを vNIC に割り当てないでください。 Azure Backup Service のような一部の Azure サービスは、少なくともプライマリ vNIC が、静的 IP アドレスではなく、DHCP に設定されていることに依存します。 「[Azure 仮想マシンのバックアップのトラブルシューティング](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)」というドキュメントも参照してください。
>
>

##### <a name="multiple-nics-per-vm"></a>VM あたり複数の NIC

Azure 仮想マシンには、複数の仮想ネットワーク インターフェイス カード (vNIC) を定義できます。 複数の vNIC を使用できるため、ネットワーク トラフィックの分離を設定することもできます (たとえば、クライアント トラフィックを 1 つの vNIC でルーティングし、バックエンド トラフィックを 2 つ目の vNIC でルーティングするなど)。 vNIC の数に関する制限は、VM のタイプによって異なります。 機能や制限事項の詳細については、次の記事で説明されています。

* [複数の NIC を持つ Windows VM の作成][virtual-networks-multiple-nics-windows]
* [複数の NIC を持つ Linux VM を作成する][virtual-networks-multiple-nics-linux]
* [テンプレートを使用した複数の NIC VM のデプロイ][virtual-network-deploy-multinic-arm-template]
* [PowerShell を使用した複数の NIC VM のデプロイ][virtual-network-deploy-multinic-arm-ps]
* [Azure CLI を使用した複数の NIC VM のデプロイ][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>サイト間接続

クロスプレミスとは、Azure VM とオンプレミスが透過的かつ永続的な VPN 接続によってリンクされた環境のことを指します。 これは、Azure で最も一般的な SAP デプロイメント パターンであると考えられます。 クロスプレミスでは、Azure 内の SAP インスタンスを使用した操作手順とプロセスが、透過的に連携する必要があります。 つまり、これらのシステムを出力できるだけでなく SAP トランスポート管理システム (TMS) を使用して、Azure 内の開発システムからオンプレミスにデプロイされたテスト システムに、変更をトランスポートできる必要があります。 サイト間接続の詳細については、[こちらの記事][vpn-gateway-create-site-to-site-rm-powershell]で説明されています。

##### <a name="vpn-tunnel-device"></a>VPN トンネル デバイス

サイト間 (Azure データ センターとオンプレミス データ センター間) の接続を作成するには、VPN デバイスを取得して構成するか、Windows Server 2012 でソフトウェア コンポーネントとして導入されたルーティングとリモート アクセス サービス (RRAS) を使用する必要があります。

* [PowerShell を使用したサイト間 VPN 接続を持つ仮想ネットワークの作成][vpn-gateway-create-site-to-site-rm-powershell]
* [サイト間 VPN Gateway 接続の VPN デバイスについて][vpn-gateway-about-vpn-devices]
* [VPN Gateway に関する FAQ][vpn-gateway-vpn-faq]

![Site-to-site connection between on-premises and Azure (オンプレミスと Azure 間のサイト間接続)][planning-guide-figure-600]

上の図は、2 つの Azure サブスクリプションで、Azure の Virtual Network で使用するための IP アドレス サブ範囲が予約されている状況を示したものです。 オンプレミス ネットワークから Azure への接続が、VPN 経由で確立されています。

#### <a name="point-to-site-vpn"></a>ポイント対サイト VPN

ポイント対サイト VPN では、すべてのクライアント マシンが独自の VPN で Azure に接続する必要があります。 ここで取り上げている SAP シナリオでは、ポイント対サイト接続は実用的ではありません。 そのため、ポイント対サイト VPN 接続に関する説明はこれ以上行いません。

詳細については、こちらを参照してください。
* [Azure Portal を使用した VNet へのポイント対サイト接続の構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [PowerShell を使用した VNet へのポイント対サイト接続の構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>マルチサイト VPN

Azure では最近、1 つの Azure サブスクリプションでマルチサイト VPN 接続を作成できる機能も提供されています。 以前、単一サブスクリプションは 1 つのサイト間 VPN 接続に制限されていました。 単一サブスクリプション用のマルチサイト VPN 接続によって、この制限はなくなりました。 これにより、クロスプレミス構成を通じて、特定のサブスクリプション用に複数の Azure リージョンを使用できるようになりました。

その他のドキュメントについては、[こちらの記事][vpn-gateway-create-site-to-site-rm-powershell]を参照してください。

#### <a name="vnet-to-vnet-connection"></a>VNet 間接続

マルチサイト VPN を使用して、各リージョンに個別の Azure Virtual Network を構成する必要があります。 しかし、多くの場合、異なるリージョン内のソフトウェア コンポーネント間で相互通信が必要になります。 この通信では、Azure リージョンからオンプレミスにルーティングし、そこから別の Azure リージョンにルーティングするという経路は望ましくありません。 経路を短縮するために、Azure では、1 つのリージョンにある 1 つの Azure Virtual Network を、別のリージョンでホストされているもう 1 つの Azure Virtual Network に接続するよう構成できます。 この機能は、VNet 間接続と呼ばれます。 この機能の詳細についてはこちら <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/> をご覧ください。

#### <a name="private-connection-to-azure-expressroute"></a>Azure ExpressRoute へのプライベート接続

Microsoft Azure ExpressRoute を使用すれば、Azure データ センターと、お客様のオンプレミス インフラストラクチャまたはコロケーション環境との間に、プライベート接続を作成できます。 ExpressRoute は、さまざまな MPLS (パケット切り替え) VPN プロバイダーや、その他のネットワーク サービス プロバイダーによって提供されます。 ExpressRoute 接続では、公共のインターネットを利用できません。 ExpressRoute 接続は、複数の並列回線を使用するため安全性と信頼性が高く、インターネット経由の一般的な接続に比べて待機時間も短く、高速です。

Azure ExpressRoute と各種ソリューションの詳細については、次のリンクから確認できます。

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route では、1 つの ExpressRoute 回線を通じて複数の Azure サブスクリプションに対応できます。詳細については、次のリンクを参照してください

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>クロスプレミスのケースでの強制トンネリング
サイト間、ポイント対サイトまたは ExpressRoute を使用してオンプレミス ドメインに参加している VM については、それらの VM 内のすべてのユーザーに対して、インターネット プロキシ設定がデプロイされていることも確認する必要があります。 既定では、これらの VM で実行されているソフトウェアや、ブラウザーを使用してインターネットにアクセスしているユーザーは、会社のプロキシを通過せず、Azure をそのまま通過してインターネットに接続します。 ただしプロキシ設定も、トラフィックを会社のプロキシ経由にするための完全な解決方法ではありません。プロキシの確認は、ソフトウェアやサービス側で行われるためです。 VM で実行されているソフトウェアがこれを行わない場合や、管理者が設定を操作した場合、インターネットへのトラフィックは、Azure からインターネットへの直接経路へと再度迂回される可能性があります。

このようなインターネットへの直接接続を回避するために、オンプレミスと Azure の間のサイト間接続を使用した強制トンネルを構成できます。 強制トンネリングの機能の詳細については、ここ <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/> を参照してください。

ExpressRoute を使用した強制トンネリングは、ExpressRoute BGP ピアリング セッションを介して、既定のルートを告知することで有効になります。

#### <a name="summary-of-azure-networking"></a>Azure ネットワークの概要

この章では、Azure のネットワークに関する重要なポイントを多数紹介しました。 主なポイントを以下にまとめます。

* Azure Virtual Network では、ネットワーク構造を Azure デプロイに配置することができます。 VNet をそれぞれ分離させることができます。また、ネットワーク セキュリティ グループを利用して、VNet 間のトラフィックを制御することもできます。
* Azure Virtual Network では、VM に IP アドレスの範囲を割り当てたり、固定の IP アドレスを割り当てることができます
* サイト間またはポイント対サイト接続を設定するには、最初に Azure Virtual Network を作成する必要があります
* 仮想マシンをデプロイした後は、VM に割り当てられた Virtual Network を変更することはできません

### <a name="quotas-in-azure-virtual-machine-services"></a>Azure Virtual Machine Services のクォータ
ストレージとネットワーク インフラストラクチャは、Azure インフラストラクチャ内の各種サービスを実行している VM 間で共有されます。 また、お客様のデータ センターと同様、インフラストラクチャ リソースの一部が過剰にプロビジョニングされる状況も、ある程度は発生します。 Microsoft Azure Platform では、リソース消費を制限し、パフォーマンスの一貫性と確定性を維持するため、ディスク、CPU、ネットワークおよびその他のクォータが使用されます。  ディスク数、CPU、RAM、およびネットワーククォータは、VM のタイプ (A5、A6 など) によって異なります。

> [!NOTE]
> SAP でサポートされている VM タイプの CPU リソースとメモリ リソースは、ホスト ノードで事前に割り当てられます。 つまり、VM がデプロイされると、VM タイプごとの定義に従って、ホスト上のリソースが利用可能になります。
>
>

SAP on Azure ソリューションを計画したり、サイズを設定する際には、各仮想マシン サイズに対するクォータを考慮する必要があります。 VM クォータの詳細については、[こちら (Linux)][virtual-machines-sizes-linux] and [here (Windows)][virtual-machines-sizes-windows] をご覧ください。

記載されているクォータは、理論上の最大値を表します。  ディスクあたりの IOPS の制限は、小規模な IO (8 kb) で達成される場合もありますが、大規模な IO (1 Mb) でも達成されない場合もあります。  IOPS 制限は、1 つのディスクの粒度に対して適用されます。

SAP システムが Azure Virtual Machine サービスとその機能に適合するかどうかや、システムを Azure にデプロイするために既存のシステムの構成を変える必要があるかどうかを決定するための大まかなデシジョン ツリーとしては、以下のデシジョン ツリーを使用できます。

![Decision tree to decide ability to deploy SAP on Azure (SAP on Azure をデプロイするための機能を決めるデシジョン ツリー)][planning-guide-figure-700]

**手順 1.** :まず検討すべき重要な情報は、該当の SAP システムに対する SAPS 要件です。 SAPS 要件は、DBMS 部分と SAP アプリケーション部分に分離する必要があります (SAP システムが既に 2 層構成でオンプレミスにデプロイされている場合でも)。 既存のシステムについては、多くの場合、使用しているハードウェアの関連 SAPS を、既存の SAP ベンチマークに基づいて決定または推定できます。 結果については、ここ <https://sap.com/about/benchmark.html> を参照してください。
新しくデプロイされる SAP システムについては、サイズ決定作業が済んでいるはずなので、それに基づいてシステムの SAPS 要件を決定できます。
Azure 上の SAP サイジングについては、このブログに加えドキュメント <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx> も併せて参照してください。

**手順 2**:既存のシステムについて、DBMS サーバーの 1 秒あたりの I/O ボリュームと I/O 操作を測定する必要があります。 新規に計画したシステムについては、新しいシステムのサイズ設定を基に、DBMS 側の I/O 要件も大まかに把握する必要があります。 把握できない場合は、最終的に概念実証を実行する必要があります。

**手順 3**:DBMS サーバーの SAPS 要件を、Azure の別の VM タイプの SAPS と比較します。 別の Azure VM タイプの SAPS に関する情報は、SAP ノート [1928533]に記載されています。 データベース層は、大半のデプロイでスケールアウトされない、SAP NetWeaver システム内の層であるため、DBMS VM をまず最初に検討する必要があります。 これに対し、SAP アプリケーション層はスケール アウトが可能です。SAP でサポートされているいずれの Azure VM タイプでも、必要な SAPS を提供できない場合、計画した SAP システムのワークロードを Azure で実行することはできません。 システムをオンプレミスでデプロイするか、システムのワークロードのボリュームを変更する必要があります。

**手順 4**:[こちら (Linux)][virtual-machines-sizes-linux] and [here (Windows)][virtual-machines-sizes-windows] の記事で説明されているように、Azure では、Standard Storage と Premium Storage のどちらを使用するかにかかわらず、ディスクあたりの IOPS クォータが強制的に適用されます。 マウントできるデータ ディスクの数は、VM タイプによって異なります。 そのため、達成できる最大 IOPS 数は、VM タイプごとに計算できます。 データベース ファイルのレイアウトによっては、複数のディスクをストライピングして、ゲスト OS 内で 1 つのボリュームにすることができます。 ただし、デプロイされた SAP システムの現在の IOPS ボリュームが、Azure の最大 VM タイプの計算済み制限を超えていて、メモリの追加によって対処することもできない場合は、SAP システムのワークロードに深刻な影響が及ぶ可能性があります。 このような場合は、Azure でのシステムのデプロイを断念せざるを得ない可能性があります。

**手順 5**:特に、2 層構成でオンプレミスにデプロイされている SAP システムについては、Azure では 3 層構成で構成しなければならない可能性があります。 この手順では、SAP アプリケーション層のコンポーネントの中に、スケールアウトすることができず、別の Azure VM タイプの CPU リソースとメモリ リソースにも適合しないコンポーネントがあるかどうかを確認する必要があります。 このようなコンポーネントがある場合、SAP システムとそのワークロードを Azure にデプロイすることはできません。 ただし、SAP アプリケーション コンポーネントを複数の Azure VM にスケールアウトできる場合は 、システムを Azure にデプロイできます。

**手順 6**:DBMS および SAP アプリケーション層のコンポーネントを Azure VM で実行できる場合は、次の点について構成を定義する必要があります。

* Azure VM の数
* 各コンポーネントの VM タイプ
* 十分な IOPS を提供するために必要な、DBMS VM 内の VHD の数

## <a name="managing-azure-assets"></a>Azure 資産の管理

### <a name="azure-portal"></a>Azure ポータル

Azure Portal は、Azure VM デプロイを管理するための 3 つのインターフェイスの 1 つです。 Azure Portal では、基本的な管理タスクを実行できます (イメージから VM をデプロイするなど)。 また、Storage アカウント、Virtual Network およびその他の Azure コンポーネントの作成も、Azure portal で問題なく処理できるタスクです。 ただし、オンプレミスから Azure に VHD をアップロードしたり、Azure 内で VHD をコピーするには、サード パーティ製ツールを使用するか、PowerShell や CLI を使用した管理タスクが必要になります。

![Microsoft Azure Portal - 仮想マシンの概要][planning-guide-figure-800]


仮想マシン インスタンスの管理タスクと構成タスクは、Azure Portal 内で実行できます。

また、仮想マシンの再起動やシャットダウンができるほか、仮想マシン インスタンスのデータ ディスクをアタッチ、デタッチ、および作成して、イメージ準備用のインスタンスをキャプチャしたり、仮想マシン インスタンスのサイズを構成することもできます。

Azure Portal では、VM とその他のさまざまな Azure サービスをデプロイして構成するための、基本的な機能が提供されます。 ただし、すべての機能が Azure Portal でカバーされるわけではありません。 Azure Portal では、次のようなタスクを実行することはできません。

* Azure に VHD をアップロードする
* VM をコピーする


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Microsoft Azure PowerShell コマンドレットを使用した管理

Windows PowerShell は、Azure に多数のシステムをデプロイしているお客様に広く採用されている、強力で拡張可能なフレームワークです。 デスクトップ、ラップトップ、または専用の管理ステーションに PowerShell コマンドレットをインストールした後は、PowerShell コマンドレットをリモートで実行できます。

Azure PowerShell コマンドレットをローカルのデスクトップ/ラップトップで使用できるようにするためのプロセスと、それらを Azure サブスクリプションで使用できるように構成する方法については[こちらの記事][powershell-install-configure]で説明しています。

また、Azure PowerShell コマンドレットをインストール、更新、および構成するための詳細な手順については、[デプロイ ガイドのこちらの章][deployment-guide-4.1]で説明しています。

VM をデプロイしたり、VM のデプロイメント内でカスタム ステップを作成するための強力なツールとして、PowerShell (PS) はお客様から高く評価されています。 Azure で SAP インスタンスを実行しているすべてのお客様が、PS コマンドレットを使用して Azure Portal の管理機能を補完したり、Azure 内のデプロイを管理するための専用ツールとして PS コマンドレットを使用しています。 Azure 用のコマンドレットでは、2,000 を超える Windows 関連コマンドレットと同じ命名規則が使用されているので、これらのコマンドレットを使用している Windows 管理者であれば、どなたでも簡単に Azure 用のコマンドレットを使用できます。

次の例 <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx> を参照してください。


Azure Monitoring Extension for SAP のデプロイ (このドキュメントの「[Azure Monitoring Solution for SAP][planning-guide-9.1]」の章をご覧ください) は、PowerShell または CLI を通じてのみ実行可能です。 したがって、Azure で SAP NetWeaver システムをデプロイまたは管理する場合には、PowerShell または CLI を設定し、構成することが必須となります。  

Azure の機能が増えるのに応じて、新しい PS コマンドレットが追加され、コマンドレットの更新が必要になります。 このため、少なくとも月に 1 回は、Azure のダウンロード サイト <https://azure.microsoft.com/downloads/> で新しいバージョンのコマンドレットを確認することをお勧めします。 新バージョンは、以前のバージョンの上にインストールされます。

Azure 関連の PowerShell コマンドについての総目録については、ここ <https://docs.microsoft.com/powershell/azure/overview> を参照してください。

### <a name="management-via-microsoft-azure-cli-commands"></a>Microsoft Azure CLI コマンドを使用した管理

Linux を使用するお客様で、Azure のリソースを管理したいお客様には、Powershell をお勧めします。 Microsoft では、その代替ツールとして、Azure CLI を提供しています。
Azure CLI は、Azure Platform で使用できるオープン ソース、クロスプラットフォームのコマンド群です。 Azure CLI では、Azure ポータルと同じ機能の多くが使用できます。

CLI のインストール方法、構成方法、および CLI コマンドを使用して Azure タスクを実行する方法については、次のリンクを参照してください。

* [Azure クラシック CLI のインストール][xplat-cli]
* [Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理][../../linux/create-ssh-secured-vm-from-template.md]
* [Azure Resource Manager での Mac、Linux、および Windows 用 Azure クラシック CLI の使用][xplat-cli-azure-resource-manager]

また、Azure CLI を使用して Azure Monitoring Extension for SAP をデプロイする方法については、in the [Deployment Guide][planning-guide]「[Linux VM 向け Azure CLI][deployment-guide-4.5.2]」の章を参照してください。

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Azure で SAP 用の VM をデプロイするためのさまざまな方法

この章では、Azure で VM をデプロイするためのさまざまな方法について学習します。 またこの章では、その他の準備手順や、Azure 内の VHD と VM の取り扱い方法についても説明します。

### <a name="deployment-of-vms-for-sap"></a>SAP 用 VM をデプロイする

Microsoft Azure では、VM および関連付けられているディスクをデプロイする方法が複数用意されています。 したがって、VM の準備はデプロイの方法によって異なる場合があるため、その違いを理解しておくことが重要です。 ここでは、一般的な例として次のシナリオについて見ていきます。

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する

オンプレミスから Azure に特定の SAP システムを移動することを計画します。 これは、OS、SAP バイナリ、DBMS バイナリを格納している VHD と、Azure への DBMS のデータおよびログ ファイルを格納している VHD をアップロードすることで行うことができます。 [以下の 2 番目のシナリオ][planning-guide-5.1.2]とは対照的に、ホスト名、SAP SID、および SAP ユーザー アカウントがオンプレミス環境で構成されているため、Azure VM でそれらを保持します。そのため、イメージを一般化する必要はありません。オンプレミスの準備手順と、一般化されていない VM や VHD を Azure にアップロードする方法については、このドキュメントの「オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備」の章を参照してください。 このようなイメージを Azure 上にデプロイするための詳細な手順については、「[シナリオ 3:SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する] ([deployment-guide-3.4])」([デプロイ ガイド][deployment-guide]) の章を参照してください。

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>顧客固有のイメージを使用する VM のデプロイ

OS または DBMS バージョンの固有のパッチ要件により、Azure Marketplace で提供されるイメージは、ニーズに適さない場合があります。 そのため、後で繰り返しデプロイできる、独自のプライベート OS/DBMS の VM イメージを使用して VM を作成する必要がある場合があります。 このようなプライベート イメージを複製用に準備するには、次の点について考慮する必要があります。

---
> ![Windows][Logo_Windows] Windows
>
> 詳細については、以下を参照してください。<https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed>Windows の設定 (Windows SID やホスト名など) は、sysprep コマンドを使用してオンプレミスの VM 上で抽象化または一般化する必要があります。
>
>
> ![Linux][Logo_Linux] Linux
>
> [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]、または [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle] に関する記事の手順に従って、Azure にアップロードする VHD を用意してください。
>
>

---
使用しているオンプレミス VM (特に 2 層システム) に SAP コンテンツを既にインストールしている場合、SAP Software Provisioning Manager でサポートされているインスタンス名の変更手順に従って、Azure VM のデプロイ後に SAP システムの設定を調整できます (SAP ノート [1619720])。 オンプレミスの準備手順と、一般化された VM を Azure にアップロードする方法については、このドキュメントの「[SAP 用の顧客固有のイメージを使用する VM のデプロイの準備][planning-guide-5.2.2]」and [Uploading a VHD from on-premises to Azure][planning-guide-5.3.2]の章を参照してください。 このようなイメージを Azure 上にデプロイするための詳細な手順については、「[シナリオ 2:SAP のカスタム イメージを使用して VM をデプロイする] ([deployment-guide-3.3])」([デプロイ ガイド][deployment-guide]) の章を参照してください。

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Azure Marketplace から VM をデプロイする

Microsoft またはサード パーティが提供する VM イメージを Azure Marketplace から取得して VM をデプロイします。 Azure で VM をデプロイした後は、オンプレミス環境の場合と同じガイドラインおよびツールに従って VM 内に SAP ソフトウェアや DBMS をインストールします。 詳細なデプロイの説明については、「[シナリオ 1:SAP 用 Azure Marketplace から VM をデプロイする][deployment-guide-3.2]」(in the [Deployment Guide][deployment-guide]) を参照してください。

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Azure 用の VM と SAP の準備

VM を Azure にアップロードする前に、VM と VHD が特定の要件を満たしていることを確認する必要があります。 使用するデプロイメント方法によって、多少の違いがあります。

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備

一般的なデプロイ方法は、SAP システムを実行している既存の VM を、オンプレミスから Azure に移動する方法です。 対象の VM と VM 内の SAP システムは、同じホスト名と、多くの場合、同じ SAP SID を使用して、Azure 内で実行される必要があります。 この場合は、VM のゲスト OS を複数のデプロイに対して一般化しないでください。 オンプレミスのネットワークが Azure に拡張された場合 (このドキュメントの「[クロスプレミス - オンプレミスのネットワークに完全に統合されることを要件とする 1 つまたは複数の SAP VM の Azure へのデプロイ][planning-guide-2.2]」の章を参照してください) には、以前にオンプレミスで使用されていたものと同じドメイン アカウントを、VM 内でも使用できます。

独自の Azure VM ディスクを準備する場合の要件は次のとおりです。

* 当初、オペレーティング システムを含んだ VHD には、最大で 127 GB のサイズしか設定できませんでした。 この制限は、2015 年 3 月末に解消されました。 現在、オペレーティング システムを含んだ VHD は、Azure Storage でホストされているその他の VHD と同様に、最大 1 TB のサイズにすることができます。
* 固定の VHD 形式である必要があります。 動的 VHD や、VHDx 形式の VHD は、Azure ではまだサポートされていません。 動的 VHD は、PowerShell コマンドレットや CLI を使用して VHD をアップロードする際に、静的 VHD に変換されます
* VM にマウントされ、Azure 内で再度 VM にマウントされる必要がある VHD も、固定 VHD 形式である必要があります。 データ ディスクのサイズ制限については、[こちら (Linux)](../../linux/managed-disks-overview.md) と[こちら (Windows)](../../windows/managed-disks-overview.md) の記事を参照してください。 動的 VHD は、PowerShell コマンドレットや CLI を使用して VHD をアップロードする際に、静的 VHD に変換されます
* 管理者特権を持つ別のローカル アカウントを追加します。VM がデプロイされ、より適切なユーザーが使用可能になるまで、このアカウントをマイクロソフトのサポートが使用したり、サービスやアプリケーションの実行コンテキストとして割り当てることができるようにします。
* 特定のデプロイメント シナリオで必要となる可能性があるアカウントとして、他のローカル アカウントを追加します。

---
> ![Windows][Logo_Windows] Windows
>
> このシナリオでは、Azure で VM をアップロードしてデプロイするために、VM の一般化 (sysprep) は必要ありません。
> ドライブ D:\ は使用しないでください。
> また、このドキュメントの「[アタッチされたディスクに対する自動マウントの設定][planning-guide-5.5.3]」の章に従って、アタッチされたディスクの自動マウントを設定してください。
>
> ![Linux][Logo_Linux] Linux
>
> このシナリオでは、Azure で VM をアップロードしてデプロイするために、VM の一般化 (waagent -deprovision) は必要ありません。
> /mnt/resource が使用されていないことと、すべてのディスクが uuid を使用してマウントされていることを確認してください。 OS ディスクについては、ブートローダー エントリにも、uuid ベースのマウントが反映されていることを確認してください。
>
>

---
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>SAP 用の顧客固有のイメージを使用する VM のデプロイの準備

一般化された OS が含まれている VHD ファイルは、Azure Storage アカウントのコンテナー内に格納されるか、管理ディスク イメージとして格納されます。 このようなイメージからの新しい VM のデプロイは、デプロイ テンプレート ファイル内のソースとして VHD またはマネージド ディスク イメージを参照することで実行できます (「[シナリオ 2:SAP のカスタム イメージを使用して VM をデプロイする][deployment-guide-3.3]」(of the [Deployment Guide][deployment-guide]) を参照)。

独自の Azure VM イメージを準備する場合の要件は次のとおりです。

* 当初、オペレーティング システムを含んだ VHD には、最大で 127 GB のサイズしか設定できませんでした。 この制限は、2015 年 3 月末に解消されました。 現在、オペレーティング システムを含んだ VHD は、Azure Storage でホストされているその他の VHD と同様に、最大 1 TB のサイズにすることができます。
* 固定の VHD 形式である必要があります。 動的 VHD や、VHDx 形式の VHD は、Azure ではまだサポートされていません。 動的 VHD は、PowerShell コマンドレットや CLI を使用して VHD をアップロードする際に、静的 VHD に変換されます
* VM にマウントされ、Azure 内で再度 VM にマウントされる必要がある VHD も、固定 VHD 形式である必要があります。 データ ディスクのサイズ制限については、[こちら (Linux)](../../windows/managed-disks-overview.md) と[こちら (Windows)](../../linux/managed-disks-overview.md) の記事を参照してください。 動的 VHD は、PowerShell コマンドレットや CLI を使用して VHD をアップロードする際に、静的 VHD に変換されます
* 特定のデプロイメント シナリオで必要となる可能性があるアカウントとして、他のローカル アカウントを追加します。
* イメージに SAP NetWeaver のインストールが含まれていて、Azure のデプロイメント時にホスト名が元の名前から変更される可能性が高い場合には、SAP Software Provisioning Manager DVD の最新バージョンをテンプレート内にコピーすることをお勧めします。 そうすることで、新しいコピーが起動された後すぐに、デプロイされた VM イメージ内で、SAP の名前変更機能を簡単に使用して、変更されたホスト名を調整したり、SAP システムの SID を変更することができます。

---
> ![Windows][Logo_Windows] Windows
>
> このドキュメントの「[アタッチされたディスクに対する自動マウントの設定][planning-guide-5.5.3]」の章で説明しているように、D:\ ドライブが、アタッチされたディスクを自動マウントするように設定されていないことを確認してください。
>
> ![Linux][Logo_Linux] Linux
>
> /mnt/resource が使用されていないことと、すべてのディスクが uuid を使用してマウントされていることを確認してください。 OS ディスクについては、ブートローダー エントリにも、uuid ベースのマウントが反映されていることを確認してください。
>
>

---
* SAP GUI (管理用または設定用) は、このようなテンプレートに事前インストールできます。
* クロスプレミス シナリオで VM を正常に実行するために必要なその他のソフトウェアは、そのソフトウェアが VM の名前変更と連携できる限り、インストール可能です。

VM を一般化して、対象の Azure デプロイメント シナリオでは使用できないアカウント/ユーザーへの依存性を最終的に解消する準備が十分にできている場合は、このようなイメージを一般化するための最後の準備手順が実行されます。

##### <a name="generalizing-a-vm"></a>VM の一般化
---
> ![Windows][Logo_Windows] Windows
>
> 最後の手順は、管理者アカウントを使用して VM にサインインすることです。 *管理者*として Windows コマンド ウィンドウを開きます。 %windir%\windows\system32\sysprep に移動し、sysprep.exe を実行します。
> 小さいウィンドウが表示されます。 **[一般化する]** オプション (既定値はオフ) をオンにし、シャットダウン オプションを既定の '再起動' から 'シャットダウン' に変更することが重要です。 この手順では、sysprep プロセスが VM のゲスト OS でオンプレミスで実行されることを前提としています。
> Azure で既に実行されている VM で手順を実行する場合は、[こちらの記事](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)に記載されている手順に従ってください。
>
> ![Linux][Logo_Linux] Linux
>
> [Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>オンプレミスと Azure 間での VM と VHD の転送
Azure Portal では、VM イメージとディスクを Azure にアップロードすることはできないので、Azure PowerShell コマンドレットまたは CLI を使用する必要があります。 また、'AzCopy'.ツールを使用する方法もあります。 このツールでは、オンプレミスと Azure の間で VHD を (双方向に) コピーできます。 また、Azure リージョン間で VHD をコピーすることもできます。 AzCopy のダウンロードと使用方法については、[こちらのドキュメント][storage-use-azcopy]を参照してください。

3 番目の方法は、サード パーティ製のさまざま GUI 指向ツールを使用する方法です。 ただし、それらのツールで Azure ページ BLOB がサポートされていることを確認してください。 ここでは、Azure ページ BLOB ストアを使用する必要があります (相違点についてはここ <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs> で説明されています)。 また、Azure で提供されるツールは、アップロードする必要がある、VM と VHD を圧縮するうえで効率的です。 この圧縮効率によってアップロード時間を減らすことができるので、このことは重要です (アップロード時間は、オンプレミス施設からインターネットまでのアップロード リンクと、対象となる Azure デプロイメント リージョンによって異なります) 。 通常、ヨーロッパの場所から米国の Azure データ センターに VM や VHD をアップロードする場合、同じ VM や VHD をヨーロッパの Azure データ センターにアップロードするよりも、長い時間がかかります。

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>オンプレミスから Azure への VHD のアップロード
オンプレミス ネットワークから既存の VM や VHD をアップロードするには、それらの VM や VHD が、このドキュメントの「[オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備][planning-guide-5.2.1]」の章に記載されている要件を満たしている必要があります。

このような VM は、一般化する必要がなく、オンプレミス側でシャット ダウンした後、そのままの状態でアップロードできます。 オペレーティング システムを含まない、追加の VHD についても同じことが言えます。

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>VHD をアップロードして Azure ディスクにする
ここでは、VHD を OS がある状態とない状態でアップロードした後、それをデータ ディスクとして VM にマウントするか、または OS ディスクとして使用します。 これはマルチステップのプロセスです

**Powershell**

* *Connect-AzAccount* を使用してサブスクリプションにサインインする
* *Set-AzContext* および SubscriptionId または SubscriptionName パラメーターを使用してコンテキストのサブスクリプションを設定する - <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext> を参照してください
* *Add-AzVhd* を使用して VHD を Microsoft Azure Storage アカウントにアップロードする - <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd> を参照してください
* (省略可能) *New-AzDisk* を使用して VHD からマネージド ディスクを作成する - <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk> を参照してください
* *Set-AzVMOSDisk* を使用して新しい VM 構成の OS ディスクを VHD またはマネージド ディスクに設定する - <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk> を参照してください
* *New-AzVM* を使用して VM 構成から新しい VM を作成する - <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm> を参照してください
* *Add-AzVMDataDisk* を使用して新しい VM にデータ ディスクを追加する - <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk> を参照してください

**Azure CLI**

* *az login* を使用してサブスクリプションにサインインする
* *az account set --subscription `<subscription name or id`>* を使用してサブスクリプションを選択する
* *az storage blob upload* を使用して VHD をアップロードする - 「[Azure Storage での Azure CLI の使用][storage-azure-cli]」を参照
* (省略可能) *az disk create* を使用して VHD から管理ディスクを作成 - https://docs.microsoft.com/cli/azure/disk を参照
* *az vm create* とパラメーター *--attach-os-disk* を使用して、アップロードした VHD または管理ディスクを OS ディスクとして指定して新しい VM を作成する
* *az vm disk attach* とパラメーター *--new* を使用してデータ ディスクを新しい VM に追加する

**テンプレート**

* Powershell または Azure CLI で VHD をアップロード
* (省略可能) PowerShell、Azure CLI、または Azure portal を使用して VHD からマネージド ディスクを作成する
* [この JSON テンプレート サンプル](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json)のように VHD を参照する JSON テンプレートを使用するか、[この JSON テンプレート サンプル](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)のように管理ディスクを使用して、VM を作成します。

#### <a name="deployment-of-a-vm-image"></a>VM イメージのデプロイメント
オンプレミス ネットワークから既存の VM または VHD をアップロードし、それを Azure VM イメージとして使用するには、それらの VM や VHD が、このドキュメントの「[SAP 用の顧客固有のイメージを使用する VM のデプロイの準備][planning-guide-5.2.2]」の章にリストされている要件を満たしている必要があります。

* Windows で *sysprep* または Linux で *waagent -deprovision* を使用して VM を汎用化 - Windows の場合:「[Sysprep テクニカル リファレンス](https://technet.microsoft.com/library/cc766049.aspx)」、Linux の場合:「[Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法][capture-image-linux-step-2-create-vm-image]」を参照
* *Connect-AzAccount* を使用してサブスクリプションにサインインする
* *Set-AzContext* および SubscriptionId または SubscriptionName パラメーターを使用してコンテキストのサブスクリプションを設定する - <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext> を参照してください
* *Add-AzVhd* を使用して VHD を Microsoft Azure Storage アカウントにアップロードする - <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd> を参照してください
* (省略可能) *New-AzImage* を使用して VHD からマネージド ディスク イメージを作成する - <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage> を参照してください
* 新しい VM config の OS ディスクを以下に設定する
  * *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* を使用した VHD - <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk> を参照してください
  * マネージド ディスク イメージ *Set-AzVMSourceImage* - <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage> を参照してください
* *New-AzVM* を使用して VM 構成から新しい VM を作成する - <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm> を参照してください

**Azure CLI**

* Windows で *sysprep* または Linux で *waagent -deprovision* を使用して VM を汎用化 - Windows の場合:「[Sysprep テクニカル リファレンス](https://technet.microsoft.com/library/cc766049.aspx)」、Linux の場合:「[Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法][capture-image-linux-step-2-create-vm-image]」を参照
* *az login* を使用してサブスクリプションにサインインする
* *az account set --subscription `<subscription name or id`>* を使用してサブスクリプションを選択する
* *az storage blob upload* を使用して VHD をアップロードする - 「[Azure Storage での Azure CLI の使用][storage-azure-cli]」を参照
* (省略可能) *az image create* を使用して VHD から管理ディスク イメージを作成する - https://docs.microsoft.com/cli/azure/image を参照
* *az vm create* とパラメーター *--image* を使用して、アップロードした VHD または管理ディスク イメージを OS ディスクとして指定して新しい VM を作成する

**テンプレート**

* Windows で *sysprep* または Linux で *waagent -deprovision* を使用して VM を汎用化 - Windows の場合:「[Sysprep テクニカル リファレンス](https://technet.microsoft.com/library/cc766049.aspx)」、Linux の場合:「[Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法][capture-image-linux-step-2-create-vm-image]」を参照
* Powershell または Azure CLI で VHD をアップロード
* (省略可能) PowerShell、Azure CLI、または Azure portal を使用して VHD からマネージド ディスク イメージを作成する
* [この JSON テンプレート サンプル](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json)のようにイメージ VHD を参照する JSON テンプレートを使用するか、[この JSON テンプレート サンプル](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)のように管理ディスク イメージを使用して、VM を作成します。

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>VHD または管理ディスクをオンプレミスにダウンロードする
Azure のサービスとしてのインフラストラクチャは、VHD および SAP システムをアップロードできるだけではありません。 SAP システムを Azure からオンプレミスに移動し直すこともできます。

ダウンロード時に VHD または Managed Disks をアクティブにすることはできません。 VM にマウントされているディスクをダウンロードする場合でも、VM をシャットダウンし、割り当てを解除する必要があります。 新しいシステムをオンプレミスでセットアップするのに必要になるデータベース コンテンツのダウンロードのみを行う必要がある場合、またダウンロード時と新しいシステムのセットアップ時に Azure 内のシステムを引き続き稼働させることができる場合、ディスクへの圧縮されたデータベース バックアップを実行することで長時間のダウンタイムを回避し、OS ベース VM もダウンロードするのではなく、単にそのディスクをダウンロードできます。

#### <a name="powershell"></a>Powershell

* 管理ディスクをダウンロードする  
  まず管理ディスクの基になる BLOB へのアクセス権を取得する必要があります。 アクセス権を取得すると、基になる BLOB を新しいストレージ アカウントにコピーし、このストレージ アカウントから BLOB をダウンロードできるようになります。

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* VHD をダウンロードする  
  SAP システムが停止し、VM がシャットダウンされたら、オンプレミスのターゲット上で PowerShell コマンドレット Save-AzVhd を使用して、VHD ディスクをオンプレミス環境にダウンロードできます。 これを行うには、VHD の URL が必要です。これは Azure portal の "ストレージ セクション" にあります (Storage アカウントと、VHD が作成されたストレージ コンテナーに移動する必要があります)。また、VHD のコピー先を把握しておく必要があります。

  次に、コマンドを使って、ダウンロード用の VHD の URL としてパラメーター SourceUri を定義して、VHD の物理的な場所 (その名前を含む) として LocalFilePath を定義できます。 コマンドは次のようになります。

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Save-AzVhd コマンドレットの詳細については、こちら (<https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>) を確認してください。

#### <a name="azure-cli"></a>Azure CLI
* 管理ディスクをダウンロードする  
  まず管理ディスクの基になる BLOB へのアクセス権を取得する必要があります。 アクセス権を取得すると、基になる BLOB を新しいストレージ アカウントにコピーし、このストレージ アカウントから BLOB をダウンロードできるようになります。
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* VHD をダウンロードする   
  SAP システムが停止し、VM がシャットダウンされたら、オンプレミスのターゲット上で Azure CLI コマンド _azure storage blob download_ を使用して、VHD ディスクをオンプレミスにダウンロードできます。 これを行うには VHD の名前とコンテナーが必要です。これは Azure portal の "ストレージ セクション" にあります (Storage アカウントと、VHD が作成されたストレージ コンテナーに移動する必要があります)。また、VHD のコピー先を把握しておく必要があります。

  次に、コマンドを使って、ダウンロード用の VHD のパラメーターの BLOB とコンテナーを定義し、VHD の物理的なターゲット場所 (その名前を含む) として宛先を定義できます。 コマンドは次のようになります。

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Azure 内での VM とディスクを転送する

#### <a name="copying-sap-systems-within-azure"></a>Azure 内での SAP システムのコピー

SAP システム (または SAP アプリケーション層をサポートする専用の DBMS サーバーでも) は、通常、バイナリがある OS または SAP データベースのデータとログ ファイルのいずれかを含む、複数のディスクで構成されます。 ディスクをコピーする Azure 機能と、ディスクをローカル ディスクに保存する Azure 機能のどちらにも、一貫した方法で複数のディスクをスナップショットする同期メカニズムはありません。 そのため、コピーまたは保存されたディスクの状態 (これらが同じ VM にマウントされている場合でも) が異なる場合があります。 これは、別々のディスクにさまざまなデータやログファイルが含まれている場合に、最終的にデータベースに不整合が発生することを意味します。

**結論:SAP システム構成の一部であるディスクのコピーまたは保存を実行するためには、SAP システムを停止する必要があります。さらに、デプロイされた VM をシャットダウンする必要があります。その後で、一連のディスクをコピーまたはダウンロードして、Azure またはオンプレミスの SAP システムのコピーを作成します。**

データ ディスクは Azure Storage アカウントに VHD ファイルとして格納され、仮想マシンに直接アタッチするか、イメージとして使用することができます。 この場合、VHD が、仮想マシンにアタッチされる前に別の場所にコピーされます。 Azure 内の VHD ファイルの完全名は、Azure 内で一意である必要があります。 既に説明したように、名前は次のように 3 つの部分で構成されます。

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

データ ディスクは管理ディスクにすることもできます。 この場合、管理ディスクは、仮想マシンにアタッチされる前に新しい管理ディスクを作成するために使用されます。 管理ディスクの名前はリソース グループ内で一意にする必要があります。

##### <a name="powershell"></a>Powershell

Azure PowerShell コマンドレットを使用して、[この記事][storage-powershell-guide-full-copy-vhd]に示されているように VHD をコピーします。 新しいマネージド ディスクを作成するには、次の例のように New-AzDiskConfig と New-AzDisk を使用します。

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して、[この記事][storage-azure-cli-copy-blobs]に示されているように VHD をコピーします。 管理ディスクを作成するには、次の例のように *az disk create* を使用します。

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage ツール

* <https://storageexplorer.com/>

次の場所に、Azure Storage エクスプローラーのプロフェッショナル エディションが用意されています。

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

ストレージ アカウント内の VHD 自体のコピーは、数秒しかかからないプロセスです (遅延コピーやコピー オン ライトを使用したスナップショットを作成する SAN ハードウェアと同様)。 VHD ファイルのコピーを入手したら、それを仮想マシンにアタッチするか、イメージとして使用して、VHD のコピーを仮想マシンにアタッチできます。

##### <a name="powershell"></a>Powershell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```
##### <a name="azure-cli"></a>Azure CLI

```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Azure Storage アカウント間でのディスクのコピー
このタスクは、Azure Portal で行うことはできません。 Azure PowerShell コマンドレット、Azure CLI、またはサードパーティのストレージ ブラウザーを使用することができます。 複数の Storage アカウント、また Azure サブスクリプション内のリージョンにまたがって非同期的に BLOB をコピーするための機能を持つ PowerShell コマンドレットまたは CLI コマンドにより、BLOB を作成および管理できます。

##### <a name="powershell"></a>Powershell
サブスクリプション間で VHD をコピーすることもできます。 詳細については、[こちらの記事][storage-powershell-guide-full-copy-vhd]をご覧ください。

PS コマンドレット ロジックの基本的な流れは次のようになります。

* *New-AzStorageContext* を使用して**ソース** ストレージ アカウントのストレージ アカウント コンテキストを作成する - <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext> を参照してください
* *New-AzStorageContext* を使用して**ターゲット** ストレージ アカウントのストレージ アカウント コンテキストを作成する - <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext> を参照してください
* 以下を使用してコピーを開始します

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* 以下を使用して、ループ内のコピーの状態を確認します。

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* 前述のように、仮想マシンに新しい VHD をアタッチします。

例については、[こちらの記事][storage-powershell-guide-full-copy-vhd]を参照してください。

##### <a name="azure-cli"></a>Azure CLI
* 以下を使用してコピーを開始します

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* 以下を使用して、ループ内にコピーがまだ存在するかどうか、状態を確認します。

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* 前述のように、仮想マシンに新しい VHD をアタッチします。

例については、[こちらの記事][storage-azure-cli-copy-blobs]を参照してください。

### <a name="disk-handling"></a>ディスクの扱い

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>SAP デプロイ用の VM/ディスク構造

VM とそれに関連付けられたディスクの構造は、簡単に操作できることが理想的です。 オンプレミスのインストールでは、お客様は、サーバー インストールを構成するさまざまな方法を開発してきました。

* OS および DBMS または SAP のすべてのバイナリを含む、1 つのベース ディスク。 2015 年 3 月以降、このディスクは、127 GB という以前の制限ではなく、最大 1 TB (テラバイト) のサイズを実現しています。
* 1 つまたは複数のディスク。SAP データベースの DBMS ログ ファイルと、DBMS 一時ストレージ領域 (DBMS でサポートされる場合) のログ ファイルが含まれます。 データベース ログ IOPS の要件が高い場合は、必要な IOPS ボリュームを確保するために複数のディスクをストライプする必要があります。
* SAP データベースの 1 つまたは 2 つのデータベース ファイルと、DBMS 一時データ ファイル (DBMS でサポートされる場合) を含む複数のディスク。

![Reference Configuration of Azure IaaS VM for SAP (SAP 用 Azure IaaS VM のリファレンス構成)][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> 多くのお客様が、たとえば、OS がインストールされた c:\ drive に SAP および DBMS バイナリがインストールされていない構成を採用していました。 これにはさまざまな理由がありましたが、根本原因に戻ると、通常、ドライブが小さく、OS のアップグレードで追加の領域が必要でした。これは、10 ～ 15 年前のことです。 最近では、これらの両方の状況はもはやあてはまりません。 現在は、大量のディスクまたは VM 上で c:\ drive をマップできます。 構造上、デプロイメントをシンプルにするために、Azure での SAP NetWeaver システムのデプロイ パターンに従うことをお勧めします。
>
> Windows オペレーティング システムのページファイルは、D: ドライブ (非永続的ディスク) 上にある必要があります。
>
> ![Linux][Logo_Linux] Linux
>
> [こちらの記事][virtual-machines-linux-agent-user-guide]に記載された説明のように、Linux swapfile を Linux 上の /mnt /mnt/resource に配置します。 スワップ ファイルは、Linux エージェント /etc/waagent.conf の構成ファイルで構成できます。 次の設定を追加または変更します。
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

変更をアクティブ化するには、以下を使用して、Linux エージェントを再起動します。

```
sudo service waagent restart
```

推奨されるスワップ ファイルのサイズについては、SAP ノート [1597355] をお読みください。

---
DBMS データ ファイルに使用されるディスクの数とでこれらのディスクがホストされる Azure Storage の種類は、IOPS 要件と必要な待機時間により決まります。 正確なクォータについては、[こちらの記事 (Linux)][virtual-machines-sizes-linux] and [this article (Windows)][virtual-machines-sizes-windows] を参照してください。

過去 2 年間にわたる SAP デプロイの経験から、次の教訓が得られました。

* 異なるデータ ファイルへの IOPS トラフィックは常に同じではありません。これは、既存のお客様のシステムにその SAP データベースを表す異なるサイズのデータ ファイルがある場合があるためです。 結果として、複数のディスクに RAID 構成を使用して、これらから分割したデータ ファイル LUN を配置するのがより適切であることがわかりました。 特に Azure Standard Storage では、IOPS レートが DBMS トランザクション ログに対して単一のディスクのクォータの上限に達する場合がありました。 このようなシナリオでは、Premium Storage を使用することをお勧めします。あるいは、ソフトウェア ストライプで複数の Standard Storage ディスクを集計します。

---
> ![Windows][Logo_Windows] Windows
>
> * [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Linux でのソフトウェア RAID の構成][virtual-machines-linux-configure-raid]
> * [Azure で Linux VM の LVM を構成する][virtual-machines-linux-configure-lvm]
> * [Azure Storage の秘密と Linux の I/O 最適化](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* Premium Storage では、パフォーマンスの大幅な向上が見られます。特に、重要なトランザクション ログの書き込みでのパフォーマンス向上が顕著です。 運用と同様のパフォーマンスの実現が期待される SAP のシナリオの場合は、Azure Premium Storage の機能を活用できる VM シリーズの使用を強くお勧めします。

OS と、推奨する SAP のバイナリおよびデータベース (基本 VM) とを含むディスクは、上限が 127 GB ではなくなったことに留意してください。 現在は 1 TB が最大サイズです。 SAP バッチ ジョブのログを含め、必要なすべてのファイルを保持するのに十分な領域があります。

その他のヒントと詳細情報については (特に DBMS VM について)、[DBMS デプロイ ガイド][dbms-guide]をご覧ください

#### <a name="disk-handling"></a>ディスクの扱い

ほとんどのシナリオでは、VM に SAP データベースをデプロイするために追加のディスクを作成する必要があります。 ディスクの数については、このドキュメントの「[SAP デプロイ用の VM/ディスク構造][planning-guide-5.5.1]」の章で説明しました。 基本 VM をデプロイした後、Azure Portal でディスクを接続または切断することができます。 ディスクの接続/切断は、VM が停止しているときだけでなく動作中にも行うことができます。 ディスクを接続するときは、空のディスクを接続するか、この時点で別の VM に接続されていない既存のディスクを接続するかを Azure portal で選択できます。

**メモ**:ディスクは一度に 1 つの VM だけに接続できます。

![Attach / detach disks with Azure Standard Storage (Azure Standard Storage によるディスクの接続/切断)][planning-guide-figure-1400]

新しい仮想マシンのデプロイ時に、Managed Disks を使用するか、ディスクを Azure Storage アカウントに格納するかを選択できます。 Premium Storage を使用する場合は、Managed Disks を使用することをお勧めします。

次に、新規の空のディスクを作成するか、以前にアップロードした既存のディスクを選択して VM に今アタッチするするかを決める必要があります。

**重要**:Azure Standard Storage ではホスト キャッシュを使用**しない**でください。 ホスト キャッシュの設定は既定の [なし] のままにしてください。 Azure Premium Storage では、I/O 特性が主に読み取り (データベースのデータ ファイルに対する一般的な I/O トラフィックなど) である場合は、読み取りキャッシュを有効にする必要があります。 データベース トランザクションのログ ファイルについては、キャッシュなしをお勧めします。

---
> ![Windows][Logo_Windows] Windows
>
> [Azure portal でデータ ディスクを接続する方法][virtual-machines-linux-attach-disk-portal]
>
> ディスクがアタッチされている場合は、VM にサインインして、Windows ディスク マネージャーを開きます。 「[アタッチされたディスクに対する自動マウントの設定][planning-guide-5.5.3]」の章で推奨されているように自動マウントが有効になっていない場合は、新しく接続されたボリュームはオンラインで初期化する必要があります。
>
> ![Linux][Logo_Linux] Linux
>
> ディスクが接続されている場合は、[こちらの記事][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]で説明されているように、VM にサインインしてディスクを初期化する必要があります。
>
>

---
新しいディスクが空のディスクの場合は、ディスクをフォーマットする必要もあります。 フォーマットについて、特に DBMS のデータ ファイルとログ ファイルに対しては、DBMS のベアメタル デプロイと同じ推奨事項が適用されます。

「[Microsoft Azure Virtual Machine の概念][planning-guide-3.2]」の章で既に説明されているように、, an Azure Storage account does not provide infinite resources in terms of I/O volume, IOPS, and data volume. Usually DBMS VMs are most affected by this. It might be best to use a separate Storage Account for each VM if you have few high I/O volume VMs to deploy in order to stay within the limit of the Azure Storage Account volume. Otherwise, you need to see how you can balance these VMs between different Storage accounts without hitting the limit of each single Storage Account. More details are discussed in the [DBMS Deployment Guide][dbms-guide]。 追加の VHD が必要になる可能性のある、純粋な SAP アプリケーション サーバー VM やその他の VM についても、これらの制限事項を覚えておく必要があります。 管理ディスクを使用する場合、これらの制限事項は適用されません。 Premium Storage を使用する場合は、管理ディスクを使用することをお勧めします。

Storage アカウントに関連するもう 1 つのトピックは、Storage アカウント内の VHD が Geo レプリケートされているかどうかということです。 Geo レプリケーションは、VM レベルではなくストレージ アカウント レベルで有効または無効に設定します。 Geo レプリケーションを有効にすると、ストレージ アカウント内の VHD が、同じリージョン内の別の Azure データ センターにレプリケートされます。 これについて決定する前に、次の制限事項について検討する必要があります。

Azure Geo レプリケーションは、VM 内の各 VHD においてローカルで機能し、VM 内の複数の VHD の間で時系列順に IO をレプリケートすることはありません。 そのため、基本 VM を表す VHD だけでなく、VM に接続されている追加の VHD は互いに独立してレプリケートされます。 つまり、異なる VHD での変更は同期されません。 IO が作成された順序で個別にレプリケートされるという事実は、Geo レプリケーションが複数の VHD に分散されているデータベースを持つデータベース サーバーに対しては価値がないことを意味しています。 DBMS に加えて、プロセスによって異なる VHD にデータが書き込まれたり操作されたりするアプリケーションや、変更の順番を維持することが重要なアプリケーションが他にある可能性もあります。 それが要件である場合は、Azure の Geo レプリケーションを有効にしないでください。 一連の VM に対して Geo レプリケーションが必要かどうかによって（ただし他のセットに対してではなく）、Geo レプリケーションが有効または無効な異なるストレージ アカウントに VM および関連する VHD を分類することができます。

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>アタッチされたディスクに対する自動マウントの設定
---
> ![Windows][Logo_Windows] Windows
>
> 独自のイメージまたはディスクから作成された VM の場合、自動マウント パラメーターを確認、また場合によっては設定する必要があります。 このパラメーターを設定することにより、VM は Azure での再起動または再デプロイ後、再アタッチ/マウントされたドライブを自動的に再マウントできるようになります。
> パラメーターは、Azure Marketplace で Microsoft によって提供されるイメージに設定されます。
>
> 自動マウントを設定する場合は、次のコマンド ライン実行可能ファイル diskpart.exe のドキュメントを参照してください。
>
> * [DiskPart コマンド ライン オプション](https://technet.microsoft.com/library/bb490893.aspx)
> * [自動マウント](https://technet.microsoft.com/library/cc753703.aspx)
>
> Windows コマンドライン ウィンドウは、管理者として開く必要があります。
>
> ディスクがアタッチされている場合は、VM にサインインして、Windows ディスク マネージャーを開きます。 「[アタッチされたディスクに対する自動マウントの設定][planning-guide-5.5.3]」の章で推奨されているように自動マウントが有効になっていない場合は、新しく接続されたボリュームはオンラインで初期化する必要があります。
>
> ![Linux][Logo_Linux] Linux
>
> [こちらの記事][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]で説明されているように、新しく接続した空のディスクを初期化する必要があります。
> また、新しいディスクを /etc/fstab に追加する必要があります。
>
>

---
### <a name="final-deployment"></a>最終デプロイメント

最終デプロイと正確な手順、特に SAP Extended Monitoring のデプロイについては、[デプロイ ガイド][deployment-guide]を参照してください。

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Azure VM 内で実行される SAP システムへのアクセス

SAP GUI を使用してパブリック インターネット経由でこれらの SAP システムに接続するシナリオの場合は、次の手順のようにする必要があります。

ドキュメントの後半で、サイト間接続 (VPN トンネル) またはオンプレミス システムと Azure システム間の Azure ExpressRoute 接続のある、クロスプレミス デプロイで SAP システムに接続するなど、その他の主要なシナリオについて説明します。

### <a name="remote-access-to-sap-systems"></a>SAP システムへのリモート アクセス

Azure Resource Manager の場合、以前のクラシック モデルのように既定のエンドポイントはありません。 Azure Resource Manager VM のすべてのポートは、次の条件が満たされている場合には開いています。

1. サブネットまたはネットワーク インターフェイスに対してネットワーク セキュリティ グループが定義されていない。 Azure VM へのネットワーク トラフィックは、いわゆる「ネットワーク セキュリティ グループ」を通じて保護できます。 詳細については、「 [ネットワーク セキュリティ グループ (NSG) について][virtual-networks-nsg]
2. ネットワーク インターフェイスに対して Azure Load Balancer が定義されていない   

[こちらの記事][virtual-machines-azure-resource-manager-architecture]で説明されている、クラシック モデルと ARM のアーキテクチャの違いを参照してください。

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>インターネット経由での SAP システムと SAP GUI 接続の構成

このトピックについて詳細に説明したこの記事 (<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>) を参照してください。

#### <a name="changing-firewall-settings-within-vm"></a>VM 内のファイアウォール設定の変更

SAP システムへの受信トラフィックを許可するには、仮想マシンでファイアウォールを構成する必要がある場合があります。

---
> ![Windows][Logo_Windows] Windows
>
> 既定では、Azure によってデプロイされた VM 内の Windows Firewall は有効になっています。 次に SAP ポートを開くことを許可する必要があります。そうしないと SAP GUI は接続することができません。
> これを行うには、次の手順を実行します。
>
> * [コントロール パネル]、[システムとセキュリティ]、[Windows ファイアウォール] を開いて、 **[詳細設定]** に移動します。
> * [受信の規則] を右クリックし、 **[新しい規則]** を選択します。
> * 次のウィザードで新しい **[ポート]** のルールを作成するよう選択します。
> * ウィザードの次の手順で、設定を TCP のままにして開きたいポート番号を入力します。 SAP インスタンス ID は 00 なので、3200 を取得しました。 インスタンスが異なるインスタンス番号を持つ場合は、そのインスタンス番号に基づいて以前に定義したポートを開く必要があります。
> * ウィザードの次の部分では、 **[接続を許可する]** チェック ボックスをオンのままにしておく必要があります。
> * ウィザードの次の手順では、[ドメイン]、[プライベート]、および [パブリック] のネットワークに対して規則を適用するかどうかを定義する必要があります。 必要に応じて調整してください。 ただし、パブリック ネットワークを通じて外部から SAP GUI に接続するには、パブリック ネットワークに規則を適用する必要があります。
> * ウィザードの最後の手順で規則に名前を付け、 **[完了]** を押して保存します。
>
> この規則はすぐに有効になります。
>
> ![ポート規則の定義][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> 既定では、Azure Marketplace の Linux イメージは iptables ファイアウォールを有効にしないので、SAP システムへの接続は動作します。 iptables または他のファイアウォールを有効にした場合は、iptables または使用しているファイアウォールのドキュメントを参照して、ポート 32xx (この xx はご利用の SAP システムのシステム番号です) への受信 TCP トラフィックを許可してください。
>
>

---
#### <a name="security-recommendations"></a>セキュリティに関する推奨事項

SAP GUI は、実行しているどの SAP インスタンス (ポート 32xx) にもすぐには接続しませんが、SAP メッセージ サーバー プロセス (ポート 36xx) に対して開いているポートを通じて最初に接続します。 以前は、同じポートが、メッセージ サーバーによってアプリケーション インスタンスへの内部通信用に使用されていました。 オンプレミスのアプリケーション サーバーが Azure のメッセージ サーバーと誤って通信するのを防ぐため、内部通信ポートは変更することができます。 SAP メッセージ サーバーとそのアプリケーション インスタンス間の内部通信を、プロジェクト テスト用の開発環境のクローンなどのオンプレミス システムから複製されたシステムの別のポート番号に変更することをお勧めします。これは、次の既定のプロファイル パラメーターで行うことができます。

> rdisp/msserv_internal
>
>

「[Security Settings for the SAP Message Server (SAP Message Server のセキュリティ設定)](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)」を参照してください


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>単一の VM と SAP NetWeaver のデモ/トレーニング シナリオ

![Azure Cloud Services 内の分離された、単一の VM SAP Demo システムを同じ VM 名で実行する][planning-guide-figure-1700]

このシナリオでは、単一の VM 内に完全なトレーニング/デモ シナリオが含まれる、標準的なトレーニング/デモ システムのシナリオを実装します。 デプロイが VM イメージ テンプレートによって実行されることを想定しています。 また、これらのデモ/トレーニングのうちの複数が、同じ名前を持つ VM でデプロイされる必要があることを想定しています。 トレーニング システム全体は、オンプレミスの資産に接続されていません。これは、ハイブリッド デプロイとは逆です。

このドキュメントの「[Azure 用の VM と SAP の準備][planning-guide-5.2]」の章のいくつかのセクションで説明しているとおりに VM イメージを作成したことが前提です。

シナリオを実装するための一連のイベントは次のとおりです。

##### <a name="powershell"></a>Powershell

* すべてのトレーニング/デモ ランドスケープの新しいリソース グループを作成します

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* Managed Disks を使用しない場合に新しいストレージ アカウントを作成します

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* すべてのトレーニング/デモ ランドスケープの新しい仮想ネットワークを作成して、同じホスト名と IP アドレスの使用を有効にします。 仮想ネットワークは、ポート 3389 へのトラフィックのみを許可して、SSH 向けにリモート デスクトップ アクセスとポート 22 を有効にするネットワーク セキュリティ グループによって保護されます。

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* インターネットから仮想マシンへのアクセスに使用できる新しいパブリック IP アドレスを作成します

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* 仮想マシン用の新しいネットワーク インターフェイスを作成します

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* 仮想マシンを作成します。 このシナリオでは、すべての VM が同じ名前になります。 これらの VM 内の SAP NetWeaver インスタンスの SAP SID も同じになります。 Azure リソース グループ内では VM の名前は一意である必要がありますが、別の Azure リソース グループで同じ名前の VM を実行できます。 既定の Windows の "管理者" アカウントまたは Linux の "ルート" は有効ではありません。 そのため、新しい管理者のユーザー名をパスワードと共に定義する必要があります。 また、VM のサイズも定義する必要があります。

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* 必要に応じて、ディスクを追加し、必要なコンテンツを復元します。 すべての BLOB 名 (BLOB への URL) は Azure 内で一意である必要があります。

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

次のコード例を Linux で使用できます。 Windows の場合、前の説明に従って PowerShell を使用するか、例を使って $rgName ではなく %rgName% を使用して、Windows コマンド *set* を使って環境変数を設定します。

* すべてのトレーニング/デモ ランドスケープの新しいリソース グループを作成します

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* 新しいストレージ アカウントの作成

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* すべてのトレーニング/デモ ランドスケープの新しい仮想ネットワークを作成して、同じホスト名と IP アドレスの使用を有効にします。 仮想ネットワークは、ポート 3389 へのトラフィックのみを許可して、SSH 向けにリモート デスクトップ アクセスとポート 22 を有効にするネットワーク セキュリティ グループによって保護されます。

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* インターネットから仮想マシンへのアクセスに使用できる新しいパブリック IP アドレスを作成します

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* 仮想マシン用の新しいネットワーク インターフェイスを作成します

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* 仮想マシンを作成します。 このシナリオでは、すべての VM が同じ名前になります。 これらの VM 内の SAP NetWeaver インスタンスの SAP SID も同じになります。 Azure リソース グループ内では VM の名前は一意である必要がありますが、別の Azure リソース グループで同じ名前の VM を実行できます。 既定の Windows の "管理者" アカウントまたは Linux の "ルート" は有効ではありません。 そのため、新しい管理者のユーザー名をパスワードと共に定義する必要があります。 また、VM のサイズも定義する必要があります。

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* 必要に応じて、ディスクを追加し、必要なコンテンツを復元します。 すべての BLOB 名 (BLOB への URL) は Azure 内で一意である必要があります。

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Template

GitHub 上の azure-quickstart-templates リポジトリのサンプル テンプレートを使用できます。

* [Simple Linux VM (シンプルな Linux VM)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Simple Linux VM (シンプルな Windows VM)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM from image (イメージからの VM)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Azure 内で通信する一連の VM を実装する

この非ハイブリッド シナリオは、トレーニングおよびデモを目的とした一般的なシナリオで、デモ/トレーニング シナリオを表すソフトウェアは複数の VM に分散されます。 さまざまな VM にインストールされたさまざまなコンポーネントが、互いに通信する必要があります。 また、このシナリオでは、オンプレミスのネットワーク通信またはクロスプレミス シナリオを必要としません。

このシナリオは、本ドキュメントの「[単一の VM と SAP NetWeaver のデモ/トレーニング シナリオ][planning-guide-7.1]」の章で説明されているインストールを拡張したものです。 ここでは、さらに多くの仮想マシンが既存のリソース グループに追加されます。 次の例では、トレーニング ランドスケープは SAP ASCS/SCS VM、DBMS を実行する VM、および SAP アプリケーション サーバー インスタンス VM で構成されます。

このシナリオを構築する前に、前のシナリオで実行済みの基本的な設定について検討する必要があります。

#### <a name="resource-group-and-virtual-machine-naming"></a>リソース グループと仮想マシンの名前付け

すべてのリソース グループ名は一意である必要があります。 リソースの独自の命名規則を設けます (`<rg-name`>-サフィックスなど)。

仮想マシン名は、リソース グループ内で一意である必要があります。

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>複数の VM 間の通信用にネットワークをセットアップする

![Set of VMs within an Azure Virtual Network (Azure Virtual Network 内の VM セット)][planning-guide-figure-1900]

同じトレーニング/デモ ランドスケープのクローンとの名前付けの競合を防ぐために、ランドスケープごとに 1 つの Azure Virtual Network を作成します。 DNS 名前解決が Azure から提供されるか、または、Azure の外に独自の DNS を構成することができます (ここでは詳しい説明は省略します)。 このシナリオでは、独自の DNS を構成しません。 ホスト名を介した 1 つの Azure Virtual Network 通信内のすべての仮想マシンが有効になります。

リソース グループだけでなく、仮想ネットワークごとにもトレーニングまたはデモ ランドスケープを分ける理由は、次のとおりです。

* セットアップとしての SAP ランドスケープにはその独自の AD/OpenLDAP が必要で、ドメイン サーバーを各ランドスケープに含める必要があるため。  
* セットアップとしての SAP ランドスケープに、固定の IP アドレスと連携する必要のあるコンポーネントがあるため。

Azure Virtual Network の詳細とこれを定義する方法について詳しくは、[こちらの記事][virtual-networks-create-vnet-arm-pportal]をご覧ください。

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>企業ネットワーク接続による SAP VM のデプロイ (クロスプレミス)

SAP ランドスケープを実行し、ハイエンドの DBMS サーバー向けベアメタル、アプリケーション層向けオンプレミス仮想化環境、およびより小規模な 2 層構成の SAP システムと Azure IaaS の間にデプロイを分割する必要があるとします。 基本的な前提条件として、1 つの SAP ランドスケープ内の SAP システムが相互に、また、そのデプロイ形式を問わず、社内にデプロイされた他の多数のソフトウェア コンポーネントと通信する必要があります。 また、SAP GUI または他のインターフェイスで接続するエンド ユーザーのデプロイ形式に差異がないようにする必要があります。 これらの条件は、オンプレミスの Active Directory/OpenLDAP および DNS サービスを、サイト間/マルチサイト接続またはプライベート接続 (Azure ExpressRouteなど) を通じて Azure システムに拡張した場合にのみ満たされます。



### <a name="scenario-of-an-sap-landscape"></a>SAP ランドスケープのシナリオ

次の図に、クロスプレミス シナリオまたはハイブリッド シナリオの概要を示します。

![Site-to-site connection between on-premises and Azure (オンプレミスと Azure アセット間のサイト間接続)][planning-guide-figure-2100]

上に示したシナリオでは、オンプレミスのシナリオを示しています。

最小要件は、安全な通信プロトコル (Azure サービスへのブラウザー アクセス用に SSL/TLS、システム アクセス用に VPN ベースの接続など) を使用することです。 企業が異なる方法で企業ネットワークと Azure 間の VPN 接続に対処することが前提となります。 一部の企業は、すべてのポートを完全に開き、 他の企業は、開く必要があるポートを正確に把握したいと考えています。

次の表は、一般的な SAP 通信ポートを示しています。 基本的には、SAP ゲートウェイ ポートを開くだけで十分です。

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Service | ポート名 | 例 `<nn`> = 01 | 既定の範囲 (最小 - 最大) | Comment (コメント) |
| --- | --- | --- | --- | --- |
| ディスパッチャー |sapdp`<nn>` * を参照 |3201 |3200 - 3299 |SAP ディスパッチャー (SAP GUI が Windows と Java 用に使用) |
| メッセージ サーバー |sapms`<sid`> ** を参照 |3600 |制限なし sapms`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw`<nn`> * を参照 |3301 |制限なし |SAP ゲートウェイ (CPIC および RFC 通信用) |
| SAP ルーター |sapdp99 |3299 |制限なし |インストール後に、CI (セントラル インスタンス) サービス名のみを /etc/services で任意の値に再割り当てできます。 |

*) nn = SAP インスタンス番号

**) sid = SAP-System-ID

各種 SAP 製品に必要なポートや、SAP 製品別のサービスの詳細については、ここ <https://scn.sap.com/docs/DOC-17124> を参照してください。
このドキュメントでは、特定の SAP 製品およびシナリオに必要な VPN デバイスで専用ポートを開くことができます。

このようなシナリオで VM をデプロイするときのその他のセキュリティ対策として、[ネットワーク セキュリティ グループ][virtual-networks-nsg]を作成して、アクセス規則を定義します。

### <a name="dealing-with-different-virtual-machine-series"></a>さまざまな仮想マシン シリーズの操作

vCPU の数、メモリ、またこれが実行されるハードウェアでの重要な設定などが異なる、さまざまな種類の VM が数多く追加されました。 SAP ではこれらの VM の一部がサポートされています (サポートされている VM の種類については、SAP ノート [1928533]をご覧ください)。 これらの VM の一部は、他の世代のホスト ハードウェアで実行されます。 これらのホスト ハードウェアの世代は、Azure スケール ユニットの粒度でデプロイされます。 この場合、選択したさまざまなサイズの VM を同じスケール ユニットで実行できない可能性があります。 可用性セットを、さまざまなハードウェアに応じたスケール ユニットにわたって使用することには限界があります。  たとえば、HA 構成でセカンダリ DBMS インスタンスを実行している VM と共に可用性セット内にある、E64s_v3 VM で SAP DBMS 層を実行する場合、VM のアップグレードが必要になる可能性があるため、単にセカンダリ VM を停止して M シリーズの VM として再起動することはできません。 理由は、M シリーズの VM と Ev3 シリーズの VM が別のハードウェア上で実行されており、別のスケール ユニット内のものが使用されているためです。 新しい可用性セットを作成し、ストレージを削除せずに、セカンダリ Ev3 シリーズの VM を削除して、新しい可用性セットに M シリーズの VM として VM を再デプロイする必要があります。

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Azure で SAP インスタンスからローカル ネットワーク プリンターで印刷する

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>クロスプレミス シナリオでの TCP/IP による印刷

Azure VM におけるオンプレミス TCP/IP ベースのネットワーク プリンターの設定は、VPN サイト間トンネルまたは ExpressRoute 接続が確立されていることを前提として、企業のネットワークでの設定とほとんど変わりません。

---
> ![Windows][Logo_Windows] Windows
>
> これを行うには、次の手順を実行します。
>
> * 一部のネットワーク プリンターには構成ウィザードが付属しており、これを使用して Azure VM でプリンターを簡単にセットアップできます。 プリンターにウィザード ソフトウェアが付属していない場合のプリンター セットアップの手動の方法として、新しい TCP/IP プリンター ポートを作成します。
> * コントロール パネル、[デバイスとプリンター]、[プリンターの追加] の順に選択します。
> * [TCP/IP アドレスまたはホスト名を使ってプリンターを追加する] を選択します。
> * プリンターの IP アドレスを入力し、
> * プリンタ ポート標準 9100 を選択します。
> * 必要に応じて、適切なプリンター ドライバーを手動でインストールします。
>
> ![Linux][Logo_Linux] Linux
>
> * Windows の場合と同様に標準の手順に従って、ネットワーク プリンターをインストールします
> * プリンターの追加方法については、[SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) または [Red Hat および Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) 向けの一般的な Linux ガイドを参照してください。
>
>

---
![Network printing (ネットワーク印刷)][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>クロスプレミス シナリオでの SMB (共有プリンター) 経由のホスト ベース プリンター

ホストベースのプリンターは、設計上、ネットワーク互換がありません。 ただし、ホスト ベースのプリンターは、プリンターが電源がオンのコンピューターに接続されていれば、ネットワーク上のコンピューター間で共有できます。 企業ネットワークをサイト間または ExpressRoute で接続し、ローカル プリンターを共有します。 SMB プロトコルは、ネーム サービスとして DNS ではなく NetBIOS を使用します。 NetBIOS ホスト名は、DNS ホスト名と異なっていてもかまいません。 標準的なケースでは、NetBIOS ホスト名と DNS ホスト名に同じ名前を使用します。 DNS ドメインは、NetBIOS 名前空間では意味を成しません。 そのため、DNS ホスト名と DNS ドメインで構成された完全修飾 DNS ホスト名を NetBIOS 名前空間で使用することはできません。

プリンター共有は、ネットワーク内の次の一意の名前によって識別されます。

* SMB ホストのホスト名 (常に必要)
* 共有の名前 (常に必要)
* ドメインの名前 (プリンター共有が SAP システムと同じドメイン内にない場合)
* さらに、プリンター共有にアクセスするために、ユーザー名とパスワードが必要になる場合があります。

方法:

---
> ![Windows][Logo_Windows] Windows
>
> ローカル プリンターを共有します。
> Azure VM で Windows エクスプローラーを開き、プリンターの共有名を入力します。
> プリンターのインストール ウィザードにより、インストール プロセスが示されます。
>
> ![Linux][Logo_Linux] Linux
>
> Linux でのネットワーク プリンターの構成に関するドキュメント、または Linux での印刷に関する章を含むドキュメントをいくつか紹介します。 これは、VM が VPN の一部であれば、Azure Linux VM と同じように機能します。
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL または Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB プリンター (プリンターの転送)

Azure では、リモート セッションでローカル プリンター デバイスへのアクセスをユーザーに提供するリモート デスクトップ サービスの機能は利用できません。

---
> ![Windows][Logo_Windows] Windows
>
> Windows での印刷の詳細については、ここ <https://technet.microsoft.com/library/jj590748.aspx> を参照してください。
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>クロスプレミスでの SAP Azure システムの Correction and Transport システム (TMS) への統合

SAP Change and Transport System (TMS) は、ランドスケープでシステム全体の転送要求をエクスポートおよびインポートするように構成する必要があります。 SAP システム (DEV) の開発インスタンスは Azure に配置され、品質保証 (QA) と運用システム (PRD) はオンプレミスに配置されることを前提としています。 さらに、セントラル移送ディレクトリがあることを前提としています。

##### <a name="configuring-the-transport-domain"></a>移送ドメインの構成

「 [Configuring the Transport Domain Controller (移送ドメイン コントローラー)](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)」の説明に従って、移送ドメイン コントローラーとして指定したシステムに移送ドメインを構成します。 システム ユーザー TMSADM が作成され、必要な RFC 宛先が生成されます。 トランザクション SM59 を使用してこれらの RFC 接続を確認できます。 ホスト名の解決が、移送ドメイン全体で有効になっている必要があります。

方法:

* このシナリオでは、オンプレミスの QAS システムを CTS ドメイン コントローラーとして使用します。 トランザクション STMS を呼び出します。 [TMS] ダイアログ ボックスが表示されます。 [Configure Transport Domain]\(移送ドメインの構成) ダイアログ ボックスが表示されます。 (このダイアログ ボックスは、移送ドメインをまだ構成していない場合にのみ表示されます。)
* 自動作成されたユーザー TMSADM が承認されていることを確認します ([SM59] -> [ABAP Connection (ABAP 接続)] -> [TMSADM@E61.DOMAIN_E61] -> [Details (詳細)] -> [Utilities(M) [ユーティリティ(M)]] -> [Authorization Test (承認テスト)])。 トランザクション STMS の初期画面に、次に示すように、この SAP システムが現在移送ドメインのコントローラーとして機能していることが示されます。

![Initial screen of transaction STMS on the domain controller (ドメイン コントローラー上のトランザクション STMS の初期画面)][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>移送ドメインに SAP システムを含める

移送ドメインに SAP システムを含めるシーケンスは次のようになります。

* Azure の DEV システムで移送システム (Client 000) に移動し、トランザクション STMS を呼び出します。 ダイアログ ボックスから [Other Configuration]\(その他の構成) を選択し、[Include System in Domain]\(ドメインにシステムを含める) で続行します。 ターゲット ホストとしてドメイン コントローラーを指定します (「[移送ドメインに SAP システムを含める](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)」)。 移送ドメインに含められるのをシステムが待機します。
* 次に、セキュリティ上の理由から、ドメイン コントローラーに戻って要求を確認する必要があります。 待機中のシステムの [System Overview]\(システムの概要) と [Approve]\(承認) を選択します。 次に、プロンプトを確認すると、構成が配布されます。

これで、SAP システムに、移送ドメイン内の他のすべての SAP システムに関する必要な情報が含まれました。 同時に、新しい SAP システムのアドレス データが他のすべての SAP システムに送信され、SAP システムが、移送コントロール プログラムの移送プロファイルに入力されました。 RFC と、ドメインの移送ディレクトリへのアクセスが起動するかどうかを確認します。

通常どおり、「 [Change and Transport System](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)」のドキュメントの説明に従って、移送システムの構成を続行します。

方法:

* オンプレミスの STMS が正しく構成されていることを確認します。
* 移送ドメイン コントローラーのホスト名を Azure 上の仮想マシンで解決できること、またその逆で解決できることを確認します。
* トランザクション STMS -> [Other Configuration]\(その他の構成) -> [Include System in Domain]\(ドメインにシステムを含める) を呼び出します。
* オンプレミスの TMS システムの接続を確認します。
* 通常どおり、移送ルート、グループ、階層を構成します。

サイト間接続のクロスプレミス シナリオでは、オンプレミスと Azure 間で引き続き相当な遅延が発生する場合があります。 開発およびテスト システムから運用システムにオブジェクトを移送するシーケンスに従った場合、または移送およびサポート パッケージをさまざまなシステムに適用することを検討している場合は、お気付きのとおり、セントラル移送ディレクトリの場所によっては、一部のシステムでセントラル移送ディレクトリ内のデータの読み取りまたは書き込みに大きな遅延が発生します。 この状況は SAP ランドスケープ構成と同様で、ここでは、さまざまなシステムが互いに距離が非常に離れたさまざまなデータ センターを通じて分散されます。

こうした遅延を解消し、移送ディレクトリに対する読み取りまたは書き込みでシステムを遅延なく動作させるには、2 つの STMS 移送ドメイン (オンプレミス用と Azure のシステム用) をセットアップして、移送ドメインをリンクできます。 SAP TMS のこの概念の背景にある原理を説明した、こちらのドキュメント (<https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>) を確認してください。

方法:

* それぞれの場所 (オンプレミスと Azure) でトランザクション STMS を使ってトランスポート ドメインを設定する - <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* ドメイン リンクを使ってドメインをリンクし、2 つのドメイン間のリンクを確認します。
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* リンクされたシステムに構成を配布します。

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Azure とオンプレミスに配置された SAP インスタンス間の RFC トラフィック (クロスプレミス)

オンプレミスおよび Azure 内にある、システム間の RFC トラフィックが機能する必要があります。 接続をセットアップするには、ソース システム内のトランザクション SM59 を呼び出します。ここでは、ターゲット システムに対する RFC 接続を定義する必要があります。 構成は、RFC 接続の標準セットアップに似ています。

クロスプレミス シナリオでは、相互に通信する必要がある SAP システムを実行する、VM が同じドメイン内にあると想定しています。 したがって、SAP システム間の RFC 接続のセットアップは、オンプレミスのシナリオのセットアップ手順や入力値と変わりません。

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Azure に配置された SAP インスタンスからローカルのファイル共有へのアクセス (またはその逆のアクセス)

Azure に配置された SAP インスタンスでは、企業プレミス内にある、ファイル共有にアクセスする必要があります。 また、オンプレミスの SAP インスタンスでは、Azure に配置されている、ファイル共有にアクセスする必要があります。 ファイル共有を有効にするには、ローカル システムでアクセス許可と共有オプションを構成する必要があります。 必ず、Azure とデータ センター間の VPN または ExpressRoute 接続でポートを開いてください。

## <a name="supportability"></a>サポート

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure Monitoring Solution for SAP

Azure 上のミッション クリティカルな SAP システムの監視を有効にするために、SAP 監視ツール SAPOSCOL または SAP Host Agent は、Azure Monitoring Extension for SAP 経由で Azure 仮想マシン サービス ホストからデータを除去します。 SAP による要求は SAP アプリケーションに固有であったため、マイクロソフトでは、必要な機能を Azure に実装せず、必要な監視コンポーネントおよび構成を Azure で実行されるその Virtual Machines にデプロイすることをお客様に委ねることにしました。 ただし、監視コンポーネントのデプロイとライフサイクル管理は Azure によってほぼ自動化されます。

#### <a name="solution-design"></a>ソリューション設計

SAP の監視を有効にするよう開発されたソリューションは、Azure VM エージェントおよび拡張機能フレームワークのアーキテクチャに基づいています。 Azure VM エージェントおよび拡張機能フレームワークの考え方は、VM 内で Azure VM 拡張機能ギャラリーで使用できるソフトウェア アプリケーションのインストールを許可することです。 この概念の背景にある原則は (Azure Monitoring Extension for SAP などの場合)、特別な機能の VM へのデプロイと、デプロイ時のそのようなソフトウェアの構成を許可することです。

VM 内の特定の Azure VM 拡張機能の操作を可能にする "Azure VM エージェント" が、Azure Portal で VM を作成するときに Windows VM に既定で組み込まれます。 SUSE、Red Hat または Oracle Linux では、VM エージェントは Azure Marketplace イメージに既に含まれています。 オンプレミスから Azure に Linux VM をアップロードする場合は、VM エージェントを手動でインストールする必要があります。

SAP 向けの Azure での監視ソリューションの基本的な構成ブロックは次のようになります。

![Microsoft Azure Extension components (Microsoft Azure 拡張機能コンポーネント)][planning-guide-figure-2400]

上のブロック図に示すように、SAP 向け監視ソリューションの一部は Azure VM イメージと Azure 拡張機能ギャラリー (Azure 操作によって管理されるグローバルにレプリケートされたリポジトリ) でホストされます。 SAP の Azure 実装を行い、Azure 操作を処理して新しいバージョンの Azure Monitoring Extension for SAP を発行することは SAP/MS の共同チームの役割です。

新しい Windows VM をデプロイするときに Azure VM エージェントが VM に自動的に追加されます。 このエージェントの機能は、SAP NetWeaver システムの監視用 Azure 拡張機能の読み込みと構成を調整することです。 Linux VM の場合、Azure VM エージェントは Azure Marketplace OS イメージに既に含まれています。

ただし、ユーザーが実行する必要のある手順があります。 これは、パフォーマンス収集の有効化と構成です。 構成に関連するプロセスは、PowerShell スクリプトまたは CLI コマンドによって自動化されます。 PowerShell スクリプトは Microsoft Azure スクリプト センターでダウンロードできます。詳しくは、[デプロイ ガイド][deployment-guide]をご覧ください。

SAP 用 Azure 監視ソリューションの全体的なアーキテクチャは次のようになります。

![Azure monitoring solution for SAP NetWeaver (SAP NetWeaver 向け Azure 監視ソリューション)][planning-guide-figure-2500]

**デプロイ時にこれらの PowerShell コマンドレットまたは CLI コマンドを使用する具体的な方法や詳しい手順については、[デプロイ ガイド][deployment-guide]** の手順をご確認ください。

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Azure にある SAP インスタンスの SAProuter への統合

Azure で実行される SAP インスタンスにも SAProuter からアクセスできる必要があります。

![SAP-Router Network Connection (SAP-ルーター ネットワーク接続)][planning-guide-figure-2600]

直接的な IP 接続がない場合、SAProuter により、参加しているシステム間の TCP/IP 通信が可能になります。 これには、通信パートナー間でネットワーク レベルでのエンド ツー エンドの接続が必要ないという利点があります。 SAProuter は既定で、ポート 3299 でリッスンします。
SAProuter 経由で SAP インスタンスを接続するには、接続の試行時に SAProuter 文字列とホスト名を指定する必要があります。

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

ここまで、SAP NetWeaver の概要や SAP NetWeaver ABAP スタックについて説明してきました。 この短いセクションでは、SAP Java スタック固有の考慮事項を掲載しています。 SAP NetWeaver Java だけに基づいているアプリケーションで最も重要な事項の 1 つは SAP エンタープライズ ポータルです。 他の SAP NetWeaver ベースのアプリケーション (SAP PI、SAP Solution Manager など) は SAP NetWeaver ABAP スタックと Java スタックの両方を使用します。 そのため、SAP NetWeaver Java スタックに関連した固有の側面についても考慮する必要があります。

### <a name="sap-enterprise-portal"></a>SAP エンタープライズ ポータル

Azure 仮想マシンでの SAP ポータルのセットアップは、クロスプレミス シナリオでデプロイする場合のオンプレミスのインストールと違いはありません。 DNS はオンプレミスで実行されるため、は、各インスタンスのポート設定は構成済みのオンプレミスとして実行できます。 本ドキュメントで説明されている推奨事項と制限は、一般的に SAP エンタープライズ ポータルや SAP NetWeaver Java スタックなどのアプリケーションに適用されます。

![Exposed SAP Portal (公開された SAP ポータル)][planning-guide-figure-2700]

特別なデプロイ シナリオとして、一部のお客様は、サイト間 VPN トンネルまたは ExpressRoute 経由で仮想マシンのホストを企業のネットワークに接続する際に、SAP エンタープライズ ポータルをインターネットに直接公開しています。 このようなシナリオでは、特定のポートが開いていることと、ファイアウォールまたはネットワーク セキュリティ グループによってブロックされていないことを確認する必要があります。 

初期のポータル URI は http(s):`<Portalserver`>:5XX00/irj で、ここでは、ポートは <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm> で SAP により文書化されている形式です。

![Endpoint configuration (エンドポイントの構成)][planning-guide-figure-2800]

SAP エンタープライズ ポータルの URL またはポートをカスタマイズする場合は、次のドキュメントをご覧ください。

* [ユーザー ポータル URL](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [既定のポート番号、ポータルのポート番号を変更する](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Azure Virtual Machines で実行する SAP NetWeaver の高可用性 (HA) と 障害復旧 (DR)

### <a name="definition-of-terminologies"></a>用語の定義

一般的に、**高可用性 (HA)** という用語は、**同じ**データ センター内の冗長性、フォールト トレランス、またはフェールオーバーで保護されたコンポーネントを通じて IT サービスのビジネス継続性を提供することで、IT の中断を最小限に抑える一連のテクノロジに関連します。 ここでは、Azure リージョンは 1 つです。

**障害復旧 (DR)** も IT サービスの中断を最小限に抑えることを目的としていますが、ここでは、通常数百キロメートル離れた場所にある**異なる**データ センター間での中断の最小化とその復旧に対処します。 通常、同じ地理的リージョン内の、またはお客様が独自に確立したさまざまな Azure リージョンを対象とします。

### <a name="overview-of-high-availability"></a>高可用性の概要

Azure における SAP 高可用性の説明は、2 つの要素に分けることができます。

* **Azure インフラストラクチャの高可用性**。コンピューティング (VM)、ネットワーク、ストレージなどの高可用性、SAP アプリケーションの可用性を高めるための利点など。
* **SAP アプリケーションの高可用性**。SAP ソフトウェア コンポーネントの高可用性など。
  * SAP アプリケーション サーバー
  * SAP ASCS/SCS インスタンス
  * DB サーバー

また、Azure インフラストラクチャの高可用性とどのように組み合わせることできるかについて考えます。

Azure における SAP の高可用性は、オンプレミスの物理環境または仮想環境内の SAP 高可用性と比較した場合、いくつかの相違があります。 Windows 上の仮想化環境での標準の SAP 高可用性構成については、SAP のペーパー <https://scn.sap.com/docs/DOC-44415> を参照してください。 Windows 向けはありますが、Linux 向けの sapinst-integrated SAP-HA 構成は用意されていません。 Linux 向け SAP HA オンプレミスの詳細については、<https://scn.sap.com/docs/DOC-8541> を参照してください。

### <a name="azure-infrastructure-high-availability"></a>Azure インフラストラクチャの高可用性

現在、99.9% の単一 VM SLA があります。 単一 VM の可用性の概要を確認するために、<https://azure.microsoft.com/support/legal/sla/> でさまざまな Azure SLA を利用できる製品を構築できます。

計算の基準は、1 か月あたり 30 日間または 43200 分です。 したがって、0.05% のダウンタイムは 21.6 分に相当します。 通常どおり、異なるサービスの可用性は次の方法で乗算されます。

(可用性サービス #1/100) * (可用性サービス #2/100) * (可用性サービス #3/100) 

次のようになります。

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975、つまり 99.75% の全体的な可用性。

#### <a name="virtual-machine-vm-high-availability"></a>仮想マシン (VM) の高可用性

仮想マシンの可用性に影響を与える可能性のある Azure プラットフォーム イベントには、計画的なメンテナンスと計画外のメンテナンスの 2 種類があります。

* 計画メンテナンス イベントは、全体の信頼性、パフォーマンス、仮想マシン上で実行されるプラットフォームのインフラストラクチャのセキュリティを向上させるために、基になる Azure プラットフォームに対して Microsoft が実行する定期的な更新です。
* 計画されていないメンテナンス イベントは、仮想マシンの基になるハードウェアまたは物理インフラストラクチャが何らかの障害が発生した場合に発生します。 こうした不具合には、ネットワーク障害、ローカル ディスク障害、またはその他のラック レベルでの障害が挙げられます。 そのような障害が検知されると、Azure プラットフォームは、仮想マシンをホストしている異常な物理サーバーから正常な物理サーバーへと、仮想マシンを自動的に移行します。 このような例が発生することはまれですが、この場合も仮想マシンの再起動が求められる場合があります。

詳細については、このドキュメント <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability> をご覧ください。

#### <a name="azure-storage-redundancy"></a>Azure Storage の冗長性

Microsoft Azure Storage アカウント内のデータは、持続性と高可用性を確保するために常にレプリケートされており、一時的なハードウェア障害が発生した場合でも Azure Storage SLA が満たされます。

Azure Storage は既定で 3 つのイメージのデータを保持するため、複数の Azure ディスク全体での RAID5 または RAID1 は必要ありません。

詳細については、この記事 <https://azure.microsoft.com/documentation/articles/storage-redundancy/> をご覧ください。

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Azure インフラストラクチャ VM Restart を利用した SAP アプリケーションの高可用性の実現

Windows Server フェールオーバー クラスタリング (WSFC) または Linux 上の Pacemaker (現在、SLES 12 以降についてのみサポートされています) などの機能を使用しない場合、Azure VM Restart を使用して、Azure の物理サーバー インフラストラクチャや基になる Azure プラットフォーム全体の計画したまたは計画外のダウンタイムから SAP システムを保護します。

> [!NOTE]
> Azure VM Restart で主に保護されるのはアプリケーションではなく、VM であることに注意してください。 VM Restart では SAP アプリケーションの高可用性は提供されませんが、一定のレベルのインフラストラクチャ可用性が提供されるため、間接的に SAP システムの可用性を高めることができます。 また、計画されたまたは計画外のホスト障害の後、VM の再起動にかかる時間で SLA を必要としません。 そのため、高可用性のこのメソッドは、(A)SCS や DBMS などの SAP システムの重要なコンポーネントには適していません。
>
>

高可用性の別の重要なインフラストラクチャ要素はストレージです。 たとえば、Azure Storage SLA では 99.9% の可用性となります。 すべての VM をそのディスクと共に 1 つの Azure Storage アカウントにデプロイした場合、潜在的な Azure Storage の非可用性により、その Azure Storage アカウントに配置されたすべての VM と、これらの VM 内で実行されているすべての SAP コンポーネントの可用性が失われます。  

すべての VM を 1 つの Azure Storage アカウントにデプロイするのではなく、各 VM に専用のストレージ アカウントを使用できます。この方法では、複数の独立した Azure Storage アカウントを使用して全体的な VM および SAP アプリケーションの可用性を改善できます。

Azure Managed Disks は、アタッチされている仮想マシンの障害ドメインに自動的に配置されます。 可用性セットに 2 つの仮想マシンを配置し、Managed Disks を使用するプラットフォームでは、異なる障害ドメインへの Managed Disks の配布にも対応できます。 また、Premium Storage を使用する場合は、Managed Disks を使用することも強くお勧めします。

Azure インフラストラクチャ HA とストレージ アカウントを使用した SAP NetWeaver システムのアーキテクチャの例を以下に示します。

![Azure インフラストラクチャ HA を利用した SAP アプリケーションの高可用性の実現][planning-guide-figure-2900]

Azure インフラストラクチャ HA と Managed Disks を使用した SAP NetWeaver システムのアーキテクチャの例を以下に示します。

![Azure インフラストラクチャ HA を利用した SAP アプリケーションの高可用性の実現][planning-guide-figure-2901]

重要な SAP コンポーネントについて、これまでに以下を実現しました。

* SAP アプリケーション サーバー (AS) の高可用性

  SAP アプリケーション サーバー インスタンスは、冗長コンポーネントです。 各 SAP AS インスタンスは、異なる Azure 障害ドメインおよびアップグレード ドメインで実行されている独自の VM にデプロイされます (「[障害ドメイン][planning-guide-3.2.1]」and [Upgrade Domains][planning-guide-3.2.2]の章をご覧ください)。 これは、Azure 可用性セットを使用して確保できます (「[Azure 可用性セット]」([planning-guide-3.2.3]) の章を参照してください)。 Azure 障害またはアップグレード ドメインの計画されたまたは計画外の潜在的な非可用性により、SAP AS インスタンスでの制限された数の VM の可用性が失われます。

  各 SAP AS インスタンスは独自の Azure Storage アカウントに配置され、Azure Storage アカウントの非利用可能性により、その SAP AS での 1 つの VM のみの可用性が失われます。 ただし、1 つの Azure サブスクリプション内の Azure Storage アカウント数に制限があることに注意してください。 VM 再起動後の (A)SCS インスタンスを確実に自動開始するには、(A)SCS インスタンスの開始プロファイルで自動開始パラメーターを設定します。詳細については、「[SAP インスタンスでの自動開始の使用][planning-guide-11.5]」の章を参照してください。
  詳細については、「[SAP アプリケーション サーバーの高可用性][planning-guide-11.4.1]」の章もお読みください。

  Managed Disks を使用している場合でも、Azure Storage アカウントにも格納されるので、ストレージの故障時に使用できなくなる可能性があります。

* *向上*

  ここでは、Azure VM Restart を使用して、インストールされた SAP (A)SCS インスタンスにより VM を保護します。 計画されたまたは計画外の Azure サーバーのダウンタイムの発生時に、VM は別の使用できるサーバーで起動されます。 前述のように、Azure VM Restart ではアプリケーションではなく主に VM が保護されます (この場合は (A)SCS インスタンス)。 VM Restart を使用して、SAP (A)SCS インスタンスの可用性の向上を間接的に達成します。 VM 再起動後の (A)SCS インスタンスを確実に自動開始するには、(A)SCS インスタンスの開始プロファイルで自動開始パラメーターを設定します。詳しくは、「[SAP インスタンスでの自動開始の使用][planning-guide-11.5]」の章をご覧ください。 これは、単一の VM で実行する単一障害点 (SPOF) としての (A)SCS インスタンスが SAP ランドスケープ全体の可用性の決定要因になることを意味します。

* *向上*

  ここでは、SAP (A)SCS インスタンスのユース ケースと同様に、Azure VM Restart を使用して、インストールされた DBMS ソフトウェアで VM を保護し、VM Restart 経由で DBMS ソフトウェアの可用性の向上を実現します。
  1 つの VM で実行される DBMS が SPOF であり、また SAP ランドス ケープ全体の可用性の決定要因です。

### <a name="sap-application-high-availability-on-azure-iaas"></a>Azure IaaS での SAP アプリケーションの高可用性

完全な SAP システムの高可用性を実現するには、冗長 SAP アプリケーション サーバー、SAP (A)SCS インスタンスや DBMS などの一意のコンポーネント (単一障害点など) など、すべての重要な SAP システム コンポーネントを保護する必要があります。

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>SAP アプリケーション サーバーの高可用性

SAP アプリケーション サーバー/ダイアログ インスタンスの場合、特定の高可用性ソリューションについて考慮する必要はありません。 高可用性は冗長性によって実現され、さまざまな仮想マシンでこれらを多く使用できます。 これらはすべてに同じ Azure 可用性セットに配置する必要があります。これにより、計画された保守管理のためのダウンタイム時に VM が同時に更新されるのを回避できます。 Azure スケール ユニット内のさまざまなアップグレード ドメインと障害ドメインに組み込まれた基本的な機能については、「[アップグレード ドメイン][planning-guide-3.2.2]」の章で既に紹介しました。このドキュメントの. Azure Availability Sets were presented in chapter [Azure Availability Sets][planning-guide-3.2.3]。

Azure スケール ユニット内の Azure 可用性セットが使用できる障害ドメインとアップグレード ドメインの数に制限はありません。 これは、多数の VM を 1 つの可用性セットにデプロイした場合、遅かれ早かれ 1 つ以上の VM が同じ障害またはアップグレード ドメインに配置されることになることを意味します。

専用の VM 内に少数の SAP アプリケーション サーバー インスタンスをデプロイし、5 つのアップグレード ドメインがあるとした場合、最終的には次のような状態になります。 可用性セット内の障害ドメインとアップグレード ドメインの実際の最大数は、後で変わる可能性があります。

![HA of SAP Application Servers in Azure (Azure での SAP アプリケーション サーバーの HA)][planning-guide-figure-3000]

詳細については、このドキュメント <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability> をご覧ください。

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Azure での SAP セントラル サービスの高可用性

Azure での SAP セントラル サービスの高可用性アーキテクチャについては、エントリ情報として「[SAP NetWeaver のための高可用性のアーキテクチャとシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)」という記事を参照してください。 この記事では、特定のオペレーティング システムについて詳しく説明します。

#### <a name="high-availability-for-the-sap-database-instance"></a>SAP データベース インスタンスの高可用性

一般的な SAP DBMS HA セットアップは、2 つの DBMS VM に基づいています。ここでは、DBMS 高可用性機能を使用してアクティブな DBMS インスタンスからのデータを 2 番目の VM とパッシブな DBMS インスタンスにレプリケートします。

DBMS の高可用性と障害復旧機能の概要や DBMS の詳細については、[DBMS デプロイ ガイド][dbms-guide]をご覧ください。

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>完全な SAP システムのエンド ツー エンドの高可用性

Azure での完全な SAP NetWeaver HA アーキテクチャの 2 つの例を紹介します。1 つは Windows 向けで、もう 1 つは Linux 向けです。

アンマネージド ディスクのみ:次に示す概念は、多数の SAP システムをデプロイしたり、デプロイされた VM の数がサブスクリプションあたりの Storage アカウント数の上限を超える場合は、多少の調整が必要になる場合があります。 このような場合は、VM の VHD を 1 つの Storage アカウント内に結合させる必要があります。 通常、これを行うには、さまざまな SAP システムの SAP アプリケーション層の VM の VHD を組み合わせます。  ここでも、さまざまな SAP システムの異なる DBMS VM の異なる VHD を 1 つの Azure Storage アカウントに結合しました。 これにより Azure Storage アカウントの IOPS 制限に適合 (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Windows の HA

![SAP NetWeaver Application HA Architecture with SQL Server in Azure IaaS (Azure IaaS での SQL Server を使用した SAP NetWeaver アプリケーションの HA アーキテクチャ)][planning-guide-figure-3200]

SAP NetWeaver システムに次の Azure 構造を使用して、インフラストラクチャの問題やホスト修正プログラムによる影響を最小限に抑えます。

* 完全なシステムを Azure にデプロイします (必須 - DBMS 層、(A)SCS インスタンス、完全なアプリケーション層を同じ場所で実行する必要があります)。
* 完全なシステムを 1 つの Azure サブスクリプション内で実行 (必須)。
* 完全なシステムを 1 つの Azure Virtual Network 内で実行 (必須)。
* いずれかの SAP システムの VM を 3 つの可用性セットに分けることができます。これは、すべての VM が同じ Virtual Network に属していても可能です。
* 各層 (DBMS、ASCS、アプリケーション サーバーなど) では、専用の可用性セットを使用する必要があります。
* 1 つの SAP システムの DBMS インスタンスを実行するすべての VM が 1 つの可用性セット内にある。 システムごとに DBMS インスタンスを実行している VM が 1 つ以上あることを前提としています。これは、SQL Server AlwaysOn、Oracle Data Guard など、ネイティブの DBMS 高可用性機能が使用されるためです。
* DBMS インスタンスを実行するすべての VM が独自のストレージ アカウントを使用する。 DBMS データとログ ファイルは、データを同期する DBMS の高可用性機能を使用して 1 つのストレージ アカウントから別のストレージ アカウントにレプリケートされます。 1 つのストレージ アカウントを利用できないと、1 つの SQL Windows クラスター ノードを利用できなくなりますが、SQL Server サービス全体を利用できなくなるわけではありません。
* 1 つの SAP システムの (A)SCS インスタンスを実行するすべての VM が 1 つの可用性セット内にある。 これらの VM の内部で (A)SCS インスタンスを保護するように Windows サーバー フェールオーバー クラスター (WSFC) を構成します。
* (A)SCS インスタンスを実行するすべての VM が独自のストレージ アカウントを使用する。 (A)SCS インスタンス ファイルと SAP グローバル フォルダーは、1 つのストレージ アカウントから別のストレージアカウントに SIOS DataKeeper レプリケーションを使用してレプリケートされます。 1 つのストレージ アカウントを利用できないと、1 つの (A)SCS Windowss クラスター ノードを利用できなくなりますが、(A)SCS サービス全体を利用できなくなるわけではありません。
* SAP アプリケーション サーバー層を表すすべての VM が 3 番目の可用性セットにある。
* SAP アプリケーション サーバーを実行するすべての VM が独自のストレージ アカウントを使用する。 1 つのストレージ アカウントを利用できないと、1 つの SAP アプリケーション サーバーを利用できなくなります。ここでは、他の SAP アプリケーション サーバーは引き続き実行されます。

次の図は、Managed Disks を使用した場合の同じランドスケープを示しています。

![SAP NetWeaver Application HA Architecture with SQL Server in Azure IaaS (Azure IaaS での SQL Server を使用した SAP NetWeaver アプリケーションの HA アーキテクチャ)][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Linux の HA

Azure における Linux での SAP HA のアーキテクチャは基本的に前述の Windows の場合と同様です。 サポートされている高可用性ソリューションの一覧については、SAP Note [1928533] を参照してください。

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>SAP インスタンスでの自動開始の使用

SAP では、VM 内の OS の起動直後に SAP インスタンスを開始する機能の提供が開始されました。 詳細な手順については、SAP Knowledge Base Article [1909114] を参照してください。 ただし、SAP はこの設定の使用を推奨していません。これは、インスタンスの再起動を管理する方法がないためであり、1 つ以上の VM が影響を受けるか、VM あたり複数のインスタンスが実行されるためです。 VM 内に SAP アプリケーション サーバー インスタンスが 1 つあるという一般的な Azure シナリオと、単一 VM が最終的に起動される場合を想定すると、自動開始は重要ではなく、このパラメーターを追加して有効にすることができます。

    Autostart = 1

このパラメーターを、SAP ABAP または Java インスタンスの開始プロファイルに追加します。

> [!NOTE]
> 自動開始パラメーターには、いくつかの欠点もあります。 具体的には、インスタンスの関連する Windows/Linux サービスが開始されたときに、パラメーターにより SAP ABAP または Java インスタンスの起動がトリガーされます。 オペレーティング システムの起動時がまさにこれに該当します。 ただし、SAP サービスの再起動は、SUM などの SAP ソフトウェア ライフサイクル管理機能や、その他の更新プログラムまたはアップグレードには通常の動作でもあります。 これらの機能では、インスタンスが再起動されることはまったく想定されません。 したがって、こうしたタスクを実行する前に、自動開始パラメーターを無効にする必要があります。 また、自動開始パラメーターは、ASCS/SCS/CI などのクラスター化された SAP インスタンスに使用しないでください。
>
>

SAP インスタンスの自動開始に関するその他の情報については、以下を参照してください。

* [Start/Stop SAP along with your Unix Server Start/Stop (Unix サーバーの開始/停止に伴う SAP の開始/停止)](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starting and Stopping SAP NetWeaver Management Agents (SAP NetWeaver 管理エージェントの開始と停止)](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [How to enable auto Start of HANA Database (HANA データベースの自動開始を有効にする方法)](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>大規模な 3 層 SAP システム
3 層 SAP 構成の高可用性については、前のセクションで既に説明しました。 ただし、DBMS サーバーの要件が高すぎるため Azure に配置できない場合のシステムはどうなるでしょうか。SAP アプリケーション層を Azure にデプロイできるでしょうか。

#### <a name="location-of-3-tier-sap-configurations"></a>3 層 SAP 構成の場所
アプリケーション層自体、またはアプリケーションやオンプレミスと Azure 間の DBMS 層を分割することはサポートされていません。 SAP システムは、完全にオンプレミスまたは Azure のいずれかにデプロイされます。 一部のアプリケーション サーバーをオンプレミスや Azure の他の機能で実行することもサポートされていません。 これが、この場合の出発点となります。 2 つの異なる Azure リージョンにデプロイされる SAP システムと SAP アプリケーション サーバー層の DBMS コンポーネントを持つこともサポートされていません。 たとえば、米国西部の DBMS と米国中部の SAP アプリケーション層などです。 このような構成をサポートしていない理由は、SAP NetWeaver アーキテクチャの待機時間の問題があるためです。

ただし、ここ 1 年で、データ センター パートナーが Azure リージョンへのコロケーションを開発しました。 これらのコロケーションは、多くの場合、Azure リージョン内の物理的な Azure データ センターに近接しています。 ExpressRoute を通じた Azure へのコロケーション内のアセット間の距離が短いことと接続性により、待機時間を 2 ミリ秒未満に抑えられます。 このような場合、こうしたコロケーションに DBMS 層 (ストレージ SAN/NAS を含む) を配置し、Azure に SAP アプリケーション層を配置できます。 [HANA L インスタンス](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。 

### <a name="offline-backup-of-sap-systems"></a>SAP システムのオフライン バックアップ
選択した SAP 構成 (2 層または 3 層) によっては、バックアップが必要になる場合があります。 VM 自体のコンテンツと、データベースのバックアップを保持する必要があります。 DBMS 関連のバックアップをデータベース メソッドで実行することが想定されます。 各種データベースについて詳しくは、[DBMS ガイド][dbms-guide]をご覧ください。 これに対して、SAP データはオフラインでバックアップできます (データベースのコンテンツを含む)。詳しくは、このセクションの説明を参照してください。オンラインについて詳しくは、次のセクションの説明を参照してください。

オフライン バックアップでは基本的に、Azure Portal を通じた VM のシャットダウンと、ベース VM ディスクおよび VM にアタッチされたすべてのディスクのコピーが必要になります。 これにより、VM のポイントインタイム イメージとその関連付けられたディスクが保持されます。 バックアップを別の Azure Storage アカウントにコピーすることをお勧めします。 手順については、このドキュメントの「[Azure Storage アカウント間でのディスクのコピー][planning-guide-5.4.2]」の章をご覧ください。
Azure ポータルを使用したシャット ダウンだけでなく、<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/> で説明されているように、Powershell または CLI を使っても実行できます。

その状態のリストアは、ベース VM と、ベース VM およびマウントされたディスクのオリジナル ディスクの削除、保存されたディスクのオリジナル Storage アカウントまたはマネージド ディスクのリソース グループへのコピー、システムの再デプロイで構成されます。
この記事ではこのプロセスを Powershell でスクリプト化する方法について説明します: <http://www.westerndevs.com/azure-snapshots/>。

前に説明されているように VM バックアップのリストアでは新しいハードウェア キーが作成されるため、新しい SAP ライセンスを必ずインストールしてください。

### <a name="online-backup-of-an-sap-system"></a>SAP システムのオンライン バックアップ

DBMS のバックアップは DBMS 固有の方法で実行されます。詳細については、[DBMS ガイド][dbms-guide]を参照してください。

SAP システム内の他の VM は、Azure 仮想マシン バックアップ機能を使用してバックアップできます。 Azure 仮想マシンのバックアップは、Azure で完全な VM をバックアップするための標準の方法です。 Azure Backup では Azure にバックアップを格納し、VM のリストアが再度可能になります。

> [!NOTE]
> 2015 年 12 月の時点では、VM Backup を使用して、SAP ライセンス用に使用される一意の VM ID を保持することはできません。 つまい、VM バックアップからのリストアには、新しい SAP ライセンス キーのインストールが必要です。これは、リストアされた VM は新しい VM であるとみなされ、保存された以前の VM の代替であるとはみなされないためです。
>
> ![Windows][Logo_Windows] Windows
>
> 論理的には、データベースを実行する VM は、DBMS システムで、たとえば SQL Server のように Windows VSS (ボリューム シャドウ コピー サービス <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) をサポートしている場合は、整合性のとれた方法でバックアップできます。
> ただし、データベースの Azure VM バックアップのポイントインタイム リストアによってはバックアップできない場合があることにご注意ください。 そのため、Azure VM Backup を使用するのではなく、DBMS 機能を使ってデータベースのバックアップを実行することをお勧めします。
>
> Azure 仮想マシンのバックアップの詳細については、こちら (<https://docs.microsoft.com/azure/backup/backup-azure-vms>) をご覧ください。
>
> 他の方法として、Azure VM にインストールされた Microsoft Data Protection Manager と Azure Backup を組み合わせて、データベースをバックアップ/リストアする方法があります。 詳細については、こちら <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction> をご覧ください。  
>
> ![Linux][Logo_Linux] Linux
>
> Linux には Windows VSS に相当するものはありません。 そのため、ファイルの整合性のバックアップのみが可能で、アプリケーションの整合性のバックアップは実行できません。 SAP DBMS バックアップは、DBMS 機能を使用して実行する必要があります。 SAP 関連のデータを含むファイル システムは、ここ <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm> で説明されているように、たとえば tar を使用して保存できます。
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>運用 SAP ランドスケープの DR サイトとしての Azure

2014 年中盤以降、Hyper-V、System Center、Azure 周辺のさまざまなコンポーネントの拡張により、Hyper-V をベースとするオンプレミスで実行される VM 向けの DR サイトとして Azure を使用できるようになりました。

このソリューションを展開する方法の詳細については、ここ <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx> を参照してください。

## <a name="summary"></a>まとめ

Azure での SAP システムの高可用性における重要なポイントは次のとおりです。

* この時点で、SAP の単一障害点は、オンプレミス デプロイメントで実行できるのとまったく同じ方法で保護することはできません。 その理由は、Azure では、サード パーティ製ソフトウェアを使用せずに共有でディスク クラスターを構築することがまだできないためです。
* DBMS 層の場合は、共有ディスク クラスター テクノロジに依存しない DBMS 機能を使用する必要があります。 詳しくは、[DBMS ガイド][dbms-guide]をご覧ください。
* Azure インフラストラクチャまたはホストのメンテナンスでの障害ドメイン内の問題の影響を最小限に抑えるには、Azure 可用性セットを使用してください。
  * SAP アプリケーション層では 1 つの可用性セットを使用することをお勧めします。
  * SAP DBMS 層では個別の可用性セットを使用することをお勧めします。
  * 異なる SAP システムの VM に同じ可用性セットを適用することは推奨されません。
  * Premium Managed Disks を使用することをお勧めします。
* SAP DBMS 層のバックアップの目的については、[DBMS ガイド][dbms-guide]をご覧ください。
* SAP ダイアログ インスタンスのバックアップは、通常単にダイアログ インスタンスを再デプロイするほうが速いため、ほとんど意味がありません。
* SAP システムのグローバル ディレクトリを含む VM を異なるインスタンスのすべてのプロファイルと共にバックアップすることは合理的であり、これは、Windows Backup (または Linux 上の tar など) で実行する必要があります。 Windows Server 2008 (R2) と Windows Server 2012 (R2) にはいくつかの違いがあり、最新の Windows Server リリースを使用したバックアップのほうが簡単であるため、Windows Server 2012 (R2) を Windows ゲスト オペレーティング システムとして実行することをお勧めします。

## <a name="next-steps"></a>次の手順
以下の記事を参照してください。

- [SAP NetWeaver のための Azure Virtual Machines のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)
