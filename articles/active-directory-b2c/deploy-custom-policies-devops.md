---
title: Azure Pipelines を使用してカスタム ポリシーをデプロイする
titleSuffix: Azure AD B2C
description: Azure DevOps Services で Azure Pipelines を使用して、CI/CD パイプラインに Azure AD B2C カスタム ポリシーをデプロイする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13f5f8da0bd58cef0974e8ea8f5f3c5172daa0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928734"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Azure Pipelines を使用してカスタム ポリシーをデプロイする

[Azure Pipelines][devops-pipelines] で設定した継続的インテグレーションとデリバリー (CI/CD) パイプラインを使用して、ソフトウェア デリバリーとコード管理の自動化に Azure AD B2C カスタム ポリシーを含めることができます。 開発、テスト、運用などのさまざまな Azure AD B2C 環境にデプロイするときは、手動プロセスを削除し、Azure Pipelines を使用して自動テストを実行することをお勧めします。

Azure Pipelines を有効にして Azure AD B2C 内でカスタム ポリシーを管理するには、次の 3 つの主要な手順が必要です。

1. Azure AD テナントに Web アプリケーション登録を作成する
1. Azure リポジトリを構成する
1. Azure パイプラインを構成する

> [!IMPORTANT]
> Azure パイプラインを使用した Azure AD B2C カスタム ポリシーの管理では、現在、Microsoft Graph API `/beta` エンドポイントで利用できる **プレビュー** 操作を使用します。 実稼働アプリケーションにおけるこれらの API の使用はサポートされていません。 詳細については、「[Microsoft Graph REST API ベータ版のエンドポイント リファレンス](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure AD B2C テナント](tutorial-create-tenant.md)、および [B2C IEF ポリシー管理者](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)ロールを持つディレクトリー内のユーザーの資格情報
* テナントにアップロード済みの[カスタム ポリシー](custom-policy-get-started.md)
* Microsoft Graph API のアクセス許可 *Policy.ReadWrite.TrustFramework* でテナントに登録されている [管理アプリ](microsoft-graph-get-started.md)
* [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)、および [Azure DevOps Services プロジェクト][devops-create-project]へのアクセス

## <a name="client-credentials-grant-flow"></a>クライアント資格情報の付与フロー

ここで説明するシナリオでは、OAuth 2.0 [クライアント資格情報付与フロー](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)を使用して、Azure Pipelines と Azure AD B2C 間のサービス間呼び出しを利用します。 この付与フローでは、Azure Pipelines のような Web サービス (Confidential クライアント) は、別の Web サービス (この場合、Microsoft Graph API) を呼び出すときに、ユーザーを偽装する代わりに、独自の資格情報を使用して認証することができます。 Azure Pipelines は、非対話形式でトークンを取得し、Microsoft Graph API に要求を行います。

## <a name="register-an-application-for-management-tasks"></a>管理タスクのためにアプリケーションを登録する

「[前提条件](#prerequisites)」で述べたように、Azure Pipelines によって実行される PowerShell スクリプトがテナント内のリソースにアクセスするために使用できるアプリケーション登録が必要です。

自動タスクに使用するアプリケーション登録が既にある場合は、アプリ登録の **API アクセス許可** 内で **[Microsoft Graph]**  >  **[ポリシー]**  >  **[Policy.ReadWrite.TrustFramework]** アクセス許可がそれに対して付与されていることを確認します。

管理アプリケーションの登録手順については、[Microsoft Graph を使用した Azure AD B2C の管理](microsoft-graph-get-started.md)に関する記事をご覧ください。

## <a name="configure-an-azure-repo"></a>Azure リポジトリを構成する

管理アプリケーションを登録すると、ポリシー ファイル用のリポジトリを構成する準備が整います。

1. Azure DevOps Services 組織にサインインします。
1. [新しいプロジェクトを作成する][devops-create-project]か、既存のプロジェクトを選択します。
1. プロジェクト内で **[リポジトリ]** に移動し、 **[ファイル]** ページを選択します。 既存のレポジトリを選択するか、この演習用に 1 つ作成します。
1. "*B2CAssets*" という名前のフォルダーを作成します。 必要なプレースホルダー ファイルに "*README.md*" という名前を付けて、そのファイルを **コミット** します。 このファイルは、必要に応じて、後で削除できます。
1. Azure AD B2C ポリシー ファイルを "*B2CAssets*" フォルダーに追加します。 これには、*TrustFrameworkBase.xml*、*TrustFrameWorkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml*、*PasswordReset.xml*、および作成した他のすべてのポリシーが含まれます。 後の手順で使用するために、各 Azure AD B2C ポリシー ファイルのファイル名を記録します (PowerShell スクリプトの引数として使用されます)。
1. リポジトリのルート ディレクトリに "*Scripts*" という名前のフォルダーを作成し、プレースホルダー ファイルに "*DeployToB2c.ps1*" という名前を付けます。 この時点でファイルをコミットしないでください。後の手順で実行します。
1. 次の PowerShell スクリプトを "*DeployToB2c.ps1*" に貼り付け、ファイルを **コミット** します。 このスクリプトは Azure AD からトークンを取得し、Microsoft Graph API を呼び出して、"*B2CAssets*" フォルダー内のポリシーを Azure AD B2C テナントにアップロードします。

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Azure パイプラインを構成する

リポジトリが初期化され、カスタム ポリシー ファイルが設定されたら、リリース パイプラインをセットアップする準備が整います。

### <a name="create-pipeline"></a>パイプラインの作成

1. Azure DevOps Services 組織にサインインし、プロジェクトに移動します。
1. プロジェクトで、 **[パイプライン]**  >  **[リリース]**  >  **[新しいパイプライン]** の順に選択します。
1. **[テンプレートの選択]** で、 **[空のジョブ]** を選択します。
1. **ステージ名** (たとえば、*DeployCustomPolicies*) を入力し、ペインを閉じます。
1. **[Add an artifact]\(成果物の追加\)** を選択し、 **[ソースの種類]** で **[Azure リポジトリ]** を選択します。
    1. PowerShell スクリプトが設定された "*Scripts*" フォルダーを含むソース リポジトリを選択します。
    1. **既定のブランチ** を選択します。 前のセクションで新しいリポジトリを作成した場合、既定のブランチは "*master*" です。
    1. **[既定のバージョン]** の設定は、 *[既定のブランチからの最新バージョン]* のままにします。
    1. リポジトリの **ソース エイリアス** を入力します。 たとえば、"*policyRepo*" などです。 エイリアス名にはスペースを含めないでください。
1. **[追加]** を選択します。
1. 目的に合わせてパイプラインの名前を変更します。 たとえば、"*Deploy Custom Policy Pipeline*" などです。
1. **[保存]** を選択して、パイプライン構成を保存します。

### <a name="configure-pipeline-variables"></a>パイプライン変数を構成する

1. **[変数]** タブを選択します。
1. **[パイプライン変数]** の下に次の変数を追加し、指定された値を設定します。

    | 名前 | 値 |
    | ---- | ----- |
    | `clientId` | 前に登録したアプリケーションの **アプリケーション (クライアント) ID**。 |
    | `clientSecret` | 前に作成した **クライアント シークレット** の値。 <br /> 変数の型を **シークレット** に変更します (ロック アイコンを選択)。 |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`。ここで、*your-b2c-tenant* は Azure AD B2C テナントの名前です。 |

1. **[保存]** を選択して変数を保存します。

### <a name="add-pipeline-tasks"></a>パイプライン タスクを追加する

次に、ポリシー ファイルをデプロイするタスクを追加します。

1. **[タスク]** タブを選択します。
1. **[エージェント ジョブ]** を選択し、プラス記号 ( **+** ) を選択してエージェント ジョブにタスクを追加します。
1. 「**PowerShell**」を検索して選択します。 "Azure PowerShell"、"ターゲット マシンでの PowerShell"、または別の PowerShell エントリを選択しないでください。
1. 新しく追加された **PowerShell スクリプト** タスクを選択します。
1. PowerShell Script タスクに対して次の値を入力します。
    * **タスクのバージョン**: 2.*
    * **表示名**:このタスクでアップロードするポリシーの名前。 たとえば、*B2C_1A_TrustFrameworkBase*。
    * **[種類]** :ファイル パス
    * **スクリプトのパス**:省略記号 (**_..._* _) を選択し、_Scripts* フォルダーに移動して、*DeployToB2C.ps1* ファイルを選択します。
    * **引数:**

        **引数** に対して次の値を入力します。 `{alias-name}` は、前のセクションで指定したエイリアスに置き換えます。

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        たとえば、指定したエイリアスが "*policyRepo*" の場合、引数の行は次のようになります。

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. **[保存]** を選択して、エージェント ジョブを保存します。

たった今追加したタスクによって、"*1 つの*" ポリシー ファイルが Azure AD B2C にアップロードされます。 続行する前に、手動でジョブをトリガー (**リリースを作成**) して、追加のタスクを作成する前に正常に完了することを確認します。

タスクが正常に完了したら、カスタム ポリシー ファイルごとに上記の手順を実行して、デプロイ タスクを追加します。 各ポリシーの `-PolicyId` および `-PathToFile` 引数の値を変更します。

`PolicyId` は、TrustFrameworkPolicy ノード内の XML ポリシー ファイルの先頭にある値です。 たとえば、次のポリシー XML の `PolicyId` は "*B2C_1A_TrustFrameworkBase*" です。

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

エージェントを実行し、ポリシー ファイルをアップロードするときは、次の順序でアップロードされていることを確認します。

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Identity Experience Framework では、ファイル構造が階層チェーンに基づいて構築されるため、この順序が適用されます。

## <a name="test-your-pipeline"></a>パイプラインをテストする

リリース パイプラインをテストするには、次のようにします。

1. **[パイプライン]** 、 **[リリース]** の順に選択します。
1. 前に作成したパイプラインを選択します。たとえば、*DeployCustomPolicies* などです。
1. **[リリースの作成]** を選択し、次に **[作成]** を選択してリリースをキューに入れます。

リリースがキューに入れられたことを示す通知バナーが表示されます。 その状態を表示するには、通知バナー内のリンクを選択するか、 **[リリース]** タブの一覧から選択します。

## <a name="next-steps"></a>次のステップ

各項目の詳細情報

* [クライアント資格情報を使用したサービス間の呼び出し](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
