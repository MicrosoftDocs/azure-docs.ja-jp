---
title: 条件付きアクセス - すべてのユーザーに対して MFA を必須にする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、すべてのユーザーに対して多要素認証の実行を必須にします
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994759"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>条件付きアクセス:すべてのユーザーに対して MFA を必須にする

Microsoft の ID セキュリティ責任者、Alex Weinert は彼のブログ投稿「[Your Pa$$word doesn't matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)」で次のように言っています。

> 「重要なのはパスワードではなく、MFA です!」 私たちの研究によると、MFA を使用すると、アカウントは 99.9% 以上侵害されにくくなります。

この記事のガイダンスは、組織が環境に適したバランスの MFA ポリシーを作成する際に役立ちます。

## <a name="user-exclusions"></a>ユーザーの除外

条件付きアクセス ポリシーは強力なツールであり、以下のアカウントはポリシーから除外することをお勧めします。

* **緊急アクセス用** アカウントまたは **非常用** アカウント。テナント全体でアカウントがロックアウトされるのを防ぎます。 発生する可能性は低いシナリオですが、すべての管理者がテナントからロックアウトされた場合に、ご自身の緊急アクセス用管理アカウントを使用してテナントにログインし、アクセスを復旧する手順を実行できます。
   * 詳細については、「[Azure AD で緊急アクセス用アカウントを管理する](../roles/security-emergency-access.md)」を参照してください。
* **サービス アカウント** と **サービス プリンシパル** (Azure AD Connect 同期アカウントなど)。 サービス アカウントは、特定のユーザーに関連付けられていない非対話型のアカウントです。 これらは通常、アプリケーションへのプログラムによるアクセスを可能にするバックエンド サービスによって使用されますが、管理目的でシステムにサインインする場合にも使用されます。 プログラムでは MFA を完了できないため、このようなサービス アカウントは対象外とする必要があります。 サービス プリンシパルによって行われた呼び出しは、条件付きアクセスによってブロックされることはありません。
   * 組織のスクリプトまたはコードでこれらのアカウントが使用されている場合は、それを[マネージド ID](../managed-identities-azure-resources/overview.md) に置き換えることを検討してください。 これらの特定のアカウントは、一時的な回避策として、ベースライン ポリシーの対象外にすることができます。

## <a name="application-exclusions"></a>アプリケーションの除外

組織には、使用中のクラウド アプリケーションが多数あります。 これらのアプリケーションすべてで、同等のセキュリティが必要なわけではありません。 たとえば、給与と勤怠のアプリケーションには MFA が必要でも、カフェのアプリケーションでは必要ない可能性があります。 管理者は、ポリシーから特定のアプリケーションを除外することを選択できます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

次の手順では、すべてのユーザーに対して多要素認証の実行を必須にする条件付きアクセス ポリシーを作成します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[すべてのクラウド アプリ]** を選択します。
   1. **[除外]** で、多要素認証を必要としないアプリケーションを選択します。
1. **[条件]**  >  **[クライアント アプリ (プレビュー)]** の **[このポリシーを適用するクライアント アプリを選択します]** で、すべてを既定値が選択された状態のままにして、 **[完了]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

### <a name="named-locations"></a>ネームド ロケーション

組織では、条件付きアクセス ポリシーに「**ネームド ロケーション**」と呼ばれる既知のネットワークの場所を組み込むことができます。 こうしたネームド ロケーションには、メイン オフィスの場所のような信頼できる IPv4 ネットワークを含めることができます。 ネームド ロケーションの構成の詳細については、「[Azure Active Directory 条件付きアクセスの場所の条件の概要](location-condition.md)」の記事を参照してください。

上記のポリシーの例では、組織は自社のネットワークからクラウド アプリにアクセスする場合に多要素認証を必要としないことを選択できます。 この場合、次の構成をポリシーに追加できます。

1. **[割り当て]** で、 **[条件]**  >  **[場所]** を選択します。
   1. **[はい]** を構成します。
   1. **[任意の場所]** を含めます。
   1. **[すべての信頼できる場所]** を除外します。
   1. **[Done]** を選択します。
1. **[Done]** を選択します。
1. ポリシーの変更を **保存** します。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-insights-reporting.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
