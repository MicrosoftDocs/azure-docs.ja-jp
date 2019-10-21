---
title: Azure の Ansible モジュールとバージョンのマトリックス
description: Azure の Ansible モジュールとバージョンのマトリックス
keywords: Ansible, ロール, マトリックス, バージョン, Azure, DevOps
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 18654500a78178c46e72f9f6cd01e8507fa179f0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241536"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible モジュールとバージョンのマトリックス

Ansible には、Azure リソースのプロビジョニングと構成に使用する一連のモジュールが含まれています。 これらのリソースには、仮想マシン、スケール セット、ネットワーク サービス、コンテナー サービスがあります。 この記事では、Azure 用のさまざまな Ansible モジュールと、それらが搭載される Ansible のバージョンについて説明します。

## <a name="ansible-modules-for-azure"></a>Azure 用 Ansible モジュール

次のモジュールは、リモート ホストで直接実行することも、プレイブックを介して実行することもできます。

これらのモジュールは、Ansible の公式リリースおよび次の Microsoft のプレイブック ロールから入手できます。

| Azure 用 Ansible モジュール                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible のロール | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Compute**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_availabilityset_facts              | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_deployment                         | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_functionapp                        | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_functionapp_facts                  | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_image                              | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_resource                           | -            | -                           | はい          | はい          | はい          | はい          |
| azure_rm_resource_facts                     | -            | -                           | はい          | はい          | はい          | はい          |
| azure_rm_resourcegroup                      | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_resourcegroup_facts                | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachine                     | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_virtualmachineextension           | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_virtualmachineimage_facts          | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachinescaleset            | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachinescaleset_facts      | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | はい          | はい          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | はい          | はい          |
| **ネットワーク**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | はい          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | はい          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | はい          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | はい          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | はい          | はい          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | はい          | はい          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | はい          | はい          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | はい          | はい          |
| azure_rm_dnsrecordset                       | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_dnsrecordset_facts                 | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_dnszone                            | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_dnszone_facts                      | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_loadbalancer                       | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_loadbalancer_facts                 | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_networkinterface                   | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_networkinterface_facts             | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_publicipaddress                    | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_publicipaddress_facts              | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_route                              | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_routetable                         | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_routetable_facts                   | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_securitygroup                      | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_subnet                             | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_virtualnetwork                     | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualnetwork_facts               | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | はい          | はい          |
| **Storage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_manageddisk_facts                  | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_storageaccount                     | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_storageaccount_facts               | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_storageblob                        | はい          | はい                         | はい          | はい          | はい          | はい          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_webapp                             | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_webapp_facts                       | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | はい          | はい          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | はい          | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_aks                                | -            | -                           | はい          | はい          | はい          | はい          |
| azure_rm_aks_facts                          | -            | -                           | はい          | はい          | はい          | はい          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_containerinstance                  | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_containerregistry                  | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | はい          | はい          |
| **データベース**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mysqldatabase                      | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_mysqlserver                        | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_postgresqldatabase                 | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_postgresqlserver                   | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_sqldatabase                        | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | はい          | はい          | はい          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_sqlserver                          | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_sqlserver_facts                    | -            | はい                         | はい          | はい          | はい          | はい          |
| **Analytics**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | はい          | はい          |
| **統合**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | はい          | はい          |
| **セキュリティ**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | はい          | はい          |
| azure_rm_keyvaultkey                        | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_keyvaultsecret                     | -            | はい                         | はい          | はい          | はい          | はい          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | はい          | はい          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | はい          | はい          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_autoscale_facts            | -            | -                         | -          | はい          | はい          | はい          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | はい          | はい          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | はい          | はい          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure のプレイブック ロールの導入

[azure_preview_module プレイブック ロール](https://galaxy.ansible.com/Azure/azure_preview_modules/)は、最新の Azure モジュールをすべて含んでいます。 更新プログラムとバグ修正は、正式な Ansible リリースよりもタイムリーに行われます。 Azure リソースのプロビジョニングのために Ansible を使用する場合は、`azure_preview_module` プレイブック ロールをインストールすることをお勧めします。

`azure_preview_module` プレイブック ロールは 3 週間ごとにリリースされます。

## <a name="next-steps"></a>次の手順

プレイブック ロールの詳細については、「[Creating Reusable Playbooks (再利用可能なプレイブックの作成)](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)」を参照してください。 
