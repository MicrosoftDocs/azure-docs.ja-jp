---
title: "Azure AD Privileged Identity Management の構成 | Microsoft Docs"
description: "Azure AD Privileged Identity Management (PIM) の機能と、PIM を使用してクラウド セキュリティを向上させる方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: 89174dad8fcd3bcceafd728feb2211926266720a
ms.lasthandoff: 02/28/2017

---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management とは
組織内のアクセス権は、Azure Active Directory (AD) Privileged Identity Management で管理、制御、監視することができます。 その対象には、Azure AD をはじめとする Microsoft の各種オンライン サービス (Office 365、Microsoft Intune など) 内のリソースへのアクセスが含まれます。  

> [!NOTE]
> Privileged Identity Management は、Azure Active Directory の Premium P2 エディションでのみ使用できます。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

組織では、セキュリティで保護された情報やリソースへのアクセス権を持つユーザーの数を最小限に抑える必要があります。こうすることで、悪意のあるユーザーがこのようなアクセス権を手にする可能性が抑えられるためです。 しかし一方で、ユーザーは、Azure、Office 365、または SaaS アプリケーションで特権操作を実行する必要があります。 組織は、ユーザーが管理者特権で行う操作を監視することなく、Azure AD でユーザーに特権アクセスを与えます。 Azure AD Privileged Identity Management はこのリスクの解決に役立ちます。  

Azure AD Privileged Identity Management では、次のことが可能です。  

* Azure AD の管理者であるユーザーを特定する
* Office 365 や Intune などの Microsoft Online Services へのオンデマンドの "ジャスト イン タイム" な管理アクセスを可能にする
* 管理者のアクセス履歴と管理者の割り当ての変更に関するレポートを取得する
* 特権ロールへのアクセスに関するアラートを受け取る

Azure AD Privileged Identity Management では、次のような組み込みの Azure AD 組織ロールを管理できます (ただし、これらに限定されません)。  

* グローバル管理者
* 課金管理者
* サービス管理者  
* ユーザー管理者
* パスワード管理者

## <a name="just-in-time-administrator-access"></a>ジャスト イン タイム管理者アクセス
従来、管理者ロールへのユーザーの割り当てには、Azure クラシック ポータルまたは Windows PowerShell が使用されていました。 その結果、そのユーザーは**永続的な管理者**になり、割り当てられたロールが常に有効になっています。 Azure AD Privileged Identity Management では、**管理者候補**という概念が導入されています。 管理者候補とは、常にではなく時折特権アクセスを必要とするユーザーのことです。 このロールは、このユーザーがアクセス権を必要とするまで非アクティブ化されています。そして、ユーザーがアクティブ化プロセスを完了すると、所定の時間の間だけ有効な管理者になります。

## <a name="enable-privileged-identity-management-for-your-directory"></a>ディレクトリで Privileged Identity Management を有効にする
Azure AD Privileged Identity Management の使用は、 [Azure Portal](https://portal.azure.com/) で開始できます。

> [!NOTE]
> ディレクトリで Azure AD Privileged Identity Management を有効にする操作は、Microsoft アカウント (例: @outlook.com) ではなく組織アカウント (例: @yourdomain.com) を持つグローバル管理者が行う必要があります。

1. ディレクトリのグローバル管理者として [Azure ポータル](https://portal.azure.com/) にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure Portal の右上隅に表示されているユーザー名を選択し、 Azure AD Privileged Identity Management を使用するディレクトリを選択します。
3. **[その他のサービス]** を選択し、[フィルター] ボックスを使用して "**Azure AD Privileged Identity Management**" を検索します。
4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。 Privileged Identity Management アプリケーションが起動します。

ディレクトリ内で Azure AD Privileged Identity Management を使用する最初のユーザーには、 [セキュリティ ウィザード](active-directory-privileged-identity-management-security-wizard.md) に最初の割り当て操作の手順が表示されます。 手順を実行した後、ディレクトリの最初の**セキュリティ管理者**と**特権ロール管理者**に自動的に設定されます。

他の管理者のアクセスを管理できるのは、特権ロール管理者のみです。 特権ロール管理者は [PIM で管理する権限を他のユーザーに付与する](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)ことができます。

## <a name="privileged-identity-management-dashboard"></a>Privileged Identity Management ダッシュボード
Azure AD Privileged Identity Manager には、次のような重要な情報を提供するダッシュボードがあります。

* セキュリティ強化の機会を知らせるアラート
* 各特権ロールに割り当てられているユーザーの数  
* 管理者候補および永続的管理者の数
* 実行中のアクセス レビュー

![PIM dashboard - screenshot][2]

## <a name="privileged-role-management"></a>特権ロールの管理
Azure AD Privileged Identity Management では、各ロールに永続的管理者または管理者候補を追加または削除することによって、管理者を管理できます。

![PIM の管理者の追加/削除 - スクリーンショット][3]

## <a name="configure-the-role-activation-settings"></a>ロール アクティブ化設定の構成
[ロールの設定](active-directory-privileged-identity-management-how-to-change-default-settings.md) を使用して、次のような候補ロールのアクティブ化プロパティを構成できます。

* ロールのアクティブ化の期間
* ロールのアクティブ化の通知
* ロール アクティブ化プロセス中にユーザーが提供する必要のある情報  

![PIM 設定 - 管理者アクティブ化 - スクリーンショット][4]

上図で、 **多要素認証** のボタンが無効化されていることに注意してください。 特定の高い特権を持つロールでは、MFA を使用して保護を強化する必要があります。

## <a name="role-activation"></a>ロールのアクティブ化
[ロールをアクティブ化する](active-directory-privileged-identity-management-how-to-activate-role.md)には、管理者候補は、ロールに対する時間制限付きの "アクティブ化" を要求します。 アクティブ化の要求は、Azure AD Privileged Identity Management の **[ロールのアクティブ化]** オプションを使用して行うことができます。

ロールのアクティブ化を要求する管理者は、Azure Portal で Azure AD Privileged Identity Management を初期化する必要があります。

ロールのアクティブ化は、カスタマイズできます。 PIM の設定では、アクティブ化の期間だけでなく、ロールをアクティブ化するために管理者が指定する必要がある情報を設定できます。

![PIM 管理者のロール アクティブ化要求 - スクリーン ショット][5]

## <a name="review-role-activity"></a>ロール アクティビティの確認
従業員および管理者がどのように特権ロールを使用しているかを追跡するには、2 つの方法があります。 1 つ目の方法は、 [監査履歴](active-directory-privileged-identity-management-how-to-use-audit-log.md)を使用する方法です。 監査履歴ログは、特権ロールの割り当てとロールのアクティブ化の履歴における変更を追跡します。

![PIM アクティブ化履歴 - スクリーンショット][6]

2 番目の方法は、標準的な [アクセス レビュー](active-directory-privileged-identity-management-how-to-start-security-review.md)を設定する方法です。 これらのアクセス レビューは、割り当てられたレビュー担当者 (たとえば、チーム マネージャー) によって実行することも、従業員が自分自身の確認を行うこともできます。 これは、アクセスを必要としているユーザーとアクセスを必要としなくなったユーザーを監視するための最善の方法です。

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM のサブスクリプションの有効期限
Azure AD PIM は一般公開前のプレビュー段階にあり、テナントで Azure AD PIM をプレビューするためのライセンスのチェックがありませんでした。  Azure AD PIM が一般公開で利用できるようになったため、2016 年 12 月以降、PIM の使用を継続するには、試用版または有料サブスクリプションがテナントに存在する必要があります。  組織が Azure AD Premium P2 を購入していなかったり、サブスクリプションの有効期限が切れている場合は、テナントで Azure AD PIM を利用できなくなります。  詳しくは、「[Azure AD PIM subscription requirements](./privileged-identity-management/subscription-requirements.md)」(Azure AD PIM サブスクリプションの要件) をご覧ください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

