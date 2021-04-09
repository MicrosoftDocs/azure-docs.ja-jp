---
title: Azure Sentinel で Azure Monitor ブックを使用してデータを視覚化する | Microsoft Docs
description: このチュートリアルでは、Azure Sentinel でブックを使用してデータを視覚化する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 3a07670e3348f74fb5c6eaec57f5e9da627e8c09
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586722"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>チュートリアル: データの視覚化と監視



Azure Sentinel に[データ ソースを接続](quickstart-onboard.md)した後、Azure Sentinel による Azure Monitor ブックの適用を使用して、データを視覚化および監視できます。これにより、多用途のカスタム ダッシュボードを作成できます。 ブックの表示は Azure Sentinel では異なりますが、[Azure Monitor ブックを使用して対話型レポートを作成する](../azure-monitor/visualize/workbooks-overview.md)方法を確認すると役立つ場合があります。 Azure Sentinel を使用すると、データ全体に対してカスタム ブックを作成できます。また、用意されている組み込みのブック テンプレートを使用してデータ ソースに接続すると、すぐにデータ全体の分析情報をすばやく得ることもできます。


このチュートリアルは、Azure Sentinel でデータを視覚化するのに役立ちます。
> [!div class="checklist"]
> * 組み込みのブックを使用する
> * 新しいブックを作成する

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースのリソース グループに対して、少なくともブックの閲覧者またはブックの共同作成者のアクセス許可が必要です。

> [!NOTE]
> Azure Sentinel で表示されるブックは、Azure Sentinel ワークスペースのリソース グループ内に保存され、作成されたワークスペースごとにタグ付けされます。

## <a name="use-built-in-workbooks"></a>組み込みのブックを使用する

1. **[ブック]** にアクセスし、 **[テンプレート]** を選択して、Azure Sentinel の組み込みのブックの完全な一覧を表示します。 どれが接続しているデータ型と関連しているかを確認する場合、Azure Sentinel に関連データが既にストリーミングされている場合には各ブックの **[Required data types]\(必要なデータ型\)** フィールドでそのデータ型の隣に緑色のチェックマークが表示されます。
  ![ブックに移動](./media/tutorial-monitor-data/access-workbooks.png)
1. データが設定されているテンプレートを確認するには、 **[テンプレートの表示]** をクリックします。
  
1. ブックを編集するには、 **[保存]** を選択して、テンプレートの JSON ファイルを保存する場所を選択します。 

   > [!NOTE]
   > これにより、関連するテンプレートに基づいて Azure リソースが作成され、データではなく、ブックの JSON ファイルが保存されます。


1. **[保存されたブックの表示]** を選択します。 次に、上部にある **[編集]** をクリックします。 ブックを編集し、必要に応じてカスタマイズできるようになりました。 ブックをカスタマイズする方法の詳細については、「[Azure Monitor ブックを使用した対話型レポートの作成](../azure-monitor/visualize/workbooks-overview.md)」を参照してください。
![ブックの表示](./media/tutorial-monitor-data/workbook-graph.png)
1. 変更を行ったら、ブックを保存できます。 

1. 次のようにして、ブックを複製することもできます。 **[編集]** 、 **[名前を付けて保存]** の順に選択します。このとき、同じサブスクリプションとリソース グループの下に別の名前で保存してください。 複製されたこれらのブックは、 **[マイ ブック]** タブに表示されます。


## <a name="create-new-workbook"></a>新しいブックを作成する

1. 新しいブックを最初から作成するには、 **[ブック]** 、 **[Add workbook]\(ブックの追加\)** の順に選択します。
  ![新しいブック画面を示すスクリーンショット。](./media/tutorial-monitor-data/create-workbook.png)

1. ブックを編集するには、 **[編集]** を選択し、必要に応じてテキスト、クエリ、およびパラメーターを追加します。 ブックをカスタマイズする方法の詳細については、「[Azure Monitor ブックを使用した対話型レポートの作成](../azure-monitor/visualize/workbooks-overview.md)」を参照してください。 

1. クエリを作成するときに、 **[データ ソース]** が **[ログ]** に設定され、 **[リソース タイプ]** が **[Log Analytics]** に設定されていることを確認してから、関連するワークスペースを選択します。 

1. ブックを作成したら、Azure Sentinel ワークスペースのサブスクリプションとリソース グループの下にブックを保存します。

1. 組織内の他のユーザーがブックを使用できるようにする場合は、 **[保存先]** で **[共有レポート]** を選択します。 このブックを自分だけが使用できるようにする場合は、 **[個人用レポート]** を選択します。

1. ワークスペース内のブックを切り替えるには、ブックの上部ウィンドウで、![ブックを開くための **[開く]** アイコン](./media/tutorial-monitor-data/switch.png)を選択できます。 右側に表示されるウィンドウで、ブックを切り替えます。

   ![ブックの切り替え](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>ブックを削除する方法

保存されたブック (保存されたテンプレートまたはカスタマイズされたブック) を削除するには、[ブック] ページで、削除する保存済みブックを選択し、 **[削除]** を選択します。 これにより、保存されたブックが削除されます。

> [!NOTE]
> これにより、ブック リソースだけでなく、テンプレートに対して行った変更も削除されます。 元のテンプレートは引き続き使用できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure ブックを使用して、Azure Sentinel でデータを視覚化する方法を説明しました。

脅威への対応を自動化する方法については、「[Azure Sentinel で脅威への自動対応を設定する](tutorial-respond-threats-playbook.md)」を参照してください。
