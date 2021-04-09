---
title: オンプレミスの Azure AD パスワード保護を有効にする
description: オンプレミスの Active Directory Domain Services 環境での Azure AD パスワード保護を有効にする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741781"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>オンプレミスの Azure Active Directory パスワード保護を有効にする

ユーザーは多くの場合、学校、スポーツ チーム、有名人などのありふれたローカル単語を使用してパスワードを作成します。 これらのパスワードは簡単に推測できるため、辞書ベースの攻撃に対しては脆弱です。 組織に強力なパスワードを適用するため、Azure Active Directory (Azure AD) パスワード保護では、グローバルかつカスタムの禁止パスワードの一覧が提供されます。 この禁止パスワードの一覧に一致するものがある場合、パスワードの変更要求はエラーとなります。

オンプレミスの Active Directory Domain Services (AD DS) 環境を保護するために、Azure AD パスワード保護をインストールして、オンプレミスの DC と連携するように構成することができます。 この記事では、オンプレミス環境での Azure AD パスワード保護を有効にする方法を示します。

オンプレミス環境での Azure AD パスワード保護のしくみの詳細については、[Windows Server Active Directory に Azure AD パスワード保護を適用する方法](concept-password-ban-bad-on-premises.md)に関する記事を参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事では、オンプレミス環境での Azure AD パスワード保護を有効にする方法を示します。 この記事を完了する前に、オンプレミスの AD DS 環境に [Azure AD パスワード保護プロキシ サービスと DC エージェントをインストールして登録します](howto-password-ban-bad-on-premises-deploy.md)。

## <a name="enable-on-premises-password-protection"></a>オンプレミスのパスワード保護を有効にする

1. [Azure portal](https://portal.azure.com) にサインインし、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[認証方法]**  >  **[パスワード保護]** の順に選択します。
1. **[Windows Server Active Directory のパスワード保護を有効にする]** オプションを *[はい]* に設定します。

    この設定が *[いいえ]* に設定されている場合、デプロイされたすべての Azure AD パスワード保護 DC エージェントが休止モードに入り、すべてのパスワードがそのまま受け入れられます。 検証アクティビティは実行されず、また、監査イベントは生成されません。

1. 最初に **[モード]** を "*監査*" に設定しておくことをお勧めします。 機能と組織内のユーザーへの影響を把握し終えたら、 **[モード]** を "*強制*" に切り替えてかまいません。 詳細については、次の「[操作のモード](#modes-of-operation)」のセクションを参照してください。
1. 準備ができたら、 **[保存]** を選択します。

    [![Azure portal の [認証方法] でオンプレミスのパスワード保護を有効にする](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>操作のモード

オンプレミスの Azure AD パスワード保護を有効にすると、"*監査*" モードまたは "*強制*" モードのどちらかを使用できます。 初期のデプロイとテストは常に監査モードで開始することをお勧めします。 イベント ログ上の項目を監視して、"*強制*" モードが有効になった後で、既存の運用プロセスが影響を受けるかどうかを予測する必要があります。

### <a name="audit-mode"></a>監査モード

"*監査*" モードは、"what-if" モードでソフトウェアを実行する方法として用意されています。 各 Azure AD パスワード保護 DC エージェント サービスでは、受信されたパスワードが現在アクティブなポリシーに従って評価されます。

現在のポリシーが監査モードに構成されている場合、"不正な" パスワードは、イベント ログ メッセージが生成される原因となりますが、処理されて更新されます。 この動作は、監査モードと強制モード間での唯一の相違点です。 他のすべての操作は、同じように実行されます。

### <a name="enforced-mode"></a>強制モード

"*強制*" モードは最終構成として用意されています。 監査モードの場合と同様に、各 Azure AD パスワード保護 DC エージェント サービスでは、受信されたパスワードが現在アクティブなポリシーに従って評価されます。 ただし、強制モードが有効になっている場合は、ポリシーに従って安全でないと見なされたパスワードは拒否されます。

強制モードで Azure AD パスワード保護 DC エージェントによってパスワードが拒否された場合、エンド ユーザーには、従来のオンプレミス パスワードの複雑さの適用によってパスワードが拒否された場合とほぼ同じエラーが表示されます。 たとえば、Windows のログオンまたはパスワード変更の画面で、次の従来のエラー メッセージがユーザーに表示されることがあります。

*"Unable to update the password.The value provided for the new password does not meet the length, complexity, or history requirements of the domain." (パスワードを更新できません。新しいパスワードに対して指定された値がドメインの長さ、複雑さ、または履歴要件を満たしていません。)*

このメッセージは、いくつかの考えられる結果の一例にすぎません。 具体的なエラー メッセージは、実際のソフトウェアや、安全でないパスワードの設定を試行するシナリオによって異なる可能性があります。

影響を受けたエンド ユーザーは、IT スタッフと協力して、新しい要件を理解し、安全なパスワードを選択できるようにする必要があります。

> [!NOTE]
> Azure AD のパスワード保護では、脆弱なパスワードが拒否されたときに、クライアント コンピューターによって表示される特定のエラー メッセージの制御は行われません。

## <a name="next-steps"></a>次のステップ

組織の禁止されたパスワードの一覧をカスタマイズするには、[Azure AD パスワード保護でのカスタムの禁止パスワードの構成](tutorial-configure-custom-password-protection.md)に関する記事を参照してください。

オンプレミスのイベントを監視するには、[オンプレミスの Azure AD パスワード保護の監視](howto-password-ban-bad-on-premises-monitor.md)に関する記事を参照してください。
