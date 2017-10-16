---
title: "Azure Stack のサービス プリンシパルを作成する | Microsoft Docs"
description: "Azure Resource Manager でロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しいサービス プリンシパルを作成する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: heathl17
manager: byronr
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 5787b25fb1dd7331e561798152678ed187e24d54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="provide-applications-access-to-azure-stack"></a>Azure Stack へのアクセスをアプリケーションに提供する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack 内の Azure Resource Manager を通じてリソースをデプロイまたは構成するためのアクセスがアプリケーションに必要な場合は、アプリケーションの資格情報であるサービス プリンシパルを作成します。  サービス プリンシパルには必要な権限のみを委任できます。  

たとえば、Azure Resource Manager を使用して Azure リソースのインベントリを管理する構成管理ツールがあります。  このシナリオでは、サービス プリンシパルを作成し、これに閲覧者のロールを付与して、構成管理ツールのアクセスを読み取り専用に制限します。 

サービス プリンシパルは、お客様自身の資格情報でアプリを実行するよりも推奨されますが、その理由は次のとおりです。

* お客様自身のアカウントのアクセス許可とは異なるアクセス許可を、サービス プリンシパルに割り当てることができます。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。
* お客様の責任が変わっても、アプリの資格情報を変更する必要はありません。
* 無人インストール用スクリプトを実行するときに、証明書を使用して認証を自動化できます。  

## <a name="getting-started"></a>使用の開始

Azure Stack のデプロイ方法に応じて、サービス プリンシパルを作成して開始します。  このドキュメントでは、[Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) および [Active Directory フェデレーション サービス (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs) のサービス プリンシパルを作成する方法を説明します。  サービス プリンシパルを作成したら、AD FS と Azure Active Directory の両方に共通する一連の手順を実行して、ロールに[アクセス許可を委任](azure-stack-create-service-principals.md#assign-role-to-service-principal)します。     

## <a name="create-service-principal-for-azure-ad"></a>Azure AD のサービス プリンシパルを作成する

Azure AD を ID ストアとして使用して Azure Stack をデプロイした場合は、Azure での手順と同様の方法でサービス プリンシパルを作成できます。  このセクションでは、それらの手順をポータルで行う方法について説明します。  始める前に、[Azure AD で必要なアクセス許可](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)があることを確認してください。

### <a name="create-service-principal"></a>サービス プリンシパルの作成
このセクションでは、アプリケーションが表示される Azure AD にアプリケーション (サービス プリンシパル) を作成します。

1. [Azure Portal](https://portal.azure.com) で Azure アカウントにログインします。
2. **[Azure Active Directory]** > **[アプリの登録]** > **[追加]** の順に選択します。   
3. アプリケーションの名前と URL を指定します。 作成するアプリケーションの種類として、**[Web アプリ/API]** または **[ネイティブ]** を選択します。 値を設定したら、**[作成]** をクリックします。

アプリケーションのサービス プリンシパルが作成されました。

### <a name="get-credentials"></a>資格情報の取得
プログラムによってログインするときは、アプリケーションの ID と認証キーを使用します。 これらの値を取得するには、次の手順に従います。

1. Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

2. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。 「[サンプル アプリケーション](#sample-applications)」の各アプリケーションでは、この値をクライアント ID と呼んでいます。

     ![[クライアント ID]](./media/azure-stack-create-service-principal/image12.png)
3. 認証キーを生成するには、**[キー]** を選択します。

4. キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。

キーを保存すると、キーの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。 キー値は、アプリケーションとしてサインインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

![保存されたキー](./media/azure-stack-create-service-principal/image15.png)


完了したら、引き続き[アプリケーションにロールを割り当て](azure-stack-create-service-principals.md#assign-role-to-service-principal)ます。

## <a name="create-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルを作成する
AD FS を使用して Azure Stack をデプロイした場合は、PowerShell を使ってサービス プリンシパルを作成し、アクセスのロールを割り当てて、その ID を使用して PowerShell からサインインできます。

### <a name="before-you-begin"></a>開始する前に

[Azure Stack を操作するために必要なツールをローカル コンピューターにダウンロードします。](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Identity PowerShell モジュールのインポート
ツールをダウンロードしたら、ダウンロードしたフォルダーに移動し、次のコマンドを使用して Identity PowerShell モジュールをインポートします。

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

モジュールのインポート時に、"AzureStack.Connect.psm1 はデジタル署名されていません。 このスクリプトはシステムで実行されません" という内容のエラーが表示されることがあります。 この問題を解決するには、管理者特権の PowerShell セッションで次のコマンドを使用して、スクリプトの実行を許可する実行ポリシーを設定します。

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>サービス プリンシパルを作成する
*DisplayName* パラメーターが更新されていることを確認し、次のコマンドを実行してサービス プリンシパルを作成できます。
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
```
### <a name="assign-a-role"></a>ロールの割り当て
サービス プリンシパルを作成したら、[ロールを割り当てる](azure-stack-create-service-principals.md#assign-role-to-service-principal)必要があります。

### <a name="sign-in-through-powershell"></a>PowerShell を使ったサインイン
ロールを割り当てたら、次のコマンドでサービス プリンシパルを使用して Azure Stack にサインインできます。

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>サービス プリンシパルにロールを割り当てる
サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1. Azure Stack ポータルで、アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。 リソース グループまたはリソースを選択することもできます。

2. アプリケーションを割り当てる特定のサブスクリプション (リソース グループまたはリソース) を選択します。

     ![割り当てのためのサブスクリプションの選択](./media/azure-stack-create-service-principal/image16.png)

3. **[アクセス制御 (IAM)]** を選択します。

     ![アクセスの選択](./media/azure-stack-create-service-principal/image17.png)

4. **[追加]**を選択します。

5. アプリケーションに割り当てるロールを選択します。

6. アプリケーションを検索して選択します。

7. **[OK]** をクリックして、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

サービス プリンシパルを作成してロールを割り当てたら、アプリケーション内でこれを使用して、Azure Stack リソースにアクセスできます。  

## <a name="next-steps"></a>次のステップ

[AD FS のユーザーの追加](azure-stack-add-users-adfs.md)
[ユーザーのアクセス許可の管理](azure-stack-manage-permissions.md)