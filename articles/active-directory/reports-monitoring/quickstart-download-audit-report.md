---
title: 'クイック スタート: Azure portal を使用して監査レポートをダウンロードする | Microsoft Docs'
description: Azure portal を使用して監査レポートをダウンロードする方法を説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68989690"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>クイック スタート: Azure portal を使用して監査レポートをダウンロードする

このクイック スタートでは、過去 24 時間のテナントに関する監査ログの CSV ファイルをダウンロードする方法について説明します。 Azure portal から最大 250,000 個のレコードをダウンロードできます。 レコードは最新の順に並べ替えられており、既定では最新の 250,000 個のレコードが取得されます。 

## <a name="prerequisites"></a>前提条件

必要なもの:

* Azure Active Directory テナント。 
* テナントの **セキュリティ管理者**、**セキュリティ閲覧者**、**グローバル管理者** のいずれかのロールであるユーザー。 さらに、テナントのすべてのユーザーは自分の監査ログにアクセスできます。

## <a name="quickstart-download-an-audit-report"></a>クイック スタート: 監査レポートをダウンロードする

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 左側のナビゲーション ウィンドウで **[Azure Active Directory]** を選択し、**[ディレクトリの切り替え]** ボタンを使用して目的の Active Directory を選択します。
3. ダッシュボードから **[Azure Active Directory]** を選択し、**[監査ログ]** を選択します。 
4. **[日付の範囲]** フィルター ドロップダウンで **[過去 24 時間]** を選択し、**[適用]** を選択して過去 24 時間の監査ログを表示します。 
5. **[ダウンロード]** ボタンを選択して、ファイル形式として **[CSV]** を選択し、ファイル名を指定して、フィルター処理したレコードを含む CSV ファイルをダウンロードします。 

![レポーティング](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory ポータルのサインイン アクティビティ レポート](concept-sign-ins.md)
* [Azure Active Directory レポートの保持期間](reference-reports-data-retention.md)
* [Azure Active Directory レポートの待機時間](reference-reports-latencies.md)
