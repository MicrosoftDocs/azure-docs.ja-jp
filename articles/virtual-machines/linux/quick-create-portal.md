---
title: "Azure クイック スタート - ポータルで VM を作成する | Microsoft Docs"
description: "Azure クイック スタート - ポータルで VM を作成する"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d009020e86fdfed6a45b5b63b9664c623bcb1843
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Azure Portal で Linux 仮想マシンを作成する

Azure 仮想マシンは、Azure Portal で作成できます。 この方法では、ブラウザーベースのユーザー インターフェイスで仮想マシンとそれに関連するすべてのリソースを作成して構成できます。 このクイック スタートでは、仮想マシンを作成してそこに Web サーバーをインストールする手順を紹介します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-ssh-key-pair"></a>SSH キー ペアの作成

このクイック スタートの作業には SSH キー ペアが必要です。 既存の SSH キー ペアがある場合は、この手順はスキップしてかまいません。

Bash シェルから次のコマンドを実行して画面の指示に従います。 コマンド出力に公開キー ファイルの名前が表示されます。 公開キー ファイルの内容をクリップボードにコピーします。

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Azure へのログイン 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-virtual-machine"></a>仮想マシンの作成

1. Azure ポータルの左上隅にある **[新規]** ボタンをクリックします。

2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS]** の順に選択します。 

3. 仮想マシンの情報を入力します。 **[認証の種類]** には **[SSH 公開キー]** を選択します。 [SSH 公開キー] にキーを貼り付けるときに、先頭と末尾の空白は削除するように注意してください。 完了したら、**[OK]** をクリックします。

    ![ポータルのブレードで VM に関する基本情報を入力する](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 

    ![VM のサイズを示すスクリーンショット](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. 設定ブレードで、既定値のまま **[OK]** をクリックします。

6. 概要ページで **[OK]** をクリックして、仮想マシンのデプロイを開始します。

7. 対応する VM が、Azure Portal のダッシュボードにピン留めされます。 デプロイが完了すると、VM のサマリー ブレードが自動的に表示されます。


## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

仮想マシンとの SSH 接続を作成します。

1. 仮想マシンのブレードで、**[接続]** ボタンをクリックします。 仮想マシンへの接続に使用できる SSH 接続文字列が表示されます。

    ![ポータル 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. 次のコマンドを実行して、SSH セッションを作成します。 接続文字列を、Azure Portal からコピーしたものに置き換えます。

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>NGINX のインストール

次の bash スクリプトを使用して、パッケージのソースを更新し、最新の NGINX パッケージをインストールします。 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完了したら SSH セッションを終了し、Azure Portal の VM のプロパティに戻ります。


## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く 

受信トラフィックと送信トラフィックのセキュリティは、ネットワーク セキュリティ グループ (NSG) で確保します。 Azure Portal から VM を作成すると、SSH 接続用のポート 22 に対する受信の規則が作成されます。 この VM は Web サーバーのホストとなるため、ポート 80 に対する NSG 規則を作成する必要があります。

1. 仮想マシンで **[リソース グループ]** の名前をクリックします。
2. **[ネットワーク セキュリティ グループ]** を選択します。 NSG は **[種類]** 列で確認できます。 
3. 左側のメニューの設定で、**[受信セキュリティ規則]** をクリックします。
4. **[追加]** をクリックします。
5. **[名前]** で「**http**」と入力します。 **[ポート範囲]** が 80 に設定されていることと、**[アクション]** が **[許可]** に設定されていることを確認します。 
6. **[OK]**をクリックします。


## <a name="view-the-nginx-welcome-page"></a>NGINX のようこそページの表示

NGINX がインストールされ、ご利用の VM に対してポート 80 が開放されると、Web サーバーにインターネットからアクセスできるようになります。 Web ブラウザーを開いて、VM のパブリック IP アドレスを入力します。 パブリック IP アドレスは、Azure Portal の VM ブレードで確認できます。

![NGINX の既定のサイト](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、リソース グループ、仮想マシン、すべての関連リソースを削除します。 そのためには、仮想マシン ブレードでリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、単純な仮想マシンとネットワーク セキュリティ グループの規則をデプロイし、Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Linux VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Linux 仮想マシンのチュートリアル](./tutorial-manage-vm.md)

