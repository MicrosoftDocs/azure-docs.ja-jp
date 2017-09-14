---
title: "SaaS アプリケーションへのアクセスをグループで管理する | Microsoft Docs"
description: "Azure Active Directory と連携する SaaS アプリケーションへのアクセス権を、Azure Active Directory Premium または Basic でグループを使用して割り当てる方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro;oldportal
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: d350011ee9fc5ced9ddb16993f68d3c840a645a5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>SaaS アプリケーションへのアクセスをグループで管理する
Azure Active Directory (Azure AD) の Azure AD Premium ライセンスまたは Azure AD Basic ライセンスでは、Azure AD と連携する SaaS アプリケーションへのアクセス権を、グループを使って割り当てることができます。 たとえばマーケティング部門を対象に 5 つの SaaS アプリケーションを使用するためのアクセス権を割り当てる場合、同部門のユーザーから成るグループを作成したうえで、マーケティングで必要となる 5 つの SaaS アプリケーションにそのグループを割り当てます。 このようにマーケティング部門のメンバーシップを一元的に管理することで時間を節約することが可能です。 この場合アプリケーションに対するユーザーの割り当ては、そのユーザーがマーケティング グループのメンバーとして追加されたときに行われます。同様に、マーケティング グループからユーザーが削除されると、アプリケーションからもその割り当てが削除されます。

> [!IMPORTANT]
> Microsoft では、この記事で言及している Azure クラシック ポータルを使用するのではなく、Azure Portal の [Azure AD 管理センター](https://aad.portal.azure.com)を使用して Azure AD を管理することをお勧めしています。 

この機能は、Azure AD アプリケーション ギャラリー内から追加できる多数のアプリケーションで利用することができます。

**SaaS アプリケーションに対してグループのアクセス権を割り当てるには**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)の左側のナビゲーション バーで **[Active Directory]** を選択します。
2. **[ディレクトリ]** タブを選択し、SaaS アプリケーションに対するグループのアクセス権を割り当てるディレクトリを開きます。
3. **[アプリケーション]** タブを選択します。 アプリケーション ギャラリーから追加したアプリケーションをクリックし、**[ユーザーとグループ]** タブをクリックします。
4. **[ユーザーとグループ]** タブで、アクセス権を割り当てるグループの名前を **[指定値で始まる]** フィールドに入力し、右上のチェック マークをオンにします。 入力するのは、グループ名の先頭部分だけでかまいません。
5. グループを選択し、 **[アクセス権の割り当て]** ボタンをクリックします。 確認メッセージが表示されたら、 **[はい]** を選択します。 現在、アプリケーションに対するグループ ベースの割り当てでは入れ子になったグループ メンバーシップはサポートされていません。
6. アプリケーションに直接割り当てられているユーザーとグループのメンバーシップを通じて割り当てられているユーザーを表示することもできます。 その場合は、[表示] ドロップダウンを **[グループ]** から **[すべてのユーザー]** に変更してください。 ディレクトリ内のユーザーが一覧表示され、アプリケーションに割り当てられているかどうかがユーザーごとに表示されます。 ユーザーがアプリケーションに直接割り当てられているのか ([割り当ての種類] = [直接])、グループのメンバーシップを通じて割り当てられているのか ([割り当ての種類] = [継承済み]) も、このリストで確認できます。

> [!NOTE]
> [ユーザーとグループ] タブが表示されるのは、Azure AD Premium または Azure AD Basic を有効にした場合のみです。
>
>

### <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Azure Active Directory とは](active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

