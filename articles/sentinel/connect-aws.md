---
title: Symantec AWS データを Azure Sentinel プレビューに接続する | Microsoft Docs
description: Symantec AWS データを Azure Sentinel に続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674114"
---
# <a name="connect-azure-sentinel-to-aws"></a>Azure Sentinel を AWS に接続する

AWS コネクタを使用してすべての AWS CloudTrail イベントを Azure Sentinel にストリーミングします。 この接続プロセスにより、Azure Sentinel へのアクセスが AWS リソース ログに委任され、AWS CloudTrail とAzure Sentinel の間の信頼関係が確立されます。 これを行うには、Azure Sentinel にアクセス許可を付与するロールを AWS で作成し、AWS ログにアクセスできるようにします。

## <a name="prerequisites"></a>前提条件

Azure Sentinel ワークスペースへの書き込みアクセス許可が必要です。

## <a name="connect-aws"></a>AWS の接続 
 
1.  アマゾン ウェブ サービス コンソールの **[Security, Identity & Compliance]\(セキュリティ、ID、およびコンプライアンス\)** で **[IAM]** をクリックします。

    ![AWS1](./media/connect-aws/aws-1.png)

2.  **[ロール]** を選択し、 **[ロールの作成]** をクリックします。

    ![AWS2](./media/connect-aws/aws-2.png)

3.  **[別の AWS アカウント]** を選択します。 **[アカウント ID]** フィールドに、**Microsoft アカウント ID** (**123412341234**) を入力します。このID は、Azure Sentinel ポータルの AWS コネクタ ページで確認できます。

    ![AWS3](./media/connect-aws/aws-3.png)

4.  必ず **[外部 ID が必要]** を選択し、外部 ID (ワークスペース ID) を入力します。この ID は、Azure Sentinel ポータルの AWS コネクタ ページで確認できます。

    ![AWS4](./media/connect-aws/aws-4.png)

5.  **[Attach permissions policy]\(アクセス許可ポリシーのアタッチ\)** で **[AWSCloudTrailReadOnlyAccess]** を選択します。

    ![AWS5](./media/connect-aws/aws-5.png)

6.  (省略可能) タグを入力します。

    ![AWS6](./media/connect-aws/aws-6.png)

7.  次に、**ロール名**を入力し、 **[ロールの作成]** をクリックします。

    ![AWS7](./media/connect-aws/aws-7.png)

8.  ロールの一覧で、作成したロールを選択します。

    ![AWS8](./media/connect-aws/aws-8.png)

9.  **ロール ARN** をコピーし、Azure Sentinel ポータルの **[Role to add]\(追加するロール\)** フィールドに貼り付けます。

    ![AWS9](./media/connect-aws/aws-9.png)

10. Log Analytics で AWS イベントに関連するスキーマを使用するには、**AWSCloudTrail** を検索します。



## <a name="next-steps"></a>次の手順
このドキュメントでは、AWS CloudTrail を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

