| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md) |リージョンあたり 20 件<sup>1</sup> |リージョンあたり 10,000 |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの VM の合計コア数 |リージョンあたり 20 件<sup>1</sup> |リージョンあたり 10,000 |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりのシリーズ (Dv2、F など) ごとの VM のコア数 |リージョンあたり 20 件<sup>1</sup> |リージョンあたり 10,000 |
| [共同管理者](../articles/billing-add-change-azure-subscription-administrator.md) 数 |無制限 |無制限 |
| [Storage アカウント](../articles/storage/storage-create-storage-account.md) 数 |200 |200<sup>2</sup> |
| サブスクリプションあたりの[リソース グループ数](../articles/azure-resource-manager/resource-group-overview.md) |800 |800 |
| [可用性セット](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 数 |リージョンあたり 2000 件 |リージョンあたり 2000 件 |
| リソース マネージャー API 読み取り数 |1 時間あたり 15000 |1 時間あたり 15000 |
| リソース マネージャー API 書き込み数 |1 時間あたり 1200 |1 時間あたり 1200 |
| リソース マネージャー API 要求のサイズ |4194304 バイト |4194304 バイト |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |適用不可<sup>3</sup> |適用不可<sup>3</sup> |
| [アフィニティ グループ](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) 数 |適用不可<sup>3</sup> |適用不可<sup>3</sup> |

<sup>1</sup>既定の制限は、プラン カテゴリの種類 (無料試用版や従量課金制など) とシリーズ (Dv2、F、G など) によって異なります。

<sup>2</sup>これには、Standard および Premium ストレージ アカウントの両方が含まれます。 必要なストレージ アカウントが 200 個を超える場合は、[Azure サポート](https://azure.microsoft.com/support/faq/)からリクエストを送信してください。 Azure Storage チームがビジネス ケースを確認します。承認された場合、最大 250 個のストレージ アカウントが与えられます。

<sup>3</sup>これらの機能は、Azure リソース グループと Azure リソース マネージャーでは必要なくなりました。

> [!NOTE]
> 仮想マシンのコアには、リージョンの合計の制限だけでなく、別に適用されるリージョンのサイズ シリーズ (Dv2、F など) ごとの制限もあることに注意してください。  たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。  このサブスクリプションでは、30 の A1 VM、30 の D1 VM、または合計コア数が 30 を超えないこの 2 つの組み合わせ (例: 10 の A1 VM と 20 の D1 VM) のデプロイが許可されます。  
> <!-- -->
> 
> 

