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
ms.date: 02/27/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c06c089fb08c19b55246122201c378917a560e14
ms.openlocfilehash: 3e4458f70afce9ebd9477b00afc39b6e84e49319
ms.lasthandoff: 03/01/2017


---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング
このセクションでは、スコープ フィルターを使用して属性ベースのルールを定義する方法について説明します。このルールで、アプリケーションに対してプロビジョニングするユーザーを指定します。

## <a name="clauses-and-scope-groups"></a>句とスコープ グループ
![スコープ フィルター][1] 

スコープ フィルターは、1 つ以上の**スコープ グループ**で定義され、それぞれが&1; つ以上の**句**を保持します。 特定のスコープ グループの句を表示するには、グループ名の左側の矢印をクリックして展開します。

**句** は、各ユーザーの属性を評価することによって、スコープ フィルターを通過できるユーザーを決定します。 たとえば、ユーザーの "都道府県" 属性が New York と等しいことを求める句が指定されていると、New York ユーザーのみがアプリケーションにプロビジョニングされます。

![スコープ グループ名][2] 

上記のスクリーンショットに示すように、各**スコープ グループ**は、1 つの必須の**句**で始まります。 この句は、ユーザーをスコープ フィルターによって評価する前に、まずアプリケーションに割り当てる必要があることを示しています。 この句を削除または変更することはできません。

新しい句または新しいスコープ グループを追加するには、適切なボタンをクリックします。 各スコープ グループに名前を付けるには、その **スコープ グループ名** プロパティを編集します。

## <a name="how-scoping-filters-are-evaluated"></a>スコープ フィルターを評価する方法
プロビジョニング中は、ユーザーがアプリケーションにアクセスする資格があるかどうかを判断するために、すべての割り当てられているユーザーをスコープ フィルターに対してテストします。 各句は、ユーザーがフィルターで除外されないために合格する必要のあるテストと考えることができます。 

複数のスコープ グループを定義している場合は、各ユーザーがアプリケーションにアクセスするには、ユーザーは少なくとも&1; つのスコープ グループで適格とされる必要があります。 ただし、ユーザーが特定のスコープ グループで適格とされるには、各スコープ グループ内のすべての句で適格とされていなければなりません。 

つまり、スコープ グループは OR でまとめられたもの、スコープ グループ内の句は AND でまとめられたものと考えることができます。 たとえば、以下のスコープ フィルターがあるとします。

![スコープ グループ名][3]  

このスコープ フィルターに従うと、ユーザーをプロビジョニングするには、そのユーザーは次の条件を満たす必要があります。

1. アプリケーションに割り当てられている
2. エンジニアリング部門で働いている
3. San Francisco または Canada で働いている

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./media/active-directory-saas-scoping-filters/ic782813.png

