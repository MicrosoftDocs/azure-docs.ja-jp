---
title: Microsoft Azure Data Box Heavy のクイック スタート | Microsoft Docs
description: Azure portal からすばやく Azure Data Box Heavy をデプロイする方法を学ぶ
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 3467b25c085fb86d4aed3918d5446d118f76ffb8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446735"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure Data Box Heavy をデプロイする

このクイック スタートでは、Azure portal を使用して Azure Data Box Heavy をデプロイする方法について説明します。 手順には、ケーブル接続の方法、構成方法、Azure にアップロードするためにデータを Data Box Heavy にコピーする方法が含まれます。 クイック スタートは、Azure portal およびデバイスのローカル Web UI で行われます。

デプロイと追跡に関する詳しい手順については、「[チュートリアル: Azure Data Box Heavy の注文](data-box-heavy-deploy-ordered.md)に関するチュートリアルを参照してください。

## <a name="prerequisites"></a>前提条件

デバイスをデプロイする前に、設置場所、Data Box サービス、デバイスに関する次の構成の前提条件を満たしておいてください。

### <a name="for-installation-site"></a>設置場所では

開始する前に次の点を確認します。

- デバイスが搬入経路全体を問題なく通過できること。 デバイスのサイズは次のとおりです。幅: 26 インチ、長さ: 48 インチ、高さ: 28 インチ。
- 1 階以外のフロアに設置する場合、エレベーターまたはスロープからでもデバイスにアクセスできること。
- デバイスの運搬担当者が 2 人いること。 デバイスの重量は約 500 ポンドで、 車輪付きです。
- データセンター内に、これだけの設置面積を占めるデバイスを収容可能な平らな場所があり、その近くに使用可能なネットワーク接続用設備があること。

### <a name="for-service"></a>サービスでは

開始する前に次の点を確認します。

- アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。
- Data Box サービスで使用するサブスクリプションは、[マイクロソフト エンタープライズ契約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)、[クラウド ソリューション プロバイダー (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)、[Microsoft Azure スポンサー プラン](https://azure.microsoft.com/offers/ms-azr-0036p/)のいずれかです。
- Data Box Heavy の注文を作成するサブスクリプションに対して、所有者または共同作成者のアクセス権があること。

### <a name="for-device"></a>デバイスでは

開始する前に次の点を確認します。

- [Data Box Heavy の安全性のガイドライン](data-box-safety.md)を確認していること。
- データセンター ネットワークに接続されているホスト コンピューターがあること。 Data Box Heavy はこのコンピューターからデータをコピーします。 お使いのホスト コンピューターでは、[サポート対象のオペレーティング システム](data-box-heavy-system-requirements.md)が実行されている必要があります。
- RJ-45 ケーブルを使用してローカル UI に接続し、デバイスを構成するためのノート PC があること。 このノート PC を使用して、デバイスの各ノードで 1 度構成を行います。
- データセンターに高速ネットワークが備わっており、少なくとも 1 つの 10 GbE 接続を使用できること。
- デバイス ノードごとに 40 Gbps ケーブルまたは 10 Gbps ケーブルが 1 本あること。 Mellanox MCX314A-BCCT ネットワーク インターフェイスとの互換性のあるケーブルを選んでください。
    - 40 Gbps ケーブルの場合、ケーブルのデバイス側の端は QSFP+ である必要があります。
    - 10 Gbps ケーブルの場合、SFP+ ケーブルが必要です。ケーブルの一端を 10 G スイッチに差し込み、QSFP+ から SFP+ へのアダプター (QSA アダプター) を使用して他端をデバイスに差し込みます。
- 電源ケーブルがデバイス背面のトレイに収納されていること。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="order"></a>順序

この手順には約 5 分かかります。

1. Azure portal で新しい Azure Data Box リソースを作成します。
2. このサービスが有効になった既存のサブスクリプションを選択し、転送の種類として **[インポート]** を選択します。 **[ソースの国]** にデータが存在する場所を、 **[宛先 Azure リージョン]** にデータの転送先を指定します。
3. **[Data Box Heavy]** を選択します。 使用可能な最大容量は 770 TB ですが、それを超えるサイズのデータを扱う場合は複数の注文を作成することができます。
4. 注文の詳細と発送情報を入力します。 ご利用のリージョンでこのサービスが提供されている場合、通知メール アドレスを指定し、概要を確認したうえで注文を作成します。

注文が作成されると、デバイスの発送準備が行われます。

## <a name="cable-for-power"></a>電源にケーブル接続する

この手順の所要時間は約 5 分です。

Data Box Heavy を入手したら、次の手順に従ってデバイスのケーブルを電源に接続し、デバイスの電源を入れます。

1. デバイスが改ざんされたり破損していたりする形跡がある場合、作業を続行しないでください。 交換用のデバイスを発送するよう Microsoft サポートに問い合わせてください。
2. 設置場所までデバイスを移動してから、後部車輪をロックします。
3. 4 つの電源ケーブルをすべてデバイス背面の電源装置に接続します。
4. 前面の電源ボタンを使用して、デバイス ノードの電源を入れます。

## <a name="cable-first-node-for-network"></a>最初のノードをネットワークにケーブル接続する

この手順の所要時間は 10 分から 15 分程度です。

1. RJ-45 CAT 6 ネットワーク ケーブルを使用して、ホスト コンピューターをデバイス上の管理ポート (MGMT) に接続します。
2. Twinax QSFP+ 銅線ケーブルを使用して、40 Gbps 以上 (1 Gbps よりも望ましい) のデータ用 DATA 1 または DATA 2 ネットワーク インターフェイスを少なくとも 1 つ接続します。 10 Gbps スイッチを使用している場合は、Twinax SFP+ 銅線ケーブルを使用して、QSFP+ から SFP+ へのアダプター (QSA アダプター) を使用して、データ用の 40 Gbps ネットワーク インターフェイスに接続します。
3. 次に示すようにしてデバイスのケーブルを接続します。  

    ![ケーブル接続された Data Box Heavy](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>最初のノードを構成する

この手順の所要時間は 5 分から 7 分程度です。

1. デバイスのパスワードを取得するには、[Azure Portal](https://portal.azure.com) で **[全般] > [デバイスの詳細]** に移動します。 デバイスの両方のノードで同じパスワードが使用されます。
2. Data Box Heavy に接続するために使用するコンピューターのイーサネット アダプターを、静的 IP アドレス 192.168.100.5、サブネット 255.255.255.0 で構成します。 `https://192.168.100.10` からデバイスのローカル Web UI にアクセスします。 デバイスを起動してから接続するまで最大 5 分かかることがあります。
3. Azure portal からパスワードを使用してサインインします。 Web サイトのセキュリティ証明書に問題があることを示すエラーが表示されます。 ブラウザー固有の手順に従い Web ページに進みます。
4. 既定では、インターフェイス (MGMT を除く) のネットワーク設定は DHCP で構成されます。 必要に応じて、これらのインターフェイスを静的インターフェイスとして構成し、IP アドレスを指定することができます。

## <a name="cable-and-configure-the-second-node"></a>2 番目のノードをケーブル接続して構成する

この手順の所要時間は 15 分から 20 分程度です。

最初のノードのときに使用した手順に従って、デバイスの 2 番目のノードをケーブル接続して構成します。  

## <a name="copy-data"></a>データをコピーする

この操作の所要時間は、データのサイズと、データのコピー時に使用するネットワークの速度によって異なります。
 
1. 両方の 40 Gbps データ インターフェイスを並列で使用して、両方のデバイス ノードにデータをコピーします。

    - Windows ホストを使用する場合は、[Robocopy](https://technet.microsoft.com/library/ee851678.aspx) などの SMB 互換のファイル コピー ツールを使用します。
    - NFS のホストの場合、`cp` コマンドまたは `rsync` を使用してデータをコピーします。
2. `\\<IP address of your device>\ShareName` というパスを使用して、デバイス上の共有に接続します。 共有アクセス資格情報を取得するには、Data Box Heavy のローカル Web UI にある **[接続とコピー]** ページに移動します。
3. 共有とフォルダーの名前、そしてデータが、[Azure Storage と Data Box Heavy サービスの制限](data-box-heavy-limits.md)に記載されているガイドラインに従っていることを確認します。

## <a name="prepare-to-ship"></a>発送の準備をする

この工程にかかる時間は、実際のデータのサイズによって異なります。

1. データのコピーがエラーなしで完了した後、ローカル Web UI の **[発送準備]** ページに移動し、発送準備を開始します。
2. 両方のノードで**発送準備**が正常に完了したら、ローカル Web UI でデバイスの電源を切ります。

## <a name="ship-to-azure"></a>Azure への発送

この操作の所要時間は 15 分から 20 分程度です。

1. ケーブルを取り外し、デバイス背面のトレイに戻します。
2. リージョンの運送業者で集荷をスケジュールします。
3. [Data Box 運用チーム](mailto:DataBoxOps@microsoft.com)に連絡して集荷について通知し、返送ラベルを入手します。
4. 返送ラベルがデバイスの前面クリア パネルに表示されます。

## <a name="verify-data"></a>データの確認

この工程にかかる時間は、実際のデータのサイズによって異なります。

1. Data Box Heavy デバイスが Azure データセンターのネットワークに接続されると、データが自動的に Azure にアップロードされます。
2. Data Box サービスから、データのコピーが完了したことが Azure portal 経由で通知されます。

    1. 失敗していないかエラー ログで確認し、適切な措置を講じます。
    2. コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この手順を完了するには、2 分から 3 分かかります。

- Data Box Heavy の注文は、注文が処理される前であれば、Azure portal からキャンセルできます。 注文が処理された後は、キャンセルできません。 注文は、完了ステージに到達するまで続行されます。 注文をキャンセルするには、 **[概要]** に移動し、コマンド バーの **[キャンセル]** をクリックします。

- Azure portal で **完了済み**または**キャンセル済み**の状態になった注文は削除することができます。 注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure へのデータのインポートを支援する Data Box Heavy をデプロイしました。 Azure Data Box Heavy の管理の詳細については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure portal を使用して Data Box Heavy を管理する](data-box-portal-admin.md)
