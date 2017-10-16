---
title: "Azure AD のアクセス レビューによるゲスト アクセスの管理 | Microsoft Docs"
description: "Azure Active Directory アクセス レビューを使用し、ゲスト ユーザーをグループのメンバーとして、またはアプリケーションに割り当てられたユーザーとして管理します。"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Azure AD のアクセス レビューによるゲスト アクセスの管理


Azure Active Directory では、組織の境界を越えたコラボレーションを [Azure AD B2B 機能](active-directory-b2b-what-is-azure-ad-b2b.md)で簡単に実現できます。 [管理者](active-directory-b2b-admin-add-users.md)または[エンド ユーザー](active-directory-b2b-how-it-works.md)が他のテナントからゲスト ユーザーを招待できます。  これは Microsoft アカウントなどのソーシャル ID にも当てはまります。

ゲスト ユーザーに適切なアクセス権を確保することも簡単にできます。  これは、ゲスト自身または意思決定者に対し、アクセス レビューに参加してゲストのアクセス権を再確認 (証明) するよう求めることによって行います。 レビュー担当者は、Azure AD からの提案に基づいて、各ユーザーの継続的なアクセスのニーズを評価することができます。 アクセス レビューが完了したら、変更を加え、不要になったアクセス権をゲストから削除することができます。

> [!NOTE]
> このドキュメントでは、ゲスト ユーザーのアクセス レビューについて重点的に説明します。 ゲストに限らず全ユーザーのアクセス権をレビューする必要がある場合は、[アクセス レビューでユーザー アクセスを管理する方法](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)についてのガイドを参照してください。  また、グローバル管理者などの管理者ロールのユーザーのメンバーシップを確認する場合は、「[Azure AD Privileged Identity Management でアクセス レビューを開始する方法](active-directory-privileged-identity-management-how-to-start-security-review.md)」を参照してください。 
>
>

## <a name="prerequisites"></a>前提条件 

アクセス レビューは、EMS E5 に含まれる Azure Active Directory の Premium P2 エディションでご利用いただけます。 詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。  アクセス レビューを使用してレビューの作成、アクセスの確認、レビューの適用を行うには、この機能を使用する各ユーザーにライセンスが必要です。  

ゲスト ユーザーに自身のアクセス権をレビューするよう依頼する場合は、[Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)に関するページで、ゲスト ユーザーのライセンスについての詳しい情報をお読みください。

## <a name="creating-and-performing-an-access-review-for-guests"></a>ゲストのアクセス レビューの作成と実行

まず、レビュー担当者のアクセス パネルにアクセス レビューが表示されるようにします。  グローバル管理者として[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動します。 

ゲスト ユーザーのレビューに関して、Azure AD はいくつかのシナリオに対応しています。  

次のいずれかを選択してください。
 - Azure Active Directory 内のグループのメンバーとして 1 人以上のゲストが存在する。 
 - Azure Active Directory 接続アプリケーションに 1 人以上のゲスト ユーザーが割り当てられており、その各ゲストに自身のアクセス権をレビューするよう依頼するか、または 1 人以上のユーザーにゲスト全員のアクセス権をレビューするよう依頼する。

 これらの各シナリオについて次の各セクションで説明します。

* [ゲストに自身のグループ メンバーシップをレビューするよう依頼する](#asking-guests-to-review-their-own-membership-in-a-group)
* [責任者にゲストのグループ メンバーシップをレビューするよう依頼する](#asking-sponsors-to-review-guests-membership-in-a-group)
* [ゲストに自身のアプリケーション アクセスをレビューするよう依頼する](#asking-guests-to-review-their-own-access-to-an-application)
* [責任者にアプリケーションへのゲスト アクセスをレビューするよう依頼する](#asking-sponsors-to-review-guests-access-to-an-application) 
* [ゲストに全般的なアクセスの必要性をレビューするよう依頼する](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>ゲストに自身のグループ メンバーシップをレビューするよう依頼する

アクセス レビューを使用すると、グループに招待されて追加されたユーザーに引き続き必要なアクセス権を確保できます。  簡単な方法は、そのグループの自分のメンバーシップをゲスト自身にレビューしてもらうことです。

1. ゲスト ユーザーのメンバーだけをレビュー対象とし、メンバーに自身でレビューしてもらうよう選択して、グループのアクセス レビューを開始します。 詳しくは、「[how to create an access review (アクセス レビューを作成する方法)](active-directory-azure-ad-controls-create-access-review.md)」をご覧ください。
2. 各ゲストに自身のメンバーシップをレビューするよう依頼します。  既定では、招待状を受け取った各ゲストは、アクセス レビューへのリンクが記載されたメールを Azure AD から受信します。  Azure AD のゲスト向けの手順については、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページを参照してください。
3. レビュー担当者が結果を入力したら、アクセス レビューを停止して変更を適用します。 詳しくは、「[アクセス レビューを完了する方法](active-directory-azure-ad-controls-complete-access-review.md)」をご覧ください。 
4. アクセス権の継続が不要であると自分で判断したユーザーに加え、回答が得られなかったユーザーも削除した方がよいでしょう。  これは、回答がないユーザーはもうメールを受け取っていないと考えられるからです。
5. グループがアクセス管理に使用されていない場合も、招待状が届かなかったことが理由でレビューの対象外となったユーザーを削除した方がよいでしょう。  これは、招待されたユーザーのメール アドレスに誤りがあったことを示している可能性があります。  ただし、グループが配布リストとして使用されている場合、連絡先オブジェクトであるためにレビューの対象外となるゲスト ユーザーも存在します。

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>責任者にゲストのグループ メンバーシップをレビューするよう依頼する

ゲストのグループ メンバーシップが引き続き必要であるかどうかをレビューするよう責任者 (グループの所有者など) に依頼することができます。

1. ゲスト ユーザーのメンバーだけをレビュー対象として選択し、1 人以上のレビュー担当者を指定して、グループのアクセス レビューを開始します。 詳しくは、「[how to create an access review (アクセス レビューを作成する方法)](active-directory-azure-ad-controls-create-access-review.md)」をご覧ください。
2. レビュー担当者に確認を依頼します。 既定では、レビュー担当者はそれぞれ、Azure AD からアクセス パネルへのリンクが記載されたメールを受信し、そこで[アクセス レビューを実行](active-directory-azure-ad-controls-perform-access-review.md)します。
3. レビュー担当者が結果を入力したら、アクセス レビューを停止して変更を適用します。 詳しくは、「[アクセス レビューを完了する方法](active-directory-azure-ad-controls-complete-access-review.md)」をご覧ください。

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>ゲストに自身のアプリケーション アクセスをレビューするよう依頼する

アクセス レビューを使用すると、特定のアプリケーションに招待されたユーザーに引き続き必要なアクセス権を確保できます。  簡単な方法は、アクセスの必要性をゲスト自身にレビューしてもらうことです。

1. ゲストだけをレビュー対象とし、ユーザーに自身のアクセス権をレビューしてもらうよう選択して、アプリケーションのアクセス レビューを開始します。 詳しくは、「[how to create an access review (アクセス レビューを作成する方法)](active-directory-azure-ad-controls-create-access-review.md)」をご覧ください。
2. 各ゲストに自身のアプリケーション アクセスをレビューするよう依頼します。  既定では、招待状を受け取った各ゲストは、アクセス レビュー (社内のアクセス パネル) へのリンクが記載されたメールを Azure AD から受信します。  Azure AD のゲスト向けの手順については、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページを参照してください。
3. レビュー担当者が結果を入力したら、アクセス レビューを停止して変更を適用します。 詳しくは、「[アクセス レビューを完了する方法](active-directory-azure-ad-controls-complete-access-review.md)」をご覧ください。
4. アクセス権の継続が不要であると自分で判断したユーザーに加え、回答が得られなかったゲスト ユーザーについても削除した方がよいでしょう。回答がないユーザーはもうメールを受け取っていないと考えられるからです。  また、参加するよう選択されなかったゲスト ユーザーも、特にそのゲストが最近招待されていないならば、削除した方がよいでしょう。招待状を受け取っていないことから、これらのユーザーには、おそらくアプリケーションへのアクセス権もありません。 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>責任者にアプリケーションへのゲスト アクセスをレビューするよう依頼する


ゲストによるアプリケーション アクセスが引き続き必要であるかどうかをレビューするよう責任者 (アプリケーションの所有者など) に依頼することができます。

1. ゲストだけをレビュー対象に選択し、1 人以上のユーザーをレビュー担当者として指定して、アプリケーションのアクセス レビューを開始します。 詳しくは、「[how to create an access review (アクセス レビューを作成する方法)](active-directory-azure-ad-controls-create-access-review.md)」をご覧ください。
2. レビュー担当者に確認を依頼します。 既定では、レビュー担当者はそれぞれ、Azure AD からアクセス パネルへのリンクが記載されたメールを受信し、そこで[アクセス レビューを実行](active-directory-azure-ad-controls-perform-access-review.md)します。
3. レビュー担当者が結果を入力したら、アクセス レビューを停止して変更を適用します。 詳しくは、「[アクセス レビューを完了する方法](active-directory-azure-ad-controls-complete-access-review.md)」をご覧ください。

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>ゲストに全般的なアクセスの必要性をレビューするよう依頼する

組織によっては、ゲストが自分のグループ メンバーシップを把握していないこともあります。

> [!NOTE]
> 以前のバージョンの Azure Portal では、UserType がゲストであるユーザーの管理アクセスが認められておらず、ディレクトリの管理者が PowerShell を使ってゲストの UserType の値をメンバーに変更している場合があります。  過去にそのような変更がご利用のディレクトリで行われていた場合、前述したクエリでは、以前管理アクセス権を持っていたゲスト ユーザーが対象から漏れる場合があります。その場合は、ゲストの UserType を手動で変更するか、それらのゲストをグループ メンバーシップに手動で追加する必要があります。

1. ゲストをメンバーとするセキュリティ グループを Azure AD に作成します (適切なグループがまだ存在しない場合)。  たとえば、手動で管理されたゲストのメンバーシップを基にグループを作成します。  または、UserType 属性の値がゲストである Contoso テナントのユーザー用に "Guests of Contoso" などの名前の動的グループを作成してもよいでしょう。
2. メンバー自身をレビュー担当者として選択して、そのグループのアクセス レビューを開始します。 詳しくは、「[how to create an access review (アクセス レビューを作成する方法)](active-directory-azure-ad-controls-create-access-review.md)」をご覧ください。
3. 各ゲストに自身のメンバーシップをレビューするよう依頼します。  既定では、招待状を受け取った各ゲストは、アクセス レビュー (社内のアクセス パネル) へのリンクが記載されたメールを Azure AD から受信します。  Azure AD のゲスト向けの手順については、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページを参照してください。
4. レビュー担当者が結果を入力したら、アクセス レビューを停止します。 詳しくは、「[アクセス レビューを完了する方法](active-directory-azure-ad-controls-complete-access-review.md)」をご覧ください。
5. 拒否されたゲスト、レビューが完了していないゲスト、招待状を受け取っていないゲストについて、ゲスト アクセスを削除します。   過去に招待状を受け取っていないためにレビューの対象として選ばれた連絡先がゲストの中に存在する場合、そのアカウントはサインオンできないよう、Azure Portal または PowerShell を使って無効にした方がよいでしょう。  そのゲストがアクセス権をもう必要としておらず、連絡先でもない場合は、該当するユーザー オブジェクトを Azure Portal または PowerShell を使ってディレクトリから削除できます。

## <a name="next-steps"></a>次のステップ

- [グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)







