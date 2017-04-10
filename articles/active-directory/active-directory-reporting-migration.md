---
title: "Azure Portal でアクティビティ レポートを見つける | Microsoft Docs"
description: "Azure Portal で Azure Active Directory のアクティビティ レポートを見つける方法について説明します。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: f2f5ed98df88df69ee300c9da94ad013a631b3b9
ms.lasthandoff: 03/14/2017


---
# <a name="find-activity-reports-in-the-azure-portal"></a>Azure Portal でアクティビティ レポートを見つける

Azure クラシック ポータルから Azure Portal に移行すると、Azure Active Directory (Azure AD) のアクティビティ ログが新しい形式で表示されます。 最近の[ブログの投稿](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)で、アクティビティ ログを作業対象のリソースのコンテキストで Azure Portal に表示する方法について説明しています。 この記事では、Azure クラシック ポータルで使用していたレポートを Azure Portal で見つける方法について説明します。

## <a name="whats-new"></a>新機能

Azure クラシック ポータルでは、レポートは次のカテゴリに分けられています。

1.    セキュリティ レポート
2.    アクティビティ レポート
3.    統合アプリケーション レポート

### <a name="activity-and-integrated-app-reports"></a>アクティビティ レポートと統合アプリケーション レポート

Azure Portal のコンテキストに基づくレポートでは、既存のレポートは 1 つのビューに統合されています。 このビューのデータは、単一の API によって提供されます。

このビューを表示するには、**[Azure Active Directory]** ブレードで、**[アクティビティ]** の下の **[監査ログ]** を選択します。

![監査ログ](./media/active-directory-reporting-migration/482.png "監査ログ")

このビューには、次のレポートが統合されています。

-    監査レポート
-     パスワード リセット アクティビティ
-     パスワード リセット登録アクティビティ
-     セルフ サービス グループ アクティビティ
-     Office 365 グループ名の変更
-     アカウント プロビジョニングのアクティビティ
-     パスワード ロールオーバーの状態
-     アカウント プロビジョニング エラー


アプリケーションの使用状況レポートは強化され、**[サインイン]** ビューに表示されるようになりました。 このビューを表示するには、**[Azure Active Directory]**ブレードで、**[アクティビティ]** の下の **[サインイン]** を選択します。

![[サインイン] ビュー](./media/active-directory-reporting-migration/483.png "[サインイン] ビュー")

**[サインイン]** ビューには、すべてのユーザーのサインインが表示されます。 この情報を使用して、アプリケーションの使用状況に関する情報を取得できます。 アプリケーションの使用状況に関する情報は、**[管理]** セクションの **[エンタープライズ アプリケーション]**で概要を確認することもできます。

![[エンタープライズ アプリケーション]](./media/active-directory-reporting-migration/484.png "[エンタープライズ アプリケーション]")

## <a name="access-a-specific-report"></a>特定のレポートへのアクセス

Azure Portal では単一のビューが用意されていますが、特定のレポートを表示することもできます。

### <a name="audit-logs"></a>監査ログ

ユーザーからのフィードバックに応じて、Azure Portal では、高度なフィルター処理を使用して目的のデータにアクセスできます。 使用できる 1 つのフィルターは*アクティビティのカテゴリ*です。これは、Azure AD のさまざまな種類のアクティビティ ログを一覧表示します。 結果を絞り込むために、カテゴリを選択することができます。

たとえば、セルフサービスによるパスワードのリセットに関連するアクティビティのみを確認する場合は、**[Self-service Password Management (セルフサービスによるパスワード管理)]** カテゴリを選択できます。 表示されるカテゴリは、作業対象のリソースに応じて異なります。  

![[監査ログのフィルター] ページの [カテゴリ] オプション](./media/active-directory-reporting-migration/06.png "[監査ログのフィルター] ページの [カテゴリ] オプション")

アクティビティのカテゴリには、以下が含まれます。

- Core Directory (コア ディレクトリ)
- Self-service Password Management \(セルフサービスによるパスワード管理)
- セルフサービスによるグループ管理
- アカウント プロビジョニング

### <a name="application-usage"></a>アプリケーションの使用状況

すべてのアプリまたは 1 つのアプリの使用状況に関する詳細を表示するには、**[アクティビティ]** の下の **[サインイン]** を選択します。 結果を絞り込むために、ユーザー名またはアプリケーション名でフィルター処理できます。

![[サインイン イベントのフィルター処理] ページ](./media/active-directory-reporting-migration/07.png "[サインイン イベントのフィルター処理] ページ")

### <a name="security-reports"></a>セキュリティ レポート

#### <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 異常アクティビティ レポート

Azure クラシック ポータルでの Azure AD の異常なアクティビティに関するセキュリティレポートは、1 つの中心となるビューに統合されています。 このビューには、Azure AD が検出して報告できるセキュリティ関連のリスク イベントがすべて表示されます。

次の表は、Azure AD 異常アクティビティ セキュリティ レポートと、各レポートに対応する Azure Portal のリスク イベントの種類を示しています。

| Azure AD 異常アクティビティ レポート |  Identity Protection のリスク イベントの種類|
| :--- | :--- |
| 資格情報が漏洩したユーザー | 漏洩した資格情報 |
| 不規則なサインイン アクティビティ | 特殊な場所へのあり得ない移動 |
| 感染している可能性があるデバイスからのサインイン | 感染しているデバイスからのサインイン|
| 不明なソースからのサインイン | 匿名の IP アドレスからのサインイン |
| 不審なアクティビティのある IP アドレスからのサインイン | 不審なアクティビティのある IP アドレスからのサインイン |
| - | 未知の場所からのサインイン |

次の Azure AD 異常アクティビティ セキュリティ レポートは、Azure Portal ではリスク イベントに含まれません。

* 複数のエラー後のサインイン
* 複数の地域からのサインイン

これらのレポートは、Azure クラシック ポータルで引き続き使用できますが、今後廃止される予定です。

詳細については、「[Azure Active Directory リスク イベント](active-directory-identity-protection-risk-events.md)」を参照してください。  


#### <a name="detected-risk-events"></a>検出されたリスク イベント

Azure Portal では、**[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、検出されたリスク イベントに関するレポートにアクセスできます。 検出されたリスク イベントは、次のレポートで追跡されます。   

- 危険な状態のユーザー
- リスクの高いサインイン

![セキュリティ レポート](./media/active-directory-reporting-migration/04.png "セキュリティ レポート")

セキュリティ レポートの詳細については、次のトピックを参照してください。

- [Azure Active Directory ポータルの危険な状態のユーザー セキュリティ レポート - プレビュー](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory ポータルのリスクの高いサインイン レポート - プレビュー](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Azure クラシック ポータルと Azure Portal のアクティビティ レポートの違い

このセクションの表は、Azure クラシック ポータルの既存のレポートを示しています。 同じ情報を Azure Portal で取得する方法も説明されています。

すべての監査データを表示するには、**[Azure Active Directory]** ブレードで、**[アクティビティ]** の下の **[監査ログ]** を選択します。

![監査ログ](./media/active-directory-reporting-migration/61.png "監査ログ")

| Azure クラシック ポータル                 | Azure Potal で見つける方法                                                         |
| ---                                  | ---                                                                        |
| 監査ログ                           | **アクティビティのカテゴリ**として **[コア ディレクトリ]** を選択します。                       |
| パスワード リセット アクティビティ              | **アクティビティのカテゴリ**として **[Self-service Password Management] \(セルフサービスによるパスワード管理)** を選択します。 |
| パスワード リセット登録アクティビティ | **アクティビティのカテゴリ**として **[Self-service Password Management] \(セルフサービスによるパスワード管理)** を選択します。     |
| セルフ サービス グループ アクティビティ         | **アクティビティのカテゴリ**として **[セルフサービスによるグループ管理]** を選択します。        |
| アカウント プロビジョニングのアクティビティ        | **アクティビティのカテゴリ**として **[Account User Provisioning (アカウント ユーザーのプロビジョニング)]** を選択します。         |
| パスワード ロールオーバーの状態             | **アクティビティのカテゴリ**として、**[Automatic App Password Rollover (アプリ パスワードの自動ロールオーバー)]** を選択します。      |
| アカウント プロビジョニング エラー          | **アクティビティのカテゴリ**として **[Account User Provisioning (アカウント ユーザーのプロビジョニング)]** を選択します。        |
| Office 365 グループ名の変更         | **アクティビティのカテゴリ**として **[Self-service Password Management] \(セルフサービスによるパスワード管理)** を選択します。 **アクティビティのリソースの種類**として **[グループ]** を選択します。 **アクティビティのソース**として **[O365 グループ]** を選択します。|

**アプリケーションの使用状況**レポートに表示するには、**[Azure Active Directory]** ブレードで、**[管理]** の下の **[エンタープライズ アプリケーション]** を選択し、**[サインイン]** を選択します。


![[エンタープライズ アプリケーション]、[サインイン]、レポート](./media/active-directory-reporting-migration/199.png "[エンタープライズ アプリケーション]、[サインイン]、レポート")

