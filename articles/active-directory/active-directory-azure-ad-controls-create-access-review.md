---
title: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure AD で作成する | Microsoft Docs"
description: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを作成する方法について説明します。"
services: active-directory
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
ms.openlocfilehash: c2c5aee3db11255be5a6fe405424c2cbb92c5b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>グループ メンバーまたはアプリケーション アクセスに対するアクセス レビューを Azure AD で作成する

アクセス権の割り当ては、ユーザーに割り当てられているアクセス権が不要になった時点で "古く" なっています。  アクセス権の割り当てが古いことで生じるリスクを軽減するために、管理者はアクセス レビューを作成することによって、アプリケーションに割り当てられているユーザーまたはグループのメンバーに対するレビューを要求することができます。 これらのシナリオの詳細については、[ユーザー アクセスの管理](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)と[ゲスト アクセスの管理](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)についてのガイドを参照してください。  

## <a name="how-to-create-an-access-review"></a>アクセス レビューを作成する方法


1. グローバル管理者として[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動し、**[プログラム]** タブに切り替えます。
2. 作成するアクセス レビュー コントロールの従属先となるプログラムをクリックします。  "既定のプログラム" というプログラムが最初から 1 つ存在するほか、別のプログラムを作成することもできます。  たとえば、コンプライアンスの取り組みやビジネス目標ごとにプログラムを 1 つ用意することができます。
3. プログラム内の コントロールをクリックし、**追加** ボタンをクリックしてコントロールを追加します。
4. アクセス レビューにはそれぞれ名前が必要です。また、必要に応じて説明を追加することもできます。  この名前はレビュー担当者に表示されます。  
5. 既定では、アクセス レビューは、その作成日を開始日として 1 か月で終了するようにスケジュールされます。  この開始日と終了日を変更することで、アクセス レビューを後で開始し、必要な日数を好きなだけ確保することができます。
6. アクセス レビューは、アプリケーションに割り当てられているグループのメンバーまたはユーザーを対象とすることができます。  また、アクセス レビューの範囲は限定することもでき、アプリケーションのメンバーになっているユーザー、つまりアプリケーションへのアクセス権を持つユーザー全員をレビューすることはせずに、メンバーとなっている (アプリケーションに割り当てられている) ゲスト ユーザーだけをレビュー対象にすることができます。
7. 対象範囲内の全ユーザーをレビューする 1 人以上の担当者を選ぶか、メンバー自身にそのアクセス権をレビューしてもらうことができます。  リソースがグループである場合は、そのグループの所有者にレビューを依頼することができます。  アクセスの承認時に理由を入力するようレビュー担当者に要求することもできます。
8. 最後に、**[開始]** をクリックします。


## <a name="managing-the-access-review"></a>アクセス レビューの管理

既定では、レビューの開始直後に Azure AD からレビュー担当者宛てにメールが送信されます。  Azure AD からメールを送信しない場合は、アクセス レビューを完了するよう必ずレビュー担当者に伝えてください。  レビュー担当者には、[アクセス レビューの手順](active-directory-azure-ad-controls-perform-access-review.md)を案内します。レビュー対象がゲストで、その本人にアクセス レビューを実施してもらう場合は、[自身のアクセス権をレビューする手順](active-directory-azure-ad-controls-perform-access-review.md)を案内してください。

レビュー担当者の中にゲストが含まれる場合、メールで通知されるのは、あらかじめ招待状を受け取ったゲストだけです。


レビュー担当者が Azure AD ダッシュボードでレビューを完了すると、[アクセス レビュー] セクションで進行状況を追跡できます。 ディレクトリのアクセス権は、 [レビューが完了する](active-directory-azure-ad-controls-complete-access-review.md)まで変更されません。

## <a name="next-steps"></a>次のステップ

アクセス レビューが開始されると、アクセス権をレビューするよう促すメールが Azure AD からレビュー担当者に自動的に送信されます。 メールが届かなかったユーザーには、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページの手順を送信できます。  

アクセス レビュー期間が過ぎるか、管理者がアクセス レビューを停止したら、[アクセス レビューの実施](active-directory-azure-ad-controls-complete-access-review.md)に関するページの手順に従って、その結果を確認したうえで適用します。


