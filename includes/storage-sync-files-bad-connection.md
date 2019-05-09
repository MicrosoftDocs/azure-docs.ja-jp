---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488027"
---
このエラーは、サーバーから Azure File Sync サービスにアクセスできない場合に発生します。 次の手順を行うと、このエラーを解決できます。

1. Windows サービス `FileSyncSvc.exe` がファイアウォールによってブロックされていないことを確認します。
2. Azure File Sync サービスへの発信接続に対して、ポート 443 が開いていることを確認します。 この確認には、`Test-NetConnection` コマンドレットを使用します。 以下の `<azure-file-sync-endpoint>` プレースホルダーの URL については、[Azure File Sync のプロキシとファイアウォールの設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)に関するページを参照してください。 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. プロキシ構成が意図したとおりに設定されていることを確認します。 この確認には、`Get-StorageSyncProxyConfiguration` コマンドレットを使用します。 Azure File Sync のプロキシ構成の詳細については、[Azure File Sync のプロキシとファイアウォールの設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)に関するページを参照してください。

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. ネットワーク接続のトラブルシューティングについてさらにサポートが必要な場合は、ネットワーク管理者に相談してください。