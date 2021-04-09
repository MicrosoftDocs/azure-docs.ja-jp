---
title: Azure 管理アクセス用の Azure ロールを設定する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 管理の管理タスクを制御および委任するための、Azure portal での Azure ロールベースのアクセス制御 (Azure RBAC)。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519501"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Azure Cognitive Search への管理アクセス用の Azure ロールを設定する

Azure では、ポータルまたはリソース マネージャー API で管理するすべてのサービスに対して、 [グローバルなロールベースの承認モデル](../role-based-access-control/role-assignments-portal.md) を用意しています。 所有者、共同作成者、および閲覧者のロールによって、各ロールに割り当てられる Active Directory のユーザー、グループ、およびセキュリティ プリンシパルに対する *サービス管理* のレベルが決まります。 

> [!Note]
> サービス上のコンテンツをセキュリティで保護するための Azure ロールベースのアクセス制御 (Azure RBAC) はありません。 サービス自体に対する認証済み要求には、管理 API キーまたはクエリ API キーを使用します。 検索結果に対する ID ベースのアクセスの場合は、セキュリティ フィルターを作成して ID 別に結果に手を入れ、要求者がアクセスしてはいけないドキュメントを削除します。 詳細については、[セキュリティ フィルター](search-security-trimming-for-azure-search.md)に関する記事を参照してください。

## <a name="management-tasks-by-role"></a>ロール別の管理タスク

Azure Cognitive Search の場合、ロールは次の管理タスクをサポートするアクセス許可レベルに関連付けられています。

| Role | タスク |
| --- | --- |
| 所有者 |API キー、インデックス、インデクサー、インデクサー データ ソース、インデクサー スケジュールなど、サービス上のサービスやオブジェクトを作成または削除します。<p>カウントとストレージ サイズなど、サービスの状態を表示します。<p>ロール メンバーシップを追加または削除します (所有者のみがロール メンバーシップを管理できます)。<p>サブスクリプション管理者とサービス所有者には、所有者ロールに自動メンバーシップがあります。 |
| Contributor | Azure ロール管理を除き、アクセス レベルは所有者と同じです。 たとえば、共同作成者はオブジェクトの作成や削除、[API キー](search-security-api-keys.md)の表示や再生成は可能ですが、ロール メンバーシップは変更できません。<br><br>[Search Service Contributor](../role-based-access-control/built-in-roles.md#search-service-contributor) は、汎用の共同作成者組み込みロールと同等のものです。 |
| Reader |サービス エンドポイント、サブスクリプション、リソース グループ、リージョン、レベル、容量など、サービスで重要なものを表示します。 また、[監視] タブでは、1 秒あたりの平均クエリ数などのサービス メトリックを表示することもできます。このロールのメンバーは、インデックス、インデクサー、データ ソース、またはスキルセット情報を表示することができません。 これには、サービス上に存在するインデックスの数など、これらのオブジェクトの使用状況データも含まれます。 |

ロールは、サービス エンドポイントへのアクセス権を付与しません。 インデックスの管理、インデックスの作成、検索データのクエリなど、検索サービスの操作は、ロールではなく API キーによって制御されます。 詳細については、「[API キーを管理する](search-security-api-keys.md)」をご覧ください。

## <a name="permissions-table"></a>アクセス許可の表

次の表では、Azure Cognitive Search で許可される操作のほか、特定の操作へのアクセスを有効にするキーについてまとめています。

Azure RBAC のアクセス許可は、ポータルの操作とサービス管理 (サービスまたはその API キーの作成、削除、または変更) に適用されます。 API キーは、サービスが存在するようになった後に作成され、サービス上でのコンテンツ操作に適用されます。 また、オブジェクトの作成や削除など、ポータルでのコンテンツ関連の操作については、Azure RBAC の所有者または共同作成者が、暗黙の管理 API キーを使用して、サービスとやりとりします。

| 操作 | 何によって制御されるか |
|-----------|-------------------------|
| サービスの作成 | Azure RBAC アクセス許可:所有者または共同作成者 |
| サービスのスケーリング | Azure RBAC アクセス許可:所有者または共同作成者|
| サービスの削除 | Azure RBAC アクセス許可:所有者または共同作成者 |
| 管理キーまたはクエリ キーの管理 | Azure RBAC アクセス許可:所有者または共同作成者|
| ポータルまたは管理 API でのサービス情報の表示 | Azure RBAC アクセス許可:所有者、共同作成者、または閲覧者  |
| ポータルまたは管理 API でのオブジェクト情報およびメトリックの表示 | Azure RBAC アクセス許可:所有者または共同作成者 |
| サービス上のオブジェクトの作成、変更、削除: <br>インデックスとコンポーネント部分 (アナライザーの定義、スコアリング プロファイル、CORS オプションなど)、インデクサー、データ ソース、シノニム、サジェスター | API を使用する場合は管理キー、ポータルを使用する場合は Azure RBAC 所有者または共同作成者 |
| インデックスのクエリ | API を使用する場合は管理キーまたはクエリ キー、ポータルを使用する場合は Azure RBAC 所有者または共同作成者 |
| オブジェクトに関するクエリ システム情報 (オブジェクトの統計、カウント、一覧を返す操作など) | API を使用する場合は管理キー、ポータルを使用する場合は Azure RBAC 所有者または共同作成者 |

## <a name="next-steps"></a>次の手順

+ [PowerShell を使用した管理](search-manage-powershell.md) 
+ [Azure Cognitive Search のパフォーマンスと最適化](search-performance-optimization.md)
+ [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)。
