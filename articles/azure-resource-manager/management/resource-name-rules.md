---
title: リソースの名前付けに関する制限事項
description: Azure リソースの名前付けに関する規則と制限事項を示します。
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 40e17e518baec40d31918d24ab5b61da741f540a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700475"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure リソースの名前付け規則と制限事項

この記事では、Azure リソースの名前付け規則と制限事項の概要について説明します。 リソースに名前を付ける方法に関する推奨事項については、「[推奨される名前付けおよびタグ付け規則](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)」を参照してください。

この記事では、リソース プロバイダーの名前空間ごとにリソースを一覧表示します。 リソース プロバイダーと Azure サービスの対応を示す一覧については、「[Azure サービスのリソース プロバイダー ](azure-services-resource-providers.md)」を参照してください。

「有効な文字」列に特に明記されていない限り、リソース名では大文字と小文字は区別されません。

以降の表では、英数字という用語は次を指しています。

* **a** から **z** (小文字)
* **A** から **Z** (大文字)
* **0** から **9** (数字)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | servers | resource group | 3 ～ 63 | 小文字と数字。<br><br>小文字で開始します。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | サービス (service) | グローバル | 1-50 | 英数字とハイフン。<br><br>先頭は文字、末尾は英数字にします。 |
> | service/apis | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/apis/issues | api | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/apis/issues/attachments | イシュー | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/apis/issues/comments | イシュー | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/apis/operations | api | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/apis/operations/tags | operation | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/apis/releases | api | 1 ～ 80 | 英数字、アンダースコア、およびハイフン。<br><br>先頭と末尾には、英数字またはアンダースコアを使用します。 |
> | service/apis/schemas | api | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/apis/tagDescriptions | api | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service / apis / tags | api | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/api-version-sets | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/authorizationServers | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/backends | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/certificates | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service / diagnostics | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/groups | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/groups/users | group | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/identityProviders | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/loggers | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/notifications | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/notifications/recipientEmails | 通知 (notification) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/openidConnectProviders | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/policies | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/products | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service / products / apis | product | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/products/groups | product | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/products/tags | product | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service / properties | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/subscriptions | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service / tags | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/templates | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |
> | service/users | サービス (service) | 1-256 | 次は使用できません:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | configurationStores | resource group | 5 ～ 50 | 英数字、アンダースコア、およびハイフン。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | locks | 割り当てのスコープ | 1-90 | 英数字、ピリオド、アンダースコア、ハイフン、およびかっこ。<br><br>末尾をピリオドにすることはできません。 |
> | policyAssignments | 割り当てのスコープ | 1-128 (表示名)<br><br>1-64 リソース名<br><br>1-24 管理グループのスコープのリソース名 | 表示名には任意の文字を含めることができます。<br><br>リソース名に `%` を含めることはできません。また、末尾をピリオドまたはスペースにすることはできません。 |
> | policyDefinitions | 定義のスコープ | 1-128 (表示名)<br><br>1-64 リソース名 | 表示名には任意の文字を含めることができます。<br><br>リソース名に `%` を含めることはできません。また、末尾をピリオドまたはスペースにすることはできません。 |
> | policySetDefinitions | 定義のスコープ | 1-128 (表示名)<br><br>1-64 リソース名<br><br>1-24 管理グループのスコープのリソース名 | 表示名には任意の文字を含めることができます。<br><br>リソース名に `%` を含めることはできません。また、末尾をピリオドまたはスペースにすることはできません。  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | automationAccounts | リソース グループとリージョン <br>(下記の「注」を参照)。 | 6-50 | 英数字とハイフン。<br><br>先頭は文字、末尾は英数字にします。 |
> | automationAccounts/certificates | Automation アカウント | 1-128 | 次は使用できません:<br> `<>*%&:\?.+/` <br><br>末尾をスペースにすることはできません。  |
> | automationAccounts/connections | Automation アカウント | 1-128 | 次は使用できません:<br> `<>*%&:\?.+/` <br><br>末尾をスペースにすることはできません。 |
> | automationAccounts/credentials | Automation アカウント | 1-128 | 次は使用できません:<br> `<>*%&:\?.+/` <br><br>末尾をスペースにすることはできません。 |
> | automationAccounts / runbooks | Automation アカウント | 1 ～ 63 | 英数字、アンダースコア、およびハイフン。<br><br>文字で開始します。  |
> | automationAccounts/schedules | Automation アカウント | 1-128 | 次は使用できません:<br> `<>*%&:\?.+/` <br><br>末尾をスペースにすることはできません。 |
> | automationAccounts/variables | Automation アカウント | 1-128 | 次は使用できません:<br> `<>*%&:\?.+/` <br><br>末尾をスペースにすることはできません。 |
> | automationAccounts/watchers | Automation アカウント | 1 ～ 63 |  英数字、アンダースコア、およびハイフン。<br><br>文字で開始します。 |
> | automationAccounts / webhooks | Automation アカウント | 1-128 | 次は使用できません:<br> `<>*%&:\?.+/` <br><br>末尾をスペースにすることはできません。 |

> [!NOTE]
> Automation アカウント名は、リージョンおよびリソース グループごとに一意です。 削除された Automation アカウントの名前は、すぐには使用できない場合があります。

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | batchAccounts | リージョン | 3 ～ 24 | 小文字と数字。 |
> | batchAccounts/applications | Batch アカウント | 1 ～ 64 | 英数字、アンダースコア、およびハイフン。 |
> | batchAccounts/certificates | Batch アカウント | 5-45 | 英数字、アンダースコア、およびハイフン。 |
> | batchAccounts / pools | Batch アカウント | 1 ～ 64 | 英数字、アンダースコア、およびハイフン。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | blockchainMembers | グローバル | 2-20 | 小文字と数字。<br><br>小文字で開始します。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | botServices | グローバル | 2 ～ 64 |  英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 |
> | botServices / channels | ボット サービス | 2 ～ 64 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 |
> | botServices/Connections | ボット サービス | 2 ～ 64 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 |
> | enterpriseChannels | resource group | 2 ～ 64 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | Redis | グローバル | 1 ～ 63 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 連続するハイフンは使用できません。 |
> | Redis/firewallRules | Redis | 1-256 | 英数字 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | profiles | resource group | 1-260 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |
> | profiles/endpoints | グローバル | 1-50 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | certificateOrders | resource group | 3-30 | 英数字。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | accounts | resource group | 2 ～ 64 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | availabilitySets | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | diskEncryptionSets | resource group | 1 ～ 80 | 英数字とアンダースコア。 |
> | disks | resource group | 1 ～ 80 | 英数字とアンダースコア。 |
> | galleries | resource group | 1 ～ 80 | 英数字とピリオド。<br><br>先頭と末尾には英数字を使用します。 |
> | galleries / applications | ギャラリー | 1 ～ 80 | 英数字、ハイフン、およびピリオド。<br><br>先頭と末尾には英数字を使用します。 |
> | galleries/applications/versions | application | 32-bit integer | 数字とピリオド。 |
> | galleries/images | ギャラリー | 1 ～ 80 | 英数字、アンダースコア、ハイフン、およびピリオド。<br><br>先頭と末尾には英数字を使用します。 |
> | galleries/images/versions | image | 32-bit integer | 数字とピリオド。 |
> | images | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | スナップショット | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | virtualMachines | resource group | 1-15 (Windows)<br>1-64 (Linux)<br><br>下記の「注意」を参照。 | スペースまたは次の文字は使用できません:<br> `\/"'[]:|<>+=;,?*@&_`<br><br>Windows VM の場合、ピリオドを含めたり、末尾をハイフンにしたりすることはできません。<br><br>Linux VM の場合、末尾をピリオドまたはハイフンにすることはできません。 |
> | virtualMachineScaleSets | resource group | 1-15 (Windows)<br>1-64 (Linux)<br><br>下記の「注意」を参照。 | スペースまたは次の文字は使用できません:<br> `\/"'[]:|<>+=;,?*@&`<br><br>アンダースコアで開始することはできません。 末尾をピリオドまたはハイフンにすることはできません。 |

> [!NOTE]
> Azure 仮想マシンには、リソース名とホスト名の 2 つの異なる名前があります。 ポータルで仮想マシンを作成すると、両方の名前に同じ値が使用されます。 前の表に記載されている制限事項は、ホスト名に適用されます。 実際のリソース名の最大文字数は 64 文字です。

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | containerGroups | resource group | 1 ～ 63 | 小文字、数字、およびハイフン。<br><br>先頭または末尾をハイフンにすることはできません。 連続するハイフンは使用できません。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | registries | グローバル | 5 ～ 50 | 英数字。 |
> | registries / buildTasks | 使用) | 5 ～ 50 | 英数字。 |
> | registries/buildTasks/steps | ビルド タスク | 5 ～ 50 | 英数字。 |
> | registries/replications | 使用) | 5 ～ 50 | 英数字。 |
> | registries / scopeMaps | 使用) | 5 ～ 50 | 英数字、ハイフン、およびアンダースコア。 |
> | registries/tasks | 使用) | 5 ～ 50 | 英数字、ハイフン、およびアンダースコア。 |
> | registries / tokens | 使用) | 5 ～ 50 | 英数字、ハイフン、およびアンダースコア。 |
> | registries/webhooks | 使用) | 5 ～ 50 | 英数字。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | managedClusters | resource group | 1 ～ 63 | 英数字、アンダースコア、およびハイフン。<br><br>先頭と末尾には英数字を使用します。 |
> | openShiftManagedClusters | resource group | 1-30 | 英数字。 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | hubs | resource group | 1 ～ 64 | 英数字。<br><br>文字で開始します。  |
> | hubs/authorizationPolicies | ハブ | 1-50 | 英数字、アンダースコア、およびピリオド。<br><br>先頭と末尾には英数字を使用します。 |
> | hubs/connectors | ハブ | 1-128 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/connectors/mappings | コネクタ | 1-128 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/interactions | ハブ | 1-128 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/kpi | ハブ | 1-512 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/links | ハブ | 1-512 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/predictions | ハブ | 1-512 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/profiles | ハブ | 1-128 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/relationshipLinks | ハブ | 1-512 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/relationships | ハブ | 1-512 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/roleAssignments | ハブ | 1-128 | 英数字とアンダースコア。<br><br>文字で開始します。 |
> | hubs/views | ハブ | 1-512 | 英数字とアンダースコア。<br><br>文字で開始します。 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | associations | resource group | 1-180 | 次は使用できません:<br>`%&\\?/`<br><br>末尾をピリオドまたはスペースにすることはできません。 |
> | resourceProviders | resource group | 3-64 | 次は使用できません:<br>`%&\\?/`<br><br>末尾をピリオドまたはスペースにすることはできません。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | jobs | resource group | 3 ～ 24 | 英数字、ハイフン、アンダースコア、およびピリオド。 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | workspaces | resource group | 3-30 | 英数字、アンダースコア、およびハイフン |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | factories | グローバル | 3 ～ 63 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |
> | factories/dataflows | factory | 1-260 | 次は使用できません:<br>`<>*#.%&:\\+?/`<br><br>英数字で開始します。 |
> | factories/datasets | factory | 1-260 | 次は使用できません:<br>`<>*#.%&:\\+?/`<br><br>英数字で開始します。 |
> | factories / integrationRuntimes | factory | 3 ～ 63 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |
> | factories/linkedservices | factory | 1-260 | 次は使用できません:<br>`<>*#.%&:\\+?/`<br><br>英数字で開始します。 |
> | factories / pipelines | factory | 1-260 | 次は使用できません:<br>`<>*#.%&:\\+?/`<br><br>英数字で開始します。 |
> | factories / triggers | factory | 1-260 | 次は使用できません:<br>`<>*#.%&:\\+?/`<br><br>英数字で開始します。 |
> | factories/triggers/rerunTriggers | トリガー (trigger) | 1-260 | 次は使用できません:<br>`<>*#.%&:\\+?/`<br><br>英数字で開始します。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | accounts | グローバル | 3 ～ 24 | 小文字と数字。 |
> | accounts/computePolicies | account | 3-60 | 英数字、ハイフン、およびアンダースコア。 |
> | accounts / dataLakeStoreAccounts | account | 3 ～ 24 | 小文字と数字。 |
> | accounts / firewallRules | account | 3 ～ 50 | 英数字、ハイフン、およびアンダースコア。 |
> | accounts / storageAccounts | account | 3-60 | 英数字、ハイフン、およびアンダースコア。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | accounts | グローバル | 3 ～ 24 | 小文字と数字。 |
> | accounts / firewallRules | account | 3 ～ 50 | 英数字、ハイフン、およびアンダースコア。 |
> | accounts/virtualNetworkRules | account | 3 ～ 50 | 英数字、ハイフン、およびアンダースコア。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | services | resource group | 2-62 | 英数字、ハイフン、ピリオド、およびアンダースコア。<br><br>英数字で開始します。 |
> | services/projects | サービス (service) | 2-57 | 英数字、ハイフン、ピリオド、およびアンダースコア。<br><br>英数字で開始します。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | servers | グローバル | 3 ～ 63 | 小文字、ハイフン、および数字。<br><br>先頭または末尾をハイフンにすることはできません。 |
> | servers/databases | servers | 1 ～ 63 | 英数字とハイフン。 |
> | servers / firewallRules | servers | 1-128 | 英数字、ハイフン、およびアンダースコア。 |
> | servers / virtualNetworkRules | servers | 1-128 | 英数字とハイフン。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | servers | グローバル | 3 ～ 63 | 小文字、ハイフン、および数字。<br><br>先頭または末尾をハイフンにすることはできません。 |
> | servers/databases | servers | 1 ～ 63 | 英数字とハイフン。 |
> | servers / firewallRules | servers | 1-128 | 英数字、ハイフン、およびアンダースコア。 |
> | servers / virtualNetworkRules | servers | 1-128 | 英数字とハイフン。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | servers | グローバル | 3 ～ 63 | 小文字、ハイフン、および数字。<br><br>先頭または末尾をハイフンにすることはできません。 |
> | servers/databases | servers | 1 ～ 63 | 英数字とハイフン。 |
> | servers / firewallRules | servers | 1-128 | 英数字、ハイフン、およびアンダースコア。 |
> | servers / virtualNetworkRules | servers | 1-128 | 英数字とハイフン。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | IotHubs | グローバル | 3 ～ 50 | 英数字とハイフン。<br><br>末尾をハイフンにすることはできません。 |
> | IotHubs/certificates | IoT ハブ | 1 ～ 64 | 英数字、ハイフン、ピリオド、およびアンダースコア。 |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | 英数字、ハイフン、ピリオド、およびアンダースコア。 |
> | provisioningServices | resource group | 3-64 | 英数字とハイフン。<br><br>末尾には英数文字を使用します。 |
> | provisioningServices/certificates | provisioningServices | 1 ～ 64 | 英数字、ハイフン、ピリオド、およびアンダースコア。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | labs | resource group | 1-50 | 英数字、アンダースコア、およびハイフン。 |
> | labs/customimages | ラボ | 1 ～ 80 | 英数字、アンダースコア、ハイフン、およびかっこ。 |
> | labs/formulas | ラボ | 1 ～ 80 | 英数字、アンダースコア、ハイフン、およびかっこ。 |
> | labs/virtualmachines | ラボ | 1-15 (Windows)<br>1-64 (Linux) | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 すべて数字にすることはできません。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | databaseAccounts | グローバル | 3 ～ 44 | 小文字、数字、およびハイフン。<br><br>先頭には小文字または数字を使用します。 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | domains | resource group | 3 ～ 50 | 英数字とハイフン。 |
> | domains / topics | domain | 3 ～ 50 | 英数字とハイフン。 |
> | eventSubscriptions | resource group | 3-64 | 英数字とハイフン。 |
> | topics | resource group | 3 ～ 50 | 英数字とハイフン。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | clusters | resource group | 6-50 | 英数字とハイフン。<br><br>文字で開始します。 文字または数字で終了します。 |
> | namespaces | グローバル | 6-50 | 英数字とハイフン。<br><br>文字で開始します。 文字または数字で終了します。 |
> | namespaces/AuthorizationRules | namespace | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には、文字または数字を使用します。 |
> | namespaces/disasterRecoveryConfigs | namespace | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には、文字または数字を使用します。 |
> | namespaces / eventhubs | namespace | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には、文字または数字を使用します。 |
> | namespaces/eventhubs/authorizationRules | イベント ハブ | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には、文字または数字を使用します。 |
> | namespaces / eventhubs / consumergroups | イベント ハブ | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には、文字または数字を使用します。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | clusters | グローバル | 3-59 | 英数字とハイフン<br><br>先頭と末尾には、文字または数字を使用します。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | jobs | resource group | 2 ～ 64 | 英数字とハイフン。<br><br>文字で開始します。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | actionGroups | resource group | 1-260 | 次は使用できません:<br>`/&%\?` <br><br>末尾をスペースまたはピリオドにすることはできません。  |
> | components | resource group | 1-260 | 次は使用できません:<br>`%&\?/` <br><br>末尾をスペースまたはピリオドにすることはできません。  |
> | scheduledQueryRules | resource group | 1-260 | 次は使用できません:<br>`*<>%{}&:\\?/#` <br><br>末尾をスペースまたはピリオドにすることはできません。  |
> | metricAlerts | resource group | 1-260 | 次は使用できません:<br>`*#&+:<>?@%{}\/` <br><br>末尾をスペースまたはピリオドにすることはできません。  |
> | activityLogAlerts | resource group | 1-260 | 次は使用できません:<br>`<>*%{}&:\\?+/#` <br><br>末尾をスペースまたはピリオドにすることはできません。  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | IoTApps | グローバル | 2-63 | 小文字、数字、およびハイフン。<br><br>先頭には小文字または数字を使用します。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | vaults | グローバル | 3 ～ 24 | 英数字とハイフン。<br><br>文字で開始します。 文字または数字で終了します。 連続するハイフンを含めることはできません。 |
> | vaults / secrets | コンテナー | 1-127 | 英数字とハイフン。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | clusters | グローバル | 4-22 | 小文字と数字。<br><br>文字で開始します。 |
> | /clusters/databases | cluster | 1-260 | 英数字、ハイフン、スペース、およびピリオド。 |
> | /clusters/databases/dataConnections | database | 1-40 | 英数字、ハイフン、スペース、およびピリオド。 |
> | /clusters/databases/eventhubconnections | database | 1-40 | 英数字、ハイフン、スペース、およびピリオド。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | integrationAccounts | resource group | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationAccounts/assemblies | 統合アカウント | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationAccounts/batchConfigurations | 統合アカウント | 1-20 | 英数字。 |
> | integrationAccounts/certificates | 統合アカウント | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationAccounts/maps | 統合アカウント | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationAccounts/partners | 統合アカウント | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationAccounts/rosettanetprocessconfigurations | 統合アカウント | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationAccounts/schemas | 統合アカウント | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationAccounts/sessions | 統合アカウント | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |
> | integrationServiceEnvironments | resource group | 1 ～ 80 | 英数字、ハイフン、ピリオド、およびアンダースコア。 |
> | integrationServiceEnvironments / managedApis | 統合サービス環境 | 1 ～ 80 | 英数字、ハイフン、ピリオド、およびアンダースコア。 |
> | workflows | resource group | 1 ～ 80 | 英数字、ハイフン、アンダースコア、ピリオド、およびかっこ。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | commitmentPlans | resource group | 1-260 | 次は使用できません:<br>`<>*%&:?+/\\`<br><br>末尾をスペースにすることは使用できません。 |
> | webServices | resource group | 1-260 | 次は使用できません:<br>`<>*%&:?+/\\`<br><br>末尾をスペースにすることは使用できません。 |
> | workspaces | resource group | 1-260 | 次は使用できません:<br>`<>*%&:?+/\\`<br><br>末尾をスペースにすることは使用できません。 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | workspaces | resource group | 3-33 | 英数字とハイフン。 |
> | workspaces / computes | ワークスペース | 2-16 | 英数字とハイフン。 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | resource group | 3-128 | 英数字、ハイフン、およびアンダースコア<br><br>先頭には文字または数字を使用します。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | accounts | resource group | 1-98 (リソース グループ名とアカウント名) | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | mediaservices | resource group | 3 ～ 24 | 小文字と数字。 |
> | mediaservices / liveEvents | メディア サービス | 1-32 | 英数字とハイフン。<br><br>英数字で開始します。 |
> | mediaservices / liveEvents / liveOutputs | ライブ イベント | 1-256 | 英数字とハイフン。<br><br>英数字で開始します。 |
> | mediaservices / streamingEndpoints | メディア サービス | 1 から 24 | 英数字とハイフン。<br><br>英数字で開始します。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | applicationGateways | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | applicationSecurityGroups | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | azureFirewalls | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | bastionHosts | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | connections | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | dnsZones | resource group | 1-63 文字<br><br>2 から 34 のラベル<br><br>各ラベルは、ピリオドで区切られた一連の文字です。 たとえば、**contoso.com** には 2 つのラベルがあります。 | 各ラベルには、英数字、アンダースコア、およびハイフンを含めることができます。<br><br>各ラベルはピリオドで区切られます。 |
> | expressRouteCircuits | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | firewallPolicies | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | firewallPolicies/ruleGroups | ファイアウォール ポリシー | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | frontDoors | グローバル | 5-64 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |
> | frontdoorWebApplicationFirewallPolicies | resource group | 1-128 | 英数字。<br><br>文字で開始します。 |
> | loadBalancers | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | loadBalancers/inboundNatRules | ロード バランサー | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | localNetworkGateways | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | networkInterfaces | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | networkSecurityGroups | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | networkSecurityGroups/securityRules | ネットワーク セキュリティ グループ | 1 ～ 80 |  英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | networkWatchers | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | privateDnsZones | resource group | 1-63 文字<br><br>2 から 34 のラベル<br><br>各ラベルは、ピリオドで区切られた一連の文字です。 たとえば、**contoso.com** には 2 つのラベルがあります。 | 各ラベルには、英数字、アンダースコア、およびハイフンを含めることができます。<br><br>各ラベルはピリオドで区切られます。 |
> | privateDnsZones / virtualNetworkLinks | プライベート DNS ゾーン | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | publicIPAddresses | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | publicIPPrefixes | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | routeFilters | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | routeFilters/routeFilterRules | ルート フィルター | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | routeTables | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | routeTables/routes | ルート テーブル | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | serviceEndpointPolicies | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | trafficmanagerprofiles | グローバル | 1 ～ 63 | 英数字、ハイフン、およびピリオド。<br><br>先頭と末尾には英数字を使用します。 |
> | virtualNetworkGateways | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | virtualNetworks | resource group | 2 ～ 64 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | virtualnetworks/subnets | 仮想ネットワーク | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | virtualNetworks / virtualNetworkPeerings | 仮想ネットワーク | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | virtualWans | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | vpnGateways | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | vpnGateways/vpnConnections | VPN Gateway | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |
> | vpnSites | resource group | 1 ～ 80 | 英数字、アンダースコア、ピリオド、およびハイフン。<br><br>英数字で開始します。 英数字またはアンダースコアで終了します。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | namespaces | グローバル | 6-50 | 英数字とハイフン<br><br>文字で開始します。 末尾には英数字を使用します。 |
> | namespaces/AuthorizationRules | namespace | 1-256 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>英数字で開始します。 |
> | namespaces / notificationHubs | namespace | 1-260 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>英数字で開始します。 |
> | namespaces/notificationHubs/AuthorizationRules | 通知ハブ | 1-256 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>英数字で開始します。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | clusters | resource group | 4-63 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |
> | workspaces | グローバル | 4-63 | 英数字とハイフン。<br><br>先頭と末尾には英数字を使用します。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | solutions | ワークスペース | 該当なし | Microsoft によって作成されたソリューションの場合、名前は次のパターンになっている必要があります:<br>`SolutionType(WorkspaceName)`<br><br>サード パーティによって作成されたソリューションの場合、名前は次のパターンになっている必要があります:<br>`SolutionType[WorkspaceName]`<br><br>有効な名前の例を次に示します:<br>`AntiMalware(contoso-IT)`<br><br>ソリューションの種類では、大文字と小文字が区別されます。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | dashboards | resource group | 3-160 | 英数字とハイフン。<br><br>制限のある文字を使用するには、**hidden-title** という名前のタグを、使用するダッシュボード名とともに追加します。 ポータルでは、ダッシュボードを表示するときにその名前が表示されます。 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3 ～ 63 | 英数字とハイフン。<br><br>先頭をハイフンにすることはできません。 連続するハイフンを使用することはできません。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | capacities | region | 3 ～ 63 | 小文字または数字。<br><br>小文字で開始します。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | vaults | resource group | 2-50 | 英数字とハイフン。<br><br>文字で開始します。 |
> | vaults/backupPolicies | コンテナー | 3-150 | 英数字とハイフン。<br><br>文字で開始します。 末尾をハイフンにすることはできません。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | namespaces | グローバル | 6-50 | 英数字とハイフン。<br><br>文字で始めます。 文字または数字で終了します。 |
> | namespaces/AuthorizationRules | namespace | 1-50 |  英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces/HybridConnections | namespace | 1-260 | 英数字、ピリオド、ハイフン、アンダースコア、およびスラッシュ。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces/HybridConnections/authorizationRules | ハイブリッド接続 | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces/WcfRelays | namespace | 1-260 | 英数字、ピリオド、ハイフン、アンダースコア、およびスラッシュ。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces/WcfRelays/authorizationRules | WCF Relay | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | deployments | resource group | 1 ～ 64 | 英数字、アンダースコア、かっこ、ハイフン、およびピリオド。 |
> | resourcegroups | subscription | 1-90 | [正規表現ドキュメント](/rest/api/resources/resourcegroups/createorupdate)の記載と一致する英数字、アンダースコア、かっこ、ハイフン、ピリオド、および Unicode 文字。<br><br>末尾をピリオドにすることはできません。 |
> | tagNames | resource | 1-512 | 次は使用できません:<br>`<>%&\?/` |
> | tagNames / tagValues | タグ名 | 1-256 | すべての文字。 |
> | templateSpecs | resource group | 1-90 | 英数字、アンダースコア、かっこ、ハイフン、およびピリオド。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | namespaces | グローバル | 6-50 | 英数字とハイフン。<br><br>文字で始めます。 文字または数字で終了します。<br><br>詳細については、[名前空間の作成](/rest/api/servicebus/create-namespace)に関するページを参照してください。 |
> | namespaces/AuthorizationRules | namespace | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces/disasterRecoveryConfigs | グローバル | 6-50 | 英数字とハイフン。<br><br>文字で開始します。 末尾には英数文字を使用します。 |
> | namespaces/migrationConfigurations | namespace |  | 常に **$default** にする必要があります。 |
> | namespaces / queues | namespace | 1-260 | 英数字、ピリオド、ハイフン、アンダースコア、およびスラッシュ。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces/queues/ authorizationRules | queue | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces / topics | namespace | 1-260 | 英数字、ピリオド、ハイフン、アンダースコア、およびスラッシュ。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces/topics/authorizationRules | topic | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces / topics / subscriptions | topic | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |
> | namespaces / topics / subscriptions / rules | subscription | 1-50 | 英数字、ピリオド、ハイフン、およびアンダースコア。<br><br>先頭と末尾には英数字を使用します。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 小文字、数字、およびハイフン。<br><br>小文字で開始します。 末尾には小文字または数字を使用します。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | signalR | グローバル | 3 ～ 63 | 英数字とハイフン。<br><br>文字で開始します。 文字または数字で終了します。  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | managedInstances | グローバル | 1 ～ 63 | 小文字、数字、およびハイフン。<br><br>先頭または末尾をハイフンにすることはできません。 <br><br> 特殊文字 (`@` など) を含めることはできません。 |
> | servers | グローバル | 1 ～ 63 | 小文字、数字、およびハイフン。<br><br>先頭または末尾をハイフンにすることはできません。 |
> | servers / administrators | server |  | `ActiveDirectory`である必要があります。 |
> | servers/databases | server | 1-128 | 次は使用できません:<br>`<>*%&:\/?`<br><br>末尾をピリオドまたはスペースにすることはできません。 |
> | servers/databases/syncGroups | database | 1-150 | 英数字、ハイフン、およびアンダースコア。 |
> | servers/elasticPools | server | 1-128 | 次は使用できません:<br>`<>*%&:\/?`<br><br>末尾をピリオドまたはスペースにすることはできません。 |
> | servers/failoverGroups | グローバル | 1 ～ 63 | 小文字、数字、およびハイフン。<br><br>先頭または末尾をハイフンにすることはできません。 |
> | servers / firewallRules | server | 1-128 | 次は使用できません:<br>`<>*%&:;\/?`<br><br>末尾をピリオドにすることはできません。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | storageAccounts | グローバル | 3 ～ 24 | 小文字と数字。 |
> | storageAccounts / blobServices | ストレージ アカウント |  | `default`である必要があります。 |
> | storageAccounts/blobServices/containers | ストレージ アカウント | 3 ～ 63 | 小文字、数字、およびハイフン。<br><br>先頭には小文字または数字を使用します。 連続するハイフンを使用することはできません。 |
> | storageAccounts / fileServices | ストレージ アカウント |  | `default`である必要があります。 |
> | storageAccounts/fileServices/shares | ストレージ アカウント | 3 ～ 63 | 小文字、数字、およびハイフン。<br><br>先頭または末尾をハイフンにすることはできません。 連続するハイフンを使用することはできません。 |
> | storageAccounts/managementPolicies | ストレージ アカウント |  | `default`である必要があります。 |
> | blob (blob) | container | 1 ～ 1,024 | 任意の URL 文字。大文字と小文字が区別されます。 |
> | queue | ストレージ アカウント | 3 ～ 63 | 小文字、数字、およびハイフン。<br><br>先頭または末尾をハイフンにすることはできません。 連続するハイフンを使用することはできません。 |
> | table | ストレージ アカウント | 3 ～ 63 | 英数字。<br><br>文字で開始します。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | storageSyncServices | resource group | 1-260 | 英数字、スペース、ピリオド、ハイフン、およびアンダースコア。<br><br>末尾をピリオドまたはスペースにすることはできません。 |
> | storageSyncServices / syncGroups | ストレージ同期サービス | 1-260 | 英数字、スペース、ピリオド、ハイフン、およびアンダースコア。<br><br>末尾をピリオドまたはスペースにすることはできません。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | managers | resource group | 2-50 | 英数字とハイフン。<br><br>文字で開始します。 末尾には英数字を使用します。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | streamingjobs | resource group | 3 ～ 63 | 英数字、ハイフン、およびアンダースコア。 |
> | streamingjobs/functions | ストリーミング ジョブ | 3 ～ 63 | 英数字、ハイフン、およびアンダースコア。 |
> | streamingjobs/inputs | ストリーミング ジョブ | 3 ～ 63 | 英数字、ハイフン、およびアンダースコア。 |
> | streamingjobs/outputs | ストリーミング ジョブ | 3 ～ 63 | 英数字、ハイフン、およびアンダースコア。 |
> | streamingjobs/transformations | ストリーミング ジョブ | 3 ～ 63 | 英数字、ハイフン、およびアンダースコア。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | 環境 | resource group | 1-90 | 次は使用できません:<br>`'<>%&:\?/#` |
> | environments / accessPolicies | 環境 | 1-90 | 次は使用できません:<br> `'<>%&:\?/#` |
> | environments/eventSources | 環境 | 1-90 | 次は使用できません:<br>`'<>%&:\?/#` |
> | environments / referenceDataSets | 環境 | 3 ～ 63 | 英数字 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entity | Scope | 長さ | 有効な文字 |
> | --- | --- | --- | --- |
> | certificates | resource group | 1-260 | 次は使用できません:<br>`/` <br><br>末尾をスペースまたはピリオドにすることはできません。  | 
> | serverfarms | resource group | 1-40 | 英数字とハイフン。 |
> | sites | グローバル | 2 から 60 | 英数字とハイフンを含みます。<br><br>先頭または末尾をハイフンにすることはできません。 |
> | sites/slots | site | 2 ～ 59 | 英数字とハイフン。 |

> [!NOTE]
> Azure Functions の名前付けルールと制限事項は、Microsoft.Web/sites と同じです。

## <a name="next-steps"></a>次のステップ

リソースの名前付け方法に関する推奨事項については、「[Ready: 推奨される名前付けおよびタグ付け規則](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)」を参照してください。
