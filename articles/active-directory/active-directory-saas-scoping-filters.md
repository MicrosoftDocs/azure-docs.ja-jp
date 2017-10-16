---
title: "スコープ フィルターを使用したアプリ プロビジョニング | Microsoft Docs"
description: "スコープ フィルターを使用して、自動ユーザー プロビジョニングをサポートするアプリ内のオブジェクトが、ビジネス要件を満たしていないのに実際にプロビジョニングされてしまうことを防ぐ方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: e40ba3d3035d5c04017a8ed558981b01fae40a13
ms.contentlocale: ja-jp
ms.lasthandoff: 09/27/2017

---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング
このセクションでは、スコープ フィルターを使用して属性ベースのルールを定義する方法について説明します。このルールで、アプリケーションに対してプロビジョニングするユーザーを指定します。

## <a name="scoping-filter-use-cases"></a>スコープ フィルターの使用例

スコープ フィルターを利用すると、Azure AD プロビジョニング サービスで、ユーザーに特定の値に一致する属性が与えられているとき、そのユーザーを追加したり、除外したりできます。 たとえば、営業チームが利用する SaaS アプリケーションに Azure AD からユーザーをプロビジョニングするとき、"Sales" の "Department" 属性を持つユーザーのみがプロビジョニングの範囲に入るように指定できます。

スコープ フィルターの利用方法はプロビジョニング コネクタの種類によって異なる場合があります。

* **Azure AD から SaaS アプリケーションへの外向きプロビジョニング** - Azure AD がソース システムのとき、プロビジョニングに含めるユーザーを決定する方法として最も一般的なものが[ユーザーとグループの割り当て](active-directory-coreapps-assign-user-azure-portal.md)です。 ユーザーとグループの割り当てはシングル サインオンの有効化にも利用され、1 つの方法でアクセスとプロビジョニングを管理できます。 スコープ フィルターを割り当てに加えて (任意で)、あるいは割り当ての代わりに利用し、属性値に基づいてユーザーを絞り込むことができます。

>[!TIP]
> エンタープライズ アプリケーションの割り当てに基づいてプロビジョニングを無効にできます。プロビジョニング設定の **[[スコープ]](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)** を **[すべてのユーザーとグループを同期する]** に設定します。 このオプションと属性基準のスコープ フィルターを利用することで、グループ基準の割り当てよりパフォーマンスが速くなります。  

* **HCM アプリケーションから Azure AD と Active Directory への内向きプロビジョニング** - [Workday などの HCM アプリケーション](active-directory-saas-workday-tutorial.md)がソース システムのとき、HCM アプリケーションから Active Directory または Azure Active Directory にプロビジョニングするユーザーを決定する主要な方法がスコープ フィルターです。

既定では、Azure AD プロビジョニング コネクタには属性基準のスコープ フィルターが設定されていません。 

## <a name="scoping-filter-construction"></a>スコープ フィルターの構造

スコープ フィルターは 1 つまたは複数の**句**で構成されます。 句は、各ユーザーの属性を評価することによって、スコープ フィルターを通過できるユーザーを決定します。 たとえば、ユーザーの "State" 属性が New York と等しいことを求める句が指定されていると、New York ユーザーのみがアプリケーションにプロビジョニングされます。 

1 つの句は、1 つの属性値の 1 つの条件を定義します。 1 つのスコープ フィルターに複数の句が作成されている場合、"AND" ロジックでまとめて評価されます。 つまり、ユーザーをプロビジョニングするには、すべての句の評価結果が "true" になる必要があります。

最後に、複数のスコープ フィルターを 1 つのアプリケーションに対して作成できます。 複数のスコープ フィルターがある場合、"OR" ロジックで評価されます。 つまり、いずれかのスコープ フィルターのすべての句の評価結果が "true" であれば、そのユーザーがプロビジョニングされます。

Azure AD プロビジョニング サービスによって処理されるユーザーまたはグループは常に個別に各スコープ フィルターに対して評価されます。

たとえば、下のスコープ フィルターをご覧ください。

![スコープ フィルター](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

このスコープ フィルターに従うと、ユーザーをプロビジョニングするには、そのユーザーは次の条件を満たす必要があります。

1. New York にいる
2. エンジニアリング部門で働いている
3. 社員 ID の番号が 1000000 から 2000000 までに入る
4. 役職が null や空ではない

## <a name="creating-scoping-filters"></a>スコープ フィルターの作成
スコープ フィルターは各 Azure AD ユーザー プロビジョニング コネクタの属性マッピングで設定されます。 下の手順では、[サポートされているアプリケーションの 1 つ](active-directory-saas-tutorial-list.md)に自動プロビジョニングを既に設定しており、それにスコープ フィルターを追加するものと想定しています。

### <a name="to-create-a-scoping-filter"></a>スコープ フィルターを作成するには:
1. [Azure ポータル](https://portal.azure.com)で、**[Azure Active Directory]、[エンタープライズ アプリケーション]、[すべてのアプリケーション]** セクションの順に進みます。
2. 自動プロビジョニングを設定しているアプリケーションを選択します。 例: "ServiceNow"
3. **[プロビジョニング]** タブを選択します。
4. **[マッピング]** セクションで、スコープ フィルターを設定するマッピングを選択します。 例: "Azure Active Directory ユーザーを ServiceNow に同期する"
5. **[ソース オブジェクト スコープ]** メニューを選択します。
6. **[スコープ フィルターの追加]** を選択します。
7. 照合対象としてソースの **[属性名]**、**[演算子]**、**[属性値]** を選択し、句を定義します。 サポートされている演算子:
   * **EQUALS** - 評価された属性が入力文字列値に厳密に (大文字と小文字が区別される) 一致する場合、句は "true" を返します。
   * **NOT EQUALS** - 評価された属性が入力文字列値に一致しない場合 (大文字と小文字は区別される)、句は "true" を返します。
   * **IS TRUE** - 評価される属性にブール値 true が含まれる場合、句は "true" を返します。
   * **IS FALSE** - 評価される属性にブール値 false が含まれる場合、句は "true" を返します。
   * **IS NULL** - 評価される属性が空の場合、句は "true" を返します。
   * **IS NOT NULL** - 評価される属性が空ではない場合、句は "true" を返します。
   * **REGEX MATCH** - 評価される属性が正規表現パターンに一致する場合、句は "true" を返します。 例: ([1-9][0-9]) は 10 から 99 までのあらゆる数字に一致します
   * **NOT REGEX MATCH** - 評価される属性が正規表現パターンに一致しない場合、句は "true" を返します。
8. **[新しいスコープ句の追加]** を選択します。
9. 必要に応じて、手順 7 - 8 を繰り返し、スコープ句を追加します。
10. **[スコープ フィルターのタイトル]** で、スコープ フィルターの名前を追加します。
11. **[OK]**を選択します。
12. [スコープ フィルター] 画面でもう一度 **[OK]** を選択します (あるいは、手順 6 - 11 を繰り返し、別のスコープ フィルターを追加します)。
13. [属性マッピング] 画面で **[保存]** を選択します。 

>[!IMPORTANT] 
> 新しいスコープ フィルターを保存すると、アプリケーションの完全同期が新たに開始されます。ソース システムのすべてのユーザーが新しいスコープ フィルターに対してもう一度評価されます。 アプリケーションのユーザーが以前はプロビジョニングの範囲に入っていたが、範囲から外れた場合、アプリケーションでそのアカウントが無効になるか、プロビジョニングが解除されます。


## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)


