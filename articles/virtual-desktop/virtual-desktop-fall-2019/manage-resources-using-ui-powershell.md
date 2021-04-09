---
title: サービス プリンシパルを使用して、Windows Virtual Desktop (クラシック) の管理ツールをデプロイする - Azure
description: ここでは、PowerShell を使用して Windows Virtual Desktop (クラシック) の管理ツールをデプロイする方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: d7219751d584eb458cded9f4e30cccb1439dfa1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89069019"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>PowerShell を使用して Windows Virtual Desktop (クラシック) 管理ツールをデプロイする

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。

この記事では、PowerShell を使用して管理ツールをデプロイする方法について説明します。

## <a name="important-considerations"></a>重要な考慮事項

Azure Active Directory (Azure AD) テナントの各サブスクリプションでは、管理ツールをそれぞれ個別にデプロイする必要があります。 このツールは、Azure AD の B2B シナリオをサポートしていません。

この管理ツールはサンプルです。 Microsoft は、セキュリティと品質の重要な更新プログラムを提供します。 [ソース コードは GitHub で入手できます](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。 あなたがカスタマー/パートナーのどちらであっても、ビジネスニーズを満たすようにツールをカスタマイズすることをお勧めします。

この管理ツールは、次のブラウザーに対応しています。

- Google Chrome 68 以降
- Microsoft Edge 40.15063 以降
- Mozilla Firefox 52.0 以降
- Safari 10 以降 (macOS のみ)

## <a name="what-you-need-to-deploy-the-management-tool"></a>管理ツールのデプロイに必要なもの

管理ツールをデプロイする前に、アプリの登録を作成して管理 UI をデプロイする Azure Active Directory (Azure AD) ユーザーが必要です。 このユーザーには、次の要件があります。

- ご使用の Azure サブスクリプション内にリソースを作成するためのアクセス許可を持っている
- Azure AD アプリケーションを作成するためのアクセス許可を持っている 次のステップに従い、あなたのユーザーが[「必要なアクセス許可」](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)の指示に従って必要なアクセス許可を保持しているかどうかを確認します。

管理ツールをデプロイして構成した後は、管理 UI を起動し、すべてが動作することを確認することを推奨します。 管理 UI を起動するユーザーには、Windows Virtual Desktop テナントを表示/編集できるロールの割り当てが必要です。

## <a name="set-up-powershell"></a>PowerShell のセットアップ

まず、Az と Azure AD の両方の PowerShell モジュールにサインインします。 サインイン方法は次のとおりです。

1. 管理者として PowerShell を開き、PowerShell スクリプトを保存したディレクトリに移動します。
2. 次のコマンドレットを実行して、あなたが管理ツールの作成に使用する Azure サブスクリプションの所有者または共同作成者のアクセス許可を持つアカウントで Azure にサインインします。

    ```powershell
    Login-AzAccount
    ```

3. 次のコマンドレットを実行して、Az PowerShell モジュールで使用したのと同じアカウントを使用して Azure AD にサインインします。

    ```powershell
    Connect-AzureAD
    ```

4. その後、RDS-Templates GitHub repo から2つの PowerShell スクリプトを保存したフォルダーに移動します。

サインイン時に使用した PowerShell ウィンドウを開いたままにして、サインインした状態で追加の PowerShell コマンドレットを実行します。

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory アプリの登録を作成する

管理ツールを正常にデプロイして構成するには、まず、[RDS-Templates GitHub リポジトリ](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)から次の PowerShell スクリプトをダウンロードする必要があります。

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

次のコマンドを実行して、必要な API アクセス許可を持つアプリの登録を作成します。

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Azure AD アプリの登録は完了です。これで管理ツールをデプロイできるようになりました。

## <a name="deploy-the-management-tool"></a>管理ツールをデプロイする

次の PowerShell コマンドを実行して、管理ツールをデプロイし、先ほど作成したサービス プリンシパルに関連付けます。

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Web アプリの作成後に、Azure AD アプリケーションにリダイレクト URI を追加して、ユーザーが正常にサインインできるようにする必要があります。

## <a name="set-the-redirect-uri"></a>リダイレクト URI を設定する

次の PowerShell コマンドを実行して、Web アプリの URL を取得し、それを認証リダイレクト URI (応答 URL とも呼ばれます) として設定します。

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

これでリダイレクト URI が追加できました。次に、管理ツールが API バックエンドサービスとインタラクトできるように API URL を更新する必要があります。

## <a name="update-the-api-url-for-the-web-application"></a>Web アプリケーションの API URL を更新する

次のスクリプトを実行して、Web アプリケーションのフロントエンドの API URL 構成を更新します。

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

これで管理ツールの Web アプリが完全に構成されました。次に、Azure AD アプリケーションを検証して同意を提供します。

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Azure AD アプリケーションを検証して同意を提供する

次のステップに従い、Azure AD アプリケーションを検証して同意を提供します。

1. インターネット ブラウザーを開き、管理者アカウントで [Azure portal](https://portal.azure.com/) にサインインします。
2. Microsoft Azure potal の上部にある検索バーで「**アプリの登録**」を検索し、 **[サービス]** の項目を選択します。
3. **[すべてのアプリケーション]** を選択し、「[Azure Active Directory アプリの登録を作成する](#create-an-azure-active-directory-app-registration)」で PowerShell スクリプトに命名した一意のアプリ名を検索します。
4. 次の図で示すように、ブラウザーの左側のパネルで **[認証]** を選択し、リダイレクト URI が管理ツールの Web アプリの URL と同じであることを確認します。

   [![入力したリダイレクト URI の認証ページ](../media/management-ui-redirect-uri-inline.png)](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. 左側のパネルで **[API のアクセス許可]** を選択し、アクセス許可が追加されたことを確認します。 あなたがグローバル管理者の場合は、 **[`tenantname` に管理者の同意を与えます]** ボタンを選択し、ダイアログ プロンプトに従って、あなたの組織に対する管理者の同意を提供します。

    [ ![[API のアクセス許可] ページ](../media/management-ui-permissions-inline.png) ](../media/management-ui-permissions-expanded.png#lightbox)

これで管理ツールを使用開始できるようになりました。

## <a name="use-the-management-tool"></a>管理ツールを使用する

管理ツールの設定が完了したので、今後はいつでもどこでも起動できます。 ツールを起動するには、次の手順を実行します。

1. Web ブラウザーで Web アプリの URL を開きます。 URL を覚えていない場合は、Azure にサインインし、管理ツール用にデプロイしたアプリサービスを見つけて、その URL を選択します。
2. ご自分の Windows Virtual Desktop 資格情報を使用してサインインします。

   > [!NOTE]
   > 管理ツールの構成時に管理者の同意を与えていない場合、このツールを使用するためには、サインインする各ユーザーがそれぞれ独自にユーザーの同意を提供する必要があります。

3. テナント グループを選択するよう求められたときには、ドロップダウン リストから **[既定のテナント グループ]** を選択します。
4. **[既定のテナント グループ]** を選択すると、ウィンドウの右側にメニューが表示されます。 このメニューの中から、テナント グループの名前を探して選択します。

   > [!NOTE]
   > カスタムのテナント グループがある場合は、ドロップダウン リストから選択せずに、手動でその名前を入力します。

## <a name="report-issues"></a>レポートに関する問題

管理ツールまたはその他の Windows Virtual Desktop ツールで問題が発生した場合には、「[Remote Desktop Services のための Azure Resource Manager テンプレート](https://github.com/Azure/RDS-Templates/blob/master/README.md)」の指示に従い、GitHub でそれを報告してください。

## <a name="next-steps"></a>次のステップ

管理ツールをデプロイして接続する方法を学習したので、Azure Service Health を使用して、サービスの問題と正常性の勧告を監視する方法を学習できます。 詳細については、[「サービスアラート設定のチュートリアル」](set-up-service-alerts-2019.md)を参照してください。
