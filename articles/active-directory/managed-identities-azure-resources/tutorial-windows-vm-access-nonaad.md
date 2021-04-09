---
title: チュートリアル`:` マネージド ID を使用して Azure Key Vault にアクセスする - Windows - Azure AD
description: Windows VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスするプロセスについて説明するチュートリアルです。
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
ms.openlocfilehash: 0f29c9daba80ffc57b96cf5bd82690dea9ac6429
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093742"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) でシステム割り当てマネージド ID を使用して [Azure Key Vault](../../key-vault/general/overview.md) にアクセスする方法について説明します。 ブートストラップとして機能する Key Vault により、クライアント アプリケーションは、Azure Active Directory (AD) で保護されていないリソースにシークレットを使用してアクセスできます。 マネージド サービス ID は Azure によって自動的に管理され、認証情報をコードに含めなくても、Azure AD 認証をサポートするサービスに対して認証を行うことができます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault に格納されているシークレットへ VM のアクセスを許可する
> * VM ID を使用してアクセス トークンを取得して、Key Vault からシークレットを取得する 

## <a name="prerequisites"></a>前提条件

- マネージド ID の知識。 Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウント。[無料アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
- 必要なリソース作成とロール管理の手順を実行するための、適切なスコープ (サブスクリプションまたはリソース グループ) の "所有者" アクセス許可。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- システム割り当てマネージド ID が有効になっている Windows 仮想マシンも必要です。
  - このチュートリアル用に仮想マシンを作成する必要がある場合は、[システム割り当てマネージド ID を有効にした仮想マシンの作成](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)に関する記事に従ってください。

## <a name="create-a-key-vault"></a>Key Vault の作成  

このセクションでは、Key Vault に格納されているシークレットへのアクセスを VM に許可する方法を説明します。 Azure リソースのマネージド ID を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。ただし、すべての Azure サービスが Azure AD 認証をサポートしているわけではありません。 Azure リソースのマネージド ID をこれらのサービスと共に使用するには、Azure Key Vault にサービス資格情報を保存し、VM のマネージド ID を使用して Key Vault にアクセスして、資格情報を取得します。

まず、Key Vault を作成し、VM のシステム割り当てマネージド ID に Key Vault へのアクセスを許可する必要があります。

1. Azure [portal](https://portal.azure.com/) を開きます。
1. 左側のナビゲーション バーの上部で、 **[リソースの作成]** を選択します。  
1. **[Marketplace を検索]** ボックスに「**Key Vault**」と入力し、**Enter** キーを押します。  
1. 結果から **[Key Vault]** を選択します。
1. **[作成]**
1. 新しい Key Vault の **名前** を入力します。

    ![Key Vault の作成画面](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

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

   ![シークレットの作成](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>アクセス権の付与

仮想マシンで使用されるマネージド ID には、Key Vault に格納するシークレットを読み取るためのアクセス権を付与する必要があります。

1. 新しく作成した Key Vault に移動します。
1. 左側のメニューで、 **[アクセス ポリシー]** を選択します。
1. **[アクセス ポリシーの追加]** を選択します

   ![Key Vault のアクセス ポリシーの作成画面](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. **[アクセス ポリシーの追加]** セクションで、 **[テンプレートからの構成 (省略可能)]** のプルダウン メニューから **[シークレットの管理]** を選択します。
1. **[プリンシパルの選択]** を選択し、検索フィールドに以前に作成した VM の名前を入力します。  結果一覧で VM を選択し、 **[選択]** を選択します。
1. **[追加]** を選択します。
1. **[保存]** を選択します。


## <a name="access-data"></a>データにアクセスする  

このセクションでは、VM ID を使用してアクセス トークンを取得し、それを使用して Key Vault からシークレットを取得する方法を説明します。 PowerShell 4.3.1 以上がインストールされていない場合、[最新バージョンをダウンロードしてインストールする](/powershell/azure/)必要があります。

最初に、VM のシステム割り当てマネージド ID を使用して、Key Vault に対して認証するためのアクセス トークンを取得します。
 
1. ポータルで **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、 **[概要]** の **[接続]** をクリックします。
2. **Windows VM** を作成したときに追加した **ユーザー名** と **パスワード** を入力します。  
3. これで、仮想マシンを使用する **リモート デスクトップ接続** が作成されました。リモート セッションで PowerShell を開きます。  
4. PowerShell では、テナント上で Web 要求を呼び出し、VM の特定のポートでローカル ホストのトークンを取得します。  

PowerShell は次のものを要求します。

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

次のような応答が表示されます。

![トークン応答を含む要求](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

次に、アクセス トークンを応答から抽出します。  

```powershell
   $KeyVaultToken = $Response.access_token
```

最後に、PowerShell の Invoke-WebRequest コマンドを使用して、Key Vault で以前に作成したシークレットを取得し、Authorization ヘッダーにアクセス トークンを渡します。  Key Vault の [**概要**] ページの [**要点**] セクションにある Key Vault の URL が必要です。  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

応答は次のようになります。 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Key Vault からシークレットを取得した後は、名前とパスワードを必要とするサービスへの認証にそのシークレットを使用できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースをクリーンアップするには、[Azure portal](https://portal.azure.com) にアクセスし、 **[リソース グループ]** を選択し、このチュートリアルで作成されたリソース グループを探して選択してから (`mi-test` など)、 **[リソース グループの削除]** コマンドを使用することもできます。

[PowerShell または CLI](../../azure-resource-manager/management/delete-resource-group.md) を使用してこれを行うこともできます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Windows VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする方法について説明しました。  Azure Key Vault の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)