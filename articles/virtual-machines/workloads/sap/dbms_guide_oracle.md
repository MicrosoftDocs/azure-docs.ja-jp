---
title: SAP ワークロードのための Oracle Azure Virtual Machines DBMS のデプロイ | Microsoft Docs
description: SAP ワークロードのための Oracle Azure Virtual Machines DBMS のデプロイ
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP、Azure、Oracle、Data Guard
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/18/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29b1bcec58d6350d0f63c3fe0ce11ef99a648019
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668974"
---
# <a name="azure-virtual-machines-oracle-dbms-deployment-for-sap-workload"></a>SAP ワークロードのための Azure Virtual Machines Oracle DBMS のデプロイ

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


このドキュメントでは、Azure IaaS に SAP ワークロードのために Oracle Database をデプロイするときに考慮すべきいくつかの異なる領域について説明します。 このドキュメントを読む前に、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」を読むことをお勧めします。 また、[Azure での SAP ワークロードのドキュメント](./get-started.md)の他のガイドを読むこともお勧めします。 

Azure で SAP on Oracle を実行できる Oracle のバージョンと対応する OS のバージョンに関しては、SAP Note [2039619] をご覧ください。

Oracle での SAP Business Suite の実行に関する一般的な情報については、[SAP on Oracle](https://www.sap.com/community/topic/oracle.html) を参照してください。
Oracle ソフトウェアを Microsoft Azure 上で実行できるようになりました。 Windows Hyper-V と Azure の一般的なサポートの詳細については、「[Oracle and Microsoft Azure FAQ (Oracle と Microsoft Azure に関する FAQ)](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)」をご覧ください。 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Oracle、SAP、Azure に関連する SAP Note 

次の SAP Note は、SAP on Azure に関連します。

| Note 番号 | タイトル |
| --- | --- |
| [1928533] |SAP Applications on Azure:Supported products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類) |
| [2015553] |SAP on Microsoft Azure:Support prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [1999351] |Troubleshooting enhanced Azure monitoring for SAP (強化された Azure Monitoring for SAP のトラブルシューティング) |
| [2178632] |Key monitoring metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [2191498] |SAP on Linux with Azure:Enhanced monitoring (Azure を使用した Linux 上の SAP: 拡張された監視機能) |
| [2039619] |SAP applications on Microsoft Azure using the Oracle database:Supported products and versions (Oracle データベースを使用した Microsoft Azure 上の SAP アプリケーション: サポートされている製品とバージョン) |
| [2243692] |Linux on Microsoft Azure (IaaS) VM:SAP license issues (Microsoft Azure (IaaS) VM 上の Linux: SAP ライセンスの問題) |
| [2069760] |Oracle Linux 7.x SAP installation and upgrade (Oracle Linux 7.x SAP のインストールとアップグレード) |
| [1597355] |Linux のスワップ領域に関する推奨事項 |
| [2171857] |Oracle Database 12c - Linux でサポートされているファイル システム |
| [1114181] |Oracle Database 11g - Linux でサポートされているファイル システム |

Oracle on Azure と SAP on Azure がサポートする正確な構成と機能については、SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619) を参照してください。

Oracle on Azure と SAP on Azure でサポートされているオペレーティング システムは、Windows と Oracle Linux のみです。 Azure に Oracle コンポーネントをデプロイする場合、広く使用されている SLES および RHEL Linux のディストリビューションはサポートされていません。 Oracle コンポーネントには、Oracle Database クライアントが含まれています。このクライアントは、SAP アプリケーションが Oracle DBMS に接続するために使用されます。 

SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619) によると、例外は SAP コンポーネントであり、これは Oracle Database クライアントを使用していません。 このような SAP コンポーネントは、SAP のスタンドアロン エンキュー、メッセージ サーバー、エンキュー レプリケーション サービス、WebDispatcher、SAP ゲートウェイです。  

Oracle Linux 上で Oracle DBMS および SAP アプリケーション インスタンスを実行していても、SLES または RHEL 上で SAP Central Services を実行し、Pacemaker ベースのクラスターで保護することができます。 高可用性フレームワークとしての Pacemaker は Oracle Linux ではサポートされていません。

## <a name="specifics-for-oracle-database-on-windows"></a>Windows 上の Oracle Database の詳細

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Windows 上の Azure VM で SAP をインストールするための Oracle 構成ガイドライン

SAP インストール マニュアルによると、Oracle 関連のファイルすべては、VM の OS ディスク (ドライブ C:) にインストールしたり、配置したりすべきではありません。 異なるサイズの仮想マシンでは、異なる数の接続されたディスクをサポートできます。 仮想マシンの種類が小さければ小さいほど、サポートできる接続されたディスクの数も少なくなります。 

VM の数が少ない場合に VM に接続できるディスク数の上限に達すると、Oracle home、stage、`saptrace`、`saparch`、`sapbackup`、`sapcheck`、または `sapreorg` を OS ディスクにインストール/配置できます。 これらの Oracle DBMS コンポーネントが I/O と I/O スループットに与える影響はあまり大きくありません。 そのため、OS ディスクで I/O 要件を処理できます。 OS ディスクの既定のサイズは、127 GB です。 

Oracle Database と再実行フェーズのログ ファイルは別々のデータ ディスクに格納する必要があります。 Oracle 一時テーブル スペースには例外があります。 `Tempfiles` を D:/ (非永続ドライブ) に作成できます。 非永続ドライブの D:\ は、(A シリーズの VM を除き) I/O 待機時間とスループットが優れています。 

`tempfiles` に適した領域の量を決定するには、既存のシステムで `tempfiles` のサイズを確認することができます。

### <a name="storage-configuration"></a>ストレージの構成
NTFS でフォーマットされたディスクを使用した単一インスタンスの Oracle のみサポートされています。 すべてのデータベース ファイルは、Managed Disks (推奨) または VHD 上の NTFS ファイル システムに保存する必要があります。 これらのディスクは Azure VM にマウントされており、[Azure ページ BLOB ストレージ](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)または [Azure Managed Disks](../../managed-disks-overview.md) に基づいています。 

「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」の記事を参照して、DBMS ワークロードに適した特定の Azure ブロック ストレージの種類の詳細を確認してください。

[Azure Managed Disks](../../managed-disks-overview.md) の使用を強くお勧めします。 また、Oracle Database デプロイの場合には [Azure Premium Storage または Azure Ultra Disk](../../disks-types.md) の使用も強くお勧めします。

ネットワーク ドライブまたは Azure ファイル サービスのようなリモート共有は、Oracle Database ファイルに対してはサポートされていません。 詳細については、次を参照してください。

- [Microsoft Azure File サービスの概要](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Microsoft Azure Files への接続の維持](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」に記載されているステートメントは、Azure ページ BLOB ストレージをベースとするディスクまたは Managed Disks を使用した Oracle Database のデプロイにも適用されます。

Azure ディスクに対する IOPS スループットにはクォータが存在します。 この概念については、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」を参照してください。 正確なクォータは使用する VM タイプによって異なります。 VM タイプとそのクォータの一覧は、「[Azure の Windows 仮想マシンのサイズ][virtual-machines-sizes-windows]」に記載されています。

サポートされている Azure VM のタイプを識別するには、SAP Note [1928533] をご覧ください。

最小の構成は次のとおりです。 

| コンポーネント | ディスク | キャッシュ | 記憶域プール |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Premium または Ultra Disk | なし | 不要 |
| \oracle\<SID>\origlogaB & mirrlogA | Premium または Ultra Disk | なし | 不要 |
| \oracle\<SID>\sapdata1...n | Premium または Ultra Disk | 読み取り専用 | Premium に対して使用できます |
| \oracle\<SID>\oraarch | Standard | なし | 不要 |
| Oracle Home、`saptrace`、... | OS ディスク (Premium) | | 不要 |


オンラインの再実行ログをホストするためのディスクの選択は、IOPS 要件に従う必要があります。 サイズ、IOPS、スループットの要件を満たしている限りは、1 つのマウント ディスク上で sapdata1...n (テーブルスペース) すべてを格納できます。 

パフォーマンスの構成は次のとおりです。

| コンポーネント | ディスク | キャッシュ | 記憶域プール |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | Premium または Ultra Disk | None | Premium に対して使用できます  |
| \oracle\<SID>\origlogaB | Premium または Ultra Disk | None | Premium に対して使用できます |
| \oracle\<SID>\mirrlogAB | Premium または Ultra Disk | None | Premium に対して使用できます |
| \oracle\<SID>\mirrlogBA | Premium または Ultra Disk | None | Premium に対して使用できます |
| \oracle\<SID>\sapdata1...n | Premium または Ultra Disk | 読み取り専用 | Premium に推奨  |
| \oracle\SID\sapdata(n+1)* | Premium または Ultra Disk | None | Premium に対して使用できます |
| \oracle\<SID>\oraarch* | Premium または Ultra Disk | なし | 不要 |
| Oracle Home、`saptrace`、... | OS ディスク (Premium) | 不要 |

\* (n+1): SYSTEM、TEMP、UNDO の各テーブルスペースをホストします。 SYSTEM、UNDO テーブルスペースの I/O パターンは、アプリケーション データをホストする他のテーブルスペースとは異なります。 SYSTEM と UNDO テーブルスペースのパフォーマンスを考慮すると、キャッシュなしが最適なオプションです。

\* oraarch: パフォーマンスの観点からは、記憶域プールは必要ありません。 より多くの領域を確保するために使用できます。

Azure Premium Storage で高い IOPS が必要な場合は、Windows 記憶域プール (Windows Server 2012 以降でのみ提供) を使用して、マウントされた複数のディスクの上に 1 つの大きな論理デバイスを作成することをお勧めします。 この方法では、ディスク領域を管理する管理オーバーヘッドを合理化し、マウントされた複数のディスク全体にファイルを手動で分散する手間を省きます。


#### <a name="write-accelerator"></a>書き込みアクセラレータ
Azure M シリーズ VM では、Azure Premium Storage と比較して、オンラインの再実行ログへの書き込み待機時間を数分の 1 に短縮できます。 オンラインの再実行ログ ファイルに使用される、Azure Premium Storage に基づくディスク (VHD) では、Azure 書き込みアクセラレータを有効にします。 詳しくは、「[書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)」をご覧ください。 または、Azure Ultra Disk をオンラインの再実行ログ ボリュームに使用します。


### <a name="backuprestore"></a>バックアップ/復元
バックアップと復元機能については、SAP BR*Tools for Oracle が標準の Windows Server オペレーティング システムと同様にサポートされています。 ディスクへのバックアップとディスクからの復元については Oracle Recovery Manager (RMAN) もサポートされます。

Azure Backup を使用して、VM のアプリケーション整合性バックアップを実行することもできます。 記事「[Azure における VM バックアップ インフラストラクチャの計画を立てる](../../../backup/backup-azure-vms-introduction.md)」では、Azure Backup でアプリケーション整合性バックアップを実行するために Windows VSS 機能を使用する方法が説明されています。 SAP によって Azure でサポートされている Oracle DBMS リリースでは、バックアップに VSS 機能を利用できます。 詳細については、Oracle のドキュメント「[Basic concepts of database backup and recovery with VSS (VSS を使用したデータベースのバックアップと復元の基本概念)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701)」を参照してください。


### <a name="high-availability"></a>高可用性
高可用性とディザスター リカバリーを目的として Oracle Data Guard がサポートされています。 Data Guard で自動フェールオーバーを実現するには、ファスト スタート フェールオーバー (FSFA) を使用することが必要です。 オブザーバー (FSFA) によってフェールオーバーがトリガーされます。 FSFA を使用しない場合は、手動フェールオーバー構成のみを使用できます。

Azure の Oracle データベースのディザスター リカバリーについて詳しくは、「[Azure 環境内の Oracle Database 12c データベースのディザスター リカバリー](../oracle/oracle-disaster-recovery.md)」をご覧ください。

### <a name="accelerated-networking"></a>Accelerated Networking
Windows への Oracle のデプロイでは、「[Azure accelerated networking (Azure 高速ネットワーク)](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)」で説明されているように高速ネットワークを強くお勧めします。 また、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」に記載されている推奨事項を検討してください。 
### <a name="other"></a>その他
「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」では、Azure 可用性セットや SAP の監視など、Oracle Database を使用した VM のデプロイに関係のある他の重要な概念が説明されています。

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Oracle Linux 上の Oracle Database の詳細
ゲスト OS として Oracle Linux を使用して、Oracle ソフトウェアを Microsoft Azure 上で実行できるようになりました。 Windows Hyper-V と Azure の一般的なサポートの詳細については、「[Azure and Oracle FAQ (Azure と Oracle に関する FAQ)](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)」をご覧ください。 

Oracle Database を活用する SAP アプリケーションの特定のシナリオも同様にサポートされます。 詳細については、ドキュメントの次の部分で説明します。

### <a name="oracle-version-support"></a>Oracle バージョンのサポート
Azure Virtual Machines で SAP on Oracle を実行できる Oracle のバージョンと対応する OS のバージョンについて詳しくは、SAP Note [2039619] をご覧ください。

Oracle での SAP Business Suite の実行に関する一般的な情報については、[SAP on Oracle のコミュニティ ページ](https://www.sap.com/community/topic/oracle.html)を参照してください。

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Linux 上の Azure VM で SAP をインストールするための Oracle 構成ガイドライン

SAP インストール マニュアルによると、Oracle 関連のファイル、VM のブート ディスクのシステム ドライバーにインストールしたり、配置したりすべきではありません。 異なるサイズの仮想マシンでは、異なる数の接続されたディスクがサポートされます。 仮想マシンの種類が小さければ小さいほど、サポートできる接続されたディスクの数も少なくなります。 

この場合、Oracle home、stage、`saptrace`、`saparch`、`sapbackup`、`sapcheck`、または `sapreorg` をブート ディスクにインストール/配置することをお勧めします。 これらの Oracle DBMS コンポーネントが I/O と I/O スループットに与える影響は大きくありません。 そのため、OS ディスクで I/O 要件を処理できます。 OS ディスクの既定のサイズは、30 GB です。 Azure portal、PowerShell、CLI を使用してブート ディスクを拡張できます。 ブート ディスクを拡張した後は、Oracle バイナリのためにパーティションを追加できます。


### <a name="storage-configuration"></a>ストレージの構成

ext4、xfs、NFSv4.1 (Azure NetApp Files (ANF) のみ) または Oracle ASM (リリース/バージョンの要件については、SAP ノート [#2039619](https://launchpad.support.sap.com/#/notes/2039619) を参照) のファイル システムが、Azure の Oracle Database ファイルに対してサポートされています。 すべてのデータベース ファイルを、VHD、Managed Disks、または ANF をベースとするこれらのファイル システムに保存する必要があります。 これらのディスクは Azure VM にマウントされ、[Azure ページ BLOB ストレージ](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)、[Azure Managed Disks](../../managed-disks-overview.md)、または [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) に基づきます。

最小要件を以下に示します。 

- Oracle Linux UEK カーネルでは、[Azure Premium SSD](../../premium-storage-performance.md#disk-caching) をサポートするには少なくとも UEK バージョン 4 が必要です。
- ANF を使用する Oracle の場合、サポートされる最小の Oracle Linux は 8.2 です。
- ANF を使用する Oracle の場合、サポートされる最小の Oracle バージョンは 19c (19.8.0.0) です。

「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」の記事を参照して、DBMS ワークロードに適した特定の Azure ブロック ストレージの種類の詳細を確認してください。

Azure ブロック ストレージを使用する場合、Oracle Database のデプロイのために [Azure Managed Disks](../../managed-disks-overview.md) と [Azure Premium SSD](../../disks-types.md) を使用することを強くお勧めします。

Azure NetApp Files を除く他の共有ディスク、ネットワーク ドライブ、または Azure File サービス (AFS) のようなリモート共有は、Oracle Database ファイルに対してはサポートされていません。 詳細については、「 

- [Microsoft Azure File サービスの概要](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Microsoft Azure Files への接続の維持](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

Azure ページ BLOB ストレージまたは Managed Disks に基づくディスクを使用している場合、「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」に記載されているステートメントは、Oracle Database でのデプロイにも適用されます。

Azure ディスクに対する IOPS スループットにはクォータが存在します。 この概念については、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」を参照してください。正確なクォータは使用される VM タイプによって異なります。 VM タイプとそのクォータの一覧については、「[Azure の Linux 仮想マシンのサイズ][virtual-machines-sizes-linux]」をご覧ください。

サポートされている Azure VM のタイプを識別するには、SAP Note [1928533] をご覧ください。

最小構成:

| コンポーネント | ディスク | キャッシュ | ストライプ化* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA & mirrlogB | Premium、Ultra Disk、または ANF | なし | 不要 |
| /oracle/\<SID>/origlogaB & mirrlogA | Premium、Ultra Disk、または ANF | なし | 不要 |
| /oracle/\<SID>/sapdata1...n | Premium、Ultra Disk、または ANF | 読み取り専用 | Premium に対して使用できます |
| /oracle/\<SID>/oraarch | Standard または ANF | なし | 不要 |
| Oracle Home、`saptrace`、... | OS ディスク (Premium) | | 不要 |

*ストライプ化: RAID0 を使用した LVM ストライプまたは MDADM

Oracle のオンラインの再実行ログをホストするためのディスクの選択は、IOPS 要件に従う必要があります。 ボリューム、IOPS、スループットの要件を満たしている限りは、1 つのマウント ディスク上の sapdata1...n (テーブルスペース) すべてを格納できます。 

パフォーマンス構成:

| コンポーネント | ディスク | キャッシュ | ストライプ化* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA | Premium、Ultra Disk、または ANF | None | Premium に対して使用できます  |
| /oracle/\<SID>/origlogaB | Premium、Ultra Disk、または ANF | None | Premium に対して使用できます |
| /oracle/\<SID>/mirrlogAB | Premium、Ultra Disk、または ANF | None | Premium に対して使用できます |
| /oracle/\<SID>/mirrlogBA | Premium、Ultra Disk、または ANF | None | Premium に対して使用できます |
| /oracle/\<SID>/sapdata1...n | Premium、Ultra Disk、または ANF | 読み取り専用 | Premium に推奨  |
| /oracle/\<SID>/sapdata(n+1)* | Premium、Ultra Disk、または ANF | None | Premium に対して使用できます |
| /oracle/\<SID>/oraarch* | Premium、Ultra Disk、または ANF | なし | 不要 |
| Oracle Home、`saptrace`、... | OS ディスク (Premium) | 不要 |

*ストライプ化: RAID0 を使用した LVM ストライプまたは MDADM

\* (n+1): SYSTEM、TEMP、UNDO の各テーブルスペースをホストします。SYSTEM、UNDO テーブルスペースの I/O パターンは、アプリケーション データをホストする他のテーブルスペースとは異なります。 SYSTEM と UNDO テーブルスペースのパフォーマンスを考慮すると、キャッシュなしが最適なオプションです。

\* oraarch: パフォーマンスの観点からは、記憶域プールは必要ありません。


Azure Premium Storage を使用するときに高い IOPS が必要な場合は、LVM (Logical Volume Manager) または MDADM を使用して、マウントされた複数のディスクの上に 1 つの大きな論理ボリュームを作成することをお勧めします。 LVM または MDADM の利用方法に関するガイドラインとポインターについて詳しくは、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」をご覧ください。 この方法では、ディスク領域を管理する管理オーバーヘッドを合理化し、マウントされた複数のディスク全体にファイルを手動で分散する手間を省きます。

Azure NetApp Files を使用する予定の場合は、dNFS クライアントが正しく構成されていることを確認してください。 サポートされている環境を整えるには、dNFS を使用することが必須です。 dNFS の構成の詳細については、[Direct NFS での Oracle Database の作成](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA)に関する記事を参照してください。

Oracle Database 用に Azure NetApp Files ベースの NFS を使用する例については、[Azure NetApp Files を使用した SAP AnyDB (Oracle 19c) のデプロイ](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)に関するブログを参照してください。


#### <a name="write-accelerator"></a>書き込みアクセラレータ
Azure M シリーズ VM で Azure 書き込みアクセラレータを使用すれば、オンラインの再実行ログへの書き込み待機時間を Azure Premium Storage を使用したときの数分の 1 に短縮できます。 オンラインの再実行ログ ファイルに使用される、Azure Premium Storage に基づくディスク (VHD) では、Azure 書き込みアクセラレータを有効にします。 詳しくは、「[書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)」をご覧ください。 または、Azure Ultra Disk をオンラインの再実行ログ ボリュームに使用します。


### <a name="backuprestore"></a>バックアップ/復元
バックアップと復元機能については、SAP BR*Tools for Oracle がベア メタルおよび Hyper-V と同様にサポートされています。 ディスクへのバックアップとディスクからの復元については Oracle Recovery Manager (RMAN) もサポートされます。

Azure Backup サービスと Azure Recovery サービスを使用して Oracle データベースをバックアップおよび回復する方法の詳細については、「[Azure Linux 仮想マシンでの Oracle Database 12c データベースのバックアップと回復](../oracle/oracle-overview.md)」を参照してください。

### <a name="high-availability"></a>高可用性
高可用性とディザスター リカバリーを目的として Oracle Data Guard がサポートされています。 Data Guard で自動フェールオーバーを実現するには、ファスト スタート フェールオーバー (FSFA) を使用することが必要です。 オブザーバー機能 (FSFA) によってフェールオーバーがトリガーされます。 FSFA を使用しない場合は、手動フェールオーバー構成のみを使用できます。 詳細については、「[Azure Linux 仮想マシンで Oracle Data Guard を実装する](../oracle/configure-oracle-dataguard.md)」を参照してください。


Azure の Oracle データベースのディザスター リカバリーについては、記事「[Azure 環境内の Oracle Database 12c データベースのディザスター リカバリー](../oracle/oracle-disaster-recovery.md)」を参照してください。

### <a name="accelerated-networking"></a>Accelerated Networking
Oracle Linux での Azure Accelerated Networking のサポートは、Oracle Linux 7 Update 5 (Oracle Linux 7.5) で提供されています。 最新の Oracle Linux 7.5 リリースにアップグレードできない場合、Oracle UEK カーネルの代わりに RedHat 互換カーネル (RHCK) を使用することで回避できる可能性があります。 

Oracle Linux 内で RHEL カーネルを使用することは、SAP Note [#1565179](https://launchpad.support.sap.com/#/notes/1565179) に従ってサポートされています。 Azure 高速ネットワークの場合、最小 RHCKL カーネル リリースは 3.10.0-862.13.1.el7 でなければなりません。 [Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)と共に Oracle Linux で UEK カーネルを使用する場合は、Oracle UEK カーネル バージョン 5 を使用する必要があります。

Azure Marketplace に基づかないイメージから VM をデプロイする場合は、次のコードを実行して、追加の構成ファイルを VM にコピーする必要があります。 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


## <a name="next-steps"></a>次の手順
記事を読む 

- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)
