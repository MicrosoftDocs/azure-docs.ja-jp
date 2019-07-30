---
title: ハードウェアの初期化 - Microsoft Azure FXT Edge Filer
description: Azure FXT Edge Filer ノードに初期パスワードを設定する方法
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451763"
---
# <a name="tutorial-set-hardware-passwords"></a>チュートリアル:ハードウェア パスワードを設定する

Azure FXT Edge Filer ノードに初めて電源を入れたときに、ルート パスワードを設定する必要があります。 ハードウェア ノードに既定のパスワードはありません。 

パスワードが設定されてルート ユーザーがサインインするまで、ネットワーク ポートは無効です。

この手順は、ノードを設置してケーブルを接続した後、クラスターの作成を試行する前に行ってください。 

このチュートリアルでは、ハードウェア ノードに接続してパスワードを設定する方法について説明します。 

このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * キーボードとモニターをノードに接続して電源を入れる
> * このノードの iDRAC ポートとルート ユーザーのパスワードを設定する
> * root としてサインインする 

クラスターで使用するノードごとにこの手順を繰り返します。 

このチュートリアルの所要時間は約 15 分です。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、以下の手順を完了してください。 

* Azure FXT Edge Filer ノードを機器ラックに[設置](fxt-install.md)し、[前のチュートリアル](fxt-network-power.md)に記載されているように電源ケーブルを取り付けてネットワークに接続します。 
* ハードウェア ノードに取り付け可能な USB 接続のキーボードと VGA 接続のモニターを用意します。 (ノードのシリアル ポートはパスワードを設定するまで非アクティブです。)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>キーボードとモニターをノードに接続する

モニターとキーボードを Azure FXT Edge Filer ノードに物理的に接続します。 

* モニターを VGA ポートに接続します。
* キーボードをいずれかの USB ポートに接続します。 

この参照図を使用して、シャーシ背面のポートを見つけてください。 

> [!NOTE]
> シリアル ポートはパスワードを設定するまで非アクティブです。 

![シリアル ポート、VGA ポート、USB ポートを示す Azure FXT Edge Filer の背面図](media/fxt-back-serial-vga-usb.png)

1 つ以上のノードを同じ周辺機器に接続する場合は、KVM スイッチを使用できます。 

前面の電源ボタンを押して、ノードの電源を入れます。 

![Azure FXT Edge Filer の前面図 - 右上付近に円形の電源ボタンが示されています](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>初期パスワードを設定する 

Azure FXT Edge Filer ノードは、起動中にさまざまなメッセージをモニターに出力します。 しばらくすると、次のような初期セットアップ画面が表示されます。

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

入力したパスワードは、以下の 2 つの処理に使用されます。 

* この Azure FXT Edge Filer ノードの一時的なルート パスワードになります。 

  このパスワードは、このノードを使用してクラスターを作成したり、このノードをクラスターに追加したりするときに変更されます。 クラスター管理パスワード (ユーザー ``admin`` に関連付けられているもの) は、クラスター内のすべてのノードのルート パスワードにもなります。

* IDRAC/IPMI ハードウェア管理ポート用の長期的なパスワードになります。

  ハードウェアの問題のトラブルシューティングを後で行うために IPMI でサインインする必要がある場合に備えて、パスワードを覚えておいてください。

パスワードを入力し、確認します。 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

パスワードを入力すると、システムの起動が続行されます。 完了すると、``login:`` プロンプトが表示されます。 

## <a name="sign-in-as-root"></a>root としてサインインする

設定したパスワードを使用して、``root`` としてサインインします。 

```
login: root
Password:**********
```

root としてサインインすると、ネットワーク ポートがアクティブになり、DHCP サーバーに接続して IP アドレスを要求します。 

## <a name="next-steps"></a>次の手順

ノードがクラスターの一部になる準備ができました。 これを使用して Azure FXT Edge Filer クラスターを作成したり、[これを既存のクラスターに追加](fxt-add-nodes.md)したりできます。 

> [!div class="nextstepaction"]
> [クラスターの作成](fxt-cluster-create.md)
