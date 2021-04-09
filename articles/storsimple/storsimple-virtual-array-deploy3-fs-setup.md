---
title: StorSimple Virtual Array をファイル サーバーとして設定する | Microsoft Docs
description: StorSimple Virtual Array のデプロイの 3 番目のチュートリアルでは、ファイル サーバーとして仮想デバイスを設定する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 244fdbf7cb723fe85e0987d176a13242f0bff064
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005930"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>StorSimple Virtual Array をデプロイする - Azure Portal を介してファイル サーバーとしてセットアップする
![仮想アレイをデプロイするために必要な手順を示す図。 3 番目の手順の最初の部分は「ファイル サーバーとして設定」で、強調表示されています。](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>はじめに

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

この記事では、初期セットアップ、StorSimple ファイル サーバーの登録、デバイス セットアップ、および SMB 共有の作成と接続の方法について説明します。 仮想アレイをファイル サーバーまたは iSCSI サーバーとして完全にデプロイするために必要なデプロイ チュートリアル シリーズの最後の記事です。

セットアップと構成のプロセスは、完了するまでに約 10 分かかることがあります。 この記事に記載されている情報は、StorSimple Virtual Array のデプロイにのみ適用されます。 StorSimple 8000 シリーズ デバイスのデプロイについては、[Update 2 を実行している StorSimple 8000 シリーズ デバイスのデプロイ](./storsimple-8000-deployment-walkthrough-u2.md)に関するページをご覧ください。

## <a name="setup-prerequisites"></a>セットアップの前提条件
StorSimple Virtual Array を構成およびセットアップする前に、以下のことを確認します。

* 「[Hyper-V で StorSimple Virtual Array をプロビジョニングする](storsimple-virtual-array-deploy2-provision-hyperv.md)」または「[VMware で StorSimple Virtual Array をプロビジョニングする](storsimple-virtual-array-deploy2-provision-vmware.md)」の説明に従って、仮想アレイをプロビジョニングし、そのアレイに接続していること。
* StorSimple Virtual Array を管理するために作成した、StorSimple デバイス マネージャー サービスからのサービス登録キーがあること。 詳細については、「[手順 2: サービス登録キーを取得する](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)」を参照してください。
* 既存の StorSimple デバイス マネージャー サービスに登録する 2 番目以降の仮想アレイの場合は、サービス データ暗号化キーがあるはずです。 このキーは、最初のデバイスがこのサービスに正常に登録されたときに生成されています。 このキーを紛失した場合は、StorSimple Virtual Array の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 」をご覧ください。

## <a name="step-by-step-setup"></a>セットアップの手順
StorSimple Virtual Array をセットアップして構成するには、次の手順を実行します。

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>手順 1:ローカル Web UI の設定を完了し、デバイスを登録する
#### <a name="to-complete-the-setup-and-register-the-device"></a>セットアップを完了し、デバイスを登録するには
1. ブラウザー ウィンドウを開き、ローカル Web UI に接続します。 型:
   
   `https://<ip-address of network interface>`
   
   前の手順に記載されている接続 URL を使用します。 Web サイトのセキュリティ証明書に問題があることを示すエラーが表示されます。 **[この Web ページの閲覧を続ける]** をクリックして、この Web ページに進みます。
   
   ![セキュリティ証明書に問題があることを示すブラウザー ウィンドウのスクリーンショット。 2 つのリンクが表示されます。1 つは Web サイト用で、もう 1 つはユーザーのホームページ用です。](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. 仮想アレイの Web UI に **StorSimpleAdmin** としてサインインします。 [Hyper-V での StorSimple Virtual Array のプロビジョニング](storsimple-virtual-array-deploy2-provision-hyperv.md)に関する記事または[VMware での StorSimple Virtual Array のプロビジョニング](storsimple-virtual-array-deploy2-provision-vmware.md)に関する記事の手順 3: 仮想アレイの起動に関するページで変更したデバイス管理者のパスワードを入力します。
   
   ![StorSimple のサインイン ページのスクリーンショット。 StorSimpleAdmin ユーザー名が表示され、パスワード入力ボックスには不確定の文字が入力されています。](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. **[ホーム]** ページが表示されます。 このページは、仮想アレイを構成し、StorSimple デバイス マネージャー サービスに登録するうえで必要なさまざまな設定を掲載しています。 **ネットワーク設定**、**Web プロキシの設定**、**時刻の設定** はオプションです。 必須の設定は、**デバイスの設定** と **クラウドの設定** のみです。
   
   ![ホーム ページのスクリーンショット。 デバイスが構成されていないことを示すテキストが表示されています。 さまざまな要素を設定するためのリンクが表示されています。](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. **[ネットワークの設定]** ページの **[ネットワーク インターフェイス]** で、DATA 0 が自動的に構成されます。 各ネットワーク インターフェイスは、既定で IP アドレスを自動的に取得するように設定されます (DHCP)。 そのため、IP アドレス、サブネット、およびゲートウェイは自動的に割り当てられます (IPv4 と IPv6 の両方に対して)。
   
   ![[ネットワークの設定] ページのスクリーンショット。さまざまなバージョンのインターネット プロトコル用に設定された IP アドレスが示されています。](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   デバイスのプロビジョニング中に複数のネットワーク インターフェイスを追加した場合は、それらをここで構成できます。 ネットワーク インターフェイスは IPv4 だけで構成するか、IPv4 と IPv6 の両方で構成することができます。 IPv6 だけで構成することはできません。
5. DNS サーバーは必須です。これは、デバイスがクラウド ストレージのサービス プロバイダーとやり取りしたり、デバイスがファイル サーバーとして構成されている場合にデバイスを名前により解決したりする際に使用されます。 **[ネットワークの設定]** ページの **[DNS サーバー]** で、次の操作を行います。
   
   1. プライマリおよびセカンダリ DNS サーバーが自動的に構成されます。 静的 IP アドレスを構成することを選択した場合は、DNS サーバーを指定できます。 高可用性を確保するため、プライマリとセカンダリ DNS サーバーを構成することをお勧めします。
   2. **[適用]** をクリックして、ネットワークの設定を適用して検証します。
6. **[デバイスの設定]** ページで次の操作を行います。
   
   1. デバイスに一意の **名前** を割り当てます。 この名前は 1 ～ 15 文字を指定でき、文字、数字、ハイフンを含めることができます。
   2. 作成するデバイスの **種類** として、 **[ファイル サーバー]** アイコン :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image6.png"::: をクリックします。 ファイル サーバーにより、共有フォルダーを作成できるようになります。
   3. デバイスの種類がファイル サーバーなので、そのデバイスをドメインに参加させる必要があります。 **[ドメイン名]** を入力します。
   4. **[Apply]** をクリックします。
7. ダイアログ ボックスが表示されます。 ドメインの資格情報を指定された形式で入力します。 チェック マーク アイコンをクリックします。 ドメインの資格情報が検証されます。 資格情報が間違っていると、エラー メッセージが表示されます。
   
   ![ユーザー名とパスワードが入力されたダイアログ ボックスを示すスクリーンショット。](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. **[Apply]** をクリックします。 これにより、デバイスの設定が適用、検証されます。
   
   ![[デバイスの設定] ページのスクリーンショット。 デバイス名とドメイン名が入力されています。](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > 仮想アレイが Active Directory の独自の組織単位 (OU) にあり、グループ ポリシー オブジェクト (GPO) が適用または継承されていないことを確認します。 グループ ポリシーにより、StorSimple Virtual Array にウイルス対策ソフトウェアなどのアプリケーションがインストールされる場合があります。 追加のソフトウェアのインストールはサポートされていないので、データが破損するおそれがあります。 
   > 
   > 
9. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できることに注意してください。
   
   ![[Web プロキシの設定] ページのスクリーンショット。 [Enable web proxy]\(Web プロキシを有効にする\) オプションがオフになっており、[認証] が [None]\(なし\)に設定されています。 その他の値は設定されていません。](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   **[Web プロキシ]** ページで、以下のことを実行します。
   
   1. "*http://&lt;host-IP アドレスまたは FQDN&gt;:ポート番号*" の形式で、 **[Web プロキシ URL]** を指定します。 HTTPS URL はサポートされていないことに注意してください。
   2. **[認証]** に **[基本]** または **[なし]** を指定します。
   3. 認証を使用する場合は、**ユーザー名** と **パスワード** も指定する必要があります。
   4. **[Apply]** をクリックします。 これにより、構成済みの Web プロキシ設定が検証され、適用されます。
10. (省略可能) デバイスの時刻設定を構成します (タイム ゾーン、プライマリおよびセカンダリ NTP サーバーなど)。 デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
    
    ![[時刻の設定] ページのスクリーンショット。 タイム ゾーンとプライマリ NTP サーバーが入力されています。 セカンダリ NTP サーバーは空白になっています。](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    **[時刻の設定]** ページで、次の操作を行います。
    
    1. ドロップダウン リストから、デバイスをデプロイする地理的な場所に基づいて **タイム ゾーン** を選択します。 デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
    2. デバイスの **[プライマリ NTP サーバー]** を指定するか、time.windows.com の既定値をそのまま使用します。 データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。
    3. 必要に応じて、デバイスの **[セカンダリ NTP サーバー]** を指定します。
    4. **[Apply]** をクリックします。 これにより、構成済みの時刻設定が検証され、適用されます。
11. デバイスのクラウドの設定を構成します。 この手順では、ローカル デバイスの構成を完了してから、StorSimple デバイス マネージャー サービスにそのデバイスを登録します。
    
    1. **サービス登録キー** (「[手順 2: サービス登録キーを取得する](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)」で取得したもの) を入力します。
    2. このサービスに登録する最初のデバイスの場合は、**サービス データ暗号化キー** が提供されます。 このキーをコピーし、安全な場所に保存しておきます。 このキーは、StorSimple デバイス マネージャー サービスに追加のデバイスを登録するときに、サービス登録キーと共に必要になります。 
       
       このサービスに登録する最初のデバイスでない場合は、サービス データ暗号化キーを指定する必要があります。 詳しくは、ローカル Web UI の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 」を参照してください。
    3. **[登録]** をクリックします。 これにより、デバイスが再起動します。 デバイスが正常に登録されるまでに、2 ～ 3 分間待機する必要がある場合があります。 デバイスが再起動したら、サインイン ページが表示されます。
       
       ![[クラウドの設定] ページのスクリーンショット。 登録キーのボックスと 暗号化キーのボックスには入力されていますが、値は編集されています。](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Azure Portal に戻ります。 **[すべてのリソース]** に移動して、StorSimple デバイス マネージャー サービスを検索します。
    
    ![Azure portal の [すべてのリソース] ページのスクリーンショット。 デバイス マネージャー サービスが強調表示されています。](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. フィルター処理された一覧で、StorSimple デバイス マネージャー サービスを選択し、 **[管理] > [デバイス]** の順に移動します。 **[デバイス]** ブレードで、デバイスが正常にサービスに接続されていること、および **セットアップの準備が完了している** 状態であることを確認します。
    
    ![デプロイ](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>手順 2:デバイスをファイル サーバーとして構成する
必要なデバイスのセットアップを完了するには、[Azure Portal](https://portal.azure.com/) で次の手順を実行します。

#### <a name="to-configure-the-device-as-file-server"></a>デバイスをファイル サーバーとして構成するには
1. StorSimple デバイス マネージャー サービスに移動し、 **[管理] > [デバイス]** の順に移動します。 **[デバイス]** ブレードで、先ほど作成したデバイスを選択します。 このデバイスは、**セットアップの準備が完了している** 状態として表示されます。
   
   ![ファイル サーバーの構成](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. デバイスをクリックすると、デバイスのセットアップの準備が完了していることを示すバナー メッセージが表示されます。
   
    ![ファイル サーバーの構成 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. コマンド バーで **[構成]** をクリックします。 これにより **[構成]** ブレードが開きます。 **[構成]** ブレードで、次の操作を行います。
   
   1. ファイル サーバー名は自動的に設定されます。
    
   2. クラウド ストレージ暗号化は必ず **[有効]** に設定します。 これによりクラウドに送信されるすべてのデータが暗号化されます。 
    
   3. ユーザー定義のキーの暗号化には 256 ビット AES キーが使用されます。 32 文字のキーを指定し、そのキーを確認のために再入力します。 後で参照できるように、キー管理アプリケーションにキーを記録します。
    
   4. **[必要な設定の構成]** をクリックして、デバイスで使用するストレージ アカウントの資格情報を指定します。 ストレージ アカウントの資格情報が構成されていない場合は、 **[新規追加]** をクリックします。 **使用するストレージ アカウントがブロック BLOB をサポートしていることを確認します。ページ BLOB はサポートされていません。** [ブロック BLOB とページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)に関する詳細情報。
   
      ![ファイル サーバーの構成 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. **[ストレージ アカウント資格情報の追加]** ブレードで、次の操作を行います。 

    1. サービスと同じサブスクリプションにストレージ アカウントがある場合は、現在のサブスクリプションを選択します。 サービス サブスクリプション外のストレージ アカウントの場合は、[その他] を指定します。 
    
    2. ドロップダウン リストで、既存のストレージ アカウントを選択します。 
    
    3. 場所は、指定したストレージ アカウントに基づいて自動的に入力されます。 
    
    4. デバイスとクラウドの間のネットワーク通信チャネルを確実にセキュリティで保護するには、TLS を有効にします。
    
    5. **[追加]** をクリックして、このストレージ アカウントの資格情報を追加します。 
   
        ![ファイル サーバーの構成 4](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. ストレージ アカウントの資格情報が正常に作成されると、 **[構成]** ブレードが更新され、指定したストレージ アカウントの資格情報が表示されます。 **[構成]** をクリックします。
   
   ![ファイル サーバーの構成 5](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   作成中のファイル サーバーが表示されます。 ファイル サーバーが正常に作成されると、その旨が通知されます。
   
   ![ファイル サーバーの構成 5b](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   デバイスの状態も "**オンライン**" に変わります。
   
   ![ファイル サーバーの構成 5c](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   共有の追加に進みます。

## <a name="step-3-add-a-share"></a>手順 3:共有の追加
共有を作成するには、[Azure Portal](https://portal.azure.com/) で次の手順を実行します。

#### <a name="to-create-a-share"></a>共有を作成するには
1. 前の手順で構成したファイル サーバーのデバイスを選択し、 **[...]** をクリックします (または右クリックします)。 コンテキスト メニューで **[共有の追加]** を選択します。 または、デバイスのコマンド バーの **[+ 共有の追加]** をクリックします。
   
   ![共有の追加](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. 次の共有設定を指定します。

   1. 共有の一意の名前。 名前は 3 ～ 127 文字を含む文字列である必要があります。
    
   2. 共有の **説明** (省略可能)。 説明は、共有の所有者の特定に役立ちます。
    
   3. 共有の **種類**。 **[階層化]** または **[ローカル固定]** を種類として選択できます。既定では [階層化] に設定されています。 ローカルの保証、低待機時間、高パフォーマンスを必要とするワークロードでは、 **[ローカル固定]** 共有を選択します。 それ以外のデータの場合は、 **[階層化]** を選択します。
      ローカル固定の共有はシック プロビジョニングされ、共有上のプライマリ データがデバイスに対してローカルのままになり、クラウドへの書き込みは行われません。 一方、階層化共有はシン プロビジョニングされます。 階層化共有を作成した場合、領域の 10% はローカル層にプロビジョニングされ、90% はクラウドにプロビジョニングされます。 たとえば、1 TB ボリュームをプロビジョニングした場合、データが階層化されるとき、100 GB はローカル領域に格納され、900 GB はクラウドに使用されます。 このため、これはデバイスのすべてのローカル領域が不足すると、階層化共有をプロビジョニングできないことを意味します。
   
   4. **[Set default full permissions to (既定の完全なアクセス許可を設定)]** フィールドで、この共有にアクセスするユーザーまたはグループにアクセス許可を割り当てます。 ユーザーまたはユーザー グループの名前を *john\@contoso.com* の形式で指定します。 この共有にアクセスする管理者特権を許可するには、(1 人のユーザーではなく) ユーザー グループを使用することをおすすめします。 ここで割り当てたアクセス許可は、ファイル エクスプローラーを使用して変更できます。
   
   5. **[追加]** をクリックして、共有を作成します。 
    
       ![共有の追加 1](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       共有の作成が進行中であることが通知されます。
   
       ![共有の追加 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      指定した設定で共有を作成すると、 **[共有]** ブレードは更新され、新しい共有が反映されます。 既定では、監視とバックアップが共有に対して有効です。
   
      ![共有の追加 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>手順 4:共有に接続する
次に、前の手順で作成した 1 つ以上の共有に接続する必要があります。 StorSimple Virtual Array に接続されている Windows Server ホストで、次の手順を実行します。

#### <a name="to-connect-to-the-share"></a>共有に接続するには
1. :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image22.png"::: + R キーを押します。[ファイル名を指定して実行] ウィンドウで、パスとして *&#92;&#92;&lt;ファイル サーバー名&gt;* を指定します。*file server name* は、ファイル サーバーに割り当てたデバイス名で置き換えます。 **[OK]** をクリックします。
   
   ![[ファイル名を指定して実行] ダイアログ ボックスのスクリーンショット。 [名前:] ボックスには、ファイル サーバーへのパスが入力されています。](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. これによりファイル エクスプローラーが開きます。 フォルダーとして作成した共有が表示されます。 コンテンツを表示するには、共有 (フォルダー) を選択してダブルクリックします。
   
   ![ファイル エクスプローラーのスクリーンショット。 新しく作成された共有を表す複数のフォルダーが表示されています。](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. これらの共有にファイルを追加して、バックアップを作成することができます。

## <a name="next-steps"></a>次のステップ
ローカル Web UI を使用して [StorSimple Virtual Array を管理する](storsimple-ova-web-ui-admin.md)方法を確認します。