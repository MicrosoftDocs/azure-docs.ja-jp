---
title: Azure の Ansible モジュールとバージョンのマトリックス
description: Azure の Ansible モジュールとバージョンのマトリックス
ms.service: azure
keywords: Ansible, ロール, マトリックス, バージョン, Azure, DevOps
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: f2f1dccfd5b91205d0673c2b82ad7cfa673f61f6
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791819"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible モジュールとバージョンのマトリックス

## <a name="ansible-modules-for-azure"></a>Azure 用 Ansible モジュール
Ansible には多数のモジュールが付属しています。これらのモジュールは、リモート ホストで直接実行することも、プレイブックを介して実行することもできます。
この記事では、仮想マシン、ネットワーク、コンテナー サービスなどの Azure クラウド リソースをプロビジョニングできる Azure 用 Ansible モジュールの一覧を示します。 これらのモジュールは、Ansible の正式なリリースから入手するか、Microsoft が公開している次のプレイブック ロールから入手できます。

| Azure 用 Ansible モジュール                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible のロール | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Compute**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_availabilityset_facts              | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_deployment                         | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_resource                           | -            | -                           | はい          | はい          | はい                                 | 
| azure_rm_resource_facts                     | -            | -                           | はい          | はい          | はい                                 | 
| azure_rm_virtualmachine_scaleset_facts      | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_virtualmachineimage_facts          | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_resourcegroup                      | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_resourcegroup_facts                | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_virtualmachine                     | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_virtualmachine_extension           | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_virtualmachine_scaleset            | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_image                              |              | はい                         | はい          | はい          | はい                                 | 
| **ネットワーク**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_virtualnetwork_facts               | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_subnet                             | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_networkinterface                   | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_networkinterface_facts             | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_publicipaddress                    | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_publicipaddress_facts              | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_dnsrecordset                       | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_dnsrecordset_facts                 | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_dnszone                            | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_dnszone_facts                      | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_loadbalancer                       | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_loadbalancer_facts                 | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | はい                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | はい                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | はい                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | はい                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | はい                                 | 
| azure_rm_securitygroup                      | はい          | はい                         | はい          | はい          | はい                                 |
| azure_rm_route                              | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_routetable                         | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | はい          | はい                                 | 
| **Storage**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_storageaccount_facts               | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_storageblob                        | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_managed_disk                       | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_managed_disk_facts                 | はい          | はい                         | はい          | はい          | はい                                 | 
| **Containers**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | はい          | はい          | はい                                 | 
| azure_rm_aks_facts                          | -            | -                           | はい          | はい          | はい                                 | 
| azure_rm_acs                                | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_containerinstance                  | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | はい                                 | 
| azure_rm_containerregistry                  | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | はい                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | はい                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | はい                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | はい                                 | 
| **Azure Functions**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | はい          | はい                         | はい          | はい          | はい                                 | 
| azure_rm_functionapp_facts                  | はい          | はい                         | はい          | はい          | はい                                 | 
| **データベース**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_sqlserver_facts                    | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_sqldatabase                        | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | はい                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | はい                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | はい                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | はい                                 | 
| azure_rm_mysqlserver                        | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_mysqldatabase                      | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | はい                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | はい                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | はい                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | はい                                 | 
| azure_rm_postgresqlserver                   | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_postgresqldatabase                 | -            | はい                         | はい          | はい          | はい                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | はい          | はい                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | はい                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | はい                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | はい                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | はい                                 | 
| **Key Vault**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | はい                         | はい          | はい          | はい                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | はい                               |
| azure_rm_keyvaultkey                        | -            | はい                         | はい          | はい          | はい                                 |
| azure_rm_keyvaultsecret                     | -            | はい                         | はい          | はい          | はい                                 |
| **Web Apps**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | はい          | はい                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | はい          | はい                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | はい          | はい                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | はい          | はい                                 | 
| **Traffic Manager**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | はい          | はい                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | はい          | はい                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | はい          | はい                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | はい          | はい                                 | 
| **自動スケール**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | はい          | はい                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | はい          | はい                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Azure のプレイブック ロールの導入
[azure_preview_module プレイブック ロール](https://galaxy.ansible.com/Azure/azure_preview_modules/)は、最も包括的なロールであり、最新の Azure モジュールをすべて含んでいます。 更新プログラムとバグ修正は、正式な Ansible リリースよりもタイムリーに行われます。 Azure リソースのプロビジョニングのために Ansible を使用する場合は、azure_preview_module プレイブック ロールをインストールすることをお勧めします。

azure_preview_module プレイブック ロールは 3 週間ごとにリリースされます。

## <a name="next-steps"></a>次の手順
プレイブック ロール関連の詳細情報については、「[Creating Reusable Playbooks](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)」(再利用可能なプレイブックの作成) をご覧ください。 
