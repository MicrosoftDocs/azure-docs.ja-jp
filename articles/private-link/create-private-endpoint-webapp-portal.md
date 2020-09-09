---
title: Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する
description: Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 06/02/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 1b3ac4c79ce92f591e74821a9f355717e4b22ea4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737395"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する (プレビュー)

Azure プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これを使用して非公開で Web アプリに接続することができます。
このクイックスタートでは、プライベート エンドポイントを使用して Web アプリをデプロイし、仮想マシンからこの Web アプリに接続する方法について説明します。

詳細については、[Azure Web アプリでのプライベート エンドポイントの使用][privatenedpointwebapp]に関するページを参照してください。

> [!Note]
>このプレビューは、PremiumV2 の Windows および Linux の Web アプリとエラスティック Premium 関数すべてに対して、パブリック リージョンでご利用いただけます。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="virtual-network-and-virtual-machine"></a>仮想ネットワークと仮想マシン

このセクションでは、仮想ネットワークとサブネットを作成し、プライベート エンドポイントを介して Web アプリにアクセスするために使用される VM をホストします。

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

このセクションでは、仮想ネットワークとサブネットを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択するか、検索ボックスで**仮想ネットワーク**を検索します。

1. **[仮想ネットワークの作成]** の [基本] タブで次の情報を入力または選択します。

   > [!div class="mx-imgBorder"]
   > ![仮想ネットワークの作成][1]

1. **[次へ: IP アドレス>]** をクリックし、次の情報を入力または選択します。

   > [!div class="mx-imgBorder"]
   >![IP アドレスの構成][2]

1. サブネット セクションで **[+ サブネットの追加]** をクリックし、次の情報を入力して、 **[追加]** クリックします

   > [!div class="mx-imgBorder"]
   >![サブネットの追加][3]

1. **[確認と作成]** をクリックします

1. 検証に成功したら、 **[作成]** をクリックします

### <a name="create-virtual-machine"></a>仮想マシンの作成

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します

1. [仮想マシンの作成 - 基本] に次の情報を入力または選択します。

   > [!div class="mx-imgBorder"]
   >![仮想マシンの基本][4]

1. **[次へ: ディスク]** を選択します

   既定の設定のままにします。

1. **[次へ: ネットワーク]** を選択し、次の情報を選択します。

   > [!div class="mx-imgBorder"]
   >![ネットワーク][5]

1. **[確認と作成]** をクリックします

1. "検証に成功しました" メッセージが表示されたら、 **[作成]** をクリックします

## <a name="create-your-web-app-and-private-endpoint"></a>Web アプリとプライベート エンドポイントの作成

このセクションでは、プライベート エンドポイントを使用してプライベート Web アプリを作成します。

> [!Note]
>プライベート エンドポイント機能は、Premium V2 SKU でのみ使用できます。

### <a name="web-app"></a>Web アプリ

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[Web]**  >  **[Web アプリ]** を選択します

1. [Web アプリの作成 - 基本] で、次の情報を入力または選択します。

   > [!div class="mx-imgBorder"]
   >![Web アプリの基本][6]

1. **[確認と作成]** を選択します

1. "検証に成功しました" メッセージが表示されたら、 **[作成]** をクリックします

### <a name="create-the-private-endpoint"></a>プライベート エンドポイントの作成

1. Web アプリのプロパティで **[設定]**  >  **[ネットワーク]** を選択し、 **[Configure your private endpoint connections]\(プライベート エンドポイント接続の構成\)** をクリックします

   > [!div class="mx-imgBorder"]
   >![Web アプリのネットワーク][7]

1. ウィザードで **[+ 追加]** クリックします

   > [!div class="mx-imgBorder"]
   >![Web アプリのプライベート エンドポイント][8]

1. サブスクリプション、VNet、およびサブネットの情報を入力し、 **[OK]** をクリックします

   > [!div class="mx-imgBorder"]
   >![Web アプリのネットワーク][9]

1. プライベート エンドポイントの作成を確認します

   > [!div class="mx-imgBorder"]
   >![確認][10]
   >![プライベート エンドポイントの最後のビュー][11]

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

1. ポータルの検索バーに、「**myVm**」と入力します
1. **[接続]** ボタンを選択します。 [接続] ボタンを選択すると [仮想マシンに接続する] が開くので、 **[RDP]** を選択します

   > [!div class="mx-imgBorder"]
   >![[RDP] ボタン][12]

1. **[RDP ファイルのダウンロード]** をクリックすると、Azure によってリモート デスクトップ プロトコル (.rdp) ファイルが作成され、コンピューターにダウンロードされます

   > [!div class="mx-imgBorder"]
   >![RDP ファイルのダウンロード][13]

1. ダウンロードされた .rdp ファイルを開きます。

- メッセージが表示されたら、[接続] を選択します。
- VM の作成時に指定したユーザー名とパスワードを入力します。

> [!Note]
> 場合によっては、[その他] > [別のアカウントを使用する] を選択して、VM の作成時に入力した資格情報を指定する必要があります。

- [OK] を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、[はい] または [続行] を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。

## <a name="access-web-app-privately-from-the-vm"></a>VM から非公開で Web アプリにアクセスする

このセクションでは、プライベート エンドポイントを使用して、プライベートで Web アプリに接続します。

1. プライベート エンドポイントのプライベート IP を取得します。検索バーに「**プライベート リンク**」と入力し、[プライベート リンク] を選択します

   > [!div class="mx-imgBorder"]
   >![Private Link][14]

1. プライベート リンク センターで、 **[プライベート エンドポイント]** を選択し、すべてのプライベート エンドポイントを一覧表示します

   > [!div class="mx-imgBorder"]
   >![プライベート リンク センター][15]

1. Web アプリとサブネットへのプライベート エンドポイント リンクを選択します

   > [!div class="mx-imgBorder"]
   >![プライベート エンドポイントのプロパティ][16]

1. プライベート エンドポイントのプライベート IP と Web アプリの FQDN (この例では webappdemope.azurewebsites.net 10.10.2.4) をコピーします

1. myVM で、パブリック IP を使用して Web アプリにアクセスできないことを確認します。 ブラウザーを開き、Web アプリ名を貼り付けると、403 アクセス不可エラー ページが表示されます

   > [!div class="mx-imgBorder"]
   >![Forbidden][17]

> [!Important]
> この機能はプレビュー段階なので、DNS エントリを手動で管理する必要があります。

1. ホスト エントリを作成し、ファイル エクスプローラーを開き、hosts ファイルを見つけます

   > [!div class="mx-imgBorder"]
   >![hosts ファイル][18]

1. メモ帳で hosts ファイルを編集し、プライベート IP アドレスと Web アプリのパブリック名を含むエントリを追加します

   > [!div class="mx-imgBorder"]
   >![hosts の内容][19]

1. ファイルを保存します。

1. ブラウザーを開き、Web アプリの URL を入力します

   > [!div class="mx-imgBorder"]
   >![PE が表示された Web サイト][20]

1. プライベート エンドポイント経由で Web アプリにアクセスしています

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライベート エンドポイント、Web アプリ、VM を使い終えたら、リソース グループとそこに含まれるすべてのリソースを削除します。

1. ポータルの上部にある [検索] ボックスに「ready-rg」と入力し、検索結果から ready-rg を選択します。
1. [リソース グループの削除] を選択します。
1. [リソース グループ名を入力してください] に「ready-rg」と入力し、[削除] を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、仮想ネットワーク上の VM、Web アプリ、およびプライベート エンドポイントを作成しました。 インターネットから VM に接続し、プライベート リンクを使用して Web アプリと安全に通信しました。 プライベート エンドポイントの詳細については、「[Azure プライベート エンドポイントとは][privateendpoint]」を参照してください。

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
