---
title: ユーザー インターフェイスをカスタマイズする
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションのユーザー インターフェイスをカスタマイズする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056824"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 内のユーザー インターフェイスをカスタマイズする

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) に表示されるユーザー インターフェイスを顧客に合わせてブランド化したりカスタマイズすることは、アプリケーション内でシームレスなユーザー エクスペリエンスを提供するのに役立ちます。 そのような操作性には、サインアップ、サインイン、プロファイル編集、パスワード リセットが含まれます。 この記事では、ページ テンプレートを使用した Azure AD B2C ページと、会社のブランドをカスタマイズします。 

> [!TIP]
> ページ テンプレート、バナー ロゴ、背景画像、背景色以外のユーザー フロー ページのその他の側面をカスタマイズするには、「[HTML テンプレートを使用して UI をカスタマイズする](customize-ui-with-html.md)」方法をご覧ください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>概要

Azure AD B2C にはいくつかの組み込みテンプレートが用意されています。これらを使用すると、ユーザー エクスペリエンスのページにプロフェッショナルな外観を提供できます。 これらのページ テンプレートは、[会社のブランド](#company-branding)機能を使用して、独自のカスタマイズの開始点としても使用できます。

### <a name="ocean-blue"></a>オーシャン ブルー

サインアップ サインイン ページ上に表示される、オーシャン ブルー テンプレートの例:

![オーシャン ブルー テンプレートのスクリーンショット](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>スレート グレー

サインアップ サインイン ページ上に表示される、スレート グレー テンプレートの例:

![スレート グレー テンプレートのスクリーンショット](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>クラシック

サインアップ サインイン ページ上に表示される、クラシック テンプレートの例:

![クラシック テンプレートのスクリーンショット](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>会社のブランド

Azure Active Directory [[会社のブランド]](../active-directory/fundamentals/customize-branding.md) 機能を使用して、バナー ロゴ、背景画像、背景色で Azure AD B2C ページをカスタマイズできます。 会社のブランドには、サインアップ、サインイン、プロファイル編集、パスワード リセットが含まれます。 

次の例に、オーシャン ブルー テンプレートを使用し、カスタム ロゴと背景画像を備えた *サインアップとサインイン* のページを示します。

![Azure AD B2C によって提供されるブランド化されたサインアップ/サインイン ページ](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>ページ テンプレートを選択する

::: zone pivot="b2c-user-flow"

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. カスタマイズするユーザー フローを選択します。
1. 左側のメニューの **[カスタマイズ]** で、 **[ページ レイアウト]** を選択し、 **[テンプレート]** を選択します。
    ![Azure portal のユーザー フロー ページのテンプレート選択ドロップダウン](./media/customize-ui/select-page-template.png)

テンプレートを選択すると、選択したテンプレートがユーザー フロー内のすべてのページに適用されます。 各ページの URI は、 **[カスタム ページ URI]** フィールドに表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

ページ テンプレートを選択するには、[コンテンツ定義](contentdefinitions.md)の `LoadUri` 要素を設定します。 次の例は、コンテンツ定義識別子と、対応する `LoadUri` を示しています。 

オーシャン ブルー:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

スレート グレー:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

クラシック: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>会社のブランドの構成

ユーザー フロー ページをカスタマイズするには、まず Azure Active Directory で会社のブランドを構成し、次に Azure AD B2C のユーザー フローでこれを有効にします。

まず、 **[会社のブランド]** でバナー ロゴ、背景画像、および背景色を設定します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[管理]** から **[会社のブランド]** を選択します。
1. 「[組織の Azure Active Directory のサインイン ページにブランドを追加する](../active-directory/fundamentals/customize-branding.md)」の手順に従います。

Azure AD B2C で会社のブランドを構成する際は、次の点に注意してください。

* 現在、Azure AD B2C の会社のブランドは、**背景画像**、**バナー ロゴ**、**背景色** のカスタマイズに制限されています。 **[詳細設定]** などの会社のブランド ペインのその他のプロパティは *サポートされていません*。
* ユーザー フロー ページでは、背景画像が読み込まれる前に背景色が表示されます。 よりスムーズな読み込みエクスペリエンスのため、背景画像の色とほぼ同じ背景色を選択することをお勧めします。
* バナー ロゴは、ユーザーがサインアップ ユーザー フローを開始するときに、ユーザーに送信された確認メールに表示されます。



## <a name="enable-company-branding-in-user-flow-pages"></a>ユーザー フロー ページでの会社のブランド化の有効化

::: zone pivot="b2c-user-flow"

会社のブランドを構成したら、それをユーザー フローで有効にします。

1. Azure portal の左側のメニューで、 **[Azure AD B2C]** を選択します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します。
1. 会社のブランド化を有効にするユーザー フローを選択します。 会社のブランド化は、標準の "*サインイン*" および標準の "*プロファイル編集*" ユーザー フローの種類では **サポートされていません**。
1. **[カスタマイズ]** で、 **[ページ レイアウト]** を選択して、ブランド化するページを選択します。 たとえば、 **[統合されたサインアップまたはサインイン ページ]** を選択します。
1. **[ページ レイアウト バージョン (プレビュー)]** では、バージョン **1.2.0** 以上を選択します。
1. **[保存]** を選択します。

ユーザー フローのすべてのページをブランド化する場合は、ユーザー フローのページ レイアウトごとにページ レイアウト バージョンを設定します。

![Azure portal での Azure AD B2C のページ レイアウトの選択](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

会社のブランドを構成したら、それをカスタム ポリシーで有効にします。 カスタム ポリシーのコンテンツ定義の "*すべて*" に対して、ページ `contract` バージョンを使用して [ページ レイアウト バージョン](contentdefinitions.md#migrating-to-page-layout)を構成します。 値の形式には、次のように `contract` という語が含まれている必要があります: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 古い **DataUri** 値を使用するカスタム ポリシーでページ レイアウトを指定するには、次の操作を行います。 詳細については、ページ バージョンで[ページ レイアウトに移行する](contentdefinitions.md#migrating-to-page-layout)方法について説明します。

次の例は、コンテンツ定義と、それに対応するページ コントラクト、および *オーシャン ブルー* ページ テンプレートを示しています。 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>次のステップ

[Azure Active Directory B2C でのアプリケーションのユーザー インターフェイスのカスタマイズ](customize-ui-with-html.md)に関する記事を参照して、アプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細を確認します。
