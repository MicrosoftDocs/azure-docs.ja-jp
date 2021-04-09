---
title: Azure VM での SAP NetWeaver の高可用性
description: Azure Virtual Machines (VM) 上の SAP NetWeaver の高可用性ガイド
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6aedaf829df941a225cd0684318f28bb06ba89d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674599"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Azure VM での SAP NetWeaver の高可用性

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms_guide_general.md
[dbms-guide-2.1]:dbms_guide_general.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms_guide_general.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms_guide_general.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms_guide_general.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms_guide_general.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms_guide_general.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms_guide_general.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms_guide_general.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms_guide_general.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms_guide_general.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms_guide_general.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms_guide_general.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms_guide_general.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms_guide_general.md#642f746c-e4d4-489d-bf63-73e80177a0a8

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

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP マルチ SID 高可用性構成)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP 製品の可用性マトリックス)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
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
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Azure Virtual Machines は、最短時間で、時間のかかる調達サイクルなしに、コンピューティング リソース、ストレージ リソース、およびネットワーク リソースを入手する必要のある組織向けのソリューションです。 Azure Virtual Machines を使用すると、SAP NetWeaver ベースの ABAP、Java、ABAP+Java スタックなどの従来のアプリケーションをデプロイできます。 オンプレミスのリソースを追加することなく、信頼性と可用性を高めます。 Azure Virtual Machines はクロスプレミス接続をサポートしているので、Azure Virtual Machines を組織のオンプレミスのドメイン、プライベート クラウド、SAP システム ランドスケープに統合できます。

この記事では、Azure Resource Manager デプロイ モデルを使用して Azure に高可用性 SAP システムをデプロイする手順について説明します。 主要なタスクは次のとおりです。

* 「[リソース][sap-ha-guide-2]」セクションの一覧で、適切な SAP Note とインストール ガイドを探します。 この記事は SAP インストール ドキュメントと SAP Note を補完するものであり、これらは特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースです。
* Azure Resource Manager デプロイ モデルと Azure クラシック デプロイ モデルの違いについて理解します。
* Azure のデプロイに適したモデルを選択できるように、Windows Server フェールオーバー クラスタリングのクォーラム モードについて理解します。
* Azure サービスでの Windows Server フェールオーバー クラスタリングの共有記憶域について理解します。
* Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) やデータベース管理システム (DBMS) のような単一障害点コンポーネントおよび SAP アプリケーション サーバーのような冗長コンポーネントを Azure で保護するために役立つ方法を理解します。
* Azure Resource Manager を使用して Azure の Windows Server フェールオーバー クラスタリング クラスターに高可用性 SAP システムをインストールして構成する手順の例に従います。
* Azure で Windows Server フェールオーバー クラスタリングを使用するために必要な追加の手順を理解します。ただし、オンプレミスのデプロイでは必要ありません。

デプロイと構成を簡単にするため、この記事では、SAP の 3 層高可用性 Resource Manager テンプレートを使用します。 このテンプレートを使用すると、高可用性 SAP システムに必要なインフラストラクチャ全体を自動的にデプロイできます。 また、インフラストラクチャは SAP システムの SAP Application Performance Standard (SAPS) サイジングもサポートします。

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> 前提条件
始める前に、次のセクションで説明されている前提条件が満たされていることを確認してください。 また、「[リソース][sap-ha-guide-2]」セクションに一覧表示されているすべてのリソースをご確認ください。

この記事では、[3 層 SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/) 用の Azure Resource Manager テンプレートを使用します。 SAP Azure Resource Manager テンプレートの概要については、「[Running SAP Applications on the Microsoft Platform](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap)」(Microsoft プラットフォームでの SAP アプリケーションの実行) を参照してください。

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> リソース
Azure での SAP のデプロイについては、以下の記事で説明されています。

* [SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP NetWeaver のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SAP NetWeaver のための Azure Virtual Machines 高可用性 (このガイド)][sap-ha-guide]

> [!NOTE]
> 可能な限り、参照する SAP インストール ガイドへのリンクを提示しています ([SAP インストール ガイド][sap-installation-guides]を参照)。 インストール プロセスに関する前提条件と情報については、SAP NetWeaver のインストール ガイドをよく読むことをお勧めします。 この記事では、Virtual Machines で使用できる SAP NetWeaver ベースのシステムに関するタスクについてのみ説明します。
>
>

次の SAP Note は、Azure での SAP のトピックに関連するものです。

| Note 番号 | タイトル |
| --- | --- |
| [1928533] |SAP Applications on Azure:Supported Products and Sizing (Azure 上の SAP アプリケーション: サポートされている製品とサイズ) |
| [2015553] |SAP on Microsoft Azure:Support Prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [1999351] |Enhanced Azure Monitoring for SAP (Azure Monitoring for SAP の強化) |
| [2178632] |Key Monitoring Metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [1999351] |Virtualization on Windows:Enhanced Monitoring (Azure を使用した Linux での仮想化: 拡張された監視機能) |
| [2243692] |Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用) |

一般的な既定の制限事項や上限など、[Azure サブスクリプションの制限事項][azure-resource-manager/management/azure-subscription-service-limits-subscription]について理解しておいてください。

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Azure Resource Manager デプロイメント モデルと Azure クラシック デプロイメント モデルでの高可用性 SAP
Azure Resource Manager デプロイ モデルと Azure クラシック デプロイ モデルでは、次の点が異なります。

- リソース グループ
- Azure リソース グループに対する Azure 内部ロード バランサーの依存関係
- SAP マルチ SID シナリオのサポート

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a>リソース グループ
Azure Resource Manager では、リソース グループを使用して Azure サブスクリプション内のすべてのアプリケーション リソースを管理できます。 統合されたアプローチでは、リソース グループ内のすべてのリソースのライフ サイクルが同じになります。 たとえば、すべてのリソースは、同時に作成され、同時に削除されます。 [リソース グループ](../../../azure-resource-manager/management/overview.md#resource-groups)に関する詳細情報を参照してください。

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure リソース グループに対する Azure 内部ロード バランサーの依存関係

Azure クラシック デプロイ モデルでは、Azure 内部ロード バランサー (Azure Load Balancer サービス) とクラウド サービス グループとの間に依存関係があります。 各内部ロード バランサーには、1 つのクラウド サービス グループが必要です。

Azure Resource Manager では、Azure Load Balancer を使うために Azure リソース グループは必要ありません。 環境は、よりシンプルで柔軟性が高くなります。

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP マルチ SID シナリオのサポート

Azure Resource Manager では、1 つのクラスターに複数の SAP システム識別子 (SID) ASCS/SCS インスタンスをインストールすることができます。 各 Azure 内部ロード バランサーで複数の IP アドレスがサポートされているため、マルチ SID インスタンスが可能になります。

Azure クラシック デプロイ モデルを使用するには、[Azure での SAP NetWeaver に関する「Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper (Azure での Windows Server フェールオーバー クラスタリングと SIOS DataKeeper を使用した SAP ASCS/SCS インスタンスのクラスタ化)」](https://go.microsoft.com/fwlink/?LinkId=613056)に記載されている手順に従ってください。

> [!IMPORTANT]
> SAP のインストールには、Azure Resource Manager デプロイ モデルを使用することを強くお勧めします。 クラシック デプロイ モデルにはない多くの利点があります。 Azure のデプロイメント モデルの詳細については、[こちら][virtual-machines-azure-resource-manager-architecture-benefits-arm] をご覧ください。   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server フェールオーバー クラスタリング
Windows Server フェールオーバー クラスタリングは、Windows での高可用性の SAP ASCS/SCS インストールと DBMS の基盤です。

フェールオーバー クラスターとは、アプリケーションとサービスの可用性を高めるために連携する、1 + n 台の独立したサーバー (ノード) のグループです。 ノード障害が発生した場合、Windows Server フェールオーバー クラスタリングは、アプリケーションとサービスを提供するクラスターを正常な状態で維持するうえで許容できるエラーの数を計算します。 フェールオーバー クラスタリングを実現するために、さまざまなクォーラム モードを選択できます。

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> クォーラム モード
Windows Server フェールオーバー クラスタリングを使用するときは、次の 4 つのクォーラム モードから選択できます。

* **ノード マジョリティ**。 クラスターの各ノードが投票できます。 クラスターは、投票の過半数を得た場合にのみ機能します。 ノードの数が奇数のクラスターでは、このオプションをお勧めします。 たとえば、7 ノードのクラスターでは 3 つのノードで障害が発生してもよく、クラスターは過半数を得て実行を続けます。  
* **ノードおよびディスク マジョリティ**。 各ノードとクラスター記憶域の指定されたディスク (ディスク監視) が投票できます。ただし、これらが使用可能であり、通信している必要があります。 クラスターは、投票数が過半数の場合にのみ機能します。 このモードは、ノード数が偶数のクラスター環境で有効です。 半数のノードとディスクがオンラインの場合、クラスターは正常な状態を維持します。
* **ノードおよびファイル共有マジョリティ**。 各ノードと、管理者が作成した指定ファイル共有 (ファイル共有監視) が投票できます。ノードとファイル共有が使用可能であるかどうかや、通信しているかどうかは問いません。 クラスターは、投票数が過半数の場合にのみ機能します。 このモードは、ノード数が偶数のクラスター環境で有効です。 ノードおよびディスク マジョリティ モードと似ていますが、監視ディスクではなく監視ファイル共有を使用します。 このモードは、実装は簡単ですが、ファイル共有自体が高可用性でない場合、単一障害点になる可能性があります。
* **マジョリティなし:ディスクのみ**。 1 つのノードが使用可能であり、クラスター記憶域内の特定のディスクと通信していれば、クラスターはクォーラムを獲得します。 また、そのディスクと通信しているノードだけがクラスターに参加できます。 このモードは使用しないことをお勧めします。

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server フェールオーバー クラスタリング オンプレミス
図 1 は、2 ノードのクラスターを示しています。 ノード間のネットワーク接続で障害が発生し、ノードは両方とも稼働している場合、クォーラム ディスクまたはファイル共有により、クラスターのアプリケーションとサービスの提供を続けるノードが決まります。 クォーラム ディスクまたはファイル共有にアクセスできるノードが、サービスを維持するノードです。

この例は 2 ノード クラスターを使用しているため、ノードおよびファイル共有マジョリティ クォーラム モードを使用します。 ノードおよびディスク マジョリティも有効な選択肢です。 運用環境では、クォーラム ディスクを使用することをお勧めします。 ネットワークおよび記憶域システムのテクノロジを使用して、高可用性にすることができます。

![図 1: Azure での SAP ASCS/SCS の Windows Server フェールオーバー クラスタリング構成の例][sap-ha-guide-figure-1000]

_**図 1:** Azure での SAP ASCS/SCS の Windows Server フェールオーバー クラスタリング構成の例_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> 共有記憶域
図 1 では、2 ノードの共有記憶域クラスターも示されています。 オンプレミスの共有記憶域クラスターでは、クラスター内のすべてのノードが共有記憶域を検出します。 ロック メカニズムがデータを破損から防ぎます。 別のノードで障害が発生したかどうかをすべてのノードが検出できます。 一方のノードで障害が発生すると、もう一方のノードがストレージ リソースの所有権を取得し、サービスの可用性を確保します。

> [!NOTE]
> SQL Server のような一部の DBMS アプリケーションでは、高可用性のために共有ディスクは必要ありません。 SQL Server Always On は、DBMS のデータとログ ファイルを、クラスター ノードのローカル ディスクから別のクラスター ノードのローカル ディスクにレプリケートします。 その場合、Windows クラスター構成に共有ディスクは不要です。
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> ネットワークと名前解決
クライアント コンピューターは、仮想 IP アドレスと DNS サーバーが提供する仮想ホスト名を使用して、クラスターに接続します。 オンプレミスのノードと DNS サーバーは、複数の IP アドレスを処理できます。

標準的なセットアップでは、次のような複数のネットワーク接続が使用されます。

* 記憶域への専用接続
* ハートビートのためのクラスター内ネットワーク接続
* クライアントがクラスターへの接続に使用するパブリック ネットワーク

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Azure での Windows Server フェールオーバー クラスタリング
ベア メタル デプロイやプライベート クラウド デプロイと比較すると、Azure Virtual Machines では Windows Server フェールオーバー クラスタリングを構成するための追加手順が必要となります。 共有クラスター ディスクを構築するときは、SAP ASCS/SCS インスタンスに複数の IP アドレスと仮想ホスト名を設定する必要があります。

この記事では、重要な概念と、Azure で SAP 高可用性セントラル サービス クラスターを構築するために必要な追加の手順について説明します。 サード パーティ製ツールの SIOS DataKeeper をセットアップし、Azure 内部ロード バランサーを構成する方法を示します。 これらのツールを使用して、Azure でファイル共有監視を使用する Windows フェールオーバー クラスターを作成できます。

![図 2:共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスタリング構成][sap-ha-guide-figure-1001]

_**図 2:** 共有ディスクを使用しない Azure での Windows Server フェールオーバー クラスタリング構成_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> SIOS DataKeeper を使用する Azure の共有ディスク
高可用性の SAP ASCS/SCS インスタンスには共有記憶域をクラスター化する必要があります。 2016 年 9 月の時点で、Azure は共有記憶域クラスターの作成に使用できる共有記憶域を提供していません。 代わりに、サード パーティ製ソフトウェアの SIOS DataKeeper Cluster Edition を使用して、クラスター共有記憶域をシミュレートするミラー化された記憶域を作成できます。 SIOS ソリューションは、リアルタイムの同期データ レプリケーションを実現します。 クラスターの共有ディスク リソースを作成する方法は次のとおりです。

1. 追加の Azure 仮想ハード ディスク (VHD) を、Windows クラスター構成内の各仮想マシン (VM) に接続します。
2. 両方の仮想マシン ノードで、SIOS DataKeeper Cluster Edition を実行します。
3. ソース仮想マシンの追加 VHD 接続ボリュームの内容をターゲット仮想マシンの追加 VHD 接続ボリュームにミラー化するように、SIOS DataKeeper Cluster Edition を構成します。 SIOS DataKeeper は、ソースとターゲットのローカル ボリュームを抽象化し、1 つの共有ディスクとして Windows フェールオーバー クラスタリングに提示します。

詳細については、[SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/) を参照してください。

![図 3:SIOS DataKeeper を使用する Azure での Windows Server フェールオーバー クラスタリング構成][sap-ha-guide-figure-1002]

_**図 3:** SIOS DataKeeper を使用する Azure での Windows Server フェールオーバー クラスタリング構成_

> [!NOTE]
> SQL Server のような一部の DBMS 製品では、高可用性のために共有ディスクは必要ありません。 SQL Server Always On は、DBMS のデータとログ ファイルを、クラスター ノードのローカル ディスクから別のクラスター ノードのローカル ディスクにレプリケートします。 その場合、Windows クラスター構成に共有ディスクは不要です。
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Azure での名前解決
Azure クラウド プラットフォームには、フローティング IP アドレスのような仮想 IP アドレスを構成するオプションは用意されていません。 クラウド内のクラスター リソースに到達するために仮想 IP アドレスを設定する別のソリューションが必要となります。
Azure Load Balancer サービスには内部ロード バランサーがあります。 内部ロード バランサーでは、クライアントはクラスターの仮想 IP アドレスを使用してクラスターにアクセスします。
クラスター ノードを含むリソース グループに、内部ロード バランサーをデプロイする必要があります。 その後、内部ロード バランサーのプローブ ポートで必要なすべてのポート フォワーディング規則を構成します。
クライアントは仮想ホスト名を使用して接続できます。 DNS サーバーがクラスター IP アドレスを解決し、内部ロード バランサーがクラスターのアクティブ ノードへのポート フォワーディングを処理します。

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Azure IaaS (サービスとしてのインフラストラクチャ) での SAP NetWeaver の高可用性
SAP ソフトウェア コンポーネントなどの SAP アプリケーションの高可用性を実現するには、次のコンポーネントを保護する必要があります。

* SAP アプリケーション サーバー インスタンス
* SAP ASCS/SCS インスタンス
* DBMS サーバー

高可用性シナリオでの SAP コンポーネントの保護の詳細については、[SAP NetWeaver のための Azure Virtual Machines の計画および実装](planning-guide.md)に関するページを参照してください。

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> 高可用性 SAP アプリケーション サーバー
SAP アプリケーション サーバーおよびダイアログ インスタンスについては、通常、特定の高可用性ソリューションは不要です。 高可用性は冗長性によって実現し、Azure Virtual Machines のさまざまなインスタンスで複数のダイアログ インスタンスを構成します。 Azure Virtual Machines の 2 つのインスタンスに少なくとも 2 つの SAP アプリケーション インスタンスをインストールする必要があります。

![図 4:高可用性 SAP アプリケーション サーバー][sap-ha-guide-figure-2000]

_**図 4:** 高可用性 SAP アプリケーション サーバー_

SAP アプリケーション サーバー インスタンスをホストするすべての仮想マシンを、同じ Azure 可用性セットに配置する必要があります。 Azure 可用性セットでは次のことが保証されます。

* すべての仮想マシンは、同じアップグレード ドメインの一部になります。 たとえば、アップグレード ドメインでは、計画的メンテナンス ダウンタイムの間に仮想マシンが同時に更新されないことが保証されます。
* すべての仮想マシンは、同じ障害ドメインの一部になります。 たとえば、障害ドメインでは、単一障害点によってすべての仮想マシンの可用性が影響を受けないように仮想マシンがデプロイされることが保証されます。

[仮想マシンの可用性を管理][virtual-machines-manage-availability]する方法の詳細を参照してください。

Azure ストレージ アカウントが単一障害点になる可能性があるため、少なくとも 2 つの Azure ストレージ アカウントを用意し、それぞれにおいて少なくとも 2 つの仮想マシンに分散させることが重要です。 理想的な設定としては、SAP ダイアログ インスタンスを実行する各仮想マシンのディスクを、異なるストレージ アカウントにデプロイします。

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> 高可用性の SAP ASCS/SCS インスタンス
図 5 は、高可用性の SAP ASCS/SCS インスタンスの例です。

![図 5:高可用性の SAP ASCS/SCS インスタンス][sap-ha-guide-figure-2001]

_**図 5:** 高可用性 SAP ASCS/SCS インスタンス_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Azure の Windows Server フェールオーバー クラスタリングを使用する SAP ASCS/SCS インスタンスの高可用性
ベア メタル デプロイやプライベート クラウド デプロイと比較すると、Azure Virtual Machines では Windows Server フェールオーバー クラスタリングを構成するための追加手順が必要となります。 Windows フェールオーバー クラスターを構築するには、SAP ASCS/SCS インスタンスをクラスター化するために、共有クラスター ディスク、複数の IP アドレス、複数の仮想ホスト名、Azure 内部ロード バランサーが必要になります。 これについては、記事の後半で詳しく説明します。

![図 6:SIOS DataKeeper を使用する Azure での SAP ASCS/SCS 構成の Windows Server フェールオーバー クラスタリング][sap-ha-guide-figure-1002]

_**図 6:** SIOS DataKeeper を使用する Azure での SAP ASCS/SCS 構成の Windows Server フェールオーバー クラスタリング_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>高可用性の DBMS インスタンス
DBMS は、SAP システムでの単一接続点でもあります。 高可用性ソリューションを使用して保護する必要があります。 図 7 では、Azure の SQL Server Always On 高可用性ソリューションと、Windows Server フェールオーバー クラスタリングおよび Azure 内部ロード バランサーを示します。 SQL Server Always On は、独自の DBMS レプリケーションを使用して、DBMS のデータとログ ファイルをレプリケートします。 この場合、クラスター共有ディスクは必要なく、セットアップ全体が簡単になります。

![図 7:高可用性 SAP DBMS と SQL Server AlwaysOn の例][sap-ha-guide-figure-2003]

_**図 7:** 高可用性 SAP DBMS と SQL Server AlwaysOn の例_

Azure Resource Manager デプロイ モデルを使用した Azure での SQL Server のクラスタリングの詳細については、次の記事を参照してください。

* [ Resource Manager の使用による Azure Virtual Machines での AlwaysOn 可用性グループの手動構成][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Azure の Always On 可用性グループに使用する Azure 内部ロード バランサーの構成][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> エンド ツー エンドの高可用性デプロイ シナリオ

### <a name="deployment-scenario-using-architectural-template-1"></a>Architectural Template 1 を使用するデプロイ シナリオ

図 8 は、**1 つ** の SAP システムに対する Azure での SAP NetWeaver 高可用性アーキテクチャの例です。 このシナリオは、次のようにセットアップされます。

- SAP ASCS/SCS インスタンス用に 1 つの専用クラスターが使用されます。
- DBMS インスタンス用に 1 つの専用クラスターが使用されます。
- SAP アプリケーション サーバー インスタンスが独自の専用 VM にデプロイされます。

![図 8:ASCS/SCS と DBMS に専用クラスターを使用する SAP 高可用性 Architectural Template 1][sap-ha-guide-figure-2004]

_**図 8:** ASCS/SCS と DBMS に専用クラスターを使用する SAP 高可用性 Architectural Template 1_

### <a name="deployment-scenario-using-architectural-template-2"></a>Architectural Template 2 を使用するデプロイ シナリオ

図 9 は、**1 つ** の SAP システムに対する Azure での SAP NetWeaver 高可用性アーキテクチャの例です。 このシナリオは、次のようにセットアップされます。

- SAP ASCS/SCS インスタンスと DBMS の **両方** に 1 つの専用クラスターが使用されます。
- SAP アプリケーション サーバー インスタンスが独自の専用 VM にデプロイされます。

![図 9:ASCS/SCS と DBMS に個別の専用クラスターを使用する SAP 高可用性 Architectural Template 2][sap-ha-guide-figure-2005]

_**図 9:** ASCS/SCS と DBMS に個別の専用クラスターを使用する SAP 高可用性 Architectural Template 2_

### <a name="deployment-scenario-using-architectural-template-3"></a>Architectural Template 3 を使用するデプロイ シナリオ

図 10 は、Azure での SAP NetWeaver 高可用性アーキテクチャの例を示します。このアーキテクチャは、&lt;SID1&gt; と &lt;SID2&gt; を持つ **2 つ** の SAP システムに対応しています。 このシナリオは、次のようにセットアップされます。

- 1 つの専用クラスターが、SAP ASCS/SCS SID1 インスタンス "*と*" SAP ASCS/SCS SID2 インスタンスの **両方** に使用されます (1 つのクラスター)。
- 1 つの専用クラスターが DBMS SID1 に使用され、もう 1 つの専用クラスターが DBMS SID2 に使用されます (2 つのクラスター)。
- SAP システム SID1 の SAP アプリケーション サーバー インスタンスには、独自の専用 VM があります。
- SAP システム SID2 の SAP アプリケーション サーバー インスタンスには、独自の専用 VM があります。

![図 10:SAP ASCS/SCS インスタンスごとに専用クラスターを使用する高可用性 Architectural Template 3][sap-ha-guide-figure-6003]

_**図 10:** SAP ASCS/SCS インスタンスごとに専用クラスターを使用する高可用性 Architectural Template 3_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> インフラストラクチャの準備

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Architectural Template 1 のインフラストラクチャを準備する
SAP 用の Azure Resource Manager テンプレートを使用すると、必要なリソースを簡単にデプロイできます。

Azure Resource Manager の 3 層テンプレートは、2 つのクラスターを持つ Architectural Template 1 のような高可用性シナリオもサポートします。 各クラスターは、SAP ASCS/SCS および DBMS の SAP 単一障害点です。

この記事で説明しているサンプル シナリオの Azure Resource Manager テンプレートは、以下で入手できます。

* [Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/)  
* [カスタム イメージ](https://github.com/Azure/azure-quickstart-templates/)

Architectural Template 1 のインフラストラクチャを準備するには

- Azure Portal の **[パラメーター]** ブレードにある **[SYSTEMAVAILABILITY]** ボックスで、 **[HA]** を選択します。

  ![図 11:SAP 高可用性 Azure Resource Manager パラメーターを設定する][sap-ha-guide-figure-3000]

_**図 11:** SAP 高可用性 Azure Resource Manager パラメーターを設定する_


  テンプレートによって次のものが作成されます。

  * **仮想マシン**:
    * SAP アプリケーション サーバーの仮想マシン: <*SAPSystemSID*>-di-<*Number*>
    * ASCS/SCS クラスターの仮想マシン: <*SAPSystemSID*>-ascs-<*Number*>
    * DBMS クラスター: <*SAPSystemSID*>-db-<*Number*>

  * **すべての仮想マシンのネットワーク カードと、関連付けられている IP アドレス**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Azure ストレージ アカウント**

  * 次のものの **可用性グループ**:
    * SAP アプリケーション サーバーの仮想マシン: <*SAPSystemSID*>-avset-di
    * SAP ASCS/SCS クラスターの仮想マシン: <*SAPSystemSID*>-avset-ascs
    * DBMS クラスターの仮想マシン: <*SAPSystemSID*>-avset-db

  * **Azure 内部ロード バランサー**:
    * ASCS/SCS インスタンスのすべてのポートと IP アドレス: <*SAPSystemSID*>-lb-ascs
    * SQL Server DBMS のすべてのポートと IP アドレス: <*SAPSystemSID*>-lb-db

  * **ネットワーク セキュリティ グループ**: <*SAPSystemSID*>-nsg-ascs-0  
    * <*SAPSystemSID*>-ascs-0 仮想マシンに対して開かれた外部リモート デスクトップ プロトコル (RDP) ポート

> [!NOTE]
> ネットワーク カードと Azure 内部ロード バランサーのすべての IP アドレスは、既定では **動的** です。 これらを、**静的** な IP アドレスに変更します。 この方法については、この記事の後の方で説明します。
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> 運用環境で使用するための企業ネットワーク接続 (クロスプレミス) を使用する仮想マシンのデプロイ
運用 SAP システムの場合、Azure サイト間 VPN または Azure ExpressRoute を使用して、企業ネットワーク接続を使用する Azure 仮想マシンをデプロイします。

> [!NOTE]
> Azure Virtual Network インスタンスを使用できます。 仮想ネットワークとサブネットは既に作成および準備されています。
>
>

1. Azure Portal の **[パラメーター]** ブレードにある **[NEWOREXISTINGSUBNET]** ボックスで、 **[existing (既存)]** を選択します。
2. **[SUBNETID]** ボックスに、Azure 仮想マシンのデプロイ先となる準備済みの Azure ネットワーク SubnetID の完全な文字列を追加します。
3. すべての Azure ネットワーク サブネットの一覧を取得するには、次の PowerShell コマンドを実行します。

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **[ID]** フィールドに **[SUBNETID]** と表示されます。
4. すべての **SUBNETID** 値の一覧を取得するには、次の PowerShell コマンドを実行します。

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** は次のようになります。

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> テストおよびデモ用のクラウドのみの SAP インスタンスのデプロイ
クラウドのみのデプロイメント モデルで高可用性 SAP システムをデプロイすることができます。 この種類のデプロイは、主として、デモおよびテストのユース ケースに適しています。 運用環境での使用には適していません。

- Azure Portal の **[パラメーター]** ブレードにある **[NEWOREXISTINGSUBNET]** ボックスで、 **[new (新規)]** を選択します。 **[SUBNETID]** フィールドは空白のままにしておきます。

  SAP Azure Resource Manager テンプレートは、Azure Virtual Network とサブネットを自動的に作成します。

> [!NOTE]
> また、同じ Azure Virtual Network インスタンス内に Active Directory と DNS 用の専用仮想マシンを少なくとも 1 つデプロイする必要があります。 テンプレートではこれらの仮想マシンは作成されません。
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Architectural Template 2 のインフラストラクチャを準備する

この Azure Resource Manager テンプレートを SAP に使うと、SAP Architectural Template 2 に必要なインフラストラクチャ リソースのデプロイを簡略化するのに役立ちます。

このデプロイ シナリオの Azure Resource Manager テンプレートは以下で入手できます。

* [Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/)  
* [カスタム イメージ](https://github.com/Azure/azure-quickstart-templates/)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Architectural Template 3 のインフラストラクチャを準備する

**マルチ SID** 向けにインフラストラクチャを準備し、SAP を構成することができます。 たとえば、追加の SAP ASCS/SCS インスタンスを "*既存の*" クラスター構成に追加できます。 詳細については、[Azure Resource Manager で SAP マルチ SID 構成を作成するために既存のクラスター構成に追加の SAP ASCS/SCS インスタンスを構成する][sap-ha-multi-sid-guide]方法に関するトピックを参照してください。

新しいマルチ SID クラスターを作成する場合は、GitHub のマルチ SID [クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates)を使用できます。
新しいマルチ SID クラスターを作成するには、次の 3 つのテンプレートをデプロイする必要があります。

* [ASCS/SCS テンプレート](#ASCS-SCS-template)
* [データベース テンプレート](#database-template)
* [アプリケーション サーバー テンプレート](#application-servers-template)

以降のセクションでは、テンプレートと、テンプレートで指定する必要があるパラメーターの詳細について説明します。

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> ASCS/SCS テンプレート

ASCS/SCS テンプレートは、複数の ASCS/SCS インスタンスをホストする Windows Server フェールオーバー クラスターの作成に使用できる 2 つの仮想マシンをデプロイします。

ASCS/SCS マルチ SID テンプレートを設定するには、[ASCS/SCS マルチ SID テンプレート][sap-templates-3-tier-multisid-xscs-marketplace-image]に、次のパラメーターの値を入力します。

  - **Resource Prefix (リソース プレフィックス)** 。  リソース プレフィックスを設定します。これは、デプロイ中に作成されるすべてのリソースのプレフィックスとして使われます。 リソースは 1 つの SAP システムのみに属するわけではないため、リソースのプレフィックスは 1 つの SAP システムの SID ではありません。  プレフィックスは、**3 ～ 6 文字** でなければなりません。
  - **Stack Type (スタックの種類)** 。 SAP システムのスタックの種類を選びます。 スタックの種類に応じて、Azure Load Balancer には、SAP システムごとに 1 つ (ABAP または Java のみ) または 2 つ (ABAP+ Java) のプライベート IP アドレスがあります。
  -  **OS Type (OS の種類)** 。 仮想マシンのオペレーティング システムを選びます。
  -  **SAP System Count (SAP システム数)** 。 このクラスターにインストールする SAP システムの数を選択します。
  -  **System Availability (システムの可用性)** 。 **[HA]** を選択します。
  -  **Admin Username and Admin Password (管理ユーザー名と管理パスワード)** 。 マシンへのサインインに使用できる新しいユーザーを作成します。
  -  **New Or Existing Subnet (新規または既存のサブネット)** 。 新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使用するかを設定します。 オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、 **[existing (既存)]** を選択します。
  -  **Subnet Id (サブネット ID)** 。VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/<*サブスクリプション ID*>/resourceGroups/<*リソース グループ名*>/providers/Microsoft.Network/virtualNetworks/<*仮想ネットワーク名*>/subnets/<*サブネット名*> のようになります

テンプレートは 1 つの Azure Load Balancer インスタンスをデプロイし、これにより、複数の SAP システムがサポートされます。

- ASCS インスタンスは、インスタンス番号 00、10、20 などで構成されます。
- SCS インスタンスは、インスタンス番号 01、11、21 などで構成されます。
- ASCS Enqueue Replication Server (ERS) (Linux のみ) インスタンスは、インスタンス番号 02、12、22 などで構成されます。
- SCS ERS (Linux のみ) インスタンスは、インスタンス番号 03、13、23 などで構成されます。

ロード バランサーには、1 つ (Linux の場合は 2 つ) の VIP が含まれています。1 つは ASCS/SCS 用の VIP、1 つは ERS 用の VIP (Linux のみ) です。

次の一覧には、すべての負荷分散規則が含まれています (x は、1、2、3 などの SAP システムの番号です)。
- すべての SAP システム用の Windows 固有のポート:445、5985
- ASCS ポート (インスタンス番号 x0):32x0、36x0、39x0、81x0、5x013、5x014、5x016
- SCS ポート (インスタンス番号 x1):32x1、33x1、39x1、81x1、5x113、5x114、5x116
- Linux 上の ASCS ERS ポート (インスタンス番号 x2):33x2、5x213、5x214、5x216
- Linux 上の SCS ERS ポート (インスタンス番号 x3):33x3、5x313、5x314、5x316

ロード バランサーは、次のプローブ ポートを使うように構成されます (x は 1、2、3 などの SAP システムの番号です)。
- ASCS/SCS 内部ロード バランサー プローブ ポート:620x0
- ERS 内部ロード バランサー プローブ ポート (Linux のみ):621x2

#### <a name="database-template"></a><a name="database-template"></a> データベース テンプレート

データベース テンプレートは、1 つの SAP システムのリレーショナル データベース管理システム (RDBMS) のインストールに使用できる 1 つまたは 2 つの仮想マシンをデプロイします。 たとえば、5 つの SAP システムに ASCS/SCS テンプレートをデプロイする場合は、このテンプレートを 5 回デプロイする必要があります。

データベース マルチ SID テンプレートを設定するには、[データベース マルチ SID テンプレート][sap-templates-3-tier-multisid-db-marketplace-image]に、次のパラメーターの値を入力します。

- **Sap System Id (SAP システム ID)** 。インストールする SAP システムの SAP システム ID を入力します。 ID は、デプロイされるリソースのプレフィックスとして使われます。
- **Os Type (OS の種類)** 。 仮想マシンのオペレーティング システムを選びます。
- **Dbtype (DB の種類)** 。 クラスターにインストールするデータベースの種類を選びます。 Microsoft SQL Server をインストールする場合は、 **[SQL]** を選択します。 仮想マシンに SAP HANA をインストールする予定の場合は、 **[HANA]** を選択します。 正しいオペレーティング システムの種類 (SQL の場合は **Windows**、HANA の場合は Linux ディストリビューション) を選択してください。 仮想マシンに接続されている Azure Load Balancer は、選んだデータベースの種類をサポートするように構成されます。
  * **SQL**。 ロード バランサーは、ポート 1433 で負荷分散を行います。 SQL Server Always On セットアップにはこのポートを使ってください。
  * **HANA**。 ロード バランサーは、ポート 35015 および 35017 で負荷分散を行います。 インスタンス番号が **50** の SAP HANA をインストールしてください。
  ロード バランサーはプローブ ポート 62550 を使います。
- **Sap System Size (SAP システムのサイズ)** 。 新しいシステムで提供する SAPS の数を設定します。 システムで必要となる SAPS の数が不明な場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
- **System Availability (システムの可用性)** 。 **[HA]** を選択します。
- **Admin Username and Admin Password (管理ユーザー名と管理パスワード)** 。 マシンへのサインインに使用できる新しいユーザーを作成します。
- **Subnet Id (サブネット ID)** 。ASCS/SCS テンプレートのデプロイ時に使ったサブネットの ID または ASCS/SCS テンプレートのデプロイの一部として作成されたサブネットの ID を入力します。

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> アプリケーション サーバー テンプレート

アプリケーション サーバー テンプレートは、1 つの SAP システムの SAP アプリケーション サーバー インスタンスとして使用できる 2 つ以上の仮想マシンをデプロイします。 たとえば、5 つの SAP システムに ASCS/SCS テンプレートをデプロイする場合は、このテンプレートを 5 回デプロイする必要があります。

アプリケーション サーバー マルチ SID テンプレートを設定するには、[アプリケーション サーバー マルチ SID テンプレート][sap-templates-3-tier-multisid-apps-marketplace-image]に、次のパラメーターの値を入力します。

  -  **Sap System Id (SAP システム ID)** 。インストールする SAP システムの SAP システム ID を入力します。 ID は、デプロイされるリソースのプレフィックスとして使われます。
  -  **Os Type (OS の種類)** 。 仮想マシンのオペレーティング システムを選びます。
  -  **Sap System Size (SAP システムのサイズ)** 。 新しいシステムで提供する SAPS の数です。 システムで必要となる SAPS の数が不明な場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
  -  **System Availability (システムの可用性)** 。 **[HA]** を選択します。
  -  **Admin Username and Admin Password (管理ユーザー名と管理パスワード)** 。 マシンへのサインインに使用できる新しいユーザーを作成します。
  -  **Subnet Id (サブネット ID)** 。ASCS/SCS テンプレートのデプロイ時に使ったサブネットの ID または ASCS/SCS テンプレートのデプロイの一部として作成されたサブネットの ID を入力します。


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure 仮想ネットワーク
このガイドの例では、Azure Virtual Network のアドレス空間は 10.0.0.0/16 です。 **Subnet** という名前のサブネットが 1 つあり、そのアドレス範囲は 10.0.0.0/24 です。 すべての仮想マシンと内部ロード バランサーは、この仮想ネットワークにデプロイされます。

> [!IMPORTANT]
> ゲスト オペレーティング システム内ではネットワーク設定をいっさい変更しないでください。 これには、IP アドレス、DNS サーバー、およびサブネットが含まれます。 ネットワークの設定はすべて Azure で構成します。 動的ホスト構成プロトコル (DHCP) サービスが設定を他に伝達します。
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP アドレス

必要な DNS IP アドレスを設定するには、次の手順を実行します。

1. Azure Portal の **[DNS サーバー]** ブレードで、仮想ネットワークの **[DNS サーバー]** オプションが **[カスタム DNS]** に設定されていることを確認します。
2. ネットワークの種類に基づいて設定を選択します。 詳細については、次のリソースを参照してください。
   * オンプレミスの DNS サーバーの IP アドレスを追加します。  
   Azure で実行されている仮想マシンにオンプレミスの DNS サーバーを拡張できます。 その場合、DNS サービスを実行する Azure Virtual Machines の IP アドレスを追加できます。
   * Azure 内の分離されたデプロイの場合:DNS サーバーとして機能する別の仮想マシンを同じ Virtual Network インスタンスにデプロイします。 DNS サービスを実行するように設定した Azure Virtual Machines の IP アドレスを追加します。

   ![図 12:Azure Virtual Network 用に DNS サーバーを構成する][sap-ha-guide-figure-3001]

   _**図 12:** Azure Virtual Network 用に DNS サーバーを構成する_

   > [!NOTE]
   > DNS サーバーの IP アドレスを変更した場合、変更を適用して新しい DNS サーバーに反映するには、Azure Virtual Machines を再起動する必要があります。
   >
   >

このガイドの例では、DNS サービスは次の Windows 仮想マシンにインストールされ、構成されています。

| 仮想マシンの役割 | 仮想マシンのホスト名 | ネットワーク カード名 | 静的 IP アドレス |
| --- | --- | --- | --- |
| 第 1 の DNS サーバー |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| 第 2 の DNS サーバー |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> クラスター化された SAP ASCS/SCS インスタンスとクラスター化された DBMS インスタンスのホスト名と静的 IP アドレス

オンプレミスのデプロイの場合、次の予約されたホスト名と IP アドレスが必要です。

| 仮想ホスト名の役割 | 仮想ホスト名 | 仮想静的 IP アドレス |
| --- | --- | --- |
| SAP ASCS/SCS の第 1 のクラスター仮想ホスト名 (クラスター管理用) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS インスタンスの仮想ホスト名 |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS の第 2 のクラスター仮想ホスト名 (クラスター管理用) |pr1-dbms-vir |10.0.0.32 |

クラスターを作成するときに、クラスター自体を管理する仮想ホスト名 **pr1-ascs-vir** と **pr1-dbms-vir** および関連する IP アドレスを作成します。 その方法については、「[クラスター構成内のクラスター ノードの収集][sap-ha-guide-8.12.1]」を参照してください。

他の 2 つの仮想ホスト名 **pr1-ascs-sap** と **pr1-dbms-sap** および関連する IP アドレスは、DNS サーバーで手動作成できます。 クラスター化された SAP ASCS/SCS インスタンスおよびクラスター化された DBMS インスタンスは、これらのリソースを使用します。 その方法については、「[クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成][sap-ha-guide-9.1.1]」を参照してください。

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> SAP 仮想マシンの静的 IP アドレスの設定
クラスターで使用する仮想マシンをデプロイした後は、すべての仮想マシンに静的 IP アドレスを設定する必要があります。 これは、ゲスト オペレーティング システムではなく、Azure Virtual Network の構成で行います。

1. Azure Portal で、 **[リソース グループ]**  >  **[ネットワーク カード]**  >  **[設定]**  >  **[IP アドレス]** の順に選択します。
2. **[IP アドレス]** ブレードの **[割り当て]** で **[静的]** を選択します。 **[IP アドレス]** ボックスに、使用する IP アドレスを入力します。

   > [!NOTE]
   > ネットワーク カードの IP アドレスを変更する場合は、Azure Virtual Machines を再起動して変更を適用する必要があります。  
   >
   >

   ![図 13:各仮想マシンのネットワーク カードの静的 IP アドレスを設定する][sap-ha-guide-figure-3002]

   _**図 13:** 各仮想マシンのネットワーク カードの静的 IP アドレスを設定する_

   すべてのネットワーク インターフェイス、つまり Active Directory/DNS サービスに使用する仮想マシンを含むすべての仮想マシンについて、この手順を繰り返します。

このガイドの例では、次の仮想マシンと静的 IP アドレスを使用します。

| 仮想マシンの役割 | 仮想マシンのホスト名 | ネットワーク カード名 | 静的 IP アドレス |
| --- | --- | --- | --- |
| 1 つ目の SAP アプリケーション サーバー インスタンス |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| 2 つ目の SAP アプリケーション サーバー インスタンス |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| 最後の SAP アプリケーション サーバー インスタンス |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| ASCS/SCS インスタンスの第 1 のクラスター ノード |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| ASCS/SCS インスタンスの第 2 のクラスター ノード |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS インスタンスの第 1 のクラスター ノード |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| DBMS インスタンスの第 2 のクラスター ノード |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Azure 内部ロード バランサーの静的 IP アドレスの設定

SAP Azure Resource Manager テンプレートでは、SAP ASCS/SCS インスタンス クラスターと DBMS クラスターで使用される Azure 内部ロード バランサーが作成されます。

> [!IMPORTANT]
> SAP ASCS/SCS の仮想ホスト名の IP アドレスは、SAP ASCS/SCS 内部ロード バランサー **pr1-lb-ascs** の IP アドレスと同じです。
> DBMS の仮想名の IP アドレスは、DBMS 内部ロード バランサー **pr1-lb-dbms** の IP アドレスと同じです。
>
>

Azure 内部ロード バランサーの静的 IP アドレスを設定するには

1. 初期デプロイでは、内部ロード バランサーの IP アドレスが **動的** に設定されます。 Azure Portal の **[IP アドレス]** ブレードの **[割り当て]** で **[静的]** を選択します。
2. 内部ロード バランサー **pr1-lb-ascs** の IP アドレスを、SAP ASCS/SCS インスタンスの仮想ホスト名の IP アドレスに設定します。
3. 内部ロード バランサー **pr1-lb-dbms** の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレスに設定します。

   ![図 14:SAP ASCS/SCS インスタンスの内部ロード バランサーの静的 IP アドレスを設定する][sap-ha-guide-figure-3003]

   _**図 14:** SAP ASCS/SCS インスタンスの内部ロード バランサーの静的 IP アドレスを設定する_

この例で使用する 2 つの Azure 内部ロード バランサーと静的 IP アドレスは次のとおりです。

| Azure 内部ロード バランサーの役割 | Azure 内部ロード バランサーの名前 | 静的 IP アドレス |
| --- | --- | --- |
| SAP ASCS/SCS インスタンスの内部ロード バランサー |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS の内部ロード バランサー |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則

SAP Azure Resource Manager テンプレートによって、必要なポートが作成されます。
* 既定のインスタンス番号が **00** である ABAP ASCS インスタンス
* 既定のインスタンス番号が **01** である Java SCS インスタンス

SAP ASCS/SCS インスタンスをインストールするときは、ABAP ASCS インスタンスには既定のインスタンス番号 **00** を使用し、Java SCS インスタンスには既定のインスタンス番号 **01** を使用する必要があります。

次に、SAP NetWeaver ポートに必要な内部負荷分散エンドポイントを作成します。

必要な内部負荷分散エンドポイントを作成するには、まず、SAP NetWeaver ABAP ASCS ポート用に次の負荷分散エンドポイント作成します。

| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 00 の ASCS インスタンス) (インスタンス番号が 10 の ERS) の具体的なポート |
| --- | --- | --- |
| エンキュー サーバー/ *lbrule3200* |32<*InstanceNumber*> |3200 |
| ABAP メッセージ サーバー/ *lbrule3600* |36<*InstanceNumber*> |3600 |
| 内部 ABAP メッセージ/ *lbrule3900* |39<*InstanceNumber*> |3900 |
| メッセージ サーバー HTTP/ *Lbrule8100* |81<*InstanceNumber*> |8100 |
| SAP 起動サービス ASCS HTTP/ *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SAP 起動サービス ASCS HTTPS/ *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| エンキュー レプリケーション/ *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP 起動サービス ERS HTTP *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP 起動サービス ERS HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| ファイル共有 *Lbrule445* | |445 |

_**表 1:** SAP NetWeaver ABAP ASCS インスタンスのポート番号_

次に、SAP NetWeaver Java SCS ポート用に次の負荷分散エンドポイントを作成します。

| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 01 の SCS インスタンス) (インスタンス番号が 11 の ERS) の具体的なポート |
| --- | --- | --- |
| エンキュー サーバー/ *lbrule3201* |32<*InstanceNumber*> |3201 |
| ゲートウェイ サーバー/ *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java メッセージ サーバー/ *lbrule3900* |39<*InstanceNumber*> |3901 |
| メッセージ サーバー HTTP/ *Lbrule8101* |81<*InstanceNumber*> |8101 |
| SAP 起動サービス SCS HTTP/ *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SAP 起動サービス SCS HTTPS/ *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| エンキュー レプリケーション/ *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP 起動サービス ERS HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP起動サービス ERS HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| ファイル共有 *Lbrule445* | |445 |

_**表 2:** SAP NetWeaver Java SCS インスタンスのポート番号_

![図 15:Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則][sap-ha-guide-figure-3004]

_**図 15:** Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則_

ロード バランサー **pr1-lb-dbms** の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレスに設定します。

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則の変更

SAP ASCS または SCS インスタンスに別の番号を使う場合は、それらのポートの名前と値を既定値から変更する必要があります。

1. Azure Portal で、 **<[*SID*>-lb-ascs ロード バランサー]**  >  **[負荷分散規則]** の順に選びます。
2. SAP ASCS または SCS インスタンスに属するすべての負荷分散規則について、以下の値を変更します。

   * 名前
   * Port
   * バックエンド ポート

   たとえば、既定の ASCS インスタンス番号を 00 から 31 に変更する場合は、表 1 に記載されているすべてのポートについて変更する必要があります。

   ポート *lbrule3200* の更新の例を次に示します。

   ![図 16:Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則を変更する][sap-ha-guide-figure-3005]

   _**図 16:** Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則を変更する_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> ドメインへの Windows 仮想マシンの追加

仮想マシンに静的 IP アドレスを割り当てた後、ドメインに仮想マシンを追加します。

![図 17:ドメインに仮想マシンを追加する][sap-ha-guide-figure-3006]

_**図 17:** ドメインに仮想マシンを追加する_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> SAP ASCS/SCS インスタンスの両方のクラスター ノードでのレジストリ エントリの追加

Azure Load Balancer は、設定されている時間 (アイドル タイムアウト) だけ接続がアイドル状態になっていると接続を閉じる内部ロード バランサーを備えています。 ダイアログ インスタンスの SAP ワーク プロセスは、最初のエンキュー/デキュー要求の送信が必要になるとすぐに、SAP エンキュー プロセスへの接続を開きます。 通常、これらの接続は、ワーク プロセスまたはエンキュー プロセスが再起動するまで確立されたままになります。 しかし、接続が一定時間アイドル状態の場合、Azure 内部ロード バランサーによって接続は閉じられます。 SAP ワーク プロセスはエンキュー プロセスへの接続が存在しなくなっていると再確立するため、これが問題になることはありません。 これらのアクティビティは SAP プロセスの開発者トレースに記録されますが、これらのトレースでは大量の追加コンテンツが作成されます。 そのため、両方のクラスター ノードで TCP/IP の `KeepAliveTime` と `KeepAliveInterval` を変更することをお勧めします。 TCP/IP パラメーターでのこれらの変更と、後で説明する SAP プロファイル パラメーターを組み合わせます。

SAP ASCS/SCS インスタンスの両方のクラスター ノードでレジストリ エントリを追加するには、まず、SAP ASCS/SCS の両方の Windows クラスター ノードに次の Windows レジストリ エントリを追加します。

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 変数名 |`KeepAliveTime` |
| 変数の型 |REG_DWORD (Decimal) |
| 値 |120000 |
| ドキュメントへのリンク |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**表 3:** 第 1 の TCP/IP パラメーターを変更する_

次に、SAP ASCS/SCS の両方の Windows クラスター ノードで次の Windows レジストリ エントリを追加します。

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 変数名 |`KeepAliveInterval` |
| 変数の型 |REG_DWORD (Decimal) |
| 値 |120000 |
| ドキュメントへのリンク |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**表 4:** 第 2 の TCP/IP パラメーターを変更する_

**変更を適用するには、両方のクラスター ノードを再起動します**。

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS インスタンスの Windows Server フェールオーバー クラスタリング クラスターのセットアップ

SAP ASCS/SCS インスタンスの Windows Server フェールオーバー クラスタリング クラスターのセットアップには、以下のタスクが含まれます。

- クラスター構成内のクラスター ノードの収集
- クラスターのファイル共有監視の構成

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> クラスター構成内のクラスター ノードの収集

1. 役割と機能の追加ウィザードで、両方のクラスター ノードにフェールオーバー クラスタリングを追加します。
2. フェールオーバー クラスター マネージャーを使用して、フェールオーバー クラスターをセットアップします。 フェールオーバー クラスター マネージャーで **[クラスターの作成]** を選択し、1 つ目のクラスターであるノード A の名前だけを追加します。2 つ目のノードは、まだ追加しないでください。2 つ目のノードは、後の手順で追加します。

   ![図 18:第 1 のクラスター ノードのサーバー名または仮想マシン名を追加する][sap-ha-guide-figure-3007]

   _**図 18:** 第 1 のクラスター ノードのサーバー名または仮想マシン名を追加する_

3. クラスターのネットワーク名 (仮想ホスト名) を入力します。

   ![図 19:クラスター名を入力する][sap-ha-guide-figure-3008]

   _**図 19:** クラスター名を入力する_

4. クラスターを作成した後、クラスター検証テストを実行します。

   ![図 20:クラスター検証チェックを実行する][sap-ha-guide-figure-3009]

   _**図 20:** クラスター検証チェックを実行する_

   プロセスのこの時点では、ディスクに関する警告は無視してかまいません。 ファイル共有監視と SIOS 共有ディスクは後で追加します。 この段階では、クォーラムについて心配する必要はありません。

   ![図 21:クォーラム ディスクが見つからない][sap-ha-guide-figure-3010]

   _**図 21:** クォーラム ディスクが見つからない_

   ![図 22:コア クラスター リソースに新しい IP アドレスが必要である][sap-ha-guide-figure-3011]

   _**図 22:** コア クラスター リソースに新しい IP アドレスが必要である_

5. コア クラスター サービスの IP アドレスを変更します。 サーバーの IP アドレスが仮想マシン ノードの 1 つを指しているため、コア クラスター サービスの IP アドレスを変更するまではクラスターを開始できません。 この操作は、コア クラスター サービスの IP リソースの **[プロパティ]** ページで行います。

   たとえば、クラスターの仮想ホスト名 **pr1-ascs-vir** の IP アドレス (このガイドの例では **10.0.0.42**) を割り当てる必要があります。

   ![図 23:[プロパティ] ダイアログ ボックスで IP アドレスを変更する][sap-ha-guide-figure-3012]

   _**図 23:** **[プロパティ]** ダイアログ ボックスで IP アドレスを変更する_

   ![図 24:このクラスター用に予約された IP アドレスを割り当てる][sap-ha-guide-figure-3013]

   _**図 24:** このクラスター用に予約された IP アドレスを割り当てる_

6. クラスター仮想ホスト名をオンラインにします。

   ![図 25:正しい IP アドレスでコア クラスター サービスが起動して実行されている][sap-ha-guide-figure-3014]

   _**図 25:** 正しい IP アドレスでコア クラスター サービスが起動して実行されている_

7. 2 つ目のクラスター ノードを追加します。

   コア クラスター サービスが起動して実行されているので、第 2 のクラスター ノードを追加できます。

   ![図 26:第 2 のクラスター ノードを追加する][sap-ha-guide-figure-3015]

   _**図 26:** 第 2 のクラスター ノードを追加する_

8. 2 つ目のクラスター ノード ホストの名前を入力します。

   ![図 27:第 2 のクラスター ノード ホストの名前を入力する][sap-ha-guide-figure-3016]

   _**図 27:** 第 2 のクラスター ノード ホストの名前を入力する_

   > [!IMPORTANT]
   > **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスがオンになって **いない** ことを確認してください。  
   >
   >

   ![図 28:このチェック ボックスはオンにしない][sap-ha-guide-figure-3017]

   _**図 28:** このチェック ボックスはオンに **しない**_

   クォーラムとディスクに関する警告は無視して構いません。 「[SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール][sap-ha-guide-8.12.3]」で説明されているように、クォーラムと共有ディスクは後で設定します。

   ![図 29:ディスク クォーラムに関する警告を無視する][sap-ha-guide-figure-3018]

   _**図 29:** ディスク クォーラムに関する警告を無視する_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> クラスターのファイル共有監視の構成

クラスターのファイル共有監視の構成には、以下のタスクが含まれます。

- ファイル共有の作成
- フェールオーバー クラスター マネージャーでのファイル共有監視クォーラムの設定

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> ファイル共有の作成

1. クォーラム ディスクではなく、ファイル共有監視を選択します。 SIOS DataKeeper は、このオプションをサポートしています。

   この記事の例では、ファイル共有監視は Azure で実行されている Active Directory/DNS サーバー上にあります。 ファイル共有監視の名前は **domcontr-0** です。 Azure への VPN 接続を構成したので (サイト間 VPN または Azure ExpressRoute 経由)、Active Directory/DNS サービスはオンプレミスであり、ファイル共有監視の実行には適していません。

   > [!NOTE]
   > Active Directory/DNS サービスがオンプレミスのみで実行している場合は、オンプレミスで実行している Active Directory/DNS Windows オペレーティング システムには、ファイル共有監視を構成しないでください。 Azure で実行しているクラスター ノードとオンプレミスの Active Directory/DNS の間のネットワーク待機時間が長すぎて、接続の問題が発生する可能性があります。 ファイル共有監視は、クラスター ノードの近くで動いている Azure Virtual Machines に構成する必要があります。  
   >
   >

   クォーラム ドライブには、1,024 MB 以上の空き領域が必要です。 クォーラム ドライブでは空き領域を 2,048 MB にすることをお勧めします。

2. クラスター名オブジェクトを追加します。

   ![図 30:クラスター名オブジェクトの共有に対するアクセス許可を割り当てる][sap-ha-guide-figure-3019]

   _**図 30:** クラスター名オブジェクトの共有に対するアクセス許可を割り当てる_

   アクセス許可には、クラスター名オブジェクト (この例では **pr1-ascs-vir$** ) の共有内のデータを変更する権限を必ず含めます。

3. クラスター名オブジェクトをリストに追加するには、 **[追加]** を選択します。 図 31 に示されているものに加えて、コンピューター オブジェクトをチェックするようにフィルターを変更します。

   ![図 31:コンピューターを含むようにオブジェクトの種類を変更する][sap-ha-guide-figure-3020]

   _**図 31:** コンピューターを含むようにオブジェクトの種類を変更する_

   ![図 32:[コンピューター] チェック ボックスをオンにする][sap-ha-guide-figure-3021]

   _**図 32:** **[コンピューター]** チェック ボックスをオンにする_

4. 図 31 に示すように、クラスター名オブジェクトを入力します。 レコードは既に作成されているので、図 30 に示すようにアクセス許可を変更できます。

5. 共有の **[セキュリティ]** タブを選択し、クラスター名オブジェクトのアクセス許可をさらに詳しく設定します。

   ![図 33:ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する][sap-ha-guide-figure-3022]

   _**図 33:** ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> フェールオーバー クラスター マネージャーでのファイル共有監視クォーラムの設定

1. クラスター クォーラム構成ウィザードを開きます。

   ![図 34:クラスター クォーラム設定の構成ウィザードを開始する][sap-ha-guide-figure-3023]

   _**図 34:** クラスター クォーラム設定の構成ウィザードを開始する_

2. **[クォーラム構成の選択]** ページで、 **[クォーラム監視を選択する]** を選択します。

   ![図 35:選択できるクォーラム構成][sap-ha-guide-figure-3024]

   _**図 35:** 選択できるクォーラム構成_

3. **[クォーラム監視の選択]** ページで、 **[ファイル共有監視を構成する]** を選択します。

   ![図 36:ファイル共有監視を選択する][sap-ha-guide-figure-3025]

   _**図 36:** ファイル共有監視を選択する_

4. ファイル共有の UNC パスを入力します (この例では \\domcontr-0\FSW)。 **[次へ]** を選択して、行うことができる変更の一覧を表示します。

   ![図 37:監視共有のファイル共有の場所を定義する][sap-ha-guide-figure-3026]

   _**図 37:** 監視共有のファイル共有の場所を定義する_

5. 必要な変更を選択し、 **[次へ]** を選択します。 図 38 に示すように、クラスター構成が正常に再構成されている必要があります。  

   ![図 38:クラスターが再構成されたことを確認する][sap-ha-guide-figure-3027]

   _**図 38:** クラスターが再構成されたことを確認する_

Windows フェールオーバー クラスターが正常にインストールされた後、フェールオーバーの検出を Azure での条件に合わせるように、いくつかのしきい値を変更する必要があります。 変更されるパラメーターは、[https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) のブログに記載されています。 ASCS/SCS 用に Windows クラスター構成を構築する 2 つの VM が同じサブネットにあることを前提とした場合、次のパラメーターを次の値に変更する必要があります。  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

これらの設定は、お客様と協力してテストし、十分な回復性という面で妥当な結果が得られました。 一方、SAP ソフトウェアでの実際のエラー状態やノード/VM 障害では、十分な速度のフェールオーバーを提供しました。 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール

これで、Azure で動作する Windows Server フェールオーバー クラスタリング構成が完了しました。 ただし、SAP ASCS/SCS インスタンスをインストールするには、共有ディスク リソースが必要です。 Azure では必要な共有ディスク リソースを作成できません。 サード パーティ製ソリューションの SIOS DataKeeper Cluster Edition を使用して、共有ディスク リソースを作成できます。

SAP ASCS/SCS クラスター共有ディスク用の SIOS DataKeeper Cluster Edition のインストールには、次のタスクが含まれます。

- .NET Framework 3.5 の追加
- SIOS DataKeeper のインストール
- SIOS DataKeeper のセットアップ

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> .NET Framework 3.5 の追加
Windows Server 2012 R2 では、Microsoft .NET Framework 3.5 は自動的に有効化またはインストールされません。 SIOS DataKeeper では、DataKeeper をインストールするすべてのノードで .NET Framework が必要になるため、クラスター内のすべての仮想マシンのゲスト オペレーティング システムに .NET Framework 3.5 をインストールする必要があります。

.NET Framework 3.5 を追加するには、次の 2 つの方法があります。

- 図 39 に示すように、Windows の役割と機能の追加ウィザードを使用します。

  ![図 39:役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールする][sap-ha-guide-figure-3028]

  _**図 39:** 役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールする_

  ![図 40:役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールするときのインストール進行状況バー][sap-ha-guide-figure-3029]

  _**図 40:** 役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールするときのインストール進行状況バー_

- コマンド ライン ツールの dism.exe を使用します。 この種のインストールでは、Windows インストール メディアの SxS ディレクトリにアクセスする必要があります。 管理者特権でのコマンド プロンプトで、次のように入力します。

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS DataKeeper のインストール

クラスターの各ノードに SIOS DataKeeper Cluster Edition をインストールします。 SIOS DataKeeper で仮想共有記憶域を作成するには、同期されたミラーを作成し、クラスター共有記憶域をシミュレートします。

SIOS ソフトウェアをインストールする前に、ドメイン ユーザー **DataKeeperSvc** を作成します。

> [!NOTE]
> **DataKeeperSvc** ユーザーを、両方のクラスター ノードの **ローカルの Administrator** グループに追加します。
>
>

SIOS DataKeeper をインストールするには

1. SIOS ソフトウェアを両方のクラスター ノードにインストールします。

   ![SIOS インストーラー][sap-ha-guide-figure-3030]

   ![図 41:SIOS DataKeeper のインストールの最初のページ][sap-ha-guide-figure-3031]

   _**図 41:** SIOS DataKeeper のインストールの最初のページ_

2. 図 42 のダイアログ ボックスで、 **[Yes (はい)]** を選択します。

   ![図 42:DataKeeper からサービスを無効にすることが通知される][sap-ha-guide-figure-3032]

   _**図 42:** DataKeeper からサービスを無効にすることが通知される_

3. 図 43 のダイアログ ボックスでは、 **[Domain or Server account (ドメインまたはサーバーのアカウント)]** を選択することをお勧めします。

   ![図 43:SIOS DataKeeper のユーザー選択][sap-ha-guide-figure-3033]

   _**図 43:** SIOS DataKeeper のユーザー選択_

4. SIOS DataKeeper 用に作成したドメイン アカウントのユーザー名とパスワードを入力します。

   ![図 44:SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する][sap-ha-guide-figure-3034]

   _**図 44:** SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する_

5. 図 45 に示すように、SIOS DataKeeper インスタンスのライセンス キーをインストールします。

   ![図 45:SIOS DataKeeper ライセンス キーを入力する][sap-ha-guide-figure-3035]

   _**図 45:** SIOS DataKeeper ライセンス キーを入力する_

6. メッセージが表示されたら、仮想マシンを再起動します。

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS DataKeeper のセットアップ

両方のノードに SIOS DataKeeper をインストールした後、構成を開始する必要があります。 構成の目的は、各仮想マシンに接続された追加の VHD 間で同期データ レプリケーションを実現することです。

1. DataKeeper の管理および構成ツールを起動し、 **[Connect to Server (サーバーに接続)]** を選択します (図 46 の赤い丸で囲んだオプション)。

   ![図 46:SIOS DataKeeper の管理および構成ツール][sap-ha-guide-figure-3036]

   _**図 46:** SIOS DataKeeper の管理および構成ツール_

2. 管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードを入力します。

   ![図 47:管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する][sap-ha-guide-figure-3037]

   _**図 47:** 管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する_

3. 2 つのノード間のレプリケーション ジョブを作成します。

   ![図 48:レプリケーション ジョブを作成する][sap-ha-guide-figure-3038]

   _**図 48:** レプリケーション ジョブを作成する_

   ウィザードでレプリケーション ジョブの作成手順が示されます。
4. ソース ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義します。

   ![図 49:レプリケーション ジョブの名前を定義する][sap-ha-guide-figure-3039]

   _**図 49:** レプリケーション ジョブの名前を定義する_

   ![図 50:現在のソース ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3040]

   _**図 50:** 現在のソース ノードとして使用するノードの基本データを定義する_

5. ターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義します。

   ![図 51:現在のターゲット ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3041]

   _**図 51:** 現在のターゲット ノードとして使用するノードの基本データを定義する_

6. 圧縮アルゴリズムを定義します。 この例では、レプリケーション ストリームを圧縮することをお勧めします。 特に再同期の状況では、レプリケーション ストリームを圧縮すると、再同期時間が大幅に短縮されます。 圧縮では仮想マシンの CPU および RAM リソースが使用されることに注意してください。 圧縮率を大きくするほど、使用される CPU リソースの量も増えます。 この設定は後で調整することもできます。

7. 確認する必要があるもう 1 つの設定は、レプリケーションを非同期と同期のどちらで行うかです。 *SAP ASCS/SCS の構成を保護する場合は、同期レプリケーションを使用する必要があります*。  

   ![図 52:レプリケーションの詳細を定義する][sap-ha-guide-figure-3042]

   _**図 52:** レプリケーションの詳細を定義する_

8. レプリケーション ジョブによってレプリケートされたボリュームを、Windows Server フェールオーバー クラスタリングのクラスター構成に共有ディスクとして示すかどうかを定義します。 SAP ASCS/SCS の構成では、Windows クラスターがレプリケートされたボリュームをクラスター ボリュームとして使用できる共有ディスクとして認識するように、 **[はい]** を選択します。

   ![図 53:[はい] を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する][sap-ha-guide-figure-3043]

   _**図 53:** **[はい]** を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する_

   ボリュームが作成された後、DataKeeper の管理および構成ツールではレプリケーション ジョブがアクティブであると表示されます。

   ![図 54:SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている][sap-ha-guide-figure-3044]

   _**図 54:** SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている_

   図 55 のように、フェールオーバー クラスター マネージャーでディスクが DataKeeper ディスクとして表示されるようになりました。

   ![図 55:DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される][sap-ha-guide-figure-3045]

   _**図 55:** DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> SAP NetWeaver システムのインストール

DBMS のセットアップは使用する DBMS システムによって異なるため、ここでは説明はしません。 ただし、さまざまな DBMS ベンダーが Azure でサポートする機能を使用して、DBMS に関する高可用性の問題に対処していることを想定しています。 たとえば、SQL Server の Always On またはデータベース ミラーリング、Oracle データベースの Oracle Data Guard があります。 この記事のシナリオでは、DBMS の保護は強化しませんでした。

異なる DBMS サービスが Azure のこの種のクラスター化された SAP ASCS/SCS 構成と対話する際に、特別な考慮事項はありません。

> [!NOTE]
> SAP NetWeaver ABAP システム、Java システム、ABAP + Java システムのインストール手順はほとんど同じです。 最も重要な違いは、SAP ABAP システムでは 1 つの ASCS インスタンスを使用することです。 SAP Java システムは 1 つの SCS インスタンスを使用します。 SAP ABAP + Java システムでは、1 つの ASCS インスタンスと、同じ Microsoft フェールオーバー クラスター グループで実行する 1 つの SCS インスタンスを使用します。 各 SAP NetWeaver インストール スタックのインストールの違いについて明確に説明します。 他のすべての部分は同じであると考えることができます。  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> 高可用性 ASCS/SCS インスタンスでの SAP のインストール

> [!IMPORTANT]
> DataKeeper でミラー化されたボリュームにページ ファイルを配置しないようにします。 DataKeeper はミラー化されたボリュームをサポートしていません。 ページ ファイルは、既定の場所である Azure Virtual Machines の一時ドライブ D のままにしておいてかまいません。 既にそこにない場合は、Azure Virtual Machines のドライブ D に Windows ページ ファイルを移動します。
>
>

高可用性 ASCS/SCS インスタンスでの SAP のインストールには、以下のタスクが含まれます。

- クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成
- 最初の SAP クラスター ノードのインストール
- ASCS/SCS インスタンスの SAP プロファイルの変更
- プローブ ポートの追加
- Windows ファイアウォール プローブ ポートを開く

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成

1. Windows DNS マネージャーで、ASCS/SCS インスタンスの仮想ホスト名の DNS エントリを作成します。

   > [!IMPORTANT]
   > ASCS/SCS インスタンスの仮想ホスト名に割り当てる IP アドレスは Azure Load Balancer ( **<*SID*>-lb-ascs**) に割り当てた IP アドレスと同じでなければなりません。  
   >
   >

   SAP ASCS/SCS の仮想ホスト名 (**pr1-ascs-sap**) の IP アドレスは、Azure Load Balancer (**pr1-lb-ascs**) の IP アドレスと同じです。

   ![図 56:SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-3046]

   _**図 56:** SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する_

2. 仮想ホスト名に割り当てる IP アドレスを定義するには、 **[DNS マネージャー]**  >  **[ドメイン]** の順に選択します。

   ![図 57:SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス][sap-ha-guide-figure-3047]

   _**図 57:** SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> 最初の SAP クラスター ノードのインストール

1. クラスター ノード A で、最初のクラスター ノード オプションを実行します。たとえば、**pr1-ascs-0** ホストで実行します。
2. Azure 内部ロード バランサーの既定のポートを維持するには、以下を選択します。

   * **ABAP システム**:**ASCS** インスタンス番号 **00**
   * **Java システム**:**SCS** インスタンス番号 **01**
   * **ABAP+Java システム**:**ASCS** インスタンス番号 **00** と **SCS** インスタンス番号 **01**

   他のインスタンス番号 (ABAP ASCS インスタンスでは 00 以外、Java SCS インスタンスでは 01 以外) を使用するには、「[Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則の変更][sap-ha-guide-8.9]」の説明に従って、Azure 内部ロード バランサーの既定の負荷分散規則を変更する必要があります。

次のいくつかのタスクは、通常の SAP インストール ドキュメントには記載されていません。

> [!NOTE]
> SAP インストール ドキュメントでは、最初の ASCS/SCS クラスター ノードをインストールする方法が説明されています。
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS インスタンスの SAP プロファイルの変更

新しいプロファイル パラメーターを追加する必要があります。 このプロファイル パラメーターにより、SAP ワーク プロセスとエンキュー サーバー間の接続が長時間にわたってアイドル状態のときに、接続が閉じられるのを防ぐことができます。 「[SAP ASCS/SCS インスタンスの両方のクラスター ノードでのレジストリ エントリの追加][sap-ha-guide-8.11]」では、問題のシナリオについて説明しました。 また、一部の基本的な TCP/IP 接続パラメーターに対する 2 つの変更も行いました。 第 2 の手順では、接続が Azure 内部ロード バランサーのアイドルしきい値に達しないようにするために、`keep_alive` 信号を送信するようにエンキュー サーバーを構成する必要があります。

ASCS/SCS インスタンスの SAP プロファイルを変更するには

1. ENSA1 を使用する場合は、このプロファイル パラメーターを SAP ASCS/SCS インスタンス プロファイルに追加します。

   ```
   enque/encni/set_so_keepalive = true
   ```
   このガイドの例では、パスは次のようになります。

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   たとえば、SAP SCS インスタンス プロファイルの対応するパスは次のようになります。

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

   ENSA1 と ENSA2 の両方について、`keepalive` OS パラメーターが SAP ノート [1410736](https://launchpad.support.sap.com/#/notes/1410736) の説明に従って設定されていることを確認します。  

2. 変更を適用するには、SAP ASCS/SCS インスタンスを再起動します。

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> プローブ ポートの追加

内部ロード バランサーのプローブ機能を使用して、クラスター全体の構成が Azure Load Balancer で動作するようにします。 通常、Azure 内部ロード バランサーは、参加している仮想マシン間に受信ワークロードを均等に分散させます。 ただし、アクティブなインスタンスが 1 つだけであるため、一部のクラスター構成では動作しません。 他のインスタンスはパッシブであり、ワークロードを受け付けることができません。 プローブ機能は、Azure 内部ロード バランサーがアクティブなインスタンスにのみワークロードを割り当てる場合に役に立ちます。 プローブ機能により、内部ロード バランサーはアクティブなインスタンスを検出して、そのインスタンスだけをワークロードのターゲットにすることができます。

プローブ ポートを追加するには

1. 次の PowerShell コマンドを実行して、現在の **ProbePort** の設定を確認します。 クラスター構成内のいずれかの仮想マシンで実行します。

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. プローブ ポートを定義します。 既定のプローブ ポート番号は **0** です。 この例では、プローブ ポート **62000** を使用します。

   ![図 58:クラスター構成のプローブ ポートは既定で 0 に設定されている][sap-ha-guide-figure-3048]

   _**図 58:** 既定のクラスター構成のプローブ ポートは 0 に設定されている_

   ポート番号は、SAP Azure Resource Manager テンプレートで定義されています。 PowerShell でポート番号を割り当てることができます。

   **SAP <*SID*> IP** クラスター リソースの新しい ProbePort 値を設定するには、次の PowerShell スクリプトを実行します。 環境に合わせて PowerShell 変数を更新してください。 スクリプトを実行した後、変更を有効にするために SAP クラスター グループを再起動するよう求められます。

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   **SAP <*SID*>** クラスター ロールをオンラインにした後、**ProbePort** が新しい値に設定されていることを確認します。

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![図 59:新しい値を設定した後でクラスター ポートをプローブする][sap-ha-guide-figure-3049]

   _**図 59:** 新しい値を設定した後でクラスター ポートをプローブする_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows ファイアウォール プローブ ポートを開く

両方のクラスター ノードで Windows ファイアウォール プローブ ポートを開く必要があります。 Windows ファイアウォール プローブ ポートを開くには、次のスクリプトを使用します。 環境に合わせて PowerShell 変数を更新してください。

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** は **62000** に設定されています。 これで、**ascsha-dbas** などの他のホストから、ファイル共有 **\\\ascsha-clsap\sapmnt** にアクセスできるようになりました。

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> データベース インスタンスのインストール

データベース インスタンスをインストールするには、SAP インストール ドキュメントに記載されている手順に従います。

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 第 2 のクラスター ノードのインストール

第 2 のクラスター ノードをインストールするには、SAP インストール ガイドの手順に従います。

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> SAP ERS Windows サービスのインスタンスのスタートアップの種類の変更

両方のクラスター ノードで、SAP ERS Windows サービスの開始の種類を **[自動 (遅延開始)]** に変更します。

![図 60:SAP ERS インスタンスのサービスの種類を遅延自動に変更する][sap-ha-guide-figure-3050]

_**図 60:** SAP ERS インスタンスのサービスの種類を遅延自動に変更する_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP プライマリ アプリケーション サーバーのインストール

プライマリ アプリケーション サーバー (PAS) のインスタンス <*SID*>-di-0 を、PAS のホストとして指定した仮想マシンにインストールします。 Azure または DataKeeper 固有の設定に対する依存関係はありません。

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 追加アプリケーション サーバーのインストール

SAP アプリケーション サーバー インスタンスのホストとして指定したすべての仮想マシンに、SAP 追加アプリケーション サーバー (AAS) をインストールします。 たとえば、<*SID*>-di-1 では <*SID*>-di-&lt;n&gt; にします。

> [!NOTE]
> これで、高可用性 SAP NetWeaver システムのインストールが完了します。 次に、フェールオーバーのテストに進みます。
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS インスタンスのフェールオーバーと SIOS レプリケーションのテスト
フェールオーバー クラスター マネージャーと SIOS DataKeeper の管理および構成ツールを使用して、SAP ASCS/SCS インスタンスのフェールオーバーと SIOS ディスク レプリケーションを簡単にテストおよび監視できます。

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS インスタンスがクラスター ノード A で動作している状態

**SAP PR1** クラスター グループが、クラスター ノード A (たとえば **pr1-ascs-0**) で動作しています。 **SAP PR1** クラスター グループに含まれ、ASCS/SCS インスタンスが使用する共有ディスク ドライブ S を、クラスター ノード A に割り当てます。

![図 61:フェールオーバー クラスター マネージャー:SAP <SID> クラスター グループがクラスター ノード A で動作している][sap-ha-guide-figure-5000]

_**図 61:** フェールオーバー クラスター マネージャー:SAP <*SID*> クラスター グループがクラスター ノード A で動作している_

SIOS DataKeeper の管理および構成ツールで、共有ディスクのデータがクラスター ノード A (例: **pr1-ascs-0 [10.0.0.40]** ) のソース ボリューム ドライブ S から、クラスター ノード B (例: **pr1-ascs-1 [10.0.0.41]** ) のターゲット ボリューム ドライブ S に同期的にレプリケートされることを確認できます。

![図 62:SIOS DataKeeper で、クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5001]

_**図 62:** SIOS DataKeeper で、クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> ノード A からノード B へのフェールオーバー

1. 次のいずれかの方法を選択して、クラスター ノード A からクラスター ノード B への SAP <*SID*> クラスター グループのフェールオーバーを開始します。
   - フェールオーバー クラスター マネージャーを使用します  
   - 次のフェールオーバー クラスター PowerShell を使用します

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Windows ゲスト オペレーティング システムでクラスター ノード A を再起動します (ノード A からノード B への SAP <*SID*> クラスター グループの自動フェールオーバーが開始されます)。  
3. Azure Portal からクラスター ノード A を再起動します (ノード A からノード B への SAP <*SID*> クラスター グループの自動フェールオーバーが開始されます)。  
4. Azure PowerShell を使用してクラスター ノード A を再起動します (ノード A からノード B への SAP <*SID*> クラスター グループの自動フェールオーバーが開始されます)。

   フェールオーバーの後、SAP <*SID*> クラスター グループは、クラスター ノード B (たとえば **pr1-ascs-1**) で動作しています。

   ![図 63:フェールオーバー クラスター マネージャーで SAP <SID> クラスター グループがクラスター ノード B で動作している][sap-ha-guide-figure-5002]

   _**図 63**:フェールオーバー クラスター マネージャーで SAP <*SID*> クラスター グループがクラスター ノード B で動作している_

   現在、共有ディスクはクラスター ノード B にマウントされています。SIOS DataKeeper は、クラスター ノード B (例: **pr1-ascs-1 [10.0.0.41]** ) のソース ボリューム ドライブ S から、クラスター ノード A (例: **pr1-ascs-0 [10.0.0.40]** ) のターゲット ボリューム ドライブ S に、データをレプリケートします。

   ![図 64:SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5003]

   _**図 64:** SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする_
