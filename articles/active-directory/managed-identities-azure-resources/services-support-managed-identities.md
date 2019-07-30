---
title: Azure リソースのマネージド ID をサポートする Azure サービス
description: Azure リソースのマネージド ID と Azure AD 認証をサポートするサービスの一覧
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6869602b7d4aff6d779d189f0b6444dde2055d4
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261900"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートするサービス

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報が含まれていなくても、Azure AD 認証をサポートする任意のサービスに対して認証を行うことができます。 Azure リソースのマネージド ID と Azure AD 認証の Azure 全体への統合が進行中です。 更新プログラムがないかどうか、頻繁に確認してください。

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージドサービス ID (MSI) の新しい名前です。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートする Azure サービス

次の Azure サービスが、Azure リソースのマネージド ID をサポートしています。

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | プレビュー | プレビュー | プレビュー | 
| ユーザー割り当て済み | プレビュー | プレビュー | プレビュー | プレビュー |

Azure Virtual Machines のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セット

|マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | プレビュー | プレビュー | プレビュー |
| ユーザー割り当て済み | プレビュー | プレビュー | プレビュー | プレビュー |

Azure Virtual Machine Scale Sets のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| ユーザー割り当て済み | プレビュー | 使用できません。 | 使用できません。 | 使用できません。 |

Azure App Service のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager テンプレート](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | プレビュー | 使用できません。 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | プレビュー | 使用できません。 | 使用できません。 | 使用できません。 |

[Azure Blueprints](../../governance/blueprints/overview.md) でマネージド ID を使用するには、次の一覧を参照してください。

- [Azure portal - ブループリント割り当て](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - ブループリント割り当て](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

マネージド ID の種類 |すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| ユーザー割り当て済み | プレビュー | 使用できません。 | 使用できません。 | 使用できません。 |

Azure Functions のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager テンプレート](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | プレビュー | プレビュー | 使用できません。 | プレビュー |
| ユーザー割り当て済み | 使用できません。 | 使用できません。 | 使用できません。 | 使用できません。 |

Azure Logic Apps のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager テンプレート](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | 使用できません。 | 使用できません。 | 使用できません。 | 使用できません。 |

Azure Data Factory V2 のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用可能 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | 使用できません。 | 使用できません。 | 使用できません。 | 使用できません。 |

Azure API Management のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Resource Manager テンプレート](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | Linux:プレビュー<br>Windows:使用できません。 | 使用できません。 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | Linux:プレビュー<br>Windows:使用できません。 | 使用できません。 | 使用できません。 | 使用できません。 |

Azure Container Instances のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager テンプレート](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry タスク

マネージド ID の種類 | すべて一般公開<br>グローバル Azure リージョン | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | プレビュー | 使用できません。 | 使用できません。 | 使用できません。 |

Azure Container Registry タスクのために (それが提供されているリージョンで) マネージド ID を構成するには、次の一覧を参照してください。

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 認証をサポートしている Azure サービス

次のサービスは、Azure AD 認証をサポートしており、Azure リソースのマネージド ID を使用するクライアント サービスでテストされています。

### <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager へのアクセスを構成するには、次の一覧を参照してください。

- [Azure portal でアクセスを割り当てる](howto-assign-access-portal.md)
- [PowerShell でアクセスを割り当てる](howto-assign-access-powershell.md)
- [Azure CLI でアクセスを割り当てる](howto-assign-access-CLI.md)
- [Azure Resource Manager テンプレートでアクセスを割り当てる](../../role-based-access-control/role-assignments-template.md)

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| 使用可能 |
| Azure Government | `https://management.usgovcloudapi.net/` | 使用可能 |
| Azure Germany | `https://management.microsoftazure.de/` | 使用可能 |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | 使用可能 |

### <a name="azure-key-vault"></a>Azure Key Vault

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| 使用可能 |
| Azure Government | `https://vault.usgovcloudapi.net` | 使用可能 |
| Azure Germany |  `https://vault.microsoftazure.de` | 使用可能 |
| Azure China 21Vianet | `https://vault.azure.cn` | 使用可能 |

### <a name="azure-data-lake"></a>Azure Data Lake 

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | 使用可能 |
| Azure Government |  | 利用不可 |
| Azure Germany |   | 利用不可 |
| Azure China 21Vianet |  | 利用不可 |

### <a name="azure-sql"></a>Azure SQL 

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | 使用可能 |
| Azure Government | `https://database.usgovcloudapi.net/` | 使用可能 |
| Azure Germany | `https://database.cloudapi.de/` | 使用可能 |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | 使用可能 |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | プレビュー |
| Azure Government |  | 利用不可 |
| Azure Germany |   | 利用不可 |
| Azure China 21Vianet |  | 利用不可 |

### <a name="azure-service-bus"></a>Azure Service Bus

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | プレビュー |
| Azure Government |  | 利用不可 |
| Azure Germany |   | 利用不可 |
| Azure China 21Vianet |  | 利用不可 |

### <a name="azure-storage-blobs-and-queues"></a>Azure Storage の BLOB とキュー

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | 使用可能 |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | 使用可能 |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | 使用可能 |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | 使用可能 |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| クラウド | Resource ID | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | 使用可能 |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | 使用可能 |
| Azure Germany | `https://*.asazure.cloudapi.de` | 使用可能 |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | 使用可能 |
