---
title: 'ベースライン ポリシー: 管理者に MFA を要求する - Azure Active Directory'
description: 管理者の多要素認証を要求する条件付きアクセス ポリシー
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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560951"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>ベースライン ポリシー:管理者に MFA を要求する (プレビュー)

特権アカウントにアクセスできるユーザーは、環境に無制限にアクセスできます。 これらのアカウントには権限があるので、特別な注意を払って対処する必要があります。 特権アカウントの保護を向上するための一般的な方法の 1 つは、特権アカウントがサインインに使用されるときに、強力な形式のアカウント検証を必須にすることです。 Azure Active Directory では、多要素認証 (MFA) を必須にすることで、アカウント検証を強力にすることができます。

**管理者の MFA を要求する (プレビュー)**   は、次のいずれかの特権管理者ロールがサインインするたびに MFA を必要とする[ベースライン ポリシー](concept-baseline-protection.md)です。

* 全体管理者
* SharePoint 管理者
* Exchange 管理者
* 条件付きアクセス管理者
* セキュリティ管理者
* ヘルプデスク管理者/パスワード管理者
* 課金管理者
* ユーザー管理者

管理者に MFA を要求するポリシーを有効にすると、上記の 9 つの管理者ロールは Authenticator アプリを使用して、MFA を登録する必要があります。 MFA の登録が完了すると、管理者はサインインするたびに MFA を実行する必要があります。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

**管理者に MFA を要求する (プレビュー)** ポリシーは、すべての重要な管理者に適用されます。したがって、デプロイが確実かつスムーズに行われるようにするための考慮事項がいくつかあります。 たとえば、MFA を実行できない、または実行すべきではないユーザーやサービス プリンシパルを Azure AD で特定する、ご自身の組織で使用されている先進認証に対応していないアプリケーションやクライアントを特定する、といった考慮事項です。

### <a name="legacy-protocols"></a>レガシ プロトコル

レガシ認証プロトコル (IMAP、SMTP、POP3 など) は、認証要求を行うためにメール クライアントによって使用されます。 これらのプロトコルは、MFA をサポートしていません。 Microsoft によって確認されているアカウントのセキュリティ侵害のほとんどでは、攻撃者がレガシ プロトコルを攻撃することによって MFA のバイパスを試みています。 管理アカウントにログインするときに確実に MFA を要求し、攻撃者が MFA をバイパスできないようにするために、このポリシーは、レガシ プロトコルから管理者アカウントに対して行われるすべての認証要求をブロックします。

> [!WARNING]
> このポリシーを有効にする前に、管理者がレガシ認証プロトコルを使用していないことを確認してください。 詳細については、[条件付きアクセスを使用して Azure AD へのレガシ認証をブロックする](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)方法に関するページを参照してください。

## <a name="enable-the-baseline-policy"></a>ベースライン ポリシーを有効にする

ポリシー **[ベースライン ポリシー: 管理者に MFA を要求する (プレビュー)]** は事前構成され、Azure portal の [条件付きアクセス] ブレードに移動すると上部に表示されます。

このポリシーを有効にして管理者を保護するには:

1.  **Azure portal**  にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[条件付きアクセス]** の順に移動します。
1. ポリシーの一覧で、 **[ベースライン ポリシー: 管理者に MFA を要求する (プレビュー)]** を選択します。
1. **[ポリシーを有効にする]** を **[ポリシーをすぐに使用する]** に設定します。
1.  **[保存]** をクリックします。

> [!WARNING]
> このポリシーがプレビュー段階であったときは、 **[将来、ポリシーを自動的に有効にする]** というオプションがありました。 ユーザーへの突然の影響を最小限に抑えるために、このオプションは削除しました。 このオプションを使用できたときに選択した場合、現在では **[ポリシーを使用しない]** が自動的に選択されています。 このベースライン ポリシーを使用する場合は、上の手順を参照して有効にしてください。

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

* [条件付きアクセス ベースライン保護ポリシー](concept-baseline-protection.md)
* [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory の条件付きアクセスとは](overview.md)
