---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534653"
---
1. プロセス サーバーを実行しているマシンへのリモート デスクトップ接続を確立します。 
2. cspsconfigtool.exe を実行して、Azure Site Recovery プロセス サーバー構成ツールを開始します。
    - このツールは、プロセス サーバーに初めてサインインしたときに自動的に起動します。
    - 自動的に開かれない場合は、デスクトップ上のそのショートカットをクリックしてください。

3. **[Configuration Server FQDN Or IP]\(構成サーバーの FQDN または IP\)** に、プロセス サーバーの登録に使用する構成サーバーの名前または IP アドレスを指定します。
4. **[Configuration Server Port]\(構成サーバーのポート\)** に 443 が指定されていることを確認します。 これは、構成サーバーが要求をリッスンするポートです。
5. **[Connection Passphrase]\(接続パスフレーズ\)** に、構成サーバーを設定するときに指定したパスフレーズを指定します。 パスフレーズを見つけるには:
    -  構成サーバーで、Site Recovery のインストール フォルダー * *\home\svssystems\bin\** に移動します。
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - 次のコマンドを実行して現在のパスフレーズを出力します。
    ```
    genpassphrase.exe -n
    ```

6. **[Data Transfer Port]\(データ転送ポート\)** は、カスタム ポートを指定した場合を除き、既定値のままにします。

7. **[保存]** をクリックして設定を保存し、プロセス サーバーを登録します。

    
    ![プロセス サーバーの登録](./media/site-recovery-vmware-register-process-server/register-ps.png)
