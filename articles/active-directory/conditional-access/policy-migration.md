---
title: Azure Active Directory 条件付きアクセスのポリシー移行の概要 | Microsoft Docs
description: Azure Portal でクラシック ポリシーを移行するために知っておく必要があることについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7464546a78e1b54cdea3bd6dd66656f5b189bc02
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506808"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件付きアクセスのポリシー移行の概要 

[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)は、承認されたユーザーによるクラウド アプリへのアクセスを制御できるようにする、Azure Active Directory (Azure AD) の機能です。 目的は変わりませんが、新しい Azure portal のリリースにより、条件付きアクセスのしくみが大幅に改善されました。

次の理由から、Azure Portal で作成していないポリシーの移行を検討します。

- 以前は処理できなかったシナリオに対処できるようになった可能性があります。
- ポリシーを統合することで、管理する必要のあるポリシーの数を減らすことができます。   
- 一元的な場所ですべての条件付きアクセス ポリシーを管理することができます。
- Azure クラシック ポータルは廃止されます。   

この記事では、既存の条件付きアクセス ポリシーを新しいフレームワークに移行するために知っておく必要があることについて説明します。
 
## <a name="classic-policies"></a>クラシック ポリシー

[Azure portal](https://portal.azure.com) では、[[条件付きアクセス - ポリシー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) ページが条件付きアクセス ポリシーのエントリ ポイントです。 ただし、環境によっては、このページを使用して作成したのではない条件付きアクセス ポリシーがある場合もあります。 これらのポリシーは*クラシック ポリシー*と呼ばれます。 クラシック ポリシーは、次の場所で作成した条件付きアクセス ポリシーです。

- Azure クラシック ポータル
- Intune クラシック ポータル
- Intune App Protection ポータル

**[条件付きアクセス]** ページでは、 **[管理]** セクションの [ **[クラシック ポリシー (プレビュー)]** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) をクリックして、クラシック ポリシーにアクセスできます。 

![Azure Active Directory](./media/policy-migration/71.png)

**[クラシック ポリシー]** ビューには以下を行うオプションがあります。

- クラシック ポリシーをフィルター処理します。
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- クラシック ポリシーを無効にします。

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- クラシック ポリシーの設定をレビューします (また、無効にします)。

   ![Azure Active Directory](./media/policy-migration/74.png)

クラシック ポリシーを無効にした場合、この手順を元に戻すことはできません。 このため、クラシック ポリシーのグループ メンバーシップは **[詳細]** ビューを使用して変更できます。 

![Azure Active Directory](./media/policy-migration/75.png)

選択したグループを変更するか、特定のグループを除外すると、含まれるすべてのユーザーとグループに対してポリシーを無効にする前に、少数のテスト ユーザーに対して無効にしたクラシック ポリシーの効果をテストできます。 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD 条件付きアクセス ポリシー

Azure portal の条件付きアクセスでは、すべてのポリシーを一元的な場所で管理することができます。 条件付きアクセスの実装方法が変更されたため、クラシック ポリシーを移行する前に基本的な概念をよく理解しておく必要があります。

参照:

- 「[Azure Active Directory の条件付きアクセスの概要](../active-directory-conditional-access-azure-portal.md)」では、基本的な概念と用語について確認します。
- 「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」では、組織内での条件付きアクセスの展開に関するガイダンスを取得します。
- 「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」では、Azure portal のユーザー インターフェイスについて理解を深めます。
 
## <a name="migration-considerations"></a>移行に関する考慮事項

この記事では、Azure AD の条件付きアクセス ポリシーを "*新しいポリシー*" とも呼びます。
クラシック ポリシーは、無効にするか削除するまで新しいポリシーと並行して動作を続けます。 

ポリシー統合のコンテキストでは次の側面が重要です。

- クラシック ポリシーは特定のクラウド アプリに関連付けられていますが、新しいポリシーで必要な場合はクラウド アプリをいくつでも選択できます。
- クラウド アプリのクラシック ポリシーと新しいポリシーのコントロールでは、すべてのコントロール (*AND*) を満たす必要があります。 
- 新しいポリシーでは、次の操作を実行できます。
   - シナリオで必要な場合に、複数の条件を結合します。 
   - 複数の許可要件をアクセス制御として選び、それらを論理 *OR* (選択したコントロールのいずれかが必要) または論理 *AND* (すべての選択したコントロールが必要) で結合します。

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

**Exchange Active Sync** をクライアント アプリの条件として含む **Office 365 Exchange online** のクラシック ポリシーを移行する場合は、1 つの新しいポリシーに統合できないことがあります。 

たとえば、すべてのクライアント アプリの種類をサポートしない場合などです。 **Exchange Active Sync** をクライアント アプリの条件として持つ新しいポリシーでは、他のクライアント アプリを選択できません。

![Azure Active Directory](./media/policy-migration/64.png)

クラシック ポリシーに複数の条件が含まれている場合も、1 つの新しいポリシーに統合できません。 **Exchange Active Sync** がクライアント アプリの条件として構成されている新しいポリシーでは、他の条件はサポートされません。   

![Azure Active Directory](./media/policy-migration/08.png)

**Exchange Active Sync** がクライアント アプリの条件として構成されている新しいポリシーがある場合は、他のすべての条件が構成されていないことを確認する必要があります。 

![Azure Active Directory](./media/policy-migration/16.png)
 
**Exchange Active Sync** をクライアント アプリの条件として含む Office 365 Exchange Online の[アプリ ベース](technical-reference.md#approved-client-app-requirement)のクラシック ポリシーは、**サポートされている**[デバイス プラットフォーム](technical-reference.md#device-platform-condition)と**サポートされていない**デバイス プラットフォームに対応できます。 関連する新しいポリシーで個々のデバイス プラットフォームを構成することはできませんが、サポートを[サポートされているデバイス プラットフォーム](technical-reference.md#device-platform-condition)のみに制限できます。 

![Azure Active Directory](./media/policy-migration/65.png)

以下がある場合は、**Exchange Active Sync** をクライアント アプリの条件として含む複数のクラシック ポリシーを統合できます。

- 条件としての **Exchange Active Sync** のみ 
- アクセス権の付与を構成するための複数の要件

1 つの一般的なシナリオは、以下の統合です。

- Azure クラシック ポータルからのデバイス ベースのクラシック ポリシー 
- Intune App Protection ポータルのアプリ ベースのクラシック ポリシー 
 
この場合、選択した両方の要件を持つ 1 つの新しいポリシーにクラシック ポリシーを統合できます。

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>デバイス プラットフォーム

[アプリ ベースのコントロール](technical-reference.md#approved-client-app-requirement)を持つクラシック ポリシーは、iOS と Android で[デバイス プラットフォーム条件](technical-reference.md#device-platform-condition)として事前に構成されています。 

新しいポリシーでは、個別にサポートする[デバイス プラットフォーム](technical-reference.md#device-platform-condition)を選ぶ必要があります。

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>次の手順

- 条件付きアクセス ポリシーの構成方法を把握するには、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。 
