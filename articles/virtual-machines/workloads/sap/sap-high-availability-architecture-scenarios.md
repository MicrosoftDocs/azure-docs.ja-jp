---
title: SAP NetWeaver のための Azure Virtual Machines 高可用性のアーキテクチャとシナリオ | Microsoft Docs
description: Azure Virtual Machines での SAP NetWeaver のための高可用性のアーキテクチャとシナリオ
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6c329a2b50c946e873391db431c1cd5ff30ab4f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709086"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver のための高可用性のアーキテクチャとシナリオ

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

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

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

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

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP マルチ SID 高可用性構成)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>用語の定義

**高可用性**: IT の中断を最小限に抑える一連のテクノロジを表し、"*同じ*" データ センター内の冗長性、フォールト トレランス、またはフェールオーバーで保護されたコンポーネントを通じて IT サービスのビジネス継続性を提供することで実現されます。 ここの例では、データ センターは 1 つの Azure リージョン内にあります。

**ディザスター リカバリー** :互いに数百マイル離れているような "*さまざまな*" データ センター間で、IT サービスの中断と復旧を最小にすることも表します。 ここの例では、データ センターは同じ地理的リージョン内のさまざまな Azure リージョンや顧客が設定した場所に存在している可能性があります。


## <a name="overview-of-high-availability"></a>高可用性の概要
Azure での SAP 高可用性は、次の 3 つの種類に分類できます。

* **Azure インフラストラクチャの高可用性** 

    たとえば、高可用性には、コンピューティング (VM)、ネットワーク、ストレージおよび SAP アプリケーションの可用性を高めるためのその利点を含めることがあります。

* **Azure インフラストラクチャ VM の再起動を利用した SAP アプリケーションの*高可用性*の実現**: 

    Linux で Windows Server フェールオーバー クラスタリング (WSFC) や Pacemaker などの機能を使用しない場合は、Azure VM の再起動を利用します。 これにより、Azure 物理サーバー インフラストラクチャと基になる Azure プラットフォーム全体の計画済みおよび計画外のダウンタイムに対して SAP システムを保護します。

* **SAP アプリケーションの高可用性** 

    SAP システム全体の高可用性を実現するには、SAP システムの重要なすべてのコンポーネントを保護する必要があります。 例:
    * SAP アプリケーション サーバーの冗長性。
    * 一意のコンポーネント。 たとえば、SAP ASCS/SCS インスタンスやデータベース管理システム (DBMS) などの単一障害点 (SPOF) コンポーネントがあります。

Azure における SAP の高可用性は、オンプレミスの物理環境または仮想環境内の SAP 高可用性とは異なります。 「[SAP NetWeaver High Availability and Business Continuity in Virtual Environments with VMware and Hyper-V on Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper]」 (Microsoft Windows で VMware や Hyper-V を使用した仮想環境での、SAP NetWeaver の高可用性とビジネス継続性) のホワイト ペーパーでは、Windows の仮想環境での SAP 高可用性の標準的な構成について説明しています。

Windows 向けはありますが、Linux 向けの sapinst-integrated SAP 高可用性構成は用意されていません。 Linux 向け SAP 高可用性オンプレミスについては、[高可用性パートナー情報][sap-ha-partner-information]に関するページをご覧ください。

## <a name="azure-infrastructure-high-availability"></a>Azure インフラストラクチャの高可用性

### <a name="sla-for-single-instance-virtual-machines"></a>単一インスタンス仮想マシンの SLA

現在 Premium Storage では、99.9% の単一 VM SLA があります。 単一 VM の可用性について理解するために、さまざまな [Azure サービス レベル アグリーメント][azure-sla]を利用できる製品を構築できます。

計算の基準は、1 か月あたり 30 日間または 43,200 分です。 たとえば、0.05% のダウンタイムは 21.6 分に相当します。 通常どおり、さまざまなサービスの可用性は次の方法で計算されます。

(可用性サービス #1/100) * (可用性サービス #2/100) * (可用性サービス #3/100) \*…

例:

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975、つまり 99.75% の全体的な可用性。

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>同じ可用性セット内の仮想マシンの複数のインスタンス
同じ*可用性セット*にデプロイされた 2 つ以上のインスタンスがあるすべての仮想マシンについては、仮想マシンが 99.95% 以上の時間において少なくとも 1 つのインスタンスに接続されることを保証します。

2 つ以上の VM が同じ可用性セットに含まれている場合、基盤となる Azure プラットフォームによって、可用性セット内の各仮想マシンに*更新ドメイン*と*障害ドメイン*が割り当てられます。

* **更新ドメイン**では、Azure インフラストラクチャの計画済みメンテナンス時に、複数の VM が同時に再起動しないことが保証されます。 一度に 1 つの VM のみが再起動されます。

* **障害ドメイン**では、共通の電源とネットワーク スイッチを共有していないハードウェア コンポーネントに VM がデプロイされることが保証されます。 サーバー、ネットワーク スイッチ、または電源で計画外のダウンタイムが発生した場合に、1 つの VM のみが影響を受けます。

詳細については、「[Azure での Windows 仮想マシンの可用性の管理][azure-virtual-machines-manage-availability]」をご覧ください。

可用性セットは、次の高可用性を実現するために使用されます。

* SAP アプリケーション サーバーの冗長性。  
* SAP ASCS/SCS インスタンスや DBMS などの SPOF を保護する 2 つ以上のノード (VM など) があるクラスター。


### <a name="azure-availability-zones"></a>Azure 可用性ゾーン
Azure では、[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) の概念をさまざまな [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)全体にロールアウトしています。 Availability Zones が提供される Azure リージョンには複数のデータ センターがあり、電源、冷却装置、ネットワークが独立しています。 1 つの Azure リージョン内で異なるゾーンを提供する理由は、2 つまたは 3 つの Availability Zones にまたがってアプリケーションをデプロイできるようにするためです。 電源やネットワークの問題により 1 つの可用性ゾーンのインフラストラクチャだけが影響を受けた場合でも、Azure リージョン内のアプリケーションのデプロイは完全に機能します。 最終的には、1 つのゾーンの VM がいくつか失われて、容量が若干減る可能性があります。 ただし、他の 2 つのゾーンの VM はまだ稼働しています。 ゾーンを提供する Azure リージョンの一覧は、[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) に関するページにあります。

Availability Zones を使用する際には、考慮すべき点がいくつかあります。 考慮事項を次に示します。

- 可用性ゾーン内に Azure 可用性セットをデプロイすることはできません。 可用性ゾーンまたは可用性セットのいずれかを VM のデプロイ フレームとして選択する必要があります。
- [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) を使用して Windows フェールオーバー クラスター サービスまたは Linux Pacemaker に基づくフェールオーバー クラスター ソリューションを作成することはできません。 代わりに、[Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があります
- Azure Availability Zones は、1 つのリージョン内のさまざまな異なる間の特定の距離を保証するものではありません
- 異なる Azure リージョン内の異なる Azure Availability Zones 間のネットワーク待ち時間は、Azure リージョンごとに異なる可能性があります。 1 つのゾーンからアクティブな DBMS VM までのネットワーク待ち時間がビジネス プロセスへの影響から依然として許容できるようなケースでは、顧客が異なるゾーンをまたがってデプロイされた SAP アプリケーション レイヤーを合理的に実行できる場合があります。 一方、あるゾーン内のアクティブな DBMS VM と別のゾーンにある VM 内の SAP アプリケーション インスタンスとの間の待ち時間が過度に侵入的であり、SAP ビジネス プロセスにとって許容できない顧客シナリオがあります。 そのため、待ち時間が長すぎる場合は、デプロイ アーキテクチャをアプリケーションのアクティブ/アクティブ アーキテクチャまたはアクティブ/パッシブ アーキテクチャとは異なるものにする必要があります。
- Azure Availability Zones にデプロイするには、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)の使用が必須です 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>仮想マシンの計画済みメンテナンスと計画外メンテナンス

2 種類の Azure プラットフォーム イベントが仮想マシンの可用性に影響を与えることがあります。

* **計画済みメンテナンス** イベントは、Microsoft によって、基になる Azure プラットフォームに対して行われる定期的な更新です。 この更新により、仮想マシンが実行されるプラットフォーム インフラストラクチャの全体の信頼性、パフォーマンス、およびセキュリティが向上します。

* **計画外メンテナンス** イベントは、仮想マシンの基盤となっているハードウェアや物理インフラストラクチャに何らかの不具合があった場合に発生します。 それには、ローカル ネットワーク障害、ローカル ディスク障害、またはその他のラック レベルでの障害が挙げられます。 そのような障害が検知されると、Azure プラットフォームは、仮想マシンをホストしている異常な物理サーバーから正常な物理サーバーへ、仮想マシンを自動的に移行します。 そのようなイベントはまれですが、それらによっても仮想マシンが再起動されることがあります。

詳細については、「[Azure での Windows 仮想マシンの可用性の管理][azure-virtual-machines-manage-availability]」をご覧ください。

### <a name="azure-storage-redundancy"></a>Azure Storage の冗長性
ストレージ アカウント内のデータは、持続性と高可用性を確保するために常にレプリケートされており、一時的なハードウェア障害が発生した場合でも Azure Storage SLA が満たされます。

Azure Storage は、既定で、データの 3 つのイメージを維持するため、複数の Azure ディスクにまたがる RAID 5 や RAID 1 を使用する必要はありません。

詳細については、「[Azure Storage のレプリケーション][azure-storage-redundancy]」をご覧ください。

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks は Azure Resource Manager のリソースの種類で、Azure Storage アカウントに格納されている仮想ハード ディスク (VHD) の代わりに使用することをお勧めします。 マネージド ディスクは接続先の仮想マシンの Azure 可用性セットに自動的に配置されます。 それにより、仮想マシンとそこで実行されるサービスの可用性が向上します。

詳細については、「[Azure Managed Disks の概要][azure-storage-managed-disks-overview]」をご覧ください。

仮想マシンのデプロイと管理が簡単なため、マネージド ディスクを使うことをお勧めします。



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Azure インフラストラクチャ高可用性を利用した SAP アプリケーションの*高可用性*の実現

Linux で WSFC や Pacemaker などの機能 (現在 SUSE Linux Enterprise Server [SLES] 12 以降でのみサポート) を使用しない場合は、Azure VM の再起動を利用します。 これにより、Azure 物理サーバー インフラストラクチャと基になる Azure プラットフォーム全体の計画済みおよび計画外のダウンタイムに対して SAP システムを保護します。

この方法について詳しくは、「[Utilize Azure infrastructure VM restart to achieve higher availability of the SAP system][sap-higher-availability]」 (SAP システムでさらに高い可用性を実現するために Azure インフラストラクチャ VM の再起動を利用する) を参照してください。

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Azure IaaS での SAP アプリケーションの高可用性

SAP システム全体の高可用性を実現するには、SAP システムの重要なすべてのコンポーネントを保護する必要があります。 例:
  * SAP アプリケーション サーバーの冗長性。
  * 一意のコンポーネント。 たとえば、SAP ASCS/SCS インスタンスやデータベース管理システム (DBMS) などの単一障害点 (SPOF) コンポーネントがあります。

次に、3 つすべての重要な SAP システム コンポーネントの高可用性を実現する方法について詳しく説明します。

### <a name="high-availability-architecture-for-sap-application-servers"></a>SAP アプリケーション サーバーの高可用性アーキテクチャ

> このセクションは次に適用されます。
>
> ![Windows][Logo_Windows] Windows および ![Linux][Logo_Linux] Linux
>

SAP アプリケーション サーバーおよびダイアログ インスタンスについては、通常、特定の高可用性ソリューションは不要です。 高可用性は冗長性によって実現し、Azure Virtual Machines のさまざまなインスタンスで複数のダイアログ インスタンスを構成します。 Azure Virtual Machines の 2 つのインスタンスに少なくとも 2 つの SAP アプリケーション インスタンスをインストールする必要があります。

![図 1:高可用性 SAP アプリケーション サーバー][sap-ha-guide-figure-2000]

_**図 1:** 高可用性 SAP アプリケーション サーバー_"

SAP アプリケーション サーバー インスタンスをホストするすべての仮想マシンを、同じ Azure 可用性セットに配置する必要があります。 Azure 可用性セットでは次のことが保証されます。

* すべての仮想マシンは、同じ更新ドメインに含まれます。  
    更新ドメインでは、計画的メンテナンス ダウンタイムの間に仮想マシンが同時に更新されないことが保証されます。

    Azure スケール ユニット内のさまざまな更新ドメインと障害ドメインに組み込まれた基本的な機能については、「[更新ドメイン][planning-guide-3.2.2]」で既に紹介しました。

* すべての仮想マシンは、同じ障害ドメインの一部になります。  
    障害ドメインでは、単一障害点によってすべての仮想マシンの可用性が影響を受けないように仮想マシンがデプロイされることが保証されます。

Azure スケール ユニット内の Azure 可用性セットで使用できる更新ドメインと障害ドメインの数に制限はありません。 1 つの可用性セットに VM を追加し続ける場合、2 つ以上の VM は最終的に同じ障害ドメインまたは更新ドメインに含まれます。

専用の VM 内に少数の SAP アプリケーション サーバー インスタンスをデプロイし、5 つの更新ドメインがあるとした場合、次のような状態になります。 可用性セット内の更新ドメインと障害ドメインの実際の最大数は、後で変わる可能性があります。

"![図 2:Azure 可用性セット内の SAP アプリケーション サーバーの高可用性][planning-guide-figure-3000]
" _**図 2:** Azure 可用性セット内の SAP アプリケーション サーバーの高可用性_"

詳細については、「[Azure での Windows 仮想マシンの可用性の管理][azure-virtual-machines-manage-availability]」をご覧ください。

詳しくは、SAP NetWeaver のための Azure Virtual Machines の計画と実装に関するドキュメントの [Azure の可用性セット][planning-guide-3.2.3]のセクションをご覧ください。

**アンマネージド ディスクのみ:** Azure ストレージ アカウントが単一障害点になる可能性があるため、少なくとも 2 つの Azure ストレージ アカウントを用意し、それぞれにおいて少なくとも 2 つの仮想マシンに分散させることが重要です。 理想的な設定としては、SAP ダイアログ インスタンスを実行する各仮想マシンのディスクを、異なるストレージ アカウントにデプロイします。

> [!IMPORTANT]
> SAP 高可用性インストールには、Azure マネージド ディスクを使用することを強くお勧めします。 マネージド ディスクは接続されている仮想マシンの可用性セットに自動的に配置されるため、仮想マシンと仮想マシンで実行されているサービスの可用性を向上させます。  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Windows での SAP ASCS/SCS インスタンスの高可用性のアーキテクチャ

> ![Windows][Logo_Windows] Windows
>

WSFC ソリューションを使用して、SAP ASCS/SCS インスタンスを保護できます。 ソリューションには、2 つのバリエーションがあります。

* **クラスター化された共有ディスクを使用した SAP ASCS/SCS インスタンスのクラスタリング**: このアーキテクチャの詳細については、[クラスター共有ディスクを使用した Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスのクラスタリング][sap-high-availability-guide-wsfc-shared-disk]に関するページを参照してください。   

* **ファイル共有を使用した SAP ASCS/SCS インスタンスのクラスタリング**: このアーキテクチャの詳細については、[ファイル共有を使用した Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスのクラスタリング][sap-high-availability-guide-wsfc-file-share]に関するページを参照してください。

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Linux での SAP ASCS/SCS インスタンスの高可用性アーキテクチャ

> ![Linux][Logo_Linux] Linux
> 
> SUSE クラスター フレームワークを使用した SAP ASCS/SCS インスタンスのクラスタリングについて詳しくは、「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性][sap-suse-ascs-ha]」をご覧ください。. For alternative HA architecture on SLES, which doesn't require highly available NFS see [High-availability guide for SAP NetWeaver on SUSE Linux Enterprise Server with Azure NetApp Files for SAP applications][sap-suse-ascs-ha-anf]。

Red Hat クラスター フレームワークを使用した SAP ASCS/SCS インスタンスのクラスタリングの詳細については、「[Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)」を参照してください


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>クラスター化された SAP ASCS/SCS インスタンスのための SAP NetWeaver マルチ SID の構成

> ![Windows][Logo_Windows] Windows
> 
> 現在、マルチ SID は WSFC でのみサポートされます。 マルチ SID は、ファイル共有と共有ディスクを使用してサポートされます。
> 
> マルチ SID 高可用性アーキテクチャについて詳しくは、次をご覧ください。

* [Windows Server フェールオーバー クラスタリングとファイル共有を使用する SAP ASCS/SCS インスタンス マルチ SID の高可用性に関するページ][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Windows Server フェールオーバー クラスタリングと共有ディスクを使用する SAP ASCS/SCS インスタンス マルチ SID の高可用性に関するページ][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>高可用性の DBMS インスタンス

DBMS は、SAP システムでの単一接続点でもあります。 高可用性ソリューションを使用して保護する必要があります。 次の図では、Azure の SQL Server AlwaysOn 高可用性ソリューションと、Windows Server フェールオーバー クラスタリングおよび Azure 内部ロード バランサーを示します。 SQL Server AlwaysOn は、独自の DBMS レプリケーションを使用して、DBMS のデータとログ ファイルをレプリケートします。 この場合、クラスター共有ディスクは必要なく、セットアップ全体が簡単になります。

![図 3:高可用性の SAP DBMS と SQL Server AlwaysOn の例][sap-ha-guide-figure-2003]

_**図 3:** 高可用性の SAP DBMS と SQL Server AlwaysOn の例_"

Azure Resource Manager デプロイ モデルを使用した Azure での SQL Server DBMS のクラスタリングの詳細については、次の記事をご覧ください。

* [Resource Manager を使用した Azure 仮想マシンでの AlwaysOn 可用性グループの手動での構成に関するページ][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Azure の AlwaysOn 可用性グループに使用する Azure 内部ロード バランサーの構成に関するページ][virtual-machines-windows-portal-sql-alwayson-int-listener]

Azure Resource Manager デプロイ モデルを使用した Azure での SAP HANA DBMS のクラスタリングの詳細については、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」をご覧ください。
