---
author: rajeswari-mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181703"
---
特定の状況に応じた手順に従います。

### <a name="unregister-a-connected-process-server"></a>接続されているプロセス サーバーの登録を解除する

1. 管理者としてプロセス サーバーにリモート接続を確立します。
2. **[コントロール パネル]** で、 **[プログラム]、[プログラムのアンインストール]** の順に開きます。
3. プログラム **Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー** をアンインストールします。
4. プログラム**Microsoft Azure Site Recovery Configuration/Process Server (Microsoft Azure Site Recovery 構成/プロセス サーバー)** をアンインストールします。
5. 手順 3 と 4 のプログラムがアンインストールされたら、**Microsoft Azure Site Recovery Configuration/Process Server Dependencies (Microsoft Azure Site Recovery 構成/プロセス サーバーの依存関係)** をアンインストールします。

### <a name="unregister-a-disconnected-process-server"></a>接続されていないプロセス サーバーの登録を解除する

プロセス サーバーがインストールされているマシンを回復する方法がない場合にのみ、以下の手順を使用してください。

1. 管理者として構成サーバーにサインインします。
2. 管理コマンド プロンプトを開き、`%ProgramData%\ASR\home\svsystems\bin` を参照します。
3. 以下のコマンドを実行して、1 つ以上のプロセス サーバーの一覧を取得します。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - 連続 No: プロセス サーバーのシリアル番号。
    - IP/Name:プロセス サーバーを実行しているマシンの IP アドレスと名前。
    - Heartbeat:プロセス サーバー マシンからの最後のハートビート。
    ![登録解除コマンド](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 登録を解除するプロセス サーバーのシリアル番号を指定します。
5. プロセス サーバーの登録解除により、その詳細のすべてがシステムから削除され、次のメッセージが表示されます。 **<サーバー名 (サーバーの IP アドレス) の登録が正常に解除されました**

