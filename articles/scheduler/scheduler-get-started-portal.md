---
title: "Azure Portal の Azure Scheduler の概要 | Microsoft Docs"
description: "Azure ポータルの Azure Scheduler の概要"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3861ee121ed1c4d086ea81640e84d924d7d17ea1


---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Azure ポータルの Azure Scheduler の概要
Azure Scheduler では、スケジュールされたジョブを簡単に作成できます。 このチュートリアルでは、ジョブの作成方法について説明します。 また、Scheduler の監視機能と管理機能についても説明します。

## <a name="create-a-job"></a>ジョブを作成する
1. [Azure ポータル](https://portal.azure.com/)にサインインします。  
2. **[+新規]** をクリックし、検索ボックスに「*Scheduler*」と入力し、結果の **[Scheduler]**、**[作成]** の順にクリックします。
   
    ![][marketplace-create]
3. ここでは、http://www.microsoft.com/ への GET 要求を送信するのみのジョブを作成します。 **[スケジューラ ジョブ]** 画面に次の情報を入力します。
   
   1. **[名前]:** `getmicrosoft`  
   2. **[サブスクリプション]:** Azure のサブスクリプション   
   3. **[ジョブ コレクション]:** 既存のジョブ コレクションを選択するか、**[新規作成]** をクリックして名前を入力します。
4. 次に、 **[アクションの設定]**で次の値を定義します。
   
   1. **[アクションの種類]:** ` HTTP`  
   2. **[メソッド]:** `GET`  
   3. **[URL]:** ` http://www.microsoft.com`  
      
      ![][action-settings]
5. 最後に、スケジュールを定義してみましょう。 ジョブは 1 回限りのジョブとして定義することができますが、ここでは定期的なスケジュールを選択しましょう。
   
   1. **[繰り返し]**: `Recurring`
   2. **[開始]**: 今日の日付
   3. **[繰り返しの間隔]**: `12 Hours`
   4. **[終了期限]**: 今日の日付から 2 日間  
      
      ![][recurrence-schedule]
6.  **[作成]**

## <a name="manage-and-monitor-jobs"></a>ジョブの管理と監視
ジョブが作成されると、メインの Azure ダッシュボードに表示されます。 ジョブをクリックすると、次のタブが表示された状態で新しいウィンドウが開きます。

1. プロパティ  
2. [アクションの設定]  
3. スケジュール  
4. 履歴
5. Users
   
   ![][job-overview]

### <a name="properties"></a>プロパティ
これらの読み取り専用のプロパティには、Scheduler ジョブの管理メタデータを記述します。

   ![][job-properties]

### <a name="action-settings"></a>[アクションの設定]
**[ジョブ]** 画面でジョブをクリックすると、そのジョブを構成できます。 簡易作成ウィザードで詳細な設定を構成していなかった場合、ここで構成できます。

すべてのアクションの種類で、再試行ポリシーとエラー アクションを変更することができます。

HTTP および HTTPS のジョブのアクションの種類では、方法を許可されている任意の HTTP 動詞に変更できます。 ヘッダーおよび基本的な認証情報を追加、削除、変更することもできます。

ストレージ キュー アクションの種類では、ストレージ アカウント、キュー名、SAS トークン、および本文を変更することができます。

サービス バス アクションの種類では、名前空間、トピックとキューのパス、認証設定、トランスポートの種類、メッセージのプロパティ、およびメッセージの本文を変更することができます。

   ![][job-action-settings]

### <a name="schedule"></a>スケジュール
簡易作成ウィザードで作成したスケジュールを変更する場合は、スケジュールを再構成することができます。

また、 [ジョブの複雑なスケジュールと高度な繰り返し](scheduler-advanced-complexity.md)

開始日時、定期実行のスケジュール、および終了日時 (定期実行ジョブの場合) を変更できます。

   ![][job-schedule]

### <a name="history"></a>履歴
**[履歴]** タブには、選択したジョブのシステム内の各ジョブ実行について、選択したメトリックが表示されます。 これらのメトリックで、Scheduler の正常性状態に関するリアルタイムの値がわかります。

1. 状態  
2. 詳細  
3. 再試行
4. 発生: 1 回目、2 回目、3 回目など
5. 実行の開始時刻  
6. 実行の終了時刻
   
   ![][job-history]

[実行] をクリックして、各実行の全体の応答など、 **[履歴の詳細]**を表示できます。 このダイアログ ボックスでは、応答をクリップボードにコピーすることもできます。

   ![][job-history-details]

### <a name="users"></a>Users
Azure のロールベースのアクセス制御 (RBAC) では、Azure Scheduler のアクセス権を詳細に管理できます。 [ユーザー] タブの使用方法については、 [Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)

## <a name="see-also"></a>関連項目
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)

 [Scheduler Concepts, Terminology, and Entity Hierarchy (Scheduler の概念、用語集、エンティティ階層構造)](scheduler-concepts-terms.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法](scheduler-advanced-complexity.md)

 [Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)](scheduler-powershell-reference.md)

 [Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Scheduler 送信認証](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png



<!--HONumber=Dec16_HO2-->


