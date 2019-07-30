---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181792"
---
実験はコンピューティング先 (ワークスペースにアタッチされているコンピューティング リソース) で実行されます。  コンピューティング先を作成すると、将来の実行でそれを再利用できます。

1. 下部の **[Run]\(実行\)** を選択して実験を実行します。

     ![実験を実行する](./media/aml-ui-create-training-compute/run-experiment.png)

1. ワークスペースに既にコンピューティング リソースがある場合は、 **[Setup Compute Targets]\(コンピューティング先の設定\)** ダイアログが表示されたら、それを選択できます。  そうでない場合は、 **[Create new]\(新規作成\)** を選択します。

    > [!NOTE]
    > ビジュアル インターフェイスでは、Machine Learning コンピューティング先でのみ実験を実行できます。 その他のコンピューティング先は表示されません。

1. コンピューティング リソースの名前を指定します。

1. **[実行]** を選択します。

    ![コンピューティング先を設定する](./media/aml-ui-create-training-compute/set-compute.png)

    これで、コンピューティング リソースが作成されます。 実験の右上隅に状態が表示されます。 

    > [!NOTE]
    > コンピューティング リソースの作成には、約 5 分かかります。 リソースの作成後は、それを再利用できるため、今後の実行では、この待機時間をスキップできます。
    >
    > コンピューティング リソースは、コストを節約するために、アイドル状態のときには 0 ノードに自動スケーリングされます。  しばらくしてから再度使用すると、スケールアップして元に戻すために、再び約 5 分の待ち時間が発生することがあります。
