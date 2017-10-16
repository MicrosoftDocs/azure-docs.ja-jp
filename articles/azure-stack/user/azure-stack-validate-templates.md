---
title: "テンプレート検証ツールを使用して Azure Stack のテンプレートをチェックする | Microsoft Docs"
description: "Azure Stack にデプロイするためのテンプレートをチェックする"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c99e5ebc2612e10f42bddbbd2f1c17d7404305d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>テンプレート検証ツールで Azure Stack のテンプレートをチェックする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

テンプレート検証ツールを使用して、Azure Stack 用に Azure Resource Manager [テンプレート](azure-stack-arm-templates.md) の準備ができているかどうかをチェックできます。 テンプレート検証ツールは Azure Stack ツールの一部として使用できます。 Azure Stack ツールをダウンロードするには、[GitHub からのツールのダウンロード](azure-stack-powershell-download.md)に関する記事に記載されている手順を使用します。 

テンプレートを検証するには、次の PowerShell モジュールと **TemplateValidator** および **CloudCapabilities** フォルダーにある JSON ファイルを使用します。 

 - AzureRM.CloudCapabilities.psm1 は、Azure Stack のようなクラウド内のサービスとバージョンを表すクラウド機能 JSON ファイルを作成します。
 - AzureRM.TemplateValidator.psm1 は、クラウド機能 JSON ファイルを使用して、Azure Stack へのデプロイ用テンプレートをテストします。
 - AzureStackCloudCapabilities_with_AddOns_20170627.json が既定のクラウド機能ファイルです。  自分で作成することも、このファイルを使用して開始することもできます。 

このトピックでは、テンプレートに対して検証を実行し、必要に応じてクラウド機能ファイルを作成します。

## <a name="validate-templates"></a>テンプレートの検証
次の手順で、AzureRM.TemplateValidator PowerShell モジュールを使用してテンプレートを検証します。 独自のテンプレートを使用するか、または [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)を検証することができます。

1.  AzureRM.TemplateValidator.psm1 PowerShell モジュールをインポートします。
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  テンプレート検証ツールを実行します。

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

テンプレート検証の警告やエラーが、PowerShell コンソールに記録され、ソース ディレクトリ内の HTML ファイルにも記録されます。 検証レポート出力の例は次のようになります。

![サンプル検証レポート](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parameters

| パラメーター | Description | 必須 |
| ----- | -----| ----- |
| TemplatePath | Resource Manager テンプレートを再帰的に検索するパスを指定します | あり | 
| TemplatePattern | 照合するテンプレート ファイルの名前を指定します。 | いいえ |
| CapabilitiesPath | クラウド機能 JSON ファイルのパスを指定します | あり | 
| IncludeComputeCapabilities | VM サイズや VM 拡張機能などの IaaS リソースの評価が含まれます | いいえ |
| IncludeStorageCapabilities | SKU の種類などのストレージ リソースの評価が含まれます | いいえ |
| レポート | 生成される HTML レポートの名前を指定します | いいえ |
| 詳細 | エラーと警告をコンソールに記録します | いいえ|


### <a name="examples"></a>例
この例では、ローカルにダウンロードされたすべての [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)を検証し、Azure Stack Development Kit の機能に対して、VM のサイズと拡張機能も検証します。

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>クラウド機能ファイルを構築する
ダウンロードしたファイルには既定の *AzureStackCloudCapabilities_with_AddOns_20170627.json* ファイルが含まれ、これには、PaaS サービスがインストールされた Azure Stack Development Kit で使用可能なサービスのバージョンが記載されています。  追加のリソース プロバイダーをインストールする場合は、AzureRM.CloudCapabilities PowerShell　モジュールを使用して、新しいサービスを含む JSON ファイルを構築できます。  

1.  Azure Stack への接続があることを確認してください。  これらの手順は Azure Stack Development Kit ホストから実行するか、または [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) を使用して、ワークステーションから接続できます。 
2.  AzureRM.CloudCapabilities PowerShell モジュールをインポートします。

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Get-CloudCapabilities コマンドレットを使用して、サービスのバージョンを取得し、クラウド機能 JSON ファイルを作成します。

    ```PowerShell
    Get-AzureRMCloudCapabilities -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>次のステップ
 - [テンプレートを Azure Stack にデプロイする](azure-stack-arm-templates.md)
 - [Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)

