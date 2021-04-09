---
title: Azure VM 上の SAP MaxDB、liveCache、Content Server のデプロイ | Microsoft Docs
description: Azure 上の SAP MaxDB、liveCache、Content Server のデプロイ
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e15b592aaa780ea80b6931c6d44fbf99e2f3a730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673062"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Azure VM 上の SAP MaxDB、liveCache、Content Server のデプロイ

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




このドキュメントでは、Azure IaaS に MaxDB、liveCache、および Content Server をデプロイするときに考慮すべきいくつかの領域について説明します。 このドキュメントの前提条件として、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」ドキュメントと [Azure 上の SAP ワークロードに関するドキュメント](./get-started.md)の中の他のガイドを読んでいる必要があります。 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Windows 上の SAP MaxDB デプロイの詳細
### <a name="sap-maxdb-version-support-on-azure"></a>Azure での SAP MaxDB バージョンのサポート
SAP は Azure で SAP NetWeaver ベースの製品を使用するために SAP MaxDB バージョン 7.9 以降を現在サポートしています。 SAP MaxDB サーバー、または SAP NetWeaver ベースの製品で使用する JDBC および ODBC ドライバーのすべての更新プログラムは、SAP Service Marketplace (<https://support.sap.com/swdc>) を通じてのみ提供されます。
SAP MaxDB での SAP NetWeaver の実行に関する一般的な情報については、<https://www.sap.com/community/topic/maxdb.html> を参照してください。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>SAP MaxDB DBMS 向けにサポートされている Microsoft Windows のバージョンと Azure VM タイプ
Azure での SAP MaxDB DBMS 向けにサポートされている Microsoft Windows のバージョンについては、こちらをご覧ください。

* [SAP 製品の可用性マトリックス (PAM)][sap-pam]
* SAP Note [1928533]

Microsoft Windows オペレーティング システムの最新バージョンである Microsoft Windows 2016 を使用することをお勧めします。

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>提供されている MaxDB 用 SAP MaxDB ドキュメント
次の SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための SAP MaxDB 構成ガイドライン
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>ストレージの構成
SAP MaxDB 向けの Azure Storage のベスト プラクティスについては、「[RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)」の章に記載されている一般的な推奨事項に従ってください。

> [!IMPORTANT]
> 他のデータベースと同様に、SAP MaxDB にはデータとログ ファイルもあります。 ただし、SAP MaxDB 用語では正しい用語は "ボリューム" です ("ファイル" ではありません)。 たとえば、SAP MaxDB のデータ ボリュームとログ ボリュームがあります。 OS ディスクのボリュームとこれらを混同しないでください。 
> 
> 

要するに、次のことを行う必要があります。

* Azure Storage アカウントを使用する場合は、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」に従って、SAP MaxDB データ ボリュームとログ ボリューム (データ ファイルとログ ファイル) を保持する Azure Storage アカウントを **ローカル冗長ストレージ (LRS)** に設定します。
* ログ ボリューム (データ ファイル) の IO パスと SAP MaxDB データ ボリューム (ログ ファイル) の IO パスを分けます。 つまり、SAP MaxDB データ ボリューム (データ ファイル) を 1 つの論理ドライブにインストールし、SAP MaxDB ログ ボリューム (ログ ファイル) を別の論理ドライブにインストールする必要があります。
* SAP MaxDB データ用かログ ボリューム (データ ファイルとログ ファイル) 用か、および Azure Standard Storage を使用するか Azure Premium Storage を使用するかに応じて、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」に従って、各ディスクに適切なキャッシュ タイプを設定します。
* ディスクあたりの現在の IOPS クォータが要件を満たしている限り、マウントされた単一のディスクにすべてのデータ ボリュームを格納し、マウントされた別の単一のディスクにデータベースのすべてのログ ボリュームを格納できます。
* 高い IOPS やより多くの領域が必要な場合は、Microsoft Windows 記憶域プール (Microsoft Windows Server 2012 以降でのみ提供) を使用して、マウントされた複数のディスクの上に 1 つの大きな論理デバイスを作成することをお勧めします。 詳細については、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」も参照してください。 この方法では、ディスク領域を管理する管理オーバーヘッドを合理化し、マウントされた複数のディスク全体にファイルを手動で分散する手間を省きます。
* Azure Premium Storage for MaxDB デプロイを使用することを強く推奨します。 

![SAP MaxDB DBMS 用の Azure IaaS VM の参照の構成](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>バックアップと復元
Azure にSAP MaxDB をデプロイする場合、バックアップ方法を確認する必要があります。 実稼働システムでない場合でも、SAP MaxDB でホストされている SAP データベースを定期的にバックアップする必要があります。 Azure Storage には 3 つのイメージを保持するため、ストレージの障害、重大な運用上またら管理上の障害から復旧するという点ではバックアップの重要度は低くなりました。 適切なバックアップと復元の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 

Azure でのデータベースのバックアップと復元はオンプレミスと同様に動作します。そのため、SAP Note [767598] に記載されている SAP MaxDB ドキュメントのいずれかで説明した標準の SAP MaxDB バックアップ/復元ツールを使用できます。 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>バックアップと復元のパフォーマンスに関する考慮事項
ベア メタル デプロイメントと同様に、バックアップのパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 そのため、次のことを想定できます。

* データ デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットが低下する。
* バックアップが書き込まれるターゲット (ストライプ ディレクトリ、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲット数を増やすには、ニーズに応じて使用または組み合わせることのできるオプションが 2 つあります。

* 専用の個別のボリュームのバックアップ
* マウントされた複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたディスク ボリュームの IOPS スループットを向上させる
* 別の専用の論理ディスクのデバイスを持たせる
  * SAP MaxDB バックアップ ボリューム (ファイル)
  * SAP MaxDB データ ボリューム (ファイル)
  * SAP MaxDB ログ ボリューム (ファイル)

複数のマウントされたディスクにボリュームをストライピングすることについては、「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」で説明されています。 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>その他の考慮事項
「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」に記載されている、Azure 可用性セットや SAP の監視などの他のすべての一般的な領域も適用されます  (SAP MaxDB データベースを使用した VM のデプロイの場合)。
その他の SAP MaxDB に固有の設定は Azure VM に透過的であり、SAP Note [767598] とこれらの SAP Note に挙げられたドキュメントに記載されています。

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Windows 上の SAP liveCache デプロイの詳細
### <a name="sap-livecache-version-support"></a>SAP liveCache バージョンのサポート
Azure Virtual Machines でサポートされている SAP liveCache の最小バージョンは **liveCache 7.9.08.31** と **LCA-Build 25** を含む **SAP LC/LCAPPS 10.0 SP 25** であり、**EhP 2 for SAP SCM 7.0** 以降のリリース向けにリリースされています。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>SAP liveCache DBMS 向けにサポートされている Microsoft Windows のバージョンと Azure VM タイプ
Azure での SAP liveCache DBMS 向けにサポートされている Microsoft Windows のバージョンについては、こちらをご覧ください。

* [SAP 製品の可用性マトリックス (PAM)][sap-pam]
* SAP Note [1928533]

オペレーティング システム Microsoft Windows Server の最新バージョンを使用することをお勧めします。 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための SAP liveCache 構成ガイドライン
#### <a name="recommended-azure-vm-types-for-livecache"></a>liveCache に推奨される Azure VM の種類
SAP liveCache は膨大な計算を実行するアプリケーションであるため、RAM と CPU の量と速度よって、SAP liveCache のパフォーマンスんに大きな影響があります。 

SAP でサポートされている Azure VM の種類 (SAP Note [1928533]) については、VM に割り当てられたすべての仮想 CPU リソースが、ハイパーバイザーの専用物理 CPU リソースによってサポートされます。 オーバープロビジョニングが行われない (そのため CPU リソースの競合がない)

同様に、SAP でサポートされるすべての種類の Azure VM インスタンスにおいて、VM メモリは 100% 物理メモリにマッピングされます。たとえば、オーバープロビジョニング (オーバーコミット) は使用されません。

このような観点から、最新の Dv2、Dv3、Ev3、および M シリーズの VM を使用することを強くお勧めします。 さまざまな VM 種類の選択肢は、liveCache に必要なメモリと必要な CPU リソースによって変わります。 他のすべての DBMS デプロイと同様に、パフォーマンスが重要なボリュームに Azure Premium Storage を利用することをお勧めします。

#### <a name="storage-configuration-for-livecache-in-azure"></a>Azure の liveCache のストレージ構成
SAP liveCache は SAP MaxDB テクノロジをベースとしているため、このドキュメントで SAP MaxDB について説明したすべての Azure Storage のベスト プラクティスの推奨事項が SAP liveCache に対しても有効です。 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>liveCache 専用の Azure VM シナリオ
SAP liveCache は、計算能力を集中的に使用するため、実稼働で使用する場合は、専用の Azure Virtual Machine をデプロイすることを強くお勧めします。 

![実稼働ユース ケースでの liveCache 専用 Azure VM](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Azure の liveCache のバックアップと復元
パフォーマンスの考慮事項を含め、バックアップと復元については、このドキュメントの関連する SAP MaxDB の章で既に説明しています。 

#### <a name="other-considerations"></a>その他の考慮事項
他のすべての一般的な領域については、関連する SAP MaxDB の章で既に説明しています。 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Azure の Windows 上の SAP コンテンツ サーバー デプロイの詳細
SAP コンテンツ サーバーは、さまざまな形式で電子ドキュメントなどのコンテンツを格納する独立した、サーバー ベースのコンポーネントです。 SAP コンテンツ サーバーはテクノロジの開発によって提供され、任意の SAP アプリケーション向けのクロス アプリケーションとして使用されます。 このサーバーは別のシステムにインストールされます。 一般的な内容は、トレーニング教材とナレッジ ウェアハウスまたは mySAP PLM ドキュメント管理システムから送信された技術図面からドキュメントです。 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Azure VM の SAP コンテンツ サーバーのバージョンのサポート
SAP が現在サポートしているのは次のものです。

* **SAP コンテンツ サーバー** バージョン **6.50 (以降)**
* **SAP MaxDB バージョン 7.9**
* **Microsoft IIS (Internet Information Server) バージョン 8.0 (以降)**

最新バージョンの SAP コンテンツ サーバーと最新のバージョンの **Microsoft IIS** を使用することを強くお勧めします。 

サポートされている最新バージョンの SAP コンテンツ サーバーと Microsoft IIS は、[SAP 製品の可用性マトリックス (PAM)][sap-pam] でご確認ください。

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>SAP コンテンツ サーバー向けにサポートされている Microsoft Windows のバージョンと Azure VM タイプ
Azure での SAP コンテンツ サーバー向けにサポートされている Microsoft Windows のバージョンについては、こちらをご覧ください。

* [SAP 製品の可用性マトリックス (PAM)][sap-pam]
* SAP Note [1928533]

最新バージョンの Microsoft Windows Server を使用することをお勧めします。

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM で SAP をインストールするための SAP コンテンツ サーバー構成ガイドライン
#### <a name="storage-configuration-for-content-server-in-azure"></a>Azure のコンテンツ サーバー用ストレージ構成
SAP MaxDB データベースのファイルを格納するように SAP コンテンツ サーバーを構成する場合、このドキュメントで SAP MaxDB について説明したすべての Azure Storage のベスト プラクティスの推奨事項が SAP コンテンツ サーバーに対しても有効です。 

ファイル システムのファイルを格納するように SAP コンテンツ サーバーを構成する場合、専用の論理ドライブを使用することをお勧めします。 Windows 記憶域スペースを使用することで、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」で説明したように、論理ディスク サイズと IOPS スループットを向上させることができます。 

#### <a name="sap-content-server-location"></a>SAP コンテンツ サーバーの場所
SAP コンテンツ サーバーは、SAP システムがデプロイされたときと同じ Azure リージョンと Azure VNET にデプロイする必要があります。 専用の Azure VM または SAP システムが実行されているのと同じ VM に SAP コンテンツ サーバー コンポーネントを展開するかどうかを自由に決めることができます。 

![SAP コンテンツ サーバー専用 Azure VM](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP キャッシュ サーバーの場所
SAP キャッシュ サーバーは、(キャッシュされた) ドキュメントへのアクセスをローカルで提供する追加のサーバー ベース コンポーネントです。 SAP キャッシュ サーバーは、SAP コンテンツ サーバーのドキュメントをキャッシュします。 ドキュメントを複数の場所から取得する必要がある場合、この機能によってネットワーク トラフィックを最適化します。 一般的な規則では、SAP キャッシュ サーバーは SAP キャッシュ サーバーにアクセスするクライアントの物理的に近くに設置する必要があります。 

そのため、次の 2 つの選択肢があります。

1. **クライアントがバックエンド SAP システム** バックエンド SAP システムが SAP コンテンツ サーバーにアクセスするように構成されている場合、その SAP システムがクライアントです。 SAP システムと SAP コンテンツ サーバーの両方が、同じ Azure リージョン (同じ Azure データ センター) にデプロイされます。2 つは互いに物理的に近くに設置されます。 そのため、専用の SAP キャッシュ サーバーを用意する必要はありません。 SAP UI クライアント (SAP の GUI または Web ブラウザー) が SAP システムに直接アクセスし、SAP システムは SAP コンテンツ サーバーからドキュメントを取得します。
2. **クライアントがオンプレミス Web ブラウザー** SAP コンテンツ サーバーは、Web ブラウザーから直接アクセスするように構成できます。 この場合は、オンプレミスで実行されている Web ブラウザーが SAP コンテンツ サーバーのクライアントです。 オンプレミス データ センターと Azure データ センターは、物理的に別の場所 (理想的には、相互に近接) に配置されます。 オンプレミス データ センターは、Azure のサイト間 VPN または ExpressRoute を使用して Azure に接続されます。 どちらのオプションでも、Azure へのセキュリティで保護された VPN ネットワーク接続を提供しますが、サイト間のネットワーク接続はオンプレミス データ センターと Azure データ センター間のネットワーク帯域幅と待機時間の SLA を提供しません。 ドキュメントへのアクセスを高速化するために、次のいずれかの操作を行うことができます。
   1. オンプレミス Web ブラウザーに近いオンプレミスに SAP キャッシュ サーバーを設置する (下の図のオプション)
   2. オンプレミス データ センターと Azure データ センター間の高速で低待機時間の専用のネットワーク接続を提供する Azure ExpressRoute を構成します。

![オンプレミス SAP キャッシュ サーバーをインストールするオプション](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>バックアップ/復元
SAP MaxDB データベースのファイルを格納するように SAP コンテンツ サーバーを構成する場合、バックアップと復元の手順、およびパフォーマンスに関する考慮事項については、このドキュメントの SAP MaxDB の章で既に説明しています。 

ファイル システムのファイルを格納するように SAP コンテンツ サーバーを構成する場合、ドキュメントが置かれているファイル構造全体の手動バックアップ/復元を実行するという選択肢があります。 SAP MaxDB のバックアップ/復元と同様に、バックアップ専用のディスク ボリュームを用意することをお勧めします。 

#### <a name="other"></a>その他
その他の SAP コンテンツ サーバー固有の設定は Azure VM に透過的であり、さまざまなドキュメントと SAP Note に記載されています。

* <https://service.sap.com/contentserver> 
* SAP Note [1619726]  
