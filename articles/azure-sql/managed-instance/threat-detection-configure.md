---
title: Advanced Threat Protection の構成
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection では、Azure SQL Managed Instance のデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティが検出されます。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686326"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance で Advanced Threat Protection を構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) の [Advanced Threat Protection](../database/threat-detection-overview.md) では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。 Advanced Threat Protection によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃**を特定できます。詳細については、[Advanced Threat Protectionアラート](../database/threat-detection-overview.md#alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event)または [Azure portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal) で受け取ることができます。

[Advanced Threat Protection](../database/threat-detection-overview.md) は、高度な SQL セキュリティ機能の統合パッケージである [Advanced Data Security](../database/advanced-data-security.md) オファリングの一部です。 Advanced Threat Protection は、SQL ADS ポータルを使用して一元的にアクセスおよび管理できます。

##  <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. 保護する SQL Managed Instance のインスタンスの構成ページに移動します。 **[設定]** ページで、 **[Advanced Data Security]** を選択します。
3. Advanced Data Security の構成ページで次のようにします
   - Advanced Data Security を **[オン]** にします。
   - 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る**電子メールのリスト**を構成します。
   - 異常な脅威監査レコードの保存先となる **Azure ストレージ アカウント**を選択します。
   - 構成する **[Advanced Threat Protection の種類]** を選択します。 詳しくは、[Advanced Threat Protection のアラート](../database/threat-detection-overview.md)に関する記事をご覧ください。
4. **[保存]** をクリックして、新しい、または更新された Advanced Data Security ポリシーを保存します。

   ![高度な脅威保護](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>次のステップ

- [Advanced Threat Protection](../database/threat-detection-overview.md) の詳細について学習します。
- マネージド インスタンスの詳細については、[Azure SQL Managed Instance の概要](sql-managed-instance-paas-overview.md)に関するページをご覧ください。
- 詳しくは、[Azure SQL Database の Advanced Threat Protection](../database/threat-detection-configure.md) に関するページをご覧ください。
- 詳しくは、[SQL Managed Instance の監査](https://go.microsoft.com/fwlink/?linkid=869430)に関するページをご覧ください。
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について学習します。
