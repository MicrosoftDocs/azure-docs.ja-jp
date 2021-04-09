---
title: SAP ワークロードのための SQL Server Azure Virtual Machines DBMS のデプロイ | Microsoft Docs
description: SAP ワークロードのための SQL Server Azure Virtual Machines DBMS のデプロイ
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: Azure、SQL Server、SAP、AlwaysOn
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4eb7e64065e311dc18f33dffb169d5c27a34008d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673037"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SAP NetWeaver のための SQL Server Azure Virtual Machines DBMS のデプロイ

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
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
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




このドキュメントでは、Azure IaaS に SAP ワークロードのために SQL Server をデプロイするときに考慮すべきいくつかの異なる領域について説明します。 このドキュメントの前提条件として、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)」ドキュメントと [Azure 上の SAP ワークロードに関するドキュメント](./get-started.md)の中の他のガイドを読んでいる必要があります。 



> [!IMPORTANT]
> このドキュメントの範囲は、SQL Server 上の Windows バージョンです。 SAP は、SAP ソフトウェアのいずれかを使用する Linux バージョンの SQL Server をサポートしていません。 このドキュメントでは、Microsoft Azure SQL Database については取り上げません。Microsoft Azure SQL Database は、Microsoft Azure Platform が提供するサービスとしてのプラットフォームです。 このホワイト ペーパーでは、SQL Server 製品の実行について説明します。これは Azure Virtual Machines でのオンプレミス デプロイメントとして知られており、Azure のサービスとしてのインフラストラクチャ機能を使用します。 これらの 2 つの製品のデータベース機能と機能性は異なっているため、混同しないでください。 関連項目: <https://azure.microsoft.com/services/sql-database/>
> 
>

一般的に、Azure IaaS で SAP ワークロードを実行するには、最新の SQL Server リリースを使用することを検討することをお勧めします。 最新の SQL Server リリースは、Azure のサービスと機能の一部との統合性が向上しています。 または、Azure IaaS インフラストラクチャで操作を最適化するように変更します。

続行する前に、「Azure Virtual Machines 上の SQL Server とは何か (Windows)」 [https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview ] の記事を確認することをお勧めします。

以降のセクションでは、上記のリンクにあるドキュメントの一部の情報を集約して説明しています。 SAP に関する特記事項についても説明し、いくつかの概念についてはさらに詳しく説明します。 ただし、SQL Server に特化したドキュメントを読み取る前に、上記のドキュメントに目を通すことを強くお勧めします。

先に進む前に知っておくべき IaaS での SQL Server に固有の情報があります。

* **SQL バージョンのサポート**: SAP のお客様に対しては、Microsoft Azure Virtual Machines で SQL Server 2008 R2 以降がサポートされています。 これより前のエディションはサポートされていません。 詳細については、この一般的な [サポートの説明](https://support.microsoft.com/kb/956893) を確認してください。 マイクロソフトは基本的には SQL Server 2008 をサポートしています。 ただし、SQL Server 2008 R2 で導入された SAP の重要な機能によって、SQL Server 2008 R2 が SAP の最小リリースとなっています。 一般的に、Azure IaaS で SAP ワークロードを実行するには、最新の SQL Server リリースを使用することを検討することをお勧めします。 最新の SQL Server リリースは、Azure のサービスと機能の一部との統合性が向上しています。 または、Azure IaaS インフラストラクチャで操作を最適化するように変更します。 そのため、このドキュメントは SQL Server 2016 と SQL Server 2017 に制限されています。
* **SQL のパフォーマンス**: Microsoft Azure がホストする Virtual Machines は、他のパブリック クラウド仮想化製品と比べて良好に機能しますが、個々の結果は異なる場合があります。 「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)」の記事を参照してください。
* **Azure Marketplace からのイメージの使用**: 新しい Microsoft Azure VM をデプロイする最も早い方法は、Azure Marketplace からのイメージを使用することです。 Azure Marketplace には、最新の SQL Server リリースを含むイメージがあります。 SQL Server が既にインストールされているイメージは、SAP NetWeaver アプリケーション用にすぐに使用することができません。 その理由は、それらのイメージ内に既定の SQL Server 照合順序がインストールされており、SAP NetWeaver システムで必要な照合順序がインストールされていないためです。 このようなイメージを使用するには、「[Microsoft Azure Marketplace からの SQL Server イメージの使用][dbms-guide-5.6]」の章に記載されている手順をご確認ください。 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>SAP 関連 SQL Server のデプロイメントの VM/VHD 構造に関する推奨事項
概要に従って、オペレーティング システム、SQL Server 実行可能ファイル、SAP 2 層システムの場合は SAP 実行可能ファイルを、個別の Azure ディスクに配置またはインストールする必要があります。 通常、SAP NetWeaver ワークロードでは、ほとんどの SQL Server システム データベースを高レベルでは使用しません。 ただし、SQL Server のシステム データベース (master、msdb、model) は、別の Azure ディスク上の他の SQL Server ディレクトリと一緒に配置する必要があります。 SQL Server の tempdb は、永続化されていない D:\ ドライブまたは別のディスクに配置する必要があります。


* A シリーズの VM を除くすべての SAP 認定 VM の種類 (SAP Note [1928533] を参照) では、tempdb データとログ ファイルを非永続ドライブの D:\ に配置できます。 
* SQL Server によって既定で 1 つのデータ ファイルを使用して tempdb がインストールされる古い SQL Server リリースの場合は、複数の tempdb データ ファイルを使用することをお勧めします。 D:\ ドライブのボリュームは VM タイプに応じて異なることに注意してください。 異なる VM の D:\ ドライブの正確なサイズについては、記事「[Azure の Windows 仮想マシンのサイズ](../../sizes.md)」を参照してください。

これらの構成により、システム ドライブが提供できるよりも多くの領域、さらに重要なより多くの IOPS とストレージ帯域幅を tempdb で消費できるようになります。 非永続の D:\ ドライブは、(A シリーズの VM を除き) I/O 待機時間とスループットが優れています。 適切な tempdb のサイズを決定するために、既存のシステムの tempdb のサイズを確認します。 

>[!NOTE]
> 作成した D:\ ドライブ上のフォルダーに tempdb データ ファイルとログ ファイルを配置する場合は、VM を再起動した後にフォルダーが存在することを確認する必要があります。 VM の再起動後に D:\ ドライブは新たに初期化されるので、すべてのファイルおよびディレクトリ構造は消去されます。SQL Server サービスの開始前に D:\ ドライブ上の最終的なディレクトリ構造を再作成する可能性については、[こちらの記事](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)を参照してください。

SQL Server と SAP データベースを実行し、D:\ ドライブに tempdb データと tempdb ログ ファイルを配置する VM 構成は次のようになります。

![SQL Server の簡易な VM ディスク構成図](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

上の図は簡易な場合を示しています。 記事「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」で説明されているように、Azure Storage の種類、ディスクの数とサイズはさまざまな要素によって変わります。 ただし一般的な推奨事項は次のとおりです。

- SQL Server データ ファイルを含む 1 つの大きなボリュームを使用します。 この構成の背景には、現実には I/O ワークロードが異なり、データベース ファイルのサイズが異なる多数の SAP データベースが存在するという理由があります。
- パフォーマンスが十分であれば、tempdb には D:\ ドライブを使用してください。 D:\ ドライブに配置した tempdb によってワークロード全体のパフォーマンスが制限される場合は、[この記事](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)で推奨されているように、tempdb を別の Azure Premium Storage または Ultra Disk のディスクに移動することを検討する必要があります。


### <a name="special-for-m-series-vms"></a>M シリーズ VM のみの特殊な推奨事項
Azure M シリーズ VM で Azure 書き込みアクセラレータを使用すれば、Azure Premium Storage のパフォーマンスに比較して、トランザクション ログへの書き込み待機時間を数分の 1 に短縮できます。 そのため、SQL Server のトランザクション ログ用のボリュームを形成する VHD には Azure 書き込みアクセラレータをデプロイする必要があります。 詳細については、「[Azure 書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)」を参照してください。
  

### <a name="formatting-the-disks"></a>ディスクのフォーマット
SQL Server の場合、SQL Server のデータとログの両方のファイルを含むディスクの NTFS ブロック サイズは 64 KB にする必要があります。 D:\ ドライブをフォーマットする必要はありません。 このドライブはフォーマット済みのものです。

データベースの復元または作成によってデータ ファイルの初期化が実行され、ファイルの内容が消去されないことをことを確認するために、SQL Server サービスが実行されているユーザー コンテキストが特定の権限を持っていることを確認する必要があります。 通常、Windows 管理者グループのユーザーは、これらのアクセス権限を持っています。 Windows 管理者以外のユーザーのユーザー コンテキストで SQL Server サービスが実行されている場合は、 **[ボリュームの保守タスクを実行]** ユーザー権限をそのユーザーに割り当てる必要があります。  Microsoft サポート技術情報の記事 (<https://support.microsoft.com/kb/2574695>) で詳細をご覧ください。

### <a name="impact-of-database-compression"></a>データベースの圧縮の影響
I/O 帯域幅が制限要因になる構成では、IOPS を削減するすべての手段が Azure のような IaaS シナリオで実行できるワークロードを拡張するのに役立つ場合があります。 そのため、まだ実行していない場合、SAP と Microsoft では、既存のデータベースを Azure にアップロードする前に SQL Server のページ圧縮を適用することお勧めします。

Azure にアップロードする前にデータベースの圧縮を実行するための推奨事項は、次の 2 つの理由によるものです。

* アップロードするデータの量が少なくなります。
* オンプレミスよりも CPU が多いまたは I/O 帯域幅が大きい、または I/O 待機時間が少ない強力なハードウェアを使用できるため、圧縮の実行にかかる時間が短くなります。
* 比較的小さなサイズのデータベースでは、ディスク割り当てのコストが少なくなる可能性があります。

データベースの圧縮は、オンプレミスと同様に Azure Virtual Machines でも動作します。 既存の SAP NetWeaver SQL Server データベースを圧縮する方法の詳細については、記事「[Improved SAP compression tool MSSCOMPRESS](/archive/blogs/saponsqlserver/improved-sap-compression-tool-msscompress)」(改善された SAP 圧縮ツール MSSCOMPRESS) を参照してください。 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 以降: Azure Blob Storage にデータベース ファイルを直接格納する
SQL Server 2014 以降では、Azure Blob ストアの周囲に VHD の "ラッパー" を用意しなくても、Azure Blob ストアに直接データベース ファイルを格納することができます。 この種類のデプロイでは、特に Standard Azure Storage またはそれより小さい VM タイプを使用すると、小さい VM タイプでマウントできるディスクの数の制限が適用されることで IOPS が制限されるという問題を解消できます。 この方法のデプロイは、ユーザー データベースでは機能しますが、SQL Server のシステム データベースでは機能しません。 また、SQL Server のデータ ファイルとログ ファイルに対しても機能します。 VHD に "ラッピング" するのではなく、このような方法で SAP SQL Server データベースをデプロイする場合は次の点に留意してください。

* 使用するストレージ アカウントは、SQL Server が実行されている VM をデプロイするために使用したストレージ アカウントと同じ Azure リージョン内にある必要があります。
* 前述の別の Azure Storage アカウントに VHD を分散させることについての考慮事項は、このデプロイ方法の場合にも適用されます。 Azure ストレージ アカウントの制限に対する I/O 操作数を意味します。
* VM のストレージ I/O クォータを考慮する代わりに、SQL Server のデータとログ ファイルを表すストレージ BLOB に対するトラフィックが、特定の VM の種類の VM のネットワーク帯域幅に反映されます。 具体的な VM の種類のネットワークとストレージの帯域幅については、記事「[Azure の Windows 仮想マシンのサイズ](../../sizes.md)」を参照してください。
* ネットワーク クォータを介してファイル I/O をプッシュすると、主にストレージ クォータが孤立し、VM の全体の帯域幅が部分的にしか使用されません。
* Azure Premium Storage がさまざまなディスク サイズに対して持っている IOPS と I/O スループットのパフォーマンス目標は適用されません。 たとえ、作成した BLOB が Azure Premium Storage 上にある場合でもそうです。 目標については、記事「[VM 向けの高パフォーマンスの Premium Storage とマネージド ディスク](../../disks-types.md#premium-ssd)」を参照してください。 Azure Premium Storage に格納されている BLOB に SQL Server のデータ ファイルとログ ファイルを直接配置した結果のパフォーマンスの特性は、Azure Premium Storage 上の VHD と異なる場合があります。
* Azure Premium Storage ディスクで使用できるホスト ベースのキャッシュは、SQL Server データ ファイルを Azure BLOB に直接デプロイするときには使用できません。
* M シリーズの VM では、Azure Write Accelerator を使用して SQL Server トランザクション ログ ファイルに対してミリ秒未満の書き込みをサポートすることはできません。 

この機能の詳細については、記事「[Microsoft Azure 内の SQL Server データ ファイル](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)」を参照してください。

運用システムの推奨事項は、この構成を避け、Azure BLOB 上で直接行うのではなく、Azure Premium Storage VHD に SQL Server のデータとログ ファイルを配置することです。


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 のバッファー プール拡張機能
SQL Server 2014 には、[バッファー プール拡張機能](/sql/database-engine/configure-windows/buffer-pool-extension)という新しい機能が導入されました。 この機能は、サーバーのローカル SSD または VM に基づく第 2 レベルのキャッシュを使用してイン メモリに保持する SQL Server のバッファー プールを拡張します。 バッファー プール拡張機能を使用すると、より大規模なデータのワーキング セットを "メモリ内" に保持することができます。 Azure VM のローカル SSD に格納されるバッファー プールの拡張機能へのアクセスによって、Azure Standard Storage にアクセスする場合よりも、さまざまな部分で高速化されます。 Azure Premium Storage 読み取りキャッシュに対するバッファー プール拡張機能と比較すると、SQL Server データ ファイル向けに推奨されているように、バッファー プール拡張機能に大きな利点はありません。 理由は、両方のキャッシュ (SQL Server バッファー プール拡張機能と Premium Storage 読み取りキャッシュ) が Azure コンピューティング ノードのローカル ディスクを使用しているためです。

これまでに SQL Server バッファー プール拡張機能と SAP ワークロードで得られるエクスペリエンスはさまざまであり、すべてのケースで使用するかどうかについて明確に推奨することはできません。 理想的なケースは、SAP アプリケーションに必要なワーキング セットがメイン メモリに収まることです。 一方、Azure では、最大 4TB のメモリを搭載した VM を提供しているため、メモリ内にワーキング セットを保持することができます。 そのため、バッファー プール拡張機能の使用は一部のまれなケースに限られており、主流のケースではありません。  

## <a name="backuprecovery-considerations-for-sql-server"></a>SQL Server のバックアップ/復旧に関する考慮事項
Azure に SQL Server をデプロイする場合、バックアップ方法を確認する必要があります。 運用システムでない場合でも、SQL Server でホストされている SAP データベースを定期的にバックアップする必要があります。 Azure Storage には 3 つのイメージを保持するため、ストレージの障害から復旧するという点ではバックアップの重要度は低くなりました。 適切なバックアップと復旧の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 

Azure でのさまざまな SQL Server のバックアップ方法については、記事「[Azure Virtual Machines おける SQL Server のバックアップと復元](../../../azure-sql/virtual-machines/windows/backup-restore.md)」を参照してください。 この記事では、いくつかの異なる可能性について説明します。

### <a name="manual-backups"></a>手動バックアップ
次のように、"手動" バックアップを実行するにはいくつかの可能性があります。

1. 直接接続された Azure ディスクに対する従来の SQL Server バックアップの実行。 この方法には、システムの更新に迅速に対応し、既存の SAP システムのコピーとして新しいシステムを構築することができるバックアップがあるという利点があります。
2.  SQL Server 2012 CU4 以降では、データベースを Azure Storage URL にバックアップできます。
3.  Azure Blob Storage のデータベース ファイルのファイルスナップショット バックアップ。 この方法は、SQL Server のデータとログ ファイルが Azure Blob Storage 上にある場合にのみ機能します。

最初の方法はよく知られており、多くの場合、オンプレミス環境でも適用されています。 ただし、長期的なバックアップの場所を解決するという課題が残されています。 ローカルに接続された Azure Storage には 30 日以上バックアップを保存したくないので、バックアップのアクセスとリテンションを管理する機能がある Azure Backup Services または別のサードパーティ製パブリック アクセス/回復ツールを使用する必要があります。 または、Windows 記憶域スペースを使用して Azure 内に大規模なファイル サーバーを構築します。

2 つ目の方法の詳細については、「[SQL Server Backup to URL](../../../azure-sql/virtual-machines/windows/backup-restore.md)」を参照してください。 SQL Server のリリースによっては、この機能にいくつかのバリエーションがあります。 そのため、使用している SQL Server リリースについて、ドキュメントを確認することをお勧めします。 この記事には、多くの制限事項が記載されている点に注意してください。 以下に対してバックアップを実行することができます。

- 1 つの Azure ページ BLOB。この場合、バックアップ サイズは 1,000 GB に制限されます。 この制限により、達成可能なスループットも制限されます。
- 複数の (最大 64 個の) Azure ブロック BLOB。この場合、理論上 12 TB のバックアップ サイズに対応できます。 ただし、顧客データベースを使用したテストでは、最大バックアップ サイズが理論上の制限よりも小さくなる可能性があることがわかりました。 この場合、バックアップの保持とバックアップへのアクセスを管理する必要があります。


### <a name="automated-backup-for-sql-server"></a>SQL Server の自動バックアップ
自動バックアップは、Azure 内の Windows VM で実行されている SQL Server Standard エディションおよび Enterprise エディションの自動バックアップ サービスを提供します。 このサービスは、Azure portal で SQL Server Windows 仮想マシン イメージに自動的にインストールされる [SQL Server IaaS Agent 拡張機能](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)によって提供されます。 SQL Server がインストールされた独自の OS イメージをデプロイする場合は、VM 拡張機能を個別にインストールする必要があります。 必要な手順については、[こちらの記事](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)を参照してください。

この方法の機能の詳細については、次の記事を参照してください。

- SQL Server 2014: [SQL Server 2014 Virtual Machines の自動バックアップ (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
- SQL Server 2016 および 2017: [Azure Virtual Machines の自動バックアップ v2 (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup.md)

ドキュメントを読むと、新しい SQL Server リリースでは機能が向上していることがわかります。 SQL Server の自動バックアップの詳細については、記事「[Microsoft Azure への SQL Server マネージド バックアップ](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)」を参照してください。 理論上のバックアップ サイズの制限は 12 TB です。  自動バックアップは、最大 12 TB のバックアップ サイズに適している方法です。 複数の BLOB が並行して書き込まれるため、100 MB/秒を超えるスループットが期待できます。 
 

### <a name="azure-backup-for-sql-server-vms"></a>SQL Server VM 用の Azure Backup
2018 年 6 月現在、この SQL Server の新しいバックアップ方法は、Azure Backup Services によるパブリック プレビューとして提供されています。 SQL Server をバックアップする方法は、他のサード パーティ製ツールが、SQL Server VSS/VDI インターフェイスなどを使用してバックアップを対象の場所にストリーミングする方法と同じです。 この場合、対象の場所は Azure Recovery Service コンテナーです。

このバックアップ方法の詳細な説明と、中央のバックアップ構成、監視、管理のさまざまな利点については、[こちら](../../../backup/backup-azure-sql-database.md)を参照してください。 


### <a name="third-party-backup-solutions"></a>サード パーティのバックアップ ソリューション
非常に多数の SAP ユーザーにとって、Azure 上で実行されていた SAP ランドスケープの部分に、完全に新しいバックアップ ソリューションを一から導入することはできませんでした。 その結果、既存のバックアップ ソリューションを使用して Azure に拡張する必要がありました。 通常、この分野の主要ベンダーの大部分では、既存のバックアップ ソリューションを Azure に拡張できました。 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Microsoft Azure Marketplace からの SQL Server イメージの使用
マイクロソフトは Azure Marketplace で VM を提供しています。VM には SQL Server のバージョンがすでに含まれています。 SQL Server および Windows のライセンスが必要な SAP ユーザーの場合、これらのイメージを使用すると、SQL Server が既にインストールされている VM をスピン アップしてライセンスの必要性に対応できる可能性があります。 SAP でそのようなイメージを使用するためには、次の事項を考慮する必要があります。

* SQL Server 評価版以外のバージョンでは、Azure Marketplace から "Windows のみ" の VM をデプロイする場合よりもコストがかかります。 価格を比較する場合は、<https://azure.microsoft.com/pricing/details/virtual-machines/windows/> および <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> を参照してください。 
* SAP でサポートされている SQL Server のリリースのみを使用できます。
* Azure Marketplace が提供する VM にインストールされている SQL Server インスタンスの照合順序は、SAP NetWeaver が SQL Server インスタンスの実行に要求する照合順序ではありません。 以降のセクションの指示で、照合順序を変更できます。

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Microsoft Windows または SQL Server VM の SQL Server の照合順序を変更する
Azure Marketplace の SQL Server イメージは SAP NetWeaver アプリケーションで必要な照合順序を使用するようにセットアップされていないため、デプロイ後すぐに変更する必要があります。 SQL Server の場合、VM がデプロイされ、管理者がデプロイされた VM にログインできるようになった時点で、次の手順でこの照合順序の変更を実行できます。

* 管理者として Windows コマンド ウィンドウを開きます。
* ディレクトリを C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012 に変更します。
* 次のコマンドを実行します。Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> は、ギャラリーを使用して最初に VM をデプロイするときに Administrator アカウントとして定義されたアカウントです。

処理にかかるのは、わずか数分間です。 この手順で正しい結果が得られるかどうかを確認するには、次の手順を実行します。

* SQL Server Management Studio を開きます。
* [クエリ] ウィンドウを開きます。
* SQL Server マスター データベースで sp_helpsort コマンドを実行します。

結果が、次のように表示されます。

```output
Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data
```

正しい結果でない場合は、SAP のデプロイを中止し、setup コマンドが期待通りに動作しなかった原因を調査します。 上記以外の SQL Server コード ページを持つ SQL Server インスタンスへの SAP NetWeaver アプリケーションのデプロイは、サポートされて **いません** 。

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Azure の SAP 向け SQL Server 高可用性
SAP 用の Azure IaaS デプロイで SQL Server を使用する場合、高可用な DBMS レイヤーをデプロイできる追加の可能性がいくつかあります。 「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」で既に説明したように、Azure では、単一の VM と、Azure 可用性セットにデプロイされている VM のペアに対して、異なるアップタイムの SLA が用意されています。 Azure 可用性セットへのデプロイが必要な運用デプロイ向けのアップタイムの SLA を目標としているとします。 この場合、このような可用性セットには最低 2 つの VM をデプロイする必要があります。 1 つの VM がアクティブな SQL Server インスタンスを実行します。 もう一方の VM はパッシブ インスタンスを実行します。

### <a name="sql-server-clustering-using-windows-scale-out-file-server-or-azure-shared-disk"></a>Windows スケールアウト ファイル サーバーまたは Azure 共有ディスクを使用する SQL Server クラスタリング
Microsoft は Windows Server 2016 で[記憶域スペース ダイレクト](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)を導入しました。 記憶域スペース ダイレクトのデプロイに基づいて、SQL Server FCI クラスタリングは一般的にサポートされています。 Azure では、Windows クラスタリングに使用できる [Azure 共有ディスク](../../disks-shared-enable.md?tabs=azure-cli)も提供されています。 SAP ワークロードに対しては、これらの HA オプションはサポートされていません。 

### <a name="sql-server-log-shipping"></a>SQL Server ログ配布
高可用性 (HA) のメソッドの 1 つは、SQL Server ログ配布です。 HA 構成に参加している VM で名前解決が機能している場合、まったく問題はなく、Azure でのセットアップはオンプレミスで行われるセットアップとまったく変わりません。 ログ配布のセットアップとログ配布の指針については、 記事「[ログ配布について (SQL Server)](/sql/database-engine/log-shipping/about-log-shipping-sql-server)」で SQL Server のログ配布の詳細を確認できます。

1 つの Azure リージョン内で高可用を達成する目的で、SQL Server のログ配布機能が Azure で使用されることはほとんどありませんでした。 ただし、以下のシナリオでは、SAP ユーザーは Azure と適切に連携してログ配布を使用していました。

- ある Azure リージョンから別の Azure リージョンへのディザスター リカバリー シナリオ
- オンプレミスから Azure リージョンへのディザスター リカバリー構成
- オンプレミスから Azure への切り替えシナリオ これらの場合、Azure 内の新しい DBMS のデプロイを、実行中のオンプレミスの運用システムと同期させるためにログ配布が使用されます。 切り替えの時点で、運用はシャットダウンされるので、最後で最新のトランザクション ログのバックアップが Azure DBMS のデプロイに確実に転送されます。 次に、Azure DBMS のデプロイが運用のために開かれます。  



### <a name="database-mirroring"></a>データベース ミラーリング
SAP でサポートされているデータベース ミラーリング (SAP Note [965908] 参照) は、SAP 接続文字列でのフェールオーバー パートナーの定義に依存しています。 クロスプレミスの場合、同じドメインに 2 つの VM があり、2 つの SQL Server インスタンスが実行されているユーザー コンテキストがドメイン ユーザーであると想定しています。また、関係する 2 つの SQL Server インスタンスのための十分な権限を持っているという前提です。 そのため、Azure でのデータベース ミラーリングのセットアップは、典型的なオンプレミスのセットアップ/構成と違いはありません。

クラウドのみのデプロイの時点で、最も簡単な方法は 1 つのドメイン内にそれらの DBMS VM (および理想的には専用の SAP VM) を持つことができるように Azure で別のドメインをセットアップすることです。

ドメインをセットアップできない場合は、<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql> に記載されているように、データベース ミラーリング エンドポイントの証明書を使用することもできます。

Azure でのデータベース ミラーリングのセットアップのチュートリアルについては、<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> を参照してください。 

### <a name="sql-server-always-on"></a>SQL Server AlwaysOn
AlwaysOn は SAP オンプレミスでサポートされており (SAP Note [1772688] を参照)、この機能は Azure の SAP との組み合わせでサポートされています。 オンプレミスでは可能ですが、現時点で Azure では AD/DNS オブジェクトを作成できないため、SQL Server 可用性グループ リスナー (Azure 可用性セットと混同しないこと) のデプロイに関する特別な考慮事項があります。 そのため、Azure 固有の動作に対応するために、いくつかの別のインストール手順が必要です。

可用性グループ リスナーを使用する場合の考慮事項は次のとおりです。

* 可用性グループ リスナーは、Windows Server 2012 以上を VM のゲスト OS として使用する場合にのみ使用できます。 Windows Server 2012 の場合、このパッチ (<https://support.microsoft.com/kb/2854082>) が適用されていることを確認する必要があります。 
* Windows Server 2008 R2 の場合、このパッチは存在せず、AlwaysOn は、接続文字列にフェールオーバー パートナーを指定するというデータベース ミラーリングと同じ方法で使用する必要があります (SAP default.pfl パラメーター dbs/mss/server によって実行されます。SAP Note [965908] を参照)。
* 可用性グループ リスナーを使用する場合、データベースの VM を専用のロード バランサーに接続する必要があります。 両方の VM が意図せずシャットダウンされた場合に Azure で新しい IP アドレスが割り当てられるのを回避するため、AlwaysOn 構成の VM のネットワーク インターフェイスに静的 IP アドレスを割り当てる必要があります (静的 IP アドレスの定義については[こちら][virtual-networks-reserved-private-ip]の記事を参照)。
* Azure の現在の機能では、クラスター名にクラスターが作成されたノードと同じ IP アドレスを割り当てるため、WSFC クラスター構成を構築してそのクラスターに特定の IP アドレスを割り当てる必要がある場合、特別な手順が必要です。 この動作は、クラスターに別の IP アドレスを割り当てるためには手動の手順を行う必要があることを意味します。
* TCP/IP エンドポイントを使用して Azure に可用性グループ リスナーを作成しようとしています。TCP/IP エンドポイントは、可用性グループのプライマリ レプリカとセカンダリ レプリカを実行している VM に割り当てられています。
* これらのエンドポイントと ACL をセキュリティで保護する必要があります。

Azure VM の SQL Server で Always On をデプロイする方法の詳細については、以下のドキュメントを参照してください。

- [Azure Virtual Machines での SQL Server Always On 可用性グループの概要](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)。
- [さまざまな地域に存在する Azure 仮想マシンに Always On 可用性グループを構成します](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)｡
- [Azure の AlwaysOn 可用性グループに使用するロード バランサーの構成](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)。

>[!NOTE]
> 可用性グループ リスナーの仮想 IP アドレスに Azure Load Balancer を構成する場合は、DirectServerReturn が構成されていることを確認します。 このオプションを構成すると、SAP アプリケーション レイヤーと DBMS レイヤーの間のネットワーク ラウンド トリップの待機時間が短縮されます。 

SQL Server Always On は、Azure for SAP ワークロードのデプロイで使用される最も一般的な高可用性とディザスター リカバリー機能です。 ほとんどのユーザーは、単一の Azure リージョン内で高可用性のために Always On を使用しています。 デプロイが 2 つのノードのみに制限されている場合、接続には 2 つの選択肢があります。

- 可用性グループ リスナーを使用します。 可用性グループ リスナーを使用する場合、Azure Load Balancer をデプロイする必要があります。 この方法は、既定のデプロイ方法です。 SAP アプリケーションは、単一のノードとではなく、可用性グループ リスナーと接続するように構成されます。
- SQL Server データベース ミラーリングの接続パラメーターを使用します。 この場合、両方のノードに名前を付ける方法で SAP アプリケーションの接続を構成する必要があります。 このような SAP 側構成の詳細については、SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908) を参照してください。 このオプションを使用する場合、可用性グループ リスナーを構成する必要はありません。 また、SQL Server の高可用性のために Azure Load Balancer を構成する必要はありません。 その結果、SQL Server インスタンスへの着信トラフィックは Azure Load Balancer 経由でルーティングされないので、SAP アプリケーション レイヤーと DBMS レイヤーの間のネットワーク待機時間は短くなります。 ただし、このオプションは、可用性グループを 2 つのインスタンスにまたがるように制限する場合にのみ機能する点に注意してください。 

Azure リージョン間に追加のディザスター リカバリー機能のために SQL Server Always On 機能を利用しているユーザーはごく少数です。 一部のユーザーは、セカンダリ レプリカからバックアップを実行する機能も使用しています。 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server Transparent Data Encryption
Azure に SAP SQL Server データベースをデプロイするときに、SQL Server [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) を使用しているユーザーは多数います。 SQL Server TDE 機能は SAP によって完全にサポートされています (SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493) を参照)。 

### <a name="applying-sql-server-tde"></a>SQL Server TDE の適用
オンプレミスで実行している別の DBMS から Azure で実行されている Windows/SQL Server に対する異種移行を実行する場合、事前に SQL Server 内に空のターゲット データベースを作成する必要があります。 次の手順では、SQL Server TDE の機能を適用します。 この手順は、まだオンプレミスで運用システムを実行している間に行います。 この順序で実行する理由は、空のデータベースを暗号化するプロセスにはかなりの時間がかかるためです。 次に、SAP インポート プロセスで、ダウンタイム フェーズ中にデータを暗号化されたデータベースにインポートします。 暗号化されたデータベースにインポートする場合のオーバーヘッドは、エクスポート フェーズ後にダウン タイム フェーズでデータベースを暗号化する場合よりも、時間に対する影響が少なくなります。 データベース上で実行されている SAP ワークロードで TDE を適用しようとすると、否定的なエクスペリエンスが発生しました。 そのため、TDE のデプロイは、特定のデータベース上の SAP ワークロードを使用せずに実行する必要があるアクティビティとして扱うことをお勧めします。

SAP SQL Server データベースをオンプレミスから Azure に移行する場合は、暗号化を最速で適用できるインフラストラクチャをテストすることをお勧めします。 この場合、以下の点に注意してください。

- データベースにデータの暗号化を適用するために使用されるスレッドの数は定義できません。 スレッド数は、主に SQL Server のデータとログ ファイルが分散されるディスク ボリュームの数によって変わります。 つまり、個別のボリューム (ドライブ文字) が多くなるほど、暗号化の実行に並行して使用されるスレッドが多くなります。 このような構成は、Azure VM の SQL Server データベース ファイル用の記憶域スペースを 1 つまたは少数を構築する場合の前述したディスク構成案とはやや矛盾しています。 ボリュームが少数の構成では、暗号化を実行するスレッド数が少なくなります。 単一スレッドの暗号化では、64 KB のエクステントを読み取り、暗号化してから、エクステントが暗号化されたことを示すレコードがトランザクション ログ ファイルに書き込まれます。 その結果、トランザクション ログにかかる負荷は中程度です。
- 以前の SQL Server リリースでは、SQL Server データベースを暗号化するときにバックアップの圧縮は効率化されませんでした。 オンプレミスの SQL Server データベースを暗号化してから Azure にバックアップをコピーして Azure でデータベースを復元する予定の場合、この動作が問題になる可能性があります。 SQL Server のバックアップ圧縮では、通常、4 倍の圧縮率を達成できます。
- SQL Server 2016 では、暗号化されたデータベースを効率的に圧縮できる新しい機能が導入されました。 詳細については、[こちらのブログ](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases)を参照してください。
 
SAP ワークロードがない、またはほとんどない TDE 暗号化のアプリケーションを扱う場合、SAP データベースに TDE を適用する処理をオンプレミスで実行するか、Azure で実行するかについて、実際の構成でテストすることをお勧めします。 TDE が適用された後のインフラストラクチャのオーバープロビジョニングとインフラストラクチャの縮小という点で、Azure の方が柔軟です。

### <a name="using-azure-key-vault"></a>Azure Key Vault の使用
Azure は、暗号キーを格納する [Key Vault](https://azure.microsoft.com/services/key-vault/) のサービスを提供しています。 一方、SQL Server は、TDE 証明書のストアとして Azure Key Vault を利用するコネクタを用意しています。

Azure Key Vault を SQL Server TDE に使用する方法の詳細については、以下のドキュメントを参照してください。

- [Azure Key Vault を使用する拡張キー管理 (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)。
- [Azure Key Vault を使用する SQL Server TDE 拡張キー管理 - 設定手順](/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault)
- [SQL Server コネクタのメンテナンスとトラブルシューティング](/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting)
- [SQL Server Transparent Data Encryption について寄せられるその他の質問 - TDE + Azure Key Vault](/archive/blogs/saponsqlserver/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault)。


>[!IMPORTANT]
>SQL Server TDE を使用する場合、特に Azure Key Vault と共に使用する場合は、SQL Server 2014、SQL Server 2016、および SQL Server 2017 の最新のパッチを使用することをお勧めします。 その理由は、ユーザーのフィードバックに基づいて、最適化と修正がコードに適用されているためです。 例については、[KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm) を参照してください。
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Azure での一般的な SAP 用 SQL Server の概要
このガイドには多くの推奨事項が記載されているため、Azure デプロイを計画する前に 2 回以上読むことをお勧めします。 ただし、Azure 上の DBMS に固有の主な一般的な推奨事項に従ってください。

1. Azure で多くのメリットのある SQL Server 2017 のように、最新の DBMS リリースを使用する。 
2. データ ファイルのレイアウトと Azure の制限事項のバランスをとるために、Azure での SAP システム ランドスケープを慎重に計画します。
   * あまり多くはないが、必要な IOPS を達成するには十分な数のディスクがある。
   * Managed Disks を使用しない場合、IOPS は Azure Storage アカウントごとに制限され、Storage のアカウントは各 Azure サブスクリプション内に制限されることに注意する ([詳細][azure-resource-manager/management/azure-subscription-service-limits])。 
   * 高いスループットを実現する必要がある場合にのみディスク間でストライピングします。
3. D:\ ドライブにソフトウェアをインストールおよび保持する必要があるファイルを置かない。このドライブは非永続的であるため、Windows の再起動時にすべて失われます。
4. Azure Standard Storage でディスク キャッシュを使用しない。
5. Azure Standard ストレージ アカウントの Azure Geo レプリケーションを使用しない。  DBMS ワークロードにローカル冗長を使用する。
6. DBMS ベンダーの HA/DR ソリューションを使用して、データベースのデータをレプリケートする。
7. 常に名前解決を使用して、IP アドレスに依存しないようにする。
8. SQL Server TDE を使用して、最新の SQL Server パッチを適用する。
9. 考えられる最高のデータベースの圧縮を使用する。 SQL Server の場合はページ圧縮。
10. Azure Marketplace の SQL Server イメージを使用するよう注意してください。 いずれかの SQL Server を使用する場合は、SAP NetWeaver システムをインストールする前にインスタンスの照合順序を変更する必要があります。
11. [デプロイ ガイド][deployment-guide]に従い、SAP Host Monitoring for Azure をインストールして構成します。


## <a name="next-steps"></a>次の手順
記事を読む 

- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)