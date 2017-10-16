---
title: "脅威の検出 - Azure SQL Database | Microsoft Docs"
description: "脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。"
services: sql-database
documentationcenter: 
author: rmatchoro
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/19/2017
ms.author: ronmat; ronitr
ms.openlocfilehash: bd3de9ed0131edc683763b0fe7f4a2ae74533944
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sql-database-threat-detection"></a>SQL Database の脅威の検出

SQL の脅威の検出は、データベースにアクセスしたりデータベースを悪用したりしようとする、異常で有害な可能性がある不自然な動作を検出します。

## <a name="overview"></a>概要

SQL の脅威の検出で提供される新しいセキュリティ階層は、異常なアクティビティに対するセキュリティ アラートを提供することによって、潜在的な脅威をユーザーが検出し、それが発生したときに対応できるようにします。  不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセス パターンが見つかった場合に、ユーザーはアラートを受信します。 SQL の脅威の検出によるアラートは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法を提供します。 ユーザーは、[SQL Database の監査](sql-database-auditing.md)を使って疑わしいイベントを調査し、データベース内のデータに対するアクセス、侵害、または悪用の試行による結果かどうかを判断できます。 脅威の検出を使用するとデータベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの一般的なセキュリティ問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。

SQL の脅威の検出によるアラートは [Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/) と統合されています。保護対象の各 SQL Database サーバーは、Azure Security Center Standard レベルの価格 (15 ドル/ノード/月) で課金されます。保護対象の SQL Database サーバーは、それぞれ 1 つのノードとしてカウントされます。 60 日間は無料で試用版をご利用いただけます。 

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal でデータベースの脅威検出を設定する
1. [https://portal.azure.com](https://portal.azure.com) で Azure Portal を起動します。
2. 監視する SQL Database の構成ブレードに移動します。 [設定] ブレードで、**[監査と脅威の検出]** を選択します。 
    ![ナビゲーション ウィンドウ][1]
3. **[監査と脅威検出]** 構成ブレードで、監査を **[ON]** にすると、脅威の検出の設定が表示されます。
  
    ![ナビゲーション ウィンドウ][2]
4. 脅威の検出を **[ON]** にします。
5. 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る電子メールのリストを構成します。
6. **[監査と脅威検出]** ブレードの **[保存]** をクリックして、新規または更新済みの監査と脅威検出の設定を保存します。
       
    ![ナビゲーション ウィンドウ][3]

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell を使用して脅威検出を設定する

スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページを参照してください。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>疑わしいイベントが検出されたときの異常なデータベース アクティビティの調査
1. 異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 <br/>
   電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。<br/>
     
    ![ナビゲーション ウィンドウ][4]
2. 電子メール アラートには、SQL 監査ログへの直接リンクが含まれています。 このリンクをクリックすると、Azure Portal が起動され、疑わしいイベントの前後の時間の SQL 監査レコードが開かれます。 監査レコードをクリックして、疑わしいデータベース アクティビティについての詳細を表示すると、実行された SQL ステートメントを見つけ (だれがアクセスし、いつ何をしたか)、正当なイベントであるか悪意のあるイベントであるかを判断することが容易になります (たとえば、SQL インジェクションに対するアプリケーションの脆弱性が悪用された、だれかが機微なデータを侵害した、など)。<br/>
   ![ナビゲーション ウィンドウ][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Azure Portal でデータベースの脅威検出のアラートを調査する

SQL Database の脅威の検出では、アラートが [Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/) と統合されています。 Azure Portal のデータベース ブレード内のライブ SQL セキュリティ タイルでは、アクティブな脅威の状態が追跡されます。 

   ![ナビゲーション ウィンドウ][6]
   
1. SQL セキュリティ タイルをクリックすると、Azure Security Center のアラート ブレードが起動され、データベースに対して検出されたアクティブな SQL 脅威の概要が表示されます。 

  ![ナビゲーション ウィンドウ][7]

2. 特定のアラートをクリックすると、より詳細な情報と、この脅威を調査し、今後の脅威に対処するためのアクションが表示されます。

  ![ナビゲーション ウィンドウ][8]


## <a name="next-steps"></a>次のステップ

* 脅威の検出の詳細については、[Azure ブログ](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/)を参照してください 
* [Azure SQL Database 監査](sql-database-auditing.md)の詳細について参照してください
* [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) の詳細について参照してください
* 価格の詳細については、[SQL Database の料金のページ](https://azure.microsoft.com/en-us/pricing/details/sql-database/)を参照してください  
* PowerShell スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページを参照してください



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


