---
title: Azure portal で Azure Data Box Gateway を接続、構成、アクティブ化する
description: Data Box Gateway をデプロイする 3 番目のチュートリアルでは、ご利用の仮想デバイスを接続、設定、およびアクティブ化する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96580603"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>チュートリアル:Azure Data Box Gateway を接続、設定、アクティブ化する

## <a name="introduction"></a>はじめに

このチュートリアルでは、ローカル Web UI を使用して、Data Box Gateway デバイスに接続し、このデバイスを設定およびアクティブ化する方法について説明します。 

セットアップとアクティブ化のプロセスは、完了するまでに約 10 分かかることがあります。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想デバイスに接続する
> * 仮想デバイスを設定してアクティブ化する

## <a name="prerequisites"></a>前提条件

Data Box Gateway を構成および設定する前に、以下のことを確認します。

* 「[Hyper-V で Data Box Gateway をプロビジョニングする](data-box-gateway-deploy-provision-hyperv.md)」または「[VMware で Data Box Gateway をプロビジョニングする](data-box-gateway-deploy-provision-vmware.md)」の説明に従って仮想デバイスをプロビジョニングし、その仮想デバイスに接続する URL を取得していること。
* Data Box Gateway デバイスを管理するために作成した Data Box Gateway サービスからのアクティブ化キーを持っていること。 詳細については、[Azure Data Box Gateway のデプロイ準備](data-box-gateway-deploy-prep.md)に関する記事をご覧ください。


## <a name="connect-to-the-local-web-ui-setup"></a>ローカル Web UI 設定に接続する 

1. ブラウザー ウィンドウを開き、次のリンクからデバイスのローカル Web UI にアクセスします。
   
   https:\//ip-address-of-network-interface
   
   前のチュートリアルに記載されている接続 URL を使用します。 Web サイトのセキュリティ証明書に問題があることを示すエラーまたは警告が表示されます。

2. **[この Web ページの閲覧を続ける]** を選択します。 これらの手順は、使用しているブラウザーによって異なることがあります。
   
    ![Web サイトのセキュリティ証明書のエラー メッセージ](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. ご利用の仮想デバイスの Web UI にサインインします。 既定のパスワードは *Password1* です。 
   
    ![ローカル Web UI へのサインイン](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. プロンプトで、デバイスのパスワードを変更します。 新しいパスワードは 8 から 16 文字にする必要があります。 大文字、小文字、数字、および特殊文字のうち 3 種類の文字を含める必要があります。

    ![デバイスのパスワードを変更する](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

これで、ご利用のデバイスの **ダッシュボード** が表示されます。

## <a name="set-up-and-activate-the-virtual-device"></a>仮想デバイスを設定してアクティブ化する
 
ダッシュボードには、仮想デバイスを構成して Data Box Gateway サービスに登録するために必要なさまざまな設定が表示されます。 **デバイス名**、**ネットワーク設定**、**Web プロキシの設定**、**時刻の設定** はオプションです。 必須の設定は、 **[クラウド設定]** のみです。
   
![ローカル Web UI の [ダッシュボード] ページ](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. 左側のウィンドウで **[デバイス名]** を選択し、デバイスのフレンドリ名を入力します。 フレンドリ名は 1 から 15 文字の長さにし、文字、数字、およびハイフンを含める必要があります。 

    ![ローカル Web UI の [デバイス名] ページ](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (省略可能) 左側のウィンドウで **[ネットワーク設定]** を選択し、設定を構成します。 仮想デバイスで、基になる仮想マシンで構成した数に応じて、少なくとも 1 つまたは複数のネットワーク インターフェイスが表示されます。 ネットワーク インターフェイスが 1 つ有効になっている仮想デバイスの **[ネットワーク設定]** ページは、次のように表示されます。
    
    ![ローカル Web UI の [ネットワーク設定] ページ](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    ネットワーク設定を構成するときは、次のことに注意してください。

    - ご利用の環境内で DHCP が有効になっている場合は、ネットワーク インターフェイスが自動的に構成されます。 IP アドレス、サブネット、ゲートウェイ、DNS は自動的に割り当てられます。
    - DHCP が有効になっていない場合は、必要に応じて、静的 IP アドレスを割り当てることができます。
    - 使用するネットワーク インターフェイスは、IPv4 として構成できます。

     >[!NOTE] 
     > デバイスに接続する別の IP アドレスがない限り、ネットワーク インターフェイスのローカル IP アドレスを静的から DHCP に切り替えないことをお勧めします。 あるネットワーク インターフェイスを使用していて、DHCP に切り替えた場合、DHCP アドレスを判別する方法がありません。 DHCP アドレスに変更する場合は、デバイスがサービスに登録されるまで待機してから変更してください。 その後、サービスについて Azure portal の **[デバイスのプロパティ]** にすべてのアダプターの IP が表示されます。

3. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、このページでのみ構成できます。
   
   ![ローカル Web UI の [Web proxy settings] (Web プロキシ設定) ページ](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   **[Web プロキシ]** ページで、次の操作を行います。
   
   1. **[Web プロキシ URL]**  ボックスに、`http://&lt;host-IP address or FQDN&gt;:Port number` という形式の URL を入力します。 HTTPS URL はサポートされていません。
   2. **[認証]** で、 **[なし]** または **[NTLM]** を選択します。
   3. 認証を使用している場合は、**ユーザー名** と **パスワード** を入力します。
   4. 構成された Web プロキシ設定を検証して適用するには、 **[適用]** を選択します。

   > [!NOTE]
   > プロキシ自動構成 (PAC) ファイルはサポートされていません。 PAC ファイルは、Web ブラウザーやその他のユーザー エージェントが、特定の URL をフェッチするための適切なプロキシ サーバー (アクセス方法) を自動的に選択する方法を定義します。
   > プロキシの証明書は信頼されていないため、すべてのトラフィックをインターセプトして読み取る (その後、独自の証明書を使用してすべてに再署名する) プロキシは互換性がありません。
   > 通常、透過プロキシは、Azure Data Box Gateway で適切に動作します。

4. (省略可能) 左側のウィンドウで **[時刻の設定]** を選択し、タイム ゾーンとデバイスのプライマリおよびセカンダリ NTP サーバーを構成します。 

    デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
    
    ![ローカル Web UI の [時刻の設定] ページ](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    **[時刻の設定]** ページで、次の操作を行います。
    
    1. **[タイム ゾーン]** ドロップダウン リストで、デバイスがデプロイされる地理的な場所に対応するタイム ゾーンを選択します。
        デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。

    2. デバイスの **[プライマリ NTP サーバー]** を指定するか、既定値の `time.windows.com` をそのまま使用します。   
        データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。

    3. オプションで、 **[セカンダリ NTP サーバー]** ボックスにデバイスのセカンダリ サーバーを入力します。

    4. 構成された時刻の設定を検証および適用するには、 **[適用]** を選択します。

6. 左側のウィンドウで **[クラウド設定]** を選択し、Azure portal の Data Box Gateway サービスでデバイスをアクティブ化します。
    
    1. **[アクティブ化キー]** ボックスに、Data Box Gateway の [[アクティブ化キーの取得]](data-box-gateway-deploy-prep.md#get-the-activation-key) で取得した **アクティブ化キー** を入力します。

    2. **[アクティブ化]** を選びます。
       
         ![ローカル Web UI の [クラウド設定] ページ](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. デバイスがアクティブ化され、緊急更新プログラム (使用可能な場合) が自動的に適用されます。 その影響に関する通知が表示されます。 Azure portal で更新の進行状況を監視します。

        ![ローカル Web UI の [クラウド設定] ページ 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **このダイアログには、コピーして安全な場所に保存する必要がある回復キーも含まれています。このキーは、デバイスが起動できない場合にデータを復旧するために使用されます。**


    4. 更新プログラムが正常に完了した後、数分待つ必要があります。 更新後が完了したら、デバイスにサインインします。 **[クラウド設定]** ページが更新され、デバイスが正常にアクティブ化されたことが示されます。

        ![更新されたローカル Web UI の [クラウド設定] ページ](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

デバイスの設定が完了しました。 これで、デバイスで共有を追加できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 仮想デバイスに接続する
> * 仮想デバイスを設定してアクティブ化する

Data Box Gateway でデータを転送する方法を学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [Data Box Gateway を使用してデータを転送する](./data-box-gateway-deploy-add-shares.md)します。
