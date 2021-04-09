---
title: トークンの有効期間を設定する
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームによって発行されたトークンの有効期間を設定する方法について説明します。 組織の既定のポリシーを管理する方法、Web サインインのポリシーを作成する方法、Web API を呼び出すネイティブ アプリのポリシーを作成する方法、および高度なポリシーを管理する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3ec94543a53e3e5b7709801de8f4cf1dde3fc3d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99428117"
---
# <a name="configure-token-lifetime-policies-preview"></a>トークンの有効期間ポリシーを構成する (プレビュー)
Microsoft ID プラットフォームによって発行されたアクセス トークン、SAML トークン、または ID トークンの有効期間を指定できます。 組織のすべてのアプリ、マルチテナント (複数の組織) アプリケーション、または組織の特定のサービス プリンシパルに対して、トークンの有効期間を設定できます。 詳細については、[構成可能なトークンの有効期間](active-directory-configurable-token-lifetimes.md)に関する記事を参照してください。

このセクションでは、トークンの有効期間に新しいルールを適用する場合に役立つ、一般的なポリシー シナリオについて説明します。 この例では、Web アプリでユーザーによる頻繁な認証を必須とするポリシーを作成する方法を説明します。

## <a name="get-started"></a>はじめに
使用を開始するには、次の手順を実行します。

1. 最新版の [Azure AD PowerShell モジュール パブリック プレビュー リリース](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。
1. `Connect` コマンドを実行して、Azure AD 管理者アカウントにサインインします。 新しいセッションを開始するたびにこのコマンドを実行します。

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 組織で作成されたすべてのポリシーを表示するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。  上記の既定値と異なる定義されたプロパティ値が含まれる結果は、廃止の範囲内にあります。

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. 特定のポリシーにリンクされているアプリとサービス プリンシパルを確認するには、**1a37dad8-5da7-4cc8-87c7-efbc0326cf20** を独自のポリシー ID に置き換えて、次の [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。 その後、条件付きアクセスのサインイン頻度を構成するか、Azure AD の既定値をそのまま使用するかを決定できます。

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

更新およびセッション トークン構成プロパティのカスタム値を定義するポリシーがテナントにある場合、Microsoft は、これらのポリシーを上記の既定値を反映する値に更新することをお勧めします。 変更が加えられていない場合、Azure AD によって自動的に既定値が使用されます。

## <a name="create-a-policy-for-web-sign-in"></a>Web サインインのポリシーを作成する

この例では、ユーザーが、Web アプリで頻繁に認証を必要とするポリシーを作成します。 このポリシーは、アクセス トークンと ID トークンの有効期間と、多要素セッション トークンの最長有効期間を Web アプリのサービス プリンシパルに設定します。

1. トークンの有効期間ポリシーを作成します。

    この Web サインイン用のポリシーでは、アクセス トークンと ID トークンの有効期間と、単一要素セッション トークンの最長有効期間を 2 時間に設定します。

    1. ポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 新しいポリシーを表示して、そのポリシーの **ObjectId** を取得するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの **ObjectId** も取得する必要があります。

    1. [Get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用して、組織のすべてのサービス プリンシパルまたは 1 つのサービス プリンシパルを表示します。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. サービス プリンシパルがある場合は、[Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>更新トークンとセッション トークンのトークン有効期間ポリシーの作成
> [!IMPORTANT]
> 2021 年 1 月 30 日の時点で、更新およびセッション トークンの有効期間は構成できません。 Azure Active Directory では、既存のポリシーの更新およびセッション トークンの構成が考慮されなくなくなりました。  既存のトークンの有効期限が切れた後に発行される新しいトークンは、[既定の構成](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement)に設定されるようになりました。 更新およびセッション トークンの構成の廃止後も、アクセス、SAML、ID の各トークンの有効期間を構成することはできます。
>
> 既存のトークンの有効期間は変更されません。 有効期限が切れると、既定値に基づいて新しいトークンが発行されます。
>
> ユーザーが再度サインインするように求められるまでの時間を定義し続ける必要がある場合は、条件付きアクセスでサインインの頻度を構成します。 条件付きアクセスの詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)」をお読みください。

### <a name="manage-an-organizations-default-policy"></a>組織の既定のポリシーを管理する
この例では、組織全体でユーザーがサインインする頻度を少なくするポリシーを作成します。 そのためには、組織全体に適用される単一要素の更新トークンに対してトークンの有効期間ポリシーを作成します。 このポリシーは、組織内のすべてのアプリケーションと、ポリシーがまだ設定されていない各サービス プリンシパルに適用されます。

1. トークンの有効期間ポリシーを作成します。

    1. 単一要素の更新トークンを "until-revoked" に設定します。 トークンは、アクセスが取り消されるまで期限切れになりません。 次のポリシー定義を作成します。

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. ポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 空白を削除するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. 新しいポリシーを表示し、ポリシーの **ObjectId** を取得するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. ポリシーを更新します。

    この例で設定する最初のポリシーは、サービスに求められるほど厳密にしないようにすることもできます。 単一要素の更新トークンを 2 日で期限が切れるように設定するには、次のコマンドを実行します。

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Web API を呼び出すネイティブ アプリケーションのポリシーを作成する
この例では、ユーザーに必要とする認証の頻度を少なくするポリシーを作成します。 ポリシーにより、ユーザーが再認証を必要とするまでの非アクティブでいられる時間も長くなります。 ポリシーは、Web API に適用されます。 ネイティブ アプリがリソースとして Web API を要求すると、このポリシーが適用されます。

1. トークンの有効期間ポリシーを作成します。

    1. Web API に対して厳密なポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 新しいポリシーを表示するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Web API にポリシーを割り当てます。 アプリケーションの **ObjectId** を取得する必要もあります。 [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) コマンドレットを使用して、アプリの **ObjectId** を見つけるか、[Azure portal](https://portal.azure.com/) を使用します。

    アプリの **ObjectId** を取得し、ポリシーを割り当てます。

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>詳細なポリシーを管理する
この例では、いくつかのポリシーを作成して、優先度システムのしくみを説明します。 複数のオブジェクトに適用される複数のポリシーを管理する方法も説明します。

1. トークンの有効期間ポリシーを作成します。

    1. 単一要素の更新トークンの有効期間を 30 日間に設定する組織の既定のポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 新しいポリシーを表示するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを使用します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. サービス プリンシパルにポリシーを割り当てます。

    これで、組織全体に適用されるポリシーが得られます。 特定のサービス プリンシパルに対してはこの 30 日間のポリシーを保持しますが、組織の既定のポリシーを "until-revoked" の上限となるよう変更するとします。

    1. 組織のすべてのサービス プリンシパルを表示するには、[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用します。

    1. サービス プリンシパルがある場合は、[Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. `IsOrganizationDefault` フラグを false に設定します。

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. 新しい組織の既定のポリシーを作成します。

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    これで、元のポリシーがサービス プリンシパルにリンクされ、新しいポリシーが組織の既定のポリシーとして設定されます。 サービス プリンシパルに適用されるポリシーが、組織の既定のポリシーよりも優先されることに注意してください。

## <a name="next-steps"></a>次のステップ
Azure AD の条件付きアクセスにおける[認証セッションの管理機能](../conditional-access/howto-conditional-access-session-lifetime.md)について学習する。
