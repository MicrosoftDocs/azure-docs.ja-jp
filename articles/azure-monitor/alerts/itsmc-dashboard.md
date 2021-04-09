---
title: ITSMC ダッシュボードを使用してエラーを調査する
description: IT Service Management Connector ダッシュボードを使用してエラーを調査する方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039531"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>ITSMC ダッシュボードを使用してエラーを調査する

この記事には、IT Service Management Connector (ITSMC) ダッシュボードに関する情報が含まれています。 このダッシュボードは、コネクタの状態を調査するのに役立ちます。

## <a name="view-errors"></a>エラーを表示する

ダッシュボードでエラーを表示するには:

1. **[すべてのリソース]** を選択し、**ServiceDesk( *<実際のワークスペース名>* )** を見つけます。

   ![Azure サービスのリソースを示すスクリーンショット。](media/itsmc-definition/create-new-connection-from-resource.png)

2. 左側のペインの **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** を選択します。

   ![[ワークプレースのデータ ソース] の下の [ITSM 接続] の選択を示すスクリーンショット。](media/itsmc-overview/add-new-itsm-connection.png)

3. **[IT Service Management Connector]** 領域の **[概要]** で **[概要の表示]** を選択します。

   ![[概要の表示] ボタンを示すスクリーンショット。](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. **[IT Service Management Connector]** 領域にグラフが表示されたら、それを選択します。

   ![グラフの選択を示すスクリーンショット。](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. ダッシュボードが表示されます。 これを使用して、コネクタの状態とエラーを確認します。
   
   ![ダッシュボード上のコネクタの状態を示すスクリーンショット。](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>ダッシュボードの要素を理解する

ダッシュボードには、このコネクタを介して ITSM ツールに送信されたアラートに関する情報が含まれています。 ダッシュボードは、4 つの部分に分かれています。

### <a name="created-work-items"></a>作成された作業項目 

**[作成された作業項目]** 領域のグラフとその下の表には、種類ごとの作業項目のカウントが含まれます。 グラフまたは表を選択すると、作業項目に関する詳細を表示できます。

![作成された作業項目を示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>影響を受けたコンピューター 

**[影響を受けたコンピューター]** 領域の表には、コンピューターとその関連作業項目が一覧表示されます。 表内の行を選択すると、コンピューターに関する詳細情報を取得できます。

表に含まれる行数は制限されています。 すべての行を表示する場合は、 **[すべてを表示]** を選択します。

![影響を受けたコンピューターを示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>コネクタの状態 

**[コネクタの状態]** 領域のグラフとその下の表には、コネクタの状態に関するメッセージが含まれます。 グラフまたは表内の行を選択すると、メッセージに関する詳細情報を取得できます。

表に含まれる行数は制限されています。 すべての行を表示する場合は、 **[すべてを表示]** を選択します。

表内のメッセージの詳細については、[こちらの記事](itsmc-dashboard-errors.md)を参照してください。

![コネクタの状態を示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>アラート ルール 

**[アラート ルール]** 領域の表には、検出されたアラート ルールの数に関する情報が含まれます。 表内の行を選択すると、検出されたルールに関する詳細情報を取得できます。
    
表に含まれる行数は制限されています。 すべての行を表示する場合は、 **[すべてを表示]** を選択します。

![アラート ルールを示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
