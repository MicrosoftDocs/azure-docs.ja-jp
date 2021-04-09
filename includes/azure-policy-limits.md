---
title: インクルード ファイル
description: インクルード ファイル
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91605841"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 定義について、_Scope_ というエントリは [管理グループ](../articles/governance/management-groups/overview.md)またはサブスクリプションを意味します。
割り当てと除外の場合、_スコープ_ というエントリは [管理グループ](../articles/governance/management-groups/overview.md)、サブスクリプション、リソース グループ、または個々のリソースを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope | ポリシーの定義 | 500 |
| Scope | イニシアチブ定義 | 200 |
| Tenant | イニシアチブ定義 | 2,500 |
| Scope | ポリシーとイニシアティブの割り当て | 200 |
| スコープ | 適用除外 | 1000 |
| ポリシー定義 | パラメーター | 20 |
| イニシアチブ定義 | ポリシー | 1000 |
| イニシアチブ定義 | パラメーター | 100 |
| ポリシーとイニシアティブの割り当て | 除外 (notScopes) | 400 |
| ポリシー規則 | 入れ子になった条件 | 512 |
| 修復タスク | リソース | 500 |
