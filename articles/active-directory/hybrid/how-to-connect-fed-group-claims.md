---
title: Azure Active Directory を使ってアプリケーションに対するグループ要求を構成する | Microsoft Docs
description: Azure AD によって、使用するグループ要求を構成する方法を示します。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: bef5942707c1ded22ba82bdb0d945b9fdb23fffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349352"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Azure Active Directory を使ってアプリケーションに対するグループ要求を構成する

Azure Active Directory では、アプリケーション内で使用するユーザー グループのメンバーシップ情報をトークンで提供できます。  主なパターンとして、次の 2 つがサポートされています。

- Azure Active Directory オブジェクト識別子 (OID) 属性によって識別されるグループ
- Active Directory (AD) の同期グループおよびユーザーに対応する sAMAccountName または GroupSID 属性によって識別されるグループ

> [!IMPORTANT]
> この機能については、留意すべき注意事項が複数あります。
>
>- オンプレミスから同期される sAMAccountName およびセキュリティ識別子 (SID) の属性の使用に関するサポートは、AD FS およびその他の ID プロバイダーからの既存のアプリケーションの移動を有効にするように設計されています。 Azure AD 内で管理されるグループには、これらの要求を発行するために必要な属性は含まれていません。
>- 大規模な組織では、ユーザーがメンバーになっているグループ数が、Azure Active Directory によってトークンに追加される制限を超える可能性があります。 SAML トークンの場合は 150 グループ、JWT の場合は 200 グループです。 これは、予期しない結果につながります。 ユーザーが多数のグループ メンバーシップを保有している場合は、オプションを使用して、要求で出力されるグループをアプリケーションの関連するグループに制限することをお勧めします。  
>- 新規のアプリケーション開発の場合、またはアプリケーションをそれに合わせて構成できる場合および入れ子になったグループのサポートが必要ない場合は、アプリ内承認がグループではなく、アプリケーション ロールに基づくようにすることをお勧めします。  これにより、トークンに入力する必要がある情報の量が制限され、安全性が高まり、アプリの構成からユーザーの割り当てが分離されます。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>AD FS およびその他の ID プロバイダーから移行するアプリケーションに対するグループ要求

AD FS を使用して認証するように構成された多くのアプリケーションでは、Windows AD グループ属性の形式のグループ メンバーシップ情報が利用されます。   これらの属性はグループ sAMAccountName であり、ドメイン名または Windows グループ セキュリティ ID (GroupSID) によって修飾される場合があります。  アプリケーションが AD FS によってフェデレーションされている場合、AD FS では TokenGroups 関数を使用して、ユーザーのグループ メンバーシップを取得します。

AD FS から移動されたアプリは、同じ形式の要求を必要とします。 グループ要求およびロール要求は、グループの Azure Active Directory objectID ではなく、ドメイン修飾された sAMAccountName または Active Directory から同期された GroupSID を含む Azure Active Directory から発行することができます。

グループ要求においてサポートされている形式は、次のとおりです。

- **Azure Active Directory Group ObjectId** (すべてのグループに利用できる)
- **sAMAccountName** (Active Directory から同期したグループに利用できる)
- **NetbiosDomain\sAMAccountName** (Active Directory から同期したグループに利用できる)
- **DNSDomainName\sAMAccountName** (Active Directory から同期したグループに利用できる)
- **On Premises Group Security Identifier** (Active Directory から同期したグループで利用できる)

> [!NOTE]
> sAMAccountName 属性および On Premises Group SID 属性は、Active Directory から同期したグループ オブジェクトだけに利用できます。   Azure Active Directory または Office365 内で作成されたグループには利用できません。   同期されたオンプレミス グループの属性を取得するように Azure Active Directory 内で構成されているアプリケーションは、同期されたグループのみでそれらを取得します。

## <a name="options-for-applications-to-consume-group-information"></a>アプリケーションにおいてグループ情報を利用するためのオプション

アプリケーションは、MS Graph グループのエンドポイントを呼び出して、認証されたユーザーのグループ情報を取得できます。 関連するグループ数が多い場合でも、この呼び出しによって、ユーザーがメンバーとして属する全グループを確実に利用できるようになります。  その際、グループの列挙は、トークン サイズの制限に依存しません。

しかし、既存のアプリケーションにおいて要求を介してグループ情報を利用することが期待されている場合は、さまざまな要求形式で Azure Active Directory を構成できます。  次のオプションを検討してください。

- アプリケーション内の承認を目的としてグループ メンバーシップを使用している場合は、グループ ObjectID を使用することをお勧めします。 グループ ObjectID は、Azure Active Directory 内で不変かつ一意であり、すべてのグループで使用できます。
- オンプレミスのグループ sAMAccountName を承認に使用する場合は、ドメイン修飾された名前を使用してください。名前が競合する可能性が低くなります。 sAMAccountName が 1 つの Active Directory ドメイン内で一意であったとしても、複数の Active Directory ドメインが Azure Active Directory テナントによって同期された場合は、複数のグループが同一の名前を持つ可能性があります。
- [アプリケーション ロール](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)を使用して、グループ メンバーシップとアプリケーション間に間接参照レイヤーを提供することを検討してください。   そうすると、アプリケーションでは、トークン内のロール要求に基づいて内部承認を決定します。
- Active Directory から同期したグループ属性を取得するようにアプリケーションが構成されている場合、グループは、それらの属性を保持していないと、要求の中には含まれません。
- グループ要求をアプリケーションに割り当てられたグループに制限するオプションが使用されている場合を除き、トークン内のグループ要求には、入れ子になったグループが含まれます。  ユーザーが GroupB のメンバーであり、GroupB が GroupA のメンバーである場合、そのユーザーに対するグループ要求には GroupA と GroupB の両方が含まれます。 組織のユーザーが多数のグループ メンバーシップを保有している場合は、トークン内にリストされるグループ数によって、トークン サイズが大きくなる場合があります。  Azure Active Directory では、SAML アサーションの場合は 150、JWT の場合は 200 に、1 つのトークン内で出力するグループ数を制限しています。  ユーザーが多数のグループのメンバーである場合、グループは省略され、その代わりにグループ情報を取得するための Graph エンドポイントへのリンクが含まれます。

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Active Directory から同期されたグループ属性を使用する場合の前提条件

ObjectId 形式を使用すると、グループ メンバーシップ要求を任意のグループのトークンで出力できます。 グループ ObjectId 以外の形式でグループ要求を使用するには、Azure AD Connect を使用してグループが Active Directory から同期されている必要があります。

Active Directory Groups に対するグループ名を出力するように Azure Active Directory を構成するには、次の 2 つの手順があります。

1. **Active Directory からグループ名を同期する** グループ要求またはロール要求の中で Azure Active Directory がグループ名またはオンプレミス グループ SID を出力するには、事前に、Active Directory から必要な属性が同期されている必要があります。  Azure AD Connect バージョン 1.2.70 以降を実行している必要があります。   1\.2.70 より前の Azure AD Connect のバージョンでは、Active Directory からグループ オブジェクトを同期しますが、必要なグループ名の属性は含まれません。  最新のバージョンにアップグレードしてください。

2. **トークン内にグループ要求が含まれるように、Azure Active Directory でアプリケーションの登録を構成する**。グループ要求は、ポータルの [エンタープライズ アプリケーション] セクションで構成することも、[アプリケーションの登録] セクションにある [アプリケーション マニフェスト] を使用して構成することも可能です。  アプリケーション マニフェストでグループ要求を構成するには、後述する「グループ属性のために Azure Active Directory の [アプリケーションの登録] を構成する」をご覧ください。

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>SSO 構成を使用して SAML アプリケーションのトークンにグループ要求を追加する

ギャラリーまたは非ギャラリーの SAML アプリケーションに対するグループ要求を構成するには、 **[エンタープライズ アプリケーション]** を開き、一覧内で該当のアプリケーションをクリックします。次に、 **[Single Sign On configuration]\(シングル サインオン構成\)** を選択し、 **[ユーザー属性とクレーム]** を選択します。

**[グループ要求を追加する]** をクリックします  

![[グループ要求を追加する] が選択された [ユーザー属性とクレーム] ページを示すスクリーンショット。](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

ラジオ ボタンを使用して、トークン内に含める必要があるグループを選択します。

![[セキュリティ グループ] が選択された [グループ クレーム] ウィンドウを示すスクリーンショット。](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| [選択] | 説明 |
|----------|-------------|
| **すべてのグループ** | セキュリティ グループ、配布リスト、ロールを出力します。  |
| **セキュリティ グループ** | グループ要求の中で、ユーザーがメンバーになっているセキュリティ グループを出力します。 |
| **ディレクトリ ロール** | ユーザーがディレクトリ ロールに割り当てられている場合は、"wids" 要求として出力されます (グループ要求は出力されません) |
| **アプリケーションに割り当てられているグループ** | アプリケーションに明示的に割り当てられ、ユーザーがメンバーになっているグループのみを出力します |

たとえば、ユーザーがメンバーとして属するセキュリティ グループを出力するには、[セキュリティ グループ] を選択します。

![[セキュリティ グループ] が選択され、[ソース属性] ドロップダウン メニューが開かれている、[グループ クレーム] ウィンドウを示すスクリーンショット。](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Azure AD objectID ではなく、Active Directory から同期されている Active Directory 属性を使用してグループを出力するには、ドロップダウンから必要な形式を選択します。 要求には、Active Directory から同期されたグループのみが含まれます。

![開かれた [ソース属性] ドロップダウン メニューを示すスクリーンショット。](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

アプリケーションに割り当てられたグループのみを出力するには、 **[アプリケーションに割り当てられているグループ]** を選択します

![[アプリケーションに割り当てられているグループ] が選択された [グループ クレーム] ウィンドウを示すスクリーンショット。](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

アプリケーションに割り当てられているグループは、トークンに含まれます。  ユーザーがメンバーになっている他のグループは省略されます。  このオプションでは、入れ子になったグループは含まれません。ユーザーは、アプリケーションに割り当てられたグループの直接のメンバーである必要があります。

アプリケーションに割り当てられているグループを変更するには、 **[エンタープライズ アプリケーション]** の一覧からアプリケーションを選択し、アプリケーションの左側のナビゲーション メニューの **[ユーザーとグループ]** をクリックします。

アプリケーションへのグループの割り当ての管理の詳細については、ドキュメント「[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)」を参照してください。

### <a name="advanced-options"></a>[詳細オプション]

グループ要求が出力される方法は、[詳細オプション] 下にある設定によって変更できます。

[グループ要求の名前をカスタマイズする]\: 選択した場合、別の要求の種類をグループ要求に指定できます。   [名前] フィールドに要求の種類を、[名前空間] フィールドに要求のオプションの名前空間を入力します。

![[グループ要求の名前をカスタマイズする] が選択され、[名前] と [名前空間] の値が入力されている、[詳細オプション] セクションを示すスクリーンショット。](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

アプリケーションによっては、グループ メンバーシップ情報を 'ロール' 要求内に表示する必要があります。 必要に応じて、[Emit groups a role claims]\(ロールとしてグループ要求を発行する\) チェック ボックスをオンにして、ユーザーのグループをロールとして出力できます。

![[グループ要求の名前をカスタマイズする] と [グループをロール要求として生成する] が選択されている、[詳細オプション] セクションを示すスクリーンショット。](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> グループ データをロールとして出力するオプションが使用されると、ロール要求内にグループのみが表示されます。  ユーザーが割り当てられているアプリケーション ロールはいずれも、ロール要求には表示されなくなります。

### <a name="edit-the-group-claims-configuration"></a>グループ要求の構成を編集する

グループ要求の構成が [ユーザー属性とクレーム] 構成に追加されると、グループ要求を追加するオプションはグレーで表示されます。グループ要求の構成を変更するには、 **[追加の要求]** の一覧でグループ要求をクリックします。

![要求の UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>グループ属性のために Azure AD の [アプリケーションの登録] を構成する

グループ要求は、[[アプリケーション マニフェスト]](../../active-directory/develop/reference-app-manifest.md) の [[Optional Claims]\(オプションの要求\)](../../active-directory/develop/active-directory-optional-claims.md) セクションでも構成できます。

1. ポータルから [Azure Active Directory]-> [アプリケーションの登録] の順に移動して、[アプリケーション] -> [マニフェスト] の順に選択します。

2. groupMembershipClaim を変更して、グループ メンバーシップの要求を有効にします。

有効な値は次のとおりです。

| [選択] | 説明 |
|----------|-------------|
| **"All"** | セキュリティ グループ、配布リスト、ロールを出力します |
| **"SecurityGroup"** | グループ要求の中で、ユーザーがメンバーになっているセキュリティ グループを出力します。 |
| **"DirectoryRole"** | ユーザーがディレクトリ ロールに割り当てられている場合は、"wids" 要求として出力されます (グループ要求は出力されません) |
| **"ApplicationGroup"** | アプリケーションに明示的に割り当てられ、ユーザーがメンバーになっているグループのみを出力します |
| **"None"** | グループは返されません (大文字と小文字は区別されないため、何も機能しません。アプリケーション マニフェストで直接設定できます)。 |

   次に例を示します。

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   既定では、グループの ObjectID は、グループ要求の値に出力されます。  オンプレミス グループ属性を含むように要求の値を変更するか、または要求の種類をロールに変更するには、次のように OptionalClaims 構成を使用します。

3. グループ名の構成を [optional claims]\(オプションの要求\) に設定します。

   トークン内のグループにオンプレミスの AD グループの属性が含まれるようにする場合は、[optional claims]\(オプションの要求\) セクションでオプション要求の適用先となるトークンの種類を指定します。  次に示す複数のトークンの種類が、一覧に表示される可能性があります。

   - OIDC ID トークン用の idToken
   - OAuth/OIDC アクセス トークン用の accessToken
   - SAML トークン用の Saml2Token

   > [!NOTE]
   > Saml2Token の種類は、SAML1.1 および SAML2.0 両方の形式のトークンに適用されます。

   関連する各トークンの種類に対して、マニフェストにある OptionalClaims セクションを使用するようにグループ要求を変更します。 OptionalClaims スキーマは次のようになっています。

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | オプション要求のスキーマ | 値 |
   |----------|-------------|
   | **name:** | 必ず "groups" になります |
   | **source:** | 使用されていません。 省略するか、null を指定します |
   | **essential:** | 使用されていません。 省略するか、false を指定します |
   | **additionalProperties:** | その他のプロパティのリスト。  有効なオプションは、"sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"、"emit_as_roles" です |

   additionalProperties では、"sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name" のいずれか 1 つのみが必要です。  複数ある場合、最初の 1 つが使用され、それ以外は無視されます。

   アプリケーションによっては、ロール要求内にユーザーに関するグループ情報が必要になります。  グループ要求からロール要求へ要求の種類を変更するには、"emit_as_roles" を追加のプロパティに付け加えます。  グループの値が、ロール要求内に出力されます。

   > [!NOTE]
   > "emit_as_roles" が使用された場合、ユーザーが割り当て済みとして構成されているアプリケーション ロールは、ロール要求には表示されません

### <a name="examples"></a>例

OAuth アクセス トークンの中で、dnsDomainName\SAMAccountName の形式でグループをグループ名として出力します

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

SAML および OIDC ID トークンの中でロール要求として netbiosDomain\samAccountName 形式で返されたグループ名を出力するには、次のように記述します。

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>次のステップ

- [グループおよびグループ要求を使用して ASP.NET Core Web アプリに認可を追加する (コード サンプル)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [ロール要求を構成する](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
