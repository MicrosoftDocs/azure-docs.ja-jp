---
title: "Machine Learning 用 PowerShell モジュール | Microsoft Docs"
description: "Azure Machine Learning 用 PowerShell モジュールは、パブリック プレビューとしてご利用いただけます。 ワークスペース、実験、Web サービスなどの作成と管理を PowerShell で行うことができます。"
keywords: "実験, 線形回帰, 機械学習アルゴリズム, 機械学習チュートリアル, 予測モデリング手法, データ サイエンス実験"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: garye;haining
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: ee3255493760917b2a96facfabe17120764cb638
ms.lasthandoff: 03/02/2017


---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Microsoft Azure Machine Learning 用 PowerShell モジュール
Azure Machine Learning 用 PowerShell モジュールは、ワークスペースや実験、データセット、従来の Web サービスなどを Windows PowerShell で管理することができる強力なツールです。

関連するドキュメントを閲覧したり、完全なソース コード付きでモジュールをダウンロードしたりするには、 [https://aka.ms/amlps](https://aka.ms/amlps)にアクセスしてください。 

> [!NOTE]
> Azure Machine Learning の PowerShell モジュールは、現在プレビュー モードです。 モジュールは、このプレビュー期間中に引き続き改善され、拡張される予定です。 「[Cortana Intelligence and Machine Learning Blog (Cortana Intelligence と Machine Learning のブログ)](https://blogs.technet.microsoft.com/machinelearning/)」で最新情報をチェックしてください。

## <a name="what-is-the-machine-learning-powershell-module"></a>Machine Learning PowerShell モジュールとは
Machine Learning PowerShell モジュールは、Azure Machine Learning のワークスペースや実験、データセット、従来の Web サービス、従来の Web サービス エンドポイントを Windows PowerShell からすべて管理できる .NET ベースの DLL モジュールです。 

モジュールと共に、整然と分離された [C# API レイヤー](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)を含んだ完全なソース コードをダウンロードできます。 この DLL を独自の .NET プロジェクトから参照すれば、.NET コードから Azure Machine Learning を管理することができます。 また、この DLL はその土台となる REST API に依存しており、好きなクライアントから直接これらの REST API を利用することができます。

## <a name="what-can-i-do-with-the-powershell-module"></a>PowerShell モジュールでできること
以下、この PowerShell モジュールで実行できるタスクの例を挙げます。 ここに挙げた以外にも、さまざまな機能があります。[こちらの詳しいドキュメント](https://aka.ms/amlps)を参照してください。

* 管理証明書を使用して新しいワークスペースをプロビジョニングする ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* 実験グラフを表す JSON ファイルをエクスポート/インポートする ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) および [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* 実験を実行する ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* 予測実験から Web サービスを作成する ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* 発行済みの Web サービスにエンドポイントを作成する ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* RRS/BES Web サービス エンドポイントを呼び出す ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) および [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

PowerShell を使用して既存の実験を実行する簡単な例を次に示します。

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

さらに詳しい使用例については、記事「[PowerShell を使用して&1; つの実験から複数の Machine Learning モデルと Web サービス エンドポイントを作成する](machine-learning-create-models-and-endpoints-with-powershell.md)」を参照してください。PowerShell モジュールを使用して、一般的に要求されるタスクを自動化する例が紹介されています。

## <a name="how-do-i-get-started"></a>開始するには?
Machine Learning PowerShell を初めて使用する方は、GitHub から[リリース パッケージ](https://github.com/hning86/azuremlps/releases)をダウンロードし、[インストール手順](https://github.com/hning86/azuremlps/blob/master/README.md)に従ってください。 その手順に、ダウンロード/解凍した DLL のブロックを解除してからご使用の PowerShell 環境にインポートする方法が説明されています。 ほとんどのコマンドレットで、ワークスペース ID とワークスペース承認トークン、そのワークスペースが存在する Azure リージョンの指定が必要となります。 これらの値を提供する最も簡単な方法は、既定の config.json ファイルを使用することです。 このファイルの構成方法も、手順に説明があります。 

必要に応じて、git ツリーの複製、コードの変更、および Visual Studio を使用したローカルなコンパイルを実行できます。

## <a name="next-steps"></a>次のステップ
PowerShell モジュールの完全なドキュメントは[https://aka.ms/amlps](https://aka.ms/amlps) にあります。 

現実世界のシナリオでモジュールを使用する広範な実例については、詳細なユース ケースである「[PowerShell を使用して&1; つの実験から複数の Machine Learning モデルと Web サービス エンドポイントを作成する](machine-learning-create-models-and-endpoints-with-powershell.md)」を参照してください。

