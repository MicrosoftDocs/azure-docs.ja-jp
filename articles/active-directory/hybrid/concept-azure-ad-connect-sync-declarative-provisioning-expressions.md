---
title: 'Azure AD Connect: 宣言型プロビジョニングの式 | Microsoft Docs'
description: 宣言型のプロビジョニングの式について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02490839a9e35695ae2e8b3f750e139ad7413aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280215"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect Sync: 宣言型のプロビジョニングの式について
Azure AD Connect Sync は、Forefront Identity Manager 2010 で初めて導入された宣言型のプロビジョニングに基づいています。 これにより、コンパイル済みコードを記述することなく完全な ID 統合ビジネス ロジックを実装できます。

宣言型のプロビジョニングの不可欠な要素は、属性フローに使用される式言語です。 使用される言語は、Microsoft ® Visual Basic ® for Applications (VBA) のサブセットです。 この言語は、Microsoft Office で使用され、VBScript の経験を持つユーザーも理解できます。 宣言型のプロビジョニングの式言語は、関数を使用するだけであり、構造化言語ではありません。 メソッドやステートメントはありません。 代わりに、関数を入れ子にして、プログラム フローを記述します。

詳細については、「 [Office 2013 Visual Basic for Applications 言語リファレンスへようこそ](/office/vba/api/overview/language-reference)」を参照してください。

属性は厳密に型指定されます。 関数は、正しい型の属性のみを受け取ります。 また、大文字と小文字が区別されます。 関数名と属性名のいずれも、適切に大文字と小文字を使用する必要があります。そのようにしない場合、エラーがスローされます。

## <a name="language-definitions-and-identifiers"></a>言語の定義と識別子
* 関数は、名前の後にかっこで囲まれた引数が続きます。たとえば、FunctionName(argument 1,argument N) などです。
* 属性は、角かっこで識別されます。たとえば、[attributeName] などです。
* パラメーターは、パーセント記号で識別されます。たとえば、%ParameterName% などです。
* 文字列定数は、引用符で囲まれます。たとえば、"Contoso" などです (注: 左右区別がある引用符 “” ではなく、左右区別がない引用符 "" を使用する必要があります)。
* 数値は、引用符を使用しないで表され、10 進数であることが求められます。 16 進数の値には、&H がプレフィックスとして付加されます。 たとえば、98052、&HFF などです。
* ブール値は、定数 (True、False) で表されます。
* 組み込み定数とリテラルは、その名前のみで表されます。たとえば、NULL、CRLF、IgnoreThisFlow などです。

### <a name="functions"></a>関数
宣言型のプロビジョニングでは、属性値を変換する機能を持つ多くの関数を使用します。 これらの関数を入れ子にすると、ある関数の結果を別の関数に渡すことができます。

`Function1(Function2(Function3()))`

全関数の一覧については、 [関数参照](reference-connect-sync-functions-reference.md)を参照してください。

### <a name="parameters"></a>パラメーター
パラメーターは、コネクタによって定義されるか、PowerShell を使用する管理者によって定義されます。 通常、パラメーターには、ユーザーが属しているドメインの名前など、システムによって異なる値が含まれます。 これらのパラメーターは属性フローで使用できます。

Active Directory Connector は、受信同期ルールについて次のパラメーターを提供しています。

| パラメーター名 | 解説 |
| --- | --- |
| Domain.Netbios |現在インポートされているドメインの NetBIOS 形式 (FABRIKAMSALES など) |
| Domain.FQDN |現在インポートされているドメインの FQDN 形式 (sales.fabrikam.com など) |
| Domain.LDAP |現在インポートされているドメインの LDAP 形式 (DC=sales,DC=fabrikam,DC=com など) |
| Forest.Netbios |現在インポートされているフォレスト名の NetBIOS 形式 (FABRIKAMCORP など) |
| Forest.FQDN |現在インポートされているフォレスト名の FQDN 形式 (fabrikam.com など) |
| Forest.LDAP |現在インポートされているフォレスト名の LDAP 形式 (DC=fabrikam,DC=com など) |

システムには次のパラメーターが用意されています。現在実行中のコネクタの識別子を取得するために使用されます。  
`Connector.ID`

ユーザーが属しているドメインの NetBIOS 名をメタバース属性ドメインに取り込む例を次に示します。  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>オペレーター
次の演算子を使用できます。

* **比較**: <、<=、<>、=、>、>=
* **算術**: +、-、\*、-
* **文字列**: & (連結)
* **論理**: && (AND)、|| (OR)
* **評価順序**: ( )

演算子は左から右に評価されます。評価の優先順位は同じです。 つまり、\* (乗算) は - (減算) よりも先に評価されません。 2\*(5+3) は、2\*5+3 と同じではありません。 かっこ () は、左から右への評価順が適切ではない場合に、評価順を変更するために使用されます。

## <a name="multi-valued-attributes"></a>複数値の属性
関数は、単一値の属性と複数値の属性両方に対して使用できます。 複数値の属性の場合、関数は、すべての値で動作し、すべての値に同じ関数を適用します。

次に例を示します。  
`Trim([proxyAddresses])` proxyAddress 属性の各値の Trim を実行します。  
`Word([proxyAddresses],1,"@") & "@contoso.com"`@-sign を含むすべての文字列では、ドメインを @contoso.com に置き換えます。  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` SIP アドレスを検索し、値から削除します。

## <a name="next-steps"></a>次のステップ
* この構成モデルについて詳しくは、「 [Understanding Declarative Provisioning (宣言型のプロビジョニングについて)](concept-azure-ad-connect-sync-declarative-provisioning.md)」をご覧ください。
* 宣言型のプロビジョニングをすぐに使用する方法については、「 [既定の構成について](concept-azure-ad-connect-sync-default-configuration.md)」をご覧ください。
* 宣言型のプロビジョニングを使用して現実に即した変更を実施する方法については、「 [既定の構成を変更する方法](how-to-connect-sync-change-the-configuration.md)」をご覧ください。

**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)

**参照トピック**

* [Azure AD Connect 同期:関数参照](reference-connect-sync-functions-reference.md)