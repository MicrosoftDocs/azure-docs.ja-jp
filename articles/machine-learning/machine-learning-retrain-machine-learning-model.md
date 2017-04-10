---
title: "Machine Learning モデルの再トレーニング | Microsoft Docs"
description: "Azure Machine Learning でモデルの再トレーニングをして Web サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: 3fd731d83ca32359193534f043b3eb3e0333e0aa
ms.lasthandoff: 03/02/2017


---
# <a name="retrain-a-machine-learning-model"></a>Machine Learning モデルの再トレーニング
Azure Machine Learning における Machine Learning のモデル運用プロセスの一環として、モデルのトレーニングと保存が行われます。 その後、このモデルを使用して、予測 Web サービスを作成します。 これによって、Web サイト、ダッシュボード、モバイル アプリでこの Web サービスを使用できます。 

Machine Learning を使って作成するモデルは、通常、静的ではありません。 新しいデータが使用可能になるか、API のコンシューマーに独自のデータがある場合は、モデルを再トレーニングする必要があります。 

再トレーニングは頻繁に発生する可能性があります。 再トレーニング API を使用して、モデルをプログラムによって再トレーニングし、新しくトレーニングされたモデルで Web サービスを更新できます。 

このドキュメントでは再トレーニングのプロセスについて説明し、再トレーニング API を使用する方法を示します。

## <a name="why-retrain-defining-the-problem"></a>再トレーニングを行う理由: 問題の定義
Machine Learning のトレーニング プロセスの一環として、モデルのトレーニングではデータのセットを使用します。 Machine Learning を使って作成するモデルは、通常、静的ではありません。 新しいデータが使用可能になるか、API のコンシューマーに独自のデータがある場合は、モデルを再トレーニングする必要があります。

これらのシナリオでは、開発者または API のコンシューマーはプログラムによる API を通じて簡単にクライアントを作成でき、独自のデータを使用して&1; 回のみまたは定期的に、モデルの再トレーニングを行うことができます。 その後再トレーニングの結果を評価し、さらに Web サービス API を更新して、新しくトレーニングを行ったモデルを使用できます。

> [!NOTE]
> 既存のトレーニング実験と新しい Web サービスがある場合は、次のセクションで説明したチュートリアルの手順ではなく、既存の予測 Web サービスの再トレーニングを確認することができます。
> 
> 

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー
このプロセスには、Web サービスとして発行されたトレーニング実験と予測実験が必要です。 トレーニング済みのモデルを再トレーニングできるようにするには、トレーニング済みのモデルの出力で、トレーニング実験を Web サービスとして発行する必要があります。 これにより、モデルへの API アクセスが有効になり再トレーニングを行うことができます。 

次の手順は、新規および従来の Web サービスに適用されます。

初期の予測 Web サービスを作成します。

* トレーニング実験を作成する
* 予測 Web 実験を作成する
* 予測 Web サービスをデプロイする

Web サービスを再トレーニングします。

* トレーニング実験を更新して、再トレーニングを可能にする
* Web サービスの再トレーニングをデプロイする
* バッチ実行サービス コードを使用して、モデルを再トレーニングする

前述のチュートリアルの手順については、「[プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」を参照してください。

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](machine-learning-manage-new-webservice.md)」をご覧ください。 

従来の Web サービスをデプロイする場合。

* 予測 Web サービスに新しいエンドポイントを作成する
* URL とコードの更新プログラムを取得する
* URL の更新プログラムを使用して、再トレーニング済みのモデルで新しいエンドポイントをポイントする 

前述のチュートリアル手順については、「[従来の Web サービスの再トレーニング](machine-learning-retrain-a-classic-web-service.md)」を参照してください。

従来の Web サービスの再トレーニングで問題が発生した場合は、「[Azure Machine Learning の従来の Web サービスにおける再トレーニングに関するトラブルシューティング](machine-learning-troubleshooting-retraining-models.md)」を参照してください。

新規の Web サービスをデプロイする場合。

* Azure Resource Manager アカウントへのサインイン
* Web サービス定義を取得します。
* Web サービス定義を JSON としてエクスポートします。
* JSON で `ilearner` BLOB への参照を更新する
* JSON を Web サービス定義にインポートします。
* Web サービスを新しい Web サービス定義で更新します。

前述のチュートリアル手順については、「[Machine Learning 管理 PowerShell コマンドレットを使用した新規 Web サービスの再トレーニング](machine-learning-retrain-new-web-service-using-powershell.md)」を参照してください。

従来の Web サービスの再トレーニングを設定するプロセスには、次の手順が含まれます。

![再トレーニング プロセスの概要][1]

新しい Web サービスの再トレーニングを設定するプロセスには、次の手順が含まれます。

![再トレーニング プロセスの概要][7]

## <a name="other-resources"></a>その他のリソース
* [Azure Data Factory を使用した Azure Machine Learning モデルの再トレーニングと更新](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [PowerShell を使用して&1; つの実験から複数の Machine Learning モデルと Web サービス エンドポイントを作成する](machine-learning-create-models-and-endpoints-with-powershell.md)
* [API を使用した AML 再トレーニング モデル](https://www.youtube.com/watch?v=wwjglA8xllg) ビデオでは、再トレーニング API と PowerShell を使用して Azure Machine Learning で作成された Machine Learning モデルの再トレーニング方法が示されます。

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png


