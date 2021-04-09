---
title: エンタイトルメント管理でレポートおよびログを表示する - Azure AD
description: Azure Active Directory エンタイトルメント管理でユーザー割り当てレポートと監査ログを表示する方法について説明します。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eb44c1efd683b6febe9a355ef72c80cc6f2e40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746628"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でレポートとログを表示する

Azure AD エンタイトルメント管理レポートと Azure AD 監査ログは、ユーザーがアクセス権を持っているリソースに関する追加情報を提供します。 管理者は、ユーザーのアクセス パッケージやリソースの割り当てを表示できるほか、監査目的で要求ログを表示したり、ユーザーの要求の状態を確認したりすることができます。 この記事では、エンタイトルメント管理レポートや Azure AD 監査ログを使用する方法について説明します。

次のビデオを視聴して、エンタイトルメント管理でユーザーがアクセス権を持っているリソースを表示する方法について学ぶことができます。

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>ユーザーのアクセス パッケージを表示する

このレポートを使用すると、ユーザーが要求できるすべてのアクセス パッケージと、現在そのユーザーに割り当てられているアクセス パッケージを一覧表示できます。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[レポート]** をクリックします。

1. **[ユーザーのパッケージへのアクセス]** をクリックします。

1. **[Select Users]** (ユーザーの選択) をクリックして、[Select Users] (ユーザーの選択) ペインを開きます。

1. 一覧でユーザーを探し、**[選択]** をクリックします。

    **[要求できます]** タブには、ユーザーがアクセスできるアクセス パッケージの一覧が表示されます。 この一覧は、アクセス パッケージで定義されている[要求ポリシー](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)によって決まります。 

    ![ユーザーのアクセス パッケージ](./media/entitlement-management-reports/access-packages-report.png)

1. アクセス パッケージに 1 つ以上のリソースのロールやポリシーがある場合、そのリソースのロールまたはポリシーのエントリーをクリックして選択内容の詳細を確認してください。

1. **[割り当て済み]** タブをクリックすると、そのユーザーに現在割り当てられているアクセス パッケージの一覧が表示されます。 アクセス パッケージがユーザーに割り当てられていると、そのユーザーがそのアクセス パッケージ内のすべてのリソースのロールにアクセスできることを意味します。

## <a name="view-resource-assignments-for-a-user"></a>ユーザーのリソースの割り当てを表示する

このレポートでは、エンタイトルメント管理でユーザーに現在割り当てられているリソースを一覧表示できます。 このレポートは、エンタイトルメント管理で管理されているリソースを対象としています。 ユーザーは、エンタイトルメント管理の外部にあるディレクトリ内の他のリソースにアクセスできる可能性があります。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[レポート]** をクリックします。

1. **[ユーザーのリソースの割り当て]** をクリックします。

1. **[Select Users]** (ユーザーの選択) をクリックして、[Select Users] (ユーザーの選択) ペインを開きます。

1. 一覧でユーザーを探し、**[選択]** をクリックします。

    ユーザーに現在割り当てられているリソースの一覧が表示されます。 また、この一覧には、アクセスの開始日と終了日と共に、リソースのロールの取得元のアクセス パッケージとポリシーも表示されます。
    
    ユーザーが 2 つ以上のパッケージ内の同じリソースにアクセスできる場合は、矢印をクリックして各パッケージやポリシーを確認できます。

    ![ユーザーのリソースの割り当て](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>ユーザーの要求のステータスを確認します。

アクセスするパッケージへのアクセスをどのようにユーザーが要求し受け取ったかの詳細については、Azure AD 監査ログを使用できます。 具体的には、`EntitlementManagement`と`UserManagement`のカテゴリ内のログ記録を使用して、各要求の処理手順における詳細情報を取得します。  

1. **[Azure Active Directory]** をクリックして、**[Audit logs]** (ログの監査) をクリックします。

1. 上部で、探している監査レコードに応じて[**カテゴリ**] を`EntitlementManagement`または`UserManagement`に変更します。  

1. **[適用]** をクリックします。

1. ログをダウンロードするには、**[ダウンロード]** をクリックします。

Azure AD で新しい要求を受け取ると、監査レコードを書き込みます。このとき **カテゴリ** は`EntitlementManagement`であり **アクティビティ** は通常`User requests access package assignment`です。  Azure portal で直接割り当てを作成した場合、監査レコードの [**アクティビティ**] フィールドは`Administrator directly assigns user to access package`であり、割り当てを実行するユーザーが **ActorUserPrincipalName** で識別されます。

Azure AD は、要求の進行中は、以下を含め追加の監査レコードを書き込みます。

| カテゴリ | アクティビティ | 要求の状態 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 要求に承認は必要ありません。 |
| `UserManagement` | `Create request approval` | 要求には承認が必要 |
| `UserManagement` | `Add approver to request approval` | 要求には承認が必要 |
| `EntitlementManagement` | `Approve access package assignment request` | 要求が承認されました |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |要求が承認されました、または承認は必要ありません。 |

ユーザーにアクセスが割り当てられると、**アクティビティ** `Fulfill access package assignment` の `EntitlementManagement` カテゴリの監査レコードが Azure AD によって書き込まれます。  アクセス権を受け取ったユーザーは [**ActorUserPrincipalName**] フィールドで識別されます。

アクセスが割り当てられていない場合、Azure AD では`EntitlementManagement`カテゴリの監査レコードを書き込みます。このとき **アクティビティ** は、要求が承認者により拒否されると`Deny access package assignment request`、承認者が承認する前に要求がタイムアウトすると`Access package assignment request timed out (no approver action taken)`になります。

ユーザーのアクセス パッケージの割り当てが期限切れになる、ユーザーがキャンセルする、または管理者が削除すると、`EntitlementManagement`カテゴリの監査レコードが書き込まれ、`Remove access package assignment`の **アクティビティ** になります。

## <a name="next-steps"></a>次のステップ

- [レポートとログをアーカイブする](entitlement-management-logs-and-reporting.md)
- [Azure AD エンタイトルメント管理のトラブルシューティング](entitlement-management-troubleshoot.md)
- [一般的なシナリオ](entitlement-management-scenarios.md)
