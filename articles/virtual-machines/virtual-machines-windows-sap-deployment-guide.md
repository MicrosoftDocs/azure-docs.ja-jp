---
title: "Windows 上の SAP のための Azure Virtual Machines のデプロイ | Microsoft Docs"
description: "Azure の Windows 仮想マシンに SAP ソフトウェアをデプロイする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 22aaa98c-bb9f-472f-b546-0b294e033cda
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 32e30b44c2f7cfa9c1069190fdc53dbe6e9f4cd5
ms.openlocfilehash: eb08ff2a61098f0e73d9cc713d485f94db0b8cc7
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-sap-on-windows-vms-in-azure"></a>Azure の Windows VM に SAP をデプロイする
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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (Azure Virtual Machines DBMS deployment for SAP on Windows)
[dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching for VMs and VHDs)
[dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure of a RDBMS deployment)
[dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (High availability and disaster recovery with Azure VMs)
[dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later)
[dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 and earlier releases)
[dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Using a SQL Server image from the Azure Marketplace)
[dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (General SQL Server for SAP on Azure summary)
[dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifics to SQL Server RDBMS)
[dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Storage configuration)
[dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup and restore)
[dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Performance considerations for backup and restore)
[dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Other)
[dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (Azure Virtual Machines deployment for SAP on Windows)
[deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP resources)
[deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Deploying a VM by using a custom image)
[deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Deploying a VM from the Azure Marketplace for SAP)
[deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Deploying a VM with a custom image for SAP)
[deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Moving a VM from on-premises using a non-generalized Azure VHD with SAP)
[deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Deployment scenarios of VMs for SAP on Microsoft Azure)
[deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Deploying Azure PowerShell cmdlets)
[deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download and import SAP-relevant PowerShell cmdlets)
[deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Join a VM to an on-premises domain - Windows only)
[deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Download, install, and enable the Azure VM Agent)
[deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configure the Azure Enhanced Monitoring Extension for SAP)
[deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Readiness check for Azure Enhanced Monitoring for SAP)
[deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Health check for the Azure monitoring infrastructure)
[deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Troubleshooting Azure monitoring for SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure the proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and troubleshooting for setting up end-to-end monitoring)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-windows-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (Azure Virtual Machines planning and implementation for SAP on Windows)
[planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resources)
[planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (High availability and disaster recovery for SAP NetWeaver running on Azure Virtual Machines)
[planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (High availability for SAP Application Servers)
[planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Using Autostart for SAP instances)
[planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud-only - Virtual Machine deployments in Azure without dependencies on the on-premises customer network)
[planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premises - Deployment of single or multiple SAP VMs in Azure fully integrated with the on-premises network)
[planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regions)
[planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fault domains)
[planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgrade domains)
[planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure availability sets)
[planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure virtual machines concept)
[planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Deploying a VM with a customer specific image)
[planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparation for moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparation for deploying a VM with a customer specific image for SAP)
[planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparing VMs with SAP for Azure)
[planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Difference between an Azure disk and an Azure image)
[planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Uploading a VHD from on-premises to Azure)
[planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copying disks between Azure Storage accounts)
[planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD structure for SAP deployments)
[planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Setting automount for attached disks)
[planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Single VM with SAP NetWeaver demo/training scenario)
[planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepts of Cloud-Only deployment of SAP instances)
[planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP)
[planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and data disks)

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:windows/classic/ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:windows/classic/ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-windows-create-vm-specialized]:virtual-machines-windows-create-vm-specialized.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../azure-resource-manager/xplat-cli-azure-resource-manager.md

Azure Virtual Machines は、時間のかかる調達サイクルなしに、最短時間でコンピューティング リソースとストレージ リソースを必要とする組織向けのソリューションです。 Azure Virtual Machines を使用して、SAP NetWeaver ベースのアプリケーションなど、従来のアプリケーションを Azure にデプロイできます。 オンプレミスのリソースを追加せずに、アプリケーションの信頼性と可用性を高めます。 Azure Virtual Machines はクロスプレミス接続をサポートしているので、Azure Virtual Machines を組織のオンプレミスのドメイン、プライベート クラウド、SAP システム ランドスケープに統合できます。

この記事では、Azure で Windows 仮想マシン (VM) に SAP アプリケーションをデプロイする手順について説明します。 この記事は、[Windows 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事の情報に基づいています。 また、この記事は、SAP ソフトウェアをインストールしてデプロイするときの主要リソースである SAP インストール ドキュメントと SAP Note を補完するものです。

## <a name="prerequisites"></a>前提条件
SAP ソフトウェアのデプロイ用の Azure 仮想マシンをセットアップするには、複数の手順とリソースが必要となります。 作業を開始する前に、Azure の Windows 仮想マシンに SAP ソフトウェアをインストールするための前提条件を満たしていることを確認してください。

### <a name="local-computer"></a>ローカル コンピューター
Windows VM または Linux VM を管理するには、PowerShell スクリプトと Azure Portal を使用します。 どちらのツールも、Windows 7 以降のバージョンの Windows を実行しているローカル コンピューターが必要です。 Linux VM だけを管理し、この作業に Linux コンピューターを使用する場合は、Azure CLI を使用できます。

### <a name="internet-connection"></a>インターネット接続
SAP ソフトウェアのデプロイに必要なツールとスクリプトをダウンロードして実行するには、インターネットに接続している必要があります。 また、Azure Enhanced Monitoring Extension for SAP を実行している Azure VM でもインターネットへのアクセスが必要です。 Azure VM が Azure 仮想ネットワークまたはオンプレミス ドメインに属している場合は、「[プロキシの構成][deployment-guide-configure-proxy]」の説明に従って関連するプロキシ設定が構成されていることを確認してください。

### <a name="microsoft-azure-subscription"></a>Microsoft Azure サブスクリプション
アクティブな Azure アカウントが必要です。

### <a name="topology-and-networking"></a>トポロジとネットワーク
Azure における SAP デプロイのトポロジとアーキテクチャを定義する必要があります。

* 使用する Azure ストレージ アカウント
* SAP システムをデプロイする仮想ネットワーク
* SAP システムをデプロイするリソース グループ
* SAP システムをデプロイする Azure リージョン
* SAP 構成 (2 層または&3; 層)
* VM サイズと、VM にマウントする追加の仮想ハード ディスク (VHD) の数
* SAP 移送/修正システム (CTS) 構成

SAP ソフトウェア デプロイ プロセスを開始する前に、Azure ストレージ アカウントまたは Azure 仮想ネットワークを作成し、構成します。 これらのリソースを作成し、構成する方法については、[Windows 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事をご覧ください。

### <a name="sap-sizing"></a>SAP のサイズ設定
SAP のサイズ設定を行うために、次の情報を把握しておきます。

* 予想される SAP ワークロード (たとえば、SAP Quick Sizer ツールを使用して予想) と SAPS (SAP Application Performance Standard) 番号
* SAP システムの必要な CPU リソースとメモリ使用量
* 1 秒あたりの必要な入出力 (I/O) 操作
* Azure の VM 間での最終的な通信に必要なネットワーク帯域幅
* オンプレミス資産と Azure にデプロイされた SAP システム間に必要なネットワーク帯域幅

### <a name="resource-groups"></a>リソース グループ
Azure Resource Manager では、リソース グループを使用して Azure サブスクリプション内のすべてのアプリケーション リソースを管理できます。 詳細については、「[Azure Resource Manager の概要][resource-group-overview]」をご覧ください。

## <a name="resources"></a>リソース

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP リソース
SAP ソフトウェア デプロイをセットアップするときには、次の SAP リソースが必要です。

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン

* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [1409604]: Azure 上の Windows に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が記載されています。
* SAP Note [2002167]: Red Hat Enterprise Linux 7.x に関する一般情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Azure PowerShell][azure-ps] の一部である SAP 固有の PowerShell コマンドレット。
* [Azure CLI][azure-cli] の一部である SAP 固有の Azure CLI コマンド。

[comment]: <> (MSSedusch TODO SAP ノート 1409604 の SAP Host Agent の ARM パッチ レベルを追加してください)

### <a name="microsoft-resources"></a>Microsoft のリソース
Microsoft の次の記事では、Azure 上の SAP デプロイについて説明しています。

* [Windows 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事
* [Windows 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Windows 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事
* [Azure Portal][azure-portal]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure VM での SAP ソフトウェアのデプロイ シナリオ
VM および関連付けられているディスクを Azure にデプロイする方法が複数用意されています。 選択したデプロイの種類によって、デプロイする VM を準備する手順が異なる場合があるため、デプロイ方法の違いを理解しておくことが重要です。

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする
Azure Marketplace で Microsoft またはサード パーティが提供するイメージを使用して VM をデプロイできます。 Marketplace には、Windows Server とさまざまな Linux ディストリビューションの標準 OS イメージが用意されています。 Microsoft SQL Server などのデータベース管理システム (DBMS) SKU を含むイメージをデプロイすることもできます。 DBMS SKU を含むイメージの使用方法の詳細については、[Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事をご覧ください。

次のフローチャートは、Azure Marketplace から VM をデプロイする際の SAP 固有の一連の手順を示しています。

![Azure Marketplace の VM イメージを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure Portal を使用した仮想マシンの作成
Azure Marketplace のイメージを使用して新しい仮想マシンを作成する最も簡単な方法は、Azure Portal を使用することです。

1.  <https://portal.azure.com/#create> に移動します。  または、Azure Portal のメニューの **[+ 新規]** をクリックします。
2.  **[コンピューティング]** を選択し、デプロイするオペレーティング システムの種類  (Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2) など) を選択します。 既定のリスト ビューには、サポートされているオペレーティング システムがすべて表示されるわけではありません。 完全な一覧を表示するには、**[すべて表示]** を選択します。 SAP ソフトウェア デプロイのサポートされているオペレーティング システムの詳細については、SAP Note [1928533] を参照してください。
3.  次のページで使用条件を確認します。
4.  **[デプロイ モデルの選択]** で **[Resource Manager]** を選択します。
5.  **[作成]**を選択します。

ウィザードの指示に従って、必要なすべてのリソース (ネットワーク インターフェイスやストレージ アカウントなど) だけでなく、仮想マシンを作成するために必要なパラメーターも設定します。 次のようなパラメーターがあります。

1. **[基本]**:
  * **名前**: リソースの名前 (仮想マシン名)。
 * **ユーザー名とパスワード**または **SSH 公開キー**: プロビジョニング時に作成されるユーザーのユーザー名とパスワードを入力します。 Linux 仮想マシンの場合、マシンへのサインインに使用する Secure Shell (SSH) 公開キーを入力できます。
 * **サブスクリプション**: 新しい仮想マシンのプロビジョニングに使用するサブスクリプションを選択します。
 * **リソース グループ**: VM のリソース グループの名前。 新しいリソース グループの名前を入力することも、既存のリソース グループの名前を入力することもできます。
 * **場所**: 新しい仮想マシンをデプロイする場所。 仮想マシンをオンプレミス ネットワークに接続する場合は、Azure をオンプレミス ネットワークに接続する仮想ネットワークの場所を必ず選択します。 詳細については、[Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事の「[Microsoft Azure のネットワーク][planning-guide-microsoft-azure-networking]」をご覧ください。
2. **[サイズ]**:

     サポートされている VM の種類の一覧については、SAP Note [1928533] を参照してください。 Azure Premium Storage を使用する場合は、適切な VM の種類を選択してください。 Premium Storage ではすべての VM の種類はサポートしていません。 詳細については、[Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事の「[ストレージ: Microsoft Azure Storage とデータ ディスク][planning-guide-storage-microsoft-azure-storage-and-data-disks]」および「[Azure Premium Storage][planning-guide-azure-premium-storage]」をご覧ください。

3. **設定**:
   * **ストレージ アカウント**: 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。 すべてのストレージの種類が SAP アプリケーションの実行に対応しているわけではありません。 ストレージの種類の詳細については、[Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事の「[Microsoft Azure Storage][dbms-guide-2.3]」をご覧ください。
   * **仮想ネットワーク**と**サブネット**: 仮想マシンをイントラネットと統合するには、オンプレミス ネットワークに接続されている仮想ネットワークを選択します。
   * **パブリック IP アドレス**: 使用するパブリック IP アドレスを選択するか、新しいパブリック IP アドレスを作成するためのパラメーターを入力します。 パブリック IP アドレスを使用して、インターネット経由で仮想マシンにアクセスできます。 仮想マシンへのアクセスをセキュリティで保護するために、ネットワーク セキュリティ グループも作成してください。
   * **ネットワーク セキュリティ グループ**: 詳細については、「[ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御][virtual-networks-nsg]」をご覧ください。
   * **可用性**: 可用性セットを選択するか、新しい可用性セットを作成するためのパラメーターを入力します。 詳細については、「[Azure の可用性セット][planning-guide-3.2.3]」をご覧ください。
   * **監視**: 監視診断を**無効**にすることができます。 「[監視の構成][deployment-guide-configure-monitoring-scenario-1]」で説明するように、Azure Enhanced Monitoring Extension を有効にするコマンドを実行すると、監視診断が自動的に有効になります。

4. **概要**:

  選択内容を確認し、**[OK]** をクリックします。

選択したリソース グループに仮想マシンがデプロイされます。

#### <a name="create-a-virtual-machine-by-using-a-template"></a>テンプレートを使用した仮想マシンの作成
[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されている SAP テンプレートのいずれかを使用して仮想マシンを作成できます。 また、[Azure Portal][virtual-machines-windows-tutorial]、[PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]、または [Azure CLI][virtual-machines-linux-tutorial] を使用して、仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン&1; 台のみ) テンプレート** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  仮想マシンを&1; 台だけ使用して&2; 層システムを作成する場合は、このテンプレートを使用します。
* [**3 層構成 (複数の仮想マシン) テンプレート** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  複数の仮想マシンを使用して&3; 層システムを作成する場合は、このテンプレートを使用します。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]**:
  * **サブスクリプション**: テンプレートのデプロイに使用するサブスクリプション。
  * **リソース グループ**: テンプレートのデプロイに使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
  * **場所**: テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。

2. **設定**:
  * **SAP System ID (SAP システム ID)**: SAP システム ID (SID)。
  * **OS の種類**: デプロイするオペレーティング システム (Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2) など) を選択します。

    既定のリスト ビューには、サポートされているオペレーティング システムがすべて表示されるわけではありません。 完全な一覧を表示するには、**[すべて表示]** を選択します。 SAP ソフトウェア デプロイのサポートされているオペレーティング システムの詳細については、SAP Note [1928533] を参照してください。
  * **SAP system size (SAP システムのサイズ)**: SAP システムのサイズ。

    新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
  * **システムの可用性** (3 層テンプレートのみ): システムの可用性。

    高可用性インストールに適した構成用の **HA** を選択します。 ABAP SAP Central Services (ASCS) 用の&2; つのデータベース サーバーと&2; つのサーバーが作成されます。
  * **ストレージの種類** (2 層テンプレートのみ): 使用するストレージの種類。

    大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)
      * [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事の「[Microsoft Azure Storage][dbms-guide-2.3]」
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
  * **管理ユーザー名**と**管理パスワード**: ユーザー名とパスワード。
    仮想マシンへのサインインに使用する新しいユーザーが作成されます。
  * **New Or Existing Subnet (新規または既存のサブネット)**: 新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使用するかを指定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、**[Existing (既存)]** を選択します。
  * **サブネット ID**: 仮想マシンの接続先となるサブネットの ID。 仮想マシンをオンプレミス ネットワークに接続する際に使用する、仮想プライベート ネットワーク (VPN) または Azure ExpressRoute 仮想ネットワークのサブネットを選択します。 通常、ID は /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名> のようになります。

3. **使用条件**:   
    法律条項を確認し、同意します。

4.  **[購入]** を選択します。

Azure Marketplace のイメージを使用すると、Azure VM エージェントが既定でデプロイされます。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成
オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM は必要な拡張機能をダウンロードしたり、監視データを収集したりすることができません。 詳細については、「[プロキシの構成][deployment-guide-configure-proxy]」をご覧ください。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)
Azure デプロイが Azure サイト間 VPN 接続または ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 ([Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この作業に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>監視の構成
現在の環境で SAP をサポートできるようにするために、「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の説明に従って Azure Enhanced Monitoring Extension for SAP をセットアップします。 「[SAP リソース][deployment-guide-2.2]」に示すリソースで、SAP Monitoring の前提条件と、SAP カーネルおよび SAP Host Agent の必要最小バージョンを確認してください。

#### <a name="monitoring-check"></a>監視の確認
「[エンド ツー エンド監視の確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」の説明に従って、監視が機能しているかどうかを確認します。

#### <a name="post-deployment-steps"></a>デプロイ後の手順
VM を作成してデプロイしたら、必要なソフトウェア コンポーネントを VM にインストールする必要があります。 VM のこの種のデプロイでは、デプロイ後にソフトウェアをインストールすることになるため、インストールするソフトウェアが Azure 内または別の VM 上で既に使用可能であるか、接続可能なディスクとして提供できる必要があります。 または、オンプレミスの資産 (インストール共有) への接続を提供するクロスプレミス シナリオを使用することを検討します。

Azure に VM をデプロイしたら、オンプレミス環境の場合と同じガイドラインとツールに従って VM に SAP ソフトウェアをインストールします。 Azure VM に SAP ソフトウェアをインストールする場合、SAP と Microsoft では、SAP インストール メディアを Azure VHD にアップロードして保存するか、必要なすべての SAP インストール メディアを格納したファイル サーバーとして機能する Azure VM を作成することを推奨しています。

[comment]: <> (MSSedusch TODO ファイル サーバーや VHD などのファイル管理を推奨する必要があるのはなぜですか。オンプレミスとそれほど異なりますか。)

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>シナリオ 2:  SAP のカスタム イメージを使用して VM をデプロイする
オペレーティング システムまたは DBMS のバージョンによってパッチ要件がそれぞれ異なるため、Azure Marketplace で提供されるイメージがニーズに合わない場合があります。 このような場合、後で再度デプロイできるように、独自の OS/DBMS の VM イメージを使用して VM を作成できます。
プライベート イメージを作成する手順は、Windows と Linux で異なります。

- - -
> ![Windows][Logo_Windows] Windows
>
> 複数の仮想マシンをデプロイするために使用できる Windows イメージを準備するには、オンプレミス VM で Windows 設定 (Windows SID やホスト名など) を抽象化または汎用化する必要があります。 これを行うには、[sysprep](https://msdn.microsoft.com/library/hh825084.aspx) を使用します。
>
> ![Linux][Logo_Linux] Linux
>
> 複数の仮想マシンをデプロイするために使用できる Linux イメージを準備するには、オンプレミス VM で一部の Linux 設定を抽象化または汎用化する必要があります。 これを行うには、`waagent -deprovision` を使用します。 詳細については、[Azure で実行されている Linux 仮想マシンのキャプチャ][virtual-machines-linux-capture-image]に関する記事、および「[Azure Linux エージェント ユーザー ガイド][virtual-machines-linux-agent-user-guide-command-line-options]」をご覧ください。
>
>

- - -
カスタム イメージを作成し、そのイメージを使用して複数の新しい VM を作成できます。 方法については、[Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事をご覧ください。 SAP Software Provision Manager を使用して新しい SAP システムをインストールし、仮想マシンに接続されている VHD からデータベース バックアップを復元するか、Azure Storage からデータベース バックアップを直接復元して (DBMS でサポートされている場合)、データベース コンテンツを設定します。 詳細については、[Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事をご覧ください。 オンプレミス VM (特に&2; 層システム) に SAP システムが既にインストールされている場合は、SAP Software Provisioning Manager でサポートされているシステムの名前変更手順に従って、Azure VM のデプロイ後に SAP システム設定を調整できます (SAP Note [1619720])。 それ以外の場合は、Azure VM のデプロイ後に SAP ソフトウェアをインストールできます。

次のフローチャートは、カスタム イメージから VM をデプロイする際の SAP 固有の一連の手順を示しています。

![Private Marketplace の VM イメージを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-300]

#### <a name="create-the-virtual-machine"></a>仮想マシンの作成
Azure Portal からプライベート OS イメージを使用してデプロイを作成するには、次のいずれかの SAP テンプレートを使用します。 これらのテンプレートは、[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されています。 また、[PowerShell][virtual-machines-upload-image-windows-resource-manager] を使用して、仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン&1; 台のみ) テンプレート** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  仮想マシンを&1; 台だけ使用して&2; 層システムを作成する場合は、このテンプレートを使用します。
* [**3 層構成 (複数の仮想マシン) テンプレート** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  複数の仮想マシンまたは独自の OS イメージを使用して&3; 層システムを作成する場合は、このテンプレートを使用します。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]**:
  * **サブスクリプション**: テンプレートのデプロイに使用するサブスクリプション。
  * **リソース グループ**: テンプレートのデプロイに使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
  * **場所**: テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。
2. **設定**:
  * **SAP System ID (SAP システム ID)**: SAP システム ID。
  * **OS の種類**: デプロイするオペレーティング システムの種類 (Windows または Linux)。
  * **SAP system size (SAP システムのサイズ)**: SAP システムのサイズ。

    新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
  * **システムの可用性** (3 層テンプレートのみ): システムの可用性。

    高可用性インストールに適した構成用の **HA** を選択します。 ASCS 用の&2; つのデータベース サーバーと&2; つのサーバーが作成されます。
  * **ストレージの種類** (2 層テンプレートのみ): 使用するストレージの種類。

    大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)
      * [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事の「[Microsoft Azure Storage][dbms-guide-2.3]」
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
  * **User Image VHD URI (ユーザー イメージ VHD の URI)**: プライベート OS イメージ VHD の URI (例: https://&lt;アカウント名>.blob.core.windows.net/vhds/userimage.vhd)。
  * **User Image Storage Account (ユーザー イメージのストレージ アカウント)**: プライベート OS イメージを保存するストレージ アカウントの名前 (例: https://&lt;アカウント名>.blob.core.windows.net/vhds/userimage.vhd の &lt;アカウント名>)。
  * **管理ユーザー名**と**管理パスワード**: ユーザー名とパスワード。

    仮想マシンへのサインインに使用する新しいユーザーが作成されます。
  * **New Or Existing Subnet (新規または既存のサブネット)**: 新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使用するかを指定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、**[Existing (既存)]** を選択します。
  * **サブネット ID**: 仮想マシンの接続先となるサブネットの ID。 仮想マシンをオンプレミス ネットワークに接続する際に使用する、VPN または ExpressRoute 仮想ネットワークのサブネットを選択します。 ID は、通常、次のようになります。

    /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名>

3. **使用条件**:   
    法律条項を確認し、同意します。

4.  **[購入]** を選択します。

#### <a name="install-the-vm-agent-linux-only"></a>VM エージェントのインストール (Linux のみ)
前のセクションで説明したテンプレートを使用するには、ユーザー イメージに Linux エージェントが既にインストールされている必要があります。インストールされていない場合、デプロイは失敗します。 「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の説明に従って、VM エージェントをダウンロードし、ユーザー イメージにインストールします。 テンプレートを使用しない場合は、VM エージェントを後でインストールすることもできます。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)
Azure デプロイが Azure サイト間 VPN 接続または Azure ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 ([Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この手順に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成
オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM は必要な拡張機能をダウンロードしたり、監視データを収集したりすることができません。 詳細については、「[プロキシの構成][deployment-guide-configure-proxy]」をご覧ください。

#### <a name="configure-monitoring"></a>監視の構成
現在の環境で SAP をサポートできるようにするために、「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の説明に従って Azure Enhanced Monitoring Extension for SAP をセットアップします。 「[SAP リソース][deployment-guide-2.2]」に示すリソースで、SAP Monitoring の前提条件と、SAP カーネルおよび SAP Host Agent の必要最小バージョンを確認してください。

#### <a name="monitoring-check"></a>監視の確認
「[エンド ツー エンド監視の確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」の説明に従って、監視が機能しているかどうかを確認します。




### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミス VM を移行する
このシナリオでは、特定の SAP システムをオンプレミス環境から Azure に移行します。 これを行うには、OS、SAP バイナリ、最終的な DBMS バイナリを格納している VHD と、DBMS のデータ ファイルとログ ファイルを格納している VHD を Azure にアップロードします。 「[シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする][deployment-guide-3.3]」で説明したシナリオとは異なり、このシナリオでは、ホスト名、SAP SID、SAP ユーザー アカウントがオンプレミス環境で構成されているため、Azure VM でこれらを保持します。 OS を汎用化する必要はありません。 ほとんどの場合、このシナリオは、SAP ランドスケープの一部がオンプレミスで実行され、一部が Azure で実行されるクロスプレミス シナリオに適用されます。

このシナリオでは、VM エージェントはデプロイ時に自動的にインストールされません。 SAP を実行するには VM エージェントと Azure Enhanced Monitoring Extension for SAP が必要であるため、仮想マシンの作成後に両方のコンポーネントを手動でダウンロードしてインストールし、有効にする必要があります。

Azure VM エージェントの詳細については、次のリソースを参照してください。

[comment]: <> (MSSedusch TODO 以下の Windows リンクを更新してください)

- - -
> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Azure Linux エージェント ユーザー ガイド][virtual-machines-linux-agent-user-guide]
>
>

- - -

次のフローチャートは、汎用化されていない Azure VHD を使用してオンプレミス VM を移行する際の一連の手順を示しています。

![VM ディスクを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-400]

ディスクが既にアップロードされ、Azure で定義されているという前提で ([Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事を参照)、以下のセクションで説明する作業を行います。

#### <a name="create-a-virtual-machine"></a>仮想マシンの作成
Azure Portal からプライベート OS ディスクを使用してデプロイを作成する場合、[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されている SAP テンプレートを使用します。 また、PowerShell を使用して仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン&1; 台のみ) テンプレート** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  仮想マシンを&1; 台だけ使用して&2; 層システムを作成する場合は、このテンプレートを使用します。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]**:
  * **サブスクリプション**: テンプレートのデプロイに使用するサブスクリプション。
  * **リソース グループ**: テンプレートのデプロイに使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
  * **場所**: テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。
2. **設定**:
  * **SAP System ID (SAP システム ID)**: SAP システム ID。
  * **OS の種類**: デプロイするオペレーティング システムの種類 (Windows または Linux)。
  * **SAP system size (SAP システムのサイズ)**: SAP システムのサイズ。

    新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
  * **ストレージの種類** (2 層テンプレートのみ): 使用するストレージの種類。

    大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)
      * [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事の「[Microsoft Azure Storage][dbms-guide-2.3]」
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
  * **OS disk VHD URI (OS ディスク VHD の URI)**: プライベート OS ディスクの URI (例: https://&lt;アカウント名>.blob.core.windows.net/vhds/osdisk.vhd)。
  * **New Or Existing Subnet (新規または既存のサブネット)**: 新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使用するかを指定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、**[Existing (既存)]** を選択します。
  * **サブネット ID**: 仮想マシンの接続先となるサブネットの ID。 仮想マシンをオンプレミス ネットワークに接続する際に使用する、VPN または Azure ExpressRoute 仮想ネットワークのサブネットを選択します。 ID は、通常、次のようになります。

    /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名>

3. **使用条件**:   
    法律条項を確認し、同意します。

4.  **[購入]** を選択します。

#### <a name="install-the-vm-agent"></a>[VM エージェントのインストール]
前のセクションで説明したテンプレートを使用するには、VM エージェントを OS ディスクにインストールする必要があります。インストールしていない場合、デプロイは失敗します。 「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の説明に従って、VM エージェントをダウンロードし、VM にインストールします。

前のセクションで説明したテンプレートを使用しない場合は、VM エージェントを後でインストールすることもできます。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)
Azure デプロイが Azure サイト間 VPN 接続または ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 ([Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この作業に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成
オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM は必要な拡張機能をダウンロードしたり、監視データを収集したりすることができません。 詳細については、「[プロキシの構成][deployment-guide-configure-proxy]」をご覧ください。

#### <a name="configure-monitoring"></a>監視の構成
現在の環境で SAP をサポートできるようにするために、「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の説明に従って Azure Enhanced Monitoring Extension for SAP をセットアップします。 「[SAP リソース][deployment-guide-2.2]」に示すリソースで、SAP Monitoring の前提条件と、SAP カーネルおよび SAP Host Agent の必要最小バージョンを確認してください。

#### <a name="monitoring-check"></a>監視の確認
「[エンド ツー エンド監視の確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」の説明に従って、監視が機能しているかどうかを確認します。

## <a name="update-the-monitoring-configuration-for-sap"></a>SAP の監視構成の更新
次のような場合に SAP の監視構成を更新します。
* Microsoft と SAP の共同チームが監視機能を拡張し、カウンターの増減を要求している場合。
* Microsoft が、監視データを提供する基になる Azure インフラストラクチャの新しいバージョンを導入したため、Azure Enhanced Monitoring Extension for SAP を変更に適応させる必要がある場合。
* Azure VM に追加の VHD をマウントするか、VHD を削除します。 このシナリオでは、ストレージ関連のデータのコレクションを更新します。 エンドポイントを追加または削除するか、VM に IP アドレスを割り当てることで構成を変更しても、監視構成には影響しません。
* A5 から他の VM サイズへの変更など、Azure VM のサイズを変更した場合。
* Azure VM に新しいネットワーク インターフェイスを追加した場合。

監視設定を更新するには、「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の手順に従って、監視インフラストラクチャを更新します。

## <a name="detailed-tasks-for-sap-software-deployment-on-a-windows-vm"></a>Windows VM に SAP ソフトウェアをデプロイするための作業の詳細
このセクションでは、構成およびデプロイ プロセスで個々の作業を実行する手順について詳しく説明します。

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell コマンドレットのデプロイ
1.  [Microsoft Azure のダウンロード](https://azure.microsoft.com/downloads/) ページに移動します。
2.  **[コマンドライン ツール]** で、**[PowerShell]** の **[Windows のインストール]** をクリックします。
3.  [Microsoft ダウンロード マネージャー] ダイアログ ボックスで、ダウンロードしたファイル (例: WindowsAzurePowershellGet.3f.3f.3fnew.exe) の **[実行]** を選択します。
4.  Microsoft Web プラットフォーム インストーラー (Microsoft Web PI) を実行するには、**[はい]** を選択します。
5.  次のようなページが表示されます。

  ![Azure PowerShell コマンドレットのインストール ページ][deployment-guide-figure-500]<a name="figure-5"></a>

6.  **[インストール]** をクリックし、マイクロソフト ソフトウェア ライセンス条項に同意します。
7.  PowerShell がインストールされます。 **[終了]** をクリックして、インストール ウィザードを閉じます。

PowerShell コマンドレットの更新プログラムがあるかどうかをこまめに確認してください。通常は毎月更新されます。 更新プログラムの有無を確認する最も簡単な方法は、手順 5. でインストール ページが表示されるまでのインストール手順を実行することです。 手順 5. で表示されるページには、コマンドレットのリリース日とリリース番号が記載されています。 SAP Note [1928533] または [2015553] に特に記載されていない限り、最新バージョンの Azure PowerShell コマンドレットを使用することをお勧めします。

コンピューターにインストールされている Azure PowerShell コマンドレットのバージョンを確認するには、次の PowerShell コマンドを実行します。
```powershell
Import-Module Azure
(Get-Module Azure).Version
```
結果は次のようになります。

![Azure PowerShell コマンドレットのバージョン チェックの結果][deployment-guide-figure-600]
<a name="figure-6"></a>

コンピューターにインストールされている Azure コマンドレットのバージョンが最新バージョンの場合、インストール ウィザードの最初のページに示される製品タイトルの後に "**(インストール済み)**" と表示されます。 Azure PowerShell コマンドレットは最新の状態です。 インストール ウィザードを閉じるには、**[終了]** をクリックします。

![Azure PowerShell コマンドレットの最新バージョンがインストールされていることを示す Azure PowerShell コマンドレットのインストール ページ][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI のデプロイ
1.  [Microsoft Azure のダウンロード](https://azure.microsoft.com/downloads/) ページに移動します。
2.  **[コマンドライン ツール]** の **[Azure コマンド ライン インターフェイス]** で、使用しているオペレーティング システムの**インストール** リンクをクリックします。
3.  [Microsoft ダウンロード マネージャー] ダイアログ ボックスで、ダウンロードしたファイル (例: WindowsAzureXPlatCLI.3f.3f.3fnew.exe) の **[実行]** を選択します。
4.  Microsoft Web プラットフォーム インストーラー (Microsoft Web PI) を実行するには、**[はい]** を選択します。
5.  次のようなページが表示されます。

  ![Azure PowerShell コマンドレットのインストール ページ][deployment-guide-figure-500]<a name="figure-5"></a>

6.  **[インストール]** をクリックし、マイクロソフト ソフトウェア ライセンス条項に同意します。
7.  Azure CLI がインストールされます。 **[終了]** をクリックして、インストール ウィザードを閉じます。

Azure CLI の更新プログラムがあるかどうかをこまめに確認してください。通常は毎月更新されます。 更新プログラムの有無を確認する最も簡単な方法は、手順 5. でインストール ページが表示されるまでのインストール手順を実行することです。


コンピューターにインストールされている Azure CLI のバージョンを確認するには、次のコマンドを実行します。
```
azure --version
```

結果は次のようになります。

![Azure CLI のバージョン チェックの結果][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>オンプレミス ドメインへの VM の参加 (Windows のみ)
オンプレミスの Active Directory と DNS が Azure に拡張されるクロスプレミス シナリオで SAP VM をデプロイする場合、VM がオンプレミス ドメインに参加している必要があります。 オンプレミス ドメインに VM を参加させるための詳細な手順と、オンプレミス ドメインのメンバーになるために必要な追加ソフトウェアは、お客様によって異なります。 通常、オンプレミス ドメインに VM を参加させるには、マルウェア対策ソフトウェアや、バックアップまたは監視ソフトウェアなどの追加ソフトウェアをインストールする必要があります。

このシナリオでは、VM が環境内のドメインに参加したときにインターネット プロキシ設定が適用されている場合に、ゲスト VM の Windows ローカル システム アカウント (S-1-5-18) にも同じプロキシ設定が適用されていることを確認する必要があります。 最も簡単な方法は、ドメイン内のシステムに適用されるドメイン グループ ポリシーを使用してプロキシを適用することです。

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Azure VM エージェントのダウンロード、インストール、有効化
汎用化されていない OS イメージ (Windows System Preparation (sysprep ) ツールで作成されていないイメージなど) からデプロイされた仮想マシンの場合、Azure VM エージェントを手動でダウンロードしてインストールし、有効にする必要があります。

Azure Marketplace から VM をデプロイした場合には、この手順は不要です。 Azure Marketplace のイメージには、Azure VM エージェントが既に含まれています。

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Azure VM エージェントのダウンロード:
  1.  [Azure VM エージェント インストーラー パッケージ](https://go.microsoft.com/fwlink/?LinkId=394789)をダウンロードします。
  2.  パーソナル コンピューターまたはサーバーで、VM エージェント MSI パッケージをローカルに保存します。
2.  Azure VM エージェントのインストール:
  1.  リモート デスクトップ プロトコル (RDP) を使用して、デプロイした Azure VM に接続します。
  2.  VM で Windows エクスプローラー ウィンドウを開き、VM エージェントの MSI ファイルのターゲット ディレクトリを選択します。
  3.  Azure VM エージェント インストーラー MSI ファイルを、ローカル コンピューター/サーバーから VM 上の VM エージェントのターゲット ディレクトリにドラッグします。
  4.  VM 上の MSI ファイルをダブルクリックします。
3.  オンプレミス ドメインに参加している VM の場合、「[プロキシの構成][deployment-guide-configure-proxy]」で説明するように、最終的なインターネット プロキシ設定が VM の Windows ローカル システム アカウント (S-1-5-18) にも適用されていることを確認します。 VM エージェントはこのコンテキストで実行されるので、Azure に接続できる必要があります。

Azure VM エージェントを更新する際にユーザーの操作は不要です。 VM エージェントは自動的に更新され、VM の再起動を必要としません。

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
次のコマンドを使用して、Linux の VM エージェントをインストールします。

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL)**

  ```
  sudo yum install WALinuxAgent
  ```

エージェントが既にインストールされている場合、Azure Linux エージェントを更新するには、[VM 上の Azure Linux エージェントを GitHub で最新バージョンに更新する方法][virtual-machines-linux-update-agent]に関する記事に記載されている手順を実行します。

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>プロキシの構成
プロキシを構成する手順は、Windows と Linux で異なります。

#### <a name="windows"></a>Windows
インターネットにアクセスするには、ローカル システム アカウントのプロキシ設定を適切に設定する必要があります。 プロキシ設定がグループ ポリシーで設定されていない場合は、ローカル システム アカウントのプロキシ設定を構成できます。

1. **[スタート]** メニューで「**gpedit.msc**」と入力し、**Enter** キーを押します。
2. **[コンピューターの構成]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[Internet Explorer]** を選択します。 **[コンピューター別にプロキシを設定する (ユーザー別ではなく)]** が無効になっているか、構成されていないことを確認します。
3. **コントロール パネル**で、**[ネットワークと共有センター]** > **[インターネット オプション]** に移動します。
4. **[接続]** タブの **[LAN の設定]** をクリックします。
5. **[設定を自動的に検出する]** チェック ボックスをオフにします。
6. **[LAN にプロキシ サーバーを使用する]** チェック ボックスをオンにし、プロキシのアドレストとポートを入力します。
7. **[Advanced] (詳細設定) ** ボタンを選択します。
8. **[例外]** ボックスに、IP アドレスとして「**168.63.129.16**」と入力します。 **[OK]**を選択します。


#### <a name="linux"></a>Linux
Microsoft Azure ゲスト エージェントの構成ファイル (\\etc\\waagent.conf) で適切なプロキシを構成します。

次のパラメーターを設定します。

1.  **HTTP プロキシ ホスト**。 たとえば、**proxy.corp.local** に設定します。
  ```
  HttpProxy.Host=<proxy host>

  ```
2.  **HTTP プロキシ ポート**。 たとえば、**80** に設定します。
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
3.  エージェントを再起動します。

  ```
  sudo service waagent restart
  ```

\\etc\\waagent.conf のプロキシ設定は、必要な VM 拡張機能にも適用されます。 Azure リポジトリを使用する場合は、これらのリポジトリへのトラフィックがオンプレミスのイントラネットを経由しないようにしてください。 強制トンネリングを有効にするためにユーザー定義ルートを作成した場合は、サイト間 VPN 接続を介さずに、リポジトリへのトラフィックをインターネットに直接ルーティングするルートを追加する必要があります。

* **SLES**

  \\etc\\regionserverclnt.cfg に示されている IP アドレスのルートも追加する必要があります。 次の図は例を示しています。

  ![強制トンネリング][deployment-guide-figure-50]


* **RHEL**

  \\etc\\yum.repos.d\\rhui-load-balancers に示されているホストの IP アドレスのルートも追加する必要があります。 例については、前の図を参照してください。

ユーザー定義ルートの詳細については、[ユーザー定義ルートと IP 転送][virtual-networks-udr-overview]に関する記事をご覧ください。

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Azure Enhanced Monitoring Extension for SAP の構成
[Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]に関するセクションの説明に従って VM を準備すると、Azure VM エージェントが仮想マシンにインストールされます。 次に、Azure グローバル データセンターの Azure 拡張リポジトリで入手できる Azure Enhanced Monitoring Extension for SAP をデプロイします。 詳細については、[Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide-9.1]に関する記事をご覧ください。

PowerShell または Azure CLI を使用して、Azure Enhanced Monitoring Extension for SAP のインストールと構成を行うことができます。 Windows コンピューターを使用して Windows VM または Linux VM に拡張機能をインストールする場合は、[Azure PowerShell][deployment-guide-4.5.1] に関するセクションをご覧ください。 Linux デスクトップを使用して Linux VM に拡張機能をインストールする場合は、[Azure CLI][deployment-guide-4.5.2] に関するセクションをご覧ください。

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Linux VM および Windows VM の Azure PowerShell
PowerShell を使用して Azure Enhanced Monitoring Extension for SAP をインストールするには、次の手順に従います。

1. 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認します。 詳細については、「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」をご覧ください。  
2. 次の PowerShell コマンドレットを実行します。
  使用可能な環境の一覧を表示するには、`commandlet Get-AzureRmEnvironment` を実行します。 パブリック Azure を使用する場合、環境は **AzureCloud** になります。 中国での Azure を使用する場合は、**AzureChinaCloud** を選択します。


      ```powershell
      $env = Get-AzureRmEnvironment -Name <name of the environment>
      Login-AzureRmAccount -Environment $env
      Set-AzureRmContext -SubscriptionName <subscription name>

      Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
      ```

アカウント データを入力し、Azure 仮想マシンを特定すると、スクリプトによって必要な拡張機能がデプロイされ、必要な機能が有効化されます。 これには数分かかる場合があります。
`Set-AzureRmVMAEMExtension` の詳細については、「[Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension]」をご覧ください。

![SAP 固有の Azure コマンドレット Set-AzureRmVMAEMExtension の正常な実行][deployment-guide-figure-900]

`Set-AzureRmVMAEMExtension` 構成スクリプトにより、SAP のホスト監視を構成するためのすべての手順が実行されます。

スクリプトの出力には次の情報が含まれています。

* (OS を含む) ベース VHD と VM にマウントされているその他のすべての VHD の監視が構成されたことが確認されます。
* 次の&2; つのメッセージで、特定のストレージ アカウントのストレージ メトリックの構成が確認されます。
* 出力の&1; つの行に、監視構成の実際の更新の状態が示されます。
* 出力の別の行で、構成がデプロイまたは更新されたことが確認されます。
* 出力の最後の行は情報提供です。 この行には、監視構成をテストするためのオプションが示されます。

Azure Enhanced Monitoring のすべての手順が正常に実行されたことと、Azure インフラストラクチャから必要なデータが提供されていることを確認するには、「[Azure Enhanced Monitoring Extension for SAP の適合性チェック][deployment-guide-5.1]」の説明に従って、Azure Enhanced Monitoring Extension for SAP の適合性チェックを実行します。 Azure 診断が関連データを収集するまで 15 ～ 30 分待ちます。

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux VM 向け Azure CLI
Azure CLI を使用して Azure Enhanced Monitoring Extension for SAP をインストールするには、次の手順に従います。

1. 「[Azure CLI のインストール][azure-cli]」の説明に従って、Azure CLI をインストールします。
2. Azure アカウントでサインインします。

  ```
  azure login
  ```

3. Azure Resource Manager モードに切り替えます。

  ```
  azure config mode arm
  ```

4. Azure Enhanced Monitoring を有効にします。

  ```
  azure vm enable-aem <resource-group-name> <vm-name>
  ```

5. Azure Linux VM 上で Azure Enhanced Monitoring Extension がアクティブであることを確認します。 \\var\\lib\\AzureEnhancedMonitor\\PerfCounters ファイルが存在するかどうかを確認します。 このファイルが存在する場合は、コマンド プロンプトで次のコマンドを実行して、Azure Enhanced Monitor によって収集された情報を表示します。
```
cat /var/lib/AzureEnhancedMonitor/PerfCounters
```

出力は次のようになります。
```
2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
…
…
```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>エンド ツー エンド監視の確認とトラブルシューティング
Azure VM をデプロイし、関連する Azure 監視インフラストラクチャを設定したら、Azure Enhanced Monitoring Extension のすべてのコンポーネントが予想どおりに機能しているかどうかを確認します。

「[Azure Enhanced Monitoring Extension for SAP の適合性チェック][deployment-guide-5.1]」の説明に従って、Azure Enhanced Monitoring Extension for SAP の適合性チェックを実行します。 適合性チェックのすべての結果が良好であり、関連するすべてのパフォーマンス カウンターに "OK" と表示されていれば、Azure 監視が正常に設定されています。 この場合、「[SAP リソース][deployment-guide-2.2]」に示されている SAP Note に記載された SAP Host Agent のインストールに進むことができます。 適合性チェックの結果に、カウンターが見つからないことが示されている場合は、「[Azure 監視インフラストラクチャ構成の正常性チェック][deployment-guide-5.2]」の説明に従って、Azure 監視インフラストラクチャの正常性チェックを実行します。 トラブルシューティングの方法については、「[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」をご覧ください。

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Azure Enhanced Monitoring Extension for SAP の適合性チェック
このチェックでは、SAP アプリケーション内で表示されるすべてのパフォーマンス メトリックが、基になる Azure 監視インフラストラクチャによって提供されていることを確認します。

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM での適合性チェックの実行

1.  Azure 仮想マシンにサインインします (管理者アカウントを使用する必要はありません)。
2.  コマンド プロンプト ウィンドウを開きます。
3.  コマンド プロンプトで、ディレクトリを Azure Enhanced Monitoring Extension for SAP のインストール フォルダー (C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;バージョン>\\drop) に変更します。

  監視の拡張機能のパスに含まれる "*バージョン*" が異なる場合があります。 インストール フォルダーに監視の拡張機能の複数のバージョンのフォルダーがある場合は、AzureEnhancedMonitoring Windows サービスの構成を確認し、*[実行可能ファイルへのパス]* に示されているフォルダーに切り替えます。

  ![Azure Enhanced Monitoring Extension for SAP を実行しているサービスのプロパティ][deployment-guide-figure-1000]

4.  コマンド プロンプトで、パラメーターを指定せずに **azperflib.exe** を実行します。

  > [!NOTE]
  > azperflib.exe はループで実行され、収集したカウンターを 60 秒ごとに更新します。 ループを終了するには、コマンド プロンプト ウィンドウを閉じます。
  >
  >

Azure Enhanced Monitoring Extension がインストールされていない場合や、AzureEnhancedMonitoring サービスが実行されていない場合は、拡張機能が正しく構成されていません。 拡張機能をデプロイする方法の詳細については、「[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」をご覧ください。

##### <a name="check-the-output-of-azperflibexe"></a>azperflib.exe の出力の確認
azperflib.exe の出力には、SAP 用に設定されたすべての Azure パフォーマンス カウンターが表示されます。 収集されたカウンターのリストの下部に表示される概要と正常性インジケーターは、Azure 監視の状態を示しています。

![azperflib.exe の実行による正常性チェックの、問題がないことを示す出力][deployment-guide-figure-1100]
<a name="figure-11"></a>

上記の図に示されている **Counters total** の返された結果 ("empty" と報告されているもの) と、**Health status** の返された結果を確認します。

結果の値は次のように解釈します。

| azperflib.exe の結果の値 | Azure 監視の正常性状態 |
| --- | --- |
| **API Calls - not available (API 呼び出し － 使用不可)** | "not available" であるカウンターは、仮想マシン構成に適用できないか、エラーであると考えられます。 「**Health status**」を参照してください。 |
| **Counters total - empty** |次の&2; つの Azure Storage カウンターは空の場合があります。 <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>他のすべてのカウンターには値が含まれている必要があります。 |
| **Health status** |リターン状態が **OK** の場合にのみ、"OK" と表示されます。 |
| **診断** |正常性状態に関する詳細情報。 |

**Health status** の値が **OK** でない場合は、「[Azure 監視インフラストラクチャ構成の正常性チェック][deployment-guide-5.2]」の手順に従います。

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM での適合性チェックの実行

1.  SSH を使用して Azure 仮想マシンに接続します。

2.  Azure Enhanced Monitoring Extension の出力を確認します。

  a.  `more /var/lib/AzureEnhancedMonitor/PerfCounters` を実行します。

   **予想される結果**: パフォーマンス カウンターのリストが返されます。 ファイルを空にすることはできません。

 b. `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` を実行します。

   **予想される結果**: エラーが **none** である&1; 行が返されます (例: **3;config;Error;;0;0;none;0;1456416792;tst-servercs;**)。

  c. `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` を実行します。

    **予想される結果**: 空または存在しないことを示す結果が返されます。

前のチェックが失敗した場合は、次の追加チェックを実行します。

1.  waagent がインストールされ、有効になっていることを確認します。

  a.  `sudo ls -al /var/lib/waagent/` を実行します。

      **予想される結果**: waagent ディレクトリの内容が表示されます。

  b.  `ps -ax | grep waagent` を実行します。

   **予想される結果**: `python /usr/sbin/waagent -daemon` のようなエントリが&1; つ表示されます。

2. Linux Diagnostic Extension がインストールされ、有効になっていることを確認します。

  a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-'` を実行します。

   **予想される結果**: Linux Diagnostic Extension ディレクトリの内容が表示されます。

 b. `ps -ax | grep diagnostic` を実行します。

   **予想される結果**: `python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon` のようなエントリが&1; つ表示されます。

3.   Azure Enhanced Monitoring Extension がインストールされ、実行されていることを確認します。

  a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-/'` を実行します。

    **予想される結果**: Azure Enhanced Monitoring Extension ディレクトリの内容が表示されます。

  b. `ps -ax | grep AzureEnhanced` を実行します。

     **予想される結果**: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon` のようなエントリが&1; つ表示されます。

3. SAP Note [1031096] の説明に従って SAP Host Agent をインストールし、`saposcol` の出力を確認します。

  a.  `/usr/sap/hostctrl/exe/saposcol -d` を実行します。

  b.  `dump ccm` を実行します。

  c.  **Virtualization_Configuration\Enhanced Monitoring Access** メトリックが **true** であるかどうかを確認します。

SAP NetWeaver ABAP アプリケーション サーバーが既にインストールされている場合は、トランザクション ST06 を開き、Enhanced Monitoring が有効になっているかどうかを確認します。

上記のチェックのいずれかが失敗した場合、拡張機能を再デプロイする方法の詳細については、「[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」をご覧ください。

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Azure 監視インフラストラクチャ構成の正常性チェック
一部の監視データが「[Azure Enhanced Monitoring Extension for SAP の適合性チェック][deployment-guide-5.1]」で説明したテストで示されているように正しく提供されない場合は、`Test-AzureRmVMAEMExtension` コマンドレットを実行して、Azure 監視インフラストラクチャと Monitoring Extension for SAP が正しく構成されているかどうかを確認します。

1.  「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」で説明したように、Azure PowerShell コマンドレットの最新バージョンがインストールされていることを確認します。
2.  次の PowerShell コマンドレットを実行します。 使用可能な環境のリストを表示するには、`Get-AzureRmEnvironment` コマンドレットを実行します。 パブリック Azure を使用する場合は、**AzureCloud** 環境を選択します。 中国で Azure を使用する場合は、**AzureChinaCloud** を選択します。
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Login-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

3.  アカウント データを入力し、Azure 仮想マシンを特定します。

  ![SAP 固有の Azure コマンドレット Test-VMConfigForSAP_GUI の入力ページ][deployment-guide-figure-1200]

4. 選択した仮想マシンの構成がスクリプトによってテストされます。

  ![SAP 用 Azure 監視インフラストラクチャの成功したテストの出力][deployment-guide-figure-1300]

正常性チェックのすべての結果が **OK** であることを確認します。 一部のチェックで **OK** と表示されていない場合は、「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」で説明した更新コマンドレットを実行します。 15 分待ってから、「[Azure Enhanced Monitoring Extension for SAP の適合性チェック][deployment-guide-5.1]」と「[Azure 監視インフラストラクチャ構成の正常性チェック][deployment-guide-5.2]」で説明したチェックをもう一度実行します。 チェックで一部またはすべてのカウンターの問題が引き続き見つかった場合は、「[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」をご覧ください。

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>SAP 用 Azure 監視インフラストラクチャのトラブルシューティング

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure パフォーマンス カウンターまったく表示されない
Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows サービスによって収集されます。 サービスが正しくインストールされていない場合、または VM で実行されていない場合は、パフォーマンス メトリックを収集できません。

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Azure Enhanced Monitoring Extension のインストール ディレクトリが空である

###### <a name="issue"></a>問題
インストール ディレクトリ C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;バージョン>\\drop が空です。

###### <a name="solution"></a>解決策
拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 コンピューターを再起動するか、`Set-AzureRmVMAEMExtension` 構成スクリプトを再実行することが必要な場合があります。

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Azure Enhanced Monitoring のサービスが存在しない

###### <a name="issue"></a>問題
AzureEnhancedMonitoring Windows サービスが存在しません。

azperflib.exe の出力で次のエラーがスローされます。

![Azure Enhanced Monitoring Extension for SAP のサービスが実行されていないことを示す azperflib.exe の実行][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>解決策
サービスが存在しない場合、Azure Enhanced Monitoring Extension for SAP が正しくインストールされていません。 [Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]に関するセクションの該当するデプロイ シナリオの手順に従って、拡張機能を再デプロイします。

拡張機能をデプロイしたら、Azure VM 内で Azure パフォーマンス カウンターが提供されているかどうかを&1; 時間後にもう一度確認します。

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Azure Enhanced Monitoring のサービスは存在するが、起動に失敗する

###### <a name="issue"></a>問題
AzureEnhancedMonitoring Windows サービスが存在し、有効になっていますが、起動に失敗します。 詳細については、アプリケーション イベント ログを確認してください。

###### <a name="solution"></a>解決策
構成が正しくありません。 「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の説明に従って、VM の監視の拡張機能を再起動します。

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] 一部の Azure パフォーマンス カウンターが見つからない
Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows サービスによって収集されます。 このサービスは、複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure 診断から読み取られます。 ストレージ カウンターは、ストレージ サブスクリプション レベルでログ記録から使用されます。

SAP Note [1999351] を使用したトラブルシューティングで問題が解決しない場合は、`Set-AzureRmVMAEMExtension` 構成スクリプトを再実行します。 ストレージ分析または診断カウンターは、有効になった直後には作成されないことがあるため、1 時間待たなければならない場合があります。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure パフォーマンス カウンターまったく表示されない
Azure のパフォーマンス メトリックは、デーモンによって収集されます。 デーモンが実行されていない場合、パフォーマンス メトリックを収集できません。

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Azure Enhanced Monitoring Extension のインストール ディレクトリが空である

###### <a name="issue"></a>問題
\\var\\lib\\waagent\\ ディレクトリに Azure Enhanced Monitoring Extension のサブディレクトリがありません。

###### <a name="solution"></a>解決策
拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 コンピューターを再起動するか、`Set-AzureRmVMAEMExtension` 構成スクリプトを再実行することが必要な場合があります。

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] 一部の Azure パフォーマンス カウンターが見つからない
Azure のパフォーマンス メトリックは、デーモンによって収集されます。デーモンは複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure 診断から読み取られます。 ストレージ カウンターは、ストレージ サブスクリプション レベルでログから使用されます。

既知の問題の最新のリストについては、SAP Note [1999351] を参照してください。この SAP Note には、Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。

SAP Note [1999351] を使用したトラブルシューティングで問題が解決しない場合は、「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の説明に従って、`Set-AzureRmVMAEMExtension` 構成スクリプトを再実行します。 ストレージ分析または診断カウンターは、有効になった直後には作成されないことがあるため、1 時間待たなければならない場合があります。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。

