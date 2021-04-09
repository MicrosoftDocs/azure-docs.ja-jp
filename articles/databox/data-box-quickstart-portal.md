---
title: Microsoft Azure Data Box のクイック スタート | Microsoft Docs
description: このクイックスタートでは、インポート注文用に Azure portal を使用して Azure Data Box をデプロイする方法について説明します。 Azure Data Box を構成し、データをコピーして Azure にアップロードします。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 3359152455df2701d897689a1f177bf63051b94b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440719"
---
# <a name="get-started-with-azure-data-box-to-import-data-into-azure"></a>Azure にデータをインポートするために Azure Data Box の使用を開始する

::: zone target="docs"

このクイック スタートでは、インポート注文用に Azure portal を使用して Azure Data Box をデプロイする方法について説明します。 手順には、ケーブル接続、構成、および Azure にアップロードするためにデータを Data Box にコピーする方法が含まれます。 このクイック スタートの手順は、Azure portal およびデバイスのローカル Web UI 上で実行します。

デプロイと追跡に関する詳しい手順については、「[チュートリアル: Azure Data Box を注文する](data-box-deploy-ordered.md)」を参照してください

::: zone-end 

::: zone target="chromeless"

このガイドでは、Azure portal を使用してインポート用に Azure Data Box をデプロイする方法について説明します。 手順には、前提条件の確認、デバイスのケーブル接続と接続、Azure にアップロードするデータのデバイスへのコピー作業が含まれます。

::: zone-end

::: zone target="docs"
 
## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のことを行います。

- Data Box サービスで使用するサブスクリプションが、次のいずれかの種類であることを確認します。
    - Microsoft 顧客契約 (MCA) (新しいサブスクリプションの場合) または Microsoft Enterprise Agreement (EA) (既存のサブスクリプションの場合)。 [新しいサブスクリプションの MCA](https://www.microsoft.com/licensing/how-to-buy/microsoft-customer-agreement) および [EA サブスクリプション](https://azure.microsoft.com/pricing/enterprise-agreement/)の詳細を確認してください。
    - Cloud Solution Provider (CSP)。 Azure CSP プログラムの詳細は[こちら](/azure/cloud-solution-provider/overview/azure-csp-overview)です。
    - Microsoft Azure スポンサー プラン。 Azure スポンサー プラン プログラムの詳細は[こちら](https://azure.microsoft.com/offers/ms-azr-0036p/)です。 

- Data Box の注文を作成するサブスクリプションに対して、所有者または共同作成者アクセス権があることを確認します。
- [ Data Box の安全性のガイドライン](data-box-safety.md)を確認します。
- Data Box にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 少なくとも 1 本の 10 GbE 接続を利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。 
- Data Box を設置できる平らな場所があること。 デバイスを標準的なラック棚に設置しようとする場合は、データ センター ラックに 7U のスロットが必要です。 デバイスは平面に置くことも、ラックに立てて置くこともできます。
- Data Box をホスト コンピューターに接続するために以下のケーブルを用意していること。
    - 10 GbE SFP+ Twinax 銅線ケーブル 2 本 (DATA 1、DATA 2 ネットワーク インターフェイスで使用)
    - RJ-45 CAT 6 ネットワーク ケーブル 1 本 (MGMT ネットワーク インターフェイスで使用)
    - RJ-45 CAT 6A OR ネットワーク ケーブル 1 本、RJ-45 CAT 6 ネットワーク ケーブル 1 本 (それぞれ 10 Gbps または 1 Gbps として構成されている DATA 3 ネットワーク インターフェイスで使用)

::: zone-end 

::: zone target="chromeless"

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. 「[チュートリアル: Azure Data Box を注文する](data-box-deploy-ordered.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータル内での注文の状態が **[配信済み]** であること。 
3. [Data Box の安全性に関するガイドライン](data-box-safety.md)を確認していること。
4. 100 TB ストレージ デバイスで使用するためのアース端子付き電源ケーブルを 1 本受け取っていること。
5. Data Box にコピーしたいデータがあるホスト コンピューターにアクセスできること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。 
6. Data Box を設置する平面へのアクセス。 デバイスを標準的なラック棚に横置きまたは縦置きするには、ラックに 7U のスロットが必要です。

::: zone-end

::: zone target="docs"

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="order"></a>Order

この手順には約 5 分かかります。

1. Azure portal で新しい Azure Data Box リソースを作成します。
2. このサービスが有効になった既存のサブスクリプションを選択し、転送の種類として **[インポート]** を選択します。 **[ソースの国]** にデータが存在する場所を、 **[宛先 Azure リージョン]** にデータの転送先を指定します。
3. **[Data Box]** を選択します。 使用可能な最大容量は 80 TB ですが、それを超えるサイズのデータについては複数の注文を作成することができます。
4. 注文の詳細と発送情報を入力します。 ご利用のリージョンでこのサービスが提供されている場合、通知メール アドレスを指定し、概要を確認したうえで注文を作成します。

注文が作成されると、デバイスの発送準備が行われます。



## <a name="cable"></a>ケーブル 

この手順には約 10 分かかります。

Data Box を入手したら、次の手順に従ってデバイスのケーブルを接続し、電源を入れます。 この手順には約 10 分かかります。

1. デバイスが改ざんされたり破損していたりする形跡がある場合、作業を続行しないでください。 交換用のデバイスを発送するよう Microsoft サポートに問い合わせてください。
2. デバイスのケーブルを接続する前に、次のケーブルがあることを確認します。
    
    - (付属品) デバイス接続用の IEC60320 C-13 コネクタが付いた定格 10A 以上のアース付き電源コード。
    - RJ-45 CAT 6 ネットワーク ケーブル 1 本 (MGMT ネットワーク インターフェイスで使用)
    - 10 GbE SFP+ Twinax 銅線ケーブル 2 本 (10 Gbps DATA 1、DATA 2 ネットワーク インターフェイスで使用)
    - RJ-45 CAT 6A OR ネットワーク ケーブル 1 本、RJ-45 CAT 6 ネットワーク ケーブル 1 本 (それぞれ 10 Gbps または 1 Gbps として構成されている DATA 3 ネットワーク インターフェイスで使用)

3. デバイスを取り外して平らな場所に置きます。 
    
4. 次に示すようにしてデバイスのケーブルを接続します。  

    ![ケーブル配線した Data Box デバイスのバックプレーン](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. 電源ケーブルをデバイスに接続します。
    2. RJ-45 CAT 6 ネットワーク ケーブルを使用して、ホスト コンピューターをデバイス上の管理ポート (MGMT) に接続します。 
    3. SFP+ Twinax 銅線ケーブルを使用して、10 Gbps 以上 (1 Gbps よりも望ましい) のデータ用 DATA 1 または DATA 2 ネットワーク インターフェイスを少なくとも 1 つ接続します。 
    4. デバイスの電源を入れます。 電源ボタンはデバイスのフロント パネルにあります。


## <a name="connect"></a>接続する

この手順の所要時間は 5 分から 7 分程度です。

1. デバイスのパスワードを取得するには、[Azure Portal](https://portal.azure.com) で **[全般] > [デバイスの詳細]** に移動します。
2. Data Box に接続するために使用するコンピューターのイーサネット アダプターを、静的 IP アドレス 192.168.100.5、サブネット 255.255.255.0 で構成します。 `https://192.168.100.10` からデバイスのローカル Web UI にアクセスします。 デバイスを起動してから接続するまで最大 5 分かかることがあります。 
3. Azure portal からパスワードを使用してサインインします。 Web サイトのセキュリティ証明書に問題があることを示すエラーが表示されます。 ブラウザー固有の手順に従い Web ページに進みます。
4. 既定では、10 Gbps のデータ インターフェイス(または 1 Gbps) のネットワーク設定は、DHCP として構成されます。 必要に応じて、このインターフェイスを静的インターフェイスとして構成し、IP アドレスを入力することができます。 

## <a name="copy-data"></a>データをコピーする

この工程にかかる時間は、実際のデータのサイズおよびネットワークの速度によって異なります。
 
1. Windows ホストを使用する場合は、Robocopy などの SMB 互換のファイル コピー ツールを使用します。 NFS のホストの場合、`cp` コマンドまたは `rsync` を使用してデータをコピーします。 ツールをデバイスに接続し、共有へのデータのコピーを開始します。 Robocopy を使用してデータをコピーする方法の詳細については、[Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)) を参照してください。
2. パス `\\<IP address of your device>\ShareName` を使用して共有に接続します。 共有アクセス資格情報を取得するには、Data Box のローカル Web UI にある **[接続とコピー]** ページに移動します。
3. 共有およびフォルダー名、およびデータが、 [Azure Storage と Data Box サービスの制限](data-box-limits.md)に記載されているガイドラインに従っていることを確認します。

## <a name="ship-to-azure"></a>Azure への発送 

この処理が完了するまでに 10 ～ 15 分程度かかります。

1. ローカル Web UI の **[出荷準備]** ページに移動し、出荷準備を開始します。 
2. ローカル Web UI からデバイスをオフにします。 ケーブルをデバイスから取り外します。 
3. 返送ラベルが E インク ディスプレイに表示されます。 E インク ディスプレイにラベルが表示されない場合は、Azure portal から配送先住所ラベルをダウンロードし、デバイスに添付されているクリア カバーに挿入します。
4. ケースをロックして、Microsoft に送付します。 

## <a name="verify-data"></a>データの確認

この工程にかかる時間は、実際のデータのサイズによって異なります。

1. Data Box デバイスが Azure データセンターのネットワークに接続されると、Azure へのデータのアップロードが自動的に開始されます。 
2. Azure Data Box サービスは、データのコピーが完了したことを Azure portal 経由でお客様に通知します。 

    1. 失敗していないかエラー ログで確認し、適切な措置を講じます。
    2. コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この手順を完了するには、2 分から 3 分かかります。

- Data Box の注文は、注文が処理される前であれば、Azure portal からキャンセルできます。 注文が処理された後は、キャンセルできません。 注文は、完了ステージに到達するまで続行されます。 注文をキャンセルするには、 **[概要]** に移動し、コマンド バーの **[キャンセル]** をクリックします。

- Azure portal で **完了済み** または **キャンセル済み** の状態になった注文は削除することができます。 注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure にデータをインポートするための Azure Data Box をデプロイしました。 Azure Data Box の管理について詳しくは、次のチュートリアルをご覧ください。 

> [!div class="nextstepaction"]
> [Azure portal を使用して Data Box を管理する](data-box-portal-admin.md)

::: zone-end
