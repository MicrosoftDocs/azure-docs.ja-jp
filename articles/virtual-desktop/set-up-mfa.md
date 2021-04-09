---
title: Windows Virtual Desktop 向け Azure 多要素認証の設定 - Azure
description: Windows Virtual Desktop でセキュリティを強化するために Azure 多要素認証を設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 00aba5d169a05eab25dcc63ca813955e71d09598
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092382"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>Windows Virtual Desktop 向け Azure 多要素認証の有効化

>[!IMPORTANT]
> Windows Virtual Desktop (クラシック) のドキュメントからこのページにアクセスしている場合は、終了した後に [Windows Virtual Desktop (クラシック) のドキュメント](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)に戻ったことを確認してください。

Windows Virtual Desktop 用の Windows クライアントは、Windows Virtual Desktop をローカル コンピューターに統合するための優れたオプションです。 ただし、Windows Virtual Desktop アカウントを Windows クライアントに構成する場合、自分自身とユーザーの安全を維持するために必要な特定の手段があります。

初めてサインインすると、クライアントによって、ユーザー名、パスワード、Azure 多要素認証が要求されます。 その後、次回のサインイン時に、クライアントで、Azure Active Directory (AD) エンタープライズ アプリケーションからのトークンが記憶されます。 セッション ホストの資格情報を求めるプロンプトで **[このアカウントを記憶する]** を選択した場合、ユーザーは、クライアントの再起動後に、資格情報を再入力しなくてもサインインできます。

資格情報を記憶させることは便利ですが、企業のシナリオや個人のデバイスでのデプロイのセキュリティを低下させる可能性もあります。 ユーザーを保護するには、クライアントが Azure 多要素認証の資格情報をより頻繁に要求し続けるようにできます。 この記事では、Windows Virtual Desktop の条件付きアクセス ポリシーを構成して、この設定を有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

使用を開始するために必要なものを次に示します。

- Azure Active Directory Premium P1 または P2 を含むライセンスをユーザーに割り当てます。
- ユーザーがグループ メンバーとして割り当てられている Azure Active Directory グループ。
- すべてのユーザーに対して Azure 多要素認証を有効にします。 その方法の詳細については、「[ユーザーに 2 段階認証を要求する方法](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)」を参照してください。

> [!NOTE]
> 次の設定は、[Windows Virtual Desktop Web クライアント](https://rdweb.wvd.microsoft.com/arm/webclient/index.html)にも適用されます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

ここでは、Windows Virtual Desktop に接続するときに多要素認証を必要とする条件付きアクセス ポリシーを作成する方法について説明します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
3. **[新しいポリシー]** を選択します。
4. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
5. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
6. **[Include]\(含める\)** で **[ユーザーとグループの選択]**  >  **[ユーザーとグループ]** を選択し、[事前準備](#prerequisites)の段階で作成したグループを選択します。
7. **[Done]** を選択します。
8. **[クラウド アプリまたはアクション]**  >  **[Include]\(含める\)** で、 **[アプリを選択]** を選択します。
9. 使用している Windows Virtual Desktop のバージョンに基づいて、次のいずれかのアプリを選択します。
   
   - Windows Virtual Desktop (クラシック) を使用している場合は、次のアプリを選択します。
       
       - **Windows Virtual Desktop** (アプリ ID 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Windows Virtual Desktop クライアント** (アプリ ID fa4345a4-a730-4230-84a8-7d9651b86739) これを使用すると、Web クライアントでポリシーを設定することができます
       
        その後、手順 11 に進みます。

   - Windows Virtual Desktop を使用している場合は、代わりに次のアプリを選択します。
       
       -  **Windows Virtual Desktop** (アプリ ID 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        その後、手順 10 に進みます。

   >[!IMPORTANT]
   > Windows Virtual Desktop Azure Resource Manager Provider (50e95039-b200-4007-bc97-8d5790743a63) という名前のアプリは選択しないでください。 このアプリは、ユーザー フィードを取得するためだけに使用され、多要素認証を持つことはできません。
   > 
   > Windows Virtual Desktop (クラシック) を使用している場合、条件付きアクセス ポリシーによってすべてのアクセスがブロックされ、Windows Virtual Desktop のアプリ ID のみが除外されている場合は、アプリ ID 9cdead84-a844-4324-93f2-b2e6bb768d07 をポリシーに追加することでこれを修正できます。 このアプリ ID を追加しないと、Windows Virtual Desktop (クラシック) リソースのフィード検出はブロックされます。

10. **[条件]**  >  **[クライアント アプリ]** に移動して、ポリシーを適用する場所を選択します。
    
    - ポリシーを Web クライアントに適用する場合は、 **[ブラウザー]** を選択します。
    - ポリシーを他のクライアントに適用する場合は、 **[モバイル アプリとデスクトップ クライアント]** を選択します。
    - ポリシーをすべてのクライアントに適用する場合は、両方のチェック ボックスをオンにします。
   
    > [!div class="mx-imgBorder"]
    > ![クライアント アプリ ページのスクリーンショット。 ユーザーが [モバイル アプリとデスクトップ クライアント] チェック ボックスをオンにしました。](media/select-apply.png)

11. アプリを選択したら、 **[選択]** を選び、 **[完了]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[クラウド アプリまたはアクション] ページのスクリーンショット。 Windows Virtual Desktop および Windows Virtual Desktop Client アプリが赤色で強調表示されています。](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >選択するアプリのアプリ ID を検索するには、 **[エンタープライズ アプリケーション]** に移動し、[アプリケーションの種類] ドロップダウン メニューから **[Microsoft アプリケーション]** を選択します。

12. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
13. **[アクセス制御]**  >  **[セッション]** で、 **[サインインの頻度]** を選択し、プロンプトの間隔として設定する時間の値を設定してから **[選択]** を選択します。 たとえば、値を **1** に設定し、単位を **[時間]** に設定した場合、最終接続の 1 時間後に接続が起動された場合に多要素認証が必要になります。
14. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
15. **[作成]** を選択して、ポリシーを有効化します。

>[!NOTE]
>Web クライアントを使用してブラウザーから Windows Virtual Desktop にサインインすると、ログにクライアント アプリ ID が a85cf173-4192-42f8-81fa-777a763e6e2c (Windows Virtual Desktop クライアント) として表示されます。 これは、クライアント アプリが、条件付きアクセス ポリシーが設定されたサーバー アプリ ID に内部的にリンクされているためです。 

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス ポリシーの詳細について確認する](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [ユーザー サインインの頻度の詳細について確認する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
