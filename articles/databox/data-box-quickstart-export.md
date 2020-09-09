---
title: クイック スタート:Microsoft Azure Data Box のデータのエクスポート
description: Azure portal で Azure Data Box のデータをすばやくエクスポートする方法について説明します。
services: databox
author: twooley
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: twooley
ms.localizationpriority: high
ms.openlocfilehash: 55366096a9eef7380ad8df855931c4d675973631
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783061"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>クイック スタート:Azure からデータをエクスポートするために Azure Data Box の使用を開始する

このクイックスタートでは、Azure portal を使用して Azure から目的の場所にデータをエクスポートする方法について説明します。 手順には、ケーブルの接続、構成、Azure からのデータのコピーの方法が含まれています。 クイック スタートは、Azure portal およびデバイスのローカル Web UI で行われます。

デプロイと追跡に関する詳しい手順については、「[チュートリアル: Azure Data Box のエクスポート注文を作成する](data-box-deploy-export-ordered.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のことを行います。

* Data Box サービスで使用するサブスクリプションが、次のいずれかの種類であることを確認します。
  * Microsoft Enterprise Agreement (EA)。 [EA サブスクリプション](https://azure.microsoft.com/pricing/enterprise-agreement/)に関する詳細をご覧ください。
  * Cloud Solution Provider (CSP)。 Azure CSP プログラムの詳細は[こちら](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)です。
  * Microsoft Azure スポンサー プラン。 Azure スポンサー プラン プログラムの詳細は[こちら](https://azure.microsoft.com/offers/ms-azr-0036p/)です。

* Data Box の注文を作成するサブスクリプションに対して、所有者または共同作成者アクセス権があることを確認します。
* [ Data Box の安全性のガイドライン](data-box-safety.md)を確認します。
* データを目的の場所に移動するための Azure Data Box デバイスを用意します。 このホスト コンピューターは次の条件を満たしている必要があります。
  * [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
  * 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。
* Data Box を設置できる平らな場所があること。 デバイスを標準的なラック棚に設置しようとする場合は、データ センター ラックに 7U のスロットが必要です。 デバイスは平面に置くことも、ラックに立てて置くこともできます。
* Data Box をホスト コンピューターに接続するために、次のケーブルを用意しておきます。
  * 10 GbE SFP+ Twinax 銅線ケーブル 2 本 (DATA 1、DATA 2 ネットワーク インターフェイスで使用)
  * RJ-45 CAT 6 ネットワーク ケーブル 1 本 (管理 (MGMT) ネットワーク インターフェイスで使用)
  * RJ-45 CAT 6A OR ネットワーク ケーブル 1 本、RJ-45 CAT 6 ネットワーク ケーブル 1 本 (それぞれ 10 Gbps または 1 Gbps として構成されている DATA 3 ネットワーク インターフェイスで使用)

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="order"></a>Order

この手順には約 10 分かかります。

1. Azure portal で新しい Azure Data Box リソースを作成します。
2. このサービスが有効になった既存のサブスクリプションを選択し、転送の種類として **[Export to Azure]\(Azure にエクスポート\)** を選択します。 **[Source Azure region]\(ソース Azure リージョン\)** にデータが存在する場所を指定し、 **[Destination country]\(宛先の国\)** にデータ転送先を指定します。
3. **[Data Box]** を選択します。 使用可能な最大容量は 80 TB ですが、それを超えるサイズのデータについては複数の注文を作成することができます。
4. **[Add storage account and export type]\(ストレージ アカウントとエクスポートの種類の追加\)** 、 **[Select Export option]\(エクスポート オプションの選択\)** の順に選択します。
5. 注文の詳細と発送情報を入力します。 ご利用のリージョンでこのサービスが提供されている場合、通知メール アドレスを指定し、概要を確認したうえで注文を作成します。

注文が作成されると、デバイスの発送準備が行われます。

## <a name="cable"></a>ケーブル

この手順には約 10 分かかります。

Data Box を入手したら、次の手順に従ってデバイスのケーブルを接続し、電源を入れます。 この手順には約 10 分かかります。

1. デバイスが改ざんされたり破損していたりする形跡がある場合は、作業を続行しないでください。 交換用のデバイスを発送するよう Microsoft サポートに問い合わせてください。
2. デバイスのケーブルを接続する前に、次のケーブルがあることを確認します。

   * 一端にデバイス接続用の IEC60320 C-13 コネクタが付いた定格 10 A 以上のアース付き電源コード (付属品)
   * RJ-45 CAT 6 ネットワーク ケーブル 1 本 (管理 (MGMT) ネットワーク インターフェイスで使用)
   * 10 GbE SFP+ Twinax 銅線ケーブル 2 本 (10 Gbps DATA 1、DATA 2 ネットワーク インターフェイスで使用)
   * RJ-45 CAT 6A OR ネットワーク ケーブル 1 本、RJ-45 CAT 6 ネットワーク ケーブル 1 本 (それぞれ 10 Gbps または 1 Gbps として構成されている DATA 3 ネットワーク インターフェイスで使用)

3. デバイスを取り外して平らな場所に置きます。

4. 次に示すようにしてデバイスのケーブルを接続します。  

    ![ケーブル配線した Data Box デバイスのバックプレーン](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. 電源ケーブルをデバイスに接続します。
    2. RJ-45 CAT 6 ネットワーク ケーブルを使用して、ホスト コンピューターをデバイスの MGMT ポートに接続します。
    3. SFP+ Twinax 銅線ケーブルを使用して、10 Gbps 以上 (1 Gbps よりも推奨) のデータ用 DATA 1 または DATA 2 ネットワーク インターフェイスを少なくとも 1 つ接続します。
    4. デバイスの電源を入れます。 電源ボタンはデバイスのフロント パネルにあります。

## <a name="connect"></a>接続する

この手順の所要時間は 5 分から 7 分程度です。

1. デバイスのパスワードを取得するには、[Azure portal](https://portal.azure.com) で **[全般]**  >  **[デバイスの詳細]** に移動します。
2. Data Box に接続するために使用するコンピューターのイーサネット アダプターに、静的 IP アドレス 192.168.100.5 と、サブネット 255.255.255.0 を割り当てます。 `https://192.168.100.10` からデバイスのローカル Web UI にアクセスします。 デバイスを起動してから接続するまで最大 5 分かかることがあります。
3. Azure portal からパスワードを使用してサインインします。 Web サイトのセキュリティ証明書に問題があることを示すエラーが表示されます。 ブラウザー固有の手順に従い Web ページに進みます。
4. 既定では、10 Gbps のデータ インターフェイス(または 1 Gbps) のネットワーク設定は、DHCP として構成されます。 必要に応じて、このインターフェイスを静的インターフェイスとして構成し、IP アドレスを入力することができます。

## <a name="copy-data"></a>データをコピーする

この工程にかかる時間は、実際のデータのサイズおよびネットワークの速度によって異なります。

1. Windows クライアントを使用する場合は、Robocopy などの SMB 互換のファイル コピー ツールを使用します。 NFS のホストの場合、`cp` コマンドまたは `rsync` を使用してデータをコピーします。 Robocopy を使用してデータをコピーする方法の詳細については、[Robocopy](https://technet.microsoft.com/library/ee851678.aspx) を参照してください。
2. デバイス共有に接続し、ホスト コンピューターへのデータのコピーを開始します。
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Azure への発送

この処理が完了するまでに 10 ～ 15 分程度かかります。

1. ローカル Web UI の **[出荷準備]** ページに移動し、出荷準備を開始します。
2. ローカル Web UI からデバイスをオフにします。 ケーブルをデバイスから取り外します。
3. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
4. 返送ラベルが E インク ディスプレイに表示されます。 E インク ディスプレイにラベルが表示されない場合は、Azure portal で **[概要]**  >  **[出荷ラベルをダウンロード]** に移動します。 配送先住所ラベルをダウンロードして、デバイスに貼り付けます。
5. デバイスを返送する場合は、地域の運送業者による集荷をスケジュールします。
6. 運送業者によって Data Box が集荷され、スキャンされると、ポータルの注文状態は "**集荷されました**" に更新されます。 追跡 ID も表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この手順を完了するには、2 分から 3 分かかります。

* Data Box の注文は、注文が処理される前であれば、Azure portal からキャンセルできます。 注文が処理された後はキャンセルできません。 注文は、完了ステージに到達するまで続行されます。 注文をキャンセルするには、 **[概要]** に移動し、コマンド バーの **[キャンセル]** を選択します。

* Azure portal で **完了済み**または**キャンセル済み**の状態になった注文は削除することができます。 注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure から Azure Data Box へのエクスポート注文をデプロイしました。 Azure Data Box の管理について詳しくは、次のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [Azure portal を使用して Data Box を管理する](data-box-portal-admin.md)
