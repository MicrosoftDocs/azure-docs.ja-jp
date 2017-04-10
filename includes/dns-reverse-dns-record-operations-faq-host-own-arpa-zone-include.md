
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>よく寄せられる質問 - Azure DNS での ARPA ゾーンのホスト

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>ISP から割り当てられた Azure DNS 上の IP ブロックの ARPA ゾーンをホストできますか?

はい。 Azure DNS での独自の IP 範囲の ARPA ゾーンのホストは完全にサポートされています。

[Azure DNS でゾーンを作成](../articles/dns/dns-getstarted-create-dnszone.md)し、ISP と連携して[ゾーンを委任](../articles/dns/dns-domain-delegation.md)するだけです。  その後、他のレコードの種類と同じ方法で、各逆引き参照の PTR レコードを管理できます。

[Azure CLI を使用して既存の逆引き参照ゾーンをインポート](../articles/dns/dns-import-export.md)することもできます。

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>ARPA ゾーンをホストするのにどれだけのコストがかかりますか?

ISP によって割り当てられた IP ブロックの ARPA ゾーンを Azure DNS でホストする場合、[Azure DNS の標準料金](https://azure.microsoft.com/pricing/details/dns/)がかかります。

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Azure DNS で IPv4 と IPv6 の両方のアドレスの ARPA ゾーンをホストできますか?

はい。


<!--HONumber=Nov16_HO3-->


