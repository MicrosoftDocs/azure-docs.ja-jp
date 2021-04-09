---
title: 動的に設定されるグループ メンバーシップのルール - Azure AD | Microsoft Docs
description: グループを自動的に設定するメンバーシップ ルールと、ルール参照を作成する方法。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 02/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c25504e3313234ac6b6f80a6e00c77fce28b1400
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174531"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Azure Active Directory の動的グループ メンバーシップ ルール

Azure Active Directory (Azure AD) では、複雑な属性ベースのルールを作成して、グループの動的メンバーシップを有効にすることができます。 動的グループ メンバーシップを利用することで、ユーザーの追加と削除に必要な管理費が削減されます。 この記事では、ユーザーまたはデバイスに対する動的メンバーシップ ルールを作成するためのプロパティと構文について説明します。 セキュリティ グループまたは Microsoft 365 グループには、動的メンバーシップのルールを設定できます。

ユーザーまたはデバイスのいずれかの属性が変更されると、システムはディレクトリ内のすべての動的なグループ ルールを評価して、この変更によってグループの追加または削除がトリガーされるかどうかを確認します。 ユーザーまたはデバイスがグループのルールを満たしている場合、それらはそのグループのメンバーとして追加されます。 ルールを満たさなくなると、削除されます。 動的グループのメンバーを手動で追加または削除することはできません。

- デバイスまたはユーザーの動的グループは作成できても、ユーザーとデバイスの両方を含むルールは作成できません。
- デバイス所有者の属性に基づいてデバイス グループを作成することはできません。 デバイス メンバーシップ ルールで参照できるのは、デバイスの属性のみです。

> [!NOTE]
> この機能を使うには、少なくとも 1 つの動的グループのメンバーである一意のユーザーごとに Azure AD Premium P1 ライセンスが必要です。 ユーザーを動的グループのメンバーにするために、そのユーザーにライセンスを割り当てる必要はありませんが、少なくともそのすべてのユーザーを対象にできるだけのライセンス数が Azure AD 組織に含まれている必要があります。 たとえば、組織のすべての動的グループに、合計 1,000 人の一意のユーザーがいる場合、ライセンス要件を満たすには、Azure AD Premium P1 に対するライセンスが 1,000 個以上必要です。
> 動的なデバイス グループのメンバーであるデバイスには、ライセンスは必要ありません。

## <a name="rule-builder-in-the-azure-portal"></a>Azure portal のルール ビルダー

Azure AD には、重要なルールをすばやく作成したり更新したりできるルール ビルダーが用意されています。 ルール ビルダーでは、最大で 5 つの式の作成がサポートされます。 ルール ビルダーを使用すると、いくつかの単純な式を使ってルールを簡単に作成できますが、すべてのルールの再現に使用することはできません。 作成したいルールがルール ビルダーでサポートされていない場合は、テキスト ボックスを使用できます。

以下に、テキスト ボックスを使用して構築することをお勧めする高度なルールまたは構文の例をいくつか示します。

- 5 つを超える式を持つルール
- 直接の部下のルール
- [演算子の優先順位](#operator-precedence)の設定
- [複雑な式を持つルール](#rules-with-complex-expressions) (例: `(user.proxyAddresses -any (_ -contains "contoso"))`)

> [!NOTE]
> ルール ビルダーは、テキスト ボックスで作成された一部のルールを表示できない場合があります。 ルール ビルダーがルールを表示できない場合は、メッセージが表示されることがあります。 ルール ビルダーは、動的グループ ルールのサポートされている構文、検証、または処理をどのような方法でも変更しません。

具体的な手順については、[動的グループの作成または更新](groups-create-rule.md)に関するページを参照してください。

![動的グループのメンバーシップのルールを追加する](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>単一式のルール構文

単一式は、メンバーシップ ルールの最もシンプルな形式であり、前述の 3 つの部分でのみ構成されます。 単一式のルールは `Property Operator Value` のようになります。プロパティの構文は object.property の名前です。

次は、単一式で正しく構成されたメンバーシップ ルールの例です。

```
user.department -eq "Sales"
```

単一式の場合、かっこは省略可能です。 メンバーシップ ルール本文の合計文字数が 2,048 文字を超えないようにしてください。

## <a name="constructing-the-body-of-a-membership-rule"></a>メンバーシップ ルールの本文の作成

グループにユーザーまたはデバイスを自動的に入力するメンバーシップ ルールは、true または false に帰結するバイナリ式です。 シンプルなルールの要素は次の 3 つです。

- プロパティ
- 演算子
- 値

式の中の要素の順序は、構文エラーを回避するために重要です。

## <a name="supported-properties"></a>サポートされているプロパティ

メンバーシップ ルールを作成するとき、3 種類のプロパティを使用できます。

- Boolean
- String
- 文字列コレクション

次は、単一式の作成に使用できるユーザー プロパティです。

### <a name="properties-of-type-boolean"></a>ブール型のプロパティ

| Properties | 使用できる値 | 使用法 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>文字列型のプロパティ

| Properties | 使用できる値 | 使用法 |
| --- | --- | --- |
| city |任意の文字列値または *null* |(user.city -eq "value") |
| country |任意の文字列値または *null* |(user.country -eq "value") |
| companyName | 任意の文字列値または *null* | (user.companyName -eq "value") |
| department |任意の文字列値または *null* |(user.department -eq "value") |
| displayName |任意の文字列値 |(user.displayName -eq "value") |
| employeeId |任意の文字列値 |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |任意の文字列値または *null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任意の文字列値または *null* |(user.givenName -eq "value") |
| jobTitle |任意の文字列値または *null* |(user.jobTitle -eq "value") |
| mail |任意の文字列値または *null* (ユーザーの SMTP アドレス) |(user.mail -eq "value") |
| mailNickName |任意の文字列値 (ユーザーのメール エイリアス) |(user.mailNickName -eq "value") |
| mobile |任意の文字列値または *null* |(user.mobile -eq "value") |
| objectId |ユーザー オブジェクトの GUID |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | オンプレミスからクラウドに同期されたユーザーのオンプレミスのセキュリティ識別子 (SID)。 |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |なし DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration、DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任意の文字列値または *null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任意の文字列値または *null* |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 コード |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任意の文字列値または *null* |(user.sipProxyAddress -eq "value") |
| state |任意の文字列値または *null* |(user.state -eq "value") |
| streetAddress |任意の文字列値または *null* |(user.streetAddress -eq "value") |
| surname |任意の文字列値または *null* |(user.surname -eq "value") |
| telephoneNumber |任意の文字列値または *null* |(user.telephoneNumber -eq "value") |
| usageLocation |2 文字の国または地域コード |(user.usageLocation -eq "US") |
| userPrincipalName |任意の文字列値 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>文字列コレクション型のプロパティ

| Properties | 使用できる値 | 使用法 |
| --- | --- | --- |
| otherMails |任意の文字列値 |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

デバイス ルールに使用されるプロパティについては、「[デバイスのルール](#rules-for-devices)」を参照してください。

## <a name="supported-expression-operators"></a>サポートされている式の演算子

次の表は、サポートされているすべての演算子とその単一式用の構文をまとめたものです。 演算子は、ハイフン (-) のプレフィックスがあってもなくても使用できます。

| 演算子 | 構文 |
| --- | --- |
| 等しくない |-ne |
| 等しい |-eq |
| 指定値で始まらない |-notStartsWith |
| [指定値で始まる] |-startsWith |
| 指定値を含まない |-notContains |
| Contains |-contains |
| 一致しない |-notMatch |
| 一致する |-match |
| 場所 | -in |
| 含まれない | -notIn |

### <a name="using-the--in-and--notin-operators"></a>-in および -notIn 演算子の使用

ユーザー属性の値を複数の異なる値に対して比較する場合は、-in または -notIn 演算子を使用できます。 値の一覧の始まりと終わりを示す目的で "[" と "]" の角かっこ記号を使用します。

 次の例の式は、user.department の値が一覧内のいずれかの値に等しい場合に true に評価されます。

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>-match 演算子の使用 
**-match** 演算子は、正規表現の照合に使用されます。 例 :

```
user.displayName -match "Da.*"   
```
Da、Dav、David は true と評価され、aDa は false と評価されます。

```
user.displayName -match ".*vid"
```
David は true と評価され、Da は false と評価されます。

## <a name="supported-values"></a>サポートされている値

式内で使用される値は、次に挙げるいくつかの型で構成できます。

* 文字列
* ブール値 – true、false
* 数値
* 配列 – 数値配列、文字列配列

式内で値を指定するとき、エラーを回避するために、正しい構文を使用することが重要です。 構文のヒント:

* 値が文字列でなければ、二重引用符は任意です。
* 文字列演算と正規表現演算は、大文字と小文字が区別されません。
* 文字列値に二重引用符が含まれているとき、両方の引用符を \` 文字でエスケープしてください。たとえば、"Sales" が値のとき、user.department -eq \`"Sales\`" が正しい構文です。
* null を値として使用し、Null チェックを実行することもできます。たとえば、`user.department -eq null` のようになります。

### <a name="use-of-null-values"></a>Null 値の使用

ルールで null 値を指定するには、*null* 値を使用します。 

* 式で *null* 値を比較するとき、-eq または -ne を使用します。
* リテラル文字列値として解釈する場合にのみ、*null* という語を引用符で囲みます。
* -not 演算子は、null の比較演算子として使用できません。 使うと、null または $null のどちらを使ってもエラーになります。

null 値を参照する正しい方法は次のとおりです。

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>複数の式を持つルール

グループ メンバーシップ ルールは、複数の単一式を論理演算子 (-and、-or、-not) で結合して作ることができます。 論理演算子は組み合わせて使用することもできます。 

複数の式で正しく構築されたメンバーシップ ルールの例を次に示します。

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>演算子の優先順位

演算子はすべて、優先順位の一番高いものから一番低いものの順で下に一覧表示されます。 同じ行にある演算子の優先順位は同じです。

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

演算子の優先順位の例を次に示します。この例では、ユーザーに対して 2 つの式が評価されます。

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

優先順位が要件を満たさない場合にのみ、かっこを追加する必要があります。 たとえば、部門を最初に評価する場合、次のようにかっこを使用して順序を決定します。

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>複雑な式を持つルール

メンバーシップ ルールは、プロパティ、演算子、値がより複雑な形式をとる複雑な式で構成できます。 次のいずれかが当てはまるとき、式が複雑であると見なされます。

* プロパティが値の集まりで、具体的には複数値プロパティで構成される
* 式で -any 演算子と -all 演算子が使用される
* 式の値自体が 1 つまたは複数の式になる

## <a name="multi-value-properties"></a>複数値プロパティ

複数値プロパティは、同じ型のオブジェクトのコレクションです。 論理演算子の -any と -all でメンバーシップ ルールを作成するときに使用できます。

| Properties | 値 | 使用法 |
| --- | --- | --- |
| assignedPlans | コレクション内の各オブジェクトは、capabilityStatus、service、servicePlanId の文字列プロパティを公開します。 |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -any (\_ -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>-any 演算子と -all 演算子を使用する

コレクション内の 1 つの項目またはすべての項目に条件を適用するには、それぞれ -any および -all 演算子を使用できます。

* -any (コレクション内の少なくとも 1 つの項目が条件に一致するときに満たされる)
* -all (コレクション内のすべての項目が条件に一致するときに満たされる)

#### <a name="example-1"></a>例 1

assignedPlans は、ユーザーに割り当てられたすべてのサービス プランをリストする複数値プロパティです。 次の式では、同様に [有効] 状態にある Exchange Online (プラン 2) サービス プランを (GUID 値として) 持っているユーザーが選択されます。

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

このようなルールを使用し、Microsoft 365 (あるいはその他の Microsoft Online Service) 機能が有効になっているすべてのユーザーをグループ化できます。 次に、一連のポリシーをグループに適用できます。

#### <a name="example-2"></a>例 2

次の式は、Intune サービス (サービス名 "SCO" によって識別されます) に関連付けられた何らかのサービス プランを持っているすべてのユーザーを選択します。

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

#### <a name="example-3"></a>例 3

次の式では、サービス プランが割り当てられていないすべてのユーザーが選択されます。

```
user.assignedPlans -all (assignedPlan.servicePlanId -eq "")
```

### <a name="using-the-underscore-_-syntax"></a>アンダースコア (\_) 構文を使用する

アンダースコア (\_) 構文は、いずれかの複数値文字列コレクション プロパティの特定の値の出現回数を一致させて、ユーザーまたはデバイスを動的グループに追加します。 -any 演算子または -all 演算子と共に使用します。

ルール内でアンダースコア (\_) を使用して、user.proxyAddress (user.otherMails の場合と同様に機能します) に基づいてメンバーを追加する例を以下に示します。 このルールは、"contoso" を含むプロキシ アドレスを持つすべてのユーザーをグループに追加します。

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>その他のプロパティと一般的なルール

### <a name="create-a-direct-reports-rule"></a>"直属の部下" ルールを作成する

マネージャーのすべての直接の部下が含まれたグループを作成できます。 将来、マネージャーの直属の部下が変更されたとき、グループのメンバーシップが自動的に調整されます。

直属の部下ルールは次の構文で構成されます。

```
Direct Reports for "{objectID_of_manager}"
```

有効なルールの例を示します。ここで、"62e19b97-8b3d-4d4a-a106-4ce66896a863" はマネージャーの objectID です。

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

次のヒントはルールを正しく使用するために役立ちます。

- **マネージャー ID** はマネージャーのオブジェクト ID です。 マネージャーの **プロファイル** にあります。
- ルールを機能させるには、組織のユーザーに対して **[マネージャー ID]** プロパティを正しく設定します。 現在の値はユーザーの **プロファイル** で確認できます。
- このルールでは、マネージャーの直属の部下のみがサポートされます。 言い換えると、マネージャーの直属の部下 *とさらに* その部下でグループを作成することはできません。
- このルールは、他のメンバーシップ ルールと組み合わせることができません。

### <a name="create-an-all-users-rule"></a>"すべてのユーザー" ルールを作成する

メンバーシップ ルールを使用し、組織内のすべてのユーザーを含むグループを作成できます。 将来、ユーザーが組織に追加されたり、組織から削除されたりしたとき、メンバーシップは自動的に調整されます。

"すべてのユーザー" ルールは、-ne 演算子と null 値を利用した単一式で構成されます。 このルールでは、メンバー ユーザーと共に B2B ゲスト ユーザーがグループに追加されます。

```
user.objectId -ne null
```
グループでゲスト ユーザーを除外し、ご自身の組織のメンバーのみを含めるようにする場合は、次の構文を使用できます。

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>"すべてのデバイス" ルールを作成する

メンバーシップ ルールを使用し、組織内のすべてのデバイスを含むグループを作成できます。 将来、デバイスが組織に追加されたり、組織から削除されたりしたとき、メンバーシップは自動的に調整されます。

"すべてのデバイス" ルールは、-ne 演算子と null 値を利用した単一式で構成されます。

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>拡張機能プロパティとカスタム拡張機能プロパティ

拡張機能属性とカスタム拡張機能プロパティは、動的メンバーシップ ルールで文字列プロパティとしてサポートされています。 [拡張属性](/graph/api/resources/onpremisesextensionattributes)はオンプレミスの Window Server AD から同期され、"ExtensionAttributeX" という形式です (X は 1 ～ 15)。 プロパティとして拡張機能属性を使用するルールの例を示します。

```
(user.extensionAttribute15 -eq "Marketing")
```

[カスタム拡張機能プロパティ](../hybrid/how-to-connect-sync-feature-directory-extensions.md)はオンプレミス Windows Server AD または接続されている SaaS アプリケーションから同期され、形式は `user.extension_[GUID]_[Attribute]` になります。

* [GUID] は Azure AD でプロパティを作成したアプリケーションの Azure AD における一意の識別子です
* [Attribute] は作成されたプロパティの名前です

カスタム拡張機能プロパティを使用するルールの例を次に示します。

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

カスタム プロパティ名は、Graph Explorer を使用してユーザーのプロパティを問い合わせ、プロパティ名を探すことで見つけられます。 また、動的ユーザー グループのルール ビルダーにある **[カスタム拡張機能のプロパティを取得します]** リンクを選択し、一意のアプリ ID を入力すると、ユーザーの動的メンバーシップ ルールの作成時に使用するカスタム拡張機能プロパティの完全な一覧が表示されるようになりました。 この一覧を最新の情報に更新して、そのアプリの新しいカスタム拡張機能プロパティを取得することもできます。

## <a name="rules-for-devices"></a>デバイスのルール

グループのメンバーシップのデバイス オブジェクトを選択するルールを作成することもできます。 グループ メンバーとしてユーザーとデバイスの両方を含めることはできません。 

> [!NOTE]
> **organizationalUnit** 属性は表示されなくなったため、使用できません。 この文字列は、特定の場合に Intune によって設定されますが、Azure AD では認識されないため、この属性に基づいてデバイスがグループに追加されることはありません。

> [!NOTE]
> systemlabels は、Intune では設定できない読み取り専用の属性です。
>
> Windows 10 の場合、deviceOSVersion 属性の正しい形式は次のとおりです: (device.deviceOSVersion -eq "10.0.17763")。 書式設定は、Get-MsolDevice PowerShell コマンドレットを使用して検証できます。

次のデバイス属性を使用できます。

 デバイス属性  | 値 | 例
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | 任意の文字列値 |(device.displayName -eq "Rob iPhone")
 deviceOSType | 任意の文字列値 | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -contains "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")<br>(device.deviceOSType -eq "Windows")
 deviceOSVersion | 任意の文字列値 | (device.deviceOSVersion -eq "9.1")<br>(device.deviceOSVersion -eq "10.0.17763.0")
 deviceCategory | 有効なデバイス カテゴリ名 | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | 任意の文字列値 | (device.deviceManufacturer -eq "Samsung")
 deviceModel | 任意の文字列値 | (device.deviceModel -eq "iPad Air")
 deviceOwnership | 個人、会社、不明 | (device.deviceOwnership -eq "Company")
 enrollmentProfileName | Apple Device Enrollment プロファイル名、Android Enterprise 企業所有専用 Enrollment プロファイル名、または Windows Autopilot プロファイル名 | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (モバイル デバイスの場合)<br>PC (Intune PC エージェントによって管理されるコンピューターの場合) | (device.managementType -eq "MDM")
 deviceId | 有効な Azure AD デバイス ID | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | 有効な Azure AD オブジェクト ID |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | すべてのオートパイロット デバイス、OrderID、PurchaseOrderID など、オートパイロットによって使用される任意の文字列値  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | Modern Workplace デバイスをタグ付けするための Intune デバイス プロパティに一致する任意の文字列 | (device.systemLabels - "M365Managed" を含む)

> [!Note]  
> デバイスに動的グループを作成する場合、deviceOwnership には、"Company" と等しい値を設定する必要があります。 Intune 上のデバイスの所有権には、代わりに Corporate として表されます。 詳細については、「[OwnerTypes](/intune/reports-ref-devices#ownertypes)」を参照してください。 

## <a name="next-steps"></a>次のステップ

次の記事は、Azure Active Directory のグループに関する追加情報を提供します。

- [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
- [新しいグループの作成とメンバーの追加](../fundamentals/active-directory-groups-create-azure-portal.md)
- [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [グループ内のユーザーの動的ルールの管理](groups-create-rule.md)
