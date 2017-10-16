---
title: "PowerShell を使用した Azure Stack での Key Vault の管理 | Microsoft Docs"
description: "PowerShell を使用して Azure Stack の Key Vault を管理する方法を説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 41cbe1526368dd88fe98f92937c6ef2b65f24682
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-in-azure-stack-using-the-portal"></a>ポータルを使って Azure Stack の Key Vault を管理する

Azure Stack ポータルを使用して Azure Stack の Key Vault を管理できます。 この記事では、Azure Stack で Key Vault を作成および管理する手順について説明します。 

## <a name="prerequisites"></a>前提条件  

* ユーザーは、Key Vault サービスを含むプランをサブスクライブする必要があります。  
 
## <a name="create-a-key-vault"></a>Key Vault を作成します 

1. ユーザー ポータル (https://portal.local.azurestack.external) にサインインします。  

2. ダッシュボードで、**[新規] > [セキュリティ + ID] > [Key Vault]** の順にクリックします。  

    ![KV 画面](media/azure-stack-kv-manage-portal/image1.png)  

3. **[Key Vault の作成]** ブレードで、キー コンテナーに**名前**を割り当てます。 キー コンテナー名には英数字と特殊文字のハイフン (-) のみを含めることができます。キー コンテナー名を数字で始めることはできません。  

4. 使用可能なサブスクリプションの一覧から**サブスクリプション**を選択します。 Key Vault サービスを提供するすべてのサブスクリプションがドロップダウン リストに表示されます。  

5. 既存の**リソース グループ**を選択するか、新しいリソース グループを作成します。  

6. **[価格レベル]** を選択します。  
    >[!NOTE]
    > Azure Stack Development Kit のキー コンテナーでは、**Standard** SKU のみがサポートされます。

7. 既存の**アクセス ポリシー**を選択するか、新しいアクセス ポリシーを作成します。 アクセス ポリシーでは、このキー コンテナーで操作を実行する権限を、ユーザー、アプリケーション、またはセキュリティ グループに付与できます。  

8. 必要に応じて**高度なアクセス ポリシー**を選択し、デプロイでの仮想マシンへのアクセス、テンプレートのデプロイでの Resource Manager へのアクセス、ボリューム暗号化での Azure Disk Encryption へのアクセスなどの機能を有効にできます。 
  
9.  設定を構成したら、**[OK]**、**[作成]** の順にクリックします。 これにより、キー コンテナーのデプロイが開始されます。 

## <a name="manage-keys-and-secrets"></a>キーとシークレットの管理

キー コンテナーを作成したら、次の手順を実行し、キー コンテナーでキーとシークレットを作成および管理します。

## <a name="create-a-key"></a>キーの作成

1. ユーザー ポータル (https://portal.local.azurestack.external) にサインインします。  

2. ダッシュボードで **[すべてのリソース]** をクリックし、先ほど作成したキー コンテナーを選択して、**[キー]** タイルをクリックします。  

3. **[キー]** ブレードで、**[追加]** をクリックします。 

4. **[キーの作成]** ブレードで、**[オプション]** 一覧から、キーの作成に使用する方法を選択します。 新しいキーを**生成**したり、既存のキーを**アップロード**したり、**バックアップ キーを復元**したりすることができます。  

5. キーの**名前**を入力します。 キーの名前には、英数字と特殊文字のハイフン (-) のみを含めることができます。  

6. 必要に応じて、キーの **[アクティブ化する日を設定する]** と **[有効期限を設定する]** の値を構成します。  

7. **[作成]** をクリックしてデプロイを開始します。  

キーが正常に作成されたら、**[キー]** ブレードで選択してプロパティを表示したり変更したりすることができます。 プロパティ セクションには、外部アプリケーションからこのキーへのアクセスに使用する URI の**キー識別子**が含まれます。 このキーでの操作を制限するには、**[許可された操作]** で設定を構成します。

![キーの URI](media/azure-stack-kv-manage-portal/image4.png)  

## <a name="create-a-secret"></a>シークレットの作成 

1. ユーザー ポータル (https://portal.local.azurestack.external) にサインインします。  
2. ダッシュボードで **[すべてのリソース]** をクリックし、先ほど作成したキー コンテナーを選択して、**[シークレット]** タイルをクリックします。  

3. **[シークレット]** ブレードで、**[追加]** をクリックします。  

4. **[シークレットの作成]** ブレードで、**[アップロード オプション]** 一覧から、シークレットの作成に使用するオプションを選択します。 シークレットの値を入力して**手動で**シークレットを作成したり、お使いのローカル コンピューターから**証明書**をアップロードしてシークレットを作成したりすることができます。  

5. シークレットの**名前**を入力します。 シークレットの名前には、英数字と特殊文字のハイフン (-) のみを含めることができます。  

6. 必要に応じて、**コンテンツの種類**を指定し、シークレットの **[アクティブ化する日を設定する]** と **[有効期限を設定する]** の値を構成します。  

7. [作成] をクリックしてデプロイを開始します。  

シークレットが正常に作成されたら、**[シークレット]** ブレードで選択してプロパティを表示したり変更したりすることができます。 プロパティ セクションには、外部アプリケーションからこのシークレットへのアクセスに使用する URI の**シークレット識別子**が含まれます。 

![シークレットの URI](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>次のステップ
* [キー コンテナーに格納されているパスワードを取得して VM をデプロイする](azure-stack-kv-deploy-vm-with-secret.md)  
* [証明書がキー コンテナーに格納されている VM をデプロイする](azure-stack-kv-push-secret-into-vm.md)     


