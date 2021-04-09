---
title: 'チュートリアル: ドメインの頂点名をサポートするためエイリアス レコードを作成する - Traffic Manager'
titleSuffix: Azure DNS
description: このチュートリアルでは、Traffic Manager でのドメイン頂点名の使用をサポートするために Azure DNS エイリアス レコードを構成する方法を示します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: 4bdfc950cc1277809811dc2c548a57cc2138a8e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "77149951"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>チュートリアル: Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する 

Azure Traffic Manager プロファイルを参照するためのドメイン名の頂点に対するエイリアス レコードを作成することができます  一例として contoso.com があります。 サービスのリダイレクトを使用する代わりに、ゾーンから直接 Traffic Manager プロファイルを参照するように Azure DNS を構成できます。 


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ホスト VM とネットワーク インフラストラクチャを作成する。
> * Traffic Manager プロファイルを作成する。
> * エイリアス レコードを作成する。
> * エイリアス レコードをテストする。


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
テスト対象の Azure DNS でホストできる利用可能なドメイン名が必要です。 このドメインに対するフル コントロールが必要となります。 フル コントロールには、このドメインのネーム サーバー (NS) レコードを設定する権限が含まれます。

Azure DNS にドメインをホストする手順については、「[チュートリアル: Azure DNS でドメインをホストする](dns-delegate-domain-azure-dns.md)」を参照してください。

このチュートリアルで使用するドメインの例は contoso.com ですが、独自のドメイン名を使用してください。

## <a name="create-the-network-infrastructure"></a>ネットワーク インフラストラクチャを作成する
まず、Web サーバーを配置する仮想ネットワークとサブネットを作成します。
1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. ポータルの左上にある **[リソースの作成]** を選択します。 検索ボックスに「*resource group*」と入力し、**RG-DNS-Alias-TM** という名前のリソース グループを作成します。
3. **[+ リソースの作成]**  >  **[ネットワーク]**  >  **[仮想ネットワーク]** の順に選択します。
4. **VNet-Servers** という名前の仮想ネットワークを作成します。 それを **RG-DNS-Alias-TM** リソース グループに配置し、サブネットに **SN-Web** という名前を付けます。

## <a name="create-two-web-server-virtual-machines"></a>Web サーバー仮想マシンを 2 つ作成する
1. **[リソースの作成]**  >  **[Windows Server 2016 VM]** を選択します。
2. 名前として「**Web-01**」と入力し、この VM を **RG-DNS-Alias-TM** リソース グループに配置します。 ユーザー名とパスワードを入力し、**[OK]** を選択します。
3. **[サイズ]** では、8 GB RAM の SKU を選択します。
4. **[設定]** で、**VNet-Server** 仮想ネットワークと **SN-Web** サブネットを選択します。
5. **[パブリック IP アドレス]** を選択します。 **[割り当て]** で **[静的]** を選択し、**[OK]** を選択します。
6. パブリック受信ポートでは、 **[HTTP]**  >  **[HTTPS]**  >  **[RDP (3389)]** を選択し、 **[OK]** を選択します。
7. **[概要]** ページで、 **[作成]** を選択します。 この手順は、完了するまでに数分かかります。

この手順を繰り返して、**Web-02** という名前の別の仮想マシンを作成します。

### <a name="add-a-dns-label"></a>DNS ラベルを追加する
Traffic Manager でパブリック IP アドレスを使用するには、DNS ラベルが必要です。
1. **RG-DNS-Alias-TM** リソース グループで、**Web-01-ip** パブリック IP アドレスを選択します。
2. **[設定]** の下で **[構成]** を選択します。
3. DNS 名ラベルのテキスト ボックスに、「**web01pip**」と入力します。
4. **[保存]** を選択します。

**Web-02-ip** パブリック IP アドレスに対してこの手順を繰り返し、DNS 名ラベルとして **web02pip** を使用します。

### <a name="install-iis"></a>IIS のインストール

**Web-01** と **Web-02** の両方に、IIS をインストールします。

1. **Web-01** に接続してサインインします。
2. **[サーバー マネージャー]** ダッシュボードの **[役割と機能の追加]** を選択します。
3. **[次へ]** を 3 回選択します。 **[サーバーの役割]** ページで、 **[Web サーバー (IIS)]** を選択します。
4. **[機能の追加]** を選択し、**[次へ]** を選択します。
5. **[次へ]** を 4 回選択します。 その後、 **[インストール]** を選択します。 この手順は、完了するまでに数分かかります。
6. インストールが完了したら、**[閉じる]** を選択します。
7. Web ブラウザーを開きます。 **localhost** を参照して既定の IIS Web ページが表示されることを確認します。

この手順を繰り返して、**Web-02** に IIS をインストールします。


## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

1. **RG-DNS-Alias-TM** リソース グループを開き、**Web-01-ip** パブリック IP アドレスを選択します。 後で使うので IP アドレスを書き留めておきます。 **Web-02-ip** パブリック IP アドレスについても、この手順を繰り返します。
1. **[リソースの作成]**  >  **[ネットワーク]**  >  **[Traffic Manager プロファイル]** の順に選択します。
2. 名前として、「**TM-alias-test**」と入力します。 それを **RG-DNS-Alias-TM** リソース グループに配置します。
3. **［作成］** を選択します
4. デプロイが完了したら、**[リソースに移動]** を選択します。
5. Traffic Manager プロファイルのページの **[設定]** で、**[エンドポイント]** を選択します。
6. **[追加]** を選択します。
7. **[種類]** で **[外部エンドポイント]** を選択し、**[名前]** に「**EP-Web01**」と入力します。
8. **[完全修飾ドメイン名 (FQDN) または IP]** テキスト ボックスに、前に書き留めた **Web-01-ip** の IP アドレスを入力します。
9. 他のリソースと同じ **[場所]** を選択して、**[OK]** を選択します。

この手順を繰り返して、**Web-02** エンドポイントを追加します。前に書き留めた **Web-02-ip** の IP アドレスを使用します。

## <a name="create-an-alias-record"></a>エイリアス レコードを作成する

Traffic Manager プロファイルを指すエイリアス レコードを作成します。

1. Azure DNS ゾーンを選択し、ゾーンを開きます。
2. **[レコード セット]** を選択します。
3. **[名前]** テキスト ボックスは、ドメイン名の頂点を表す空のままにします  一例として contoso.com があります。
4. **[Type]\(種類\)** は **A** レコードのままにします。
5. **[Alias Record Set]\(エイリアス レコード セット\)** チェック ボックスをオンにします。
6. **[Choose Azure service]\(Azure サービスの選択\)** を選択して、**TM-alias-test** Traffic Manager プロファイルを選択します。

## <a name="test-the-alias-record"></a>エイリアス レコードをテストする

1. Web ブラウザーから、ドメイン名の頂点を参照します  一例として contoso.com があります。 IIS の既定の Web ページが表示されます。 Web ブラウザーを閉じます。
2. **Web-01** 仮想マシンをシャットダウンします。 シャットダウンが完了するまで数分待ちます。
3. 新しい Web ブラウザーを開いて、ドメイン名の頂点を再度参照します。
4. Traffic Manager が状況を処理して、トラフィックを **Web-02** に転送するため、再び既定の IIS ページが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースが不要になったら、**RG-DNS-Alias-TM** リソース グループは削除してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、頂点のドメイン名を使用して Traffic Manager プロファイルを参照するためのエイリアス レコードを作成しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ゾーンの頂点にある負荷分散された Web アプリをホストする](./dns-alias-appservice.md)
