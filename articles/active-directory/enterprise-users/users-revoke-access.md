---
title: Azure Active Directory で緊急時にユーザー アクセスを取り消す | Microsoft Docs
description: Azure Active Directory でユーザーのすべてのアクセスを取り消す方法
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 12/02/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826ca9fc20d8bbcf9a5f90ccc895b9f9867a6be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860577"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Azure Active Directory でユーザー アクセスを取り消す

管理者がユーザーのすべてのアクセスを取り消す必要があるシナリオには、侵害されたアカウント、従業員の解雇、およびその他の内部の脅威が含まれます。 環境の複雑さに応じて、管理者はアクセスが確実に取り消されるようにいくつかの手順を実行できます。 シナリオによっては、アクセスの取り消しが開始されてから、アクセスが実質的に取り消されるまでに一定の時間がかかることがあります。

リスクを軽減するには、トークンのしくみを理解しておく必要があります。 トークンにはさまざまな種類があり、以下のセクションで説明するパターンのいずれかに分類されます。

## <a name="access-tokens-and-refresh-tokens"></a>アクセス トークンと更新トークン

アクセス トークンと更新トークンは、シック クライアント アプリケーションでよく使用されます。また、シングル ページ アプリなどのブラウザーベースのアプリケーションでも使用されます。

- ユーザーが Azure AD に対して認証を行うと、承認ポリシーが評価され、そのユーザーに特定のリソースへのアクセスを許可できるかどうかが判断されます。  

- 承認されると、Azure AD によってリソースのアクセス トークンと更新トークンが発行されます。  

- Azure AD によって発行されたアクセス トークンの有効期限は、既定では 1 時間です。 認証プロトコルで許可されている場合、アプリは、アクセス トークンの有効期限が切れたときに更新トークンを Azure AD に渡すことで、ユーザーを自動で再認証できます。

その後、Azure AD によってその承認ポリシーが再評価されます。 ユーザーがまだ承認されている場合、Azure AD によって新しいアクセス トークンと更新トークンが発行されます。

アクセス トークンは、トークンの有効期間 (通常は約 1 時間) より短い時間内にアクセスを取り消す必要がある場合に、セキュリティ上の問題になることがあります。 このため、Microsoft では、Microsoft 365 アプリケーションへの[継続的アクセス評価](../conditional-access/concept-continuous-access-evaluation.md)を行うために積極的に取り組んでいます。これにより、アクセス トークンをほぼリアルタイムで確実に無効化できます。  

## <a name="session-tokens-cookies"></a>セッション トークン (Cookie)

ほとんどのブラウザーベースのアプリケーションでは、アクセス トークンと更新トークンではなく、セッション トークンが使用されます。  

- ユーザーがブラウザーを開いて、Azure AD 経由でアプリケーションに対して認証を行うと、ユーザーは 2 つのセッション トークンを受け取ります。 1 つは Azure AD からで、もう 1 つはアプリケーションからです。  

- アプリケーションが独自のセッション トークンを発行すると、アプリケーションへのアクセスはアプリケーションのセッションによって管理されます。 この時点で、ユーザーが影響を受けるのは、アプリケーションが認識している承認ポリシーのみになります。

- Azure AD の承認ポリシーは、アプリケーションがユーザーを Azure AD に送り返すたびに再評価されます。 通常、再評価は自動的に行われますが、頻度はアプリケーションの構成方法によって異なります。 セッション トークンが有効である限り、アプリがユーザーを Azure AD に送り返すことはありません。

- セッション トークンを取り消すには、アプリケーションが独自の承認ポリシーに基づいてアクセスを取り消す必要があります。 Azure AD では、アプリケーションによって発行されたセッション トークンを直接取り消すことはできません。  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>ハイブリッド環境でユーザーのアクセスを取り消す

オンプレミスの Active Directory が Azure Active Directory と同期されているハイブリッド環境では、IT 管理者が次の操作を実行することをお勧めします。  

### <a name="on-premises-active-directory-environment"></a>オンプレミスの Active Directory 環境

Active Directory の管理者として、オンプレミス ネットワークに接続し、PowerShell を開き、次の操作を実行します。

1. Active Directory でユーザーを無効にします。 「[Disable-ADAccount](/powershell/module/addsadministration/disable-adaccount)」を参照してください。

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Active Directory でユーザーのパスワードを 2 回リセットします。 「[Set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword)」を参照してください。

    > [!NOTE]
    > ユーザーのパスワードを 2 回変更する理由は、特にオンプレミスのパスワード レプリケーションで遅延が発生した場合に、Pass-the-Hash のリスクを軽減するためです。 このアカウントが侵害されていないと想定できる場合は、パスワードのリセットを 1 回だけにすることができます。

    > [!IMPORTANT] 
    > 次のコマンドレット内のサンプルのパスワードは使用しないでください。 パスワードは必ずランダムな文字列に変更してください。

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory の環境

Azure Active Directory の管理者として、PowerShell を開き、``Connect-AzureAD`` を実行して、次の操作を実行します。

1. Azure AD でユーザーを無効にします。 「[Set-AzureADUser](/powershell/module/azuread/Set-AzureADUser)」を参照してください。

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. ユーザーの Azure AD 更新トークンを取り消します。 「[Revoke-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)」を参照してください。

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. ユーザーのデバイスを無効にします。 「[Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice)」を参照してください。

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>省略可能な手順

- [Intune で管理されたアプリケーションから会社のデータをワイプする](/mem/intune/apps/apps-selective-wipe)。

- [会社所有のデバイスをワイプして、デバイスを出荷時の設定にリセットする](/mem/intune/remote-actions/devices-wipe)。

> [!NOTE]
> ワイプ後にデバイス上のデータを復元することはできません。

## <a name="when-access-is-revoked"></a>アクセスが取り消されたとき

管理者が上記の手順を実行すると、ユーザーは Azure Active Directory に関連付けられているすべてのアプリケーションの新しいトークンを取得できなくなります。 取り消してからユーザーがアクセスを失うまでの経過時間は、アプリケーションがアクセスを許可する方法によって異なります。

- **アクセス トークンを使用するアプリケーション** の場合、アクセス トークンの有効期限が切れると、ユーザーはアクセスを失います。

- **セッション トークンを使用するアプリケーション** では、トークンの有効期限が切れるとすぐに既存のセッションが終了します。 ユーザーの無効状態がアプリケーションに同期されている場合、そのアプリケーションはユーザーの既存のセッションを自動的に取り消すことができます (そのように構成されている場合)。  所要時間は、アプリケーションと Azure AD 間の同期の頻度によって異なります。

## <a name="next-steps"></a>次のステップ

- [Azure AD 管理者向けのセキュリティで保護されたアクセスプラクティス](../roles/security-planning.md)
- [ユーザー プロファイル情報を追加または更新する](../fundamentals/active-directory-users-profile-azure-portal.md)
