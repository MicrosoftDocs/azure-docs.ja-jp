---
title: Azure AD Connect 同期サービスのシャドウ属性 | Microsoft Docs
description: Azure AD Connect 同期サービスのシャドウ属性の機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 128303cb51b39db8442fdda71f949db17923bfa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90088972"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect 同期サービスのシャドウ属性
Azure AD のほとんどの属性は、オンプレミスの Active Directory の場合と同じように表現されます。 ただし、一部の属性には特別な処理が必要であるため、Azure AD の属性値が Azure AD Connect で同期された値と異なる場合があります。

## <a name="introducing-shadow-attributes"></a>シャドウ属性の概要
Azure AD の一部の属性には、2 つの表現があります。 オンプレミスの値と計算された値の両方が格納されます。 これらの特別な属性を、シャドウ属性と呼びます。 この動作が見られる最も一般的な 2 つの属性に、**userPrincipalName** と **proxyAddress** があります。 属性値に変化が起こるのは、これらの属性に未確認のドメインを表す値が存在する場合です。 ただし、Connect の同期エンジンがその観点からシャドウ属性の値を読み取るため、この属性は Azure AD によって確認されています。

Azure Portal または PowerShell を使用してシャドウ属性を確認することはできません。 ただし、この概念を理解することは、属性の値がオンプレミスとクラウドで異なるという特定のシナリオのトラブルシューティングに役立ちます。

この動作を理解するには、次の Fabrikam の例をご覧ください。  
![対応する Azure AD ドメインの値が "Not Added"、"Not Verified"、および "Verified" である、Active Directory UPN サフィックスのいくつかの例を示すスクリーンショット。](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
オンプレミスの Active Directory 内に複数の UPN サフィックスがありますが、確認済みなのは 1 つのみです。

### <a name="userprincipalname"></a>userPrincipalName
ユーザーの未確認ドメイン内には、次の属性値があります。

| Attribute | 値 |
| --- | --- |
| オンプレミスの userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD の shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD の userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

userPrincipalName 属性は、PowerShell を使用しているときに表示される値です。

実際のオンプレミスの属性値は Azure AD に格納されるため、ユーザーが fabrikam.com ドメインを確認するときに、Azure AD は shadowUserPrincipalName の値で userPrincipalName 属性を更新します。 これらの値を更新するために Azure AD Connect の変更を同期する必要はありません。

### <a name="proxyaddresses"></a>proxyAddresses
確認済みドメインのみを含む場合は proxyAddresses でも同じプロセスが発生しますが、いくつかのロジックが追加されます。 確認済みドメインのチェックは、メールボックスのユーザーに対してのみ発生します。 メールが有効なユーザーまたは連絡先は別の Exchange 組織内のユーザーであることを意味するため、proxyAddresses のあらゆる値をこれらのオブジェクトに追加することができます。

オンプレミスまたは Exchange Online のメールボックスのユーザーの場合、確認済みドメインの値のみが表示されます。 次のようになります。

| Attribute | 値 |
| --- | --- |
| オンプレミスの proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online の proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

この場合、**smtp:abbie.spencer\@fabrikam.com** は、ドメインが確認されなかったため、削除されました。 ただし、Exchange では **SIP:abbie.spencer\@fabrikamonline.com** の追加も行われています。 Fabrikam はオンプレミスで Lync/Skype を使用していませんが、Azure AD と Exchange Online ではその準備が行われます。

proxyAddresses のこのロジックは、**ProxyCalc** と呼ばれます。 ProxyCalc は、ユーザーに対する以下の変更のたびに呼び出されます。

- ユーザーが、Exchange のライセンスを与えられていなかったとしても、Exchange Online を含むサービス プランを割り当てられたとき。 たとえば、SharePoint Online しか割り当てられていなかったユーザーに Office E3 SKU が割り当てられた場合です。 メールボックスがまだオンプレミスの場合も、これに当てはまります。
- 属性 msExchRecipientTypeDetails が値を持つとき。
- proxyAddresses または userPrincipalName に変更を加えるとき。

ProxyCalc は、ユーザーの変更処理に時間がかかる場合があるため、Azure AD Connect のエクスポート プロセスと同期されません。

> [!NOTE]
> この ProxyCalc のロジックは、高度なシナリオでその他の動作を行いますが、このトピックには記載していません。 このトピックは動作の説明を目的とするため、文書化されているのは一部の内部ロジックのみです。

### <a name="quarantined-attribute-values"></a>検疫済みの属性値
シャドウ属性は、属性値が重複している場合にも使用されます。 詳細については、[重複属性の回復性](how-to-connect-syncservice-duplicate-attribute-resiliency.md)に関するページを参照してください。

## <a name="see-also"></a>参照
* [Azure AD Connect Sync](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
