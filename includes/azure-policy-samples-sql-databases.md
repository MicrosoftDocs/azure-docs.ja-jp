---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: dcthegeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7bf885f2ab81e5d86878d2b0b53f4e98b8aedd9a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181337"
---
### <a name="sql-databases"></a>SQL データベース

|  |  |
|---------|---------|
| [許可された SQL DB の SKU](../articles/governance/policy/samples/allowed-sql-db-skus.md) | 承認された SKU が SQL データベースで使用される必要があります。 許可された SKU ID の配列または許可された SKU 名の配列を指定します。 |
| [DB レベルの脅威検出設定の監査](../articles/governance/policy/samples/audit-db-threat-detection-setting.md) | SQL データベース セキュリティ アラート ポリシーが特定の状態に設定されていない場合、このポリシーの監査を行います。 脅威検出を有効または無効のどちらにするかを指示する値を指定します。  |
| [SQL Database の暗号化の監査](../articles/governance/policy/samples/sql-database-encryption-audit.md) | SQL Database で Transparent Data Encryption が有効になっていないかどうかを監査します。 |
| [SQL DB レベルの監査設定の監査](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | SQL データベースの監査設定が指定された設定と一致しない場合に、この設定の監査を行います。 監査設定を有効または無効のどちらにするべきかを指示する値を指定します。  |