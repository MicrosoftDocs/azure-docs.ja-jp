---
title: Azure Active Directory の代替ログイン ID としてメールでサインインする
description: 代替ログイン ID としてメール アドレスを使用して Azure Active Directory にサインインする (プレビュー) ようにユーザーを構成して有効にする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 4e39d7f15e3ca3c6e241c767a5f881d7170c6379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255969"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>代替ログイン ID としてメールを使用して Azure Active Directory にサインインする (プレビュー)

> [!NOTE]
> 代替ログイン ID としてメールを使用した Azure AD へのサインインは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

多くの組織では、ユーザーがオンプレミス ディレクトリ環境と同じ資格情報を使用して Azure Active Directory (Azure AD) にサインインできるようにしたいと考えています。 ハイブリッド認証と呼ばれるこの方法では、ユーザーは 1 組の資格情報を記憶しておくだけでよくなります。

一部の組織では、次の理由によりハイブリッド認証に移行していません。

* 既定で、Azure AD のユーザー プリンシパル名 (UPN) が、オンプレミス ディレクトリと同じ UPN に設定されています。
* Azure AD UPN を変更すると、オンプレミス環境と Azure AD 環境の間で不一致が生じ、特定のアプリケーションやサービスで問題が発生する可能性があります。
* ビジネスまたはコンプライアンス上の理由により、組織では、Azure AD へのサインインにオンプレミスの UPN を使用したくないと考えています。

ハイブリッド認証への移行を支援するために、ユーザーが代替ログイン ID として確認済みドメイン内のメールでサインインできるように Azure AD を構成できます。 たとえば、*Contoso* が *Fabrikam* にブランド変更した場合、従来の `balas@contoso.com` UPN でのサインインを続行するのではなく、代替ログイン ID としてメールを使用できるようになりました。 アプリケーションまたはサービスにアクセスするために、ユーザーは、割り当てられたメール (`balas@fabrikam.com` など) を使用して Azure AD にサインインします。

この記事では、代替ログイン ID として電子メールを有効にして使用する方法について説明します。 この機能は、Azure AD Free エディション以上で使用できます。

> [!NOTE]
> この機能は、クラウドで認証された Azure AD ユーザーのみを対象としています。

> [!NOTE]
> 現在、クラウド認証を使用したテナントの Azure AD 参加済みの Windows 10 デバイスでは、この機能はサポートされていません。 この機能は、Hybrid Azure AD 参加済みデバイスには適用できません。

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Azure AD サインイン方法の概要

Azure AD にサインインするには、ユーザーが自分のアカウントを一意に識別する名前を入力します。 これまでは、サインイン名として使用できるのは Azure AD UPN のみでした。

オンプレミスの UPN がユーザーの推奨されるサインイン メールである組織の場合、この方法は優れていました。 そうした組織では、Azure AD UPN をオンプレミスの UPN とまったく同じ値に設定することで、ユーザーの一貫したサインイン エクスペリエンスを実現しています。

しかし、組織によっては、オンプレミスの UPN をサインイン名として使用していません。 オンプレミス環境では、代替ログイン ID を使用してサインインできるようにローカル AD DS を構成しています。 Azure AD UPN をオンプレミスの UPN と同じ値に設定すると、Azure AD がユーザーにその値を使用してサインインするよう求めるため、そうするわけにはいかないのです。

この問題の一般的な回避策は、Azure AD UPN を、ユーザーがサインインに使用するメール アドレスに設定することでした。 この方法は有効ですが、オンプレミスの AD と Azure AD 内で UPN が異なるため、この構成はすべての Microsoft 365 ワークロードと互換性がありません。

別の方法は、Azure AD とオンプレミスの UPN を同じ値に同期し、ユーザーが確認済みのメールを使用して Azure AD にサインインできるように Azure AD を構成するというものです。 この機能を実現するには、オンプレミス ディレクトリ内のユーザーの *ProxyAddresses* 属性で 1 つ以上のメール アドレスを定義します。 そうすると、*ProxyAddresses* が Azure AD Connect を使用して自動的に Azure AD と同期されます。

## <a name="preview-limitations"></a>プレビューの制限事項

電子メールを代替ログイン ID として Azure AD にサインインすることは、Azure AD Free エディション以上で使用できます。

現在のプレビュー状態では、ユーザーが代替ログイン ID として UPN 以外の電子メールを使用してサインインすると、次の制限が適用されます。

* UPN 以外の電子メールを使用してサインインしている場合でも、ユーザーに UPN が表示される場合があります。 次のような動作の例が表示されます。
    * `login_hint=<non-UPN email>` を使用した Azure AD サインインに誘導される場合、ユーザーは UPN でサインインするように求められます。
    * ユーザーが UPN 以外の電子メールを使用してサインインし、間違ったパスワードを入力すると、" *[パスワードの入力]* " ページが UPN の表示に変わります。
    * [https://portal.azure.com](https://portal.azure.com) や Microsoft Office など、一部の Microsoft サイトおよびアプリでは、通常、右上に表示される **[アカウント マネージャー]** コントロールに、サインインに使用される UPN 以外の電子メールではなく、ユーザーの UPN が表示される場合があります。

* 現在、次のような一部のフローは、UPN 以外の電子メールとは互換性がありません。
    * 現在、ID 保護では、電子メールの代替ログイン ID と "*漏洩した資格情報*" のリスク検出との照合を行っていません。 このリスク検出では、UPN を使用して、漏洩した資格情報を照合します。 詳細については、[Azure AD Identity Protection のリスク検出と修復][identity-protection]に関する記事を参照してください。
    * 代替ログイン ID の電子メールに送信された B2B 招待は、完全にはサポートされていません。 代替ログイン ID として電子メールに送信された招待を受け入れると、ユーザーはテナント化されたエンドポイントで代替電子メールを使用してサインインできなくなる場合があります。

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>サインインのメール アドレスを Azure AD に同期する

従来の Active Directory ドメイン サービス (AD DS) 認証または Active Directory フェデレーションサービス (AD FS) 認証は、ネットワーク上で直接行われ、AD DS インフラストラクチャによって処理されます。 ハイブリッド認証では、ユーザーは代わりに Azure AD に直接サインインすることができます。

このハイブリッド認証アプローチをサポートするには、オンプレミスの AD DS 環境を、[Azure AD Connect][azure-ad-connect] を使用して Azure AD に同期させ、パスワード ハッシュの同期 (PHS) またはパススルー認証 (PTA) を使用するように構成します。

どちらの構成オプションでも、ユーザーはユーザー名とパスワードを Azure AD に送信し、ここで資格情報が検証され、チケットが発行されます。 ユーザーが Azure AD にサインインすると、組織が AD FS インフラストラクチャをホストし管理する必要性はなくなります。

![パスワード ハッシュ同期を使用した Azure AD ハイブリッド ID の図](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![パススルー認証を使用した Azure AD ハイブリッド ID の図](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Azure AD Connect によって自動的に同期されるユーザー属性の 1 つが *ProxyAddresses* です。 オンプレミスの AD DS 環境で *ProxyAddresses* 属性の一部として定義されているメール アドレスをユーザーが持っている場合は、Azure AD に自動的に同期されます。 このメール アドレスは、直接 Azure AD サインイン プロセスで、代替ログイン ID として使用できます。

> [!IMPORTANT]
> テナントの確認済みドメインのメールのみが Azure AD に同期されます。 それぞれの Azure AD テナントには、1 つ以上の確認済みドメインがあり、これらは所有権が実証され、テナントに一意にバインドされます。
>
> 詳細については、[Azure AD でのカスタム ドメイン名の追加と確認][verify-domain]に関するページをご覧ください。

詳細については、「[Azure AD ハイブリッド ID ソリューションの適切な認証方法を選択する][hybrid-auth-methods]」をご覧ください。

## <a name="enable-user-sign-in-with-an-email-address"></a>メール アドレスを使用してユーザーのサインインを有効にする

*ProxyAddresses* 属性を適用したユーザーが Azure AD Connect を使用して Azure AD に同期された後、ユーザーがテナントの代替ログイン ID としてメールを使用してサインインできるようにする機能を有効にする必要があります。 この機能は、UPN 値に対してだけでなく、メール アドレスの *ProxyAddresses* 値に対してサインイン名を確認するように、Azure AD ログイン サーバーに指示します。

現在、プレビュー期間中は、PowerShell を使用して、代替ログイン ID 機能としてメールを使用したサインインのみを有効にすることができます。 次の手順を完了するには、*テナント管理者* 権限が必要です。

1. 管理者として PowerShell セッションを開き、[Install-Module][Install-Module] コマンドレットを使用して *AzureADPreview* モジュールをインストールします。

    ```powershell
    Install-Module AzureADPreview
    ```

    メッセージが表示されたら、 **[Y]** を選択して NuGet をインストールするか、信頼されていないリポジトリからインストールします。

1. [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して *テナント管理者* として Azure AD テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

    このコマンドは、アカウント、環境、およびテナント ID に関する情報を返します。

1. 次のように [Get-AzureADPolicy][Get-AzureADPolicy] コマンドレットを使用して、*HomeRealmDiscoveryPolicy* ポリシーがテナントに 既に存在しているかどうかを確認します。

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. 現在構成されているポリシーがない場合、コマンドは何も返しません。 ポリシーが返された場合は、この手順をスキップし、次の手順に進んで、既存のポリシーを更新します。

    *HomeRealmDiscoveryPolicy* ポリシーをテナントに追加するには、次の例に示すように、[New-AzureADPolicy][New-AzureADPolicy] コマンドレットを使用し、*AlternateIdLogin* 属性を *"Enabled": true* に設定します。

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    ポリシーが正常に作成されると、次の出力例に示すように、コマンドによってポリシー ID が返されます。

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 既にポリシーが構成されている場合は、次のポリシー出力の例に示すように、 *AlternateIdLogin*  属性が有効になっているかどうかを確認します。

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    ポリシーが存在していても、*AlternateIdLogin* 属性が存在していないか有効になっていない場合、または他の属性が保持したいと考えているポリシーに存在している場合は、[Set-AzureADPolicy][Set-AzureADPolicy] コマンドレットを使用して既存のポリシーを更新します。

    > [!IMPORTANT]
    > ポリシーを更新するときには、必ず古い設定と新しい  *AlternateIdLogin* 属性を含めます。

    次の例では、 *AlternateIdLogin* 属性を追加し、既に設定されている可能性のある  *AllowCloudPasswordValidation* 属性を保持します。

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    更新されたポリシーによって変更が表示され、*AlternateIdLogin* 属性が有効になっていることを確認します。

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

ポリシーが適用されている場合は、伝達されて、ユーザーが代替ログイン ID を使用してサインインできるようになるまでに、最大 1 時間かかることがあります。

## <a name="test-user-sign-in-with-email"></a>メールでユーザー サインインをテストする

ユーザーがメールでサインインできるかどうかをテストするには、[https://myprofile.microsoft.com][my-profile] を参照し、UPN (`balas@contoso.com` など) ではなくそのメール アドレス (`balas@fabrikam.com` など) に基づいてユーザー アカウントでサインインできます。 サインイン エクスペリエンスは、UPN ベースのサインイン イベントの場合と同じ外観になります。

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>メール アドレスを使用したユーザー サインインをテストするために段階的なロールアウトを有効にする  

[段階的なロールアウト][staged-rollout]を使用すると、テナント管理者は特定のグループに対して各機能を有効にすることができます。 テナント管理者が、段階的なロールアウトを使用して、メール アドレスでのユーザー サインインをテストすることをお勧めします。 管理者がこの機能をテナント全体にデプロイする準備ができたら、ホーム領域検出ポリシーを使用する必要があります。  


次の手順を完了するには、*テナント管理者* 権限が必要です。

1. 管理者として PowerShell セッションを開き、[Install-Module][Install-Module] コマンドレットを使用して、*AzureADPreview* モジュールをインストールします。

    ```powershell
    Install-Module AzureADPreview
    ```

    メッセージが表示されたら、 **[Y]** を選択して NuGet をインストールするか、信頼されていないリポジトリからインストールします。

2. [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して *テナント管理者* として Azure AD テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

    このコマンドは、アカウント、環境、およびテナント ID に関する情報を返します。

3. 次のコマンドレットを使用して、既存の段階的なロールアウト ポリシーをすべて一覧表示します。
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. この機能について既存の段階的なロールアウト ポリシーが存在しない場合は、段階的ロールアウト ポリシーを新たに作成し、ポリシー ID を書き留めておきます。

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

5. 段階的なロールアウト ポリシーに追加されるグループの directoryObject ID を検索します。 次の手順で使用するため、*Id* パラメーターに返される値をメモに記録します。
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. 次の例に示すように、グループを段階的なロールアウト ポリシーに追加します。 *-Id* パラメーターの値を、手順 4 でポリシー ID に対して返された値に置き換え、 *-RefObjectId* パラメーターの値を、手順 5 でメモに記録した *Id* に置き換えます。 グループ内のユーザーがプロキシ アドレスを使用してサインインできるようになるまで、最大 1 時間かかることがあります。

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
グループに新しく追加されたメンバーについては、プロキシ アドレスを使用してサインインできるようになるまで、最大 24 時間かかることがあります。

### <a name="removing-groups"></a>グループの削除

段階的なロールアウト ポリシーからグループを削除するには、次のコマンドを実行します。

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>ポリシーの削除

段階的なロールアウト ポリシーを削除するには、まずポリシーを無効にして、システムから削除します。

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>トラブルシューティング

ユーザーがメール アドレスを使用したサインイン イベントに問題を抱えている場合は、次のトラブルシューティングの手順を確認してください。

1. オンプレミスの AD DS 環境で、*ProxyAddresses* 属性に対してユーザー アカウントのメール アドレスが設定されていることを確認します。
1. Azure AD Connect が構成されていること、およびオンプレミスの AD DS 環境から Azure AD にユーザー アカウントが正常に同期されていることを確認します。
1. Azure AD *HomeRealmDiscoveryPolicy* ポリシーの *AlternateIdLogin* 属性が *"Enabled": true* に設定されていることを確認します。

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

## <a name="next-steps"></a>次のステップ

Azure AD アプリ プロキシや Azure AD Domain Services などのハイブリッド ID の詳細については、[オンプレミス ワークロードのアクセスおよび管理の Azure AD ハイブリッド ID][hybrid-overview] に関するページをご覧ください。

ハイブリッド ID 操作の詳細については、[パスワード ハッシュ同期方法][phs-overview]または[パススルー認証][pta-overview]の同期をご覧ください。

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
