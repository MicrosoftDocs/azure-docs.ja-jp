---
title: Azure Lab Services での Windows シャットダウン動作の制御に関するガイド | Microsoft Docs
description: アイドル状態の Windows 仮想マシンを自動的にシャットダウンし、Windows シャットダウン コマンドを削除する手順について説明します。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 248bbeabaf704ba636e2f82c7a93d0ee90a09f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647700"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Windows シャットダウン動作の制御ガイド

Azure Lab Services には、Windows 仮想マシン (VM) が予期せずに実行され続けることがないように、次のようないくつかのコスト制御が用意されています。
 - [スケジュールを設定する](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [ユーザーのクォータを設定する](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [切断時の自動シャットダウンを有効にする](./how-to-enable-shutdown-disconnect.md)

これらのコスト制御を使用しても、次の場合のように、Windows VM が予期せず実行され続けることがあり、その結果、学生のクォータから差し引かれることになります。

- **RDP ウィンドウが開いたままになっている**
  
    学生が RDP を使用して VM に接続したときに、誤って RDP ウィンドウを開いたままにしておくことがあります。  **[automatic shutdown on disconnect]\(切断時の自動シャットダウン\)** 設定は、RDP セッションが切断された後にのみトリガーされるため、RDP ウィンドウが開いたままになっている限り、有効になりません。

- **VM をオフにするために Windows シャットダウン コマンドが使用されている**
  
    学生は、[Azure Lab Services の [停止] ボタン](./how-to-use-classroom-lab.md#start-or-stop-the-vm)を使用する代わりに、Windows シャットダウン コマンドや、Windows で提供されている他のシャットダウン メカニズムを使用して、VM をオフにすることがあります。  この場合、Azure Lab Services の観点からは、VM は引き続き使用されています。
    
このガイドでは、このような状況が発生しないようにするために、アイドル状態の Windows VM を自動的にシャットダウンし、 **[スタート]** メニューから Windows シャットダウン コマンドを削除する手順について説明します。  

> [!NOTE]
> 学生が VM を起動したが、実際には RDP を使用してその VM に接続していないときに、VM が予期せずにクォータから差し引かれる場合もあります。  このガイドは、現時点ではこのシナリオに対応して "*いません*"。  代わりに、学生は、VM を起動したら RDP を使用して直ちに VM に接続するよう通知を受けるか、VM を停止する必要があります。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>[スタート] メニューからの Windows シャットダウン コマンドの削除

Windows の **[ローカル グループ ポリシー]** 設定を使用して、 **[スタート]** メニューからシャットダウン コマンドを削除することもできます。

シャットダウン コマンドを削除するには、テンプレート VM に接続し、次の PowerShell スクリプトを実行します。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

または、テンプレート VM を使用して、以下の手動の手順を実行することもできます。

1. Windows キーを押して **gpedit** と入力し、次に **[グループ ポリシーの編集] (コントロール パネル)** を選択します。

1. **[コンピューターの構成] > [管理用テンプレート] > タスク バーと [スタート] メニュー** に進みます。  

    ![ローカル グループ ポリシー エディター](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. **[シャットダウン、再起動、スリープ、休止コマンドを削除してアクセスできないようにする]** を右クリックして、 **[編集]** をクリックします。

1. **[有効]** 設定を選択し、 **[OK]** をクリックします。
 
   ![シャットダウン設定](./media/how-to-windows-shutdown/edit-shutdown.png)

1. シャットダウン コマンドが Windows の **[スタート]** メニューに表示されていないことに注意してください。**Disconnect** コマンドのみが表示されています。

    ![シャットダウン コマンド](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>次のステップ
Windows テンプレート VM を準備する方法についての次の記事を参照してください。[Azure Lab Services での Windows テンプレート マシンの設定に関するガイド](how-to-prepare-windows-template.md)