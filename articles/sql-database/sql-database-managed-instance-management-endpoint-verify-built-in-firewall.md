---
title: Azure SQL Database Managed Instance のビルトイン ファイアウォールを理解する | Microsoft Docs
description: Azure SQL Database Managed Instance のビルトイン ファイアウォールの保護機能を検証する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 774455a2901782ef52b213c6a13c17636e28b1a4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788107"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Managed Instance のビルトイン ファイアウォールを検証する

Managed Instance を保護するネットワーク セキュリティ グループ (NSG) は、**すべての発信元**に対し、管理ポート 9000、9003、1438、1440、1452 を開放することが Managed Instance の[必須のインバウンド セキュリティ規則](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules)によって義務付けられています。 これらのポートは NSG レベルでは開放されますが、ネットワーク レベルではビルトインのファイアウォールによって保護されます。

## <a name="verify-firewall"></a>ファイアウォールを検証する

これらのポートを検証するには、任意のセキュリティ スキャナー ツールを使用して、それらのポートをテストします。 次のスクリーンショットは、そうしたツールの 1 つについて、その使い方を示したものです。

![ビルトインのファイアウォールを検証する](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>次の手順

Managed Instances と接続の詳細については、「[Azure SQL Database Managed Instance の接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)」を参照してください。