## <a name="general-considerations-for-n-series-vms"></a>N シリーズ VM の一般的な考慮事項

* N シリーズ VM を利用できるリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/en-us/regions/services/)」を参照してください。

* N シリーズ VM をデプロイするには、Resource Manager デプロイメント モデルを使用する必要があります。

* Azure Portal を使用して N シリーズ VM を作成する場合は、**[基本]** ブレードの **[VM ディスクの種類]** で **[HDD]** を選択します。 利用可能な N シリーズ サイズを選択するには、**[サイズ]** ブレードで **[すべて表示]** をクリックします。

* N シリーズ VM では、Azure Premium Storage で提供される VM ディスクはサポートされていません。

* 多数の N シリーズ VM をデプロイする場合は、従量課金のサブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

* Azure サブスクリプションの (リージョンごとの) コア クォータと NC または NV のコアの個別のクォータを増やす必要が生じる場合があります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../articles/azure-supportability/how-to-create-azure-support-request.md) (無料) してください。 既定の制限は、サブスクリプション カテゴリによって異なる場合があります。







