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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Azure ポータルで Linux 仮想マシンを作成する

Azure 仮想マシンは、Azure ポータルで作成できます。 この方法では、ブラウザーベースのユーザー インターフェイスで VM と関連するすべての Azure リソースを作成および構成できます。

開始する前に、秘密および公開 SSH キーを用意しておく必要があります。 Azure 用 SSH キーの作成の詳細については、[Azure 用 SSH キーの作成](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

## <a name="log-in-to-azure"></a>Azure へのログイン 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-virtual-machine"></a>仮想マシンの作成

2. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

3. **[新規]** ブレードの **[Compute]** を選択し、**[Compute]** ブレードの **[Ubuntu Server 16.04 LTS]** を選択して、**[作成]** ボタンをクリックします。

4. 仮想マシンの **[基本]** フォームに入力します。 **[認証の種類]** には SSH をお勧めします。 **[SSH 公開キー]** にキーを貼り付けるときに、先頭と末尾の空白は削除するように注意してください。 **[リソース グループ]** では、新しいグループを作成します。 リソース グループとは、Azure リソースの作成と一括管理に使用する論理コンテナーです。 完了したら、**[OK]** をクリックします。

    ![ポータルのブレードで VM に関する基本情報を入力する](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. VM のサイズを選択し、**[選択]** をクリックします。 

    ![ポータルのブレードで VM のサイズを選択する](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. 設定ブレードの **[管理ディスクを使用]** で **[はい]** を選択し、他の設定は既定のままにして、**[OK]** をクリックします。

7. 概要ページで **[OK]** をクリックして、仮想マシンのデプロイを開始します。

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

デプロイが完了したら、仮想マシンとの SSH 接続を作成します。

1. 仮想マシンをクリックします。 VM は、Azure ポータルのホーム画面に表示されます。または、左側のメニューで **[Virtual Machines]** を選択すると表示されます。

2. **[接続]** をクリックします。 仮想マシンへの接続に使用できる SSH 接続文字列が表示されます。

    ![ポータル 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. 次のコマンドを実行して、SSH セッションを作成します。 接続文字列を、Azure ポータルからコピーしたものに置き換えます。

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、リソース グループ、仮想マシン、すべての関連リソースを削除します。 そのためには、仮想マシン ブレードでリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

[可用性が高い仮想マシンの作成のチュートリアル](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[VM デプロイ CLI サンプルを探索する](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

