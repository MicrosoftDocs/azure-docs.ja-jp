---
title: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポートについて説明します。
services: active-directory
author: markusvi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7209f468f493e226fae22ccd260e8ceb2e570494
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60286675"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルでリスクのフラグ付きユーザーを修復する

Azure Active Directory (Azure AD) のセキュリティ レポートでは、環境内でユーザー アカウントが侵害されている確率を評価することができます。 リスクのフラグ付きユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Microsoft は、お客様の環境のセキュリティの保持に努めています。 この取り組みの一環として、Microsoft は、異常なアクティビティや既知の攻撃パターンと一致するアクティビティを継続的に監視しています。 

お客様のユーザーのアカウントに対する未承認のアクセスを示す可能性がある異常なアクティビティが検出された場合は、対処できるように通知します。 これは、Microsoft 固有のシステムが侵害されているという意味ではありません。

## <a name="access-the-users-flagged-for-risk-report"></a>リスクのフラグ付きユーザー レポートへのアクセス

リスクのフラグ付きユーザーは、Azure portal の[リスク レポートに示されたユーザー](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers)から確認できます。 Azure AD をお持ちでない場合は、[https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) の無料試用版にサインアップできます。 

リスクのフラグ付きユーザー レポートから、各ユーザーに対して次のアクションを実行できます。

- 一時パスワードを生成する
- ユーザーに次回のサインインで自分のパスワードを安全にリセットすることを要求する
- 修復アクションを行わずにユーザーのリスクを無視する。

詳細については、[リスクのフラグ付きユーザー セキュリティ レポート](concept-user-at-risk.md)に関するページを参照してください。

### <a name="azure-ad-subscription-for-office-365-customers"></a>Office 365 のお客様の Azure AD サブスクリプション

Office 365 の資格情報を使用して **Azure 管理センター**にアクセスすることもできます。 Azure AD へのアクセスをアクティブにすると、Azure AD ポータルにリダイレクトされます。 Basic サブスクリプション レベルでは、レポートで提供される詳細情報の量が限定されます。 追加のデータと分析を使用できるのは、Azure Premium サブスクライバーのみです。

Microsoft 365 管理センターで**リスクのフラグ付きユーザー**のレポートにアクセスするには:

1.  左側のナビゲーション メニューで、 **[管理センター]** を選択します。 
2.  **[Azure AD ]** を選びます。
3.  **Azure Active Directory 管理センター**にログインします。
4.  ページ上部に **[新しいポータルの確認]** というバナーが表示される場合は、そのリンクを選択します。
4.  左側のナビゲーション メニューで、 **[Azure Active Directory]** を選択します。 
5.  ナビゲーション ウィンドウの **[セキュリティ]** セクションから、 **[リスクのフラグ付きユーザー]** を選択します。

## <a name="remediation-actions"></a>修復アクション

影響を受けたアカウントの問題を解決し、環境を保護するために、以下の操作を実行してください。

1.  多要素認証とセルフサービスのパスワード リセットに関する[情報が正しいことを検証](https://aka.ms/MFAValid)します。 
2.  すべてのユーザーの[多要素認証を有効](https://aka.ms/MFAuth)にします。 
3.  この[修復スクリプト](https://aka.ms/remediate)を使用すると、影響を受けたすべてのアカウントに対して、以下の手順を自動的に実行します。 

    a. パスワードをリセットしてアカウントを保護し、アクティブなセッションを強制終了します。

    b. メールボックスの代理人を削除します。

    c. 外部ドメインへのメール転送ルールを無効にします。

    d. メールボックスのグローバル メール転送プロパティを削除します。

    e. ユーザーのアカウントで MFA を有効にします。

    f. アカウントのパスワードの複雑さを高く設定します。

    g. メールボックスの監査を有効にします。

    h. 管理者が確認するための監査ログを生成します。

4. Office 365 のテナントおよびその他の IT インフラストラクチャを調査します。たとえば、すべてのテナント設定、ユーザー アカウント、ユーザーごとの構成設定で、変更の可能性を確認します。 持続的な侵害手段の兆候や、侵入者が VPN 資格情報を取得したり他の組織リソースにアクセスしたりするための最初の足掛かりを利用した兆候を調べます。 

5.  調査の一環として、法執行機関などの官公庁に通知する必要があるかどうかを検討してください。

他にも、以下の作業が必要です。

- [異常なアクティビティへの対処に関するこのガイダンス](https://aka.ms/fixaccount)を読み、実装します。 
- テナントでのアクティビティの分析に役立てるために、[監査パイプラインを有効](https://aka.ms/improvesecurity)にします。 完了すると、監査ストアはアクティビティ ログの読み込みを開始します。 この時点で、[セキュリティ/コンプライアンス センターの検索と調査のリソース](https://aka.ms/sccsearch)を活用することもできます。 
- すべてのアカウントに対して、この[スクリプトを使用してメールボックスの監査を有効](https://aka.ms/mailboxaudit1)にします。 
- すべてのメールボックスの代理人のアクセス許可とメール転送ルールを確認します。 この [PowerShell スクリプト](https://aka.ms/delegateforwardrules)を使用して、この作業を実行することができます。 

## <a name="next-steps"></a>次の手順

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [リスクのフラグ付きユーザー](concept-user-at-risk.md)
