---
title: Azure VM 上の Azure リソースのマネージド ID を使用してサインインする方法
description: Azure リソース サービス プリンシパルの Azure VM マネージド ID を使用して、スクリプト クライアントのサインインとリソースへのアクセスを行うための手順を追った説明と例。
services: active-directory
documentationcenter: ''
author: markusvi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43aa0859fa67cc6b2f5c5974f072e7b6d4b29527
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112970"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Azure VM 上の Azure リソースのマネージド ID を使用してサインインする方法 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
この記事では、Azure リソース サービス プリンシパルのマネージド ID を使用してサインインするための PowerShell と CLI スクリプトの例と、エラー処理などの重要なトピックに関するガイダンスを提供します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

この記事に含まれている Azure PowerShell または Azure CLI の例を使用するには、最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) または [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) をインストールする必要があります。 

> [!IMPORTANT]
> - この記事のすべてのサンプル スクリプトは、Azure リソースのマネージド ID が有効になっている VM 上でコマンドライン クライアントが実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure ポータルで VM への "接続" 機能を使用します。 VM で Azure リソースのマネージド ID を有効にする方法の詳細については、「[Azure Portal を使用して VM 上に Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」、または関連する記事 (PowerShell、CLI、テンプレート、または Azure SDK の使用) のいずれかを参照してください。 
> - リソースへのアクセス中のエラーを回避するには、VM での Azure Resource Manager の操作を許可する適切なスコープ (VM 以上) の "読み取り" アクセス以上のアクセス権が VM のマネージド ID に付与されている必要があります。 [Azure portal を使用したリソースにアクセスするための Azure リソースのマネージド ID の割り当て](howto-assign-access-portal.md)に関する記事を参照してください。

## <a name="overview"></a>概要

Azure リソースのマネージド ID は、[サービス プリンシパル オブジェクト](../develop/developer-glossary.md#service-principal-object)を提供します。これは、VM 上で [Azure リソースのマネージド ID を有効にしたときに作成](overview.md#how-does-it-work)されます。 サービス プリンシパルに Azure のリソースへのアクセス権を付与し、スクリプトまたはコマンドライン クライアントがサインインおよびリソースにアクセスするための ID として使用できます。 従来では、独自の ID でセキュリティで保護されたリソースにアクセスするには、スクリプト クライアントは以下を実行する必要がありました。  

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

これらのエラーが発生した場合、[Azure ポータル](https://portal.azure.com)で Azure VM に戻り、

- **[ID]** ページに移動して、 させ **[システム割り当て]** が [はい] に設定されていることを確認します。
- **[拡張機能]** ページに移動して、Azure リソースのマネージド ID 拡張機能 **(2019 年 1 月に非推奨となる予定)** が正常に展開されていることを確認します。

いずれかが正しくない場合は、Azure リソースのマネージド ID をリソース上にもう一度展開するか、展開エラーのトラブルシューティングを行う必要があります。 VM の構成についてサポートが必要な場合は、「[Azure Portal を使用して VM 上に Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure VM 上で Azure リソースのマネージド ID を有効にするには、「[Configure managed identities for Azure resources on an Azure VM using PowerShell](qs-configure-powershell-windows-vm.md)」(PowerShell を使用して VM 上に Azure リソースのマネージド ID を構成する)、または「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md)」を参照してください。






