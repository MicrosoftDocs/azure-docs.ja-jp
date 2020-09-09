---
title: Azure Lab Services での Windows シャットダウン動作の制御に関するガイド | Microsoft Docs
description: アイドル状態の Windows 仮想マシンを自動的にシャットダウンし、Windows シャットダウン コマンドを削除する手順について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e17f6e79c3d18d82dd206954dcfb0e06b02b4d53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445170"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Windows シャットダウン動作の制御ガイド

Azure Lab Services には、Windows 仮想マシン (VM) が予期せずに実行され続けることがないように、次のようないくつかのコスト制御が用意されています。
 - [スケジュールを設定する](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [ユーザーのクォータを設定する](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [切断時の自動シャットダウンを有効にする](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

これらのコスト制御を使用しても、次の場合のように、Windows VM が予期せず実行され続けることがあり、その結果、学生のクォータから差し引かれることになります。

- **RDP ウィンドウが開いたままになっている**
  
    学生が RDP を使用して VM に接続したときに、誤って RDP ウィンドウを開いたままにしておくことがあります。  **[automatic shutdown on disconnect]\(切断時の自動シャットダウン\)** 設定は、RDP セッションが切断された後にのみトリガーされるため、RDP ウィンドウが開いたままになっている限り、有効になりません。

- **VM をオフにするために Windows シャットダウン コマンドが使用されている**
  
    学生は、[Azure Lab Services の [停止] ボタン](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)を使用する代わりに、Windows シャットダウン コマンドや、Windows で提供されている他のシャットダウン メカニズムを使用して、VM をオフにすることがあります。  この場合、Azure Lab Services の観点からは、VM は引き続き使用されています。
    
このガイドでは、このような状況が発生しないようにするために、アイドル状態の Windows VM を自動的にシャットダウンし、 **[スタート]** メニューから Windows シャットダウン コマンドを削除する手順について説明します。  

> [!NOTE]
> 学生が VM を起動したが、実際には RDP を使用してその VM に接続していないときに、VM が予期せずにクォータから差し引かれる場合もあります。  このガイドは、現時点ではこのシナリオに対応して "*いません*"。  代わりに、学生は、VM を起動したら RDP を使用して直ちに VM に接続するよう通知を受けるか、VM を停止する必要があります。

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>アイドル状態の VM に対する自動 RDP 切断とシャットダウン

Windows には **[ローカル グループ ポリシー]** 設定が用意されており、この設定を使用して、アイドル状態になった RDP セッションを自動的に切断するための時間制限を設定できます。  マウスやキーボード入力が "*ない*" 場合、セッションはアイドル状態であると判断されます。  マウスやキーボード入力を伴わない長期実行アクティビティによって、VM はアイドル状態になります。  これには、長いクエリの実行、ビデオのストリーミング、コンパイルなどが含まれます。クラスのニーズに応じて、アイドル時間の制限を設定して、これらの種類のアクティビティを処理するのに十分な時間を確保できます。  たとえば、必要に応じて、アイドル時間の制限を 1 時間以上に設定できます。

次に示すのは、 **[アイドル セッションの制限]** を[ **[automatic shutdown on disconnect]\(切断時の自動シャットダウン\)** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) 設定と組み合わせて構成した場合の学生のエクスペリエンスです。
 1. 学生が、RDP を使用して Windows VM に接続します。
 2. 学生が RDP ウィンドウを開いたままにしているときに、指定された **[アイドル セッションの制限]** (5 分など) の間、VM がアイドル状態になっていると、次のダイアログボックスが表示されます。

    ![アイドル時間の制限の期限切れダイアログ](./media/how-to-windows-shutdown/idle-time-expired.png)

1. 学生が **[OK]** をクリック "*しない*" と、RDP セッションは 2 分後に自動的に切断されます。
2. RDP セッションが切断された後、 **[automatic shutdown on disconnect]\(切断時の自動シャットダウン\)** 設定に指定された期間が経過すると、VM は Azure Lab Services によって自動的にシャットダウンされます。

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>テンプレート VM で RDP のアイドル セッションの時間制限を設定する

RDP セッションのアイドル時間の制限を設定するには、テンプレート VM に接続し、次の PowerShell スクリプトを実行します。

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
または、テンプレート VM を使用して、以下の手動の手順を実行することもできます。

1. Windows キーを押して **gpedit** と入力し、次に **[グループ ポリシーの編集] (コントロール パネル)** を選択します。

1. **[コンピューターの構成] > [管理用テンプレート] > [Windows コンポーネント] > [リモート デスクトップ サービス] > [リモート デスクトップ セッション ホスト] > [セッションの時間制限]** に移動します。  

    ![ローカル グループ ポリシー エディター](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. **[アクティブでアイドル状態になっているリモート デスクトップ サービス セッションの制限時間を設定する]** を右クリックして、 **[編集]** をクリックします。

1. 以下の設定を入力して、 **[OK]** をクリックします。
   1. **[Enabled]** を選択します。
   1. **[オプション]** で、 **[アイドル セッションの制限]** を指定します。

    ![アイドル セッションの制限](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. 最後に、この動作を **[automatic shutdown on disconnect]\(切断時の自動シャットダウン\)** 設定と組み合わせるために、 次のハウツー記事のステップを実行します:[切断時の VM の自動シャットダウンを有効にする](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)。

> [!WARNING]
> PowerShell を使用してレジストリ設定を直接変更するか、グループ ポリシー エディターを手動で使用し、この設定を構成したら、設定を適用するためにまず、VM を再起動する必要があります。  また、レジストリを使用して設定を構成する場合、グループ ポリシー エディターが更新され、レジストリ設定の変更が反映されるとは限りません。しかしながら、レジストリ設定は期待どおりに適用され、アイドル状態のまま指定した時間を経過すると、RDP セッションが切断されます。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>[スタート] メニューからの Windows シャットダウン コマンドの削除

Windows の **[ローカル グループ ポリシー]** 設定を使用して、 **[スタート]** メニューからシャットダウン コマンドを削除することもできます。

シャットダウン コマンドを削除するには、テンプレート VM に接続し、次の PowerShell スクリプトを実行します。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

または、テンプレート VM を使用して、以下の手動の手順を実行することもできます。

1. Windows キーを押して **gpedit** と入力し、次に **[グループ ポリシーの編集] (コントロール パネル)** を選択します。

1. **[コンピューターの構成] > [管理用テンプレート] > タスク バーと [スタート] メニュー**に進みます。  

    ![ローカル グループ ポリシー エディター](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. **[シャットダウン、再起動、スリープ、休止コマンドを削除してアクセスできないようにする]** を右クリックして、 **[編集]** をクリックします。

1. **[有効]** 設定を選択し、 **[OK]** をクリックします。
 
   ![シャットダウン設定](./media/how-to-windows-shutdown/edit-shutdown.png)

1. シャットダウン コマンドが Windows の **[スタート]** メニューに表示されていないことに注意してください。**Disconnect** コマンドのみが表示されています。

    ![シャットダウン コマンド](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>次のステップ
Windows テンプレート VM を準備する方法についての次の記事を参照してください。[Azure Lab Services での Windows テンプレート マシンの設定に関するガイド](how-to-prepare-windows-template.md)