---
title: "Azure Stack クイック スタート - VM ポータルの作成"
description: "Azure Stack クイック スタート - ポータルを使用した Linux VM の作成"
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5f815bafdcc7c05ec7f4149fb7c9df178f0f80e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Azure Stack ポータルで Linux 仮想マシンを作成する

*適用対象: Azure Stack 統合システムおよび Azure Stack 開発キット*

Azure Stack 仮想マシンは、Azure Stack ポータルで作成できます。 この方法では、仮想マシンとすべての関連リソースを作成して構成するためのブラウザー ベースのユーザー インターフェイスが提供されます。 このクイック スタートでは、Linux 仮想マシンをすばやく作成し、そこに Web サーバーをインストールする方法を示します。

## <a name="prerequisites"></a>前提条件

* **Azure Stack Marketplace 内の Linux イメージ**

   Azure Stack Marketplace には、既定では Linux イメージが含まれていません。 そのため、Linux 仮想マシンを作成するには、その前に Azure Stack オペレーターが「[Download marketplace items from Azure to Azure Stack (Azure から Azure Stack へのマーケットプレース項目のダウンロード)](../azure-stack-download-azure-marketplace-item.md)」のトピックで説明されている手順を使用して **Ubuntu Server 16.04 LT** イメージをダウンロードしていることを確認してください。

* **SSH クライアントへのアクセス**

   Azure Stack Development Kit (ASDK) を使用している場合は、使用している環境で SSH クライアントにアクセスできない可能性があります。 この場合は、SSH クライアントを含むいくつかのパッケージから選択できます。 たとえば、SSH クライアントと SSH キー ジェネレーターを含む PuTTY (puttygen.exe) をインストールできます。 指定できるオプションの詳細については、Azure の関連記事「[How to Use SSH keys with Windows on Azure (Azure 上の Windows で SSH キーを使用する方法)](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients)」を参照してください。

   このクイック スタートでは、PuTTY を使用して SSH キーを生成し、Linux 仮想マシンに接続します。 PuTTY をダウンロードしてインストールするには、[http://www.putty.org/](http://www.putty.org) にアクセスしてください。

## <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成

このクイック スタートを完了するには、SSH キー ペアが必要です。 既存の SSH キー ペアがある場合は、この手順はスキップしてかまいません。

1. PuTTY のインストール フォルダー (既定の場所は ```C:\Program Files\PuTTY```) に移動し、```puttygen.exe``` を実行します。
2. [PuTTY Key Generator] ウィンドウで、**[Type of key to generate] (生成するキーの種類)** が **[RSA]** に設定され、**[Number of bits in a generated key] (生成されるキーのビット数)** が **[2048]** に設定されていることを確認します。 準備ができたら、**[Generate] (生成)** をクリックします。

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. キーの生成プロセスを完了するには、[PuTTY Key Generator] ウィンドウ内でマウス カーソルを移動します。
4. キーの生成が完了したら、**[Save public key] (公開キーを保存する)** および **[Save private key] (秘密キーを保存する)** をクリックして公開キーと秘密キーをファイルに保存します。

   ![PuTTY キー](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Azure Stack ポータルへのサインイン

Azure Stack ポータルにサインインします。 Azure Stack ポータルのアドレスは、接続している Azure Stack 製品によって異なります。

* Azure Stack Development Kit (ASDK) の場合は、https://portal.local.azurestack.external にアクセスします。
* Azure Stack 統合システムの場合は、Azure Stack オペレーターによって提供された URL にアクセスします。

## <a name="create-the-virtual-machine"></a>仮想マシンの作成

1. Azure Stack ポータルの左上隅にある **[新規]** ボタンをクリックします。

2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS]** の順に選択します。
3. **Create** をクリックしてください。

4. 仮想マシンの情報を入力します。 **[認証の種類]** には **[SSH 公開キー]** を選択します。 SSH 公開キー (前にファイルに保存したもの) を貼り付けるときは、先頭と末尾の空白をすべて削除するように注意してください。 完了したら、**[OK]** をクリックします。

   ![仮想マシンの基本](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 仮想マシンとして **[D1_V2]** を選択します。

   ![マシンのサイズ](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. **[設定]** ページで、既定値をそのままにして **[OK]** をクリックします。

7. **[概要]** ページで、**[OK]** をクリックして仮想マシンのデプロイを開始します。


## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

1. 仮想マシンのページで **[接続]** をクリックします。 これにより、仮想マシンに接続するために使用できる SSH 接続文字列が表示されます。

   ![仮想マシンを接続する](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. PuTTY を開きます。
3. **[PuTTY Configuration] (PuTTY の構成)** 画面で、**[カテゴリ]** の下の **[SSH]** を展開してから、**[Auth] (認証)** をクリックします。**[参照]** をクリックし、前に保存した秘密キー ファイルを選択します。

   ![PuTTY 秘密キー](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. **[カテゴリ]** で、上にスクロールして **[Session] (セッション)** をクリックします。
5. **[Host Name (or IP address)] (ホスト名 (または IP アドレス))** ボックスで、前に表示した Azure Stack ポータルから接続文字列を貼り付けます。 この例では、この文字列は ```asadmin@192.168.102.34``` です。
 
   ![PuTTY セッション](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. **[開く]** をクリックして、仮想マシンへのセッションを開きます。

   ![Linus セッション](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>NGINX のインストール

次の bash スクリプトを使用してパッケージ ソースを更新し、仮想マシンに最新の NGINX パッケージをインストールします。 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完了したら、SSH セッションを終了し、Azure Stack ポータルの仮想マシンの [概要] ページに戻ります。


## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く 

受信トラフィックと送信トラフィックのセキュリティは、ネットワーク セキュリティ グループ (NSG) で確保します。 Azure Stack ポータルから仮想マシンが作成されると、SSH 接続用のポート 22 上の受信規則が作成されます。 この仮想マシンは Web サーバーをホストするため、ポート 80 用の NSG 規則を作成する必要があります。

1. 仮想マシンの **[概要]** ページで、**[リソース グループ]** の名前をクリックします。
2. 仮想マシンの**ネットワーク セキュリティ グループ**を選択します。 NSG は **[種類]** 列で確認できます。 
3. 左側のメニューの **[設定]** で、**[受信セキュリティ規則]** をクリックします。
4. **[追加]**をクリックします。
5. **[名前]** で「**http**」と入力します。 **[ポート範囲]** が 80 に設定されていることと、**[アクション]** が **[許可]** に設定されていることを確認します。 
6. **[OK]**をクリックします。


## <a name="view-the-nginx-welcome-page"></a>NGINX のようこそページの表示

NGINX がインストールされ、仮想マシン上のポート 80 が開かれたため、その仮想マシンのパブリック IP アドレスで Web サーバーにアクセスできるようになりました。 そのパブリック IP アドレスは、Azure Stack ポータルの仮想マシンの [概要] ページにあります。

Web ブラウザーを開き、```http://<public IP address>``` を参照します。

![NGINX の既定のサイト](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、リソース グループ、仮想マシン、すべての関連リソースを削除します。 そのためには、仮想マシン ページでリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、単純な Linux 仮想マシンとネットワーク セキュリティ グループの規則をデプロイし、Web サーバーをインストールしました。 Azure Stack 仮想マシンの詳細については、「[Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)」に進んでください。

