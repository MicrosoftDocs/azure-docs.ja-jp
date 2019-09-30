---
title: Linux VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする
description: Linux VM のシステム割り当てマネージド ID を使用して Azure Resource Manager にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26d434069870fbfa52687a3abc7913fd121687a1
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000067"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>チュートリアル:Linux VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする方法について説明します。 ブートストラップとして機能する Key Vault により、クライアント アプリケーションは、Azure Active Directory (AD) で保護されていないリソースにシークレットを使用してアクセスできます。 Azure リソースのマネージド ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 

学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault に格納されているシークレットへ VM のアクセスを許可する 
> * VM の ID を使用してアクセス トークンを取得して、Key Vault からシークレットを取得する 
 
## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Key Vault に格納されているシークレットへ VM のアクセスを許可する  

Azure リソースのマネージド サービス ID を使用すると、Azure Active Directory 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。 ただし、すべての Azure サービスが Azure AD 認証をサポートしているわけではありません。 Azure リソースのマネージド ID をこれらのサービスと共に使用するには、Azure Key Vault にサービス資格情報を保存し、Azure リソースのマネージド ID を使用して Key Vault にアクセスして、資格情報を取得します。 

まず、Key Vault を作成し、VM のシステム割り当てマネージド ID に Key Vault へのアクセスを許可する必要があります。   

1. 左側のナビゲーション バーの上部で、 **[リソースの作成]**  >  **[セキュリティ + ID]**  >  **[Key Vault]** を選択します。  
2. 新しい Key Vault の**名前**を入力します。 
3. 以前に作成した VM と同じサブスクリプションおよびリソース グループで、Key Vault を見つけます。 
4. **[アクセス ポリシー]** を選択して **[新規追加]** ボタンをクリックします。 
5. テンプレートの [構成] で、 **[シークレットの管理]** を選択します。 
6. **[プリンシパルの選択]** を選択し、検索フィールドに以前に作成した VM の名前を入力します。  結果一覧で VM を選択し、 **[選択]** をクリックします。 
7. **[OK]** をクリックして新しいアクセス ポリシーの追加を終了し、 **[OK]** をクリックしてアクセス ポリシーの選択を完了します。 
8. **[作成]** をクリックして Key Vault の作成を完了します。 

    ![イメージ テキスト](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

次に、Key Vault にシークレットを追加して、後で VM で実行されているコードを使用して、シークレットを取得できるようにします。 

1. **[すべてのリソース]** を選択し、作成した Key Vault を検索して選択します。 
2. **[シークレット]** を選択し、 **[追加]** をクリックします。 
3. **[アップロード オプション]** から **[手動]** を選択します。 
4. シークレットの名前と値を指定します。  値は任意のものを指定できます。 
5. アクティブ化した日付と有効期限の日付をクリアのままにし、 **[有効]** を **[はい]** のままにします。 
6. **[作成]** をクリックしてシークレットを作成します。 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>VM の ID を使用してアクセス トークンを取得して、Key Vault からシークレットを取得する  

これらの手順を完了するには、SSH クライアントが必要です。  Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。
 
1. ポータルで Linux VM に移動し、 **[概要]** の **[接続]** をクリックします。 
2. 任意の SSH クライアントを使用して、VM に**接続**します。 
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
    
    このアクセス トークンを使用して Azure Key Vault に認証することができます。  次の CURL 要求は、CURL と Key Vault REST API を使用して Key Vault からシークレットを読み取る方法を示しています。  Key Vault の **[概要]** ページの **[要点]** セクションにある Key Vault の URL が必要です。  前の呼び出しで取得したアクセス トークンも必要になります。 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    応答は次のようになります。 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault からシークレットを取得した後は、名前とパスワードを必要とするサービスへの認証にそのシークレットを使用できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Linux VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする方法について説明しました。  Azure Key Vault の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-overview)




