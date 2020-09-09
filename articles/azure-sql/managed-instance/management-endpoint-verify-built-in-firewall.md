---
title: ビルトイン ファイアウォールのポート セキュリティを確認する
description: Azure SQL Managed Instance のビルトイン ファイアウォールの保護機能を検証する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708707"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Azure SQL Managed Instance のビルトイン ファイアウォールを検証する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

SQL Managed Instance を保護するネットワーク セキュリティ グループ (NSG) では、**すべての発信元**から管理ポート 9000、9003、1438、1440、および 1452 が開放されることが、Azure SQL Managed Instance の[必須のインバウンド セキュリティ規則](connectivity-architecture-overview.md#mandatory-inbound-security-rules)によって義務付けられています。 これらのポートは NSG レベルでは開放されますが、ネットワーク レベルではビルトインのファイアウォールによって保護されます。

## <a name="verify-firewall"></a>ファイアウォールを検証する

これらのポートを検証するには、任意のセキュリティ スキャナー ツールを使用して、それらのポートをテストします。 次のスクリーンショットは、そうしたツールの 1 つについて、その使い方を示したものです。

![ビルトインのファイアウォールを検証する](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>次のステップ

SQL Managed Instances と接続の詳細については、「[Azure SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)」を参照してください。
