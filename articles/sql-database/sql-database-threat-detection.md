---
title: Advanced Threat Protection - Azure SQL Database | Microsoft Docs
description: Advanced Threat Protection では、単一データベースまたはエラスティック プール内のデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティが検出されます。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 755a3b391cb7b4909169b034cc8d89892ec2ed05
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816553"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>単一データベースまたはプールされたデータベースに対する Azure SQL Database の Advanced Threat Protection

単一データベースおよびプールされたデータベースに対する [Advanced Threat Protection](sql-database-threat-detection-overview.md) では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。 Advanced Threat Protection によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃**を特定できます。詳細については、[Advanced Threat Protectionアラート](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)または [Azure portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) で受け取ることができます。

[Advanced Threat Protection](sql-database-threat-detection-overview.md) は、高度な SQL セキュリティ機能の統合パッケージである [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) オファリングの一部です。 Advanced Threat Protection は、SQL ADS ポータルを使用して一元的にアクセスおよび管理できます。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure portal で Advanced Threat Protection を設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. 保護する Azure SQL Database サーバーの構成ページに移動します。 セキュリティの設定で、 **[Advanced Data Security]** を選びます。
3. **[Advanced Data Security]** の構成ページで、

   - サーバーで Advanced Data Security を有効にします
   - **[Advanced Threat Protection 設定]** の **[アラートの送信先]** テキスト ボックスで、異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取るメール アドレスの一覧を指定します。
  
   ![Advanced Threat Protection の設定](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > スクリーンショットの価格は、常に現在の価格を反映しているとは限りません。

## <a name="set-up-advanced-threat-protection-using-powershell"></a>PowerShell を使用して Advanced Threat Protection を設定する

スクリプトの例については、[PowerShell を使用した監査と Advanced Threat Protection の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [Advanced Threat Protection](sql-database-threat-detection-overview.md) についてさらに詳しく学習します。
- [マネージド インスタンスでの Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) の詳細について学習します。  
- [Advanced Data Security](sql-database-advanced-data-security.md) の詳細について学習します。
- [監査](sql-database-auditing.md)の詳細について学習します。
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について学習します。
- 価格の詳細については、[SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。  
