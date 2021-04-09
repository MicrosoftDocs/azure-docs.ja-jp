---
title: クイック スタート:Power BI ワークスペースを Synapse ワークスペースにリンクする
description: このガイドの手順に従って、Power BI ワークスペースを Azure Synapse Analytics ワークスペースにリンクします。
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: business-intelligence
ms.date: 10/27/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 9c63e5e24495f373f288d2789780a6c671a7cc24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98218401"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>クイック スタート:Power BI ワークスペースを Synapse ワークスペースにリンクする

このクイックスタートでは、Power BI ワークスペースを Azure Synapse Analytics ワークスペースに接続して、Synapse Studio から新しい Power BI レポートとデータセットを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- [Azure Synapse ワークスペースと、関連するストレージ アカウントを作成します](quickstart-create-workspace.md)
- [Power BI Professional または Premium ワークスペース](/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Power BI ワークスペースを Synapse ワークスペースにリンクする

1. Synapse Studio から、 **[管理]** をクリックします。

    ![Synapse Studio で [管理] をクリックします。](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. **[外部接続]** で、 **[リンクされたサービス]** をクリックします。

    ![リンクされたサービスが強調表示されています。](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. **[+ 新規]** をクリックします。

    ![リンクされたサービスの [+ 新規] が強調表示されています。](media/quickstart-link-powerbi/new-highlighted.png)

4. **[Power BI]** をクリックし、 **[続行]** をクリックします。

    ![Power BI のリンクされたサービスが表示されています。](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. リンクされたサービスの名前を入力し、ドロップダウン リストからワークスペースを選択します。

    ![Power BI のリンクされたサービスの設定が表示されています。](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. **Create** をクリックしてください。

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Synapse Studio で Power BI ワークスペースを表示する

ワークスペースがリンクされると、Synapse Studio から Power BI データセットを参照でき、新しい Power BI レポートを作成して編集することもできます。

1. **[開発]** をクリックします。

    ![Synapse Studio で [開発] をクリックします。](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. [Power BI] を展開し、使用するワークスペースを展開します。

    ![Power BI とワークスペースを展開します。](media/quickstart-link-powerbi/develop-expand-powerbi.png)

新しいレポートを作成するには、 **[開発]** タブの上部にある **+** をクリックします。既存のレポートを編集するには、レポート名をクリックします。 保存した変更は、Power BI ワークスペースに書き戻されます。

![Power BI レポートを表示および編集します。](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>次のステップ

[Azure Storage に格納されているファイルについて Power BI レポートを作成する](sql/tutorial-connect-power-bi-desktop.md)の詳細を確認します。