---
title: ML Studio (classic):PowerShell モジュール - Azure
description: Azure Machine Learning Studio (クラシック) のワークスペース、実験、Web サービスなどの作成と管理を PowerShell で行うことができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: a165a15825e09931cee871396c22cd6ab35af719
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431303"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (クラシック) 用 PowerShell モジュール

**適用対象:** ![はい](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![いいえ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)


PowerShell モジュールを使用すると、ワークスペース、データセット、Web サービスなどの Studio (クラシック) のリソースと資産をプログラムで管理できます。

次の 3 つの PowerShell モジュールを使用して Studio (クラシック) のリソースとやり取りすることができます。

* 2018 年にリリースされた [Azure PowerShell Az](#az-rm)。AzureRM のすべての機能が含まれていますが、コマンドレット名は異なります
* 2016 年にリリースされた [AzureRM](#az-rm)。PowerShell Az に置き換えられます
* 2016 年にリリースされた [Azure Machine Learning PowerShell クラシック](#classic)

これらの PowerShell モジュールにはいくつかの類似点もありますが、それぞれが特定のシナリオ向けに設計されています。 この記事では、PowerShell のモジュールの相違点について説明し、どれを選択すればよいかを把握しやすくします。  

以下の[サポート表](#support-table)で、各モジュールでサポートされるリソースを確認してください。 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az および AzureRM

Az は、現在、Azure とやり取りすることを目的とした PowerShell モジュールであり、AzureRM の以前のすべての機能が含まれています。 AzureRM は引き続きバグ修正プログラムを受信しますが、新しいコマンドレットや機能は受信しなくなります。  Az と AzureRM は、両方とも、**Azure Resource Manager** デプロイ モデルを使ってソリューションを管理します。 これらのリソースには、Studio (クラシック) ワークスペースと Studio (クラシック) の "新しい" Web サービスが含まれます。 

PowerShell クラシックを Az または AzureRM と共にインストールし、"新規" と "クラシック" 両方のリソースの種類をカバーすることができます。 ただし、Az と AzureRM が同時にインストールされていることは推奨されません。 Az と AzureRM の間で決定する場合、Microsoft では今後のすべてのデプロイ用に Az をお勧めします。  Az と AzureRM の比較および移行パスについて詳しくは、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)」をご覧ください。

Az の使用を開始するには、[Azure Az のインストール手順](https://docs.microsoft.com/powershell/azure/install-az-ps)に従ってください。

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell クラシック

Studio (クラシック) の [PowerShell クラシック モジュール](https://aka.ms/amlps)を使用すると、**クラシック デプロイ モデル**を使用してデプロイされたリソースを管理することができます。 これらのリソースには、Studio (クラシック) ユーザーの資産、"従来の" Web サービス、および "従来の" Web サービス エンドポイントが含まれます。

ただし、Microsoft では、リソースのデプロイと管理を簡素化するために、将来のすべてのリソースに対して Resource Manager デプロイ モデルを使用することをお勧めします。 デプロイ モデルの詳細については、「[Azure Resource Manager とクラシック デプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)」を参照してください。

PowerShell クラシックを初めて使用する方は、GitHub から[リリース パッケージ](https://github.com/hning86/azuremlps/releases)をダウンロードし、[インストール手順](https://github.com/hning86/azuremlps/blob/master/README.md)に従ってください。 その手順に、ダウンロード/解凍した DLL のブロックを解除してからご使用の PowerShell 環境にインポートする方法が説明されています。

PowerShell クラシックを Az または AzureRM と共にインストールし、"新規" と "クラシック" 両方のリソースの種類をカバーすることができます。

## <a name="powershell-support-table"></a><a name="support-table"></a> PowerShell サポート表


|  **Az** |  **PowerShell クラシック** |
| --- | --- | --- |
| ワークスペースを作成/削除する | [Resource Manager テンプレート](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| ワークスペース コミットメント プランを管理する | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| ワークスペース ユーザーを管理する |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Web サービスを管理する | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("新しい" Web サービス)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("クラシック" Web サービス) |
| Web サービスのエンドポイント/キーを管理する |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| ユーザーのデータセット/トレーニング済みモデルを管理する| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| ユーザーの実験を管理する |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| カスタム モジュールを管理する | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>次のステップ
これらの PowerShell モジュールの完全なドキュメントを参照してください。
* [PowerShell クラシック](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
