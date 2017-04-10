---
title: "PowerShell を使用した Azure Government への接続 | Microsoft Docs"
description: "PowerShell を使用して Azure Government にサブスクリプションを接続する方法"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>PowerShell を使用した Azure Government への接続
Azure CLI を使用するには、Azure パブリックではなく Azure Government に接続する必要があります。 Azure CLI を使用すると、スクリプトで大規模なサブスクリプションを管理したり、現在 Azure ポータルにはない機能を利用したりできます。 Azure パブリックで PowerShell を使用した経験をお持ちであれば、操作の大部分は同じです。  Azure Government で異なるのは次の点です。

* アカウントへの接続方法
* リージョン名

> [!NOTE]
> PowerShell を初めて使用する場合は、[Azure PowerShell の概要](/powershell/azureps-cmdlets-docs)に関するページを確認してください。
> 
> 

PowerShell を開始する際、Azure Government に接続するよう Azure PowerShell に指示するために、環境パラメーターを指定する必要があります。  パラメーターを指定することで、PowerShell は適切なエンドポイントに接続できます。  エンドポイントのコレクションはアカウントにログインする際に決定されます。  API ごとに必要な環境スイッチのバージョンが異なります。

| 接続の種類 | コマンド |
| --- | --- |
| [Azure Active Directory (クラシック デプロイメント モデル)](https://msdn.microsoft.com/library/dn708504.aspx) のコマンド |`Add-AzureAccount -Environment AzureUSGovernment` |
| [リソース管理](https://msdn.microsoft.com/library/mt125356.aspx)のコマンド |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) のコマンド |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory (Resource Manager デプロイメント モデル)](https://msdn.microsoft.com/library/azure/mt757189.aspx) のコマンド |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI コマンド ライン](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

New-azurestoragecontext でストレージ アカウントに接続して AzureUSGovernment を指定する際にも、環境スイッチを使用できます。

### <a name="determining-region"></a>リージョンの特定
接続が完了した後にも、相違点が&1; つあります。サービスの対象を設定する際に使用するリージョンです。  Azure クラウドごとに異なるリージョンが用意されています。  リージョンの詳細は、利用可能なサービスに関するページで確認できます。  通常、コマンドの Location パラメーターには、このリージョンを使用します。

その際に注意点が&1; つあります。  Azure Government のリージョンでは、一般的な名前とは異なる形式を使用しています。

| 一般的な名前 | コマンド |
| --- | --- |
| 米国政府バージニア州 |米国政府バージニア州 |
| 米国政府アイオワ州 |米国政府アイオワ州 |

> [!NOTE]
> Location パラメーターを使用する際に、US と Gov 間にスペースを挿入しません。
> 
> 

Azure Government で利用可能なリージョンを確認したい場合は、次のコマンドを実行し、最新のリストを印刷してください。

    Get-AzureLocation

Azure で利用可能な環境を調べる場合は、次のコマンドをしてください。

    Get-AzureEnvironment

