---
title: "クラウドでの Azure MFA の概要 | Microsoft Docs"
description: "クラウドで Azure MFA を開始する方法について説明する Microsoft Azure Multi-Factor Authentication のページです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 79c6ef627d20be52b6067d1f6dfea2a53505c590
ms.lasthandoff: 03/18/2017


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>クラウドでの Azure Multi-Factor Authentication Server の概要
この記事では、クラウドで Azure Multi-Factor Authentication の使用を開始する方法について説明します。

> [!NOTE]
> 次のドキュメントでは、 **Azure クラシック ポータル**を使用してユーザーを有効にする方法について説明しています。 Office 365 ユーザー向けに Azure Multi-Factor Authentication を設定する方法についての情報をお探しの場合は、「[Office 365 ユーザー用の多要素認証を設定する](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)」を参照してください。

![MFA in the Cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>前提条件
[Azure サブスクリプションにサインアップする](https://azure.microsoft.com/pricing/free-trial/) - Azure サブスクリプションをまだ取得していない場合は、サインアップが必要です。 Azure MFA を開始して使ってみるだけの場合、試用版のサブスクリプションを使用できます。

## <a name="enable-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の有効化
ユーザーが Azure Multi-Factor Authentication を利用できるライセンスをお持ちであれば、Azure MFA を有効にするうえで必要な作業は特にありません。 ユーザーごとに&2; 段階認証の適用を開始できます。 Azure MFA を利用できるライセンスは、次の&3; 種類です。
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

この&3; 種類のライセンスのいずれもお持ちでない場合や、全ユーザーをカバーできるだけのライセンスがない場合でも、心配いりません。 手順が&1; つ増え、ディレクトリで[多要素認証プロバイダーを作成する](multi-factor-authentication-get-started-auth-provider.md)作業が加わるだけです。

## <a name="turn-on-two-step-verification-for-users"></a>ユーザーに対する&2; 段階認証をオンにする
あるユーザーに対して新たに&2; 段階認証を必須とするには、そのユーザーの状態を [無効] から [有効] に変更します。  ユーザーの状態の詳細については、「[Azure Multi-Factor Authentication におけるユーザーの状態](multi-factor-authentication-get-started-user-states.md)」を参照してください。

ユーザーの MFA を有効にするには、次の手順を使用します。

### <a name="to-turn-on-multi-factor-authentication"></a>多要素認証を有効にするには
1. [Azure クラシック ポータル](https://manage.windowsazure.com)に管理者としてサインインします。
2. 左側の **[Active Directory]**をクリックします。
3. [ディレクトリ] の下で、有効にするユーザーのディレクトリを選択します。
   ![Click Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. 上部の **[ユーザー]**をクリックします。
5. ページ下部の **[Multi-Factor Authentication の管理]**をクリックします。 新しいブラウザーのタブが開きます。
   ![Click Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. 2 段階認証を有効にするユーザーを探します。 上部でビューを変更することが必要になる場合があります。 状態が **[無効]** になっていることを確認します。
   ![ユーザーの有効化](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. ユーザーの名前の横にあるチェック ボックスを **オン** にします。
8. 右側の **[有効化]**をクリックします。
   ![Enable user](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. **[Multi-Factor Auth を有効にする]**をクリックします。
   ![Enable user](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. ユーザーの状態が **[無効]** から **[有効]** に変わります。
    ![Enable Users](./media/multi-factor-authentication-get-started-cloud/user.png)

有効にした後は、ユーザーにメールで通知することをお勧めします。 2 段階認証が有効になっているユーザーは、次回のサインイン時に、2 段階認証のためにアカウントの登録が必要であることを示すメッセージが表示されます。 このほか、ユーザーが&2; 段階認証の使用を開始した後には、ブラウザー以外のアプリからロックアウトされないように、アプリのパスワードを設定する必要があります。

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>PowerShell を使って&2; 段階認証を自動でオンにする
[Azure AD PowerShell](/powershell/azureps-cmdlets-docs) を使用して[状態](multi-factor-authentication-whats-next.md)を変更するには、次のようにします。  `$st.State` は、次のいずれかの状態と一致するように変更できます。

* 有効
* 適用
* 無効  

> [!IMPORTANT]
> ユーザーの状態を [無効] から [強制] に直接変更することはお勧めしていません。 ユーザーが MFA の登録を終えておらず、[アプリのパスワード](multi-factor-authentication-whats-next.md#app-passwords)を取得していない場合には、ブラウザーベースでないアプリが動作を停止してしまうからです。 ブラウザーベースでないアプリを使用しており、アプリのパスワードを必要とする場合は、状態を一度 [無効] から [有効] に変更することをお勧めします。 これにより、ユーザーが登録とアプリのパスワードの取得を行うことができるようになります。 パスワードの取得が終わったら、状態を [強制] に移行してください。

ユーザーを一括で有効にする方法の&1; つとして、PowerShell を使用します。 現在、Azure ポータルには一括有効化の機能がないため、各ユーザーを個別に選択する必要があります。 そのため、ユーザーの数が多いと作業量が膨大になることがあります。 以下のように PowerShell スクリプトを作成することで、ユーザーのリストをループ処理して各ユーザーを有効にすることができます。

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

たとえば次のようになります。

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


詳細については、「[Azure Multi-Factor Authentication におけるユーザーの状態](multi-factor-authentication-get-started-user-states.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
クラウド側の Azure Multi-Factor Authentication を設定したら、デプロイ側の構成とセットアップを行います。 詳細については、「[Azure Multi-Factor Authentication の構成](multi-factor-authentication-whats-next.md)」を参照してください。


