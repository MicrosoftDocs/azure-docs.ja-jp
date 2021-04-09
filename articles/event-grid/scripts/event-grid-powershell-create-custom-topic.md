---
title: Azure PowerShell のサンプル スクリプト - カスタム トピックの作成 | Microsoft Docs
description: この記事では、Event Grid のカスタム トピックの作成方法を示したサンプル Azure PowerShell スクリプトを紹介しています。
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 915b8e13adaa440063f5db62b72fa39e1035d576
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460781"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>PowerShell を使用した Event Grid のカスタム トピックの作成

このスクリプトは、Event Grid のカスタム トピックを作成します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してカスタム トピックを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | Event Grid のカスタム トピックを作成します。 |

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](/powershell/azure/get-started-azureps)をご覧ください。
