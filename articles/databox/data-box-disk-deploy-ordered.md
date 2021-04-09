---
title: Azure Data Box Disk の注文方法のチュートリアル | Microsoft Docs
description: このチュートリアルでは、データを Azure にインポートするために、Azure Data Box Disk にサインアップして注文する方法を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: c83e46932e8d2a28d8503fe3fb6514988c364d97
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92125271"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>チュートリアル:Azure Data Box Disk を注文する

Azure Data Box Disk は、迅速かつ簡単な信頼性の高い方法でオンプレミス データを Azure にインポートできるハイブリッド クラウド ソリューションです。 Microsoft が提供するソリッドステート ディスク (SSD) にデータを転送し、ディスクを返送します。 その後、このデータは Azure にアップロードされます。

このチュートリアルでは、Azure Data Box Disk を注文する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * Data Box Disk を注文する
> * 注文を追跡する
> * 注文をキャンセルする

## <a name="prerequisites"></a>前提条件

デプロイする前に、Data Box サービスと Data Box Disk に関する次の構成の前提条件を完了してください。

### <a name="for-service"></a>サービスでは

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>デバイスでは

開始する前に次の点を確認します。

* データのコピー元として使用できるクライアント コンピューターがあること。 クライアント コンピューターの要件は以下のとおりです。
  * [サポート対象のオペレーティング システム](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)が実行されていること。
  * Windows クライアントである場合は、その他の[必須ソフトウェア](data-box-disk-system-requirements.md#other-required-software-for-windows-clients)がインストールされていること。  

## <a name="order-data-box-disk"></a>Data Box Disk を注文する

サインイン先:

* Data Box Disk を注文するための Azure portal (URL: https://portal.azure.com )。
* または、Azure Government ポータル (URL: https://portal.azure.us )。 詳細については、[ポータルを使用して Azure Government に接続する](../azure-government/documentation-government-get-started-connect-with-portal.md)方法に関するページを参照してください。

次の手順に従って、Data Box Disk を注文します。

1. ポータルの左上隅にある **[+ リソースの作成]** をクリックし、「*Azure Data Box*」を検索します。 **[Azure Data Box]** をクリックします。

   ![Azure Data Box 1 を検索する](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. **Create** をクリックしてください。

3. ご利用のリージョンで Data Box サービスが利用可能かどうかを確認します。 次の情報を入力または選択し、 **[適用]** をクリックします。

    ![Data Box Disk オプションを選択する](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |設定|値|
    |---|---|
    |サブスクリプション|Data Box サービスが有効になっているサブスクリプションを選択します。<br> サブスクリプションは、課金アカウントにリンクされます。 |
    |転送の種類| Azure へのインポート|
    |ソースの国または地域 | データが現在存在する国/地域を選択します。|
    |宛先 Azure リージョン|データを転送する Azure リージョンを選択します。|
  
4. **[Data Box Disk]** を選択します。 1 つの注文 (5 台のディスク) でのソリューションの最大容量は 35 TB です。 データ サイズがこれより大きい場合は、複数の注文を作成できます。

     ![Data Box Disk オプションを選択する 2](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

5. **[注文]** で **注文の詳細** を指定します。 次の情報を入力または選択します。

    |設定|値|
    |---|---|
    |名前|注文を追跡するためのフレンドリ名を指定します。<br> 名前の長さは 3 ～ 24 文字で、文字、数字、ハイフンを使うことができます。 <br> 名前の最初と最後は、文字か数字とする必要があります。 |
    |Resource group| 既存のグループを使用するか、新しいグループを作成します。 <br> リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。 |
    |宛先 Azure リージョン| ストレージ アカウントのリージョンを選択します。<br> 現時点では、米国、西ヨーロッパ、北ヨーロッパ、カナダ、およびオーストラリアにおけるすべてのリージョンのストレージ アカウントがサポートされています。 |
    |推定データ サイズ (TB 単位)| 推定サイズを TB 単位で入力します。 <br>データ サイズに基づいて、Microsoft は適切な数の 8 TB SSD (使用可能な容量は 7 TB) を発送します。 <br>5 台のディスクの最大使用可能容量は最大 35 TB です。 |
    |ディスク パスキー| **[Azure 生成のパスキーではなくカスタム キーを使用]** をオンにした場合は、ディスク パスキーを指定します。 <br> 少なくとも 1 つの数字と 1 つの特殊文字を含む 12 から 32 文字の英数字で構成されたキーを入力します。 使用できる特殊文字は `@?_+` です。 <br> このオプションをスキップし、Azure によって生成されたパスキーを使用してディスクをロック解除することができます。|
    |保存先     | ストレージ アカウント、マネージド ディスク、またはその両方から選択します。 <br> 指定した Azure リージョンに基づいて、既存のストレージ アカウントのフィルター処理された一覧からストレージ アカウントを選択します。 Data Box Disk は 1 つのストレージ アカウントのみにリンクできます。 <br> 新しい **汎用 v1 アカウント**、**汎用 v2 アカウント**、または **Blob Storage アカウント** を作成することもできます。 <br>仮想ネットワークに対するストレージ アカウントがサポートされます。 セキュリティで保護されたストレージ アカウントと Data Box サービスとを連携させるには、ストレージ アカウントのネットワーク ファイアウォール設定内で、信頼できるサービスを有効にします。 詳細については、[Azure Data Box を信頼できるサービスとして追加する](../storage/common/storage-network-security.md#exceptions)方法を参照してください。|

    保存先としてストレージ アカウントを使用している場合は、以下のようなスクリーンショットが表示されます。

    ![ストレージ アカウントの Data Box Disk 注文](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Data Box Disk を使用してオンプレミスの VHD からマネージド ディスクを作成する場合は、次の情報も指定する必要があります。

    |設定  |値  |
    |---------|---------|
    |Resource group     | オンプレミスの VHD からマネージド ディスクを作成する場合は、新しいリソース グループを作成します。 既存のリソース グループを使用するのは、Data Box サービスによってマネージド ディスク用にそのグループが Data Box Disk 注文に対して作成された場合だけにしてください。 <br> サポートされているリソース グループは 1 つのみです。|

    ![マネージド ディスクの Data Box Disk 注文](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    マネージド ディスクに指定されたストレージ アカウントは、ステージング ストレージ アカウントとして使用されます。 Data Box サービスで、VHD がステージング ストレージ アカウントにアップロードされ、マネージド ディスクに変換され、リソース グループに移動されます。 詳細については、「[Azure へのデータのアップロードを確認する](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure)」を参照してください。

6. **[次へ]** をクリックします。

    ![注文の詳細を指定する](media/data-box-disk-deploy-ordered/data-box-order-details.png)

7. **[配送先住所]** タブで、氏名、会社の名前と郵送先住所、有効な電話番号を入力します。 **[住所の確認]** をクリックします。 配送先住所でサービスが利用可能かどうかが確認されます。 指定した配送先住所でサービスを利用できる場合は、その旨が通知されます。

   注文が処理されると、電子メール通知が届きます。 自己管理の出荷を選択した場合、[自己管理の出荷の使用](data-box-disk-portal-customer-managed-shipping.md)に関するページをご覧ください。

    ![配送先住所を指定する](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
8. **[通知の詳細]** で、電子メール アドレスを指定します。 指定した電子メール アドレスに、注文の状態の更新に関する電子メール通知が送信されます。

    グループの管理者が辞めた場合も引き続き通知を受け取ることができるように、グループ メールを使用することをお勧めします。

9. 注文、連絡先、通知、プライバシー条項に関する情報の **概要** を確認します。 プライバシー条項への同意に対応するボックスをオンにします。

10. **[注文]** をクリックします。 注文が作成されるまで数分かかります。

## <a name="track-the-order"></a>注文を追跡する

注文後、Azure portal から注文の状態を追跡できます。 目的の注文に移動し、 **[概要]** に移動して状態を表示します。 ポータルに、**注文済み** 状態のジョブが表示されます。

![注文済み状態の Data Box Disk](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png)

ディスクが使用できない場合は通知されます。 ディスクが使用可能な場合、Microsoft は出荷用のディスクを特定し、ディスク パッケージを準備します。 ディスクの準備中に、次のアクションが実行されます。

* BitLocker の AES-128 方式でディスクが暗号化されます。  
* ディスクへの未承認のアクセスを防ぐために、ディスクがロックされます。
* このプロセス中に、ディスクのロックを解除するパスキーが生成されます。

ディスクの準備が完了すると、ポータルに **処理済み** 状態の注文が表示されます。

その後、Microsoft がディスクを準備し、地域の運送業者を通じて発送します。 ディスクが出荷されたら、追跡番号が送信されます。 ポータルには、**出荷済み** 状態の注文が表示されます。

## <a name="cancel-the-order"></a>注文をキャンセルする

この注文をキャンセルするには、Azure portal で **[概要]** に移動し、コマンド バーの **[キャンセル]** をクリックします。

キャンセルが可能なのは、ディスクの注文後、出荷に向けて注文が処理されている間のみです。 注文が処理された後は、注文をキャンセルすることはできなくなります。

![注文をキャンセルする](media/data-box-disk-deploy-ordered/cancel-order1.png)

キャンセルされた注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * Data Box Disk を注文する
> * 注文を追跡する
> * 注文をキャンセルする

次のチュートリアルに進み、Data Box Disk を設定する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box Disk を設定する](./data-box-disk-deploy-set-up.md)