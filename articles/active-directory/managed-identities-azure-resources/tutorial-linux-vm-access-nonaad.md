---
title: チュートリアル`:` マネージド ID を使用して Azure Key Vault にアクセスする - Linux - Azure AD
description: Linux VM のシステム割り当てマネージド ID を使用して Azure Resource Manager にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb31d6e25ce1c1ff5c3e4dbabb4fa53da0bd2ef3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093936"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>チュートリアル:Linux VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) でシステム割り当てマネージド ID を使用して [Azure Key Vault](../../key-vault/general/overview.md) にアクセスする方法について説明します。 ブートストラップとして機能する Key Vault により、クライアント アプリケーションは、Azure Active Directory (AD) で保護されていないリソースにシークレットを使用してアクセスできます。 マネージド サービス ID は Azure によって自動的に管理され、認証情報をコードに含めなくても、Azure AD 認証をサポートするサービスに対して認証を行うことができます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault に格納されているシークレットへ VM のアクセスを許可する 
> * VM の ID を使用してアクセス トークンを取得して、Key Vault からシークレットを取得する 
 
## <a name="prerequisites"></a>前提条件

- マネージド ID の基礎知識。 Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウント。[無料アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
- 必要なリソース作成とロール管理の手順を実行するための、適切なスコープ (サブスクリプションまたはリソース グループ) の "所有者" アクセス許可。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- システム割り当てマネージド ID が有効になっている Linux 仮想マシンも必要です。
  - このチュートリアル用に仮想マシンを作成する必要がある場合は、[Azure portal での Linux 仮想マシンの作成](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)に関する記事に従ってください。


## <a name="create-a-key-vault"></a>Key Vault の作成  

このセクションでは、Key Vault に格納されているシークレットへのアクセスを VM に許可する方法を説明します。 Azure リソースのマネージド ID を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。ただし、すべての Azure サービスが Azure AD 認証をサポートしているわけではありません。 Azure リソースのマネージド ID をこれらのサービスと共に使用するには、Azure Key Vault にサービス資格情報を保存し、VM のマネージド ID を使用して Key Vault にアクセスして、資格情報を取得します。

まず、Key Vault を作成し、VM のシステム割り当てマネージド ID に Key Vault へのアクセスを許可する必要があります。

1. Azure [portal](https://portal.azure.com/) を開きます。
1. 左側のナビゲーション バーの上部で、 **[リソースの作成]** を選択します。  
1. **[Marketplace を検索]** ボックスに「**Key Vault**」と入力し、**Enter** キーを押します。  
1. 結果から **[Key Vault]** を選択します。
1. **[作成]**
1. 新しい Key Vault の **名前** を入力します。

    ![Key Vault の作成画面](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. このチュートリアルで使用する仮想マシンを作成したサブスクリプションとリソース グループを必ず選択して、必要なすべての情報を入力します。
1. **[確認および作成]** を選択します。
1. **[作成]**

### <a name="create-a-secret"></a>シークレットの作成

次に、Key Vault にシークレットを追加し、VM で実行されているコードを使用して後で取得できるようにします。 このチュートリアルでは、PowerShell を使用していますが、この仮想マシンで実行されるすべてのコードに同じ概念が適用されます。

1. 新しく作成した Key Vault に移動します。
1. **[シークレット]** を選択し、**[追加]** をクリックします。
1. **[生成/インポート]** を選択します。
1. **[シークレットの作成]** 画面の **[アップロード オプション]** で、 **[手動]** を選択したままにします。
1. シークレットの名前と値を指定します。  値は任意のものを指定できます。 
1. アクティブ化した日付と有効期限の日付をクリアのままにし、**[有効]** を **[はい]** のままにします。 
1. **[作成]** をクリックしてシークレットを作成します。

   ![シークレットの作成](./media/tutorial-linux-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>アクセス権の付与

仮想マシンで使用されるマネージド ID には、Key Vault に格納するシークレットを読み取るためのアクセス権を付与する必要があります。

1. 新しく作成した Key Vault に移動します。
1. 左側のメニューで、 **[アクセス ポリシー]** を選択します。
1. **[アクセス ポリシーの追加]** を選択します

   ![Key Vault のアクセス ポリシーの作成画面](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. **[アクセス ポリシーの追加]** セクションで、 **[テンプレートからの構成 (省略可能)]** のプルダウン メニューから **[シークレットの管理]** を選択します。
1. **[プリンシパルの選択]** を選択し、検索フィールドに以前に作成した VM の名前を入力します。  結果一覧で VM を選択し、 **[選択]** を選択します。
1. **[追加]** を選択します。
1. **[保存]** を選択します。

## <a name="access-data"></a>データにアクセスする

これらの手順を完了するには、SSH クライアントが必要です。  Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。
 
1. ポータルで Linux VM に移動し、 **[概要]** の **[接続]** をクリックします。 
2. 任意の SSH クライアントを使用して、VM に **接続** します。 
3. ターミナル ウィンドウで、CURL を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure Key Vault のアクセス トークンを取得するよう要求します。  
 
    アクセス トークンの CURL 要求を次に示します。  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    応答には、リソース マネージャーへのアクセスに必要なアクセス トークンが含まれています。 
    
    応答:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    このアクセス トークンを使用して Azure Key Vault に認証することができます。  次の CURL 要求は、CURL と Key Vault REST API を使用して Key Vault からシークレットを読み取る方法を示しています。  Key Vault の [**概要**] ページの [**要点**] セクションにある Key Vault の URL が必要です。  前の呼び出しで取得したアクセス トークンも必要になります。 
        
    ```bash
    curl 'https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    応答は次のようになります。 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault からシークレットを取得した後は、名前とパスワードを必要とするサービスへの認証にそのシークレットを使用できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースをクリーンアップするには、[Azure portal](https://portal.azure.com) にアクセスし、 **[リソース グループ]** を選択し、このチュートリアルで作成されたリソース グループを探して選択してから (`mi-test` など)、 **[リソース グループの削除]** コマンドを使用することもできます。

[PowerShell または CLI](../../azure-resource-manager/management/delete-resource-group.md) を使用してこれを行うこともできます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Linux VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする方法について説明しました。  Azure Key Vault の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)