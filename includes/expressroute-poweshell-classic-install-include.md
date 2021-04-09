---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018167"
---
最新バージョンの Azure Service Management (SM) PowerShell モジュールと ExpressRoute モジュールをインストールします。 Azure CloudShell 環境を使用して SM モジュールを実行することはできません。

1. [Service Management モジュールのインストール](/powershell/azure/servicemanagement/install-azure-ps)に関する記事の手順を使用して、Azure Service Management モジュールをインストールします。 Az または RM モジュールが既にインストールされている場合は、必ず '-AllowClobber' を使用してください。
2. インストールされているモジュールをインポートします。 次の例を使用する場合、インストールされている PowerShell モジュールの場所とバージョンを反映するようにパスを調整します。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Azure アカウントにサインインするには、管理者特権で PowerShell コンソールを開き、アカウントに接続します。 次の例を使用すると、Service Management モジュールを使用した接続に役立ちます。

   ```powershell
   Add-AzureAccount
   ```