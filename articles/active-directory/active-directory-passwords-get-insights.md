---
title: "Get Insights: Azure AD のパスワード管理レポート | Microsoft Docs"
description: "この記事では、レポートを使用して、組織内のパスワード管理操作を把握する方法について説明します。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 1472b51d-53f4-4b0f-b1be-57f6fa88fa65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: e4e9e16c62ab1262021d1a719f5aa15d936f4843
ms.lasthandoff: 03/28/2017


---
# <a name="how-to-get-operational-insights-with-password-management-reports"></a>パスワード管理レポートで運用情報を把握する方法
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
>
>

このセクションでは、Azure Active Directory のパスワード管理レポートを使用して、ユーザーが組織内でどのようにパスワード リセットや変更を使用しているかを表示する方法について説明します。

* [**パスワード管理レポートの概要**](#overview-of-password-management-reports)
* [**新しい Azure Portal でパスワード管理レポートを表示する方法**](#how-to-view-password-management-reports)
 * [レポートの読み取りを許可されているディレクトリ ロール](#directory-roles-allowed-to-read-reports)
* [**新しい Azure Portal での [Self-service Password Management (セルフサービスのパスワード管理)] のアクティビティの種類**](#self-service-password-management-activity-types)
 * [Blocked from self-service password reset (セルフサービスのパスワード リセットのブロック)](#activity-type-blocked-from-self-service-password-reset)
 * [Change password (self-service) (パスワードの変更 (セルフサービス))](#activity-type-change-password-self-service)
 * [Reset password (by admin) (パスワードのリセット (管理者))](#activity-type-reset-password-by-admin)
 * [Reset password (self-service) (パスワードのリセット (セルフサービス))](#activity-type-reset-password-self-service)
 * [Self serve password reset flow activity progress (セルフサービスのパスワード リセット フロー アクティビティの進行状況)](#activity-type-self-serve-password-reset-flow-activity-progress)
 * [Unlock user account (self-service) (ユーザー アカウントのロック解除 (セルフサービス))](#activity-type-unlock-user-account-self-service)
 * [User registered for self-service password reset (セルフサービスのパスワード リセットを登録したユーザー)](#activity-type-user-registered-for-self-service-password-reset)
* [**Azure AD レポートおよびイベント API からパスワード管理イベントを取得する方法**](#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
 * [レポート API によるデータ取得の制限事項](#reporting-api-data-retrieval-limitations)
* [**PowerShell を使用してパスワード リセット登録イベントをすばやくダウンロードする方法**](#how-to-download-password-reset-registration-events-quickly-with-powershell)
* [**クラシック ポータルでパスワード管理レポートを表示する方法**](#how-to-view-password-management-reports-in-the-classic-portal)
* [**クラシック ポータルでの組織内のパスワード リセット登録アクティビティの表示**](#view-password-reset-registration-activity-in-the-classic-portal)
* [**クラシック ポータルでの組織内のパスワード リセット アクティビティの表示**](#view-password-reset-activity-in-the-classic-portal)


## <a name="overview-of-password-management-reports"></a>パスワード管理レポートの概要
パスワード リセットをデプロイした後、一般的な手順では、次に組織内でリセットがどのように使用されているかを確認します。  たとえば、ユーザーが登録しているパスワード リセット方法、または過去数日間にパスワード リセットが実行された回数を確認できます。  次に、現在、[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)のパスワード管理レポートを使用して確認できる一般的な項目をいくつか示します。

* パスワード リセットを登録した人数
* パスワード リセットを登録したユーザー
* ユーザーが登録しているデータ
* 過去 7 日間で自分のパスワードをリセットしたユーザー数
* パスワードをリセットするためにユーザーまたは管理者が使用する一般的な方法
* パスワード リセットを試みる場合に、ユーザーまたは管理者が直面する一般的な問題
* 自らのパスワードを頻繁にリセットしている管理者
* パスワード リセットに関する不審なアクティビティの有無

## <a name="how-to-view-password-management-reports"></a>パスワード管理レポートを表示する方法
新しい [Azure Portal](https://portal.azure.com) エクスペリエンスでは、パスワード リセットおよびパスワード リセット登録アクティビティの表示方法が改良されています。  新しい [Azure Portal](https://portal.azure.com) でパスワード リセットおよびパスワード リセット登録イベントを表示するには、次の手順に従います。

1. [**portal.azure.com**](https://portal.azure.com) に移動します。
2. Azure Portal メインの左側のウィンドウで、**[その他のサービス]** メニューをクリックします。
3. サービスの一覧で、**Azure Active Directory** を検索して選択します。
4. Azure Active Directory のナビゲーション メニューで **[ユーザーとグループ]** をクリックします。
5. [ユーザーとグループ] のナビゲーション メニューで、**[監査ログ]** ナビゲーション項目をクリックします。 これにより、ディレクトリ内のすべてのユーザーに対して発生しているすべての監査イベントが表示されます。 このビューをフィルター処理して、すべてのパスワード関連イベントを表示することもできます。
6. パスワード管理イベントのみが表示されるようにこのビューをフィルター処理するには、ブレードの上部にある **[フィルター]** ボタンをクリックします。
7. **[フィルター]** メニューから **[カテゴリ]** ドロップダウンを選択し、カテゴリの種類を **[Self-service Password Management (セルフサービスのパスワード管理)]** に変更します。
8. 必要な場合は特定の **[アクティビティ]** を選択して、さらにフィルター処理します。
### <a name="direct-link-to-user-audit-blade"></a>ユーザー監査ブレードへの直接リンク
ポータルにサインインしている場合は、次のリンクから、これらのイベントを確認できるユーザー監査ブレードに直接移動することができます。

* [ユーザー管理監査ビューに直接移動する](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit)

### <a name="directory-roles-allowed-to-read-reports"></a>レポートの読み取りを許可されているディレクトリ ロール
現時点では、次のディレクトリ ロールが Azure クラシック ポータルで Azure AD パスワード管理レポートを読み取ることができます。

* グローバル管理者

会社のグローバル管理者がこれらのレポートを読み取るには、事前に 1 回以上レポート タブまたは監査ログにアクセスして、このデータを組織の利益のために収集することを選択しておく必要があります。 この選択を行うまでは、データが組織のために収集されません。

ディレクトリ ロールと各ロールが実行できる操作の詳細については、「[Azure Active Directory での管理者ロールの割り当て](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles)」をご覧ください。

## <a name="self-service-password-management-activity-types"></a>[Self-service Password Management (セルフサービスのパスワード管理)] のアクティビティの種類
**[Self-Service Password Management (セルフサービスのパスワード管理)]** 監査イベント カテゴリには、次のアクティビティの種類が表示されます。  一覧の後に、各種類の説明を示します。

* [**Blocked from self-service password reset (セルフサービスのパスワード リセットのブロック)**](#activity-type-blocked-from-self-service-password-reset) - ユーザーが 24 時間以内に合計 5 回以上、パスワードのリセット、特定のゲートの使用、または電話番号の確認を試行したことを示します。
* [**Change password (self-service) (パスワードの変更 (セルフサービス))**](#activity-type-change-password-self-service) - ユーザーが自発的または (期限切れにより) 強制的にパスワードの変更を実行したことを示します。
* [**Reset password (by admin) (パスワードのリセット (管理者))**](#activity-type-reset-password-by-admin) - 管理者が Azure Portal からユーザーに代わってパスワードのリセットを実行したことを示します。
* [**Reset password (self-service) (パスワードのリセット (セルフサービス))**](#activity-type-reset-password-self-service) - ユーザーが[Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)から自分のパスワードのリセットに成功したことを示します。
* [**Self serve password reset flow activity progress (セルフサービスのパスワード リセット フロー アクティビティの進行状況)**](#activity-type-self-serve-password-reset-flow-activity-progress) - ユーザーがパスワード リセット プロセスの一環として実行する個々の特定の手順 (特定のパスワード リセット認証ゲートの通過など) を示します。
* [**Unlock user account (self-service) (ユーザー アカウントのロック解除 (セルフサービス))**](#activity-type-unlock-user-account-self-service) - ユーザーが [Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)から[リセットなしの AD アカウントのロック解除](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password)機能を使用して、自分のパスワードをリセットせずに自分の Active Directory アカウントのロック解除に成功したことを示します。
* [**User registered for self-service password reset (セルフサービスのパスワード リセットを登録したユーザー)**](#activity-type-user-registered-for-self-service-password-reset) - ユーザーが、現在指定されているテナント パスワード リセット ポリシーに従って、自分のパスワードをリセットするために必要なすべての情報を登録したことを示します。

### <a name="activity-type-blocked-from-self-service-password-reset"></a>アクティビティの種類: Blocked from self-service password reset (セルフサービスのパスワード リセットのブロック)
このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが 24 時間以内に合計 5 回以上、パスワードのリセット、特定のゲートの使用、または電話番号の確認を試行したことを示します。
* **アクティビティのアクター** - 追加のリセット操作の実行を禁止されたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 追加のリセット操作の実行を禁止されたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
 * _成功_ - ユーザーが24 時間以内の追加のリセットの実行、追加の認証方法の試行、または追加の電話番号の確認を禁止されたことを示します。
* **アクティビティの状態が失敗になった理由** - 該当なし

### <a name="activity-type-change-password-self-service"></a>アクティビティの種類: Change password (self-service) (パスワードの変更 (セルフサービス))
このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが自発的または (期限切れにより) 強制的にパスワードの変更を実行したことを示します。
* **アクティビティのアクター** - 自分のパスワードを変更したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 自分のパスワードを変更したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
 * _成功_ - ユーザーが自分のパスワードの変更に成功したことを示します。
 * _失敗_ - ユーザーが自分のパスワードの変更に失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。
* **アクティビティの状態が失敗になった理由** -
 * _FuzzyPolicyViolationInvalidPassword_ - ユーザーが選択したパスワードが、マイクロソフトの禁止されたパスワードの検出機能によって一般的すぎるパスワードまたは特に脆弱なパスワードと見なされたため、自動的に禁止されました。

### <a name="activity-type-reset-password-by-admin"></a>アクティビティの種類: Reset password (by admin) (パスワードのリセット (管理者))
このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - 管理者が Azure Portal からユーザーに代わってパスワードのリセットを実行したことを示します。
* **アクティビティのアクター** -別のエンドユーザーまたは管理者に代わってリセットを実行した管理者。 グローバル管理者、パスワード管理者、ユーザー管理者、ヘルプデスク管理者のいずれかである可能性があります。
* **アクティビティのターゲット** - 自分のパスワードがリセットされたユーザー。 エンドユーザーまたは別の管理者である可能性があります。
* **使用できるアクティビティの状態**
 * _成功_ - 管理者がユーザーのパスワードのリセットに成功したことを示します。
 * _失敗_ - 管理者がユーザーのパスワードのリセットに失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。

### <a name="activity-type-reset-password-self-service"></a>アクティビティの種類: Reset password (self-service) (パスワードのリセット (セルフサービス))
このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが[Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)から自分のパスワードのリセットに成功したことを示します。
* **アクティビティのアクター** - 自分のパスワードをリセットしたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 自分のパスワードをリセットしたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
 * _成功_ - ユーザーが自分のパスワードのリセットに成功したことを示します。
 * _失敗_ - ユーザーが自分のパスワードのリセットに失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。
* **アクティビティの状態が失敗になった理由** -
 * _FuzzyPolicyViolationInvalidPassword_ - 管理者が選択したパスワードが、マイクロソフトの禁止されたパスワードの検出機能によって一般的すぎるパスワードまたは特に脆弱なパスワードと見なされたため、自動的に禁止されました。

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>アクティビティの種類: Self serve password reset flow activity progress (セルフサービスのパスワード リセット フロー アクティビティの進行状況)
このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーがパスワード リセット プロセスの一環として実行する個々の特定の手順 (特定のパスワード リセット認証ゲートの通過など) を示します。
* **アクティビティのアクター** - パスワード リセット フローの一部を実行したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - パスワード リセット フローの一部を実行したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
 * _成功_ - ユーザーがパスワード リセット フローの特定の手順に成功したことを示します。
 * _失敗_ - パスワード リセットフローの特定の手順が失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。
* **使用可能なアクティビティの状態の理由**
 * [使用可能なすべてのリセット アクティビティの状態の理由](#allowed-values-for-details-column)については、下の表をご覧ください。

### <a name="activity-type-unlock-user-account-self-service"></a>アクティビティの種類: Unlock user account (self-service) (ユーザー アカウントのロック解除 (セルフサービス))
このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが [Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)から[リセットなしの AD アカウントのロック解除](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password)機能を使用して、自分のパスワードをリセットせずに自分の Active Directory アカウントのロック解除に成功したことを示します。
* **アクティビティのアクター** - 自分のパスワードをリセットせずに自分のアカウントをロック解除したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 自分のパスワードをリセットせずに自分のアカウントをロック解除したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
 * _成功_ - ユーザーが自分のアカウントのロック解除に成功したことを示します。
 * _失敗_ - ユーザーが自分のアカウントのロック解除に失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>アクティビティの種類: User registered for self-service password reset (セルフサービスのパスワード リセットを登録したユーザー)
このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが、現在指定されているテナント パスワード リセット ポリシーに従って、自分のパスワードをリセットするために必要なすべての情報を登録したことを示します。
* **アクティビティのアクター** - パスワード リセットに登録したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - パスワード リセットに登録したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
 * _成功_ - ユーザーが現在のポリシーに従ってパスワード リセットの登録に成功したことを示します。
 * _失敗_ - ユーザーがパスワード リセットの登録に失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。 注 - これはユーザーが自分のパスワードをリセットできないということではなく、単に登録プロセスを完了しなかったことを意味します。 ユーザーのアカウントに未確認の正しいデータ (未確認の電話番号など) がある場合、そのデータを確認していなくても、パスワードのリセットに使用することができます。 詳細については、「[ユーザーの登録時に発生すること](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)」をご覧ください。

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Azure AD レポートおよびイベント API からパスワード管理イベントを取得する方法
2015 年 8 月から、Azure AD レポートおよびイベント API で、パスワード リセットおよびパスワード リセット登録レポートに含まれるすべての情報の取得がサポートされるようになりました。 この API を使用して、任意のレポート テクノロジと統合するために個別のパスワード リセットおよびパスワード リセット登録イベントをダウンロードできます。

### <a name="how-to-get-started-with-the-reporting-api"></a>レポート API の使用を開始する方法
このデータにアクセスするには、サーバーからデータを取得するための小規模なアプリケーションまたはスクリプトを作成する必要があります。 [Azure AD Reporting API の使用を開始する方法](active-directory-reporting-api-getting-started.md)

作業用スクリプトの準備ができたら、シナリオの条件を満たすために取得可能なパスワード リセットおよび登録イベントを調査します。

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): パスワード リセット イベントで利用可能な列を一覧します。
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): パスワード リセット登録イベントで利用可能な列を一覧します。

### <a name="reporting-api-data-retrieval-limitations"></a>レポート API によるデータ取得の制限事項
現時点では、Azure AD レポートおよびイベント API で取得できるのは**過去 30 日間**の [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) および [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) タイプの最大 **75,000 個のイベント**です。

この枠を超えてデータを取得および保存する必要がある場合は、データを外部データベースに保存し、結果として作成されたデルタを API を使用してクエリすることをお勧めします。 ベスト プラクティスの 1 つは、組織内でパスワード リセット登録プロセスを開始したときにこのデータの取得を開始し、データを外部に永続的に保存してからそれ以降のデルタを追跡し続けることです。

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>PowerShell を使用してパスワード リセット登録イベントをすばやくダウンロードする方法
Azure AD レポートおよびイベント API を直接使用する方法に加えて、以下の PowerShell スクリプトを使用してディレクトリ内の最近の登録イベントをダウンロードすることもできます。 この方法は、最近登録したユーザーを表示したい場合や、パスワード リセットのロールアウトが想定どおりに実行されていることを確認したい場合に便利です。

* [Azure AD SSPR 登録アクティビティの PowerShell スクリプト](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

## <a name="how-to-view-password-management-reports-in-the-classic-portal"></a>クラシック ポータルでパスワード管理レポートを表示する方法
パスワード管理レポートを参照するには、次の手順に従います。

1. **Azure クラシック ポータル**で [[Active Directory]](https://manage.windowsazure.com) 拡張機能をクリックします。
2. ポータルに表示される一覧から、ディレクトリを選択します。
3. **[レポート]** タブをクリックします。
4. **[動作状況のログ]** セクションの下で、
5. **[パスワード リセット アクティビティ]** レポートまたは **[パスワード リセット登録アクティビティ]** レポートのいずれかを選択します。

   ![][001]

## <a name="view-password-reset-registration-activity-in-the-classic-portal"></a>クラシック ポータルでのパスワード リセット登録アクティビティの表示
パスワード リセット登録アクティビティ レポートでは、組織で実行されたすべてのパスワード リセットの登録が表示されます。  パスワード リセットの登録は、パスワード リセット登録ポータル ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) で認証情報を正常に登録したユーザーについて、このレポートに表示されます

* **最大期間**: 30 日
* **最大行数**: 75,000
* **ダウンロードの可否**: CSV ファイルでダウンロードできます。

    ![][002]

### <a name="description-of-report-columns"></a>レポートの列の説明
次の一覧では、レポートの各列について詳細に説明します。

* **ユーザー** – パスワード リセット登録操作を試みたユーザー。
* **ロール** – ディレクトリ内のユーザーの役割。
* **日付と時刻** – 試行の日付と時刻。
* **登録データ** – パスワード リセット登録中にユーザーが提供した認証データ。

### <a name="description-of-report-values"></a>レポートの値の説明
次の表には、各列で使用できるさまざまな値について説明します。

| 分割 | 使用できる値とその意味 |
| --- | --- |
| 登録データ |**連絡用電子メール** – ユーザーが、連絡用電子メールまたは認証用電子メールを認証のために使用しました。<p><p>**会社電話** – ユーザーが、会社電話を認証のために使用しました。<p>**携帯電話** – ユーザーが、携帯電話または認証用の電話を認証のために使用しました。<p>**秘密の質問** – ユーザーが、秘密の質問を認証のために使用しました。<p>**上の任意の組み合わせ (連絡用電子メール + 携帯電話など)** – 2 ゲート ポリシーを指定した場合に発生し、パスワード リセット要求を認証するためにユーザーがいずれの方法を使用したかを示します。 |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>クラシック ポータルでのパスワード リセット アクティビティの表示
このレポートには、組織で実行されたすべてのパスワード リセット試行が表示されます。

* **最大期間**: 30 日
* **最大行数**: 75,000
* **ダウンロードの可否**: CSV ファイルでダウンロードできます。

    ![][003]

### <a name="description-of-report-columns"></a>レポートの列の説明
次の一覧では、レポートの各列について詳細に説明します。

1. **ユーザー** – パスワード リセット操作を試みたユーザー (ユーザーがパスワードをリセットするときに指定するユーザー ID フィールドに基づきます)。
2. **ロール** – ディレクトリ内のユーザーの役割。
3. **日付と時刻** – 試行の日付と時刻。
4. **使用された方法** – このリセット操作でユーザーが使用した認証方法。
5. **結果** – パスワード リセット操作の最終的な結果。
6. **詳細** – パスワード リセットによって、表示された値についての理由の詳細。  予期しないエラーを解決するために実行できる軽減手順も含まれます。

### <a name="description-of-report-values"></a>レポートの値の説明
次の表には、各列で使用できるさまざまな値について説明します。

| 分割 | 使用できる値とその意味 |
| --- | --- |
| 使用された方法 |**連絡用電子メール** – ユーザーが、連絡用電子メールまたは認証用電子メールを認証のために使用しました。<p>**会社電話** – ユーザーが、会社電話を認証のために使用しました。<p>**携帯電話** – ユーザーが、携帯電話または認証用の電話を認証のために使用しました。<p>**秘密の質問** – ユーザーが、秘密の質問を認証のために使用しました。<p>**上の任意の組み合わせ (連絡用電子メール + 携帯電話など)** – 2 ゲート ポリシーを指定した場合に発生し、パスワード リセット要求を認証するためにユーザーがいずれの方法を使用したかを示します。 |
| 結果 |**中止** – ユーザーはパスワード リセットを開始しましたが、完了せずに途中で中止しました。<p>**ブロック** - 24 時間以内にパスワード リセット ページまたは単一のパスワード リセット ゲートを多く使用しすぎたため、ユーザーのアカウントで、パスワード リセットの使用が禁止されました。<p>**キャンセル** – ユーザーはパスワード リセットを開始しましたが、途中で [キャンセル] ボタンをクリックして、セッションをキャンセルしました。 <p>**管理者への連絡** – 解決できない問題がセッション中に発生したため、ユーザーはパスワード リセット フローを完了せずに、[管理者に連絡] リンクをクリックしました。<p>**失敗** – おそらくユーザーがこの機能を使用するように構成されていなかったため、パスワードをリセットできませんでした (ライセンスがない、認証情報が不足、パスワードがオンプレミスで管理されているが書き戻しがオフになっている場合など)。<p>**成功** – パスワード リセットは成功しました。 |
| 詳細 |次の表を参照してください。 |

### <a name="allowed-values-for-details-column"></a>[詳細] 列で許可される値
パスワード リセット アクティビティ レポートを使用している場合に、想定される結果の種類の一覧を以下に示します。

| 詳細 | 結果の種類 |
| --- | --- |
| ユーザーは、電子メールの確認オプションの完了後に中止しました。 |中止 |
| ユーザーは、モバイル SMS の確認オプションの完了後に中止しました。 |中止 |
| ユーザーは、音声通話の確認オプションの完了後に中止しました。 |中止 |
| ユーザーは、会社音声通話の確認オプションの完了後に中止しました。 |中止 |
| ユーザーは、秘密の質問オプションの完了後に中止しました。 |中止 |
| ユーザーは、ユーザー ID の入力後に中止しました。 |中止 |
| ユーザーは、電子メールの確認オプションの開始後に中止しました。 |中止 |
| ユーザーは、モバイル SMS の確認オプションの開始後に中止しました。 |中止 |
| ユーザーは、音声通話の確認オプションの開始後に中止しました。 |中止 |
| ユーザーは、会社音声通話の確認オプションの開始後に中止しました。 |中止 |
| ユーザーは、秘密の質問オプションの開始後に中止しました。 |中止 |
| ユーザーは、新しいパスワードを選択する前に中止しました。 |中止 |
| ユーザーは、新しいパスワードを選択するときに中止しました。 |中止 |
| ユーザーは、非常に多くの無効な SMS 確認コードを入力したため、24 時間ブロックされました。 |ブロック |
| ユーザーは、携帯電話の音声確認を何度も試行したため、24 時間ブロックされました。 |ブロック |
| ユーザーは、会社電話の音声確認を何度も試行したため、24 時間ブロックされました。 |ブロック |
| ユーザーは、秘密の質問に何度も答えようとしたため、24 時間ブロックされました。 |ブロック |
| ユーザーは、電話番号を何度も確認しようとしたため、24 時間ブロックされました。 |ブロック |
| ユーザーは、必要な認証方式に合格する前にキャンセルしました。 |キャンセル |
| ユーザーは、新しいパスワードを送信する前にキャンセルしました。 |キャンセル |
| ユーザーは、電子メールの確認オプションを試行した後に、管理者に連絡しました。 |管理者への連絡 |
| ユーザーは、モバイル SMS の確認オプションを試行した後に、管理者に連絡しました。 |管理者への連絡 |
| ユーザーは、モバイル音声通話の確認オプションを試行した後に、管理者に連絡しました。 |管理者への連絡 |
| ユーザーは、会社音声通話の確認オプションを試行した後に、管理者に連絡しました。 |管理者への連絡 |
| ユーザーは、秘密の質問の確認オプションを試行した後に、管理者に連絡しました。 |管理者への連絡 |
| パスワード リセットは、このユーザーに対して有効になっていません。 この問題を解決するには、[構成] タブで、パスワード リセットを有効にします。 |失敗 |
| ユーザーには、ライセンスがありません。 この問題を解決するには、ユーザーにライセンスを追加します。 |失敗 |
| ユーザーは、Cookie を有効にしていないデバイスからリセットしようとしました。 |失敗 |
| ユーザーのアカウントでは、認証方法が十分に定義されていません。 この問題を解決するには、認証情報を追加します。 |失敗 |
| ユーザーのパスワードは、オンプレミスで管理されています。 この問題を解決するには、パスワードの書き戻しを有効にします。 |失敗 |
| オンプレミス パスワード リセット サービスに接続できませんでした。 同期コンピューターのイベント ログを確認してください。 |失敗 |
| ユーザーのオンプレミスのパスワードのリセット中に問題が発生しました。 同期コンピューターのイベント ログを確認してください。 |失敗 |
| このユーザーは、パスワード リセット ユーザー グループのメンバーではありません。 この問題を解決するには、このグループに、ユーザーを追加します。 |失敗 |
| パスワード リセットが、このテナントですべて無効になっています。 これを解決するには、 [ここ](http://aka.ms/ssprtroubleshoot) を参照してください。 |失敗 |
| ユーザーが、正常にパスワードをリセットしました。 |成功 |

## <a name="next-steps"></a>次のステップ
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**概要**](active-directory-passwords-getting-started.md) - ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。

[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"

