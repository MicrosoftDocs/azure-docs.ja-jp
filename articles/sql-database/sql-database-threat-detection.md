---
title: 脅威の検出 - Azure SQL Database | Microsoft Docs
description: 脅威検出では、単一データベースまたはエラスティック プール内のデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティを検出します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797211"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>単一またはプールされたデータベース用の Azure SQL Database の脅威検出

単一およびプールされたデータベース用の[脅威検出](sql-database-threat-detection-overview.md)では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティを検出します。 脅威検出によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃**を特定できます。詳細については、[脅威検出アラート](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)または [Azure portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) で受け取ることができます。

[脅威検出](sql-database-threat-detection-overview.md)は、高度な SQL セキュリティ機能の統合パッケージである [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) オファリングの一部です。 脅威検出は、SQL ADS ポータルを使って一元的にアクセスおよび管理できます。 Advanced Data Security パッケージの料金は、論理サーバーごとに毎月 15 ドルです (最初の 30 日間は無料です)。

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal でデータベースの脅威検出を設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. 保護する Azure SQL Database サーバーの構成ページに移動します。 セキュリティの設定で、**[Advanced Data Security]** を選びます。
3. **[Advanced Data Security]** の構成ページで、

   - サーバーで Advanced Data Security を有効にします。
   - **[脅威検出の設定]** の **[アラートの送信先]** テキスト ボックスで、異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取るメール アドレスの一覧を指定します。
  
   ![脅威検出の設定](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell を使用して脅威検出を設定する

スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [脅威の検出](sql-database-threat-detection-overview.md)の詳細について学習します。
- [Managed Instance の脅威検出](sql-database-managed-instance-threat-detection.md)の詳細について学習します。  
- [Advanced Data Security](sql-database-advanced-data-security.md) の詳細について学習します。
- [監査](sql-database-auditing.md)の詳細について学習します。
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について学習します。
- 価格の詳細については、[SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。  
