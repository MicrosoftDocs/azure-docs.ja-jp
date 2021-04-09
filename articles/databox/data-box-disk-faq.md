---
title: Microsoft Azure Data Box Disk の FAQ | Microsoft Docs in data
description: 大量のデータを Azure に転送することを可能にするクラウド ソリューション、Azure Data Box Disk に関してよく寄せられる質問とその回答を取り上げます。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: 88aedb7daa375ae6b4a9107dceed1d25ed72ed92
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039106"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk:よく寄せられる質問

Microsoft Azure Data Box Disk クラウド ソリューションを使用すると、迅速かつ安価な信頼性の高い方法で、数テラバイトのデータを Azure に送信できます。 この FAQ では、Azure portal での Data Box Disk の使用に関して多く寄せられる質問とその回答を紹介しています。 

質問と回答は次の各カテゴリに分類されます。

- サービスについて
- 構成と接続 
- 状態の追跡
- データの移行 
- データの確認とアップロード 


## <a name="about-the-service"></a>サービスについて

### <a name="q-what-is-azure-data-box-service"></a>Q. Azure Data Box サービスとは何ですか? 
A.  Azure Data Box サービスは、オフラインでのデータ インジェストを意図して設計されています。 このサービスによって管理される一連の製品はすべて、異なる記憶域容量のデータ転送を想定してカスタマイズされています。 

### <a name="q-what-are-azure-data-box-disks"></a>Q. Azure Data Box Disk とは何ですか?
A. Azure Data Box Disk を使用すると、テラバイト級のデータを Azure との間で、迅速に、低コストで、かつ安全に転送することができます。 お客様には、1 台から 5 台 (最大記憶域容量 35 TB) のディスクが Microsoft から発送されます。 Azure portal で Data Box サービスを使用して、ディスクの構成、接続、ロック解除を簡単に行うことができます。  

Microsoft BitLocker ドライブ暗号化によってディスクが暗号化され、暗号化キーは Azure portal で管理されます。 その後、お客様のサーバーからデータをコピーしていただきます。 データセンターでは Microsoft が、プライベート ネットワークの高速なアップロード リンクを使用し、ドライブに格納されたお客様のデータを Azure にアップロードすることによって、そのデータをクラウドに移行します。

### <a name="q-when-should-i-use-data-box-disks"></a>Q. Data Box Disk はどのようなときに使用すべきでしょうか?
A. Azure に転送したいデータが 40 TB (またはそれより少なくても) あれば、Data Box Disk を有効活用できると考えられます。

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. Data Box Disk の価格を教えてください。
A. Data Box Disk の価格については、[価格に関するページ](https://azure.microsoft.com/pricing/details/databox/disk/)を参照してください。

### <a name="q-how-do-i-get-data-box-disks"></a>Q. Data Box Disk の入手方法は? 
A.  Data Box Disk を入手するには、Azure portal にログインし、Data Box ディスクの注文を作成します。 連絡先情報と通知の詳細を指定してください。 発注後、利用の可否に応じて、10 日以内にディスクが発送されます。

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. Data Box Disk の 1 回のやり取りで転送できる最大データ量を教えてください。
A. 1 台につき 8 TB の容量 (使用可能な容量は 7 TB) のディスクが 5 台で、使用可能な最大容量は 35 TB となります。 したがって、1 回のやり取りで転送できるデータは 35 TB となります。 それを超えるデータを転送するには、さらに多くのディスクを注文する必要があります。

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. 自分のリージョンで Data Box Disk が利用できるかどうかは、どうやって調べればよいでしょうか? 
A.  Data Box Disk が現在使用できる場所については、「[利用可能なリージョン](data-box-disk-overview.md#region-availability)」を参照してください。  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. Data Box Disk では、どのリージョンにデータを保存できますか?
A. Data Box Disk は、米国、カナダ、EU、英国、オーストラリア、シンガポール、インド、中国、香港、日本、韓国、南アフリカ内のすべてのリージョンでサポートされています。 サポートされるのは、Azure パブリック クラウド リージョンのみです。 Azure Government やその他のソブリン クラウドはサポートされません。

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>Q. ある国/地域の場所にあるソース データを別の国の Azure リージョンにインポートする方法はありますか?
A. Data Box Disk は配送先と同じ国/地域内でのデータ インジェストのみをサポートし、国境を越えることはありません。 欧州連合 (EU) 域内の注文は唯一の例外であり、EU のどの国にも、また EU のどの国/地域からも Data Box Disks を配送できます。

たとえばカナダの場所にあるデータを Azure 米国西部ストレージ アカウントに移行する場合、次のようにして実現できます。

#### <a name="option-1"></a>オプション 1:  

データを含む[サポートされているディスク](../import-export/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks)を、[Azure Import/Export サービス](../import-export/storage-import-export-service.md)を使用して、カナダのソースの場所から Azure 米国西部のデータセンターに発送します。

#### <a name="option-2"></a>オプション 2:

1. ストレージ アカウントを選択して (たとえば、カナダ中部など)、カナダで Data Box Disk を注文します。 SSD ディスクは、カナダ中部の Azure データセンターから、注文の作成時に指定された (カナダの) 出荷先住所に出荷されます。

2. オンプレミス サーバーからのデータをディスクにコピーした後、Microsoft から提供されている返却ラベルを使用して、カナダの Azure データセンターに返します。 Data Box Disk に存在するデータは、注文の作成時に選択したカナダの Azure リージョンの移行先のストレージ アカウントにアップロードされます。

3. その後、AzCopy などのツールを使用して、米国西部のストレージ アカウントにデータをコピーできます。 この手順では、Data Box Disk の課金に含まれていない [Standard Storage](https://azure.microsoft.com/pricing/details/storage/) と [帯域幅の料金](https://azure.microsoft.com/pricing/details/bandwidth/)が発生します。

#### <a name="q-does-data-box-disk-store-any-customer-data-outside-of-the-service-region"></a>Q. Data Box Disk によって、サービス リージョン外に格納される顧客データはありますか?

A. いいえ。 Data Box Disk によって、顧客データがサービス リージョン外に格納されることはありません。 顧客は、データの完全な所有権を持ち、注文の作成時に選択したストレージ アカウントに基づいて、指定された場所にデータを保存できます。  

顧客データに加えて、Data Box Disk のデータには、メタデータ、監視ログなどがあります。 データの損失を防ぐため、すべてのリージョン (ブラジル南部と東南アジアを除く) で、Data Box Disk のデータは geo 冗長ストレージ アカウントを使用して[ペアになっているリージョン](../best-practices-availability-paired-regions.md)に保管され、レプリケートされます。  

ブラジル南部と東南アジアでは、[データ所在地の要件](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information)により、Data Box のデータは 1 つのリージョンに含まれるようにゾーン冗長ストレージ (ZRS) アカウントに保管されます。 東南アジアの場合、Data Box Disk のすべてのデータはシンガポールに保管されます。ブラジル南部の場合、データはブラジルに保管されます。 

ブラジル南部と東南アジアでサービス停止が発生した場合、顧客は別のリージョンから新しい注文を作成できます。 新しい注文は、それらが作成されたリージョンから提供され、Data Box Disk の往復の発送はお客様が担当します。



### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Q. リージョン全体で障害が発生した場合にデータを回復するにはどうすればよいですか?

A. 大きな災害のために 1 つのリージョンが失われるような極端な状況では、Microsoft がリージョン間のフェールオーバーを開始できます。 この場合、ユーザーによる操作は必要ありません。 注文が同じ国または商取引の境界内にある場合は、フェールオーバー リージョンを通じて処理されます。 ただし、一部の Azure リージョンには、同じ地理的または商取引の境界内にペアのリージョンがありません。 これらのリージョンのいずれかに障害が発生した場合は、使用可能な別のリージョンから Data Box の注文を再作成し、この新しいリージョンの Azure にデータをコピーする必要があります。 詳しくは、「[ビジネス継続性とディザスター リカバリー (BCDR):Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)」をご覧ください。

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. Data Box Disk に関して何か問題が発生した場合、どこに連絡すればよいですか?
A. Data Box Disk に関して何か問題が発生した場合は、[Microsoft サポートに問い合わせ](./data-box-disk-contact-microsoft-support.md)てください。

## <a name="order-device"></a>デバイスの注文

### <a name="q-how-do-i-get-data-box-disk"></a>Q. Data Box Disk の入手方法は? 
A.  Azure Data Box Disk を入手するには、Azure portal にサインインし、Data Box Disk の注文を作成します。 連絡先情報と通知の詳細を指定してください。 発注後、在庫状況に応じて、10 日以内に Data Box Disk が配送されます。 詳細については、[Data Box の注文](data-box-disk-deploy-ordered.md)に関する記事を参照してください。

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>Q. Azure portal で Data Box Disk の注文を作成できませんでした。 なぜでしょうか。
A. Data Box Disk の注文を作成できない場合は、サブスクリプションの種類またはアクセスのいずれかに問題があります。

サブスクリプションを確認してください。 Data Box Disk は、エンタープライズ契約 (EA) とクラウド ソリューション プロバイダー (CSP) のサブスクリプション オファーでのみ利用できます。 これらの種類のサブスクリプションのいずれもお持ちでない場合は、Microsoft サポートに連絡してサブスクリプションをアップグレードしてください。

サポートされている種類のサブスクリプション プランをお持ちの場合は、サブスクリプションのアクセス レベルを確認してください。 注文を作成するには、サブスクリプションの共同作成者または所有者である必要があります。

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>Q. 注文の作成から Azure にデータがアップロードされるまでにはどのくらいの時間がかかりますか?

A. 注文処理のフェーズごとの推定リード タイムの目安は、以下のとおりです。  

これらのリード タイムは、"*推定*" です。 注文処理の各段階にかかる時間は、データセンターの負荷、同時実行されている注文、およびその他の環境条件の影響を受けます。

**Data Box Disk の注文の推定リード タイム:**

1. Data Box Disk の注文:数分間 (ポータルから)
2. ディスクの割り当てと準備:在庫状況と処理される保留中の注文の数に応じて、最大 5 営業日
3. 発送:2 から 3 営業日
4. お客様のサイトでのデータ コピー:データの性質、サイズ、ファイル数によって異なります。
5. 返却発送:2 から 3 営業日
6. データセンターでの処理と Azure へのアップロード:データのアップロードは、操作の処理が完了し、ディスクが接続されるとすぐにデータセンターから開始されます。 アップロード時間は、データの性質、サイズ、ファイル数によって異なります。

## <a name="configure-and-connect"></a>構成と接続
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. Data Box Disk の数を注文で指定することはできますか?
A.  いいえ。 データのサイズとディスクの在庫に応じて、8 TB のディスク (最大 5 台) が発送されます。  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. Data Box Disk のロックを解除する方法を教えてください。 
A.  Azure portal で、対象の Data Box Disk の注文に移動し、 **[デバイスの詳細]** に移動します。 パスキーをコピーします。 お使いのオペレーティング システム用の Data Box Disk ロック解除ツールを Azure portal からダウンロードして抽出します。 ディスクにコピーするデータがあるコンピューターでツールを実行します。 パスキーを指定すると、ディスクのロックが解除されます。 ロック解除に使用するパスキーは、すべてのディスクで共通です。 

詳細な手順については、[Windows クライアントでのディスクのロック解除](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)または [Linux クライアントでのディスクのロック解除](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)に関するページを参照してください。

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. Linux ホスト コンピューターを使用して、Data Box Disk に接続し、データをコピーすることはできますか?
A.  はい。 Linux と Windows のどちらのクライアントを使用しても、Data Box Disk に接続し、データをコピーできます。 詳細については、ホスト コンピューターに関して[サポートされるオペレーティング システム](data-box-disk-system-requirements.md)の一覧を参照してください。

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. ディスクは発送されましたが、この注文をキャンセルしようと思っています。 [キャンセル] ボタンが利用できないのはなぜでしょうか?
A.  注文をキャンセルできるのは、ディスクの注文後、発送される前までです。 ディスクが発送された後は、注文をキャンセルすることはできなくなります。 ただし、有料でディスクを返却することはできます。 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. ホスト コンピューターに複数の Data Box Disk を同時に接続してデータを転送することはできますか?
A. はい。 同じホスト コンピューターに複数の Data Box Disk を接続してデータを転送したり、複数のコピー ジョブを並列実行したりすることができます。

## <a name="track-status"></a>状態の追跡

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. ディスクの注文から返送までを追跡するにはどうすればよいですか? 
A.  Data Box Disk の注文の状態は、Azure portal で追跡できます。 注文を作成する際に、通知メールを指定するように求められます。 指定した場合は、注文の状態が変化するたびにメールで通知されます。 [通知メールの構成](data-box-portal-ui-admin.md#edit-notification-details)方法に関するページを参照してください。

### <a name="q-how-do-i-return-the-disks"></a>Q. ディスクの返送方法を教えてください。 
A.  Microsoft からの発送パッケージには、Data Box Disk と共に配送先住所ラベルが同梱されています。 このラベルを梱包箱に貼り付けて、封をしたパッケージを集荷場所に持ち込んでください。 ラベルを破損または紛失した場合は、 **[概要] > [出荷ラベルをダウンロード]** に移動して、新しい返送ラベルをダウンロードしてください。

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Data Box Disk の注文を自分で受け取ることはできますか? 自分が選んだ配送業者を使ってディスクを返送できますか?
A. はい。 また、Microsoft では、US Gov リージョンでのみ、自己管理型の発送に対応しています。 Data Box Disk を注文するときに、自己管理型の発送オプションを選択できます。 注文した Data Box Disk を受け取るには、次の手順を行います。
    
1. 発注後、注文が処理されてディスクが準備されます。 受け取りの準備ができたことを知らせるメールが届きます。 
2. 注文を受け取る準備ができたら、Azure portal 上で対象の注文に移動し、 **[概要]** ブレードに移動します。 
3. Azure portal にコードを含む通知が表示されます。 [Azure Data Box オペレーション チーム](mailto:adbops@microsoft.com)にメールを送信し、コードを知らせます。 チームから場所と受け取り日時が連絡されます。 電子メールの通知を受信した後、5 営業日以内にチームに電話する必要があります。

データのコピーと検証が完了したら、次の手順に従ってディスクを返送します。

1. データの検証が完了したら、ディスクの接続を解除します。 接続ケーブルを取り外してください。
2. すべてのディスクと接続ケーブルをエアー クッションで包んで梱包箱に詰めます。 アクセサリが不足している場合は、料金がかかることがあります。

    - 最初の配送時に使われていた梱包を再利用してください。 ディスクはエアー クッションでしっかりと包んで梱包することをお勧めします。
    - 箱内の物があまり動かないように、すき間が少なくなるようにしてください。
3. Azure portal 上で対象の注文の **[概要]** ブレードにアクセスします。 コードを含む通知が表示されます。
4. そのコードを使用して [Azure Data Box オペレーション チーム](mailto:adbops@microsoft.com)にメールを送信し、コードを知らせます。 いつどこにディスクを持ち込むかについての情報が提供されます。


## <a name="migrate-data"></a>データの移行

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. Data Box Disk で使用できる最大データ サイズは?  
A.  Data Box Disk ソリューションのディスク数は最大 5 台で、使用可能な最大容量は 35 TB です。 ディスク自体は 8 TB (7 TB を使用可能) です。

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. Data Box Disk でサポートされるブロック BLOB とページ BLOB の最大サイズを教えてください。 
A.  最大サイズは Azure Storage の上限によって左右されます。 最大ブロック BLOB は約 4.768 TiB、ページ BLOB の最大サイズは 8 TiB です。 詳細については、「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../storage/blobs/scalability-targets.md)」を参照してください。

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. Data Box Disk のデータ転送速度を教えてください。
A. USB 3.0 で接続したディスクでテストしたところ、ディスクのパフォーマンスは最大 430 MB/s でした。 実際の数値は、使用するファイルのサイズによって異なります。 ファイルのサイズが小さい場合は、パフォーマンスが低下する可能性があります。

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 移動中のデータのセキュリティは、どのようにして確保されているのですか? 
A.  Data Box Disk は BitLocker の AES-128 ビット方式を使って暗号化されており、そのパスキーは Azure portal でしか入手できません。 パスキーを入手するには、ご利用のアカウントの資格情報を使って Azure portal にログインします。 Data Box Disk ロック解除ツールを実行するときに、このパスキーを指定してください。

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. Data Box Disk にはどのようにしてデータをコピーするのですか? 
A.  `Robocopy` や `Diskboss` などの SMB コピー ツールを使用して、データをディスクにドラッグ アンド ドロップしてください。Windows ファイル エクスプローラーを使用することもできます。

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. データのコピーを高速化するうえで何かヒントはありますか?
A.  コピー処理は、次の方法で高速化できます。

- データ コピーのストリームを複数使用する。 たとえば、`Robocopy` でマルチスレッド オプションを使用します。 使用される正確なコマンドについては、「[チュートリアル: Azure Data Box Disk へのデータのコピーと検証](data-box-disk-deploy-copy-data.md#copy-data-to-disks)」を参照してください。
- 複数のセッションを使用する。
- ネットワーク共有経由でコピーすることは避け (ネットワーク速度によって制限される可能性があるため)、ディスクが接続されているコンピューターのローカルにデータを置くようにします。
- コピー処理全体を通して USB 3.0 以降を使用する。 [`USBView` ツール](/windows-hardware/drivers/debugger/usbview)をダウンロードします。このツールを使って、コンピューターに接続されている USB コントローラーと USB デバイスを特定してください。
- データのコピーに使用するコンピューターのパフォーマンスをベンチマークする。 [`Bluestop``FIO` ツール](https://ci.appveyor.com/project/axboe/fio)をダウンロードし、これを使って、サーバーのハードウェアのパフォーマンスをベンチマークしてください。 最新の x86 または x64 ビルドを選択し、 **[Artifacts]\(成果物\)** タブを選択して、MSI をダウンロードします。

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. コピー元のデータに小さいファイル (数キロバイトまたは数メガバイト) が含まれている場合に、データの転送速度を高める方法を教えてください。
A.  コピー処理は、次の方法で高速化できます。

- 高速なストレージ上にローカル VHDx を作成するか、または (速度は低くなりますが) HDD/SSD 上に空の VHD を作成します。
- それを VM にマウントします。
- その VM のディスクにファイルをコピーします。

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. Data Box Disk で複数のストレージ アカウントを使用することはできますか?
A.  いいえ。 現在 Data Box Disk でサポートされているストレージ アカウントは 1 つだけです (汎用またはクラシック)。 ホット BLOB とクール BLOB の両方がサポートされています。

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>Q. 自分のデータに使用できる Data Box Disk 用ツールセットには何が含まれていますか。
A. Data Box Disk で使用できるツールセットには、次の 3 つのツールが含まれています。
 - **Data Box Disk ロック解除ツール**:このツールは、Microsoft から出荷された暗号化ディスクのロックを解除するために使用します。 ツールを使用してディスクのロックを解除するときは、Azure portal での Data Box Disk の注文に関して取得できるパスキーを指定する必要があります。 
 - **Data Box Disk 検証ツール**:このツールは、Azure の命名規則に従ってサイズ、形式、および BLOB 名を検証するために使用します。 コピーしたデータのチェックサムも生成されます。これは、Azure にアップロードされたデータを検証するために使用されます。
 - **Data Box Disk 分割コピー ツール**:このツールは、複数のディスクを使用していて、大きなデータセットをそれらのすべてのディスクに分割してコピーする必要がある場合に使用します。 このツールは、現在 Windows で使用できます。 このツールはマネージド ディスクではサポートされません。 このツールでは、コピー時にデータが検証されるため、このツールを使用するときは検証手順をスキップできます。

このツールセットは、Windows と Linux の両方で使うことができます。 ツールセットは、次の場所からダウンロードできます。
- [Windows 用 Data Box Disk ツールセットをダウンロードする](https://aka.ms/databoxdisktoolswin) 
- [Linux 用 Data Box Disk ツールセットをダウンロードする](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>Q. Data Box Disk を使用して Azure Files にデータを転送した後、そのデータを Azure File Sync で使用することはできますか? 
A. Azure Files は Data Box Disk でサポートされていますが、Azure File Sync では適切に機能しません。そのファイルのデータを Azure File Sync で使用する場合、メタデータは維持されません。


## <a name="verify-and-upload"></a>確認とアップロード

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. ディスクを返送した後、どのぐらいで Azure にある自分のデータにアクセスできるようになりますか? 
A.  データ コピーの注文の状態が完了として表示されたら、すぐにデータにアクセスすることができます。

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. アップロード後、私のデータは Azure 内のどこに置かれるのですか?
A.  ディスク上の *BlockBlob* フォルダーと *PageBlob* フォルダーにデータをコピーすると、*BlockBlob* フォルダーと *PageBlob* フォルダーのサブフォルダーごとのコンテナーが Azure Storage アカウントに作成されます。 *BlockBlob* と *PageBlob* フォルダーの直下にファイルをコピーした場合、そのファイルは Azure Storage アカウントの *$root* という既定のコンテナーに置かれます。 データを *AzureFile* フォルダー以下のフォルダーにコピーすると、ファイル共有が作成されます。

### <a name="q-i-just-noticed-that-i-didnt-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. コンテナーに付けた名前が Azure の要件に従っていないことに気付きました。 Azure へのデータのアップロードは失敗しますか?
A. コンテナー名に含まれる大文字は、自動的に小文字に変換されます。 名前が他の方法で準拠していない場合 (たとえば、特殊文字やその他の言語が含まれている場合)、アップロードは失敗します。 詳細については、[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)を参照してください。

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. 複数の Data Box Disk にコピーしたデータは、どのようにして確認すればよいでしょうか?
A.  データのコピーが完了した後、*DataBoxDiskImport* フォルダーに用意されている `DataBoxDiskValidation.cmd` を実行することで、検証用のチェックサムを生成できます。 ディスクが複数ある場合は、ディスクごとにコマンド ウィンドウを開いてこのコマンドを実行する必要があります。 データのサイズによっては、この操作にかなり (数時間など) 時間がかかる場合があるので注意してください。

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. 返送したディスクのデータは、その後、どうなるのですか?
A.  Azure へのデータのコピーが完了した後、そのデータは、NIST SP 800-88 Revision 1 のガイドラインに従ってディスクから確実に消去されます。  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 移動中のデータは、どのようにして保護されるのですか? 
A.  Data Box Disk は Microsoft BitLocker 暗号化の AES-128 方式で暗号化され、すべてのディスクのロックを解除してデータにアクセスするには、1 つのパスキーが必要です。

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Q. Data Box Disk にデータを追加した場合、チェックサムの検証を再実行する必要はありますか?
A. はい。 データを検証する場合 (推奨)、ディスクにデータを追加したときは検証を再実行する必要があります。

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. データの転送にすべてのディスクを使い切ってしまったので、ディスクを追加注文する必要があります。 簡単に注文する方法はありますか?
A. 前回の注文を複製することができます。 複製することで前回と同じ注文が作成されるので、注文の詳細のみを編集すればよく、住所や連絡先、通知の詳細を入力する必要はありません。

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>Q. ManagedDisk フォルダーにデータをコピーしました。 マネージド ディスク用に指定されたリソース グループがあるマネージド ディスクが表示されません。 データは Azure にアップロードされましたか? どうすれば見つけられますか?
A. はい。 データは Azure にアップロードされましたが、指定されたリソース グループのあるマネージド ディスクが表示されない場合は、データが無効だった可能性があります。 ページ BLOB、ブロック BLOB、Azure Files、またはマネージド ディスクが無効だった場合は、次のフォルダーに移動されます。
 - ページ BLOB は *databoxdisk-invalid-pb-* で始まるブロック BLOB コンテナーに移動されます。
 - Azure Files は *databoxdisk-invalid-af-* で始まるブロック BLOB コンテナーに移動されます。
 - マネージド ディスクは *databoxdisk-invalid-md-* で始まるブロック BLOB コンテナーに移動されます。

## <a name="next-steps"></a>次のステップ

- [Data Box Disk のシステム要件](data-box-disk-system-requirements.md)を確認する。
- [Data Box Disk の制限事項](data-box-disk-limits.md)を理解する。
- Azure portal で [Azure Data Box Disk](data-box-disk-quickstart-portal.md) をすばやく展開する。