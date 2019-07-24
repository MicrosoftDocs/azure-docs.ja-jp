---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554047"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 _Scope_ というエントリは、サブスクリプションまたは[管理グループ](../articles/governance/management-groups/overview.md)のいずれかを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope (スコープ) | ポリシーの定義 | 250 |
| Scope (スコープ) | イニシアチブ定義 | 100 |
| Tenant | イニシアチブ定義 | 1,000 |
| Scope (スコープ) | ポリシーとイニシアティブの割り当て | 100 |
| ポリシー定義 | parameters | 20 |
| イニシアチブ定義 | ポリシー | 100 |
| イニシアチブ定義 | parameters | 100 |
| ポリシーとイニシアティブの割り当て | 除外 (notScopes) | 250 |
| ポリシー規則 | 入れ子になった条件 | 512 |
