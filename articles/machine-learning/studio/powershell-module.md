---
title: Machine Learning Studio 用 PowerShell モジュール
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio のワークスペース、実験、Web サービスなどの作成と管理を PowerShell で行うことができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 01/25/2019
ms.openlocfilehash: bd3a82f326cdf7f51e8842e45333ff2bd647c260
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092754"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Azure Machine Learning Studio 用 PowerShell モジュール

PowerShell モジュールを使用すると、ワークスペース、データセット、Web サービスなどの Studio のリソースと資産をプログラムで管理できます。

次の 3 つの Powershell モジュールを使用して Studio リソースとやり取りすることができます。

* 2018 年にリリースされた [Azure PowerShell Az](#az-rm)。AzureRM のすべての機能が含まれていますが、コマンドレット名は異なります
* 2016 年にリリースされた [AzureRM](#az-rm)
* 2016 年にリリースされた [Azure Machine Learning PowerShell クラシック](#classic)

これらのモジュールにはいくつかの類似点もありますが、それぞれが特定のシナリオ向けに設計されています。 この記事では、PowerShell のモジュールの相違点について説明し、どれを選択すればよいかを把握しやすくします。

## <a name="choosing-modules"></a> モジュールの選択

利用可能な PowerShell モジュールの選択は、管理しているリソースの種類によって異なります。

以下の[サポート表](#support-table)で、各モジュールでサポートされるリソースを確認してください。 PowerShell クラシックは Az または AzureRM のいずれかと並行してインストールできるため、2 つのモジュールをインストールして、すべてのリソースの種類 (クラシックと Az またはクラシックと AzureRM) をカバーすることができます

ただし、Az と AzureRM が同時にインストールされていることは推奨されません。 Az と AzureRM の間で決定する場合、Microsoft では今後のすべてのデプロイ用に Az をお勧めします。 AzureRm は、環境でそれが必要となる特殊な状況がある場合にのみ使用してください。

Az と AzureRM の相違点の詳細については、[Azure PowerShell Az の概要](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)に関するページを参照してください。

## <a name="az-rm"></a> Azure PowerShell Az および AzureRM

Az と AzureRM は、両方とも、**Azure Resource Manager** デプロイ モデルを使ってソリューションを管理します。 これらのリソースには、Studio ワークスペースと Studio の新しい Web サービスが含まれます。 クラシック デプロイ モデルを使用してデプロイされたリソースを管理するには、PowerShell クラシック モジュールを使用する必要があります。 デプロイ モデルの詳細については、「[Azure Resource Manager とクラシック デプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)」を参照してください。

Az は、現在、Azure とやり取りすることを目的とした PowerShell モジュールであり、AzureRM の以前のすべての機能が含まれています。 AzureRM は引き続きバグ修正プログラムを受信しますが、新しいコマンドレットや機能は受信しなくなります。 AzureRM からのアップグレード パスはありますが、Studio を操作しているときに Az に関する問題が発生した場合は、問題を報告してから、AzureRM の使用に戻ってください。

Az の使用を開始するには、[Azure Az のインストール手順](https://docs.microsoft.com/powershell/azure/install-az-ps)に従ってください。

## <a name="classic"></a> PowerShell クラシック

Studio の [PowerShell クラシック モジュール](https://aka.ms/amlps)を使用すると、**クラシック デプロイ モデル**を使用してデプロイされたリソースを管理することができます。 これらのリソースには、Studio ユーザーの資産、従来の Web サービス、および従来の Web サービス エンドポイントが含まれます。

ただし、Microsoft では、リソースのデプロイと管理を簡素化するために、すべての新しいリソースに対して Resource Manager デプロイ モデルを使用することをお勧めします。 デプロイ モデルの詳細については、「[Azure Resource Manager とクラシック デプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)」を参照してください。

PowerShell クラシックを初めて使用する方は、GitHub から[リリース パッケージ](https://github.com/hning86/azuremlps/releases)をダウンロードし、[インストール手順](https://github.com/hning86/azuremlps/blob/master/README.md)に従ってください。 その手順に、ダウンロード/解凍した DLL のブロックを解除してからご使用の PowerShell 環境にインポートする方法が説明されています。

## <a name="support-table"></a> PowerShell サポート表

 **Studio ワークスペース** | **Az** |  **AzureRM** | **PowerShell クラシック** |
| --- | --- | --- | --- |
| ワークスペースを作成/削除する | [Resource Manager テンプレート](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Resource Manager テンプレート](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| ワークスペース ユーザーを管理する |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| コミットメント プランを管理する | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Web サービス** | **Az** | **AzureRM** | **PowerShell クラシック** |
| Web サービスを管理する | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (新しい Web サービス) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (新しい Web サービス) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (クラシック Web サービス) |
| エンドポイント/キーを管理する |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (新しい Web サービス) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (新しい Web サービス) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (クラシック Web サービス) |
|||
| **ユーザー資産** | **Az** | **AzureRM** | **PowerShell クラシック** |
| データセット/トレーニング済みモデルを管理する |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 実験を管理する |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| カスタム モジュールを管理する |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>次の手順
PowerShell モジュールの完全なドキュメントは、以下のリンクにあります。
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell クラシック](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
