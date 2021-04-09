---
title: クイックスタート`:` マネージド ID を使用して Azure Resource Manager にアクセスする - Azure AD
description: Linux VM のシステム割り当てマネージド ID を使用して Azure Resource Manager にアクセスするプロセスについて説明するクイック スタートです。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653159c2e40d3375a422f0da14274f57130de1fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93359682"
---
# <a name="use-a-linux-vm-system-assigned-managed-identity-to-access-azure-resource-manager"></a>Linux VM のシステム割り当てマネージド ID を使用して Azure Resource Manager にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このクイック スタートでは、Linux 仮想マシン (VM) のシステム割り当て ID を使用して Azure Resource Manager API にアクセスする方法について説明します。 Azure リソースのマネージド ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Resource Manager で VM にリソース グループへのアクセスを許可する 
> * VM ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す 

## <a name="prerequisites"></a>前提条件

- マネージド ID の知識。 Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウント。[無料アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
- システム割り当てマネージド ID が有効になっている Linux 仮想マシンも必要です。
  - このチュートリアル用に仮想マシンを作成する必要がある場合は、[Azure portal での Linux 仮想マシンの作成](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)に関する記事に従ってください。

## <a name="grant-access"></a>アクセス権の付与

Azure リソースのマネージド ID を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。 Azure Resource Manager API は、Azure AD の認証をサポートします。 最初に、Azure Resource Manager のリソース (ここでは、VM が含まれているリソース グループ) へのアクセスをこの VM の ID に許可する必要があります。  

1. **リソース グループ** のタブに移動します。
2. 仮想マシン用に使用した特定の **リソース グループ** を選択します。
3. 左側のパネルの **[アクセス制御 (IAM)]** に移動します。
4. VM 用に新しいロールの割り当てを **追加** します。 **[閲覧者]** として **[ロール]** を選択します。
5. 次のドロップダウンで、 **[アクセスの割り当て先]** として **[仮想マシン]** リソースを選択します。
6. 次に、 **[サブスクリプション]** ドロップダウンに適切なサブスクリプションが表示されていることを確認します。 **[リソース グループ]** で **[すべてのリソース グループ]** を選択します。
7. 最後に、 **[選択]** の一覧で、使用する Linux 仮想マシンを選択し、 **[保存]** をクリックします。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-resource-manager"></a>VM のシステム割り当てマネージド ID を使用してアクセス トークンを取得し、それを使用して Resource Manager を呼び出す

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. ポータルで Linux VM に移動し、 **[概要]** の **[接続]** をクリックします。  
2. 任意の SSH クライアントを使用して、VM に **接続** します。 
3. ターミナル ウィンドウで、`curl` を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure Resource Manager のアクセス トークンを取得するよう要求します。  
 
    アクセス トークンの `curl` 要求を次に示します。  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -H Metadata:true   
    ```
    
    > [!NOTE]
    > "resource"パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。    Resource Manager のリソース ID の場合は、URI の末尾にスラッシュを含める必要があります。 
    
    応答には、Azure Resource Manager へのアクセスに必要なアクセス トークンが含まれています。 
    
    応答:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    このアクセス トークンを使用して、Azure Resource Manager にアクセスし、たとえば、この VM アクセスを許可した Resource Group の詳細を確認することができます。 \<SUBSCRIPTION ID\>、 \<RESOURCE GROUP\>、および \<ACCESS TOKEN\> の値を先ほど作成したものと置き換えます。 
    
    > [!NOTE]
    > URL では大文字小文字が区別されるため、リソース グループの命名時に以前使用したものと同じ大文字小文字が使用されていること、および "resourceGroup" で大文字の "G" が使用されていることを確認してください。  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    応答は、特定の Resource Group の情報とともに返されます。 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、システム割り当て ID を使用して Azure Resource Manager API にアクセスする方法について説明しました。  Azure Resource Manager の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
>[Azure PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する](how-to-manage-ua-identity-powershell.md)