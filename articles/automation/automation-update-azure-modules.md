---
title: "Azure Automation の Azure モジュールの更新 | Microsoft Docs"
description: "この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。"
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7a2999b3b1a54668f6ef45433efabd5a495418fe
ms.openlocfilehash: ec84df70d4a77e3b81a88aa286fc492d92e3e753
ms.lasthandoff: 02/14/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する方法

各 Automation アカウントでは、最も一般的な Azure PowerShell モジュールが既定で提供されます。  Azure モジュールは Azure チームにより定期的に更新されるため、Automation アカウントでは、新しいバージョンが利用可能になったときにアカウント内の各モジュールを更新できるようになりました。

## <a name="updating-azure-modules"></a>Azure モジュールの更新

1. Automation アカウントの [モジュール] ブレードに、**Update Azure Modules (Azure モジュールの更新)** オプションが追加されました。  このオプションは常に有効です。<br><br> ![[モジュール] ブレードの [Update Azure Modules] (Azure モジュールの更新) オプション](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. **Update Azure Modules (Azure モジュールの更新)** をクリックすると、操作を続けるかどうかを尋ねる確認通知が表示されます。<br><br> ![Azure モジュールの更新通知](media/automation-update-azure-modules/automation-update-azure-modules-notification.png)

3. **[はい]** をクリックすると、モジュールの更新プロセスが開始されます。  更新プロセスの所要時間は約 15 分から 20 分であり、以下のモジュールが更新されます。

  * Azure
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    モジュールが既に最新の状態である場合、プロセスは数秒で完了します。  更新プロセスが完了すると通知が表示されます。<br><br> ![Azure モジュールの更新の更新状態](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

スケジュール済みの runbook がある場合、更新プロセスの一環として、最新バージョンのモジュールを使用するようにスケジュールが更新されます。

runbook で Azure PowerShell モジュールのコマンドレットを使用して Azure リソースを管理する場合、モジュールを最新に保つために&1; か月に&1; 回程度この更新プロセスを実行する必要があります。

## <a name="next-steps"></a>次のステップ

統合モジュールの詳細、およびカスタム モジュールを作成して Automation をさらに別のシステム、サービス、またはソリューションと統合する方法については、[統合モジュール](automation-integration-modules.md)に関するページを参照してください。
