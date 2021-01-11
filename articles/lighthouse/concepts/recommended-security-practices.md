---
title: 推奨セキュリティ プラクティス
description: Azure Lighthouse を使用する場合、セキュリティとアクセス制御を考慮することが重要です。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: ddc4d2c69e7aacc49c5309bc407aecd9bc8e572f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163273"
---
# <a name="recommended-security-practices"></a>推奨セキュリティ プラクティス

[Azure Lighthouse](../overview.md) を使用する場合、セキュリティとアクセス制御を考慮することが重要です。 テナント内のユーザーは、顧客のサブスクリプションとリソース グループに直接アクセスできるようになるため、テナントのセキュリティを確保するための手順を実行する必要があります。 また許可の対象も、顧客のリソースを効果的に管理するうえで必要なアクセス権に限定することが必要です。 このトピックでは、そのために役立つ推奨事項を紹介しています。

> [!TIP]
> これらの推奨事項は、Azure Lighthouse を使用して[複数のテナントを管理するエンタープライズ](enterprise.md)にも適用されます。

## <a name="require-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication を必須とする

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (2 段階認証) には、複数の認証ステップを要求することで、攻撃者によるアカウントへのアクセスを阻止する効果があります。 管理テナント内のすべてのユーザー (委任された顧客のリソースにアクセスできるユーザーを含む) について、Multi-Factor Authentication を必須としてください。

顧客にも、そのテナントに Azure Multi-Factor Authentication の導入を求めることをお勧めします。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>最小限の特権の原則を使用してグループにアクセス許可を割り当てる

管理しやすくするため、顧客のリソースを管理するうえで必要なロールごとに Azure Active Directory (Azure AD) グループを使用します。 そうすることで、各ユーザーに直接アクセス許可を割り当てずに、必要に応じて個々のユーザーをグループに追加したり、グループから削除したりすることができます。

> [!IMPORTANT]
> Azure AD グループのアクセス許可を追加するためには、 **[グループの種類]** を **[Office 365]** ではなく、 **[セキュリティ]** にする必要があります。 このオプションは、グループの作成時に選択します。 詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

アクセス許可の体系を作成する際は、ユーザーがジョブの完了に必要なアクセス許可のみを持ち、不注意によるエラーの可能性が低くなるように、必ず最小限の特権の原則に従ってください。

たとえば、次のような体系を使用することが考えられます。

|グループ名  |Type  |principalId  |ロール定義  |ロール定義 ID  |
|---------|---------|---------|---------|---------|
|Architects     |ユーザー グループ         |\<principalId\>         |Contributor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|評価     |ユーザー グループ         |\<principalId\>         |Reader         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM Specialists     |ユーザー グループ         |\<principalId\>         |VM 共同作成者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|オートメーション     |サービス プリンシパル名 (SPN)         |\<principalId\>         |Contributor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

これらのグループを作成したら、必要に応じてユーザーを割り当てることができます。 追加するのは、本当にアクセスを必要とするユーザーに限定します。 グループ メンバーシップを定期的に見直し、メンバーに含めることへの妥当性や必要性を失ったユーザーがいれば削除してください。

[パブリック マネージド サービス オファーを通じて顧客をオンボード](../how-to/publish-managed-services-offers.md)すると、追加したすべてのグループ (またはユーザーあるいはサービス プリンシパル) は、そのプランを購入したすべての顧客に対して同じアクセス許可を持つことに注意してください。 顧客ごとに異なる担当グループを割り当てるには、各顧客に限定された個別のプライベート プランを公開するか、Azure Resource Manager テンプレートを使用して顧客を個別にオンボードする必要があります。 たとえば、アクセスがごく限られたパブリック プランを公開しておき、追加のアクセス権については、顧客と直接連携しながら、カスタマイズされた Azure リソーステンプレートを使用して、そのリソースをオンボードし、必要に応じて別途アクセス権を付与していくことが考えられます。

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication をデプロイ](../../active-directory/authentication/howto-mfa-getstarted.md)します。
- [テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)について学習します。
