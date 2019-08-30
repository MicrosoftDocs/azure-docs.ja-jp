---
title: Advanced Threat Protection を構成する - Azure SQL Database マネージド インスタンス | Microsoft Docs
description: Advanced Threat Protection では、マネージド インスタンスのデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティが検出されます。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: eb6d8229e85e54a6a3fc8591dc4688a73773fd8b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816596"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Azure SQL Database マネージド インスタンスで Advanced Threat Protection を構成する

[マネージド インスタンス](sql-database-managed-instance-index.yml)の [Advanced Threat Protection](sql-database-threat-detection-overview.md) では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。 Advanced Threat Protection によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃**を特定できます。詳細については、[Advanced Threat Protectionアラート](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)または [Azure portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) で受け取ることができます。

[Advanced Threat Protection](sql-database-threat-detection-overview.md) は、高度な SQL セキュリティ機能の統合パッケージである [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) オファリングの一部です。 Advanced Threat Protection は、SQL ADS ポータルを使用して一元的にアクセスおよび管理できます。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure portal で Advanced Threat Protection を設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. 保護するマネージド インスタンスの構成ページに移動します。 **[設定]** ページで、 **[Advanced Data Security]** を選択します。
3. Advanced Data Security の構成ページで次のようにします
   - Advanced Data Security を **[オン]** にします。
   - 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る**電子メールのリスト**を構成します。
   - 異常な脅威監査レコードの保存先となる **Azure ストレージ アカウント**を選択します。
   - 構成する **[Advanced Threat Protection の種類]** を選択します。 詳しくは、[Advanced Threat Protection のアラート](sql-database-threat-detection-overview.md)に関する記事をご覧ください。
4. **[保存]** をクリックして、新しい、または更新された Advanced Data Security ポリシーを保存します。

   ![高度な脅威保護](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > スクリーンショットの価格は、常に現在の価格を反映しているとは限りません。

## <a name="next-steps"></a>次の手順

- [Advanced Threat Protection](sql-database-threat-detection-overview.md) の詳細について学習します。
- マ[Managed Instance の概要](sql-database-managed-instance.md)に関する記事でマネージド インスタンスの詳細について学習します。
- [単一データベースの Advanced Threat Protection](sql-database-threat-detection.md) の詳細について学習します。
- [Managed Instance の監査](https://go.microsoft.com/fwlink/?linkid=869430)の詳細について学習します。
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について学習します。
