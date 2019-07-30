---
title: Azure Data Box Heavy の返送に関するチュートリアル | Microsoft Docs
description: Azure Data Box Heavy を Microsoft に送付する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 84db33e4c7ac612353c590ac9d2904ac3bc48d38
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592386"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>チュートリアル:Azure Data Box Heavy の返送と Azure へのデータ アップロードの確認


このチュートリアルでは、Azure Data Box Heavy を返送し、Azure にアップロードされたデータを確認する方法について説明します。

このチュートリアルでは、次のようなトピックについて説明します。

> [!div class="checklist"]
> * 前提条件
> * 発送の準備をする
> * Data Box Heavy を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box Heavy からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

- [Azure Data Box の設定に関するAzure Data Box にデータをコピーして確認する](data-box-heavy-deploy-copy-data.md)」を完了していることを確認してください。
- コピージョブが完了していること。 コピージョブが進行中の場合、発送準備を行うことはできません。

## <a name="prepare-to-ship"></a>発送の準備をする

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Data Box Heavy の返送

1. デバイスの電源をオフにして、すべてのケーブル類を取り外します。 4 つの電源コードをまとめ、デバイスの背面からアクセスできるトレイにしっかりと格納します。
2. FedEx (米国) または DHL (EU) の小口トラック輸送でデバイスを返送します。

    1. [Data Box 運用チーム](mailto:DataBoxOps@microsoft.com)に連絡して集荷について通知し、返送ラベルを入手します。
    2. 運送業者の現地の電話番号にかけて、集荷のスケジュールを設定します。
    3. 返送ラベルは、必ず外箱の目立つ位置に貼付してください。
    4. 以前の出荷の古い配送先住所ラベルは必ずデバイスから取り除いてください。
3. 運送業者によって Data Box Heavy が集荷され、スキャンされると、ポータルの注文状態は **[集荷されました]** に更新されます。 追跡 ID も表示されます。

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

Microsoft がディスクを受け取ってスキャンすると、注文の状態は "**受取済み**" に更新されます。 その後、デバイスの損傷または改ざんの印がないか物理的に検証されます。

検証が完了すると、Data Box Heavy は Azure データセンターのネットワークに接続されます。 データのコピーが自動的に開始されます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

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

## <a name="erasure-of-data-from-data-box-heavy"></a>Data Box Heavy からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。 消去が完了した後、[注文履歴をダウンロード](data-box-portal-admin.md#download-order-history)できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件
> * 発送の準備をする
> * Data Box Heavy を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box Heavy からデータを消去する

次の記事に進み、ローカル Web UI 経由で Data Box Heavy を管理する方法を学習します。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Azure Data Box を管理する](./data-box-local-web-ui-admin.md)


