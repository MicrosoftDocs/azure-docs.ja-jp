---
title: Azure VM 上の Service Fabric クラスターのインフラストラクチャを作成するチュートリアル - Azure Service Fabric | Microsoft Docs
description: このチュートリアルでは、Service Fabric クラスターを実行するための Azure VM インフラストラクチャを設定する方法を学習します。
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: d9db71a1b64ea6bf2dc73500160ce8e5e6022ef6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385033"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>チュートリアル:Service Fabric クラスターをホストする Azure VM インフラストラクチャを作成する

Service Fabric で採用されている "すべての OS、すべてのクラウド" のアプローチの一環として、Service Fabric スタンドアロン クラスターには、独自の環境を選んでクラスターを作成する選択肢が用意されています。 このチュートリアル シリーズでは、Azure VM をホストとするスタンドアロン クラスターを作成し、そこにアプリケーションをインストールします。

このチュートリアルは、シリーズの第 1 部です。 この記事では、Service Fabric のスタンドアロン クラスターをホストするために必要な Azure VM リソースを生成します。 後続の記事では、Service Fabric スタンドアロン スイートをインストールし、クラスターにサンプル アプリケーションをインストールして、最後にクラスターをクリーンアップする必要があります。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure VM インスタンスのセットを作成する
> * セキュリティ グループを変更する
> * いずれかのインスタンスにログインする
> * Service Fabric 用にインスタンスを準備する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションが必要です。  まだアカウントを持っていない場合は、[Azure portal](https://portal.azure.com) に移動して作成してください。

## <a name="create-azure-virtual-machine-instances"></a>Azure 仮想マシン インスタンスを作成する

1. Azure portal にサインインして **[仮想マシン]** を選択します ([仮想マシン (クラシック)] ではありません)。

   ![Azure portal の VM][az-console]

2. **[追加]** ボタンを選択すると、 **[仮想マシンの作成]** フォームが開きます。

3. **[基本]** タブで、目的のサブスクリプションとリソース グループを選択してください (新しいリソース グループの使用が推奨されます)。

4. **イメージ**の種類を **[Windows Server 2016 Datacenter]** に変更します。 
 
5. インスタンスの**サイズ**を **[Standard DS2 v2]** に変更します。 管理者の**ユーザー名**と**パスワード**を設定し、それらをメモします。

6. ここでは、**受信ポートの規則**はブロックしたままにしておいてください。これらは次のセクションで構成します。

7. **[ネットワーキング]** タブで、新しい**仮想ネットワーク**を作成し、その名前をメモしておきます。

8. 次に、 **[NIC ネットワーク セキュリティ グループ]** を **[高度]** に設定します。 新しいセキュリティ グループを作成し、その名前をメモします。また、次の規則を作成して、任意のソースからの TCP トラフィックを許可します。

   ![sf-inbound][sf-inbound]

   * ポート `3389` は RDP および ICMP 用です (基本的な接続)。
   * ポート `19000-19003` は Service Fabric 用です。
   * ポート `19080-19081` は Service Fabric 用です。
   * ポート `8080` は Web ブラウザーの要求用です。

   > [!TIP]
   > Service Fabric で仮想マシンも一緒に接続するには、インフラストラクチャをホストしている各 VM が同じ資格情報を持っている必要があります。  資格情報を一致させるには、2 つの一般的な方法があります。すべての仮想マシンを同じドメインに参加させる方法と、各 VM で同じ管理者パスワードを設定する方法です。 さいわい、Azure では同じ**仮想ネットワーク**上のすべての仮想マシンは容易に接続できます。そのため、すべてのインスタンスを同じネットワーク上に用意するようにします。

9. 別の規則を追加します。 ソースを **[サービス タグ]** に設定し、ソースのサービス タグを **[VirtualNetwork]** に設定します。 Service Fabric では、クラスター内の通信のために次のポートをオープンにしておく必要があります: 135、137 から 139、445、20001 から 20031、20606 から 20861。

   ![vnet-inbound][vnet-inbound]

10. 残りのオプションは、既定の状態でかまいません。 必要に応じてそれらを確認してから、仮想マシンを起動します。

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Service Fabric クラスター用のインスタンスをさらに作成する

さらに 2 つの**仮想マシン**を起動します。前のセクションで説明されているのと同じ設定を使用するようにしてください。 特に、管理者のユーザー名とパスワードは前と同じものを使用します。 **仮想ネットワーク**と **NIC ネットワーク セキュリティ グループ**は、再作成する必要がありません。ドロップダウン メニューから、既に作成してあるものを選択します。 各インスタンスがデプロイされるまでには、数分かかる場合があります。

## <a name="connect-to-your-instances"></a>インスタンスに接続する

1. **[仮想マシン]** セクションで、自分のインスタンスを 1 つ選択します。

2. **[概要]** タブで、"*プライベート*" IP アドレスをメモします。 次に、 **[接続]** をクリックします。

3. **[RDP]** タブで、先ほど明示的に開いたポート 3389 と、パブリック IP アドレスを使用していることに注意してください。 RDP ファイルをダウンロードします。
 
4. RDP ファイルを開きます。求められたら、VM の設定時に指定したユーザー名とパスワードを入力します。

5. インスタンスに接続したら、リモート レジストリが実行されていたことを検証し、SMB を有効にして、SMB およびリモート レジストリに必要なポートを開く必要があります。

   これは、SMB を有効にするための PowerShell コマンドです。

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. ファイアウォールでポートを開くための PowerShell コマンドは、次のとおりです。

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. 自分の他のインスタンスでこのプロセスを繰り返し、そのたびにプライベート IP アドレスをメモします。

## <a name="verify-your-settings"></a>設定を検証する

1. 基本的な接続を検証するには、RDP を使用していずれかの VM に接続します。

2. その VM 内で**コマンド プロンプト**を開いて、ping コマンドを使用して一方から他方の VM に接続します。下の IP アドレスは、先ほどメモしたプライベート IP アドレスのいずれか (既に接続している VM の IP ではないもの) に置き換えてください。

   ```
   ping 172.31.20.163
   ```

   `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` のような出力が 4 回繰り返された場合、インスタンス間の接続は機能しています。

3. 次に、以下のコマンドを使用して、SMB 共有が機能することを検証します。

   ```
   net use * \\172.31.20.163\c$
   ```

   出力として、`Drive Z: is now connected to \\172.31.20.163\c$.` が返されるはずです。


   これで、お客様のインスタンスは Service Fabric 用に正しく準備されました。

## <a name="next-steps"></a>次の手順

シリーズのパート 1 では、3 つの Azure VM インスタンスを起動し、Service Fabric インストール用に構成する方法について学習しました。

> [!div class="checklist"]
> * Azure VM インスタンスのセットを作成する
> * セキュリティ グループを変更する
> * いずれかのインスタンスにログインする
> * Service Fabric 用にインスタンスを準備する

クラスターの Service Fabric を構成するには、シリーズのパート 2 に進んでください。

> [!div class="nextstepaction"]
> [Service Fabric をインストールする](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
