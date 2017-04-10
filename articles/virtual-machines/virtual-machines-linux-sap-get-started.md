---
title: "Azure の Linux VM における SAP の概要| Microsoft Docs"
description: "Microsoft Azure 上の仮想マシン (VM) で実行される SAP ソリューションについて説明します"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 9e64fc5425db5ffde6af493e961edfcac527b6df
ms.lasthandoff: 03/03/2017


---
# <a name="using-sap-on-azure-linux-virtual-machines-vms"></a>SAP on Azure Linux Virtual Machines (VM) の使用
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

[azure-cli]:../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (Linux 仮想マシン (VM) 上の SAP NetWeaver – DBMS デプロイ ガイド) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM と VHD のキャッシング) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (ソフトウェア RAID) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS デプロイの構造) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure VM の高可用性と障害復旧) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 以降) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 以前のリリース) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Microsoft Azure Marketplace からの SQL Server イメージの使用) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Azure での一般的な SAP 用 SQL Server の概要) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS の詳細) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (ストレージの構成) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (バックアップと復元) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (バックアップと復元のパフォーマンスに関する考慮事項) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (その他) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (Linux 仮想マシン (VM) 上の SAP NetWeaver – デプロイ ガイド) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP リソース) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (カスタム イメージを使用して VM をデプロイする) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell コマンドレットのデプロイ) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP 関連の PowerShell コマンドレットのダウンロードとインポート) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (オンプレミス ドメインへの VM の参加 (Windows のみ)) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Azure VM エージェントのダウンロード、インストール、有効化) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Azure Enhanced Monitoring Extension for SAP の構成) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Azure Enhanced Monitoring for SAP の適合性チェック) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Azure 監視インフラストラクチャ構成のヘルス チェック) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (SAP 用 Azure 監視インフラストラクチャのトラブルシューティング)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (Azure Virtual Machines (VMs) への SAP NetWeaver の導入 - 計画/導入ガイド) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (リソース) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP アプリケーション サーバーの高可用性) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (SAP インスタンスでの自動開始の使用) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (クラウドのみ - オンプレミスの顧客ネットワークとの依存関係を持たない仮想マシンの Azure へのデプロイ) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (クロス プレミス - オンプレミス ネットワークに完全に統合されることを要件とする&1; つまたは複数の SAP VM の Azure へのデプロイ) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure リージョン) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (障害ドメイン) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (アップグレード ドメイン) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure の可用性セット) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure Virtual Machine の概念) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (顧客固有のイメージを使用する VM のデプロイ) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備) [planning-guide-5.2.2]:virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (SAP 用の顧客固有のイメージを使用する VM のデプロイの準備) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Azure 用の VM と SAP の準備) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure ディスクと Azure イメージの違い) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (オンプレミスから Azure への VHD のアップロード) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Azure Storage アカウント間でのディスクのコピー) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP デプロイ用の VM/VHD 構造) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (アタッチされたディスクに対する自動マウントの設定) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (単一の VM と SAP NetWeaver のデモ/トレーニング シナリオ) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (SAP インスタンスのクラウド専用のデプロイの概念) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)

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
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

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
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:windows/classic/ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:windows/classic/ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
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
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

Microsoft Azure を SAP 対応クラウド パートナーとして選択すると、ミッション クリティカルな SAP ワークロードを、拡張性が高く、SAP に準拠した、エンタープライズで実証済みのプラットフォームで確実に実行できます。  Azure を使用して、拡張性と柔軟性、そしてコストの削減を実現しましょう。 Microsoft と SAP のパートナーシップの拡大により、Azure で開発/テストから運用環境をまたいで SAP アプリケーションを実行できるうえ、そのすべてがサポートされます。 SAP NetWeaver から SAP S4/HANA、Linux から Windows、そして SAP HANA から SQL まで対応しています。 

Microsoft Azure 仮想マシン サービスと SAP HANA on Azure L インスタンスを通じて、マイクロソフトは包括的な IaaS (Infrastructure as a Service) プラットフォームを提供しています。 広範囲の SAP ソリューションが Azure でサポートされているため、この "入門ドキュメント" は、マイクロソフトが現在公開している一連の SAP ドキュメントの目次として機能します。 より多くのタイトルがマイクロソフトのドキュメント ライブラリに追加されるとともに、ここに表示される情報も追加されます。 

## <a name="sap-hana-certifications-on-microsoft-azure"></a>Microsoft Azure における SAP HANA 認定資格
| SAP 製品 | サポート対象 OS | Azure のサービス |
| --- | --- | --- |
| SAP HANA Developer Edition (SQLODBC、ODBO (Windows のみ)、ODBC、JDBC ドライバー、HANA Studio、HANA データベースで構成される HANA クライアント ソフトウェアを含む) |Red Hat Enterprise Linux、SUSE Linux Enterprise |A7、A8 |
| HANA One |Red Hat Enterprise Linux、SUSE Linux Enterprise |DS14_v2 (一般公開時) |
| SAP S/4HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |GS5、SAP HANA on Azure (L インスタンス) 用に制御された可用性 |
| Suite on HANA、OLTP |Red Hat Enterprise Linux、SUSE Linux Enterprise |SAP HANA on Azure (L インスタンス) |
| HANA Enterprise for BW、OLAP |Red Hat Enterprise Linux、SUSE Linux Enterprise |単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |
| SAP BW/4HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認定資格
以下の SAP 製品に関して Microsoft Azure は認定を受けており、マイクロソフトと SAP から完全サポートを受けることができます。

| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business Suite ソフトウェア |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (Windows のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、GS1 から GS5 |
| SAP Business All-in-One |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (Windows のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、GS1 から GS5 |
| SAP BusinessObjects BI |Windows |該当なし |A5 から A11、D11 から D14、DS11 から DS14、GS1 から GS5 |
| SAP NetWeaver |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (Windows のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、GS1 から GS5 |

## <a name="getting-started-with-sap-hana-on-azure"></a>SAP HANA on Azure の概要
タイトル: Azure VM に SAP HANA を手動でインストールするためのクイック スタート ガイド

要約: このクイック スタート ガイドは、SAP NetWeaver 7.5 と SAP HANA SP12 の手動インストールを行って、単一インスタンスの SAP HANA プロトタイプ/デモ システムを Azure VM でセットアップする際に役立ちます。 このガイドは、JSON テンプレートを使用する方法を含め、Azure Portal と Powershell/CLI のいずれかを使って仮想マシンまたは仮想ネットワークをデプロイする方法など、Azure IaaS の基本について読者が熟知していることを前提としています。 さらに、読者が SAP HANA、SAP NetWeaver、オンプレミスでのそのインストール方法について理解しているとも想定しています。

更新日: 2016 年 12 月

[ガイドはこちら](virtual-machines-linux-sap-hana-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ
タイトル: SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ

概要: これは、アーキテクチャ/テクニカル デプロイ ガイドであり、Azure の新しい SAP HANA on Azure (L インスタンス) で SAP をデプロイする場合に役立つ情報を提供します。 SAP ソリューションの特定のセットアップを取り扱った包括的なガイドではなく、初期のデプロイメントと継続的な操作に役立つ情報を提供することを意図しています。 SAP HANA のインストールに関連する SAP ドキュメント (またはこのトピックについて取り上げた多くの SAP サポート ノート) の代わりとしてこのガイドを使用しないでください。 概要を説明し、Azure (L インスタンス) に SAP HANA をインストールする際の追加情報を提供するものです。

更新日: 2016 年 12 月

[ガイドはこちら](./linux/sap-hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続
タイトル: SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続

概要: お客様とマイクロソフト エンタープライズ アカウント チームとの間で Azure (L インスタンス) の SAP HANA の購入処理が終了した後は、適切に接続するためのさまざまな構成が必要です。  このドキュメントでは、理解しておいていただきたい情報を、必須となる情報とともにおおまかに説明します。 このドキュメントでは、収集する必要がある情報と、スクリプトの実行が必要になる構成の概要を説明します。 

更新日: 2016 年 12 月

[ガイドはこちら](./linux/sap-hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="install-sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) への SAP HANA のインストール
タイトル: SAP HANA on Azure (L インスタンス) への SAP HANA のインストール

概要: このドキュメントでは、Azure の L インスタンスに SAP HANA をインストールするためのセットアップ手順について説明します。

更新日: 2016 年 12 月

[ガイドはこちら](./linux/sap-hana-overview-sap-hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) の高可用性と障害復旧
タイトル: SAP HANA on Azure (L インスタンス) の高可用性と障害復旧

概要: 高可用性 (HA) と障害復旧 (DR) は、ミッション クリティカルな SAP HANA on Azure (L インスタンス) サーバーを実行する場合の重要な要素です。 正しい HA/DR 戦略を適切に構築、実装するには、SAP、システム インテグレーター、Microsoft と協力することが重要です。 環境に固有の目標復旧時点 (RPO) と目標復旧時間 (RTO) のような重要な考慮事項を検討する必要があります。  このドキュメントでは、お客様に必要なレベルの HA と DR を実現するためのオプションについて説明します。

更新日: 2016 年 12 月

[このドキュメントはこちらからご利用いただけます](./linux/sap-hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="troubleshooting-and-monitoring-of-sap-hana-on-azure-large-instances"></a>Azure (L インスタンス) の SAP HANA のトラブルシューティングと監視
タイトル: Azure (L インスタンス) の SAP HANA のトラブルシューティングと監視

概要: このガイドは、Azure 環境の SAP HANA の監視機能を構築する際に役立つ情報のほか、トラブルシューティングに関する追加情報を取り扱います。 

更新日: 2016 年 12 月

[このドキュメントはこちらからご利用いただけます](./linux/sap-hana-overview-troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quickstart-guide-for-netweaver-on-suse-linux-on-azure"></a>Azure 上の SUSE Linux での NetWeaver のクイック スタート ガイド
タイトル: Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト 

要約: この記事では、Microsoft Azure SUSE Linux 仮想マシン (VM) で SAP NetWeaver を実行する際のさまざまな考慮事項について説明します。 2016 年 5 月 19 日 の時点で、SAP NetWeaver は Azure 上の SUSE Linux VM で正式にサポートされています。 Linux のバージョンや SAP カーネルのバージョンなど、すべての詳細については、SAP Note 1928533「SAP Applications on Azure: Supported Products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類)」を参照してください。

更新日: 2016 年 9 月

[ガイドはこちら](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="3da0389e-708b-4e82-b2a2-e92f132df89c"></a>計画と導入
タイトル:  Linux 仮想マシン (VM) 上の SAP NetWeaver – 計画/導入ガイド

概要: SAP NetWeaver を Azure Virtual Machines で実行することを検討している方は、まずこのホワイト ペーパーをお読みください。 既にあるまたは導入を予定している SAP NetWeaver ベースのシステムを Azure Virtual Machines 環境にデプロイできるかどうかを評価するうえで参考になります。 SAP NetWeaver のデプロイに伴うさまざまなシナリオのほか、Azure に固有の SAP 構成も取り上げています。 このホワイト ペーパーには、ハイブリッド SAP ランドスケープを運用するために SAP/Azure 側で必要となる構成情報が詳細に説明されています。 SAP NetWeaver ベースのシステムの高可用性を IaaS で確保するために講じることのできる対策についても触れています。

最終更新日: 2016 年 3 月

[ガイドはこちら][計画ガイド]

## <a name="6aadadd2-76b5-46d8-8713-e8d63630e955"></a>デプロイ
タイトル: Linux 仮想マシン (VM) 上の SAP NetWeaver – デプロイ ガイド

概要: SAP NetWeaver ソフトウェアを Azure 内の仮想マシンにデプロイする手順について説明します。 このホワイト ペーパーでは、Azure Monitoring Extensions for SAP の有効化に重点を置きつつ (Azure Monitoring Extensions for SAP で発生する問題のトラブルシューティングに関する推奨事項など)、3 つのデプロイ シナリオを中心に説明しています。 既に計画/導入ガイドを読んでいることを前提としています。

最終更新日: 2016 年 3 月

[ガイドはこちら][デプロイ ガイド]

## <a name="1343ffe1-8021-4ce6-a08d-3a1553a4db82"></a>DBMS デプロイ ガイド
タイトル: Linux 仮想マシン (VM) 上の SAP NetWeaver – DBMS デプロイ ガイド

概要: このホワイト ペーパーでは、SAP と連携させる DBMS システムの計画と導入に関する注意点を取り上げています。 最初のパートでは、全般的な注意点が列挙されています。 その後、SAP でサポートされる各種 DBMS を Azure にデプロイする作業に関連した説明が続きます。 紹介されている DBMS は、SQL Server、SAP ASE、Oracle です。 その個々の領域において、これらの DBMS と共に SAP システムを Azure で実行する際に考慮する必要のある事柄を説明します。 Azure 上の各種 DBMS でサポートされるバックアップと高可用性の手法といったテーマは、SAP アプリケーションの使用法の観点から取り上げています。

最終更新日: 2016 年 3 月

[ガイドはこちら][dbms ガイド]


