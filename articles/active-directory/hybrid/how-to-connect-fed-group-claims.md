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
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 2d547c73137605e4666499b568bdcebce394935a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595220"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Azure Active Directory (パブリック プレビュー) を使ってアプリケーションに対するグループ要求を構成する

Azure Active Directory では、アプリケーション内で使用するユーザー グループのメンバーシップ情報をトークンで提供できます。  主なパターンとして、次の 2 つがサポートされています。

- Azure Active Directory オブジェクト識別子 (OID) 属性によって識別されるグループ (一般公開)
- Active Directory (AD) の同期グループおよびユーザーに対応する sAMAccountName または GroupSID 属性によって識別されるグループ (パブリック プレビュー)

> [!IMPORTANT]
> このプレビュー機能については、留意すべき注意事項が複数あります。
>
>- オンプレミスから同期される sAMAccountName およびセキュリティ識別子 (SID) の属性の使用に関するサポートは、AD FS およびその他の ID プロバイダーからの既存のアプリケーションの移動を有効にするように設計されています。 Azure AD 内で管理されるグループには、これらの要求を発行するために必要な属性は含まれていません。
>- 大規模な組織では、ユーザーがメンバーになっているグループ数が、Azure Active Directory によってトークンに追加される制限を超える可能性があります。 SAML トークンの場合は 150 グループ、JWT の場合は 200 グループです。 これは、予期しない結果につながります。 これが潜在的な問題である場合は、テストを実施し、必要ならば、Microsoft から追加される拡張機能によってアプリケーションの関連グループへの要求を制限できるようになるまでお待ちになることをお勧めします。  
>- 新規のアプリケーション開発の場合、またはアプリケーションをそれに合わせて構成できる場合および入れ子になったグループのサポートが必要ない場合は、アプリ内承認がグループではなく、アプリケーション ロールに基づくようにすることをお勧めします。  これにより、トークンにアクセスするのに必要な情報量が制限され、より安全になり、アプリの構成からユーザーの割り当てが分離されます。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>AD FS およびその他の ID プロバイダーから移行するアプリケーションに対するグループ要求

AD FS によって認証するように構成された多くのアプリケーションでは、Windows AD グループ属性の形式のグループ メンバーシップ情報に依存しています。   これらの属性はグループ sAMAccountName であり、ドメイン名または Windows グループ セキュリティ ID (GroupSID) によって修飾される場合があります。  アプリケーションが AD FS によってフェデレーションされている場合、AD FS では TokenGroups 関数を使用して、ユーザーのグループ メンバーシップを取得します。

アプリが AD FS から受信するトークンと一致するように、グループの Azure Active Directory objectID ではなくドメイン修飾された sAMAccountName を含めて、グループ要求およびロール要求を発行することができます。

グループ要求においてサポートされている形式は、次のとおりです。

- **Azure Active Directory Group ObjectId** (すべてのグループに利用できる)
- **sAMAccountName** (Active Directory から同期したグループに利用できる)
- **NetbiosDomain\sAMAccountName** (Active Directory から同期したグループに利用できる)
- **DNSDomainName\sAMAccountName** (Active Directory から同期したグループに利用できる)
- **On Premises Group Security Identifier** (Active Directory から同期したグループで利用できる)

> [!NOTE]
> sAMAccountName 属性および On Premises Group SID 属性は、Active Directory から同期したグループ オブジェクトだけに利用できます。   Azure Active Directory または Office365 内で作成されたグループには利用できません。   同期されたオンプレミス グループの属性を取得するように Azure Active Directory 内で構成されているアプリケーションは、同期されたグループのみでそれらを取得します。

## <a name="options-for-applications-to-consume-group-information"></a>アプリケーションにおいてグループ情報を利用するためのオプション

アプリケーションにおいてグループ情報を取得するための 1 つの方法は、認証されたユーザーのグループ メンバーシップを取得するために、グラフ グループのエンドポイントを呼び出すことです。 関連するグループ数が多く、かつ、アプリケーションにおいてユーザーがメンバーとして属するすべてのグループを列挙する必要がある場合でも、この呼び出しによって、ユーザーがメンバーとして属する全グループが確実に利用できるようになります。  その際、グループの列挙は、トークン サイズの制限に依存しません。

しかし、既存のアプリケーションにおいて、受信したトークン内の要求を介してグループ情報を利用することが既に期待されている場合は、多数のさまざまな要求オプションを使って、アプリケーションのニーズに合うように Azure Active Directory を構成できます。  以下のオプションを検討します。

- アプリケーション内の承認を目的としてグループ メンバーシップを使用している場合は、Azure Active Directory 内で不変かつ一意であり、すべてのグループに利用できる Group ObjectID を使用することをお勧めします。
- オンプレミスのグループ sAMAccountName を承認に使用する場合は、ドメイン修飾された名前を使用してください。名前の競合が発生しにくくなります。 Active Directory ドメイン内では sAMAccountName は必然的に一意になりますが、複数の Active Directory ドメインが Azure Active Directory テナントによって同期された場合は、複数のグループが同一の名前になってしまう可能性があります。
- [アプリケーション ロール](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)を使用して、グループ メンバーシップとアプリケーション間に間接参照レイヤーを提供することを検討してください。   そうすると、アプリケーションでは、トークン内のロール要求に基づいて内部承認を決定します。
- Active Directory から同期したグループ属性を取得するようにアプリケーションが構成されている場合、グループは、それらの属性を保持していないと、要求の中には含まれません。
- トークン内のグループ要求には、入れ子になったグループが含まれます。   ユーザーが GroupB のメンバーであり、GroupB が GroupA のメンバーである場合、そのユーザーに対するグループ要求には GroupA と GroupB の両方が含まれます。 たくさんのグループ メンバーシップが入れ子になっているグループおよびユーザーが頻繁に使用される組織では、トークン内にリストされるグループ数によって、トークン サイズが大きくなる場合があります。   Azure Active Directory では、SAML アサーションの場合は 150、JWT の場合は 200 に、1 つのトークン内で出力するグループ数を制限することで、トークンが大きくなりすぎるのを防いでいます。  制限を超える多数のグループにユーザーが属している場合は、該当するグループが出力され、リンクをたどることでグループ情報を取得できます。

> Active Directory から同期されたグループ属性を使用する場合の前提条件:  Azure AD Connect を使用して、グループが Active Directory から同期されている必要があります。

Active Directory Groups に対するグループ名を出力するように Azure Active Directory を構成するには、次の 2 つの手順があります。

1. **Active Directory からグループ名を同期する** グループ要求またはロール要求の中で Azure Active Directory がグループ名またはオンプレミス グループ SID を出力するには、事前に、Active Directory から必要な属性が同期されている必要があります。  Azure AD Connect バージョン 1.2.70 以降を実行している必要があります。   バージョン 1.2.70 より前の Azure AD Connect では、Active Directory からグループ オブジェクトを同期しても、必要なグループ名の属性が既定で含まれることはありません。  現在のバージョンにアップグレードする必要があります。

2. **トークン内にグループ要求を含むように、Azure Active Directory においてアプリケーションの登録を構成する** グループ要求は、ギャラリーまたは非ギャラリーの SAML SSO アプリケーション用のポータルの [エンタープライズ アプリケーション] セクションで構成するか、または [アプリケーションの登録] セクションにある [アプリケーション マニフェスト] を使用して構成することが可能です。  アプリケーション マニフェストでグループ要求を構成するには、後述する「グループ属性のために Azure Active Directory の [アプリケーションの登録] を構成する」をご覧ください。

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>SSO 構成を使って SAML アプリケーションに対してグループ要求を構成する

ギャラリーまたは非ギャラリーの SAML アプリケーションに対するグループ要求を構成するには、[エンタープライズ アプリケーション] を開き、一覧内で該当のアプリケーションをクリックして [シングル サインオン] 構成を選択します。

[Groups returned in token]\(要求で返されるグループ\) の横にある編集アイコンを選択します。

![要求の UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

ラジオ ボタンを使用して、トークン内に含める必要があるグループを選択します。

![要求の UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 選択肢番号 | 説明 |
|----------|-------------|
| **すべてのグループ** | セキュリティ グループのリストと配布リストを出力します。   また、'wids' 要求の中では、ユーザーが割り当てられたディレクトリ ロールが出力され、ロール要求の中では、ユーザーが割り当てられたいずれかのアプリケーション ロールが出力されます。 |
| **セキュリティ グループ** | グループ要求の中で、ユーザーがメンバーになっているセキュリティ グループを出力します。 |
| **配布リスト** | ユーザーがメンバーになっている配布リストを出力します。 |
| **ディレクトリ ロール** | ユーザーがディレクトリ ロールに割り当てられている場合は、'wids' 要求として出力されます (グループ要求は出力されません)。 |

たとえば、ユーザーがメンバーとして属するセキュリティ グループを出力するには、[セキュリティ グループ] を選択します。

![要求の UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Azure AD objectID ではなく、Active Directory から同期されている Active Directory 属性を使用してグループを出力するには、ドロップダウンから必要な形式を選択します。  これにより、要求内のオブジェクト ID がグループ名を含む文字列値に置き換えられます。   要求には、Active Directory から同期されたグループのみが含まれます。

![要求の UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>[詳細オプション]

グループ要求が出力される方法は、[詳細オプション] 下にある設定によって変更できます。

[グループ要求の名前をカスタマイズする]: 選択した場合、別の要求の種類をグループ要求に指定できます。   [名前] フィールドに要求の種類を、[名前空間] フィールドに要求のオプションの名前空間を入力します。

![要求の UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

アプリケーションによっては、グループ メンバーシップ情報を 'ロール' 要求内に表示する必要があります。 必要に応じて、[Emit groups a role claims]\(ロールとしてグループ要求を発行する\) チェック ボックスをオンにして、ユーザーのグループをロールとして出力できます。

![要求の UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> グループ データをロールとして出力するオプションが使用されると、ロール要求内にグループのみが表示されます。  ユーザーが割り当てられているアプリケーション ロールはいずれも、ロール要求には表示されなくなります。

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>グループ属性のために Azure AD の [アプリケーションの登録] を構成する

グループ要求は、[[アプリケーション マニフェスト]](../../active-directory/develop/reference-app-manifest.md) の [[Optional Claims]\(オプションの要求\)](../../active-directory/develop/active-directory-optional-claims.md) セクションでも構成できます。

1. ポータルから [Azure Active Directory]-> [アプリケーションの登録] の順に移動して、[アプリケーション] -> [マニフェスト] の順に選択します。

2. groupMembershipClaim を変更して、グループ メンバーシップの要求を有効にします。

   有効な値は次のとおりです。

   - "All"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   例:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   既定では、グループの ObjectID は、グループ要求の値に出力されます。  オンプレミス グループ属性を含むように要求の値を変更するか、または要求の種類をロールに変更するには、次のように OptionalClaims 構成を使用します。

3. グループ名の構成を [optional claims]\(オプションの要求\) に設定します。

   [optional claims]\(オプションの要求\) セクションにあるオンプレミス AD グループ属性をトークン内のグループに含める場合は、オプション要求の適用先となるトークンの種類、要求されるオプション要求の名前、および必要な追加のプロパティを指定します。  次に示す複数のトークンの種類が、一覧に表示される可能性があります。

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
   | **source:** | 使用されません。 省略するか、null を指定します |
   | **essential:** | 使用されません。 省略するか、false を指定します |
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

## <a name="next-steps"></a>次の手順

[ハイブリッド ID とは](whatis-hybrid-identity.md)
