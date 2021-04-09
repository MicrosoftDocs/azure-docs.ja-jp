---
title: PowerShell を使用してカスタム ポリシーを管理する
titleSuffix: Azure AD B2C
description: Azure AD B2C のカスタム ポリシーをプログラムで管理するには、Azure Active Directory (Azure AD) PowerShell コマンドレットを使用します。 PowerShell を使用してカスタム ポリシーの作成、読み取り、更新、削除を行います。
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928717"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Azure PowerShell を使用して Azure AD B2C のカスタム ポリシーを管理する

Azure PowerShell には、Azure AD B2C テナントに対するコマンド ラインベースおよびスクリプトベースのカスタム ポリシー管理用コマンドレットがいくつか用意されています。 Azure AD PowerShell モジュールを使用して、次のことを行う方法について説明します。

* Azure AD B2C テナントのカスタム ポリシーを一覧表示する
* テナントからポリシーをダウンロードする
* ポリシーの内容を上書きして既存のポリシーを更新する
* Azure AD B2C テナントに新しいポリシーをアップロードする
* テナントからカスタム ポリシーを削除する

## <a name="prerequisites"></a>前提条件

* [Azure AD B2C テナント](tutorial-create-tenant.md)、および [B2C IEF ポリシー管理者](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)ロールを持つディレクトリー内のユーザーの資格情報
* テナントにアップロード済みの[カスタム ポリシー](custom-policy-get-started.md)
* [Azure AD PowerShell for Graph **プレビュー モジュール**](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>PowerShell セッションを B2C テナントに接続する

Azure AD B2C テナントでカスタム ポリシーを使用するには、まず、[Connect-AzureAD][Connect-AzureAD] コマンドを使用して、PowerShell セッションをテナントに接続する必要があります。

次のコマンドを実行します。`{b2c-tenant-name}` を Azure AD B2C テナントの名前で置き換えてください。 ディレクトリ内で [B2C IEF ポリシー管理者](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)ロールが割り当てられているアカウントを使用してサインインします。

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

サインインが成功したことを示すコマンド出力の例:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>テナント内のすべてのカスタム ポリシーを一覧表示する

カスタム ポリシーを検出することで、Azure AD B2C 管理者はビジネス ロジックを確認および管理したり、運用に追加したりできます。 Azure AD B2C テナント内のカスタム ポリシーの ID 一覧を取得するには、[Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] コマンドを使用します。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

コマンドの出力例:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>ポリシーをダウンロードする

ポリシー ID の一覧を確認したら、[Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] を使用して、特定のポリシーをターゲットにして、その内容をダウンロードできます。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

この例では、ID *B2C_1A_signup_signin* のポリシーがダウンロードされます。

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

ローカルでポリシーの内容を編集するには、`-OutputFilePath` 引数を使用してコマンドの出力をファイルにパイプ処理し、このファイルを任意のエディターで開きます。

出力をファイルに送信するコマンドの例:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>既存のポリシーを更新する

作成またはダウンロードしたポリシー ファイルを編集したら、[Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] コマンドを使用して、更新済みポリシーを Azure AD B2C に発行できます。

Azure AD B2C テナントに既に存在するポリシーの ID を指定して `Set-AzureADMSTrustFrameworkPolicy` コマンドを発行すると、そのポリシーの内容が上書きされます。

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

コマンドの例:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

その他の例については、[Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] コマンド リファレンスを参照してください。

## <a name="upload-a-new-policy"></a>新しいポリシーをアップロードする

運用環境で実行されているカスタム ポリシーを変更する場合は、フォールバックまたは A/B テスト シナリオに対応するために、複数のバージョンのポリシーを発行することができます。 または、既存のポリシーのコピーを作成し、小さな変更をいくつか加えて修正し、別のアプリケーションで使用できるように新しいポリシーとしてアップロードすることもできます。

新しいポリシーをアップロードするには、[New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] コマンドを使用します。

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

コマンドの例:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>カスタム ポリシーを削除する

クリーンな運用ライフ サイクルを維持するには、使用されていないカスタム ポリシーを定期的に削除することをお勧めします。 たとえば、新しいポリシー セットへの移行を実行し、新しいポリシーが機能することを確認したら、古いポリシー バージョンを削除することができます。 また、一連のカスタム ポリシーを発行しようとしてエラーが発生した場合は、失敗したリリースの一部として作成されたポリシーを削除した方がよい場合があります。

[Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] コマンドを使用して、テナントからポリシーを削除します。

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

コマンドの例:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>ポリシーのアップロードのトラブルシューティング

新しいカスタム ポリシーの発行や既存ポリシーの更新を試みたときに、XML の書式設定が不適切であったり、ポリシー ファイルの継承チェーンにエラーがあったりすると、検証エラーが発生する可能性があります。

次の例では、無効な形式の XML を含む内容を使用してポリシーを更新しようとしています (簡潔にするために出力は切り詰められています)。

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

カスタム ポリシーのトラブルシューティングの詳細については、「[Azure AD B2C のカスタム ポリシーと Identity Experience Framework のトラブルシューティング](./troubleshoot-custom-policies.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

PowerShell を使用して、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインの一環としてカスタム ポリシーをデプロイする方法の詳細については、[Azure DevOps パイプラインからのカスタム ポリシーのデプロ](deploy-custom-policies-devops.md)に関するページをご覧ください。

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
