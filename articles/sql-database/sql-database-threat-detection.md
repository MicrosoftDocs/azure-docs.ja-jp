---
title: "脅威の検出 - Azure SQL Database | Microsoft Docs"
description: "脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: eadaa3e7a279b6b92da1d0c026c3002297dfd298
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-threat-detection"></a>SQL Database の脅威の検出

脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。  脅威の検出は現在プレビュー段階です。

## <a name="overview"></a>概要

脅威の検出で提供される新しいセキュリティ階層は、異常なアクティビティに対するセキュリティ アラートを提供することによって、発生する可能性のある脅威をユーザーが検出して対応できるようにします。  ユーザーは、[SQL Database の監査](sql-database-auditing.md)を使って疑わしいイベントを調査し、データベース内のデータに対するアクセス、侵害、または悪用の試行による結果かどうかを判断できます。
脅威の検出を使用するとデータベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

たとえば、脅威の検出は、SQL インジェクションの可能性を示す特定の異常なデータベース アクティビティを検出します。 SQL インジェクションはインターネットでの一般的な Web アプリケーションのセキュリティの問題の&1; つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。

## <a name="next-steps"></a>次のステップ

* 脅威の検出の構成と管理については、「[Configure and manage Azure SQL Database threat detection in the Azure portal](sql-database-threat-detection-portal.md)」(Azure Portal で Azure SQL Database の脅威の検出を構成して管理する) をご覧ください。

