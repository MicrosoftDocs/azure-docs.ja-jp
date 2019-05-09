---
title: チュートリアル - Azure Active Directory のログをストレージ アカウントにアーカイブする (プレビュー) | Microsoft Docs
description: Azure Active Directory のログをストレージ アカウントにプッシュするよう Azure Diagnostics を設定する方法について説明します (プレビュー)
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d34b41e780466d9ab734b9dbd0e58016dba7b10
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434723"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account-preview"></a>チュートリアル:Azure AD のログを Azure ストレージ アカウントにアーカイブする (プレビュー)

このチュートリアルでは、Azure Active Directory (Azure AD) のログを Azure ストレージ アカウントにルートするよう Azure Monitor の診断設定をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件 

この機能を使用するには、次が必要です。

* Azure ストレージ アカウントが付属した Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure AD テナント。
* Azure AD テナントの "*グローバル管理者*" または "*セキュリティ管理者*" であるユーザー。

## <a name="archive-logs-to-an-azure-storage-account"></a>ログを Azure ストレージ アカウントにアーカイブする

1. [Azure Portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]** > **[アクティビティ]** > **[監査ログ]** の順に選択します。 

3. **[エクスポート設定]** を選択します。 

4. **[診断設定]** ウィンドウで、次のいずれかの操作を実行します。
   * 既存の設定を変更するには、**[設定の編集]** を選択します。
   * 新しい設定を追加するには、**[診断の設定の追加]** を選択します。  
     最大で 3 つの設定を作成できます。 

     ![設定のエクスポート](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. 設定にその目的を連想しやすいよう、わかりやすい名前を付けます ("*Azure ストレージ アカウントに送信*" など)。 

6. **[ストレージ アカウントへのアーカイブ]** チェック ボックスをオンにし、**[ストレージ アカウント]** を選択します。 

7. ログのルーティング先となる Azure サブスクリプションとストレージ アカウントを選択します。
 
8. **[OK]** をクリックして構成を終了します。

9. 次のいずれかまたは両方を実行します。
    * 監査ログをストレージ アカウントに送信するには、**[AuditLogs]** チェックボックスをオンにします。 
    * サインイン ログをストレージ アカウントに送信するには、**[SignInLogs]** チェックボックスをオンにします。

10. スライダーを使用してログ データのリテンション期間を設定します。 既定ではこの値は *0* になっています。つまり、ログはストレージ アカウントに無期限に保持されます。 別の値を設定すると、その選択した日数より古いイベントが自動的にクリーンアップされます。

11. **[保存]** を選択して設定を保存します。

    ![診断設定](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. 約 15 分後、ログが対象のストレージ アカウントにプッシュされていることを確認します。 [Azure portal](https://portal.azure.com) に移動し、**[ストレージ アカウント]** を選択して前に使用したストレージ アカウントを選択し、**[BLOB]** を選択します。 **[監査ログ]** については、**[insights-log-audit]** を選択します。 **[サインイン ログ]** については、**[insights-logs-signin]** を選択します。

    ![ストレージ アカウント](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>次の手順

* [Azure Monitor で監査ログのスキーマを解釈する](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
* [よく寄せられる質問と既知の問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
