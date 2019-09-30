---
title: Azure VM でリモート デスクトップの接続が頻繁に切れる | Microsoft Docs
description: Azure VM でリモート デスクトップの接続が頻繁に切れる問題のトラブルシューティングの方法について説明します。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: be563e39ed1bfa405830999a96d8630b6f8254bb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057966"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Azure VM でリモート デスクトップの接続が頻繁に切れる

この記事では、リモート デスクトップ プロトコル (RDP) を使用した Azure 仮想マシン (VM) への接続が頻繁に切れる問題のトラブルシューティングの方法を説明します。

> [!NOTE] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager デプロイ モデルの使用について説明します。 新しいデプロイでは、クラシック デプロイ モデルではなく、このモデルを使用することをお勧めします。

## <a name="symptom"></a>症状

セッション中に断続的な RDP 接続の問題が発生する。 最初に VM に接続することはできるが、その後で接続が切断される。

## <a name="cause"></a>原因

この問題は、RDP リスナーが正しく構成されていない場合に発生する可能性があります。 通常、この問題は、カスタム イメージを使用している VM で発生します。

## <a name="solution"></a>解決策

これらの手順を実行する前に、バックアップとして、影響を受ける VM の [OS ディスクのスナップショットを取得](../windows/snapshot-copy-managed-disk.md)します。 

この問題をトラブルシューティングするには、シリアル コンソールを使用するか、VM の OS ディスクを復旧 VM にアタッチして [VM をオフライン修復](#repair-the-vm-offline)します。

### <a name="serial-control"></a>シリアル コンソール

1. [シリアル コンソールに接続し、CMD インスタンスを開きます](./serial-console-windows.md)。 次に、次のコマンドを実行して、RDP 構成をリセットします。 VM でシリアル コンソールが有効になっていない場合は、次の手順に進みます。
2. RDP セキュリティ レイヤーを 0 まで下げます。 この設定では、サーバーとクライアント間の通信は、ネイティブの RDP 暗号化を使用します。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. 従来の RDP クライアントが接続できるように、暗号化レベルを最小設定まで下げます。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. クライアント コンピューターのユーザー構成を読み込むように RDP を設定します。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. RDP キープアライブ コントロールを有効にします。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. RDP 再接続のコントロールを設定します。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. RDP セッション時間のコントロールを設定します。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. RDP 切断時間のコントロールを設定します。 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. RDP 接続時間のコントロールを設定します。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. RDP セッションのアイドル時間のコントロールを設定します。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. "Limit the maximum concurrent connections"(同時接続の最大数を制限する) コントロールを設定します。

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. VM を再起動し、RDP を使用して接続をもう一度やり直します。

### <a name="repair-the-vm-offline"></a>VM をオフライン修復する

1. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。
2. OS ディスクを復旧 VM に接続したら、[ディスクの管理] コンソールでそのディスクが **[オンライン]** になっていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。
3. 接続した OS ディスクで、 **\windows\system32\config** フォルダーに移動します。 ロールバックが必要な場合に備えて、このフォルダーのすべてのファイルをバックアップとしてコピーします。
4. レジストリ エディター (regedit.exe) を起動します。
5. **HKEY_LOCAL_MACHINE** キーを選択します。 メニューで、 **[ファイル]**  >  **[ハイブの読み込み]** を選択します。
6. 接続した OS ディスクで、 **\windows\system32\config\SYSTEM** フォルダーを参照します。 ハイブの名前として、「**BROKENSYSTEM**」と入力します。 **HKEY_LOCAL_MACHINE** キーの下に、新しいレジストリ ハイブが表示されます。 次に、**HKEY_LOCAL_MACHINE** キーの下のソフトウェア ハイブ **\windows\system32\config\SOFTWARE** を読み込みます。 ハイブ ソフトウェアの名前として、「**BROKENSYSTEM**」と入力します。 
7. 管理者特権でのコマンド プロンプト ウィンドウを開いて ( **[管理者として実行]** )、残りの手順のコマンドを実行して RDP 構成をリセットします。 
8. サーバーとクライアント間の通信がネイティブの RDP 暗号化を使用するように、RDP セキュリティ レイヤーを 0 まで下げます。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. 従来の RDP クライアントが接続できるように、暗号化レベルを最小設定まで下げます。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. クライアント マシンのユーザー構成を読み込むように RDP を設定します。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. RDP キープアライブ コントロールを有効にします。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. RDP 再接続のコントロールを設定します。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. RDP セッション時間のコントロールを設定します。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. RDP 切断時間のコントロールを設定します。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. RDP 接続時間のコントロールを設定します。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. RDP セッションのアイドル時間のコントロールを設定します。   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. "Limit the maximum concurrent connections"(同時接続の最大数を制限する) コントロールを設定します。

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. VM を再起動し、RDP を使用して接続をもう一度やり直します。

## <a name="need-help"></a>お困りの際は、 
サポートにお問い合せください。 お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。





