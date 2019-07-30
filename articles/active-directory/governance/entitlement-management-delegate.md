---
title: Azure AD エンタイトルメント管理 (プレビュー) でのタスクの委任 - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理でタスクを委任するために割り当てることのできるロールについて説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798627"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) でのタスクの委任

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

既定では、グローバル管理者とユーザー管理者は、Azure AD エンタイトルメント管理のすべての側面を作成および管理できます。 ただし、これらのロールのユーザーが、アクセス パッケージが必要なすべてのシナリオを把握しているとは限りません。 通常、コラボレーションが必要な相手を把握しているのは、部署内のユーザーです。

管理者以外のユーザーに無制限のアクセス許可を付与する代わりに、それぞれの業務に必要な最小限のアクセス許可を付与することで、競合の発生や不適切なアクセス許可を回避することができます。 この記事では、エンタイトルメント管理のさまざまなタスクを委任するために割り当てることができるロールについて説明します。

## <a name="delegate-example-for-departmental-adoption"></a>部門別導入の委任の例

エンタイトルメント管理でタスクを委任する方法を理解するには、例を考えてみるとよいでしょう。 お客様の組織に次の 5 人のユーザーがいるとします。

| User | 部署 | メモ |
| --- | --- | --- |
| Alice | IT | 全体管理者 |
| Bob | 研究 | Bob は研究グループの所有者でもあります |
| Carole | 研究 |  |
| Dave | Marketing |  |
| Elisa | Marketing | Elisa はマーケティング アプリケーションの所有者でもあります |

研究とマーケティングのどちらの部署も、ユーザーにエンタイトルメント管理を使用したいと考えています。 Alice はまだ、他の部署でエンタイトルメント管理を使用する準備ができていません。 次の方法なら、Alice が研究とマーケティングの部署にタスクを委任できる可能性があります。

1. Alice がカタログ作成者用の新しい Azure AD セキュリティ グループを作成し、そのグループのメンバーとして Bob、Carol、Dave、Elisa を追加します。

1. Alice がエンタイトルメント管理の設定を使用して、そのグループをカタログ作成者ロールに追加します。

1. Carol が**研究**カタログを作成し、そのカタログの共同所有者として Bob を追加します。 Bob は、所有する研究グループをリソースとしてカタログに追加して、研究コラボレーション用のアクセス パッケージで使用できるようにします。

1. Dave が**マーケティング** カタログを作成し、そのカタログの共同所有者として Elisa を追加します。 Elisa は、所有するマーケティング アプリケーションをリソースとしてカタログに追加して、マーケティング コラボレーション用のアクセス パッケージで使用できるようにします。

これで、研究とマーケティングの部署でエンタイトルメント管理を利用できるようになりました。 Bob、Carol、Dave、Elisa は、それぞれのカタログにアクセス パッケージを作成して管理できます。

![エンタイトルメント管理の委任の例](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>エンタイトルメント管理のロール

エンタイトルメント管理には、エンタイトルメント管理に固有の次のロールがあります。

| Role | 説明 |
| --- | --- |
| カタログ作成者 | カタログを作成および管理します。 通常は、グローバル管理者ではない IT 管理者、またはリソース コレクションのリソース所有者です。 カタログを作成した人物が、自動的にカタログの最初のカタログ所有者になります。カタログ所有者はさらに追加することができます。 |
| カタログ所有者 | 既存のカタログを編集および管理します。 通常は、IT 管理者かリソース所有者、またはカタログ所有者が指定したユーザーです。 |
| アクセス パッケージ マネージャー | カタログ内のすべての既存アクセス パッケージを編集および管理します。 |

また、アクセス パッケージの指定された承認者と申請者も、ロールではありませんが権限を持ちます。
 
* 承認者:アクセス パッケージへの要求を承認または拒否することがポリシーによって許可されています。ただし、アクセス パッケージの定義を変更することはできません。
* 申請者:アクセス パッケージのポリシーによって、そのアクセス パッケージへの要求が許可されています。

次の表に、これらのロールで実行できるタスクの一覧を示します。

| タスク | カタログ作成者 | カタログ所有者 | アクセス パッケージ マネージャー | 承認者 |
| --- | :---: | :---: | :---: | :---: |
| [新しいカタログを作成する](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [カタログにリソースを追加する](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [カタログを編集する](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [カタログを削除する](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [カタログ所有者またはアクセス パッケージ マネージャーをカタログに追加する](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [新しいアクセス パッケージをカタログに作成する](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [アクセス パッケージ内のリソースのロールを管理する](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージを要求できるユーザーを指定する](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージにユーザーを直接割り当てる](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージに割り当てられているユーザーを表示する](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージの要求を表示する](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [要求の配信エラーを表示する](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [保留中の要求をキャンセルする](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージを非表示にする](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージを削除する](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス要求を承認する](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>カタログにリソースを追加するために必要なロール

グローバル管理者は、カタログ内の任意のグループ (クラウドが作成したセキュリティ グループまたはクラウドが作成した Office 365 グループ)、アプリケーション、または SharePoint Online サイトを追加または削除することができます。 ユーザー管理者は、カタログ内の任意のグループまたはアプリケーションを追加または削除することができます。

グローバル管理者でもユーザー管理者でもないユーザーがカタログにグループ、アプリケーション、または SharePoint Online サイトを追加する場合、そのユーザーは、必要な Azure AD ディレクトリ ロールとカタログ所有者のエンタイトルメント管理ロールの "*両方*" を持っている必要があります。 次の表は、カタログにリソースを追加するために必要なロールの組み合わせを示します。 カタログからリソースを削除するには、同じロールが必要です。

| Azure AD ディレクトリ ロール | エンタイトルメント管理ロール | セキュリティ グループの追加 | Office 365 グループの追加 | アプリの追加 | SharePoint Online サイトの追加 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md) | 該当なし |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [ユーザー管理者](../users-groups-roles/directory-assign-admin-roles.md) | 該当なし |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理者](../users-groups-roles/directory-assign-admin-roles.md) | カタログ所有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 管理者](../users-groups-roles/directory-assign-admin-roles.md) | カタログ所有者 |  | :heavy_check_mark: |  |  |
| [Teams サービス管理者](../users-groups-roles/directory-assign-admin-roles.md) | カタログ所有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理者](../users-groups-roles/directory-assign-admin-roles.md) | カタログ所有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md) | カタログ所有者 |  |  | :heavy_check_mark: |  |
| [クラウド アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md) | カタログ所有者 |  |  | :heavy_check_mark: |  |
| User | カタログ所有者 | グループ所有者の場合のみ | グループ所有者の場合のみ | アプリ所有者の場合のみ |  |

## <a name="add-a-catalog-creator"></a>カタログ作成者を追加する

カタログ作成を委任する場合は、ユーザーをカタログ作成者ロールに追加します。  個々のユーザーを追加することも、手間を省くためにグループを追加することもできます。これにより、メンバーがカタログを作成できるようになります。 カタログ作成者ロールにユーザーを割り当てるには、これらの手順に従います。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューの **[エンタイトルメント管理]** セクションで、 **[設定]** をクリックします。

1. **[編集]** をクリックします。

1. **[Delegate entitlement management]\(エンタイトルメント管理の委任\)** セクションで、 **[Add catalog creators]\(カタログ作成者の追加\)** をクリックし、このエンタイトルメント管理ロールのメンバーとなるユーザーまたはグループを選択します。

1. **[選択]** をクリックします。

1. **[Save]** をクリックします。

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>カタログ所有者またはアクセス パッケージ マネージャーを追加する

カタログまたはカタログ内のアクセス パッケージの管理を委任する場合は、ユーザーをカタログ所有者またはアクセス パッケージ マネージャーのロールに追加します。 どのユーザーでも、カタログを作成すると、最初のカタログ所有者になります。 カタログ所有者またはアクセス パッケージ マネージャーのロールにユーザーを割り当てるには、これらの手順に従います。

**事前に必要なロール:** ユーザー管理者またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、管理者を追加するカタログを開きます。

1. 左側のメニューで **[ロールと管理者]** をクリックします。

1. **[所有者の追加]** または **[Add access package managers]\(アクセス パッケージ マネージャーの追加\)** をクリックして、それらのロールのメンバーを選択します。

1. **[選択]** をクリックすると、これらのメンバーが追加されます。

## <a name="next-steps"></a>次の手順

- [承認者を追加する](entitlement-management-access-package-edit.md#policy-request)
- [カタログにリソースを追加する](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
