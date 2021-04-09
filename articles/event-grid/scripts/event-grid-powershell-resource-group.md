---
title: Azure PowerShell のサンプル スクリプト - リソース グループのサブスクライブ | Microsoft Docs
description: この記事では、リソース グループの Event Grid イベントをサブスクライブする方法を示したサンプル Azure PowerShell スクリプトを紹介しています。
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: c8e84fe12cf239e7dbb0e6a15088efda793ddec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460747"
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>PowerShell を使用したリソース グループのイベントのサブスクライブ

このスクリプトは、リソース グループのイベントに対する Event Grid サブスクリプションを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

プレビュー版サンプル スクリプトには、Event Grid モジュールが必要です。 インストールするには、`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` を実行します

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.ps1 "Subscribe to resource group")]

## <a name="sample-script---preview-module"></a>サンプル スクリプト - プレビュー モジュール

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.ps1 "Subscribe to resource group")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](/powershell/azure/get-started-azureps)をご覧ください。
