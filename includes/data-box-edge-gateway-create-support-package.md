---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556794"
---
デバイスの問題が発生した場合は、システム ログからサポート パッケージを作成できます。 Microsoft サポートでは、このパッケージを使用して問題のトラブルシューティングを行います。 サポート パッケージを作成するには、次の手順に従います。

1. [デバイスの PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。
2. `Get-HcsNodeSupportPackage` コマンドを使用してサポート パッケージを作成します。 コマンドレットの使用方法は次のとおりです。

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    このコマンドレットはデバイスからログを収集し、指定されたネットワークまたはローカル共有にこれらのログをコピーします。

    使用されるパラメーターは次のとおりです。

    - `-Path` - サポート パッケージのコピー先のネットワークまたはローカル パスを指定します。 (必須)
    - `-Credential` - 保護されたパスにアクセスする視覚情報を指定します。
    - `-Zip` - ZIP ファイルを生成するように指定します。
    - `-Include` - サポート パッケージにコンポーネントを含めるように指定します。 指定しないと、`Default` が想定されます。
    - `-IncludeArchived` - サポート パッケージにアーカイブ ログを含めるように指定します。
    - `-IncludePeriodicStats` - サポート パッケージに定期的な統計ログを含めるように指定します。

    
