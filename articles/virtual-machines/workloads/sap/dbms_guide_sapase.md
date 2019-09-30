---
title: SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ | Microsoft Docs
description: SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101320"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
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
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
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



このドキュメントでは、Azure IaaS での SAP ASE をデプロイするときに考慮すべきいくつかの異なる領域について説明します。 このドキュメントの前提条件として、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」ドキュメントと [Azure 上の SAP ワークロードに関するドキュメント](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)の中の他のガイドを読んでいる必要があります。 

## <a name="specifics-to-sap-ase-on-windows"></a>Windows 上の SAP ASE の仕様
Microsoft Azure 以降では、既存の SAP ASE アプリケーションを Azure Virtual Machines に移行できます。 Azure Virtual Machine の SAP ASE では、さまざまなエンタープライズ アプリケーションを Microsoft Azure に簡単に移行することで、これらのアプリケーションのデプロイメント、管理、およびメンテナンスの総保有コストを削減できます。 Azure Virtual Machine の SAP ASE では、管理者および開発者はオンプレミスで提供されているものと同じ開発ツールや管理ツールを引き続き使用できます。

Azure Virtual Machines の SLA についてはこちら <https://azure.microsoft.com/support/legal/sla/virtual-machines> をご覧ください。

Microsoft Azure では、最小のものから、何千ものユーザーが使用する大規模なものまで、さまざまな規模の SAP システムとランドス ケープの実行を可能にする仮想マシンの種類が多数提供されています。 SAP 認定 VM SKU のSAP サイズ設定 SAPS 番号については、SAP Note [1928533] を参照してください。

「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」に記載されている Azure Storage の使用、SAP VM のデプロイまたは SAP の監視に関する声明と推奨事項は、SAP ASE のデプロイにも適用されます。

### <a name="sap-ase-version-support"></a>SAP ASE バージョンのサポート
SAP は SAP Business Suite 製品と合わせて使用するために SAP ASE バージョン 16.0 を現在サポートしています。 SAP ASE サーバー、または SAP Business Suite 製品で使用する JDBC および ODBC ドライバーのすべての更新プログラムは、SAP Service Marketplace (<https://support.sap.com/swdc>) を通じてのみ提供されます。

Sybase の Web サイトから直接 SAP ASE サーバー、または、JDBC、および ODBC ドライバーの更新プログラムをダウンロードしないでください。 オンプレミスと Azure Virtual Machines 内の SAP 製品で使用するようにサポートされているパッチについて詳しくは、次の SAP Note をご覧ください。

* [1590719]
* [1973241]

SAP ASE で SAP Business Suite を実行する場合の一般的な情報については、 [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM で SAP 関連 SAP ASE をインストールするための SAP ASE 構成ガイドライン
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE のデプロイの構造
SAP ASE 実行可能ファイルは VM の OS ディスク (c: ドライブ\) のシステム ドライブに配置またはインストールされている必要があります。 通常、大部分の SAP ASE システムおよびツールのデータベースのワークロードは高くありません。 従って、システムおよびツールのデータベース (master、model、saptools、sybmgmtdb、sybsystemdb) は、C:\ ドライブに残すことができます。 

例外は一時データベースで、一部の SAP ERP とすべての BW ワークロードでは、大量のデータ ボリュームまたは I/O 操作ボリュームが必要になる場合があります。 VM の OS ディスクでは提供できないボリュームまたは IOPS (C: ドライブ\)。

インストールのために使用された SAPInst/SWPM をバージョンによっては、SAP ASE インスタンスの構成は次のようになります。

* SAP ASE をインストールするときに作成された 1 つの SAP ASE tempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および SAP インストール ルーチンによって作成された追加の saptempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および ERP/BW tempdb の特定の要件を満たすために手動で作成された追加の tempdb (例: 次の SAP Note [1752266])

特定の ERP ワークロードまたはすべての BW ワークロードの場合、追加で作成された tempdb の tempdb デバイスを C:\ 以外のドライブに保持することは、パフォーマンスの観点から理に適っています。 追加の tempdb が存在しない場合は、1 つ作成することをお勧めします (SAP Note [1752266])。

このようなシステムでは、追加で作成された tempdb に対して次の手順を実行する必要があります。

* 最初の tempdb デバイスを SAP データベースの最初のデバイスに移動します。
* SAP データベースのデバイスを含む VHD のそれぞれに tempdb のデバイスを追加します。

この構成によって、tempdb はシステム ドライブが提供するよりも多くの領域を使用できます。 参照用に、オンプレミスで実行している既存のシステムで tempdb デバイスのサイズを確認できます。 また、このような構成により tempdb に対する IOPS 値をシステム ドライブでは設定できない値に設定できます。 オンプレミスで実行されているシステムを、tempdb に対する I/O ワークロードの監視に使用できます。

VM の D:\ ドライブに、SAP ASE デバイスを配置しないでください。 SAP ASE の場合、tempdb に保管されているオブジェクトが一時的なものであっても、この推奨事項は tempdb にも当てはまります。

データとトランザクション ログ ファイルの展開についての声明と提案は、「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」に記載されています。 Windows ベースの場合、十分な IOPS、スループット、およびボリュームを提供するストライプ セットを構築するために Windows 記憶域スペースを使用して展開することをお勧めします。  

#### <a name="impact-of-database-compression"></a>データベースの圧縮の影響
I/O 帯域幅が制限要因になる構成では、IOPS を削減するすべての手段が Azure のような IaaS シナリオで実行できるワークロードを拡張するのに役立つ場合があります。 そのため、既存の SAP データベースを Azure にアップロードする前に、SAP ASE 圧縮が使用されていることを確認するようお勧めします。

次の理由により、Azure にアップロードする前に圧縮を適用することをお勧めします。

* Azure にアップロードするデータの量が少なくなります。
* オンプレミスよりも CPU が多いまたは I/O 帯域幅が大きい、または I/O 待機時間が少ない強力なハードウェアを使用できるため、圧縮の実行にかかる時間が短い
* 比較的小さなサイズのデータベースでは、ディスク割り当てのコストが少なくなる可能性があります。

データベースと LOB の圧縮は、オンプレミスと同様に Azure Virtual Machines でも動作します。 既存の SAP ASE データベースですでに圧縮を使用しているかどうかを確認する方法について詳しくは、SAP Note [1750510] をご覧ください。

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit を使用してデータベースのインスタンスを監視する
データベース プラットフォームとして SAP ASE を使用する SAP システムの場合、DBACockpit はトランザクション DBACockpit に埋め込まれたブラウザー ウィンドウとして、または Webdynpro としてアクセスできます。 ただし、データベースの管理と管理を行うための完全な機能は DBACockpit の Webdynpro の実装でのみ使用可能です。

オンプレミス システムと同様に、DBACockpit の Webdynpro 実装で使用されるすべての SAP NetWeaver 機能を有効にするために、いくつかの手順が必要です。 SAP Note [1245200] に従って、Webdynpros の使用を有効にし、必要なものを生成します。 上記の Note の手順に従う際には、インターネット通信マネージャー (icm) に加えて、http 接続や https 接続に使用するポートも構成します。 http の既定の設定は次のようになります。

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

トランザクション DBACockpit で生成されたリンクは次のようになります。

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP システムをホストする Azure Virtual Machine が AD や DNS に接続されている方法に従って、DBACockpit を開こうとしているコンピューターで解決できる完全修飾ホスト名を ICM が使用していることを確認する必要があります。 ICM がプロファイルのパラメーターに応じて完全修飾ホスト名を判定し、必要な場合に icm/host_name_full に明示的にパラメーターを設定する方法について詳しくは、SAP Note [773830] をご覧ください。

オンプレミスと Azure 間のクロスプレミス接続を使用しないクラウドのみのシナリオで VM をデプロイした場合は、パブリック IP アドレスと、domainlabel を定義する必要があります。 VM のパブリック DNS 名の形式は、次のようになります。

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 名の詳細については、[こちら][virtual-machines-azurerm-versus-azuresm]を参照してください。

SAP プロファイル パラメーター icm/host_name_full を Azure VM のリンクの DNS 名に設定すると、リンクは次のようになる場合があります。

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

この場合、次のことを行う必要があります。

* ICM との通信に使用される TCP/IP ポート用に、Azure Portal でネットワーク セキュリティ グループへの受信規則を追加します。
* ICM との通信に使用される TCP/IP ポート用に Windows ファイアウォールの構成への受信規則を追加します。

使用可能なすべての修正を自動的にインポートする場合、使用している SAP のバージョンに該当する修正コレクション SAP Note を定期的に適用することをお勧めします。

* [1558958]
* [1619967]
* [1882376]

SAP ASE の DBA Cockpit に関する詳細については、次の SAP Note で参照することができます。

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE のバックアップ/復旧に関する考慮事項
Azure に SAP ASE をデプロイする場合、バックアップ方法を確認する必要があります。 運用システム以外でも、SAP データベースは定期的にバックアップする必要があります。 Azure Storage では 3 つのイメージが保持されるため、ストレージの障害から復旧するという意味ではバックアップの重要度は低くなります。 適切なバックアップと復元の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 

Azure でのデータベースのバックアップと復元はオンプレミスと同様に動作します。 次の SAP Note をご覧ください。

* [1588316]
* [1585981]

(ダンプの構成の作成とバックアップのスケジュール設定についての詳細について)。 戦略やニーズに応じて、既存のディスクの 1 つの上にデータベースおよびディスクへのログ ダンプを構成し、バックアップ用にディスクを追加できます。 エラー発生時のデータ損失の危険性を減らすために、データベース ファイルが存在しないディスクを使用することをお勧めします。

データと LOB だけでなく、SAP ASE の圧縮は、バックアップの圧縮を提供します。 データベースとログのダンプが消費する領域を小さくするには、バックアップの圧縮を使用することをお勧めします。 詳しくは、SAP Note [1588316] をご覧ください。 バックアップの圧縮はバックアップまたは内部設置型にダンプ Azure 仮想マシンからバックアップを含む VHD をダウンロードする場合、転送されたデータの量を削減するために重要ではもです。

データベースまたはログのダンプ先として D:\ ドライブを使用してはいけません。

#### <a name="performance-considerations-for-backupsrestores"></a>バックアップと復元のパフォーマンスに関する考慮事項
ベア メタル デプロイメントと同様に、バックアップ/復元のパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 バックアップの圧縮は CPU リソースを消費することに留意してください。 バックアップの圧縮で使用される CPU 使用量は、CPU スレッド数が少ない VM では重大な影響を与える可能性があります。 そのため、次のことを想定できます。

* データベース デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットは小さくなる。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップが書き込まれるターゲット (ストライプ ディレクトリ、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲットの数を増やすために、次の 2 つのオプションが用意されています。必要に応じて一方を使用または組み合わせて使用できます。

* マウントされた複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたボリュームの IOPS スループットを向上させる
* ダンプの書き込み先として複数のターゲット ディレクトリを使用するダンプ構成を SAP ASE レベルで作成する

複数のマウントされたディスクにディスク ボリュームをストライピングすることについては、「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」で説明されています。 SAP ASE ダンプ構成で複数のディレクトリを使用する方法について詳しくは、[Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp) でダンプ構成の作成に使用されるストアド プロシージャ sp_config_dump に関するドキュメントをご覧ください。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM を使用した障害復旧
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SAP Sybase Replication Server を使用したデータのレプリケーション
SAP Sybase Replication Server (SRS) によって、SAP ASE はデータベース トランザクションを離れた場所に非同期的に転送するウォーム スタンバイ ソリューションを提供します。 

SRS のインストールと動作は、Azure Virtual Machine サービスでホストされている VM 内で、オンプレミスと同様に機能します。

SAP ASE HADR には Azure 内部のロード バランサーは不要で、OS レベルのクラスタリングに対する依存関係はありません。 Azure の Windows と Linux VM 上で動作します。 SAP ASE HADR の詳細については、[SAP ASE HADR ユーザー ガイド](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)をご覧ください。

## <a name="specifics-to-sap-ase-on-linux"></a>Linux 上の SAP ASE の仕様
Microsoft Azure 以降では、既存の SAP ASE アプリケーションを Azure Virtual Machines に簡単に移行できます。 Virtual Machine の SAP ASE では、さまざまなエンタープライズ アプリケーションを Microsoft Azure に簡単に移行することで、これらのアプリケーションのデプロイメント、管理、およびメンテナンスの総保有コストを削減できます。 Azure Virtual Machine の SAP ASE では、管理者および開発者はオンプレミスで提供されているものと同じ開発ツールや管理ツールを引き続き使用できます。

Azure VM をデプロイする際には、<https://azure.microsoft.com/support/legal/sla> にアクセスして正式な SLA を理解しておくことが重要です。

SAP のサイズ設定情報と SAP 認定 VM SKU の一覧は、SAP Note [1928533] をご覧ください。 Azure 仮想マシンの SAP のサイズ設定については、<https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> および <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx> にも記載されています。

Azure Storage、SAP VM の展開、SAP Monitoring の使用方法に関する声明と推奨事項は、このドキュメントの最初の 4 つの章で説明したように SAP アプリケーションと共に SAP ASE の展開に適用されます。

次の 2 つの SAP Note には、Linux 上の ASE とクラウド内の ASE に関する一般的な情報が含まれています。

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ASE バージョンのサポート
SAP は SAP Business Suite 製品と合わせて使用するために SAP ASE バージョン 16.0 を現在サポートしています。 SAP ASE サーバー、または SAP Business Suite 製品で使用する JDBC および ODBC ドライバーのすべての更新プログラムは、SAP Service Marketplace (<https://support.sap.com/swdc>) を通じてのみ提供されます。

オンプレミス インストールの場合、Sybase の web サイトから直接 SAP ASE サーバー、または、JDBC、および ODBC ドライバーの更新プログラムをダウンロードしないでください。 オンプレミスと Azure Virtual Machines 内の SAP Business Suite 製品で使用するようにサポートされているパッチについて詳しくは、次の SAP Note をご覧ください。

* [1590719]
* [1973241]

SAP ASE で SAP Business Suite を実行する場合の一般的な情報については、 [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM で SAP 関連 SAP ASE をインストールするための SAP ASE 構成ガイドライン
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE のデプロイの構造
SAP ASE 実行可能ファイルは VM のルート ファイル システム (/sybase) に配置またはインストールする必要があります。 通常、大部分の SAP ASE システムおよびツールのデータベースのワークロードは高くありません。 このため、システムおよびツール データベース (master、model、saptools、sybmgmtdb、sybsystemdb) はルート ファイル システム上に保存することができます。 

例外は一時データベースで、一部の SAP ERP とすべての BW ワークロードでは、大量のデータ ボリュームまたは I/O 操作ボリュームが必要になる場合があります。 VM の OS ディスクによって提供できないボリュームまたは IOPS。 

SAPInst/SWPM システムをインストールするために使用するバージョンによっては、次のデータベースが含まれる場合があります。

* SAP ASE をインストールするときに作成された 1 つの SAP ASE tempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および SAP インストール ルーチンによって作成された追加の saptempdb
* SAP ASE のインストールによって作成された SAP ASE tempdb、および ERP/BW tempdb の特定の要件を満たすために手動で作成された追加の tempdb (例: 次の SAP Note [1752266])

特定の ERP ワークロードまたはすべての BW ワークロードのパフォーマンス上の理由から、(SWPM または手動で) 追加で作成された tempdb の tempdb デバイスを、単一の Azure データ ディスク、または複数の Azure データ ディスクにまたがる Linux RAID で表すことができる別のファイル システムに保持することは、理に適っています。 追加の tempdb が存在しない場合は、1 つ作成することをお勧めします (SAP Note [1752266])。

このようなシステムでは、追加で作成された tempdb に対して次の手順を実行する必要があります。

* 最初の tempdb ディレクトリを SAP データベースの最初のファイル システムに移動します。
* SAP データベースのファイルシステムが含まれる各ディスクに tempdb ディレクトリを追加します。

この構成によって、tempdb はシステム ドライブが提供するよりも多くの領域を使用できます。 参照用に、オンプレミスで実行している既存のシステムで tempdb デバイスのサイズを確認できます。 また、このような構成により tempdb に対する IOPS 値をシステム ドライブでは設定できない値に設定できます。 オンプレミスで実行されているシステムを、tempdb に対する I/O ワークロードの監視に使用できます。

VM の /mnt または /mnt/resource に SAP ASE ディレクトリを配置しないでください。 SAP ASE の場合、tempdb に保管されているオブジェクトが一時的なものであっても、この推奨事項は tempdb にも当てはまります。 /mnt または /mnt/resource は、永続的ではない、既定の Azure VM の一時領域です。 Azure VM の一時領域の詳細については、[こちらの記事][virtual-machines-linux-how-to-attach-disk]を参照してください。

データとトランザクション ログ ファイルの展開についての声明と提案は、「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」に記載されています。 Linux ベースの場合、十分な IOPS、スループット、およびボリュームを提供するストライプ セットを構築するために LVM または MDADM を使用して展開することをお勧めします。 

#### <a name="impact-of-database-compression"></a>データベースの圧縮の影響
I/O 帯域幅が制限要因になる構成では、IOPS を削減するすべての手段が Azure のような IaaS シナリオで実行できるワークロードを拡張するのに役立つ場合があります。 そのため、既存の SAP データベースを Azure にアップロードする前に、SAP ASE 圧縮が使用されていることを確認するようお勧めします。

次の理由により、Azure にアップロードする前に圧縮を適用することをお勧めします。

* Azure にアップロードするデータの量が少なくなります。
* オンプレミスよりも CPU が多いまたは I/O 帯域幅が大きい、または I/O 待機時間が少ない強力なハードウェアを使用できるため、圧縮の実行にかかる時間が短い
* 比較的小さなサイズのデータベースでは、ディスク割り当てのコストが少なくなる可能性があります。

データベースと LOB の圧縮は、オンプレミスと同様に Azure Virtual Machines でも動作します。 既存の SAP ASE データベースですでに圧縮を使用しているかどうかを確認する方法について詳しくは、SAP Note [1750510] をご覧ください。 データベース圧縮に関するその他の情報については、SAP Note [2121797] をご覧ください。

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit を使用してデータベースのインスタンスを監視する
データベース プラットフォームとして SAP ASE を使用する SAP システムの場合、DBACockpit はトランザクション DBACockpit に埋め込まれたブラウザー ウィンドウとして、または Webdynpro としてアクセスできます。 ただし、データベースの管理と管理を行うための完全な機能は DBACockpit の Webdynpro の実装でのみ使用可能です。

オンプレミス システムと同様に、DBACockpit の Webdynpro 実装で使用されるすべての SAP NetWeaver 機能を有効にするために、いくつかの手順が必要です。 SAP Note [1245200] に従って、Webdynpros の使用を有効にし、必要なものを生成します。 上記の Note の手順に従う際には、インターネット通信マネージャー (icm) に加えて、http 接続や https 接続に使用するポートも構成します。 Http の既定の設定は次のようになります。

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

トランザクション DBACockpit で生成されたリンクは次のようになります。

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP システムをホストする Azure Virtual Machine が AD や DNS に接続されている方法に従って、DBACockpit を開こうとしているコンピューターで解決できる完全修飾ホスト名を ICM が使用していることを確認する必要があります。 ICM がプロファイルのパラメーターに応じて完全修飾ホスト名を判定し、必要な場合に icm/host_name_full に明示的にパラメーターを設定する方法について詳しくは、SAP Note [773830] をご覧ください。

オンプレミスと Azure 間のクロスプレミス接続を使用しないクラウドのみのシナリオで VM をデプロイした場合は、パブリック IP アドレスと、domainlabel を定義する必要があります。 VM のパブリック DNS 名の形式は、次のようになります。

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 名の詳細については、[こちら][virtual-machines-azurerm-versus-azuresm]を参照してください。

SAP プロファイル パラメーター icm/host_name_full を Azure VM のリンクの DNS 名に設定すると、リンクは次のようになる場合があります。

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

この場合、次のことを行う必要があります。

* ICM との通信に使用される TCP/IP ポート用に、Azure Portal でネットワーク セキュリティ グループへの受信規則を追加します。
* ICM との通信に使用される TCP/IP ポート用に Windows ファイアウォールの構成への受信規則を追加します。

使用可能なすべての修正を自動的にインポートする場合、使用している SAP のバージョンに該当する修正コレクション SAP Note を定期的に適用することをお勧めします。

* [1558958]
* [1619967]
* [1882376]

SAP ASE の DBA Cockpit に関する詳細については、次の SAP Note で参照することができます。

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE のバックアップ/復旧に関する考慮事項
Azure に SAP ASE をデプロイする場合、バックアップ方法を確認する必要があります。 運用システム以外でも、SAP データベースは定期的にバックアップする必要があります。 Azure Storage では 3 つのイメージが保持されるため、ストレージの障害から復旧するという意味ではバックアップの重要度は低くなります。 適切なバックアップと復元の計画を維持するための主な理由は、ポイントイン タイム復旧機能を提供することにより、論理エラーや人的エラーから復旧できる可能性が高くなるためです。 したがって、目標は、特定の時点までデータベースを復元するためにバックアップを使用するか、既存のデータベースをコピーして別のシステムに配置するために Azure のバックアップを使用するかのいずれかです。 

Azure でのデータベースのバックアップと復元はオンプレミスと同様に動作します。 次の SAP Note をご覧ください。

* [1588316]
* [1585981]

(ダンプの構成の作成とバックアップのスケジュール設定についての詳細について)。 戦略やニーズに応じて、既存のディスクの 1 つの上にデータベースおよびディスクへのログ ダンプを構成し、バックアップ用にディスクを追加できます。 エラー発生時のデータ損失の危険性を減らすために、データベース ディレクトリやデータベース ファイルが存在しないディスクを使用することをお勧めします。

データと LOB だけでなく、SAP ASE の圧縮は、バックアップの圧縮を提供します。 データベースとログのダンプが消費する領域を小さくするには、バックアップの圧縮を使用することをお勧めします。 詳しくは、SAP Note [1588316] をご覧ください。 バックアップの圧縮はバックアップまたは内部設置型にダンプ Azure 仮想マシンからバックアップを含む VHD をダウンロードする場合、転送されたデータの量を削減するために重要ではもです。

データベースまたはログのダンプ先として、Azure 仮想マシンは一時領域 /mnt または /mnt/resource を使用することはできません。

#### <a name="performance-considerations-for-backupsrestores"></a>バックアップと復元のパフォーマンスに関する考慮事項
ベア メタル デプロイメントと同様に、バックアップ/復元のパフォーマンスは、並列で読み取ることができるボリュームの数と、それらのボリュームのスループットに依存します。 バックアップの圧縮は CPU リソースを消費することに留意してください。 バックアップの圧縮で使用される CPU 使用量は、CPU スレッド数が少ない VM では重大な影響を与える可能性があります。  そのため、次のことを想定できます。

* データベース デバイスの格納に使用されるディスクの数が少ないほど、読み取りの全体的なスループットは小さくなる。
* VM の CPU スレッドの数が少ないほど、バックアップの圧縮への影響が大きくなる。
* バックアップが書き込まれるターゲット (Linux ソフトウェア RAID、ディスク) が少ないほど、スループットが低下する。

書き込まれるターゲットの数を増やすために、次の 2 つのオプションが用意されています。必要に応じて一方を使用または組み合わせて使用できます。

* マウントされた複数のディスクの上にバックアップ ターゲット ボリュームをストライピングし、そのストライピングされたボリュームの IOPS スループットを向上させる
* ダンプの書き込み先として複数のターゲット ディレクトリを使用するダンプ構成を SAP ASE レベルで作成する

複数のマウントされたディスクにディスク ボリュームをストライピングすることについては、「[SAP ワークロードのための Azure Virtual Machines DBMS のデプロイに関する考慮事項](dbms_guide_general.md)」で説明されています。 SAP ASE ダンプ構成で複数のディレクトリを使用する方法について詳しくは、[Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp) でダンプ構成の作成に使用されるストアド プロシージャ sp_config_dump に関するドキュメントをご覧ください。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM を使用した障害復旧
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SAP Sybase Replication Server を使用したデータのレプリケーション
SAP Sybase Replication Server (SRS) によって、SAP ASE はデータベース トランザクションを離れた場所に非同期的に転送するウォーム スタンバイ ソリューションを提供します。 

SRS のインストールと動作は、Azure Virtual Machine サービスでホストされている VM 内で、オンプレミスと同様に機能します。

SAP Replication Server を経由する ASE HADR はサポートされています。 SAP ASE 16.03 を使用して、このような構成を試行することを強くお勧めします。 このような構成のインストール方法の詳細については、この[ブログ](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/)をご覧ください。
