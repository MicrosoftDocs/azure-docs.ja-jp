---
title: チュートリアル:ハードウェアの初期化 - Azure FXT Edge Filer
description: ハードウェア ノードに接続して、Azure FXT Edge Filer ノードに初期パスワードを設定する方法について説明します。
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 79d908077d3bd66a3ed86759e37574383358881b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218852"
---
# <a name="tutorial-set-hardware-passwords"></a>チュートリアル:ハードウェア パスワードを設定する

Azure FXT Edge Filer ノードに初めて電源を入れたときに、ルート パスワードを設定する必要があります。 ハードウェア ノードに既定のパスワードはありません。

パスワードが設定されてルート ユーザーがサインインするまで、ネットワーク ポートは無効です。

この手順は、ノードを設置してケーブルを接続した後、クラスターの作成を試行する前に行ってください。

このチュートリアルでは、ハードウェア ノードに接続してパスワードを設定する方法について説明します。 また、ノードをセキュリティで保護するために BIOS のセットアップ パスワードを追加する方法についても説明します。

このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
>
> * キーボードとモニターをノードに接続して電源を入れる
> * BIOS のセットアップ パスワードを設定する
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

## <a name="create-a-bios-setup-password"></a>BIOS のセットアップ パスワードを作成する

BIOS のセットアップ パスワードにより、ノードの BIOS 設定が偶発的または不正な変更から保護されます。 このパスワードはクラスターの作成には不要ですが、クラスターのセキュリティ戦略の一環として強くお勧めします。

BIOS のセットアップ パスワードを作成するには:

1. ノードの電源を入れるか再起動し、すぐに F2 キーを押してシステム セットアップ ユーティリティを開きます。

1. **[System Setup Main Menu]\(システム セットアップのメイン メニュー\)** 画面で、 **[System BIOS]\(システム BIOS\)**  >  **[システム セキュリティ]** を選択します。

1. **[Password Status]\(パスワードの状態\)** 設定が **[ロック解除]** になっていることを確認します。

1. **[Setup Password]\(パスワードの設定\)** フィールドを使用して、パスワードを設定します (システム BIOS パスワードを使用する場合は、この画面で設定することもできます)。

1. Esc キーを押して **[System BIOS]\(システム BIOS\)** 画面に戻り、もう一度 Esc キーを押します。 変更を保存するかどうかを確認するメッセージが表示されます。 システムが自動的に再起動しない場合は再起動し、通常の起動画面を表示します。<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>初期パスワードを設定する

Azure FXT Edge Filer ノードは、起動中にさまざまなメッセージをモニターに出力します。 しばらくすると、次のような初期セットアップ画面が表示されます。

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

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

## <a name="next-steps"></a>次のステップ

ノードがクラスターの一部になる準備ができました。 これを使用して Azure FXT Edge Filer クラスターを作成したり、[これを既存のクラスターに追加](fxt-add-nodes.md)したりできます。

> [!div class="nextstepaction"]
> [クラスターの作成](fxt-cluster-create.md)
