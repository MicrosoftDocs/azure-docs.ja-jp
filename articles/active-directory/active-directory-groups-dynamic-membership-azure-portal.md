---
title: "Azure Active Directory の属性ベースの動的グループ メンバーシップ | Microsoft Docs"
description: "サポートされている式のルールの演算子とパラメーターを含む、動的グループ メンバーシップの高度なルールを作成する方法。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 44748f3152718f3cec348d7e2bdccdbe0f79091e
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Azure Active Directory で動的グループ メンバーシップの属性ベースのルールを作成する
Azure Active Directory (Azure AD) では、グループの複雑な属性ベースの動的メンバーシップを有効にする高度なルールを作成できます。 この記事では、ユーザーまたはデバイスについて動的なメンバーシップ ルールを作成するための属性と構文について詳しく説明します。

ユーザーまたはデバイスのいずれかの属性が変更されると、システムはディレクトリ内のすべての動的なグループ ルールを評価して、この変更によってグループの追加または削除がトリガーされるかどうかを確認します。 ユーザーまたはデバイスがグループのルールを満たしている場合、それらはそのグループのメンバーとして追加されます。 ルールを満たさなくなると、削除されます。

> [!NOTE]
> - セキュリティ グループまたは Office 365 グループには、動的メンバーシップのルールを設定できます。
>
> - この機能を使うには、少なくとも 1 つの動的グループに追加される各ユーザー メンバーに Azure AD Premium P1 ライセンスが必要です。
>
> - デバイスまたはユーザーの動的グループは作成できても、ユーザー オブジェクトとデバイス オブジェクトの両方を含むルールは作成できません。

> - 現時点では、ユーザーの属性の所有に基づいてデバイス グループを作成することはできません。 デバイスのメンバーシップのルールは、ディレクトリ内のデバイス オブジェクトの直接の属性のみを参照できます。

## <a name="to-create-an-advanced-rule"></a>高度なルールを作成するには
1. グローバル管理者またはユーザー アカウントの管理者であるアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. **[ユーザーとグループ]** を選択します。
3. **[すべてのグループ]** を選択します。

   ![グループ ブレードを開く](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)
4. **[すべてグループ]** で、**[新しいグループ]** を選択します。

   ![新しいグループを追加する](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)
5. **[グループ]** ブレードで、新しいグループの名前と説明を入力します。 **[メンバーシップの種類]** で、ユーザーとデバイスのどちらのルールを作成するかに応じて、**[動的ユーザー]** または **[動的デバイス]** を選択し、**[動的クエリの追加]** をクリックします。 デバイスのルールに使用する属性については、「 [属性を使用したデバイス オブジェクトのルールの作成](#using-attributes-to-create-rules-for-device-objects)」をご覧ください。

   ![動的メンバーシップのルールを追加する](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)
6. **[動的メンバーシップ ルール]** ブレードで、**[動的メンバーシップの高度な規則の追加]** ボックスにルールを入力し、ブレードの下部にある **[作成]** をクリックします。
7. **[作成]** on the **[グループ]** をクリックして、グループを作成します。

## <a name="constructing-the-body-of-an-advanced-rule"></a>高度なルール本体の作成
グループの動的なメンバーシップ管理を目的として作成される高度なルールは基本的に、3 つの構成要素から成る、true または false を結果として返す 2 項演算式です。 その 3 つの構成要素を次に示します。

* 左辺のパラメーター
* 2 項演算子
* 右辺の定数

完全な高度なルールは (leftParameter binaryOperator "RightConstant") のようになります。ここで、左かっこと右かっこは 2 項演算式全体に対して省略可能であり、二重引用符も省略可能で (右辺の定数が文字列の場合にのみその定数に必要)、左辺のパラメーターの構文は user.property です。 高度なルールは、複数の 2 項演算式を論理演算子 (-and、-or、-not) で組み合わせることができます。

以下に示したのは、正しい構文に沿って作成された高度なルールの例です。
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
サポートされているパラメーターと式のルール演算子の全一覧については、以降のセクションを参照してください。 デバイスのルールに使用する属性については、「 [属性を使用したデバイス オブジェクトのルールの作成](#using-attributes-to-create-rules-for-device-objects)」をご覧ください。

高度なルール本体の合計文字数が 2048 文字を超えないようにしてください。

> [!NOTE]
> 文字列演算と正規表現演算は、大文字と小文字が区別されません。 定数に $null を使用することで Null チェックを実行することもできます (例: user.department -eq $null)。
> 二重引用符 (") を含んだ文字列は、バック クォート文字 (`) でエスケープする必要があります (例: user.department -eq \`"Sales")。

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

すべての演算子を優先順位の低い順に以下に示します。 同じ行にある演算子の優先順位は同じです。
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
すべての演算子は、ハイフンのプレフィックスあり、またはなしで使用できます。 優先順位が要件を満たさない場合にのみ、かっこを追加する必要があります。
For example:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
は以下に匹敵します。
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>-In および -notIn 演算子の使用

ユーザー属性の値を複数の異なる値に対して比較する場合は、-In または -notIn 演算子を使用できます。 -In 演算子を使用した例を次に示します。
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
値のリストの開始と終了に "[" と "]" を使用していることに注意してください。 この条件は、user.department の値がリスト内のいずれかの値に等しい場合に True に評価されます。


## <a name="query-error-remediation"></a>クエリ エラーの修復
次の表では、一般的なエラーとその解決方法を示します。

| クエリ解析エラー | 間違った使用法 | 修正後 |
| --- | --- | --- |
| エラー: 属性がサポートされていません。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>該当する属性が、[サポートされているプロパティ一覧](#supported-properties)に記載されていることを確認してください。 |
| エラー: 属性で演算子がサポートされていません。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>プロパティの型に対してサポートされていない演算子が使用されています (この例では、-contains をブール型で使用することはできません)。 プロパティの型に合った適切な演算子を使用してください。 |
| エラー: クエリ コンパイル エラー。 |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1.演算子が不足しています。 結合述語を 2 つ使用してください (-and または -or)。<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2. -match に使用されている正規表現に誤りがあります。<br/><br/>(user.userPrincipalName -match ".*@domain.ext") または (user.userPrincipalName -match "@domain.ext$")|

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
```
   user.mail –ne null
```
は次と同等
```
   user.mail –ne $null
   ```

## <a name="extension-attributes-and-custom-attributes"></a>拡張属性とカスタム属性
拡張属性とカスタム属性は、動的なメンバーシップ ルールでサポートされます。

拡張属性はオンプレミスの Window Server AD から同期され、"ExtensionAttributeX" という形式です (X は 1 ～ 15)。
拡張属性を使用するルールの例を次に示します。
```
(user.extensionAttribute15 -eq "Marketing")
```
カスタム属性はオンプレミスの Windows Server AD または接続された SaaS アプリケーションから同期され、"user.extension_[GUID]\__[Attribute]" という形式です。[GUID] は AAD で属性を作成したアプリケーションの AAD での一意の識別子、[Attribute] は作成された属性の名前です。
カスタム属性を使用するルールの例を次に示します
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
カスタム属性名は、Graph Explorer を使用してユーザーの属性をクエリして属性名を検索することにより、ディレクトリで見つけることができます。

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

 デバイス属性  | 値 | 例
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | 任意の文字列値 |(device.displayName -eq "Rob Iphone”)
 deviceOSType | 任意の文字列値 | (device.deviceOSType -eq "IOS")
 deviceOSVersion | 任意の文字列値 | (device.OSVersion -eq "9.1")
 deviceCategory | 有効なデバイス カテゴリ名 | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | 任意の文字列値 | (device.deviceManufacturer -eq "Samsung")
 deviceModel | 任意の文字列値 | (device.deviceModel -eq "iPad Air")
 deviceOwnership | 個人、会社、不明 | (device.deviceOwnership -eq "Company")
 domainName | 任意の文字列値 | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Apple Device Enrollment Profile 名 | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (モバイル デバイスの場合)<br>PC (Intune PC エージェントによって管理されるコンピューターの場合) | (device.managementType -eq "MDM")
 organizationalUnit | オンプレミスの Active Directory で設定される組織単位の名前と一致する任意の文字列値 | (device.organizationalUnit -eq "US PCs")
 deviceId | 有効な Azure AD デバイス ID | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | 有効な Azure AD オブジェクト ID |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>動的なメンバーシップを静的に変更する、またはその逆の変更を行う
グループのメンバーシップの管理方法を変更することができます。 これは、システムで同じグループの名前と ID を保持する場合に便利です。グループへの既存の参照は有効のままであるため、新しいグループを作成する場合にそれらの参照を更新する必要がありません。

この機能をサポートするように Azure Portal の更新を行っているところです。 当面は、[Azure クラシック ポータル](https://manage.windowsazure.com)(手順は[こちら](active-directory-accessmanagement-groups-with-advanced-rules.md#changing-dynamic-membership-to-static-and-vice-versa)) を使用するか、または次に示すように PowerShell コマンドレットを使用してください。

> [!WARNING]
> 既存の静的グループを動的なグループに変更すると、既存のすべてのメンバーはグループから削除されてから、新しいメンバーを追加するためにメンバーシップ ルールが処理されます。 アプリまたはリソースへのアクセスを制御するためにグループが使用されている場合、元のメンバーは、メンバーシップ ルールが完全に処理されるまでアクセスできなくなる可能性があります。
>
> グループの新しいメンバーシップが予期したとおりのものになるように、事前に新しいメンバーシップ ルールをテストすることをお勧めします。

**PowerShell を使用してグループに対するメンバーシップの管理を変更する**

> [!NOTE]
> 動的なグループ プロパティを変更するには、[Azure AD PowerShell バージョン 2](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) のコマンドレットを使用する必要があります。
>
> 現時点では、ライブラリの最新のプレビュー バージョンのみに、必要なコマンドレットが含まれています。 [こちら](https://www.powershellgallery.com/packages/AzureADPreview)からインストールできます。

既存のグループにおいてメンバーシップの管理を切り替える関数を次に示します。 GroupTypes プロパティを正しく操作し、動的なメンバーシップに関係のない値がそこに存在する場合はその値を保持するようにしてください。

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
グループを静的にするには:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
グループを動的にするには:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory のグループに関する追加情報を提供します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)

