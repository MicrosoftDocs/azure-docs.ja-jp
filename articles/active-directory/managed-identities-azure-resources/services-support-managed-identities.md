---
title: マネージド ID をサポートする Azure サービス - Azure AD
description: Azure リソースのマネージド ID と Azure AD 認証をサポートするサービスの一覧
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 058873df989c444ebe06fc20a2f8a40fd2d3c594
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594548"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートするサービス

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報が含まれていなくても、Azure AD 認証をサポートする任意のサービスに対して認証を行うことができます。 Azure リソースのマネージド ID と Azure AD 認証の Azure 全体への統合が進行中です。 更新プログラムがないかどうか、頻繁に確認してください。

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージドサービス ID (MSI) の新しい名前です。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートする Azure サービス

次の Azure サービスが、Azure リソースのマネージド ID をサポートしています。


### <a name="azure-api-management"></a>Azure API Management

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | プレビュー | プレビュー | 使用不可 | プレビュー |

Azure API Management のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Resource Manager テンプレート](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 利用不可 | ![利用可能][check] |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check]  | 利用不可  | ![利用可能][check] |

Azure App Configuration のために (提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check]  | ![利用可能][check]  | ![利用可能][check] |

Azure App Service のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager テンプレート](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | プレビュー | 使用不可 | 使用できません | 使用できません |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

Azure Arc 対応 Kubernetes では、現在、[システムによって割り当てられた ID がサポートされています](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。 マネージド サービス ID 証明書は、すべての Azure Arc 対応 Kubernetes エージェントによって Azure との通信のために使用されています。

### <a name="azure-arc-enabled-servers"></a>Azure Arc 対応サーバー

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用できません | 使用できません |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用できません |

すべての Azure Arc 対応サーバーには、システムによって割り当てられた ID があります。 Azure Arc 対応サーバーでは、システムによって割り当てられた ID を無効化または変更することはできません。 Azure Arc 対応サーバーでマネージド ID を使用する方法の詳細については、次のリソースを参照してください。

- [Arc 対応サーバーでの Azure リソースに対する認証](../../azure-arc/servers/managed-identity-authentication.md)
- [Arc 対応サーバーでのマネージド ID の使用](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | プレビュー | 使用不可 | 使用できません | 使用できません |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

サブスクリプションを新しいテナントに移動した場合は、次のドキュメントを参照してマネージド ID を再構成します。
* [切断された Automanage アカウントの修復](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprint

|マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用できません | 使用できません |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用できません | 使用不可 |

[Azure Blueprints](../../governance/blueprints/overview.md) でマネージド ID を使用するには、次の一覧を参照してください。

- [Azure portal - ブループリント割り当て](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - ブループリント割り当て](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |


### <a name="azure-communication-services"></a>Azure Communication Services

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | 使用できません | 使用できません | 使用できません |
| ユーザー割り当て済み | ![利用可能][check] | 使用できません | 使用できません | 使用不可 |


### <a name="azure-container-instances"></a>Azure Container Instances

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | Linux: プレビュー<br>Windows: 使用不可 | 使用できません | 使用できません | 使用できません |
| ユーザー割り当て済み | Linux: プレビュー<br>Windows: 使用不可 | 使用できません | 使用できません | 使用不可 |

Azure Container Instances のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager テンプレート](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry タスク

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | 使用できません | 使用できません | 使用できません |
| ユーザー割り当て済み | プレビュー | 使用不可 | 使用できません | 使用できません |

Azure Container Registry タスクのために (それが提供されているリージョンで) マネージド ID を構成するには、次の一覧を参照してください。

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

Azure Data Factory V2 のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | 使用できません | 使用できません | 使用できません |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

Azure Digital Twins のために (それが提供されているリージョンで) マネージド ID を構成するには、次の一覧を参照してください。

- [Azure portal](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

マネージド ID の種類 |すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | プレビュー | プレビュー | 使用不可 | プレビュー |
| ユーザー割り当て済み | 使用不可 | 使用できません  | 使用できません  | 使用不可 |

### <a name="azure-firewall-policy"></a>Azure ファイアウォール ポリシー

マネージド ID の種類 |すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | 使用できません | 使用できません | 使用できません | 使用できません |
| ユーザー割り当て済み | プレビュー | 使用不可  | 使用できません  | 使用不可 |

### <a name="azure-functions"></a>Azure Functions

マネージド ID の種類 |すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check]  | ![利用可能][check]  | ![利用可能][check]  |

Azure Functions のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager テンプレート](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

Azure IoT Hub のために (それが提供されているリージョンで) マネージド ID を構成するには、次の一覧を参照してください。

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | Azure Import Export サービスが利用可能なリージョンで利用可能 | プレビュー | 利用可能 | 利用可能 |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用できません | 使用できません |
| ユーザー割り当て済み | プレビュー | 使用不可 | 使用できません | 使用不可 |


詳細については、「[Azure Kubernetes Service でマネージド ID を使用する](../../aks/use-managed-identity.md)」を参照してください。

### <a name="azure-log-analytics-cluster"></a>Azure Log Analytics クラスター

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |

詳細については、[Azure Monitor で ID がどのように機能するか](../../azure-monitor/logs/customer-managed-keys.md)に関するページを参照してください

### <a name="azure-logic-apps"></a>Azure Logic Apps

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check] | 使用不可 | ![利用可能][check] |


Azure Logic Apps のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager テンプレート](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | プレビュー | 利用不可 | 使用不可 | 使用できません |
| ユーザー割り当て済み | プレビュー | 使用不可 | 使用できません | 使用不可 |

詳細については、「[Azure Machine Learning でマネージド ID を使用する](../../machine-learning/how-to-use-managed-identities.md)」を参照してください。

### <a name="azure-policy"></a>Azure Policy

|マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

Azure Policy のために (それが提供されているリージョンで) マネージド ID を構成するには、次の一覧を参照してください。

- [Azure Portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az-policy-assignment-create)
- [Azure リソース マネージャーのテンプレート](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

すべてのリージョンで [Service Fabric アプリケーションのマネージド ID](../../service-fabric/concepts-managed-identity.md) を使用できます。

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | 利用不可 | 利用不可 | 利用不可 |
| ユーザー割り当て済み | ![利用可能][check] | 利用不可 | 利用不可 |利用不可 |

すべてのリージョンの Azure Service Fabric アプリケーションのためにマネージド ID を構成するには、次の一覧を参照してください。

- [Azure Resource Manager テンプレート](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | 利用不可 | 利用不可 | ![利用可能][check] |
| ユーザー割り当て済み | 利用不可 | 利用不可 | 利用不可 | 利用不可 |


詳細については、「[Azure Spring Cloud アプリケーションのシステム割り当てマネージド ID を有効にする方法](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)」を参照してください。

### <a name="azure-stack-edge"></a>Azure Stack Edge

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | Azure Stack Edge サービスが利用可能なリージョンで利用可能 | 使用不可 | 使用できません | 使用できません |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

### <a name="azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セット

|マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] |

Azure Virtual Machine Scale Sets のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] |
| ユーザー割り当て済み | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] | ![利用可能][check] |

Azure Virtual Machines のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | 利用不可 | 利用不可 | 利用不可 | 利用不可 |
| ユーザー割り当て済み | [サポートされているリージョンで利用可能](../../virtual-machines/image-builder-overview.md#regions) | 利用不可 | 利用不可 | 利用不可 |

Azure VM Image Builder のために (それが提供されているリージョンで) マネージド ID を構成する方法については、[Image Builder の概要](../../virtual-machines/image-builder-overview.md#permissions)に関するページを参照してください。
### <a name="azure-signalr-service"></a>Azure SignalR Service

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | プレビュー | プレビュー | 使用不可 | プレビュー |
| ユーザー割り当て済み | プレビュー | プレビュー | 使用不可 | プレビュー |

Azure SignalR Service のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Resource Manager テンプレート](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| システム割り当て済み | Azure Resource Mover サービスが提供されているリージョンで使用可能 | 使用不可 | 使用できません | 使用できません |
| ユーザー割り当て済み | 使用不可 | 使用できません | 使用できません | 使用不可 |

Azure Resource Mover を使用するには、次のドキュメントを参照してください。

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 認証をサポートしている Azure サービス

次のサービスは、Azure AD 認証をサポートしており、Azure リソースのマネージド ID を使用するクライアント サービスでテストされています。

### <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager へのアクセスを構成するには、次の一覧を参照してください。

- [Azure portal でアクセスを割り当てる](howto-assign-access-portal.md)
- [PowerShell でアクセスを割り当てる](howto-assign-access-powershell.md)
- [Azure CLI でアクセスを割り当てる](howto-assign-access-CLI.md)
- [Azure Resource Manager テンプレートでアクセスを割り当てる](../../role-based-access-control/role-assignments-template.md)

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![利用可能][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![利用可能][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![利用可能][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![利用可能][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![利用可能][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![利用可能][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![利用可能][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![利用可能][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![利用可能][check] |
| Azure Government |  | 利用不可 |
| Azure Germany |   | 利用不可 |
| Azure China 21Vianet |  | 利用不可 |

### <a name="azure-sql"></a>Azure SQL

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![利用可能][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![利用可能][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![利用可能][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![利用可能][check] |

### <a name="azure-data-explorer"></a>Azure Data Explorer

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![利用可能][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![利用可能][check] |
| Azure Germany | `https://<account>.<region>.kusto.cloudapi.de` | ![利用可能][check] |
| Azure China 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![利用可能][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![利用可能][check] |
| Azure Government |  | 利用不可 |
| Azure Germany |   | 利用不可 |
| Azure China 21Vianet |  | 利用不可 |

### <a name="azure-service-bus"></a>Azure Service Bus

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![利用可能][check] |
| Azure Government |  | ![利用可能][check] |
| Azure Germany |   | 利用不可 |
| Azure China 21Vianet |  | 利用不可 |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage の BLOB とキュー

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![利用可能][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![利用可能][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![利用可能][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![利用可能][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| クラウド | Resource ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![利用可能][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![利用可能][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![利用可能][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![利用可能][check] |

> [!Note]
> Microsoft Power BI は[マネージド ID もサポートします](../../stream-analytics/powerbi-output-managed-identity.md)。


[check]: media/services-support-managed-identities/check.png "利用可能"
