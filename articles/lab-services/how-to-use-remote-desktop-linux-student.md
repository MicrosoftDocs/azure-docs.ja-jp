---
title: Azure Lab Services で Linux VM に接続する | Microsoft Docs
description: Azure Lab Services のラボで Linux 仮想マシン用リモート デスクトップを使用する方法について学習します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85443419"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Azure Lab Services のクラスルーム ラボで Linux 仮想マシンに接続する
この記事では、学生がラボで以下を使用して Linux 仮想マシン (VM) に接続する方法を示します。
- SSH (Secure Shell プロトコル) ターミナル
- GUI (グラフィカル ユーザー インターフェイス) リモート デスクトップ

> [!IMPORTANT] 
> SSH は自動的に構成されるため、学生も講師も、追加のセットアップなしで Linux VM に SSH 接続できます。 ただし、学生が GUI リモート デスクトップを使用して接続する必要がある場合は、講師が追加のセットアップを行う必要があります。  詳細については、[Linux 仮想マシンのリモート デスクトップの有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。

## <a name="connect-to-the-student-vm-using-ssh"></a>SSH を使用して学生の VM に接続する

1. 学生がラボのポータルに直接 (`https://labs.azure.com`)、または登録リンク (`https://labs.azure.com/register/<registrationCode>`) を使用してサインインすると、学生がアクセスできる各ラボのタイルが表示されます。 
   
1. VM が停止状態の場合は、タイルでボタンを切り替えて VM を起動します。 

2. **[接続]** を選択します。 VM に接続するための 2 つのオプションが表示されます。つまり、**SSH** と **RDP** です。

    ![学生用 VM - 接続オプション](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. **[SSH]** オプションを選択すると、 **[仮想マシンに接続する]** ダイアログ ボックスが表示されます。  

    ![SSH 接続文字列](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. テキスト ボックスの横にある **[コピー]** ボタンをクリックして、SSH 接続情報をクリップボードにコピーします。 

5. 次の手順でこの接続情報を使用できるように、TextPad などに SSH 接続情報を保存します。

6. SSH ターミナル ([Putty](https://www.putty.org/) など) から、VM に接続します。

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>GUI リモート デスクトップを使用して学生の VM に接続する
講師は、学生が GUI リモート デスクトップを使用した場合も接続できるように、VM を構成することができます。  この場合、学生は、**Microsoft リモートデスクトップ (RDP)** と **X2Go** クライアント アプリケーションのどちらを使用して各自の VM に接続するかを講師に確認する必要があります。  これらのアプリケーションのどちらを使っても、学生はリモートで各自の VM に接続し、ローカル コンピューター上に Linux のグラフィカル デスクトップを表示できます。

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Microsoft リモート デスクトップ (RDP) を使用して学生の VM に接続する
講師が Linux のグラフィカル デスクトップ環境 (MATE、XFCE など) 用の RDP および GUI パッケージを使用してラボを設定した後、学生は Microsoft リモート デスクトップ (RDP) を使用して各自の Linux VM に接続できるようになります。 接続する手順は次のとおりです。 

1. VM のタイルで、VM が実行されていることを確認し、 **[接続]** をクリックします。 VM に接続するための 2 つのオプションが表示されます。つまり、**SSH** と **RDP** です。

    ![学生用 VM - 接続オプション](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. **RDP** オプションを選択します。  RDP ファイルがマシンにダウンロードされたら、VM に保存します。

3. Windows コンピューターから接続している場合は、通常、Microsoft リモート デスクトップ (RDP) クライアントが既にインストールされ、構成されています。  そのため、RDP ファイルをクリックして開き、リモート セッションを開始するだけで済みます。

    代わりに、Mac または Chromebook から接続する場合は、次の手順を参照してください。
   - [Mac で RDP を使用して VM に接続する](connect-virtual-machine-mac-remote-desktop.md)。
   - [Chromebook で RDP を使用して VM に接続する](connect-virtual-machine-chromebook-remote-desktop.md)。  

### <a name="connect-to-the-student-vm-using-x2go"></a>X2Go を使用して学生の VM に接続する
講師が Linux のグラフィカル デスクトップ環境 (MATE、XFCE など) 用の X2Go および GUI パッケージを使用してラボを設定した後、学生は X2Go を使用して各自の Linux VM に接続できるようになります。

学生は、どの Linux グラフィカル デスクトップ環境がインストールされているかを講師に確認する必要があります。  この情報は、次の手順で X2Go クライアントを使用して接続する際に必要になります。

1. ローカル コンピューターに [X2Go クライアント](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)をインストールします。

1. [最初のセクション](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh)の手順に従って、VM の SSH 接続情報をコピーします。  この情報は、X2Go クライアントを使用して接続するために必要です。

1. SSH 接続情報が得られたら、X2Go クライアントを開き、 **[Session]\(セッション\)**  >  **[New Session]\(新しいセッション\)** の順に選択します。
   ![X2Go で新しいセッションを作成する](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. **[Session Preferences]\(セッションの基本設定\)** ペインに、SSH 接続情報に基づいて値を入力します。  接続情報は次の例のようになります。

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    この例を使用した場合、以下のように値を入力します。

   - **[Session name]\(セッション名\)** - 名前 (VM の名前など) を指定します。
   - **[Host]\(ホスト\)** - VM の ID (例: **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** )。
   - **[Login]\(ログイン\)** - VM のユーザー名 (例: **student**)。
   - **[SSH port]\(SSH ポート\)** - VM に割り当てられた一意のポート (例: **12345**)。
   - **[Session type]\(セッションの種類\)** - 講師が VM を構成した Linux グラフィック デスクトップ環境を選択します。  この情報は、講師から入手する必要があります。

    最後に、 **[OK]** をクリックしてセッションを作成します。

    ![X2Go の基本設定](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  右側のペインでセッションをクリックします。

    ![X2Go で新しいセッションを開始する](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > 次のようなメッセージが表示されたら、 **[yes]\(はい\)** を選択して、パスワードの入力を続けます: **The authenticity of host '[`00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com`]:12345' can't be established.ECDSA key fingerprint is SHA256:00000000000000000000000000000000000000000000.Are you sure you want to continue connecting (yes/no)?**

2. パスワードの入力を求めるメッセージが表示されたら、入力して **[OK]** をクリックします。  これで、VM の GUI デスクトップ環境にリモート接続できます。

## <a name="next-steps"></a>次のステップ
クラスルーム ラボで Linux VM 用リモート デスクトップ接続機能を有効にする方法については、[Linux 仮想マシン用リモート デスクトップの有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。 

