---
title: 管理単位の管理 (プレビュー) - Azure AD | Microsoft Docs
description: 管理単位を使用した Azure Active Directory での細かいレベルの権限の委任
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5854ef57a08218fee4479eb9e075824f4a1ff781
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376385"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory での管理単位の管理 (プレビュー)

この記事では、Azure Active Directory (Azure AD) での管理単位について説明します。 管理単位は 1 つのAzure AD リソースであり、他の Azure AD リソースのコンテナーにすることができます。 このプレビュー リリースでは、管理単位にはユーザーとグループのみを含めることができます。

管理単位を使用すると、定義した組織の部門、リージョン、またはその他のセグメントに制限されている管理者アクセス許可を付与できます。 管理単位を使用して、各地域の管理者に権限を委任したり、ポリシーを細かく設定したりできます。 たとえば、ユーザー アカウント管理者は、管理単位内のユーザーに対してのみ、プロファイル情報の更新、パスワードのリセット、およびライセンスの割り当てを実行できます。

たとえば、リージョン サポート スペシャリストに委任すると、[ヘルプデスク管理者](directory-assign-admin-roles.md#helpdesk-administrator)の役割は、そのスペシャリストがサポートするリージョン内のユーザーのみの管理に制限されます。

## <a name="deployment-scenario"></a>デプロイ シナリオ

管理単位を使用して管理スコープを制限することは、あらゆる種類の独立した部署で構成される組織に役立つ可能性があります。 多数の自律的なスクール (ビジネス スクール、エンジニアリング スクールなど) で構成され、各スクールにアクセスの制御、ユーザーの管理、およびスクールのポリシーの設定を行う IT 管理者のチームが存在する大規模な大学の例を検討します。 全体管理者は、以下を行うことができます。

- ビジネス スクール管理単位内の Azure AD ユーザーのみに対する管理アクセス許可を含むロールを作成する
- ビジネス スクールの管理単位を作成する
- ビジネス スクールの学生とスタッフのみを含む管理単位を設定する
- ビジネス スクールの IT チームを、そのスコープを持つロールに追加する

## <a name="license-requirements"></a>ライセンスの要件

管理単位を使用するには、各管理単位の管理者に Azure Active Directory Premium ライセンスが必要です。また、管理単位のメンバーに Azure Active Directory Free ライセンスが必要です。 詳細については、「 [Azure AD Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

## <a name="manage-administrative-units"></a>管理単位の管理

このプレビュー リリースでは、Azure portal、PowerShell コマンドレットおよびスクリプト、または Microsoft Graph を使用して管理単位を管理できます。 詳細については、Microsoft のドキュメントをご覧ください。

- [ロールを作成、削除、設定し、管理単位に追加する](roles-admin-units-manage.md):詳細な操作方法に関する手順
- [管理単位を操作する](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0):PowerShell を使用して管理単位を操作する方法
- [管理単位グラフのサポート](/graph/api/resources/administrativeunit?view=graph-rest-beta):管理単位の Microsoft Graph に関する詳細なドキュメント。

### <a name="planning-your-administrative-units"></a>管理単位の計画

管理単位を使用して、Azure AD リソースを論理的にグループ化することができます。 たとえば、IT 部門がグローバルに分散している組織では、地理的な境界を定義する管理単位を作成することが理にかなっている場合があります。 多国籍組織に、業務において半自律的な各種 "サブ組織" がある別のシナリオでは、各サブ組織を管理単位で表すことができます。

管理単位を作成する条件は、組織の固有の要件に従って設定されます。 管理単位は、Microsoft 365 サービスをまたがって構造体を定義するための一般的な方法です。 Microsoft 365 サービスをまたがる使用を考慮して管理単位を準備することをお勧めします。 管理単位の下にある Microsoft 365 間で共通のリソースを関連付けることができる場合は、管理単位から最大の価値を得ることができます。

次の段階に進むには、組織内の管理単位の作成が予想されます。

1. 初期導入:組織は、初期条件に基づいて管理単位の作成を開始し、条件の絞り込みに応じて管理単位の数が増加します。
1. 排除:条件を明確に定義すると、不要になった管理単位が削除されます。
1. 安定化:組織の構造は明確に定義されており、管理単位の数が短時間で大幅に変更されることはありません。

## <a name="currently-supported-scenarios"></a>現在サポートされているシナリオ

全体管理者または特権ロール管理者は、Azure AD ポータルを使用して管理単位を作成し、ユーザーを管理単位のメンバーとして追加して、IT スタッフを管理単位スコープの管理者ロールに割り当てることができます。 管理単位スコープの管理者は、Microsoft 365 管理センターを使用して、管理単位内のユーザーの基本的な管理を行うことができます。

さらに、グループを管理単位のメンバーとして追加できます。また、管理単位スコープのグループ管理者は、PowerShell、Microsoft Graph、Azure AD ポータルを使用してそれらを管理できます。

次の表では、管理単位シナリオの現在のサポートについて説明します。

### <a name="administrative-unit-management"></a>管理単位管理

アクセス許可 |   MS Graph/PowerShell   | Azure AD ポータル | Microsoft 365 管理センター
----------- | ----------------------- | --------------- | -----------------
管理単位の作成と削除   |    サポートされています    |   サポートされています   |    サポートされていません
管理単位のメンバーの個別の追加および削除    |   サポートされています    |   サポートされています   |    サポートされていません
.csv ファイルを使用した管理単位のメンバーの一括での追加および削除   |    サポートされていません     |  サポートされています   |    サポートの計画はありません
管理単位スコープの管理者の割り当て  |     サポートされています    |   サポートされています    |   サポートされていません
属性に基づく AU メンバーの動的な追加および削除 | サポートされていません | サポートされていません | サポートされていません

### <a name="user-management"></a>[ユーザー管理]

アクセス許可 |   MS Graph/PowerShell   | Azure AD ポータル | Microsoft 365 管理センター
----------- | ----------------------- | --------------- | -----------------
ユーザー プロパティ、パスワード、ライセンスの管理単位スコープ管理   |    サポートされています     |  サポートされています   |   サポートされています
ユーザー サインインの管理単位スコープのブロックとブロック解除    |   サポートされています   |    サポートされています   |    サポートされています
ユーザー MFA 資格情報の管理単位スコープの管理   |    サポートされています   |   サポートされています   |   サポートされていません

### <a name="group-management"></a>グループ管理

アクセス許可 |   MS Graph/PowerShell   | Azure AD ポータル | Microsoft 365 管理センター
----------- | ----------------------- | --------------- | -----------------
グループ プロパティおよびメンバーの管理単位スコープの管理     |  サポートされています   |    サポートされています    |  サポートされていません
グループ ライセンスの管理単位スコープの管理   |    サポートされています  |    サポートされています   |   サポートされていません

> [!NOTE]
>
> 管理単位スコープを持つ管理者は、動的なグループ メンバーシップ ルールを管理できません。

管理単位では、管理アクセス許可にのみスコープが適用されます。 メンバーまたは管理者が[既定のユーザー アクセス許可](../fundamentals/users-default-permissions.md)を使用して管理単位外の他のユーザー、グループ、またはリソースを参照するのを防ぐことはありません。 Microsoft 365 管理センターでは、スコープの管理者の管理単位外のユーザーはフィルターで除外されますが、Azure AD ポータル、PowerShell、およびその他の Microsoft サービス内で他のユーザーを参照することができます。

## <a name="next-steps"></a>次のステップ

- [AU の管理](roles-admin-units-manage.md)
- [AU でのユーザーの管理](roles-admin-units-add-manage-users.md)
- [AU でのグループの管理](roles-admin-units-add-manage-groups.md)
- [スコープ指定されたロールの AU への割り当て](roles-admin-units-assign-roles.md)
