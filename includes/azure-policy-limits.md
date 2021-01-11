---
title: インクルード ファイル
description: インクルード ファイル
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: 9fcaca3f89217f649eb970ec70514a5735222782
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854060"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 _Scope_ というエントリは、サブスクリプションまたは[管理グループ](../articles/governance/management-groups/overview.md)のいずれかを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope | ポリシーの定義 | 500 |
| Scope | イニシアチブ定義 | 200 |
| Tenant | イニシアチブ定義 | 2,500 |
| Scope | ポリシーとイニシアティブの割り当て | 200 |
| ポリシー定義 | パラメーター | 20 |
| イニシアチブ定義 | ポリシー | 1000 |
| イニシアチブ定義 | パラメーター | 100 |
| ポリシーとイニシアティブの割り当て | 除外 (notScopes) | 400 |
| ポリシー規則 | 入れ子になった条件 | 512 |
| 修復タスク | リソース | 500 |
