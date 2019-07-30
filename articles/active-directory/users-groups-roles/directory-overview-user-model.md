---
title: ユーザー、グループ、ライセンス、およびロールの概要 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory における割り当てライセンス、管理者ロール、グループ メンバーシップとユーザーとの関係
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: vincesm
ms.date: 01/28/2019
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2053ad07e555cc04cea714524141068f1404796
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083886"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>大規模組織のユーザー、グループ、ライセンス、ロール

この記事では、Azure AD 管理者向けに、グループ、ライセンス、デプロイされているエンタープライズ アプリ、管理者ロールの観点から、ユーザーを対象とする主要な [ID 管理](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context)タスク間の関係について説明します。 Azure AD のグループと管理者ロールを使うことにより、組織の規模の拡大に伴って次のことを行えます。

* ライセンスを個人ではなくグループに割り当てる
* より権限が低いロールにアクセス許可を委任して Azure AD の管理作業を分散する
* エンタープライズ アプリのアクセス権をグループに割り当てる

## <a name="assign-users-to-groups"></a>ユーザーをグループに割り当てる

Azure AD のグループを使うと、ライセンスを多数のユーザーに割り当てたり、デプロイされているエンタープライズ アプリへのユーザー アクセス権を割り当てたりすることができます。 Azure AD でグループを使用して管理者ロールを割り当てたり、外部のリソース (SaaS アプリケーション、SharePoint サイトなど) へのアクセス権を付与したりすることが可能です。

また、柔軟性を高めたり、グループ メンバーシップの管理作業を軽減したりするために、Azure AD の[動的グループ](groups-create-rule.md)を使用して、グループのメンバーシップを自動的に拡大、縮小することもできます。 1 つ以上の動的グループのメンバーになっている一意のユーザーには、それぞれ Azure AD Premium P1 ライセンスが必要です。

## <a name="assign-licenses-to-groups"></a>ライセンスをグループに割り当てる

ライセンスの割り当てまたは削除を個々のユーザーに対して実施するためには、時間と注意が必要になることがあります。 代わりに[ライセンスをグループに割り当てる](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)ことによって、大規模なライセンス管理の負荷を軽減することができます。

Azure AD では、ライセンスを付与されているグループにユーザーが参加すると、適切なライセンスがそのユーザーに自動的に割り当てられます。 ユーザーがグループから離脱すると、そのライセンスの割り当てが Azure AD によって解除されます。 Azure AD グループがない場合には、組織に参加するユーザーや組織から離脱するユーザーのライセンスを一括追加または一括削除するために、PowerShell スクリプトを作成するか、Graph API を使用する必要があります。

使用できるライセンスが不足している場合や、何らかの問題 (同時には割り当てることができないサービス プランなど) が生じた場合、グループのライセンスに関する問題の状態を Azure portal で確認できます。

>[!NOTE]
>グループベースのライセンス機能は、現在パブリック プレビュー段階にあります。 プレビュー期間中は、有料の Azure Active Directory (Azure AD) ライセンス プランと試用版のどちらでも、この機能をご利用いただけます。

## <a name="delegate-administrator-roles"></a>管理者ロールを委任する

多くの大規模組織は、そのユーザー (アプリケーションを登録する必要のあるユーザーなど) に強力なグローバル管理者ロールを割り当てることなく、ユーザーそれぞれが仕事上のタスクを遂行するうえで十分なアクセス許可を入手する手段を求めています。 以下に示したのは、アプリケーション管理作業をよりきめ細かく分散させるうえで役立つ新しい Azure AD 管理者ロールの例です。

 ロール名 | アクセス許可の概要
 --------- | -------------------
 **アプリケーション管理者** | エンタープライズ アプリケーションとアプリケーションの登録を追加して管理したり、プロキシ アプリケーションの設定を構成したりすることができます。 アプリケーション管理者は、条件付きアクセス ポリシーとデバイスを表示することはできますが、それらを管理することはできません。
 **クラウド アプリケーション管理者** | エンタープライズ アプリケーションとエンタープライズ アプリの登録を追加して管理することができます。 このロールには、アプリケーション管理者のすべてのアクセス許可が含まれます。ただし、アプリケーション プロキシの設定を管理することはできません。
**アプリケーション開発者** | アプリケーションの登録を追加、更新できます。ただし、エンタープライズ アプリケーションを管理したり、アプリケーション プロキシを構成したりすることはできません。

新しい Azure AD 管理者ロールが継続的に追加されています。 現在利用できるロールについては、Azure portal または[管理者ロールのアクセス許可に関するリファレンス](directory-assign-admin-roles.md)をご確認ください。

## <a name="assign-app-access"></a>アプリへのアクセス権を割り当てる

Azure AD を使用すると、[Azure AD テナントにデプロイされたエンタープライズ アプリ](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)へのグループ アクセス権を割り当てることができます。 アプリへのグループ割り当てと動的グループを組み合わせれば、組織の規模拡大に伴って、ユーザーに対するアプリへのアクセス権の割り当てを自動化することができます。 エンタープライズ アプリへのアクセス権を割り当てるには、Azure Active Directory Premium P1 または Premium P2 ライセンスが必要です。

また、Azure AD では、アクセス権の割り当て先となるグループとアプリの間を行き来するデータをきめ細かく制御することができます。 [[エンタープライズ アプリケーション]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) でアプリを開き、 **[プロビジョニング]** を選択して次の作業を実施できます。

* 自動プロビジョニングをサポートするアプリに対してその設定を行う
* アプリのユーザー管理 API に接続するための資格情報を指定する
* ユーザー アカウントのプロビジョニング時または更新時に Azure AD とアプリの間でやり取りされるユーザー属性を制御するマッピングを設定する
* アプリの Azure AD プロビジョニング サービスを開始、停止したり、プロビジョニング キャッシュをクリアしたり、サービスを再開したりする
* **プロビジョニング アクティビティ レポート** (Azure AD とアプリの間で作成、更新、削除されたすべてのユーザーとグループのログを確認できる) および**プロビジョニング エラー レポート** (より詳細なエラー メッセージを確認できる) を確認する

## <a name="next-steps"></a>次の手順

Azure AD 管理者としてまだ日が浅い方は、[Azure Active Directory の基礎](https://docs.microsoft.com/azure/active-directory/fundamentals/index)に関するドキュメントで基本的な事柄をマスターしてください。

または、[グループの作成](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)、[ライセンスの割り当て](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)、[アプリのアクセス権の割り当て](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)、[管理者ロールの割り当て](directory-assign-admin-roles.md)を始めることができます。
