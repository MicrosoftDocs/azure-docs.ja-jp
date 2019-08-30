---
title: レポートを表示し、Azure AD エンタイトルメント管理 (プレビュー) - Azure Active Directory にログインします。
description: ユーザーの割り当てレポートを表示する方法について説明し、Azure Active Directory のエンタイトルメント管理 (プレビュー) で監査ログに記録します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2420fc25795ec74939649fb8a17ead7c8cfdd1df
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032448"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>レポートを表示し、Azure AD エンタイトルメント管理 (プレビュー) にログインします。

> [!IMPORTANT]
> Azure Active Directory (Azure AD) のエンタイトルメント管理は現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ユーザーの割り当てレポートと Azure Active Directory の監査ログでは、ご利用のディレクトリ内のユーザーに関する追加情報が提供されます。 管理者は、ユーザーがアクセス権を持っているリソースを表示し、監査目的で要求ログを表示したり、ユーザーの要求の状態を確認したりすることができます。 この記事では、ユーザーの割り当てレポートと Azure AD の監査ログを使用する方法について説明します。

次のビデオを視聴して、Azure Active Directory でエンタイトルメント管理を使用して、ユーザーのアクセス権を管理する方法について学習します。

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-resources-a-user-has-access-to"></a>ユーザーがアクセスするリソースの表示

1. **[Azure Active Directory]** をクリックしてから、 **[Identity Governance]** (IDのガバナンス) をクリックします。

1. 左側のメニューで **[User assignments report]** (ユーザーの割り当てレポート) をクリックします。

1. **[Select Users]** (ユーザーの選択) をクリックして、[Select Users] (ユーザーの選択) ペインを開きます。

1. アクセスできるリソースを表示するリストでユーザーを見つけます。

1. 該当のユーザーを選択し、 **[Select]** (選択) をクリックします。

    ユーザーがアクセスできるリソースの一覧が表示されます。 これには、アクセスのパッケージ、ポリシー、および日付が含まれます。

    ![ユーザー割り当てのレポート](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>ユーザーの要求のステータスを確認します。

アクセスするパッケージへのアクセスをどのようにユーザーが要求し受け取ったかの詳細については、Azure AD 監査ログを使用できます。 具体的には、`EntitlementManagement`と`UserManagement`のカテゴリ内のログ記録を使用して、各要求の処理手順における詳細情報を取得します。  

1. **[Azure Active Directory]** をクリックして、 **[Audit logs]** (ログの監査) をクリックします。

1. 上部で、探している監査レコードに応じて **[カテゴリ]** を`EntitlementManagement`または`UserManagement`に変更します。  

1. **[Apply]** をクリックします。

1. ログをダウンロードするには、 **[ダウンロード]** をクリックします。

Azure AD で新しい要求を受け取ると、監査レコードを書き込みます。このとき**カテゴリ**は`EntitlementManagement`であり**アクティビティ**は通常`User requests access package assignment`です。  Azure portal で直接割り当てを作成した場合、監査レコードの **[アクティビティ]** フィールドは`Administrator directly assigns user to access package`であり、割り当てを実行するユーザーが **ActorUserPrincipalName** で識別されます。

Azure AD は、要求の進行中は、以下を含め追加の監査レコードを書き込みます。

| Category | アクティビティ | 要求の状態 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 要求に承認は必要ありません。 |
| `UserManagement` | `Create request approval` | 要求には承認が必要 |
| `UserManagement` | `Add approver to request approval` | 要求には承認が必要 |
| `EntitlementManagement` | `Approve access package assignment request` | 要求が承認されました |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |要求が承認されました、または承認は必要ありません。 |

ユーザーにアクセスが割り当てられると、**アクティビティ**`Fulfill access package assignment`の`EntitlementManagement`カテゴリの監査レコードが書き込まれます。  アクセス権を受け取ったユーザーは **[ActorUserPrincipalName]** フィールドで識別されます。

アクセスが割り当てられていない場合、Azure AD では`EntitlementManagement`カテゴリの監査レコードを書き込みます。このとき**アクティビティ**は、要求が承認者により拒否されると`Deny access package assignment request`、承認者が承認する前に要求がタイムアウトすると`Access package assignment request timed out (no approver action taken)`になります。

ユーザーのアクセス パッケージの割り当てが期限切れになる、ユーザーがキャンセルする、または管理者が削除すると、`EntitlementManagement`カテゴリの監査レコードが書き込まれ、`Remove access package assignment`の**アクティビティ**になります。

## <a name="next-steps"></a>次の手順

- [Azure AD エンタイトルメント管理のトラブルシューティング](entitlement-management-troubleshoot.md)
- [一般的なシナリオ](entitlement-management-scenarios.md)
