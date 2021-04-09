---
title: SCIM、Microsoft Graph、Azure AD を使用してユーザーをプロビジョニングし、アプリをデータで強化する
description: SCIM と Microsoft Graph を一緒に使用してユーザーをプロビジョニングし、必要なデータでアプリケーションを強化します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 0a5d84585f28f6d13cbceb1fec41d6cdabf6d08c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255629"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SCIM と Microsoft Graph を一緒に使用してユーザーをプロビジョニングし、必要なデータでアプリケーションを強化する

**対象読者:** この記事は、Azure Active Directory (Azure AD) と統合されるアプリケーションを構築する開発者を対象としています。 Zoom、ServiceNow、DropBox など、Azure AD と既に統合されているアプリケーションを使用する場合は、この記事をスキップして、アプリケーション固有の[チュートリアル](../saas-apps/tutorial-list.md)を参照するか、[プロビジョニング サービスがどのように機能するか](./how-provisioning-works.md)を確認してください。

**一般的なシナリオ**

Azure AD には、プロビジョニングのためのすぐに使用できるサービスと、アプリケーションを構築するための拡張可能なプラットフォームが用意されています。 次のデシジョン ツリーは、開発者が [SCIM](https://aka.ms/scimoverview) と [Microsoft Graph](/graph/overview) を使用してプロビジョニングを自動化する方法を示しています。 

> [!div class="checklist"]
> * 自分のアプリケーションでユーザーを自動的に作成する
> * ユーザーがアクセスできないようにする必要があるときにユーザーを自分のアプリケーションから自動的に削除する
> * 自分のアプリケーションを複数の ID プロバイダーと統合してプロビジョニングする
> * Teams、Outlook、Office などの Microsoft サービスからのデータで自分のアプリケーションを強化する
> * Azure AD と Active Directory でユーザーとグループを自動的に作成、更新、および削除する

![SCIM Graph のデシジョン ツリー](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>シナリオ 1:自分のアプリでユーザーを自動的に作成する
現在、IT 管理者は、ユーザー アカウントを手動で作成するか、CSV ファイルをアプリケーションに定期的にアップロードすることによって、ユーザーをプロビジョニングしています。 このプロセスはお客様にとって時間がかかり、アプリケーションの導入が遅れる原因になります。 ユーザーを作成するために必要なものは、名前、メール、userPrincipalName などの基本的なユーザー情報だけです。 

**推奨事項**: 
* 顧客がさまざまな IdP を使用していて、それぞれを統合するための同期エンジンを維持することを望まない場合は、SCIM 準拠の [/Users](https://aka.ms/scimreferencecode) エンドポイントをサポートします。 顧客は、このエンドポイントを手軽に使用して Azure AD プロビジョニング サービスと統合し、アクセスが必要なときにユーザー アカウントを自動的に作成できるようになります。 エンドポイントを 1 回構築するだけで、すべての IdP との互換性が確保されます。 SCIM を使用したユーザーの作成方法については、以下の要求の例をご覧ください。
* Azure AD 内のユーザー オブジェクトにあるユーザー データと Microsoft 全体のその他のデータが必要な場合は、ユーザー プロビジョニングのための SCIM エンドポイントを構築し、Microsoft Graph を呼び出して残りのデータを取得することを検討してください。 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>シナリオ 2: 自分のアプリからユーザーを自動的に削除する
対象のアプリケーションを使用しているお客様は、セキュリティを重視して、従業員のアカウントが不要になったときにそれを削除するためのガバナンス要件を設定しています。 対象のアプリケーションからのプロビジョニング解除を自動化するにはどうすればよいですか。

**推奨事項:** SCIM 準拠の /Users エンドポイントをサポートします。 Azure AD プロビジョニング サービスは、ユーザーがそれ以上アクセスできないようにする必要があるときに無効化と削除の要求を送信します。 ユーザーの無効化と削除の両方をサポートすることをお勧めします。 無効化と削除の要求については、次の例を参照してください。 

ユーザーの無効化
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
ユーザーの削除
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>シナリオ 3: 自分のアプリのグループ メンバーシップの管理を自動化する
自分のアプリケーションでは、さまざまなリソースへのアクセスのためにグループが利用されています。お客様は、Azure AD で使用しているグループを再利用することを考えています。 Azure AD からグループをインポートし、メンバーシップの変更に合わせてそれを更新するにはどうすればよいですか。  

**推奨事項:** SCIM 準拠の /Groups [エンドポイント](https://aka.ms/scimreferencecode)をサポートします。 Azure AD プロビジョニング サービスによって、アプリケーションでのグループの作成と、メンバーシップの更新の管理が処理されます。 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>シナリオ 4: Teams、Outlook、OneDrive などの Microsoft サービスからのデータでアプリを強化する
自分のアプリケーションは Microsoft Teams に組み込まれており、メッセージ データを利用しています。 また、ユーザーのファイルは OneDrive に保存されます。 これらのサービスと Microsoft 全体のデータで自分のアプリケーションを強化するにはどうすればよいですか。

**推奨事項:** [Microsoft Graph](/graph/) が、Microsoft データにアクセスするためのエントリ ポイントになります。 各ワークロードにより、必要なデータを備えた API が公開されます。 上記のシナリオでは、Microsoft Graph を [SCIM プロビジョニング](./use-scim-to-provision-users-and-groups.md)と共に使用することができます。 SCIM を使用して基本的なユーザー属性を自分のアプリケーションにプロビジョニングしつつ、Graph を呼び出してその他の必要なデータを取得できます。 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>シナリオ 5: Teams、Outlook、Azure AD などの Microsoft サービスにおける変更を追跡する
Teams と Outlook のメッセージの変更を追跡し、リアルタイムでそれらに対応できるようにする必要があります。 これらの変更を自分のアプリケーションにプッシュするにはどうすればよいですか。

**推奨事項:** Microsoft Graph には、さまざまなリソースの [変更通知](/graph/webhooks)および [変更追跡機能](/graph/delta-query-overview)が用意されています。 変更通知に関する次の制限事項に注意してください。
- イベント レシーバーがイベントを確認した後、なんらかの理由でそのイベントを処理できない場合は、イベントが失われる可能性があります。
- 変更を受信する順序は、時系列であるとは限りません。
- 必ずしも変更通知に[リソース データ](/graph/webhooks-with-resource-data)が含まれるとは限りません。上記の理由により、開発者は、多くの場合、同期シナリオの変更追跡と共に変更通知を使用します。 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>シナリオ 6:Azure AD でユーザーとグループをプロビジョニングする
自分のアプリケーションでは、お客様が必要とするユーザーに関する情報を Azure AD に作成します。 これには、採用を管理する HR アプリケーションや、ユーザーの電話番号を作成する通信アプリ、Azure AD で有用になる可能性があるデータを生成するその他のアプリなどがあります。 Azure AD のユーザー レコードにこのデータを入力するにはどうすればよいですか。 

**推奨事項** Microsoft Graph では、/Users および /Groups エンドポイントを公開しています。これをすぐに統合して、Azure AD にユーザーをプロビジョニングすることができます。 Azure Active Directory ではこれらのユーザーを Active Directory に書き戻す処理がサポートされておりませんので、注意してください。 

> [!NOTE]
> Microsoft では、Workday や SuccessFactors などの HR アプリケーションからデータをプルするプロビジョニング サービスを用意しています。 これらの統合は、Microsoft によって構築および管理されます。 Microsoft のサービスへの新しい HR アプリケーションのオンボードについては、[UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) でリクエストすることができます。 

## <a name="related-articles"></a>関連記事

- [同期に関する Microsoft Graph のドキュメントを確認する](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [カスタム SCIM アプリと Azure AD の統合](use-scim-to-provision-users-and-groups.md)