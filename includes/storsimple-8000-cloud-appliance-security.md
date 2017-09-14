<!--alkohli 02/21/2017 cloud appliance security-->

StorSimple Cloud Appliance を使用する際は、セキュリティに関する次の考慮事項に留意してください。

* このクラウド アプライアンスは Microsoft Azure サブスクリプションを通じてセキュリティで保護されます。 つまり、このクラウド アプライアンスを使用している場合に Azure サブスクリプションが侵害されると、クラウド アプライアンス上に格納されたデータも影響を受けます。
* StorSimple に格納されたデータの暗号化に使われる証明書の公開キーは、Azure Portal に対して安全に公開され、秘密キーは StorSimple Cloud Appliance 上に保持されます。 StorSimple Cloud Appliance では、公開キーと秘密キーの両方が Azure に格納されます。
* クラウド アプライアンスは、Microsoft Azure データセンターでホストされます。

