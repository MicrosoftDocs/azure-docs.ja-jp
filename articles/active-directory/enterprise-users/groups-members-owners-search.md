---
title: グループのメンバーと所有者を検索してフィルター処理する (プレビュー) - Azure Active Directory |Microsoft Docs
description: Azure portal でグループのメンバーと所有者を検索し、フィルター処理します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547374"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Azure Active Directory でグループとメンバーを検索する (プレビュー)

この記事では、グループのメンバーと所有者を検索する方法と、Azure Active Directory (Azure AD) ポータルでグループの向上のプレビューの一部として検索フィルターを使用する方法について説明します。 メンバーと所有者を含むグループをすばやく簡単に管理するのに役立つ、グループ エクスペリエンスが大幅に向上しています。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

このプレビューの変更点は次のとおりです。

- グループ名の部分文字列検索などの、新しいグループ検索機能
- メンバーと所有者のリストに対する新しいフィルター処理および並べ替えオプション
- メンバーと所有者のリストの新しい検索機能
- 大規模なグループのより正確なグループ数

## <a name="enabling-and-managing-the-preview"></a>プレビューの有効化と管理

プレビューに簡単に参加できるようになりました。

  1. [Azure AD ポータル](https://portal.azure.com)にサインインし、**[グループ]** を選択します。
  2. [グループ - すべてのグループ] ページで、ページの上部にあるバナーを選択して、プレビューに参加します。

また、**[すべてのグループ]** ページで **[プレビュー情報]** を選択して、最新の機能と機能強化を確認することもできます。 プレビューに参加した後、機能強化され、プレビューの一部である、すべてのグループ ページでプレビュー タグが表示されます。 このプレビューの一部として、すべてのグループ ページが更新されているわけではありません。

問題が発生している場合は、**[すべてのグループ]** ページの上部にあるバナーを選択して、従来のエクスペリエンスに戻すことができます。 Microsoft ではエクスペリエンスの向上のため、皆様からのフィードバックをお待ちしております。

## <a name="group-search-and-sorting"></a>グループの検索と並べ替え

グループ リスト検索の機能が強化されたため、検索文字列を入力できる場合は、グループ名のリストに対して `startswith` と部分文字列検索が自動的に実行されます。 部分文字列検索は単語全体でのみ実行され、特殊文字は含まれません。 Substring 検索では大文字と小文字が区別されます。

![[すべてのグループ] ページでの新しい部分文字列検索](./media/groups-members-owners-search/groups-search-preview.png)

たとえば、"policy" を検索すると、"MDM policy – West" と "Policy group" の両方が返されるようになります。 "New_policy" という名前のグループは返されません。

- グループ メンバーシップ リストでも同じ検索を実行できます。
- 名前列見出しの右側にある矢印を使用して、グループ リストを名前で並べ替え、リストを昇順または降順で並べ替えられるようになりました。

## <a name="group-member-search-and-filtering"></a>グループ メンバーの検索とフィルター処理

### <a name="search-group-member-and-owner-lists"></a>グループ メンバーと所有者のリストを検索する

特定のグループのメンバーを名前で検索し、グループの所有者のリストに対しても同じ検索を実行できるようになりました。 新しいエクスペリエンスでは、検索ボックスに文字列を入力すると、startswith 検索が自動的に実行されます。 たとえば、"Scott" を検索すると、Scott Wilkinson が返されます。

![グループ メンバーと所有者のリストでの新しい部分文字列の検索](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>メンバーと所有者のリストをフィルター処理する

検索に加えて、メンバーと所有者のリストをユーザーの種類でフィルター処理できるようになりました。 これは、リストの [ユーザーの種類] 列に表示される情報です。 そのため、メンバーとゲストでリストをフィルター処理して、グループ内にゲストが存在するかどうかを判断できます。

### <a name="view-and-manage-membership"></a>メンバーシップを表示して管理する

特定のグループの直接メンバーを表示するだけでなく、[メンバー] ページ内のグループのすべてのメンバーのリストを表示できるようになりました。 メンバー リストには、推移メンバーを含む、グループのすべての一意のメンバーが含まれます。

また、直接メンバー リストとすべてのメンバー リストを個別に検索し、フィルター処理することもできます。 すべてのメンバー リストをフィルター処理しても、直接メンバー リストに適用されているフィルターには影響しません。

## <a name="improved-group-member-counts"></a>改善されたグループ メンバー数

グループの **[概要]** ページを改善し、すべてのサイズのグループについてグループ メンバー数が示されるようにしました。 メンバー数が 1,000 を超えるグループについても、メンバー数が表示されます。 **[概要]** ページに、グループの直接メンバーの総数と、メンバーシップの合計数 (推移メンバーを含むグループのすべての一意のメンバー) が表示されるようになりました。

![グループ メンバーシップ数の精度の向上](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>次のステップ

これらの記事では、Azure AD でのグループの操作に関する追加情報が提供されます。

- [グループとメンバーを表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
- [グループ メンバーシップを管理する](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [グループ内のユーザーの動的ルールの管理](groups-create-rule.md)
- [グループの設定を編集する](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [グループを使用したリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
- [グループを使用して SaaS アプリへのアクセスを管理する](groups-saasapps.md)
- [PowerShell コマンドを使用してグループを管理する](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Azure サブスクリプションを Azure Active Directory に追加する](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
