---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/19/2019
ms.author: alkohli
ms.openlocfilehash: a23b0b2c71207bf84a4938d54a78a62efb6cbcbd
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172646"
---
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

        ![マネージド ディスク リソース グループを識別する](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        メモしたリソース グループに移動し、目的のマネージド ディスクを見つけます。

        ![リソース グループに接続されているマネージド ディスク](media/data-box-verify-upload-return/managed-disks-resource-group.png)

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
> [ローカル Web UI を使用して Azure Data Box を管理する](../articles/databox/data-box-local-web-ui-admin.md)