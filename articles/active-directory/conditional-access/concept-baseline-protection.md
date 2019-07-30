---
title: 条件付きアクセス ベースライン保護ポリシー - Azure Active Directory
description: 組織を一般的な攻撃から保護する条件付きアクセス ベースライン ポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970fdaba1870097e253b51c70e523e399bc88dfc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440795"
---
# <a name="what-are-baseline-policies"></a>ベースライン ポリシーとは?

ベースライン ポリシーは、多くの一般的な攻撃から組織を保護するのに役立つ一連の定義済みポリシーです。 これらの一般的な攻撃としては、パスワード スプレー、リプレイ、およびフィッシングなどが考えられます。 ベースライン ポリシーは、Azure AD のすべてのエディションで使用できます。 この数年間で ID ベースの攻撃が増えているため、Microsoft はこれらのベースライン保護ポリシーをすべてのユーザーが利用できるようにしています。 これらの 4 つのポリシーの目標は、すべての組織が追加の費用なしでベースライン レベルのセキュリティを確実に有効にできるようにすることです。  

カスタマイズされた条件付きアクセス ポリシーを管理するには、Azure AD Premium ライセンスが必要です。

## <a name="baseline-policies"></a>ベースライン ポリシー

![Azure portal での条件付きアクセス ベースライン ポリシー](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

組織で有効にすることができるベースライン ポリシーには次の 4 つがあります。

* [管理者に MFA を要求する (プレビュー)](howto-baseline-protect-administrators.md)
* [エンド ユーザーの保護 (プレビュー)](howto-baseline-protect-end-users.md)
* [レガシ認証をブロックする (プレビュー)](howto-baseline-protect-legacy-auth.md)
* [サービス管理のために MFA を要求する (プレビュー)](howto-baseline-protect-azure.md)

この 4 つのポリシーはすべて、POP、IMAP、より以前の Office デスクトップ クライアントなどのレガシ認証フローに影響を与えます。

### <a name="require-mfa-for-admins-preview"></a>管理者に MFA を要求する (プレビュー)

管理者アカウントには権限とアクセス権があるため、特別な注意を払って対処する必要があります。 特権アカウントの保護を向上するための一般的な方法の 1 つは、特権アカウントがサインインに使用されるときに、強力な形式のアカウント検証を必須にすることです。 Azure Active Directory では、Azure Multi-Factor Authentication への登録とその使用を管理者に要求することで、アカウント検証を強力にすることができます。

[管理者に MFA を要求する (プレビュー)](howto-baseline-protect-administrators.md)  は、最も高い特権を持つ Azure AD ロールであると見なされている次のディレクトリ ロールに対して多要素認証 (MFA) を要求するベースライン ポリシーです。

* 全体管理者
* SharePoint 管理者
* Exchange 管理者
* 条件付きアクセス管理者
* セキュリティ管理者
* ヘルプデスク管理者/パスワード管理者
* 課金管理者
* ユーザー管理者

これらのアカウントがスクリプトまたはコードで使用されている組織の場合は、 [マネージド ID](../managed-identities-azure-resources/overview.md) に置き換えることを検討してください。

### <a name="end-user-protection-preview"></a>エンド ユーザーの保護 (プレビュー)

高い特権を持つ管理者だけが攻撃の標的になるわけではありません。 悪意のあるアクターは通常のユーザーをターゲットにする傾向があります。 これらの悪意のあるアクターは、アクセス権を取得した後、元々のアカウント所有者に代わって機密性の高い情報へのアクセスを要求したり、ディレクトリ全体をダウンロードして組織全体にフィッシング攻撃を仕掛けたりする可能性があります。 すべてのユーザーを対象にした保護を向上するための一般的な方法の 1 つは、危険なサインインが検出されたときに、より強力な形式のアカウント検証を要求することです。

**エンド ユーザーの保護 (プレビュー)** は、ディレクトリ内のすべてのユーザーを保護するベースライン ポリシーです。 このポリシーを有効にすると、すべてのユーザーが 14 日以内に Azure Multi-Factor Authentication に登録することを求められます。 登録されると、ユーザーはリスクの高いサインインの試行中にのみ、MFA を求められます。 侵害されたユーザー アカウントは、パスワードがリセットされてリスクがなくなるまでブロックされます。

### <a name="block-legacy-authentication-preview"></a>レガシ認証をブロックする (プレビュー)

レガシ認証プロトコル (例: IMAP、SMTP、POP3) は、より古いメール クライアントで、認証のために通常使用されているプロトコルです。 レガシ プロトコルでは、多要素認証はサポートされていません。 ご利用のディレクトリに対して多要素認証を要求するポリシーをお持ちの場合でも、悪意のあるアクターはこれらのレガシ プロトコルのいずれかを使用して多要素認証をバイパスすることができます。

レガシ プロトコルによって行われる悪意のある認証要求からアカウントを保護するための最善の方法は、それらをブロックすることです。

**レガシ認証をブロックする (プレビュー)** ベースライン ポリシーでは、レガシ プロトコルを使用して行われる認証要求がブロックされます。 すべてのユーザーを正常にサインインさせるには、先進認証を使用する必要があります。 その他のベースライン ポリシーと組み合わせて使用すれば、レガシ プロトコルから出される要求はブロックされます。 さらに、すべてのユーザーは必要に応じて MFA を要求されます。 このポリシーで、Exchange ActiveSync はブロックされません。

### <a name="require-mfa-for-service-management-preview"></a>サービス管理のために MFA を要求する (プレビュー)

組織はさまざまな Azure サービスを使用すると共に、それらを Azure Resource Manager ベースの次のようなツールで管理します。

* Azure ポータル
* Azure PowerShell
* Azure CLI

これらのツールのいずれかを使用してリソースを管理することは、高い特権が必要なアクションです。 これらのツールでは、サービス設定やサブスクリプションの課金など、サブスクリプション全体の構成を変更できます。

特権を必要とするアクションを保護するために、この**サービス管理のために MFA を要求する (プレビュー)** ポリシーでは、Azure portal、Azure PowerShell、または Azure CLI にアクセスするどのユーザーに対しても多要素認証を要求します。

## <a name="enable-a-baseline-policy"></a>ベースライン ポリシーを有効にする

ベースライン ポリシーを有効にするには:

1.  **Azure portal**  にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[条件付きアクセス]** の順に移動します。
1. ポリシーの一覧では、有効にしたいベースライン ポリシーを選択します。
1. **[ポリシーを有効にする]** を **[オン]** に設定します。
1. [保存] をクリックします。

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

* [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory の条件付きアクセスとは](overview.md)
* [管理者に MFA を要求する (プレビュー)](howto-baseline-protect-administrators.md)
* [エンド ユーザーの保護 (プレビュー)](howto-baseline-protect-end-users.md)
* [レガシ認証をブロックする (プレビュー)](howto-baseline-protect-legacy-auth.md)
* [サービス管理のために MFA を要求する (プレビュー)](howto-baseline-protect-azure.md)
