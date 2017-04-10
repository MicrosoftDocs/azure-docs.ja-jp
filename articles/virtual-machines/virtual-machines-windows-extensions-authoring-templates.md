---
title: "Windows VM の拡張機能を使用したテンプレートの作成 | Microsoft Docs"
description: "Windows VM の拡張機能を使用した Azure Resource Manager テンプレートの作成について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 418dd1f7-ded8-45ab-9a5a-a59d245e2555
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: cc2e3c234ef9219d1b836771dbbc456d73a13ac8


---
# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Windows VM 拡張機能を使用した Azure Resource Manager テンプレートの作成
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Azure PowerShell では、次の Azure PowerShell コマンドレットを実行します。

      Get-AzureVMAvailableExtension


このコマンドレットによって、発行元の名前、拡張機能名、バージョンが次のように返されます。

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

これら 3 つのプロパティはそれぞれ、前述のテンプレート スニペットに含まれる "publisher"、"type"、"typeHandlerVersion" に対応しています。

> [!NOTE]
> 常に拡張機能の最新バージョンを使用して最新機能を取得することをお勧めします。
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>拡張機能構成パラメーターのスキーマの識別
拡張機能のテンプレートを作成する次の手順では、構成パラメーターを指定するための形式を特定します。 それぞれの拡張機能では、独自のパラメーター セットがサポートされています。

Windows 拡張機能のサンプル構成については、[Windows 拡張機能のサンプル](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

VM の拡張機能を使用して完成したテンプレートを取得するには、以下を参照してください。

[Windows VM のカスタム スクリプト拡張機能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

テンプレートを作成したら、Azure PowerShell を使用してそのテンプレートをデプロイできます。




<!--HONumber=Nov16_HO3-->


