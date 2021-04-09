---
title: エンタイトルメント管理での一般的なシナリオ - Azure AD
description: Azure Active Directory エンタイトルメント管理での一般的なシナリオの場合に従う必要がある、大まかな手順について説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f45a1481661aa350815560d795ab7411f99545
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317840"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理での一般的なシナリオ

組織のエンタイトルメント管理を構成するには、いくつかの方法があります。 ただし、開始したばかりであるなら、管理者、カタログ所有者、アクセス パッケージ マネージャー、承認者、要求元の一般的なシナリオを理解することをお勧めします。

## <a name="delegate"></a>委任

### <a name="administrator-delegate-management-of-resources"></a>管理者: リソースの管理を委任する

1. [ビデオを視聴する: IT から部門マネージャーへの委任](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [カタログ作成者ロールにユーザーを委任する](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>カタログ作成者: リソースの管理を委任する

- [新しいカタログを作成する](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>カタログ所有者: リソースの管理を委任する

1. [カタログに共同所有者を追加する](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [カタログにリソースを追加する](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>カタログ所有者: アクセス パッケージの管理を委任する

1. [ビデオを視聴する: カタログ所有者からアクセス パッケージ マネージャーへの委任](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [アクセス パッケージ マネージャー ロールにユーザーを委任する](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>組織内のユーザーのアクセスを統制する

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>アクセス パッケージ マネージャー: リソースへのアクセス要求を組織内の従業員に許可する

1. [新しいアクセス パッケージを作成する](entitlement-management-access-package-create.md#start-new-access-package)
1. [グループ、チーム、アプリケーション、SharePoint サイトをアクセス パッケージに追加する](entitlement-management-access-package-create.md#resource-roles)
1. [ディレクトリ内のユーザーにアクセス要求を許可する要求ポリシーを追加する](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [有効期限の設定を指定する](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>申請者:リソースへのアクセスを要求する

1. [マイ アクセス ポータルへのサインイン](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. アクセス パッケージの検索
1. [アクセスの要求](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>承認者:リソースへの要求を承認する

1. [マイ アクセス ポータルで要求を開く](entitlement-management-request-approve.md#open-request)
1. [アクセス要求を承認または拒否する](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>申請者:既にアクセス権のあるリソースを表示する

1. [マイ アクセス ポータルへのサインイン](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. アクティブなアクセス パッケージの表示

## <a name="govern-access-for-users-outside-your-organization"></a>組織外のユーザーのアクセスを統制する

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>管理者: 外部パートナー組織とコラボレーションする

1. [外部ユーザーのアクセスのしくみを読む](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [外部ユーザーの設定を確認する](entitlement-management-external-users.md#settings-for-external-users)
1. [外部組織に接続を追加する](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>アクセス パッケージ マネージャー: 外部パートナー組織とコラボレーションする

1. [新しいアクセス パッケージを作成する](entitlement-management-access-package-create.md#start-new-access-package)
1. [グループ、チーム、アプリケーション、SharePoint サイトをアクセス パッケージに追加する](entitlement-management-access-package-resources.md#add-resource-roles)
1. [ディレクトリ外のユーザーにアクセス要求を許可する要求ポリシーを追加する](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [有効期限の設定を指定する](entitlement-management-access-package-create.md#lifecycle)
1. [アクセス パッケージを要求するためのリンクをコピーする](entitlement-management-access-package-settings.md)
1. 外部パートナーの連絡先パートナーにそのユーザーと共有するためのリンクを送信する

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>申請者:外部ユーザーとしてリソースへのアクセスを要求する

1. 連絡先から受信したアクセス パッケージ リンクを検索する
1. [マイ アクセス ポータルへのサインイン](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [アクセスの要求](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>承認者:リソースへの要求を承認する

1. [マイ アクセス ポータルで要求を開く](entitlement-management-request-approve.md#open-request)
1. [アクセス要求を承認または拒否する](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>申請者:既にアクセス権のあるリソースを表示する

1. [マイ アクセス ポータルへのサインイン](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. アクティブなアクセス パッケージの表示

## <a name="day-to-day-management"></a>日々の管理

### <a name="access-package-manager-update-the-resources-for-a-project"></a>アクセス パッケージ マネージャー: プロジェクトのリソースを更新する

1. [ビデオを視聴する: 日々の管理: 変化への対応](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. アクセス パッケージを開く
1. [グループ、チーム、アプリケーション、SharePoint サイトを追加または削除する](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>アクセス パッケージ マネージャー: プロジェクトの期間を更新する

1. [ビデオを視聴する: 日々の管理: 変化への対応](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. アクセス パッケージを開く
1. [ライフサイクルの設定を開く](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [有効期限の設定を更新する](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>アクセス パッケージ マネージャー: プロジェクトのアクセスの承認方法を更新する

1. [ビデオを視聴する: 日々の管理: 変化への対応](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [要求設定の既存ポリシーを開く](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [承認設定を更新する](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>アクセス パッケージ マネージャー: プロジェクトの人員を更新する

1. [ビデオを視聴する: 日々の管理: 変化への対応](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [アクセスが不要になったユーザーを削除する](entitlement-management-access-package-assignments.md)
1. [要求設定の既存ポリシーを開く](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [アクセスが必要なユーザーを追加する](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>アクセス パッケージ マネージャー: アクセス パッケージに特定のユーザーを直接割り当てる

1. [異なるライフサイクル設定がユーザーに必要になった場合、アクセス パッケージに新しいポリシーを追加する](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [アクセス パッケージに特定のユーザーを直接割り当てる](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>割り当てとレポート

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>管理者: アクセス パッケージに割り当てられているユーザーを表示する

1. アクセス パッケージを開く
1. [割り当てを表示する](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [レポートとログをアーカイブする](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>管理者: ユーザーに割り当てられているリソースを表示する

1. [ユーザーのアクセス パッケージを表示する](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [ユーザーのリソースの割り当てを表示する](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>プログラムによる管理

Microsoft Graph を使用して、アクセス パッケージ、カタログ、ポリシー、要求、および割り当てを管理することもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、[エンタイトルメント管理 API](/graph/tutorial-access-package-api?view=graph-rest-beta) を呼び出すことができます。

## <a name="next-steps"></a>次のステップ

- [委任とロール](entitlement-management-delegate.md)
- [要求プロセスとメール通知](entitlement-management-process.md)