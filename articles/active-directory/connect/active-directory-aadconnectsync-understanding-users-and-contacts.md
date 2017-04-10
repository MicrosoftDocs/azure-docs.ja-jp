---
title: "Azure AD Connect 同期: ユーザーと連絡先について | Microsoft Docs"
description: "Azure AD Connect Sync のユーザーと連絡先について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: markvi;andkjell
translationtype: Human Translation
ms.sourcegitcommit: c5b54c80d9a74d72b242f8d7dd55e2dcadbfa48e
ms.openlocfilehash: 96d568b6d264fa484e09ee81cdbc779a6f65eb0d
ms.lasthandoff: 03/01/2017


---
# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Azure AD Connect Sync: ユーザーと連絡先について
複数の Active Directory フォレストを使用することになる理由はさまざまあり、複数の異なるデプロイ トポロジがあります。 一般的なモデルとしては、アカウント リソース デプロイ、合併や買収の後で GAL 同期が行われたフォレストなどがあります。 ただし、純粋なモデルがある一方で、ハイブリッド モデルも一般的です。 Azure AD Connect Sync の既定の構成では特殊なモデルを想定しませんが、インストール ガイドにおけるユーザーの一致の選択方法によっては、異なる動作が見られることもあります。

このトピックでは、既定の構成が特定のトポロジでどのように動作するかを説明します。 構成の概要についてと、構成を確認するために使用できる同期ルール エディターについて取り上げます。

構成の前提となるいくつかの一般的なルールがあります。

* ソースの Active Directory からインポートする順序に関係なく、最終的な結果は常に同じになる必要があります。
* アクティブなアカウントは、**userPrincipalName** や **sourceAnchor** など、サインイン情報を常に提供します。
* アクティブなアカウントが見つからない場合、無効なアカウントは、リンクされたメールボックスでない限り、userPrincipalName と sourceAnchor を提供します。
* リンクされたメールボックスを使用するアカウントは、userPrincipalName と sourceAnchor に使用されることはありません。 アクティブなアカウントは後で見つかることが前提です。
* 連絡先オブジェクトは、Azure AD に対して連絡先またはユーザーとしてプロビジョニングされます。 すべてのソースの Active Directory フォレストが処理されるまでは、実際にはわかりません。

## <a name="contacts"></a>連絡先
合併や買収の後、連絡先は異なるフォレストのユーザーを表しているのが一般的です。そこでは、GALSync ソリューションが&2; つ以上の Exchange フォレストをつないでいます。 連絡先オブジェクトは、メール属性を使用してコネクタ スペースからメタバースを常に結合しています。 同じメール アドレスの連絡先オブジェクトまたはユーザー オブジェクトが既にある場合、これらのオブジェクトは一緒に結合されます。 これは、**In from AD – Contact Join** というルールで構成されます。 また、定数が **Contact** であるメタバース属性 **sourceObjectType** への属性フローを使用する **In from AD – Contact Common** というルールもあります。 このルールの優先順位は低いので、ユーザー オブジェクトが同じメタバース オブジェクトに結合された場合は、**In from AD – User Common** というルールによって User という値がこの属性に提供されます。 このルールでは、この属性は、ユーザーが&1; 人も結合されていない場合に Contact という値を使用し、ユーザーが&1; 人でも見つかった場合に User という値を使用します。

Azure AD に対するオブジェクトのプロビジョニングでは、メタバース属性 **sourceObjectType** が **Contact** に設定された場合、**Out to AAD – Contact Join** という送信ルールによって連絡先オブジェクトが作成されます。 この属性が **User** に設定された場合は、代わりに **Out to AAD – User Join** というルールによってユーザー オブジェクトが作成されます。
より多くのソースの Active Directory がインポートされ、同期されるときに、オブジェクトは Contact から User に昇格できます。

たとえば、GALSync トポロジでは、最初のフォレストをインポートするときに、2 番目のフォレストですべてのユーザーの連絡先オブジェクトを見つけます。 これにより、AAD コネクタの新しい連絡先オブジェクトがステージングされます。 後で&2; 番目のフォレストをインポートして同期するときに、実際のユーザーを見つけ、既存のメタバース オブジェクトに結合します。 次に、AAD の連絡先オブジェクトを削除し、代わりに新しいユーザー オブジェクトを作成します。

ユーザーが連絡先として表されるトポロジを使用する場合は、インストール ガイドでメール属性のユーザーに一致するように選択する必要があります。 別のオプションを選択した場合は、順序に依存する構成を使用します。 連絡先オブジェクトは、常にメール属性で結合されますが、ユーザー オブジェクトは、このオプションがインストール ガイドで選択された場合にのみメール属性で結合されます。 そうすると、ユーザー オブジェクトより前に連絡先オブジェクトがインポートされた場合、メタバース内の&2; つの異なるオブジェクトが同じメール属性を使用することになる可能性があります。 Azure AD へのエクスポート中に、エラーがスローされます。 この動作は仕様によるもので、不適切なデータが示されるか、インストール時にトポロジが正常に特定されなかったことが示されます。

## <a name="disabled-accounts"></a>無効なアカウント
無効なアカウントは、Azure AD にも同期されます。 無効なアカウントは、会議室などの Exchange のリソースを表すことが一般的です。 リンクされたメールボックスを使用するユーザーは例外で、すでに説明したように、これらのユーザーは Azure AD に対してアカウントをプロビジョニングしません。

前提では、無効なユーザー アカウントが見つかった場合、後で別のアクティブなアカウントを見つけることはなく、オブジェクトは、見つかった userPrincipalName および sourceAnchor を使用して Azure AD にプロビジョニングされます。 別のアクティブなアカウントが同じメタバース オブジェクトに結合される場合は、その userPrincipalName と sourceAnchor が使用されます。

## <a name="changing-sourceanchor"></a>sourceAnchor の変更
オブジェクトが Azure AD にエクスポートされると、sourceAnchor は変更できなくなります。 オブジェクトがエクスポートされると、Azure AD が受け取った **sourceAnchor** 値を使用してメタバース属性 **cloudSourceAnchor** が設定されます。 **sourceAnchor** が変更され、**cloudSourceAnchor** と一致しない場合、**Out to AAD – User Join** というルールによって **[sourceAnchor 属性が変更されました]** というエラーがスローされます。 この場合、オブジェクトを再度同期できるようにする前に、構成またはデータを修正して、同じ sourceAnchor がメタバースに再び存在するようにする必要があります。

## <a name="additional-resources"></a>その他のリソース
* [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)


