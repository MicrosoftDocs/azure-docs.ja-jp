---
title: Azure 管理アクセス用の Azure ロールを設定する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 管理用の管理タスクを制御および委任するための、Azure portal でのロールベースのアクセス制御 (RBAC)。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: f0c8fe6b8df5efef0cf3948c8d628d20c79502ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928685"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Azure Cognitive Search への管理アクセス用の Azure ロールを設定する

Azure では、ポータルまたはリソース マネージャー API で管理するすべてのサービスに対して、 [グローバルなロールベースの承認モデル](../role-based-access-control/role-assignments-portal.md) を用意しています。 所有者、共同作成者、および閲覧者のロールによって、各ロールに割り当てられる Active Directory のユーザー、グループ、およびセキュリティ プリンシパルに対する*サービス管理*のレベルが決まります。 

> [!Note]
> インデックスの一部やドキュメントのサブセットをセキュリティで保護するためのロールベースのアクセス制御 (RBAC) はありません。 検索結果に対する ID ベースのアクセスの場合は、セキュリティ フィルターを作成して ID 別に結果に手を入れ、要求者がアクセスしてはいけないドキュメントを削除します。 詳細については、[セキュリティ フィルター](search-security-trimming-for-azure-search.md)と[Active Directory を使用してセキュリティ保護する](search-security-trimming-for-azure-search-with-aad.md)を参照してください。

## <a name="management-tasks-by-role"></a>ロール別の管理タスク

Azure Cognitive Search の場合、ロールは次の管理タスクをサポートするアクセス許可レベルに関連付けられています。

| Role | タスク |
| --- | --- |
| 所有者 |API キー、インデックス、インデクサー、インデクサー データ ソース、インデクサー スケジュールなど、サービス上のサービスやオブジェクトを作成または削除します。<p>カウントとストレージ サイズなど、サービスの状態を表示します。<p>ロール メンバーシップを追加または削除します (所有者のみがロール メンバーシップを管理できます)。<p>サブスクリプション管理者とサービス所有者には、所有者ロールに自動メンバーシップがあります。 |
| Contributor |Azure ロール管理を除き、アクセス レベルは所有者と同じです。 たとえば、共同作成者はオブジェクトの作成や削除、[API キー](search-security-api-keys.md)の表示や再生成は可能ですが、ロール メンバーシップは変更できません。 |
| [Search Service Contributor 組み込みロール](../role-based-access-control/built-in-roles.md#search-service-contributor) | 共同作成者ロールと同じです。 |
| Reader |サービスの要点とメトリックを表示します。 このロールのメンバーは、インデックス、インデクサー、データ ソース、または重要な情報を表示できません。  |

ロールは、サービス エンドポイントへのアクセス権を付与しません。 インデックスの管理、インデックスの作成、検索データのクエリなど、検索サービスの操作は、ロールではなく API キーによって制御されます。 詳細については、「[API キーを管理する](search-security-api-keys.md)」をご覧ください。

## <a name="permissions-table"></a>アクセス許可の表

次の表では、Azure Cognitive Search で許可される操作のほか、特定の操作へのアクセスを有効にするキーについてまとめています。

| 操作 | アクセス許可 |
|-----------|-------------------------|
| サービスの作成 | Azure サブスクリプション所有者 |
| サービスのスケーリング | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者  |
| サービスの削除 | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者 |
| サービス上のオブジェクトの作成、変更、削除: <br>インデックスとコンポーネント部分 (アナライザーの定義、スコアリング プロファイル、CORS オプションなど)、インデクサー、データ ソース、シノニム、サジェスター | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者 |
| インデックスのクエリ | 管理者キーまたはクエリ キー (RBAC は適用不可) |
| クエリ システム情報 (オブジェクトの統計、カウント、一覧を返す操作など) | 管理者キー、リソースに対する RBAC (所有者、共同作成者、閲覧者) |
| 管理者キーの管理 | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者 |
| クエリ キーの管理 |  管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者  |

## <a name="see-also"></a>関連項目

+ [PowerShell を使用した管理](search-manage-powershell.md) 
+ [Azure Cognitive Search のパフォーマンスと最適化](search-performance-optimization.md)
+ [Azure Portal でのロールベースの Access Control の基礎を確認する](../role-based-access-control/overview.md)