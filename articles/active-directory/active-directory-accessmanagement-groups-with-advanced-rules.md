---
title: "Azure Active Directory でオブジェクト属性に基づいてグループのメンバーを動的に設定する | Microsoft Docs"
description: "サポートされている式のルール演算子とパラメーターを含む、グループ メンバーシップの高度なルールを作成する方法。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: ae2a2e477137bc117111b147e1f088d528a55de5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---

# <a name="populate-groups-dynamically-based-on-object-attributes"></a>オブジェクト属性に基づいてグループのメンバーを動的に設定する
Azure クラシック ポータルでは、Azure Active Directory (Azure AD) グループに対して、より複雑な属性ベースの動的メンバーシップを有効化できます。  

ユーザーまたはデバイスのいずれかの属性が変更されると、システムはディレクトリ内のすべての動的なグループ ルールを評価して、この変更によってグループの追加または削除がトリガーされるかどうかを確認します。 ユーザーまたはデバイスがグループのルールを満たしている場合、それらはそのグループのメンバーとして追加されます。 ルールを満たさなくなると、削除されます。

> [!NOTE]
> - セキュリティ グループまたは Office 365 グループには、動的メンバーシップのルールを設定できます。
>
> - この機能を使うには、少なくとも 1 つの動的グループに追加される各ユーザー メンバーに Azure AD Premium P1 ライセンスが必要です。
>
> - デバイスまたはユーザーの動的グループは作成できても、ユーザー オブジェクトとデバイス オブジェクトの両方を含むルールは作成できません。

> - 現時点では、ユーザーの属性の所有に基づいてデバイス グループを作成することはできません。 デバイスのメンバーシップのルールは、ディレクトリ内のデバイス オブジェクトの直接の属性のみを参照できます。

## <a name="to-create-an-advanced-rule"></a>高度なルールを作成するには
1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[Active Directory]**を選択し、該当する組織のディレクトリを開きます。
2. **[グループ]** タブを選択し、編集するグループを開きます。
3. **[構成]** タブを選択し、**[高度なルール]** オプションを選択して、テキスト ボックスに高度なルールを入力します。

## <a name="constructing-the-body-of-an-advanced-rule"></a>高度なルール本体の作成
グループの動的なメンバーシップ管理を目的として作成される高度なルールは基本的に、3 つの構成要素から成る、true または false を結果として返す 2 項演算式です。 その 3 つの構成要素を次に示します。

* 左辺のパラメーター
* 2 項演算子
* 右辺の定数

たとえば全体で見ると、(leftParameter binaryOperator "RightConstant") のようになります。2 項演算式全体を開きかっこと閉じかっこで囲んだ上で、右辺の定数は二重引用符で囲む必要があります。左辺のパラメーターの構文は user.property という形式で入力します。 高度なルールは、複数の 2 項演算式を論理演算子 (-and、-or、-not) で組み合わせることができます。
以下に示したのは、正しい構文に沿って作成された高度なルールの例です。

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

サポートされているパラメーターと式のルール演算子の全一覧については、以降のセクションを参照してください。


プロパティの先頭には、適切なオブジェクトの種類 (ユーザーまたはデバイス) が付加されている必要があります。
次のルールは検証に失敗します: mail –ne null

適切なルールは、次のようになります。

user.mail –ne null

高度なルール本体の合計文字数が 2048 文字を超えないようにしてください。

> [!NOTE]
> 文字列演算と正規表現演算は、大文字と小文字が区別されません。
> 二重引用符 (") を含んだ文字列は、バック クォート文字 (`) でエスケープする必要があります (例: user.department -eq \`"Sales")。
> 引用符は文字列型の値にのみ使用し、また、英語の引用符のみを使用してください。
>
>

## <a name="supported-expression-rule-operators"></a>サポートされている式のルール演算子
次の表に、高度なルール本体で使用できる、サポートされているすべての式のルール演算子とその構文を示します。

| 演算子 | 構文 |
| --- | --- |
| 等しくない |-ne |
| 等しい |-eq |
| 指定値で始まらない |-notStartsWith |
| 指定値で始まる |-startsWith |
| 指定値を含まない |-notContains |
| 指定値を含む |-contains |
| 一致しない |-notMatch |
| 一致する |-match |
| イン | -in |
| 含まれない | -notIn |

## <a name="operator-precedence"></a>演算子の優先順位

すべての演算子を優先順位の低い順から並べると、-any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn となります。同じ行にある演算子は優先順位が同じです。

すべての演算子は、ハイフンのプレフィックスあり、またはなしで使用できます。

かっこは必ずしも必要ではなく、優先順位が要件を満たさない場合にのみかっこを追加する必要があることに注意してください。例:

   user.department –eq "Marketing" –and user.country –eq "US"

は以下に匹敵します。

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>-In および -notIn 演算子の使用

ユーザー属性の値を複数の異なる値に対して比較する場合は、-In または -notIn 演算子を使用できます。 -In 演算子を使用した例を次に示します。

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

値のリストの開始と終了に "[" と "]" を使用していることに注意してください。 この条件は、user.department の値がリスト内のいずれかの値に等しい場合に True に評価されます。

## <a name="query-error-remediation"></a>クエリ エラーの修復
次の表では、発生する可能性のあるエラーとその解決方法を示します。

| クエリ解析エラー | 間違った使用法 | 修正後 |
| --- | --- | --- |
| エラー: 属性がサポートされていません。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>プロパティは、[サポートされているプロパティの一覧](#supported-properties)のいずれかと一致している必要があります。 |
| エラー: 属性で演算子がサポートされていません。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/>プロパティはブール型です。 前掲の一覧からブール型でサポートされている演算子 (-eq または -ne) を使用してください。 |
| エラー: クエリ コンパイル エラー。 |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>論理演算子は、サポートされているプロパティ一覧のいずれかと一致している必要があります。正規表現では (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error。 |
| エラー: バイナリ式の形式が正しくありません。 |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>クエリにいくつかの誤りが存在します。 かっこの位置が正しくありません。 |
| エラー: 動的メンバーシップの設定中に不明なエラーが発生しました。 |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>クエリにいくつかの誤りが存在します。 かっこの位置が正しくありません。 |

## <a name="supported-properties"></a>サポートされているプロパティ
高度なルールで使用できるすべてのユーザー プロパティを次に示します。

### <a name="properties-of-type-boolean"></a>ブール型のプロパティ
使用可能な演算子

* -eq
* -ne

| プロパティ | 使用できる値 | 使用法 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>文字列型のプロパティ
使用可能な演算子

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| プロパティ | 使用できる値 | 使用法 |
| --- | --- | --- |
| city |任意の文字列値または $null |(user.city -eq "value") |
| country |任意の文字列値または $null |(user.country -eq "value") |
| companyName | 任意の文字列値または $null | (user.companyName -eq "value") |
| department |任意の文字列値または $null |(user.department -eq "value") |
| displayName |任意の文字列値 |(user.displayName -eq "value") |
| facsimileTelephoneNumber |任意の文字列値または $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任意の文字列値または $null |(user.givenName -eq "value") |
| jobTitle |任意の文字列値または $null |(user.jobTitle -eq "value") |
| mail |任意の文字列値または $null (ユーザーの SMTP アドレス) |(user.mail -eq "value") |
| mailNickName |任意の文字列値 (ユーザーのメール エイリアス) |(user.mailNickName -eq "value") |
| mobile |任意の文字列値または $null |(user.mobile -eq "value") |
| objectId |ユーザー オブジェクトの GUID |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | オンプレミスからクラウドに同期されたユーザーのオンプレミスのセキュリティ識別子 (SID)。 |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |なし DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration、DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任意の文字列値または $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任意の文字列値または $null |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 コード |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任意の文字列値または $null |(user.sipProxyAddress -eq "value") |
| state |任意の文字列値または $null |(user.state -eq "value") |
| streetAddress |任意の文字列値または $null |(user.streetAddress -eq "value") |
| surname |任意の文字列値または $null |(user.surname -eq "value") |
| telephoneNumber |任意の文字列値または $null |(user.telephoneNumber -eq "value") |
| usageLocation |2 文字の国コード |(user.usageLocation -eq "US") |
| userPrincipalName |任意の文字列値 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>文字列コレクション型のプロパティ
使用可能な演算子

* -contains
* -notContains

| プロパティ | 使用できる値 | 使用法 |
| --- | --- | --- |
| otherMails |任意の文字列値 |(user.otherMails -contains "alias@domain") |

| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>複数値プロパティ
使用可能な演算子

* -any (コレクション内の少なくとも 1 つの項目が条件に一致するときに満たされる)
* -all (コレクション内のすべての項目が条件に一致するときに満たされる)

| プロパティ | 値 | 使用法 |
| --- | --- | --- |
| assignedPlans |コレクション内の各オブジェクトは、capabilityStatus、service、servicePlanId の文字列プロパティを公開します。 |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

複数値プロパティは、同じ型のオブジェクトのコレクションです。 コレクション内の 1 つの項目またはすべての項目に条件を適用するには、それぞれ -any および -all 演算子を使用できます。 For example:

assignedPlans は、ユーザーに割り当てられたすべてのサービス プランをリストする複数値プロパティです。 次の式は、同様に [有効] 状態にある Exchange Online (プラン 2) サービス プランを持っているユーザーを選択します。

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Guid 識別子は、Exchange Online (プラン 2) サービス プランを識別します。)

> [!NOTE]
> これは、Office 365 (またはその他の Microsoft Online Service) 機能が有効になっているすべてのユーザーを (たとえば、特定のポリシー セットによって対象にするために) 識別する場合に役立ちます。

次の式は、Intune サービス (サービス名 "SCO" によって識別されます) に関連付けられた何らかのサービス プランを持っているすべてのユーザーを選択します。
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Null 値の使用

ルールで null 値を指定するには、null または $null を使用します。 例:

   user.mail –ne null is equivalent to user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>拡張属性とカスタム属性
拡張属性とカスタム属性は、動的なメンバーシップ ルールでサポートされます。

拡張属性はオンプレミスの Window Server AD から同期され、"ExtensionAttributeX" という形式です (X は 1 ～ 15)。
拡張属性を使用するルールの例を次に示します。

(user.extensionAttribute15 -eq "Marketing")

カスタム属性はオンプレミスの Windows Server AD または接続された SaaS アプリケーションから同期され、"user.extension_[GUID]\__[Attribute]" という形式です。[GUID] は AAD で属性を作成したアプリケーションの AAD での一意の識別子、[Attribute] は作成された属性の名前です。
カスタム属性を使用するルールの例を次に示します

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

カスタム属性名は、Graph Explorer を使用してユーザーの属性をクエリして属性名を検索することにより、ディレクトリで見つけることができます。 現在、オンプレミス Active Directory から同期されている複数値の属性はサポートされていません。 

## <a name="direct-reports-rule"></a>"直接の部下" のルール
マネージャーのすべての直接の部下が含まれたグループを作成できます。 将来、マネージャーの直接の部下が変更された場合、グループのメンバシップは自動的に調整されます。

> [!NOTE]
> 1. このルールを機能させるには、テナント内のユーザーについて **[Manager ID] \(マネージャー ID)** プロパティが正しく設定されていることを確認してください。 各ユーザーの現在の値は、そのユーザーの **[プロファイル] タブ**で確認できます。
> 2. このルールは、**直接の**部下のみをサポートします。 現在、入れ子になった階層のグループ (たとえば、直接の部下とその部下が含まれたグループ) を作成することはできません。

**グループを構成するには**

1. 「[高度なルールを作成するには](#to-create-the-advanced-rule)」のセクションの手順 1. ～ 5. に従い、**[Dynamic User] \(動的ユーザー)** の **[Membership type] \(メンバシップの種類)** を選択します。
2. **[Dynamic membership rules (動的メンバーシップのルール)]** ブレードで、次の構文を使用してルールを入力します。

    *Direct Reports for "{obectID_of_manager}"*

    有効なルールの例:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. ルールを保存した後、指定されたマネージャー ID 値を持つすべてのユーザーがグループに追加されます。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>属性を使用したデバイス オブジェクトのルールの作成
グループのメンバーシップのデバイス オブジェクトを選択するルールを作成することもできます。 次のデバイス属性を使用できます。

| プロパティ              | 使用できる値                  | 使用法                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| accountEnabled          | true false                      | (device.accountEnabled -eq true)                            |
| displayName             | 任意の文字列値                | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | 任意の文字列値                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | 任意の文字列値                | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | 任意の文字列値                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | 任意の文字列値                | (device.deviceManufacturer -eq "Microsoft")                 |
| deviceModel             | 任意の文字列値                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | 任意の文字列値                | (device.deviceOwnership -eq "")                             |
| domainName              | 任意の文字列値                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | 任意の文字列値                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false                      | (device.deviceOSType -eq true)                              |
| managementType          | 任意の文字列値                | (device.managementType -eq "")                              |
| organizationalUnit      | 任意の文字列値                | (device.organizationalUnit -eq "")                          |
| deviceId                | 有効なデバイス ID                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | 有効な AAD objectId            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> Azure クラシック ポータルの "単純なルール" のドロップダウンを使用してこれらのデバイス ルールを作成することはできません。
>
>

## <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory に関する追加情報を示します。

* [グループの動的メンバーシップのトラブルシューティング](active-directory-accessmanagement-troubleshooting.md)
* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

