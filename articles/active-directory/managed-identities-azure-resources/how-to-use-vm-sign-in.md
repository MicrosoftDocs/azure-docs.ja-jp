---
title: Azure VM 上でマネージド ID を使用してサインインする - Azure ADV
description: Azure リソース サービス プリンシパルの Azure VM マネージド ID を使用して、スクリプト クライアントのサインインとリソースへのアクセスを行うための手順を追った説明と例。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 61e83bd27c9434c4222e0161e3b643b183d1aa84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99090962"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Azure VM 上の Azure リソースのマネージド ID を使用してサインインする方法 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
この記事では、Azure リソース サービス プリンシパルのマネージド ID を使用してサインインするための PowerShell と CLI スクリプトの例と、エラー処理などの重要なトピックに関するガイダンスを提供します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

この記事に含まれている Azure PowerShell または Azure CLI の例を使用するには、最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) または [Azure CLI](/cli/azure/install-azure-cli) をインストールする必要があります。 

> [!IMPORTANT]
> - この記事のすべてのサンプル スクリプトは、Azure リソースのマネージド ID が有効になっている VM 上でコマンドライン クライアントが実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure ポータルで VM への "接続" 機能を使用します。 VM で Azure リソースのマネージド ID を有効にする方法の詳細については、「[Azure Portal を使用して VM 上に Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」、または関連する記事 (PowerShell、CLI、テンプレート、または Azure SDK の使用) のいずれかを参照してください。 
> - リソースへのアクセス中のエラーを回避するには、VM での Azure Resource Manager の操作を許可する適切なスコープ (VM 以上) の "読み取り" アクセス以上のアクセス権が VM のマネージド ID に付与されている必要があります。 [Azure portal を使用したリソースにアクセスするための Azure リソースのマネージド ID の割り当て](howto-assign-access-portal.md)に関する記事を参照してください。

## <a name="overview"></a>概要

Azure リソースのマネージド ID は、[サービス プリンシパル オブジェクト](../develop/developer-glossary.md#service-principal-object)を提供します。これは、VM 上で [Azure リソースのマネージド ID を有効にしたときに作成](overview.md)されます。 サービス プリンシパルに Azure のリソースへのアクセス権を付与し、スクリプトまたはコマンドライン クライアントがサインインおよびリソースにアクセスするための ID として使用できます。 従来では、独自の ID でセキュリティで保護されたリソースにアクセスするには、スクリプト クライアントは以下を実行する必要がありました。  

   - Azure AD で機密/Web クライアント アプリケーションとして登録し合意されている
   - (多くの場合、スクリプトに埋め込まれている) アプリの資格情報を使用して、そのサービス プリンシパルを使ってサインインする。

Azure リソースのマネージド ID では、スクリプト クライアントはこれを行う必要がなくなります。理由は、Azure リソース サービス プリンシパルのマネージド ID でサインインできるためです。 

## <a name="azure-cli"></a>Azure CLI

次のスクリプトは、以下を行う方法を示しています。

1. VM の Azure リソース サービス プリンシパルのマネージド ID で Azure AD にサインインします。  
2. Azure Resource Manager を呼び出して VM のサービス プリンシパルの ID を取得します。 CLI がトークンの取得や使用を自動的に管理します。 `<VM-NAME>` を必ずお使いの仮想マシン名に置き換えてください。  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

次のスクリプトは、以下を行う方法を示しています。

1. VM の Azure リソース サービス プリンシパルのマネージド ID で Azure AD にサインインします。  
2. VM に関する情報を取得するには、Azure Resource Manager コマンドレットを呼び出します。 PowerShell が自動的にトークンの使用を管理します。  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure サービスのリソース ID

Azure AD をサポートするリソースで、Azure リソースのマネージド ID をテスト済みのリソースとそれぞれのリソース ID の一覧については、「[Azure AD 認証をサポートしている Azure サービス](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」を参照してください。

## <a name="error-handling-guidance"></a>エラー処理に関するガイダンス 

次のような応答が返ってきた場合は、VM の Azure リソースのマネージド ID が正しく構成されていない可能性があります。

- PowerShell:*Invoke-WebRequest: リモート サーバーに接続できません*
- CLI:*MSI: `http://localhost:50342/oauth2/token` からトークンを取得できませんでした。'HTTPConnectionPool(host='localhost', port=50342) エラーが発生しました* 

これらのいずれかのエラーが発生した場合は、[Azure portal](https://portal.azure.com) の Azure VM に戻り、 **[ID]** ページにアクセスし **[システム割り当て済み]** が [はい] に設定されていることを確認します。

## <a name="next-steps"></a>次のステップ

- Azure VM 上で Azure リソースのマネージド ID を有効にするには、「[Configure managed identities for Azure resources on an Azure VM using PowerShell](qs-configure-powershell-windows-vm.md)」(PowerShell を使用して VM 上に Azure リソースのマネージド ID を構成する)、または「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md)」を参照してください。