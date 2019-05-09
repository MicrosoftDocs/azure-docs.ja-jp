---
title: 特定のユーザーがいつアプリケーションにアクセスできるようになるかを確認する | Microsoft Docs
description: 非常に重要なユーザーが、Azure AD でユーザー プロビジョニング向けに構成したアプリケーションにいつアクセスできるようになるかを確認する方法を説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d2bb3b7385467d2606a2a4fa0afb43b9440ab79
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163106"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>特定のユーザーがいつアプリケーションにアクセスできるようになるかを確認する
アプリケーションで自動ユーザー プロビジョニングを使用するとき、Azure AD は[ユーザーとグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)などに基づいて、定期的にスケジュールされた間隔 (通常は 10 分ごと) で自動的にユーザー アカウントをアプリ内でプロビジョニングし、更新します。

## <a name="how-long-does-it-take"></a>どのくらいの時間がかかりますか?

指定したユーザーをプロビジョニングするためにかかる時間は、主に最初の "完全な" 同期が既に行われたかどうかによって変わります。

Azure AD とアプリの間での初回の同期には 20 分～数時間かかることがあります。この時間は Azure AD のディレクトリ サイズと、プロビジョニングの対象となるユーザー数によって異なります。 

初回の同期後の両方のシステムの状態を表すウォーターマークがプロビジョニング サービスに保存されるため、初回の同期後の後続の同期はより速くなり (たとえば 10 分以内など)、パフォーマンスが改善されます。

## <a name="how-to-check-the-status-of-a-user"></a>ユーザーの状態を確認する方法

選択したユーザーのプロビジョニングの状態を確認するには、Azure AD の監査ログを確認します。

プロビジョニングの監査ログには、Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [監査ログ]** タブからアクセスできます。**[アカウント プロビジョニング]** カテゴリでログをフィルター処理すると、そのアプリのプロビジョニング イベントのみを表示できます。 属性マッピングでユーザー向けに構成された "照合 ID" に基づいてユーザーを検索できます。 

たとえば、"ユーザー プリンシパル名" または "電子メール アドレス" を Azure AD 側で照合属性として構成した場合、プロビジョニングされていないユーザーは "audrey@contoso.com" という値を持つため、監査ログで "audrey@contoso.com" を検索し、返されたエントリを確認します。

プロビジョニングの監査ログには、プロビジョニング サービスによって実行されたすべての操作が記録されます。これには、次の操作が含まれます。

* プロビジョニングの対象となる割り当て済みユーザーを Azure AD で照会する
* これらのユーザーが存在するかどうかを対象となるアプリで照会する
* システム間でユーザー オブジェクトを比較する
* 比較に基づいて、対象のシステムのユーザー アカウントを追加または更新するか、無効にする

## <a name="next-steps"></a>次の手順
[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
