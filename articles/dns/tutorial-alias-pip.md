---
title: チュートリアル:Azure パブリック IP アドレスを参照する Azure DNS エイリアス レコードを作成する
description: このチュートリアルでは、Azure パブリック IP アドレスを参照する Azure DNS エイリアス レコードを構成する方法を示します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d3017d09e94040d16950598dad360fe32930c16b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80985441"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>チュートリアル:Azure パブリック IP アドレスを参照するエイリアス レコードを構成する 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネットワーク インフラストラクチャを作成する。
> * パブリック IP を使用して Web サーバー仮想マシンを作成する。
> * パブリック IP を指すエイリアス レコードを作成する。
> * エイリアス レコードをテストする。


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
テスト対象の Azure DNS でホストできる利用可能なドメイン名が必要です。 このドメインに対するフル コントロールが必要となります。 フル コントロールには、このドメインのネーム サーバー (NS) レコードを設定する権限が含まれます。

Azure DNS 内でドメインをホストする手順については、「[チュートリアル:Azure DNS でドメインをホストする](dns-delegate-domain-azure-dns.md)」を参照してください。

このチュートリアルで使用するドメインの例は contoso.com ですが、独自のドメイン名を使用してください。

## <a name="create-the-network-infrastructure"></a>ネットワーク インフラストラクチャを作成する
まず、Web サーバーを配置する仮想ネットワークとサブネットを作成します。
1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. ポータルの左上にある **[リソースの作成]** を選択します。 検索ボックスに「*resource group*」と入力し、**RG-DNS-Alias-pip** という名前のリソース グループを作成します。
3. **[+ リソースの作成]**  >  **[ネットワーク]**  >  **[仮想ネットワーク]** の順に選択します。
4. **VNet-Server** という名前の仮想ネットワークを作成します。 それを **RG-DNS-Alias-pip** リソース グループに配置し、サブネットに **SN-Web** という名前を付けます。

## <a name="create-a-web-server-virtual-machine"></a>Web サーバー仮想マシンを作成する
1. **[リソースの作成]**  >  **[Windows Server 2016 VM]** を選択します。
2. 名前として「**Web-01**」と入力し、この VM を **RG-DNS-Alias-TM** リソース グループに配置します。 ユーザー名とパスワードを入力し、 **[OK]** を選択します。
3. **[サイズ]** では、8 GB RAM の SKU を選択します。
4. **[設定]** で、**VNet-Server** 仮想ネットワークと **SN-Web** サブネットを選択します。 パブリック受信ポートでは、 **[HTTP]**  >  **[HTTPS]**  >  **[RDP (3389)]** を選択し、 **[OK]** を選択します。
5. **[概要]** ページで、 **[作成]** を選択します。

この手順は、完了するまでに数分かかります。 仮想マシンには、Web-01-ip という基本的な動的パブリック IP を持つ NIC が接続されます。 このパブリック IP は、仮想マシンが再起動されるたびに変わります。

### <a name="install-iis"></a>IIS のインストール

**Web-01** にIIS をインストールする

1. **Web-01** に接続してサインインします。
2. **[サーバー マネージャー]** ダッシュボードの **[役割と機能の追加]** を選択します。
3. **[次へ]** を 3 回選択します。 **[サーバーの役割]** ページで、 **[Web サーバー (IIS)]** を選択します。
4. **[機能の追加]** を選択し、 **[次へ]** を選択します。
5. **[次へ]** を 4 回選択し、 **[インストール]** を選択します。 この手順は、完了するまでに数分かかります。
6. インストールが完了したら、 **[閉じる]** を選択します。
7. Web ブラウザーを開きます。 **localhost** を参照して既定の IIS Web ページが表示されることを確認します。

## <a name="create-an-alias-record"></a>エイリアス レコードを作成する

パブリック IP アドレスを指すエイリアス レコードを作成します。

1. Azure DNS ゾーンを選択し、ゾーンを開きます。
2. **[レコード セット]** を選択します。
3. **[名前]** ボックスで **[web01]** を選択します。
4. **[Type]\(種類\)** は **A** レコードのままにします。
5. **[Alias Record Set]\(エイリアス レコード セット\)** チェック ボックスをオンにします。
6. **[Azure サービスの選択]** を選択し、**Web-01-ip** パブリック IP アドレスを選択します。

## <a name="test-the-alias-record"></a>エイリアス レコードをテストする

1. **RG-DNS-Alias-pip** リソース グループで、**Web-01** 仮想マシンを選択します。 パブリック IP アドレスをメモします。
1. Web ブラウザーから、Web01-01 仮想マシンの完全修飾ドメイン名を参照します (例: **web01.contoso.com**)。 この時点では、IIS の既定の Web ページが表示されます。
2. Web ブラウザーを閉じます。
3. **Web 01** 仮想マシンを停止し、再起動します。
4. 仮想マシンの再起動後、仮想マシンの新しいパブリック IP アドレスをメモします。
5. 新しいブラウザーを開きます。 もう一度、Web01-01 仮想マシンの完全修飾ドメイン名を参照します (例: **web01.contoso.com**)。

この手順は成功します。エイリアス レコードを使用して、標準の A レコードではなくパブリック IP アドレス リソースを指したからです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースが不要になったら、**RG-DNS-Alias-pip** リソース グループは削除してください。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、エイリアス レコードを作成し、Azure パブリック IP アドレスを参照しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
