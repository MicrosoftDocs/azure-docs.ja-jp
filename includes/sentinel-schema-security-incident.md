---
title: インクルード ファイル
description: インクルード ファイル
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761723"
---
### <a name="the-data-model-of-the-schema"></a>スキーマのデータ モデル

| フィールド | データ型 | 説明 |
| ---- | ---- | ---- |
| **AdditionalData** | 動的 | アラート数、ブックマーク数、コメント数、アラート製品の名前と戦術 |
| **AlertIds** | 動的 | インシデントが作成される原因となったアラート |
| **BookmarkIds** | 動的 | ブックマークが付けられたエンティティ |
| **分類** | string | インシデント終了の分類 |
| **ClassificationComment** | string | インシデント終了の分類のコメント |
| **ClassificationReason** | string | インシデント終了の分類の理由 |
| **ClosedTime** | DATETIME | インシデントが最後に閉じられたときのタイムスタンプ (UTC) |
| **コメント** | 動的 | インシデント コメント |
| **CreatedTime** | DATETIME | インシデントが作成されたときのタイムスタンプ (UTC) |
| **説明** | string | インシデントの説明 |
| **FirstActivityTime** | DATETIME | 最初のイベントの時刻 |
| **FirstModifiedTime** | DATETIME | インシデントが最初に変更されたときのタイムスタンプ (UTC) |
| **IncidentName** | string | 内部 GUID |
| **IncidentNumber** | INT |  |
| **IncidentUrl** | string | インシデントへのリンク |
| **ラベル** | 動的 | タグ |
| **LastActivityTime** | DATETIME | 前回のイベントの時刻 |
| **LastModifiedTime** | DATETIME | インシデントが最後に変更されたときのタイムスタンプ (UTC) <br>(現在のレコードによって記述された変更) |
| **ModifiedBy** | string | インシデントを変更したユーザーまたはシステム |
| **所有者** | 動的 |  |
| **RelatedAnalyticRuleIds** | 動的 | インシデントのアラートがトリガーされる原因となったルール |
| **重大度** | string | インシデントの重要度 (高/中/低/情報提供) |
| **SourceSystem** | string | 定数 ('Azure') |
| **状態** | string |  |
| **TenantId** | string |  |
| **TimeGenerated** | DATETIME | 現在のレコードが作成されたときのタイムスタンプ (UTC) <br>(インシデントの変更時) |
| **タイトル** | string | 
| **Type** | string | 定数 ('SecurityIncident') |
|
