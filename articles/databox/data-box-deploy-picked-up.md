---
title: Azure Data Box の返送に関するチュートリアル | Microsoft Docs
description: Azure Data Box を Microsoft に送付する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 28666aaac4ec221acca00d937d54a753a4e6a055
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172680"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>チュートリアル:Azure Data Box の返送と Azure へのデータ アップロードの確認

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box の返送と Azure へのデータ アップロードの確認

::: zone-end

::: zone target="docs"

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

::: zone-end

::: zone target="chromeless"

データのコピーが完了したら、デバイスを準備して発送します。 デバイスが Azure データセンターに到着すると、データが自動的に Azure にアップロードされます。

## <a name="prepare-to-ship"></a>発送の準備をする

発送を準備する前に、コピー ジョブが完了していることを確認します。

1. ローカル Web UI の **[出荷準備]** ページに移動し、出荷準備を開始します。 
2. ローカル Web UI からデバイスをオフにします。 ケーブルをデバイスから取り外します。 

次の手順は、デバイスを返送する場所によって決まります。

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box を返送する

デバイスへのデータ コピーを確実に完了しさせ、 **[発送の準備をする]** の実行を正常終了させます。 デバイスを発送する地域によっては、手順が異なります。

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[米国、カナダ、ヨーロッパ](#tab/in-us-canada-europe)

米国、カナダ、またはヨーロッパでデバイスを返送する場合は、次の手順を行います。

1. 必ずデバイスの電源をオフにてケーブルを取り外します。 
2. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. 配送先住所ラベルが電子インク ディスプレイに表示されていることを確認し、運送業者の集荷をスケジュールします。 ラベルを破損または紛失した場合、またはラベルが電子インク ディスプレイに表示されていない場合、Microsoft サポートにお問い合わせください。 サポートから指示された場合は、Azure portal の **[概要] > [出荷ラベルをダウンロード]** にアクセスしてください。 配送先住所ラベルをダウンロードして、デバイスに貼り付けます。 
4. デバイスを返送する場合は、UPS での集荷をスケジュールします。 集荷のスケジュールを設定するには:

    - 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
    - 電話で、電子インク ディスプレイまたは印刷ラベルに表示されている返送追跡番号を伝えます。
    - 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Data Box を持ち込むこともできます。
4. 運送業者によって Data Box が集荷され、スキャンされると、ポータルの注文状態は "**集荷されました**" に更新されます。 追跡 ID も表示されます。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australiatabin-australia"></a>[オーストラリア](#tab/in-australia)

オーストラリアの Azure データセンターには、追加のセキュリティ通知があります。 すべての国内配送には事前通知が必要です。 オーストラリアで発送するには、次の手順を行います。


1. 返品対象のデバイスを発送する場合に使用する元の箱を保管しておきます。
2. デバイスへのデータ コピーが完了し、 **[発送準備]** の実行が正常終了することを確認します。
3. デバイスの電源をオフにして、ケーブルを取り外します。
4. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
5. メールで Quantium Solutions に集荷を依頼します。 Azure portal で指定されたサービス参照番号を参照してください。 次のメール テンプレートを使用します: - *TAU コードが記載された返送用の配送先住所ラベルの依頼*。 メールには、必ず次の詳細情報を含めてください。 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions Australia から返送ラベルがメールで送られてきます。
7. 返品ラベルを印刷して、梱包箱に貼り付けます。
8. 荷物を宅配業者に渡します。

必要に応じて、Quantium Solutions のサポート (Azure@quantiumsolutions.com) にメールまたは電話でお問い合わせいただくことができます。

電話でご注文についてお問い合わせする場合:

- 最初に集荷に関するメールを送信します。
- 電話で注文の名前を伝えます。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-japantabin-japan"></a>[日本](#tab/in-japan) 

1. 返品対象のデバイスを発送する場合に使用する元の箱を保管しておきます。
2. デバイスの電源をオフにして、ケーブルを取り外します。
3. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
4. 伝票に送信元の情報としてお客様の会社名と住所の情報を記入します。
5. 次のメール テンプレートを使用して Quantium Solutions にメールを送信します。

    - 日本郵便の着払伝票が含まれていなかった場合、または紛失した場合は、このメールにそのことを記載します。 Quantium Solutions Japan が日本郵便に集荷を依頼し、集荷時に伝票を持って行くように伝えます。
    - 複数の注文がある場合は、必ず個別に集荷するようにメールを送信します。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. 集荷を予約した後、Quantium Solutions からメールの確認を受信します。 確認のメールには、着払伝票に関する情報も含まれています。

必要に応じて、次の情報で Quantium Solutions のサポート (日本語) にお問い合わせください。 

- メール: Customerservice.JP@quantiumsolutions.com 
- 電話: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



