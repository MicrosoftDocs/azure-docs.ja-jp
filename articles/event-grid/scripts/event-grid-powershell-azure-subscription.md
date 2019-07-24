---
title: Azure PowerShell のサンプル スクリプト - Azure サブスクリプションのサブスクライブ | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Azure サブスクリプションのサブスクライブ
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 3d3d7a864bf6941dfb0bf7496b291639e7e5ea6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180553"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>PowerShell を使用した Azure サブスクリプションのイベントのサブスクライブ

このスクリプトは、Azure サブスクリプションのイベントに対する Event Grid サブスクリプションを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

プレビュー版サンプル スクリプトには、Event Grid モジュールが必要です。 インストールするには、`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` を実行します

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>サンプル スクリプト - プレビュー モジュール

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次の手順

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/get-started-azureps)をご覧ください。
