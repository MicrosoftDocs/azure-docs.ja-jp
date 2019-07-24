---
title: Azure PowerShell のサンプル スクリプト - サービス インスタンスのスケーリング | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - サービス インスタンスのスケーリング
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 8b457f1d2dd8c2f9e5c707906eb9ca2aa818c411
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735086"
---
# <a name="scale-the-service-instance"></a>サービス インスタンスのスケーリング

このサンプル スクリプトは、リージョンをスケーリングして API Management サービス インスタンスに追加します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をローカルにインストールして使う場合、このチュートリアルでは Azure PowerShell モジュールのバージョン 1.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/api-management/scale-and-addregion-apim-service/scale_and_addregion_apim_service.ps1 "Scale the APIM service instance")]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループとすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/overview)を参照してください。

その他の Azure API Management 用 Azure PowerShell サンプルは、[PowerShell サンプル](../powershell-samples.md)のページにあります。
