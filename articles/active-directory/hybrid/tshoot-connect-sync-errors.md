---
title: 'Azure AD Connect: 同期中のエラーのトラブルシューティング | Microsoft Docs'
description: Azure AD Connect の同期中に発生したエラーのトラブルシューティングを行う方法を説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d976cd924644828f5861e4c54460a8b4e4f81444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643866"
---
# <a name="troubleshooting-errors-during-synchronization"></a>同期中のエラーのトラブルシューティング
エラーが発生する可能性があるのは、Windows Server Active Directory (AD DS) と Azure Active Directory (Azure AD) で ID データが同期されているときです。 この記事では、さまざまな種類の同期エラーの概要、これらのエラーを引き起こすシナリオ、エラーを修正する方法について説明します。 この記事では一般的なエラーの種類を取り上げます。発生する可能性があるすべてのエラーについて説明するものではありません。

 この記事では、読者に [Azure AD と Azure AD Connect の設計概念](plan-connect-design-concepts.md)の知識があることを想定しています。

Azure AD Connect の最新バージョン \(2016 年 8 月以降\) では、同期エラーのレポートは [Azure portal](https://aka.ms/aadconnecthealth) で Azure AD Connect Health for sync の一部として提供されます。

2016 年 9 月 1 日以降、[Azure Active Directory Duplicate Attribute Resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) 機能がすべての "*新しい*" Azure Active Directory テナントに対して既定で有効になります。 この機能は既存のテナントについても数か月のうちに自動的に有効になります。

Azure AD Connect は、同期を保つディレクトリに対して 3 種類の操作 (インポート、同期、エクスポート) を実行します。 エラーは、どの操作でも発生する可能性があります。 この記事では、主に、Azure AD へのエクスポート中のエラーについて説明します。

## <a name="errors-during-export-to-azure-ad"></a>Azure AD へのエクスポート中のエラー
この後のセクションで、Azure AD コネクタを使用した Azure AD へのエクスポート操作中に発生するさまざまな種類の同期エラーについて説明します。 このコネクタは、"contoso.*onmicrosoft.com*" という形式の名前で識別されます。
Azure AD へのエクスポート中のエラーは、Azure AD Connect \(同期エンジン\) が Azure Active Directory に対して試行した操作 \(追加、更新、削除など\) が失敗したことを意味します。

![エクスポート エラーの概要](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>データの不一致エラー
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>説明
* Azure AD Connect \(同期エンジン\) がオブジェクトの追加または更新を Azure Active Directory に指示すると、Azure AD は、受け取るオブジェクトの **sourceAnchor** 属性を使用して、Azure AD 内のオブジェクトの **immutableId** 属性と照合します。 このように照合されたものは、**完全一致** と呼ばれます。
* Azure AD で受け取るオブジェクトの **sourceAnchor** 属性と **immutableId** 属性が一致するオブジェクトが **見つからない** 場合、新しいオブジェクトをプロビジョニングする前に、代替策として ProxyAddresses 属性と UserPrincipalName 属性を使用して一致するものを見つけようとします。 このように照合されたものは、**あいまい一致** と呼ばれます。 あいまい一致の目的は、既に Azure AD に存在するオブジェクト (Azure AD に元から存在する) と、同期中に追加または更新される新しいオブジェクト(オンプレミスで同じエンティティ (ユーザー、グループ) を表す) を一致させることです。
* **InvalidSoftMatch** エラーが発生するのは、完全一致で一致するオブジェクトが見つからない場合 **かつ**、あいまい一致によって一致するオブジェクトが見つかるが、そのオブジェクトの *immutableId* の値が受け取るオブジェクトの *SourceAnchor* と異なる場合です。このエラーは、一致するオブジェクトが、オンプレミス Active Directory の別のオブジェクトと同期されたことを示します。

つまり、あいまい一致が機能するためには、あいまい一致の対象となるオブジェクトに *immutableId* の値があってはなりません。 *immutableId* に値が設定されているオブジェクトで完全一致が行われないが、あいまい一致の基準を満たす場合は、操作が InvalidSoftMatch 同期エラーで終了します。

Azure Active Directory スキーマでは、次の属性について複数のオブジェクトが同じ値を持つことはできません。 \(これは包括的なリストではありません。\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Azure AD Attribute Duplicate Attribute Resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) 機能は、Azure Active Directory の既定動作としても展開されます。  これにより、オンプレミス AD 環境に存在する重複した ProxyAddresses 属性や UserPrincipalName 属性の処理において、Azure AD の復元力が向上し、Azure AD Connect (および他の同期クライアント) によって検出される同期エラーの数が減少します。 この機能では重複エラーは修正されません。 したがって、データの修正は必要です。 ただし、新しいオブジェクトのプロビジョニングは許可されます (この機能がない場合には、Azure AD に重複値があるとプロビジョニングが禁止されます)。 これにより、同期クライアントに返される同期エラーの数も減少します。
> テナントでこの機能が有効になっている場合、新しいオブジェクトのプロビジョニング中に InvalidSoftMatch 同期エラーは生成されません。
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>InvalidSoftMatch のシナリオ例
1. ProxyAddresses 属性の値が同じ複数のオブジェクトがオンプレミス Active Directory に存在します。 1 つだけが Azure AD でプロビジョニングされます。
2. userPrincipalName 属性の値が同じ複数のオブジェクトがオンプレミス Active Directory に存在します。 1 つだけが Azure AD でプロビジョニングされます。
3. あるオブジェクトがオンプレミス Active Directory に追加され、その ProxyAddresses 属性の値が Azure Active Directory の既存のオブジェクトのものと同じです。 オンプレミスに追加されたオブジェクトは、Azure Active Directory でプロビジョニングされません。
4. あるオブジェクトがオンプレミス Active Directory に追加され、その userPrincipalName 属性の値が Azure Active Directory の既存のアカウントのものと同じです。 そのオブジェクトは、Azure Active Directory でプロビジョニングされません。
5. 同期されたアカウントがフォレスト A からフォレスト B に移動されました。Azure AD Connect (同期エンジン) は ObjectGUID 属性を使用して、SourceAnchor を計算しました。 フォレストの移動の後で、SourceAnchor の値は変わります。 新しいオブジェクト (フォレスト B) は、Azure AD の既存のオブジェクトと同期できません。
6. 同期されたオブジェクトが誤ってオンプレミス Active Directory から削除されました。その後、Azure Active Directory でアカウントを削除せずに、同じエンティティ (ユーザーなど) のために新しいオブジェクトが Active Directory に作成されました。 新しいアカウントは、既存の Azure AD オブジェクトと同期できません。
7. Azure AD Connect がアンインストールされ、再インストールされました。 再インストール時に、別の属性が SourceAnchor として選択されました。 以前同期されていたすべてのオブジェクトは、InvalidSoftMatch エラーにより同期を停止しました。

#### <a name="example-case"></a>事例:
1. **Bob Smith** は、オンプレミス Active Directory (*contoso.com*) と同期されている、Azure Active Directory のユーザーです。
2. Bob Smith の **UserPrincipalName** は **bobs\@contoso.com** として設定されています。
3. **"abcdefghijklmnopqrstuv=="** は **SourceAnchor** です。これは、オンプレミス Active Directory の Bob Smith の **objectGUID** (すなわち Azure Active Directory の Bob Smith の **immutableId**) を使用して Azure AD Connect によって計算されます。
4. Bob には、**proxyAddresses** 属性に次の値もあります。
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob\@contoso.com**
5. 新しいユーザー **Bob Taylor** がオンプレミス Active Directory に追加されます。
6. Bob Taylor の **UserPrincipalName** は **bobt\@contoso.com** として設定されています。
7. **"abcdefghijkl0123456789==""** は **sourceAnchor** です。これは、オンプレミス Active Directory の Bob Taylor の **objectGUID** を使用して Azure AD Connect によって計算されます。 Bob Taylor のオブジェクトはまだ Azure Active Directory に同期されません。
8. Bob Taylor には、proxyAddresses 属性に次の値もあります。
   * smtp: bobt@contoso.com
   * smtp: bob.taylor@contoso.com
   * **smtp: bob\@contoso.com**
9. 同期中に、Azure AD Connect はオンプレミス Active Directory への Bob Taylor の追加を認識し、同じ変更を行うように Azure AD に依頼します。
10. Azure AD は、最初に完全一致を実行します。 つまり、immutableId が "abcdefghijkl0123456789==" と等しいオブジェクトがないかどうか検索します。 Azure AD の他のオブジェクトにはその immutableId がないため、完全一致は失敗します。
11. 次に、Azure AD は、Bob Taylor のあいまい一致を試みます。 つまり、proxyAddresses が smtp: bob@contoso.com など 3 つの値と等しいオブジェクトがないか検索します。
12. Azure AD は、あいまい一致条件を満たす Bob Smith のオブジェクトを見つけます。 このオブジェクトの immutableId の値は "abcdefghijklmnopqrstuv==" です。 これは、このオブジェクトがオンプレミスの Active Directory の別のオブジェクトから同期されたことを示します。 したがって、Azure AD はこれらのオブジェクトのあいまい一致を行うことができず、**InvalidSoftMatch** 同期エラーが発生します。

#### <a name="how-to-fix-invalidsoftmatch-error"></a>InvalidSoftMatch エラーを修正する方法
InvalidSoftMatch エラーの最も一般的な理由は、2 つのオブジェクトの異なる SourceAnchor \(immutableId\) の ProxyAddresses 属性または UserPrincipalName 属性 (あるいは両方) の値が同じであることです。これらの属性が、Azure AD でのあいまい一致プロセスで使用されます。 無効なあいまい一致を修正するには、次の手順に従います。

1. エラーの原因となった、重複する proxyAddresses 属性、userPrincipalName 属性、または他の属性の値を特定します。 また、どの 2 つ \(以上\) のオブジェクトが競合しているかも特定します。 [Azure AD Connect Health for sync](./how-to-connect-health-sync.md) によって生成されるレポートが、2 つのオブジェクトを特定するために役立ちます。
2. 重複した値をそのまま使用するオブジェクトと、使用すべきでないオブジェクトを決めます。
3. 重複する値を、その値を使用すべきでないオブジェクトから削除します。 そのオブジェクトのソースであるディレクトリで、この変更を行う必要があります。 場合によっては、競合しているオブジェクトの 1 つを削除する必要があります。
4. オンプレミス AD で変更を行った場合は、Azure AD Connect で変更を同期します。

Azure AD Connect Health for sync の同期エラーレポートは 30 分ごとに更新され、最新の同期試行のエラーが含まれます。

> [!NOTE]
> ImmutableId は、名前が示すとおり、オブジェクトの存続期間中に変更すべきではありません。 Azure AD Connect が上記のリストのシナリオを念頭に置いて構成されてなかった場合は、同じエンティティ (同じユーザー、グループ、連絡先など) を表す AD オブジェクトについて Azure AD Connect が異なる SourceAnchor 値を計算し、Azure AD オブジェクトを引き続き使用する必要があるという状況が生じることがあります。
>
>

#### <a name="related-articles"></a>関連記事
* [Microsoft 365 でのディレクトリ同期を妨げる重複または無効な属性に関する記事](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>説明
Azure AD が 2 つのオブジェクトのあいまい一致を試行するとき、"オブジェクトの種類" (ユーザー、グループ、連絡先など) が異なる 2 つのオブジェクトで、あいまい一致の実行に使用される属性の値が同一である場合があります。 これらの属性の重複は Azure AD では許可されないため、この操作は "ObjectTypeMismatch" 同期エラーで終了します。

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>ObjectTypeMismatch エラーのシナリオ例
* メール対応セキュリティ グループが Microsoft 365 で作成されます。 管理者は、ProxyAddresses 属性の値が Microsoft 365 グループと同じ新しいユーザーまたは連絡先をオンプレミス AD に追加します (まだ Azure AD に同期されません)。

#### <a name="example-case"></a>事例
1. 管理者が、税部門のために新しいメール対応セキュリティ グループを Microsoft 365 に作成し、電子メール アドレスを tax@contoso.com と設定します。 このグループには、**smtp: tax\@contoso.com** の ProxyAddresses 属性値が割り当てられています。
2. 新しいユーザーが Contoso.com に加わり、そのユーザーのアカウントが proxyAddress を **smtp: tax\@contoso.com** としてオンプレミスに作成されます。
3. Azure AD Connect が新しいユーザー アカウントを同期するとき、"ObjectTypeMismatch" エラーが生成されます。

#### <a name="how-to-fix-objecttypemismatch-error"></a>ObjectTypeMismatch エラーを修正する方法
ObjectTypeMismatch エラーの最も一般的な原因は、異なる種類 (ユーザー、グループ、連絡先など) の 2 つのオブジェクトの ProxyAddresses 属性の値が同じであることです。 ObjectTypeMismatch を修正するには:

1. エラーの原因となった、重複する proxyAddresses 属性 (または他の属性) の値を特定します。 また、どの 2 つ \(以上\) のオブジェクトが競合しているかも特定します。 [Azure AD Connect Health for sync](./how-to-connect-health-sync.md) によって生成されるレポートが、2 つのオブジェクトを特定するために役立ちます。
2. 重複した値をそのまま使用するオブジェクトと、使用すべきでないオブジェクトを決めます。
3. 重複する値を、その値を使用すべきでないオブジェクトから削除します。 そのオブジェクトのソースであるディレクトリで、この変更を行う必要があることに注意してください。 場合によっては、競合しているオブジェクトの 1 つを削除する必要があります。
4. オンプレミス AD で変更を行った場合は、Azure AD Connect で変更を同期します。 Azure AD Connect Health for sync の同期エラーレポートは 30 分ごとに更新され、最新の同期試行のエラーが含まれます。

## <a name="duplicate-attributes"></a>重複する属性
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>説明
Azure Active Directory スキーマでは、次の属性について複数のオブジェクトが同じ値を持つことはできません。 つまり、Azure AD の各オブジェクトはこれらの属性について常に一意の値を持つように強制されます。

* ProxyAddresses
* UserPrincipalName

Azure AD Connect が新しいオブジェクトの追加または既存のオブジェクトの更新を試行したときに、上記の属性の値が Azure Active Directory 内の別のオブジェクトに既に割り当てられていた場合、その操作は "AttributeValueMustBeUnique" 同期エラーで終了します。

#### <a name="possible-scenarios"></a>考えられるシナリオ:
1. 重複する値が、既に同期済みのオブジェクトに割り当てられます。これによって、別の同期オブジェクトとの競合が発生します。

#### <a name="example-case"></a>事例:
1. **Bob Smith** は、オンプレミス Active Directory (contoso.com) と同期されている、Azure Active Directory のユーザーです。
2. Bob Smith のオンプレミスでの **UserPrincipalName** は **bobs\@contoso.com** として設定されています。
3. Bob には、**proxyAddresses** 属性に次の値もあります。
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob\@contoso.com**
4. 新しいユーザー **Bob Taylor** がオンプレミス Active Directory に追加されます。
5. Bob Taylor の **UserPrincipalName** は **bobt\@contoso.com** として設定されています。
6. **Bob Taylor** には、**ProxyAddresses** 属性に次の値もあります。i. smtp: bobt@contoso.com ii. smtp: bob.taylor@contoso.com
7. Bob Taylor のオブジェクトは Azure AD と正常に同期されます。
8. 管理者は Bob Taylor の **ProxyAddresses** 属性を次の値で更新することにしました。i. **smtp: bob\@contoso.com**
9. Azure AD は、Bob Taylor の Azure AD 内のオブジェクトを上記の値で更新しようとしますが、この ProxyAddresses 値は既に Bob Smith に割り当てられているため、操作は "AttributeValueMustBeUnique" エラーで失敗します。

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>AttributeValueMustBeUnique エラーを修正する方法
AttributeValueMustBeUnique エラーの最も一般的な理由は、2 つのオブジェクトの異なる SourceAnchor \(immutableId\) の ProxyAddresses 属性または UserPrincipalName 属性 (あるいは両方) の値が同じであることです。 AttributeValueMustBeUnique エラーを修正するには、次の手順に従います。

1. エラーの原因となった、重複する proxyAddresses 属性、userPrincipalName 属性、または他の属性の値を特定します。 また、どの 2 つ \(以上\) のオブジェクトが競合しているかも特定します。 [Azure AD Connect Health for sync](./how-to-connect-health-sync.md) によって生成されるレポートが、2 つのオブジェクトを特定するために役立ちます。
2. 重複した値をそのまま使用するオブジェクトと、使用すべきでないオブジェクトを決めます。
3. 重複する値を、その値を使用すべきでないオブジェクトから削除します。 そのオブジェクトのソースであるディレクトリで、この変更を行う必要があることに注意してください。 場合によっては、競合しているオブジェクトの 1 つを削除する必要があります。
4. オンプレミス AD で変更を行った場合は、エラーを修正するために Azure AD Connect で変更を同期します。

#### <a name="related-articles"></a>関連記事
-[Microsoft 365 でのディレクトリ同期を妨げる重複または無効な属性に関する記事](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>データ検証の失敗
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>説明
Azure Active Directory は、データそのものにさまざまな制約を適用した上で、ディレクトリへのデータの書き込みを許可します。 これらの制限により、そのようなデータに依存するアプリケーションをエンド ユーザーが使用する際に、最適なエクスペリエンスを得ることができます。

#### <a name="scenarios"></a>シナリオ
a. UserPrincipalName 属性値に無効な文字またはサポートされていない文字が含まれています。
b. UserPrincipalName 属性が必要な形式ではありません。

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>IdentityDataValidationFailed エラーを修正する方法
a. userPrincipalName 属性の文字がサポートされており、必要な形式であることを確認します。

#### <a name="related-articles"></a>関連記事
* [Microsoft 365 へのディレクトリ同期を通してユーザーをプロビジョニングするための準備](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>説明
これにより、ユーザーの UserPrincipalName のサフィックスがあるフェデレーション ドメインから別のフェデレーション ドメインに変更された場合に **"FederatedDomainChangeError"** 同期エラーが発生することになります。

#### <a name="scenarios"></a>シナリオ
同期されたユーザーで、オンプレミスの UserPrincipalName のサフィックスが、あるフェデレーション ドメインから別のフェデレーション ドメインに変更されました。 たとえば、*UserPrincipalName = bob\@contoso.com* が *UserPrincipalName = bob\@fabrikam.com* に変更されました。

#### <a name="example"></a>例
1. Contoso.com のアカウントである Bob Smith が、新しいユーザーとして UserPrincipalName bob@contoso.com で Active Directory に追加されます。
2. Bob が Contoso.com の別の部門 Fabrikam.com に移動し、UserPrincipalName が bob@fabrikam.com に変更されます。
3. contoso.com ドメインと fabrikam.com ドメインは両方とも Azure Active Directory のフェデレーション ドメインです。
4. Bob の userPrincipalName は更新されず、"FederatedDomainChangeError" 同期エラーが発生します。

#### <a name="how-to-fix"></a>修正方法
ユーザーの UserPrincipalName サフィックスが bob@**contoso.com** から bob\@**fabrikam.com** に更新され、**contoso.com** と **fabrikam.com** のどちらも **フェデレーション ドメイン** の場合、次の手順に従って同期エラーを修正します。

1. Azure AD 内のユーザーの UserPrincipalName を bob@contoso.com から bob@contoso.onmicrosoft.com に更新します。 次の PowerShell コマンドを Azure AD PowerShell Module で使用できます。`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. 次の同期サイクルで同期の試行を許可します。 このとき、同期が成功して、Bob の UserPrincipalName が予期したとおり bob@fabrikam.com に更新されます。

#### <a name="related-articles"></a>関連記事
* [異なるフェデレーション ドメインを使用するようにユーザー アカウントの UPN を変更した後、Azure Active Directory 同期ツールによって変更が同期されない](./howto-troubleshoot-upn-changes.md)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>説明
Azure Active Directory スキーマで設定されている、使用可能なサイズ制限、長さの制限、または個数制限を属性が超過すると、同期操作は **LargeObject** または **ExceededAllowedLength** 同期エラーで終了します。 通常、このエラーは次の属性について発生します。

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>考えられるシナリオ
1. Bob の userCertificate 属性に格納されている、Bob に割り当てられた証明書の数が多すぎます。 期限切れの古い証明書が含まれている可能性があります。 ハード制限は、証明書 15 個です。 userCertificate 属性による LargeObject エラーを処理する方法の詳細については、[userCertificate 属性による LargeObject エラーの処理](tshoot-connect-largeobjecterror-usercertificate.md)に関する記事をご覧ください。
2. Bob の userSMIMECertificate 属性に格納されている、Bob に割り当てられた証明書の数が多すぎます。 期限切れの古い証明書が含まれている可能性があります。 ハード制限は、証明書 15 個です。
3. Active Directory で設定された Bob の thumbnailPhoto が大きすぎ、Azure AD で同期できません。
4. Active Directory での ProxyAddresses 属性の自動作成時に、オブジェクトに割り当てられた ProxyAddresses が多すぎます。

### <a name="how-to-fix"></a>修正方法
1. エラーを引き起こした属性が、許可されている制限内になるようにします。

## <a name="existing-admin-role-conflict"></a>既存の管理者ロールの競合

### <a name="description"></a>説明
**既存の管理者ロールの競合** は、ユーザー オブジェクトに次のものがある場合に、同期中にそのユーザー オブジェクトに対して発生します。

- 管理権限、および
- 既存の Azure AD オブジェクトと同じ UserPrincipalName

Azure AD Connect は、オンプレミスの AD からのユーザー オブジェクトと、管理ロールが割り当てられている Azure AD 内のユーザー オブジェクトとのあいまい一致は行えません。  詳細については、「[Azure AD の UserPrincipalName の設定](plan-connect-userprincipalname.md)」を参照してください

![既存の管理者](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>修正方法
この問題を解決するには、次の操作を行ってください。

1. Azure AD アカウント (所有者) をすべての管理者ロールから削除する。 
2. 検疫済みオブジェクトをクラウドから **物理的に削除** する。 
3. クラウド アカウントに対するオンプレミス ユーザーのあいまい一致を次の同期サイクルで対処します。(クラウド ユーザーはグローバル GA ではなくなっているため)。 
4. 所有者のロールのメンバーシップを復元する。 

>[!NOTE]
>オンプレミスのユーザー オブジェクトと Azure AD ユーザー オブジェクトの間のあいまい一致が完了した後、管理ロールを既存のユーザー オブジェクトに再度割り当てることができます。

## <a name="related-links"></a>関連リンク
* [Locate Active Directory Objects in Active Directory Administrative Center (Active Directory 管理センターで Active Directory オブジェクトを見つける)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Azure Active Directory PowerShell を使用してオブジェクトについて Azure Active Directory を照会する方法に関する記事](/previous-versions/azure/jj151815(v=azure.100))