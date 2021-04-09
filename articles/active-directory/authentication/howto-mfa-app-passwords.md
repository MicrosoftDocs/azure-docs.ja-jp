---
title: Azure AD Multi-Factor Authentication のアプリ パスワードを構成する - Azure Active Directory
description: Azure AD Multi-Factor Authentication でレガシ アプリケーションのアプリ パスワードを構成して使用する方法について説明します
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743107"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>アプリ パスワードを使用して、レガシ アプリケーションで Azure AD Multi-Factor Authentication を有効にして使用する

一部の古い非ブラウザー アプリ (Office 2010 以前や iOS 11 より前の Apple Mail など) では、認証プロセスの一時停止や中断が認識されません。 ユーザーが Azure AD Multi-Factor Authentication を有効にしていて、これらの古い非ブラウザー アプリのいずれかを使用しようとする場合、アプリによる認証が正常に行われないことがあります。 ユーザー アカウントに対して有効になっている Azure AD Multi-Factor Authentication を使用した安全な方法でこれらのアプリケーションを使用するには、アプリ パスワードを使用できます。 これらのアプリ パスワードは従来のパスワードを置き換えて、アプリが多要素認証をバイパスして正常に動作できるようにします。

Microsoft Office 2013 クライアント以降向けの最新の認証がサポートされています。 Office 2013 クライアント (Outlook を含む) は、最新の認証プロトコルをサポートしており、2 段階認証で機能するように有効にできます。 クライアントを有効にした場合、そのクライアントではアプリ パスワードは必要ありません。

この記事では、多要素認証プロンプトをサポートしていないレガシ アプリケーションで、アプリ パスワードを有効にして使用する方法について説明します。

>[!NOTE]
> アプリ パスワードは、条件付きアクセス ベースの多要素認証ポリシーと最新の認証では機能しません。

## <a name="overview-and-considerations"></a>概要と考慮事項

ユーザー アカウントが Azure AD Multi-Factor Authentication で有効にされると、追加の検証の要求によって、通常のサインイン プロンプトが中断されます。 一部の古いアプリケーションでは、サインイン プロセスのこの中断を認識しないため、認証が失敗します。 ユーザー アカウントのセキュリティを維持し、Azure AD Multi-Factor Authentication を有効のままにするには、ユーザーの通常のユーザー名とパスワードの代わりにアプリ パスワードを使用できます。 サインイン時にアプリ パスワードを使用すると、追加の検証プロンプトが表示されないため、認証は成功します。

アプリ パスワードは自動的に生成され、ユーザーが指定しません。 この自動的に生成されたパスワードの方が攻撃者から推測されづらく、より安全です。 アプリ パスワードはアプリケーションごとに 1 回入力するだけであるため、ユーザーはパスワードを追跡したり、毎回それらを入力したりする必要がありません。

アプリ パスワードを使用する場合は、次の考慮事項が適用されます。

* ユーザー 1 人あたりのアプリ パスワード数の上限は 40 個である。
* パスワードをキャッシュし、オンプレミス シナリオで使用しているアプリケーションでは、職場または学校アカウント以外で、アプリ パスワードが不明であるため、失敗する可能性があります。 このシナリオの例として、オンプレミスだが、アーカイブ メールはクラウド内にある Exchange メールがあります。 このシナリオでは、同じパスワードは機能しません。
* ユーザーのアカウントで Azure AD Multi-Factor Authentication を有効にすると、Outlook や Microsoft Skype for Business などのほとんどの非ブラウザー クライアントでアプリ パスワードを使用できます。 ただし、Windows PowerShell などの非ブラウザー アプリケーションからアプリ パスワードを使用して、管理操作を実行することはできません。 ユーザーが管理者アカウントを持っている場合でも、操作を実行することはできません。
    * PowerShell スクリプトを実行するには、サービス アカウントを強固なパスワードで作成します。そのアカウントで 2 段階認証を有効にしないでください。
* ユーザー アカウントが侵害された疑いがあり、アカウント パスワードを取り消すか、またはリセットした場合は、アプリ パスワードも更新する必要があります。 ユーザー アカウントのパスワードが取り消されるか、またはリセットされても、アプリ パスワードは自動的には取り消されません。 ユーザーは既存のアプリ パスワードを削除し、新しいアプリ パスワードを作成する必要があります。
   * 詳細については、「[[追加のセキュリティ確認] ページを使用してアプリ パスワードを作成および削除する](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)」を参照してください。

>[!WARNING]
> クライアントがオンプレミスの自動検出エンドポイントとクラウドの自動検出エンドポイントの両方と通信するハイブリッド環境では、アプリ パスワードは機能しません。 オンプレミスでの認証にはドメイン パスワードが必要です。 クラウドでの認証にはアプリ パスワードが必要です。

### <a name="app-password-names"></a>アプリ パスワード名

アプリ パスワードの名前は、それが使用されるデバイスを反映させるようにします。 Outlook、Word、Excel などのブラウザー以外のアプリケーションがあるラップトップでは、これらのアプリに対して **Laptop** という名前のアプリ パスワードを 1 つ作成します。 デスクトップ コンピューター上で実行される同じアプリケーションに対しては、**Desktop** という名前の別のアプリ パスワードを作成します。

アプリケーションごとに 1 つのアプリ パスワードではなく、デバイスごとに 1 つのアプリ パスワードを作成することをお勧めします。

## <a name="federated-or-single-sign-on-app-passwords"></a>フェデレーション (シングル サインオン) アプリ パスワード

Azure AD では、オンプレミスの Active Directory ドメイン サービス (AD DS) とのフェデレーション (シングル サインオン (SSO)) がサポートされています。 組織が Azure AD とフェデレーションされており、Azure AD Multi-factor Authentication を使用している場合は、次のアプリ パスワードの考慮事項が適用されます。

>[!NOTE]
> 次の点はフェデレーション (SSO) 顧客にのみ適用されます。

* アプリ パスワードは Azure AD によって検証されます。したがって、フェデレーションをバイパスします。 フェデレーションは、アプリ パスワードを設定するときにのみアクティブに使用されます。
* フェデレーション (SSO) ユーザーの場合、パッシブ フローとは異なり、ID プロバイダー (IdP) には接続されません。 アプリ パスワードは職場または学校アカウントに格納されます。 ユーザーが退職した場合、そのユーザーの情報は、**DirSync** を使用して、リアルタイムで職場または学校アカウントに送信されます。 アカウントの無効化/削除を同期させるには最大 3 時間かかる場合があり、Azure AD 内のアプリ パスワードの無効化/削除が遅れることがあります。
* オンプレミスのクライアント アクセス制御設定は、アプリ パスワード機能には適用されません。
* アプリ パスワード機能で使用できるオンプレミスの認証ログまたは監査機能はありません。

一部の高度なアーキテクチャでは、多要素認証をクライアントで使用するときに資格情報の組み合わせが必要になります。 これらの資格情報には、職場または学校アカウントのユーザー名とパスワード、およびアプリ パスワードを含めることができます。 要件は認証の実行方法によって異なります。 オンプレミスのインフラストラクチャに対して認証するクライアントの場合は、職場または学校アカウントのユーザー名が必要です。 Azure AD に対して認証するクライアントの場合は、アプリ パスワードが必要です。

たとえば、次のアーキテクチャがあるとします。

* Active Directory のオンプレミスのインスタンスが Azure AD とフェデレーションしている。
* Exchange Online を使用している。
* Skype for Business をオンプレミスで使用している。
* Azure AD Multi-Factor Authentication を使用している。

このシナリオでは、次の資格情報を使用します。

* Skype for Business にサインインする場合は、職場または学校アカウントのユーザー名とパスワードを使用します。
* Exchange にオンラインで接続している Outlook クライアントからアドレス帳にアクセスする場合は、アプリ パスワードを使用します。

## <a name="allow-users-to-create-app-passwords"></a>ユーザーがアプリ パスワードを作成できるようにする

既定では、ユーザーはアプリ パスワードを作成できません。 アプリ パスワード機能を有効にして、ユーザーがそれらを使えるようにする必要があります。 ユーザーがアプリ パスワードを作成できるようにするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を探して選択し、次に **[ユーザー]** を選択します。
3. *[ユーザー]* ウィンドウの上部を横切るナビゲーション バーから **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. **[サービス設定]** ページで、 **[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可する]** オプションを選択します。

    ![アプリ パスワードをユーザーに許可するための多要素認証サービス設定を示す Azure portal のスクリーンショット](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> ユーザーがアプリ パスワードを作成できないようにしても、既存のアプリ パスワードを引き続き使用できます。 ただし、この機能を無効にされると、ユーザーはこれらの既存のアプリ パスワードを管理したり削除したりできなくなります。
>
> アプリ パスワードの作成機能を無効にする場合は、[条件付きアクセス ポリシーを作成して、レガシ認証を使用できないようにする](../conditional-access/block-legacy-authentication.md)こともお勧めします。 この方法を使用すると、既存のアプリ パスワードは機能しなくなり、最新の認証方法が強制的に使用されます。

## <a name="create-an-app-password"></a>アプリケーション パスワードの作成

ユーザーが Azure AD Multi-Factor Authentication の初期登録を完了すると、登録プロセスの最後にアプリ パスワードを作成するオプションがあります。

ユーザーによるアプリ パスワードの作成は、登録後も可能です。 ユーザー向けの情報と詳細な手順については、[Azure AD Multi-Factor Authentication でのアプリ パスワードの概要](../user-help/multi-factor-authentication-end-user-app-passwords.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

ユーザーが Azure AD Multi-Factor Authentication に迅速に登録できるようにする方法の詳細については、[統合されたセキュリティ情報の登録の概要](concept-registration-mfa-sspr-combined.md)に関する記事を参照してください。
