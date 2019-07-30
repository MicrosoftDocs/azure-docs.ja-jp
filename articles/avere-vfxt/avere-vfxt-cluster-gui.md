---
title: Avere vFXT のコントロール パネルにアクセスする - Azure
description: vFXT クラスターとブラウザー ベースの Avere Control Panel に接続して Avere vFXT を構成する方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: v-erkell
ms.openlocfilehash: 830be92d37f304598cca05c3ac80973158c38a59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439986"
---
# <a name="access-the-vfxt-cluster"></a>vFXT クラスターへのアクセス

設定を変更し、Avere vFXT クラスターを監視するには、Avere Control Panel を使用します。 Avere Control Panel は、クラスターに対するブラウザーベースのグラフィカル インターフェイスです。

vFXT クラスターはプライベート仮想ネットワーク内にあるため、クラスターの管理 IP アドレスに到達するには、SSH トンネルを作成するか、別の方法を使用する必要があります。 基本的な手順は 2 つあります。 

1. ワークステーションとプライベート vnet 間に接続を作成する 
1. Web ブラウザーでクラスターのコントロール パネルを読み込む 

> [!NOTE] 
> この記事では、クラスター コントローラー、またはクラスターの仮想ネットワーク内にある別の VM にパブリック IP アドレスを設定していることを前提としています。 この記事では、その VM をホストとして使用してクラスターにアクセスする方法について説明します。 vnet アクセスのために VPN または ExpressRoute を使用している場合は、[Avere Control Panel への接続](#connect-to-the-avere-control-panel-in-a-browser)に関するセクションに進んでください。

接続する前に、クラスター コントローラーの作成時に使用した SSH 公開キーと秘密キーのペアがローカル コンピューターにインストールされていることを確認します。 ヘルプが必要な場合は、[Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) または [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) の SSH キーのドキュメントをお読みください。 (パブリック キーの代わりにパスワードを使用した場合は、接続するときにパスワードの入力を求められます。) 

## <a name="create-an-ssh-tunnel"></a>SSH トンネルを作成する 

SSH トンネルは、Linux ベースまたは Windows 10 クライアント システムのコマンド ラインから作成できます。 

SSH トンネリング コマンドを次の形式で使用します。 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

このコマンドでは、クラスター コントローラーの IP アドレスを介してクラスターの管理 IP アドレスに接続します。

例:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

SSH 公開キーを使用してクラスターを作成し、一致するキーがクライアント システムにインストールされている場合、認証は自動的に行われます。 パスワードを使用した場合は、それを入力するよう求められます。

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>ブラウザーで Avere Control Panel に接続する

この手順では、Web ブラウザーを使用して、vFXT クラスター上で実行されている構成ユーティリティに接続します。

* SSH トンネル接続を行うには、Web ブラウザーを開いて `https://127.0.0.1:8443` に移動します。 

  トンネルを作成したときのクラスターの IP アドレスに接続されるため、必要なのはローカル ホストの IP アドレスをブラウザーで使用することだけです。 8443 以外のローカル ポートを使用した場合は、そのポート番号を使用してください。

* VPN または ExpressRoute を使用してクラスターにアクセスする場合は、ブラウザーでクラスター管理 IP アドレスに移動します。 例: ``https://203.0.113.51``

ブラウザーによっては、 **[Advanced]\(詳細設定\)** をクリックして、そのページへのアクセスが安全であることを確認する必要があります。

クラスターの作成時に指定した、ユーザー名 `admin` と管理パスワードを入力します。

![ユーザー名 'admin' とパスワードが入力された Avere のサインイン ページのスクリーンショット](media/avere-vfxt-gui-login.png)

**[Login]\(ログイン\)** をクリックするか、キーボードの Enter キーを押します。

## <a name="next-steps"></a>次の手順

これでクラスターにアクセスし、[サポート](avere-vfxt-enable-support.md)を有効にすることができるようになりました。
