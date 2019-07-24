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
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318245"
---
### <a name="sql-servers"></a>SQL Server

|  |  |
|---------|---------|
| [Azure Active Directory 管理者不在の監査](../articles/governance/policy/samples/audit-no-aad-admin.md) | Azure Active Directory 管理者が SQL Server に割り当てられていない場合に、監査を行います。 |
| [サーバー レベルの脅威検出設定の監査](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | SQL データベース セキュリティ アラート ポリシーが特定の状態に設定されていない場合、このポリシーの監査を行います。 脅威検出を有効または無効のどちらにするかを指示する値を指定します。  |
| [SQL Server の監査設定の監査](../articles/governance/policy/samples/sql-server-audit.md) | 監査設定が有効になっているかどうかに基づいて SQL Server を監査します。 |
| [SQL Server レベルの監査設定の監査](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | SQL Server の監査設定が指定された設定と一致しない場合に、この設定の監査を行います。 監査設定を有効または無効のどちらにするべきかを指示する値を指定します。 |
| [SQL Server バージョン 12.0 が必要](../articles/governance/policy/samples/require-sql-12.md) | SQL Server でバージョン 12.0 を使用する必要があります。  |