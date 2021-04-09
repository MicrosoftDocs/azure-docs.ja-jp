---
title: ITSM 製品から LA ワークスペースに同期されるデータ
description: この記事では、ITSM 製品から LA ワークスペースに同期されるデータの概要について説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 83994c1b6e150342a777a079d79d6a594d30c3ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041605"
---
# <a name="data-synced-from-your-itsm-product"></a>お使いの ITSM 製品から同期されるデータ

接続の構成 ([データの同期] フィールドを使用) に基づいて、インシデントと変更要求が ITSM ツールから Log Analytics ワークスペースに同期されます。
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>同期されるデータ

このセクションでは、ITSMC によって収集されるデータの例の一部を示します。

**ServiceDesk_CL** 内のフィールドは、Log Analytics にインポートする作業項目の種類によって異なります。 以下に、2 つの作業項目の種類について、フィールドの一覧を示します。

**作業項目:** **インシデント**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- サービス デスク接続名
- サービス デスク ID
- State
- 緊急度
- 影響
- Priority
- エスカレーション
- 作成者
- 解決者
- 終了者
- source
- 割当先
- カテゴリ
- タイトル
- 説明
- 作成日
- 終了日
- 解決日
- 最終更新日
- Computer

**作業項目:** **変更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- サービス デスク接続名
- サービス デスク ID
- 作成者
- 終了者
- source
- 割当先
- タイトル
- Type
- カテゴリ
- State
- エスカレーション
- 競合状態
- 緊急度
- Priority
- リスク
- 影響
- 割当先
- 作成日
- 終了日
- 最終更新日
- 要求日
- 開始予定日
- 終了予定日
- 作業開始日
- 作業終了日
- 説明
- Computer

## <a name="servicenow-example"></a>ServiceNow の例 
### <a name="output-data-for-a-servicenow-incident"></a>ServiceNow インシデントの出力データ

| Log Analytics のフィールド | ServiceNow のフィールド |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |緊急度 |
| Impact_s |影響|
| Priority_s | Priority |
| CreatedBy_s | 開始者 |
| ResolvedBy_s | 解決者|
| ClosedBy_s  | 終了者 |
| Source_s| 連絡先の種類 |
| AssignedTo_s | 割り当て先  |
| Category_s | カテゴリ |
| Title_s|  簡単な説明 |
| Description_s|  Notes |
| CreatedDate_t|  開始済み |
| ClosedDate_t| closed|
| ResolvedDate_t|解決済み|
| Computer  | 構成項目 |

### <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 変更要求の出力データ

| Log Analytics | ServiceNow のフィールド |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 終了者 |
| AssignedTo_s | 割り当て先  |
| Title_s|  簡単な説明 |
| Type_s|  Type |
| Category_s|  カテゴリ |
| CRState_s|  State|
| Urgency_s|  緊急度 |
| Priority_s| Priority|
| Risk_s| リスク|
| Impact_s| 影響|
| RequestedDate_t  | Requested by date\(要求日\) |
| ClosedDate_t | 終了日 |
| PlannedStartDate_t  | 開始予定日 |
| PlannedEndDate_t  | 終了予定日 |
| WorkStartDate_t  | 実際の開始日 |
| WorkEndDate_t | 実際の終了日|
| Description_s | 説明 |
| Computer  | 構成項目 |

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)
