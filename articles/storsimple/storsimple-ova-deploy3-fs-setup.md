---
title: "StorSimple Virtual Array をファイル サーバーとして設定する | Microsoft Docs"
description: "StorSimple Virtual Array のデプロイの&3; 番目のチュートリアルでは、ファイル サーバーとして仮想デバイスを設定する方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: adc1c942-dd4e-4589-bc10-18ae3f7cbcdc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: e1863b43706ffc200bb94c4a26ae75080a6dd857


---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>StorSimple Virtual Array のデプロイ - ファイル サーバーとしてセットアップする
![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>はじめに
この記事は、2016 年 3 月の一般公開 (GA) リリースを実行する Microsoft Azure StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスまたは StorSimple 仮想デバイスとも呼ばれます) に適用されます。 この記事では、初期セットアップ、StorSimple ファイル サーバーの登録、デバイス セットアップ、および SMB 共有の作成と接続の方法について説明します。 仮想アレイをファイル サーバーまたは iSCSI サーバーとして完全にデプロイするために必要なデプロイ チュートリアル シリーズの最後の記事です。

セットアップと構成のプロセスは、完了するまでに約 10 分かかることがあります。

## <a name="setup-prerequisites"></a>セットアップの前提条件
StorSimple 仮想デバイスを構成およびセットアップする前に、以下のことを確認します。

* 「[Hyper-V で StorSimple Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-hyperv.md)」または「[VMware で StorSimple Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-vmware.md)」の説明に従って仮想デバイスをプロビジョニングし、接続していること。
* StorSimple 仮想デバイスを管理するために作成した、StorSimple Manager サービスからのサービス登録キーがあること。 詳細については、StorSimple Virtual Array の「 [手順 2. サービス登録キーを取得する](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) 」をご覧ください。
* 既存の StorSimple Manager サービスに登録する&2; 番目または後続の仮想デバイスである場合は、サービス データ暗号化キーがあるはずです。 このキーは、最初のデバイスがこのサービスに正常に登録されたときに生成されています。 このキーを紛失した場合は、StorSimple Virtual Array の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 」をご覧ください。

## <a name="step-by-step-setup"></a>セットアップの手順
StorSimple 仮想デバイスをセットアップして構成するには、次の詳細な手順を実行します。

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>手順 1: ローカル Web UI のセットアップを完了し、デバイスを登録する
#### <a name="to-complete-the-setup-and-register-the-device"></a>セットアップを完了し、デバイスを登録するには
1. ブラウザー ウィンドウを開き、以下を入力してローカル Web UI に接続します。    
   
   `https://<ip-address of network interface>`
   
   前の手順に記載されている接続 URL を使用します。 Web サイトのセキュリティ証明書に問題があることを示すエラーが表示されます。 **[この Web ページの閲覧を続ける]**をクリックして、この Web ページに進みます。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)
2. 仮想デバイスの Web UI に **StorSimpleAdmin**としてサインインします。 「[Hyper-V で StorSimple Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-hyperv.md)」または「[VMware で Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-vmware.md)」の「手順 3: 仮想デバイスを起動し、IP アドレスを取得する」で変更したデバイス管理者のパスワードを入力します。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)
3. **[ホーム]** ページが表示されます。 このページは、仮想デバイスを StorSimple Manager サービスに構成、登録するのに必要なさまざまな設定を掲載しています。 **ネットワーク設定**、**Web プロキシの設定**、**時刻の設定**はオプションであることに注意してください。 必須の設定は、**デバイスの設定**と**クラウドの設定**のみです。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)
4. **[ネットワークの設定]** ページの **[ネットワーク インターフェイス]** で、DATA 0 が自動的に構成されます。 各ネットワーク インターフェイスは、既定で IP アドレスを自動的に取得するように設定されます (DHCP)。 そのため、IP アドレス、サブネット、およびゲートウェイは自動的に割り当てられます (IPv4 と IPv6 の両方に対して)。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)
   
   デバイスのプロビジョニング中に複数のネットワーク インターフェイスを追加した場合は、それらをここで構成できます。 ネットワーク インターフェイスは IPv4 だけで構成するか、IPv4 と IPv6 の両方で構成することができます。 IPv6 だけで構成することはできません。
5. DNS サーバーは必須です。これは、デバイスがクラウド ストレージのサービス プロバイダーとやり取りしたり、デバイスがファイル サーバーとして構成されている場合にデバイスを名前により解決したりする際に使用されます。 **[ネットワークの設定]** ページの **[DNS サーバー]** で、次の操作を行います。
   
   1. プライマリおよびセカンダリ DNS サーバーが自動的に構成されます。 静的 IP アドレスを構成することを選択した場合は、DNS サーバーを指定できます。 高可用性を確保するため、プライマリとセカンダリ DNS サーバーを構成することをお勧めします。
   2. **[Apply]**をクリックします。 これにより、ネットワークの設定が適用、検証されます。
6. **[デバイスの設定]** ページで次の操作を行います。
   
   1. デバイスに一意の **名前** を割り当てます。 この名前は 1 ～ 15 文字を指定でき、文字、数字、ハイフンを含めることができます。
   2. 作成するデバイスの**種類**として、**[ファイル サーバー]** アイコン ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) をクリックします。 ファイル サーバーにより、共有フォルダーを作成できるようになります。
   3. デバイスの種類がファイル サーバーなので、そのデバイスをドメインに参加させる必要があります。 **[ドメイン名]**を入力します。
   4. **[Apply]**をクリックします。
7. ダイアログ ボックスが表示されます。 ドメインの資格情報を指定された形式で入力します。 チェック マーク アイコンをクリックします。 ドメインの資格情報が検証されます。 資格情報が間違っていると、エラー メッセージが表示されます。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)
8. **[Apply]**をクリックします。 これにより、デバイスの設定が適用、検証されます。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > 仮想アレイが Active Directory の独自の組織単位 (OU) にあり、グループ ポリシー オブジェクト (GPO) が適用または継承されていないことを確認します。 グループ ポリシーにより、StorSimple Virtual Array にウイルス対策ソフトウェアなどのアプリケーションがインストールされる場合があります。 追加のソフトウェアのインストールはサポートされていないので、データが破損するおそれがあります。 
   > 
   > 
9. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できることに注意してください。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)
   
   **[Web プロキシ]** ページで、以下のことを実行します。
   
   1. *http://&lt;host-IP アドレスまたは FDQN&gt;:ポート番号*の形式で、**[Web プロキシ URL]** を指定します。 HTTPS URL はサポートされていないことに注意してください。
   2. **[認証]** に **[基本]** または **[なし]** を指定します。
   3. 認証を使用する場合は、**ユーザー名**と**パスワード**も指定する必要があります。
   4. **[Apply]**をクリックします。 これにより、構成済みの Web プロキシ設定が検証され、適用されます。
10. (省略可能) デバイスの時刻設定を構成します (タイム ゾーン、プライマリおよびセカンダリ NTP サーバーなど)。 デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
    
    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)
    
    **[時刻の設定]** ページで、次の操作を行います。
    
    1. ドロップダウン リストから、デバイスをデプロイする地理的な場所に基づいて **タイム ゾーン** を選択します。 デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
    2. デバイスの **[プライマリ NTP サーバー]** を指定するか、time.windows.com の既定値をそのまま使用します。 データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。
    3. 必要に応じて、デバイスの **[セカンダリ NTP サーバー]** を指定します。
    4. **[Apply]**をクリックします。 これにより、構成済みの時刻設定が検証され、適用されます。
11. デバイスのクラウドの設定を構成します。 この手順では、ローカル デバイスの構成を完了してから、StorSimple Manager サービスにデバイスを登録します。
    
    1. StorSimple Virtual Array の「 **手順 2. サービス登録キーを取得する** 」で取得した [サービス登録キー](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) を入力します。
    2. これがこのサービスに登録する最初のデバイスの場合は、この手順をスキップして、次の手順に進みます。 デバイスがこのサービスに登録する最初のデバイスでない場合は、 **サービス データ暗号化キー**を指定する必要があります。 このキーは、StorSimple Manager サービスに追加のデバイスを登録する際にサービス登録キーと共に必要になります。 詳しくは、ローカル Web UI の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 」を参照してください。
    3. **[登録]**をクリックします。 これにより、デバイスが再起動します。 デバイスが正常に登録されるまでに、2 ～ 3 分間待機する必要がある場合があります。 デバイスが再起動したら、サインイン ページが表示されます。
       
       ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
12. Azure クラシック ポータルに戻ります。 **[デバイス]** ページで、状態を参照して、デバイスが正常にサービスに接続されていることを確認します。 デバイスの状態は " **アクティブ**" と表示されます。

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>手順 2: 必要なデバイスのセットアップを完了する
StorSimple デバイスのデバイス構成を完了するには、次の手順を実行する必要があります。

* このデバイスに関連付けるストレージ アカウントを選択します。
* クラウドに送信されるデータの暗号化の設定を選択します。

必要なデバイスのセットアップを完了するには、 [Azure クラシック ポータル](https://manage.windowsazure.com/) で次の手順を実行します。

#### <a name="to-complete-the-minimum-device-setup"></a>デバイスの最小セットアップを完了するには
1. **[デバイス]** ページで、先ほど作成したデバイスを選択します。 このデバイスは " **アクティブ**" として表示されます。 デバイス名を指している矢印をクリックし、 **[クイック スタート]**をクリックします。
2. **[デバイスのセットアップの完了]** をクリックして、デバイスの構成ウィザードを起動します。
3. デバイスの構成ウィザードの **[基本設定]** ページで、次の手順を実行します。
   
   1. このデバイスで使用するストレージ アカウントを指定します。 ドロップダウン リストからこのサブスクリプションの既存のストレージ アカウントを選択したり、 **[追加]** を指定して、別のサブスクリプションのアカウントを選択したりできます。
   2. クラウドに送信される、保存された状態のすべてのデータの暗号化設定 (AES 暗号化) を定義します。 データを暗号化するには、 **[クラウド ストレージの暗号化の有効化]**チェック ボックスをオンにします。 32 文字を含むクラウド ストレージの暗号化キーを入力します。 確認のため、キーを再入力します。 ユーザー定義のキーの暗号化には 256 ビット AES キーが使用されます。
   3. チェック マーク アイコン ![](./media/storsimple-ova-deploy3-fs-setup/image15.png)をクリックします。
      
      ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

これで設定が更新されました。 設定が正常に更新されると、デバイスのセットアップの完了ボタンは淡色表示になります。 デバイスの **[クイック スタート]** ページが再び表示されます。

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

> [!NOTE]
> **[構成]** ページにアクセスすれば、その他すべてのデバイス設定をいつでも変更できます。
> 
> 

## <a name="step-3-add-a-share"></a>手順 3: 共有を追加する
共有を作成するには、 [Azure クラシック ポータル](https://manage.windowsazure.com/) で次の手順を実行します。

#### <a name="to-create-a-share"></a>共有を作成するには
1. デバイスの **[クイック スタート]** ページで、**[共有の追加]** をクリックします。 共有の追加ウィザードが開始されます。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)
2. **[基本設定]** ページで次の操作を行います。
   
   1. 共有の一意の名前を指定します。 名前は 3 ～ 127 文字を含む文字列である必要があります。
   2. (省略可能) 共有の説明を入力します。 説明は、共有の所有者の特定に役立ちます。
   3. 共有の使用法の種類を選択します。 使用法の種類には、**[階層化]** または **[ローカル固定]** を選択でき、既定では [階層化] に設定されています。 ローカルの保証、低待機時間、高パフォーマンスを必要とするワークロードでは、 **[ローカル固定]** 共有を選択します。 それ以外のデータの場合は、 **[階層化]** を選択します。
   
   ローカル固定の共有はシック プロビジョニングされ、共有上のプライマリ データがデバイスに対してローカルのままになり、クラウドへの書き込みは行われません。 一方、階層化共有はシン プロビジョニングされます。 階層化共有を作成した場合、領域の 10% はローカル層にプロビジョニングされ、90% はクラウドにプロビジョニングされます。 たとえば、1 TB ボリュームをプロビジョニングした場合、データが階層化されるとき、100 GB はローカル領域に格納され、900 GB はクラウドに使用されます。 このため、これはデバイスのすべてのローカル領域が不足すると、階層化共有をプロビジョニングできないことを意味します。
3. 共有のプロビジョニングされた容量を指定します。 利用できる容量より小さい容量を指定する必要があります。 階層化された共有を使用している場合は、共有のサイズは 500 GB ～ 20 TB である必要があります。 ローカル固定共有の場合、50 GB ～ 2 TB の共有サイズを指定します。 利用できる容量を、共有のプロビジョニングのガイドとして使用します。 利用可能なローカル容量が 0 GB の場合、ローカル固定共有または階層化共有をプロビジョニングすることはできません。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)
4. 矢印アイコン ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) をクリックして、次のページに進みます。
5. **[追加設定]** ページで、この共有にアクセスするユーザーまたはグループにアクセス許可を割り当てます。 ユーザーまたはユーザー グループの名前を *<mailto:john@contoso.com>* の形式で指定します。 この共有にアクセスする管理者特権を許可するには、(1 人のユーザーではなく) ユーザー グループを使用することをおすすめします。 ここでアクセス許可を割り当てた後は、Windows エクスプローラーを使用してこれらのアクセス許可を変更することができます。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)
6. チェック マーク アイコン ![](./media/storsimple-ova-deploy3-fs-setup/image21.png)をクリックします。 指定された設定で共有が作成されます。 既定では、監視とバックアップが共有に対して有効になります。

## <a name="step-4-connect-to-the-share"></a>手順 4: 共有に接続する
次に、前の手順で作成した共有に接続する必要があります。 Windows Server ホスト上で次の手順を実行します。

#### <a name="to-connect-to-the-share"></a>共有に接続するには
1. ![](./media/storsimple-ova-deploy3-fs-setup/image22.png)+ R キーを押します。[ファイル名を指定して実行] ウィンドウで、パスとして *\\<file server name>* を指定し、*ファイル サーバー名*をファイル サーバーに割り当てたデバイス名で置き換えます。 **[OK]**をクリックします。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)
2. エクスプローラーが開きます。 フォルダーとして作成した共有が表示されます。 コンテンツを表示するには、共有 (フォルダー) を選択してダブルクリックします。
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)
3. これらの共有にファイルを追加して、バックアップを作成することができます。

![動画アイコン](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **ビデオ**

StorSimple Virtual Array をファイル サーバーとして構成、登録する方法を説明したビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Configure-a-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>次のステップ
ローカル Web UI を使用して [StorSimple Virtual Array を管理する](storsimple-ova-web-ui-admin.md)方法を確認します。




<!--HONumber=Jan17_HO5-->


