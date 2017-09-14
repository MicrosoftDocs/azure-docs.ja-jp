ストレージ アカウントには、次の 2 種類があります。

### <a name="general-purpose-storage-accounts"></a>汎用ストレージ アカウント
汎用ストレージ アカウントを使用すると、1 つのアカウントにあるテーブル、キュー、ファイル、BLOB、Azure 仮想マシン ディスクなどの Azure Storage サービスにアクセスできます。 この種類のストレージ アカウントには、次の 2 つのパフォーマンス レベルがあります。

* テーブル、キュー、ファイル、BLOB、および Azure 仮想マシン ディスクを格納できる、Standard Storage パフォーマンス レベル。
* 現在、Azure 仮想マシン ディスクのみをサポートしている、Premium Storage パフォーマンス レベル。 Premium Storage の詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../articles/storage/common/storage-premium-storage.md) 」をご覧ください。

### <a name="blob-storage-accounts"></a>BLOB ストレージ アカウント
BLOB ストレージ アカウントとは、Azure Storage に BLOB (オブジェクト) として非構造化データを格納するための特殊なストレージ アカウントです。 BLOB ストレージ アカウントは、既存の汎用ストレージ アカウントと同様で、現在使用されているすべての優れた耐久性、可用性、スケーラビリティ、およびパフォーマンス機能を共有します。たとえば、ブロック BLOB と追加 BLOB の 100% の API 整合性などです。 ブロックまたは追加 Blob Storage のみを必要とするアプリケーションでは、BLOB ストレージ アカウントを使用することをお勧めします。

> [!NOTE]
> BLOB ストレージ アカウントは、ブロック BLOB と追加 BLOB のみをサポートします。ページ BLOB はサポートしません。
> 
> 

BLOB ストレージ アカウントでは、**アクセス レベル**属性を公開します。この属性は、アカウントの作成時に指定し、必要に応じて後で変更できます。 アクセス層には次の 2 つの種類があり、データ アクセス パターンに応じて指定することができます。

* **ホット** アクセス レベル。ストレージ アカウント内のオブジェクトへのアクセス頻度が高いことを示します。 より低いアクセス コストで、データを格納することができます。
* **クール** アクセス レベル。ストレージ アカウント内のオブジェクトへのアクセス頻度が低いことを示します。 より低いデータ ストレージ コストで、データを格納することができます。

データの使用パターンが変化した場合は、いつでもこれらのアクセス層を切り替えることができます。 アクセス層を変更すると、追加料金が発生することがあります。 詳細については、 [BLOB ストレージ アカウントの価格と課金](../articles/storage/blobs/storage-blob-storage-tiers.md#pricing-and-billing) に関するセクションを参照してください。

BLOB ストレージ アカウントの詳細については、「 [Azure Blob Storage: クール層とホット層](../articles/storage/blobs/storage-blob-storage-tiers.md)」を参照してください。

ストレージ アカウントを作成するには、事前に Azure サブスクリプション (多様な Azure サービスにアクセスするためのプラン) が必要です。 Azure を初めて使うときは、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)で試用することができます。 サブスクリプション プランの購入時には、さまざまな [購入オプション](https://azure.microsoft.com/pricing/purchase-options/)から選択できます。 [MSDN サブスクライバー](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)には、毎月、Azure Storage を含む Azure サービスに使用できる無料使用分のクレジットが与えられます。 ボリューム価格については、「 [Azure Storage 料金 ](https://azure.microsoft.com/pricing/details/storage/) 」を参照してください。

ストレージ アカウントの作成方法の詳細については、「 [ストレージ アカウントの作成](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) 」を参照してください。 1 つのサブスクリプションに対し、最大 200 の一意の名前を持つストレージ アカウントを作成できます。 ストレージ アカウントの制限の詳細については、「 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../articles/storage/common/storage-scalability-targets.md) 」を参照してください。

