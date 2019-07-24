---
title: Azure VM でリモート デスクトップ サービスが起動しない | Microsoft Docs
description: 仮想マシンへの接続時のリモート デスクトップ サービスに関する問題のトラブルシューティングを行う方法について学習する |Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: c9accf09da240f6d3235edd3c9da1876529d8654
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650752"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Azure VM でリモート デスクトップ サービスが起動しない

この記事では、Azure 仮想マシン (VM) への接続時にリモート デスクトップ サービス (TermService) が起動しないか起動に失敗する場合の問題のトラブルシューティングを行う方法について説明します。

> [!NOTE]  
> Azure には、リソースの作成と操作を行うデプロイメント モデルが 2 種類あります。[Azure Resource Manager とクラシックです](../../azure-resource-manager/resource-manager-deployment-model.md)。 この記事では、Resource Manager デプロイ モデルの使用について説明します。 新しいデプロイでは、クラシック デプロイ モデルではなく、このモデルを使用することをお勧めします。

## <a name="symptoms"></a>現象

VM に接続しようとすると、次のシナリオが発生します。

- VM のスクリーンショットに、完全に読み込まれ、資格情報を待機しているオペレーティング システムが表示されている。

    ![VM の状態のスクリーンショット](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- イベント ビューアーを使用して、VM でイベント ログをリモートで表示します。 リモート デスクトップ サービス (TermService) が起動していないか、起動に失敗していることがわかります。 サンプル ログを以下に示します。

    **ログ名**:    システム </br>
    **ソース**:      サービス コントロール マネージャー </br>
    **日付**:        12/16/2017 11:19:36 AM</br>
    **イベント ID**:    7022</br>
    **タスク カテゴリ**:なし</br>
    **レベル**:       Error</br>
    **キーワード**:    クラシック</br>
    **ユーザー**:        該当なし</br>
    **コンピューター**:      vm.contoso.com</br>
    **説明**:Remote Desktop Services サービスは起動時に停止しました。 

    次のクエリを実行して、これらのエラーを検索するシリアル アクセス コンソール機能を使用することもできます。 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>原因
 
この問題は、リモート デスクトップ サービスが VM で実行されていないために発生します。 原因は、次のどのシナリオかによって異なります。 

- TermService サービスが**無効**に設定されている。 
- TermService サービスがクラッシュまたはハングする。 
- 構成が適切でないため、TermService が起動しない。

## <a name="solution"></a>解決策

この問題をトラブルシューティングするには、シリアル コンソールを使用します。 または、VM の OS ディスクを復旧 VM に接続して、[VM をオフライン修復](#repair-the-vm-offline)します。

### <a name="use-serial-console"></a>シリアル コンソールの使用

1. **[サポートとトラブルシューティング]** > **[シリアル コンソール]** を選択して [[シリアル コンソール]](serial-console-windows.md) にアクセスします。 VM で機能が有効な場合、VM を正常に接続できます。

2. CMD インスタンス用の新しいチャネルを作成します。 「**CMD**」と入力してチャネルを開始し、チャネル名を取得します。

3. CMD インスタンスを実行しているチャネルに切り替えます。 この例では、チャネル 1 です。

   ```
   ch -si 1
   ```

4. **Enter** キーをもう一度押し、VM の有効なユーザー名とパスワード (ローカルまたはドメイン ID) を入力します。

5. TermService サービスの状態についてクエリを実行します。

   ```
   sc query TermService
   ```

6. サービス状態の表示が **[停止済み]** の場合は、サービスを開始してみてください。

    ```
    sc start TermService
     ``` 

7. サービスが正常に開始されているかどうかを確認するために、サービスに対してもう一度クエリを実行します。

   ```
   sc query TermService
   ```
8. サービスを開始できない場合、表示されたエラーに応じて解決策を実行します。

    |  Error |  推奨事項 |
    |---|---|
    |5 - ACCESS DENIED |[アクセス拒否エラーによる TermService サービスの停止](#termservice-service-is-stopped-because-of-an-access-denied-problem)に関する記述を参照してください。 |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |「[TermService サービスが無効化されている](#termservice-service-is-disabled)」を参照してください。  |  
    |1058 - ERROR_SERVICE_DISABLED  |「[TermService サービスがクラッシュまたはハングする](#termservice-service-crashes-or-hangs)」を参照してください。  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。|
    |1067 - ERROR_PROCESS_ABORTED  |「[TermService サービスがクラッシュまたはハングする](#termservice-service-crashes-or-hangs)」を参照してください。  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |「[TermService service fails because of logon failure](#termservice-service-fails-because-of-logon-failure)」 (ログオンの失敗が原因で TermService サービスが失敗する) を参照してください |
    |1070 - ERROR_SERVICE_START_HANG   | 「[TermService サービスがクラッシュまたはハングする](#termservice-service-crashes-or-hangs)」を参照してください。 |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | 「[TermService サービスが無効化されている](#termservice-service-is-disabled)」を参照してください。  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 |
    |1753   |問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>アクセス拒否の問題が原因で TermService サービスが停止する

1. [シリアル コンソール](serial-console-windows.md)に接続し、PowerShell インスタンスを開きます。
2. 次のスクリプトを実行して、プロセス モニター ツールをダウンロードします。

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. ここで、**procmon** トレースを開始します。

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```

4. **アクセス拒否**の原因となっているサービスを開始して、問題を再現します。 

   ```
   sc start TermService 
   ```

   失敗した場合は、プロセス モニター トレースを終了します。

   ```   
   procmon /Terminate 
   ```

5. \ **c:\temp\ProcMonTrace.PML** というファイルを収集します。

    1. [データ ディスクを VM に接続します](../windows/attach-managed-disk-portal.md
)。
    2. 新しいドライブにファイルをコピーできるシリアル コンソールを使用します。 たとえば、「 `copy C:\temp\ProcMonTrace.PML F:\` 」のように入力します。 このコマンドの F は、接続されたデータ ディスクのドライブ文字です。
    3. データ ドライブを切断してから、プロセス モニターがインストールされている作業用 VM に接続します。

6. プロセス モニター (作業用 VM) を使用して、**ProcMonTrace.PML** を開きます。 次に、以下のスクリーンショットに示すように、 **Result is ACCESS DENIED** でフィルター処理します。

    ![プロセス モニターでの結果によるフィルター処理](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. 出力されるレジストリ キー、フォルダー、ファイルを修正します。 通常、この問題は、サービスで使用されるサインイン アカウントに、これらのオブジェクトにアクセスするための ACL アクセス許可がない場合に発生します。 サインイン アカウントの適切な ACL アクセス許可は、正常な VM で確認できます。 

#### <a name="termservice-service-is-disabled"></a>TermService サービスが無効化されている

1. サービスを既定のスタートアップ値に復元します。

   ```
   sc config TermService start= demand 
   ```

2. サービスを開始します。

   ```
   sc start TermService
   ```

3. その状態をもう一度クエリして、サービスが実行されていることを確認します。

   ```
   sc query TermService 
   ```

4. リモート デスクトップを使用して VM に接続してみます。

#### <a name="termservice-service-fails-because-of-logon-failure"></a>ログオンの失敗が原因で TermService サービスが失敗する

1. この問題は、このサービスの開始アカウントが変更された場合に発生します。 これを既定値に戻します。 

        sc config TermService obj= 'NT Authority\NetworkService'
2. サービスを開始します。

        sc start TermService
3. リモート デスクトップを使用して VM に接続してみます。

#### <a name="termservice-service-crashes-or-hangs"></a>TermService サービスがクラッシュまたはハングする
1. サービスの状態が**開始中**または**停止中**のままになっている場合は、サービスを停止してみてください。 

        sc stop TermService
2. サービスをそれ自体の 'svchost' コンテナーで切り離します。

        sc config TermService type= own
3. サービスを開始します。

        sc start TermService
4. それでもサービスを開始できない場合は、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

### <a name="repair-the-vm-offline"></a>VM をオフライン修復する

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。
2. 復旧 VM へのリモート デスクトップ接続を開始します。 接続したディスクが [ディスクの管理] コンソールで **[オンライン]** になっていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。
3. 管理者特権でのコマンド プロンプト インスタンス (**[管理者として実行]**) を開きます。 次に、以下のスクリプトを実行します。 接続された OS ディスクに割り当てられたドライブ文字が **F** であると仮定します。これを、ご利用の VM の適切な値に置き換えます。 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [OS ディスクを切断して、VM を再作成します](../windows/troubleshoot-recovery-disks-portal.md)。 その後、問題が解決されているかどうかを確認します。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、[サポートに問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)て問題を解決してください。
