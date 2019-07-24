---
title: Microsoft Azure Data Box の返送 | Microsoft Docs
description: Azure Data Box を Microsoft に送付する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 72d6ce58a986ddd0d0976d99de5ca3426d78f0b9
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287164"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>チュートリアル:Azure Data Box の返送と Azure へのデータ アップロードの確認

このチュートリアルでは、Azure Data Box を返送し、Azure にアップロードされたデータを確認する方法について説明します。

このチュートリアルでは、次のようなトピックについて説明します。

> [!div class="checklist"]
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

- 「[チュートリアル: Azure Data Box にデータをコピーして確認する](data-box-deploy-copy-data.md)」を完了していることを確認してください。 
- コピージョブが完了していること。 コピージョブが進行中の場合、発送準備を行うことはできません。

## <a name="prepare-to-ship"></a>発送の準備をする

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Data Box を返送する

1. デバイスの電源をオフにして、ケーブル類を取り外します。 デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
2. 配送先住所ラベルが電子インク ディスプレイに表示されていることを確認し、運送業者の集荷をスケジュールします。 ラベルを破損または紛失した場合、またはラベルが電子インク ディスプレイに表示されていない場合、Microsoft サポートにお問い合わせください。 サポートから指示された場合は、Azure portal の **[概要] > [出荷ラベルをダウンロード]** にアクセスしてください。 配送先住所ラベルをダウンロードして、デバイスに貼り付けます。 
3. デバイスを返送する場合は、UPS での集荷をスケジュールします。 集荷のスケジュールを設定するには:

    - 最寄りの UPS (国固有のフリー ダイヤル) に連絡します。
    - 電話で、電子インク ディスプレイまたは印刷ラベルに表示されている返送追跡番号を伝えます。
    - 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Data Box を持ち込むこともできます。
4. 運送業者によって Data Box が集荷され、スキャンされると、ポータルの注文状態は "**集荷されました**" に更新されます。 追跡 ID も表示されます。

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

Microsoft がディスクを受け取ってスキャンすると、注文の状態は "**受取済み**" に更新されます。 その後、デバイスの損傷または改ざんの印がないか物理的に検証されます。

検証が完了すると、Data Box は Azure データセンターのネットワークに接続されます。 データのコピーが自動的に開始されます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

コピーが完了すると、注文の状態が "**完了**" に更新されます。

コピー元からデータを削除する前に、データが Azure にアップロードされていることを確認します。 データは次の場所にあります。

- お使いの Azure Storage アカウント。 データを Data Box にコピーする場合は、そのデータがタイプに応じて Azure Storage アカウントの次のいずれかのパスにアップロードされます。

  - ブロック BLOB およびページ BLOB の場合: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files の場合: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    あるいは、Azure Portal で Azure ストレージ アカウントにアクセスし、そこから移動することもできます。

- マネージド ディスク リソース グループ。 マネージド ディスクを作成するとき、VHD はページ BLOB としてアップロードされた後、マネージド ディスクに変換されます。 マネージド ディスクは、注文の作成時に指定されたリソース グループに接続されています。 

    - Azure のマネージド ディスクへのコピーが正常に完了した場合は、Azure portal の **[注文の詳細]** に移動して、マネージド ディスクに指定されているリソース グループをメモすることができます。

        ![マネージド ディスク リソース グループを識別する](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        メモしたリソース グループに移動し、目的のマネージド ディスクを見つけます。

        ![リソース グループに接続されているマネージド ディスク](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - VHDX または動的/差分 VHD をコピーした場合、VHDX/VHD はページ BLOB としてステージング ストレージ アカウントにアップロードされますが、VHD からマネージド ディスクへの変換は失敗します。 ステージング環境の **[ストレージ アカウント] > [BLOB]** に移動し、適切なコンテナー (Standard SSD、Standard HDD、または Premium SSD) を選択します。 VHD は、ステージング ストレージ アカウントにページ BLOB としてアップロードされます。

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box からデータを消去する

次の記事に進み、ローカル Web UI 経由で Data Box を管理する方法を学習します。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Azure Data Box を管理する](./data-box-local-web-ui-admin.md)


