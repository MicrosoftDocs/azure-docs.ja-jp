---
title: Microsoft Azure Data Box の FAQ | Microsoft Docs in data
description: 大量のデータを Azure に転送できるクラウド ソリューションである Azure Data Box について、よく寄せられる質問と回答を掲載しています。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8f090a3f07eba529fd39976545eb654083e637eb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561410"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: よく寄せられる質問

Microsoft Azure Data Box ハイブリッド ソリューションにより、転送デバイスを使用することで、迅速かつ安価で信頼性の高い方法で、数テラバイトのデータを Azure に送信できます。 この FAQ では、Azure portal で Data Box の使用に関して多く寄せられる質問とその回答を紹介しています。 

質問と回答は次の各カテゴリに分類されます。

- サービスについて
- デバイスの注文
- 構成と接続 
- 状態の追跡
- データをコピーする 
- デバイスの配送
- データの確認とアップロード 
- 生産物流管理のサポート

## <a name="about-the-service"></a>サービスについて

### <a name="q-what-is-azure-data-box-service"></a>Q. Azure Data Box サービスとは何ですか? 
A.  Azure Data Box サービスは、オフラインでのデータ インジェストを意図して設計されています。 このサービスでは、データ転送用にすべて調整された、さまざまなストレージ容量の製品各種を管理します。 

### <a name="q-what-is-azure-data-box"></a>Q. Azure Data Box とは何ですか?
A. Azure Data Box を使用すると、テラバイト単位のデータを迅速、安価かつ安全に Azure に転送できます。 Data Box デバイスは、Azure portal から注文します。 Microsoft は、80 TB の容量が使用できるストレージ デバイスを地域の運送業者経由で配送しています。 

デバイスを受け取ったら、ローカル Web UI を使用してすぐに設定できます。 データをサーバーからデバイスにコピーし、デバイスを Azure に返送します。 そのデータは、Azure のデータセンターにてデバイスから Azure へと自動的にアップロードされます。 プロセス全体は、Azure portal の Data Box サービスでエンド ツー エンドで追跡できます。

### <a name="q-when-should-i-use-data-box"></a>Q. Data Box はどのようなときに使用すべきでしょうか?
A. Azure に転送するデータが 40 TB から 500 TB であるならば、Data Box を有効活用できるでしょう。 データ サイズが 40 TB 未満の場合は Data Box Disk を使用し、500 TB を超える場合は [Data Box Heavy](data-box-heavy-overview.md) にサインアップしてください。

### <a name="q-what-is-the-price-of-data-box"></a>Q. Data Box の価格を教えてください。
A. Data Box は 10 日間の標準料金でご利用いただけます。 Azure portal で注文を作成中に製品モデルを選択すると、デバイスの料金が表示されます。 配送も無料ですが、Azure のストレージについては料金が適用されます。 詳細については、[Azure Data Box の価格](https://azure.microsoft.com/pricing/details/storage/databox/)に関するページを参照してください。 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Q. Data Box の 1 回のやり取りで転送できるデータの最大量を教えてください。
A. Data Box の原容量は 100 TB であり、使用可能な容量は 80 TB です。 Data Box により、最大 80 TB のデータを転送できます。 より多くのデータを転送するには、さらに多くのディスクを注文する必要があります。

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Q. どのようにしたら自分のリージョンで Data Box が利用できるかを確認できますか? 
A.  Data Box を利用できる国や地域については、「[利用可能なリージョン](data-box-overview.md#region-availability)」をご覧ください。  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Q. Data Box では、どのリージョンでデータを保存できますか?
A. Data Box は、米国内のすべてのリージョン、西ヨーロッパ、北ヨーロッパ、フランス、英国、日本、オーストラリア、およびカナダでサポートされています。 詳細については、「[Region availability (利用可能なリージョン)](data-box-overview.md#region-availability)」をご覧ください。

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>Q. Data Box に関して何か問題が発生した場合、どこに連絡すればよいですか?
A. Data Box に関して何か問題が発生した場合は、[Microsoft サポート](data-box-disk-contact-microsoft-support.md)にお問い合わせください。

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>Q. Data Box を紛失しました。 デバイスを紛失した場合に料金は発生しますか?
A. はい。 デバイスを紛失したり破損したりした場合は料金が発生します。 この料金については、[価格のページ](https://azure.microsoft.com/pricing/details/storage/databox/)および[製品のサービス利用規約](https://www.microsoft.com/licensing/product-licensing/products)内で説明されています。


## <a name="order-device"></a>デバイスの注文

### <a name="q-how-do-i-get-data-box"></a>Q. Data Box の入手方法は? 
A.  Azure Data Box を入手するには、Azure portal にサインインし、Data Box の注文を作成します。 連絡先情報と通知の詳細を指定してください。 発注後、在庫状況に応じて、10 日以内に Data Box が配送されます。 詳細については、[Data Box の注文](data-box-deploy-ordered.md)に関する記事を参照してください。

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>Q. Azure portal で Data Box の注文を作成できませんでした。 なぜでしょうか?
A. Data Box の注文を作成できなかった場合は、サブスクリプションの種類またはアクセスのいずれかに問題があります。 

サブスクリプションを確認してください。 Data Box は、マイクロソフトエンタープライズ契約 (EA) とクラウド ソリューション プロバイダー (CSP) のサブスクリプション プランでのみ利用できます。 サブスクリプションが上記のいずれのタイプにも該当しない場合は、Microsoft サポートに連絡してサブスクリプションをアップグレードしてください。

サポートされている種類のサブスクリプション プランをお持ちの場合は、サブスクリプションのアクセス レベルを確認してください。 注文を作成するには、サブスクリプションの共同作成者または所有者である必要があります。

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>Q. Data Box デバイスをいくつか注文しました。 追加注文を作成できません。 なぜでしょうか?
A. 商取引の境界 (選択された国とリージョンの組み合わせ) ごとに、サブスクリプションあたり最大 5 件まで、有効な注文を作成できます。 デバイスをさらに注文する必要がある場合は、Microsoft サポートに連絡してサブスクリプションの上限を増やしてください。

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Q. 注文を作成しようとすると、Data Box サービスが利用できないという通知を受け取ります。 これはどういう意味でしょうか。
A. これは、選択した国とリージョンの組み合わせでは Data Box サービスは利用できないことを意味しています。 この組み合わせを変更すると、Data Box サービスを利用できるようになるでしょう。 サービスが利用できるリージョンの一覧については、[Data Box が利用可能なリージョン](data-box-overview.md#region-availability)に関する記事を参照してください。

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Q. 数日前に Data Box を注文しました。 Data Box はいつ届きますか?
A. ご注文をいただくと、デバイスの在庫状況が確認されます。 デバイスが在庫にある場合は、10 日以内に配送されます。 需要が高い時期があることが考えられます。 この状況では、注文は待ち行列に入れられることになり、Azure portal で状況の変化を追跡できます。 90 日以内に注文が履行されない場合、その注文は自動的にキャンセルされます。

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Q. 自分の Data Box がデータでいっぱいになっており、もうひとつ注文する必要があります。 簡単に注文する方法はありますか?
A. 前回の注文を複製することができます。 複製することで前回と同じ注文が作成されるので、注文の詳細のみを編集すればよく、住所や連絡先、通知の詳細を入力する必要はありません。

## <a name="configure-and-connect"></a>構成と接続

### <a name="q-how-do-i-unlock-the-data-box"></a>Q. Data Box をロック解除する方法を教えてください。 
A.  Azure portal で、対象の Data Box の注文に移動し、 **[デバイスの詳細]** に移動します。 ロック解除パスワードをコピーします。 Data Box で、このパスワードを使用してローカル Web UI にログインします。 詳細については、[チュートリアルの Azure Data Box の開梱、配線、接続](data-box-deploy-set-up.md)に関するページを参照してください。

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Q. Linux ホスト コンピューターを使用して、Data Box に接続してデータをコピーすることはできますか?
A.  はい。 Data Box を使用して、SMB クライアントと NFS クライアントに接続できます。 詳細については、ホスト コンピューターに関して[サポートされるオペレーティング システム](data-box-system-requirements.md)の一覧を参照してください。

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. Data Box は配送されましたが、この注文をキャンセルしようと思っています。 [キャンセル] ボタンが利用できないのはなぜでしょうか?
A.  注文をキャンセルできるのは、Data Box を注文してから、注文が処理される前までです。 Data Box の注文が処理されると、注文はキャンセルできなくなります。 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Q. Data Box を複数のホスト コンピューターに同時に接続して、データを転送することはできますか?
A. はい。 複数のホスト コンピューターを Data Box に接続してデータを転送したり、複数のコピー ジョブを並列で実行することができます。 詳細については、[チュートリアルの Azure Data Box へのデータのコピー](data-box-deploy-copy-data.md)に関するページを参照してください。

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Q. Data Box の両方の 10 GbE インターフェイスに接続してデータを転送できますか?
A. はい。 Data Box の両方の 10 GbE インターフェイスに接続して、同時にデータをコピーすることができます。 データをコピーする方法の詳細については、[チュートリアルの Azure Data Box へのデータのコピー](data-box-deploy-copy-data.md)に関するページを参照してください。

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Q. 前面操作パネルで、システム障害インジケーター LED が点灯しています。 どうすればよいですか。
A. システム障害インジケーター LED が点灯している場合、システムは正常でないことを示しています。 [Microsoft サポート](data-box-disk-contact-microsoft-support.md) に対処法をお問い合わせください。

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>Q. Azure portal で、Data Box のロック解除パスワードが入手できません。 なぜでしょうか?
A. Azure portal でロック解除パスワードが入手できない場合は、サブスクリプションとストレージ アカウントでアクセス許可を確認してください。 リソース グループ レベルで、共同作成者または所有者の権限を所有していることを確認してください。 権限がない場合は、アクセス資格情報を表示するために、少なくとも 1 つの Data Box Operator の役割権限を所有している必要があります。

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Q. Data Box では、ポート チャネル構成がサポートされていますか。 MPIO はどうですか。
A. Data Box では、ポート チャネル構成、マルチパス IO (MPIO) 構成、または vLAN 構成はサポートされていません。

## <a name="track-status"></a>状態の追跡

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Q. 注文してからデバイスを返送するまで、Data Box を追跡する方法を教えてください。 
A.  Data Box の注文状況は Azure portal で追跡できます。 注文を作成する際に、通知メールを指定するように求められます。 指定した場合は、注文の状態が変化するたびにメールで通知されます。 [通知メールの構成](data-box-portal-ui-admin.md#edit-notification-details)方法に関するページを参照してください。

### <a name="q-how-do-i-return-the-device"></a>Q. デバイスの返送方法を教えてください。 
A.  Microsoft では、E インク表示に配送先住所ラベルを表示します。 配送先住所ラベルが E インク表示に表示されない場合は、 **[概要] > [配送先住所ラベルのダウンロード]** に移動します。 ラベルをダウンロードして印刷し、デバイスの透明なプラスチック タグに挿入して、そのデバイスを集荷場所に持ち込みます。 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Q. デバイスが Azure データセンターに到着したという電子メール通知を受け取りました。 データのアップロードが進行中であるかどうかを確認する方法を教えてください。
A. Azure portal では、Data Box の注文に移動し、**概要**に移動できます。 Azure へのデータ アップロードが開始されている場合は、右側のペインでコピーの進行状況が表示されます。 

## <a name="migrate-data"></a>データの移行

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Q. Data Box で使用できる最大データ サイズを教えてください。  
A.  Data Box では、80 TB のストレージ容量を使用できます。 データ サイズが 40 TB から 80 TB の場合、1 台の Data Box デバイスを使用できます。 500 TB までのより大きなデータ サイズの場合、複数の Data Box デバイスを注文できます。 データ サイズが 500 TB を超える場合は、Data Box Heavy にサインアップしてください。  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Q. Data Box でサポートされるブロック BLOB とページ BLOB の最大サイズを教えてください。 
A.  最大サイズは Azure Storage の上限によって左右されます。 最大ブロック BLOB は約 4.768 TiB、ページ BLOB の最大サイズは 8 TiB です。 詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../storage/common/storage-scalability-targets.md)」をご覧ください。 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 移動中のデータのセキュリティは、どのようにして確保されているのですか? 
A. 移動中に Data Box が確実に保護されるように、いくつかのセキュリティ機能が実装されています。 これらの中には、開封明示シール、ハードウェアとソフトウェアの改ざん検出、デバイスのロック解除パスワードなどがあります。 詳細については、「[Azure Data Box のセキュリティとデータ保護](data-box-security.md)」を参照してください。

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Q. Data Box にはどのようにデータをコピーするのですか? 
A.  SMB クライアントを使用している場合、Robocopy や Diskboss などの SMB コピー ツールを使用して、または Windows ファイル エクスプローラーのドラッグ アンド ドロップでも、データをデバイスにコピーできます。 

NFS クライアントを使用している場合は、[rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/)、または [Ultracopier](https://ultracopier.first-world.info/) を使用できます。 

詳細については、[チュートリアルの Azure Data Box へのデータのコピー](data-box-deploy-copy-data.md)に関するページを参照してください。

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. データのコピーを高速化するうえで何かヒントはありますか?
A.  コピー処理は、次の方法で高速化できます。

- データ コピーのストリームを複数使用する。 たとえば、Robocopy でマルチスレッド オプションを使用します。 使用される正確なコマンドについては、「[チュートリアル: Azure Data Box にデータをコピーして確認する](data-box-deploy-copy-data.md)」を完了していることを確認してください。
- 複数のセッションを使用する。
- ネットワーク共有 (ネットワーク速度によって制限される可能性のある場所) 経由でコピーする代わりに、Data Box が接続されているコンピューターにデータがローカルに存在していることを確認してください。
- データのコピーに使用するコンピューターのパフォーマンスをベンチマークする。 [Bluestop FIO ツール](https://bluestop.org/fio/)をダウンロードします。このツールを使って、サーバーのハードウェアのパフォーマンスをベンチマークしてください。

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Q. Data Box で複数のストレージ アカウントを使用することはできますか?
A.  はい。 Data Box では、最大 10 件のストレージ アカウント、汎用、クラシック、または BLOB ストレージがサポートされています。 ホット BLOB とクール BLOB の両方がサポートされています。 


## <a name="ship-device"></a>デバイスの配送

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Q. 自分のデバイスが届けられましたが、破損しているようです。 どうすればよいですか。
A. デバイスが到着時に破損していたり、改ざんの兆候がある場合は、デバイスを使用しないでください。 [Microsoft サポートに連絡して](data-box-disk-contact-microsoft-support.md)、早急にデバイスを返却してください。 デバイスを交換する場合は、新しい Data Box の注文を作成することもできます。 この場合、交換用のデバイスには請求されません。

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>Q. 自身の運送業者を使って Data Box を配送できますか?
A. Data Box サービスでは、Microsoft が Azure データセンターとの間の配送を扱います。 自身の運送業者を使用する場合は、Azure インポート/エクスポート サービスを使用できます。 詳細については、「[Azure インポート/エクスポート サービスの概要](../storage/common/storage-import-export-service.md)」を参照してください。

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>Q. Data Box デバイスは配送中に国境を越えますか?
A. すべての Data Box デバイスは配送先と同じ国内から出荷され、国境を越えることはありません。 欧州連合 (EU) 域内の注文は唯一の例外であり、EU のどの国にも、また EU のどの国からもデバイスを配送できます。 これは、Data Box デバイスと Data Box Heavy デバイスの両方に適用されます。

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Q. E インク表示が返品ラベルを示していません。 どうすればよいですか。
A. E インク表示が返品ラベルを示していない場合は、次の手順を実行します。
- 以前の配送から古い配送先住所ラベルとステッカーを取り外します。
- Azure portal で注文に移動します。 **[概要]** および **[配送先住所ラベルのダウンロード]** に移動します。 詳細については、「[配送先住所ラベルのダウンロード](data-box-portal-admin.md#download-shipping-label)」を参照してください。
- 配送先住所ラベルを印刷し、デバイスに添付されている透明なプラスチック スリーブに挿入します。 
- 配送先住所ラベルがはっきりと見えることを確認してください。 

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 移動中のデータは、どのようにして保護されるのですか? 
A.  移動中では、Data Box の次の機能によりデータを保護できます。
 - Data Box ディスクは、AES 256 ビットの暗号化で暗号化されます。 
 - デバイスはロックされ、データにアクセスするにはロック解除パスワードを入力する必要があります。
詳細については、「[Data Box のセキュリティ機能](data-box-security.md)」を参照してください。  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>Q. [配送の準備] が完了しており、デバイスをシャット ダウンします。 それでも Data Box にデータをさらに追加できますか?
A. はい。 デバイスを起動して、データをさらに追加できます。 データのコピーが完了したら、 **[配送の準備]** をもう一度実行する必要があります。

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Q. デバイスは届いたのですが、起動しません。 返送する方法を教えてください。
A. デバイスが起動しない場合は、Azure portal で、該当する注文に移動してください。 配送先住所ラベルをダウンロードして、デバイスに貼り付けます。 詳細については、「[配送先住所ラベルのダウンロード](data-box-portal-admin.md#download-shipping-label)」を参照してください。

## <a name="verify-and-upload"></a>確認とアップロード

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Q. Data Box を返送したら、Azure 内の自分のデータにすぐにアクセスできますか? 
A.  **データのコピー**の注文状況が**完了**として表示されたら、データにはすぐにアクセスできるはずです。

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. アップロード後、私のデータは Azure 内のどこに置かれるのですか?
A.  データを Data Box にコピーする場合、そのデータがブロック BLOB、ページ BLOB、または Azure ファイルのいずれであるかに応じて、データは Azure Storage アカウントの次のいずれかのパスにアップロードされます。
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  あるいは、Azure Portal で Azure ストレージ アカウントにアクセスし、そこから移動することもできます。

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. コンテナーに付けた名前が Azure の要件に従っていないことに気付きました。 Azure へのデータのアップロードは失敗しますか?
A.  コンテナー名に大文字が使われている場合、それらの名前は自動的に小文字へと変換されます。 名前が他の方式に準拠していない場合 (特殊文字やその他の言語など)、アップロードは失敗します。 共有、コンテナー、ファイルの名前付けに関するベスト プラクティスの詳細については、次を参照してください。
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (共有の名前付けと参照)
- [ブロック BLOB とページ BLOB の規則](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Q. Data Box にコピーしたデータを検証する方法を教えてください。
A.  データのコピーが完了したら、 **[配送の準備]** を実行すると、データが検証されます。 Data Box は、検証の処理中にファイルのリストとデータのチェックサムを生成します。 ファイルのリストをダウンロードすれば、ソース データ内のファイルに対してリストを検証できます。 詳細については、「[配送の準備](data-box-deploy-picked-up.md#prepare-to-ship)」を参照してください。

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>Q. Data Box を返却した後に、私のデータはどうなりますか?
A.  Azure へのデータ コピーが完了すると、NIST SP 800-88 リビジョン 1 のガイドラインに従って、Data Box 上のディスクから、データは安全に消去されます。 詳細については、「[Data Box からのデータの消去](data-box-deploy-picked-up.md#erasure-of-data-from-data-box)」を参照してください。

## <a name="audit-report"></a>監査レポート

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Azure Data Box サービスでは、生産物流管理の手順をどのように支援して顧客をサポートしますか?
A.  Azure Data Box サービスでは、生産物流管理のドキュメントのために使用できるレポートを以前から提供しています。 Azure では、監査ログとコピー ログがストレージ アカウントで利用できます。注文が完了した後では、Azure portal で[注文履歴をダウンロード](data-box-portal-admin.md#download-order-history)できます。


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>生産物流管理をサポートするために、どのようなレポートが利用できますか?
A.  生産物流管理をサポートするために、次のレポートが利用できます。

- UPS からの物流輸送。
- 電源投入とユーザー共有アクセスのログ。
- Data Box に正常に取り込まれた各ファイルに対する、64 ビット巡回冗長検査 (CRC-64) またはチェックサムによるマニフェスト ファイル。
- Azure ストレージ アカウントにアップロードできなかったファイルのレポート。
- データが Azure ストレージ アカウントにコピーされた後での、Data Box デバイスのサニタイズ (NIST 800 88R1 規格に準拠)。

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>運送業者の追跡ログは (UPS から) 利用できますか。 
A.  運送業者の追跡ログは、Data Box の注文履歴に取り込まれます。 このレポートは、デバイスが Azure データセンターに返却され、デバイス ディスク上のデータがクリーン アップされた後に利用できます。 すぐに必要な場合は、注文追跡番号で運送業者の Web サイトに直接アクセスすれば、追跡情報を入手することもできます。

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Data Box  を Azure データセンターに移送できますか? 
A.  いいえ。 現在のところ、Azure データセンターでは、顧客または UPS 以外の運送業者からの Data Box の配送を受け付けていません。


## <a name="next-steps"></a>次の手順

- [Data Box のシステム要件](data-box-system-requirements.md)を確認する。
- [Data Box の制限事項](data-box-limits.md)を理解する。
- Azure portal で [Azure Data Box](data-box-quickstart-portal.md) をすばやく展開する。
