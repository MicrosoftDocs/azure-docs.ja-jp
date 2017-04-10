
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>よく寄せられる質問 - Azure によって割り当てられた IP アドレスの逆引き DNS

### <a name="how-much-do-reverse-dns-records-cost"></a>逆引き DNS レコードのコストはどのくらいですか?

無料です。  逆引き DNS レコードまたはクエリに追加コストはかかりません。

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Azure によって割り当てられたパブリック IP アドレスの逆引き DNS レコードはインターネットから解決されますか?

はい。 パブリック IP アドレス用の逆引き DNS プロパティを設定すると、Azure は、すべてのインターネット ユーザーについて逆引き DNS レコードを解決するために必要なすべての DNS 委任と DNS ゾーンを管理します。

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Public IP Addresses の既定の逆引き DNS レコードは作成されますか?

いいえ。 逆引き DNS はオプトイン機能です。 既定の逆引き DNS レコードを構成しない設定の場合、レコードは作成されません。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完全修飾ドメイン名 (FQDN) の形式とは何ですか?

FQDN は順方向で指定します。末尾にはドットを指定する必要があります (例: "app1.contoso.com.")。

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>指定した逆引き DNS の検証チェックが失敗すると、どうなりますか?

逆引き DNS の検証チェックが失敗すると、サービス管理操作は失敗します。 必要に応じて逆引き DNS 値を修正し、再試行してください。

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Azure Websites では逆引き DNS を管理できますか?

Azure Websites では、逆引き DNS はサポートされていません。 逆引き DNS は Azure Virtual Machines でサポートされます。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>パブリック IP アドレス用に複数の逆引き DNS を構成できますか?

いいえ。 サポートされる逆引き DNS レコードはパブリック IP アドレスごとに 1 つだけです。 ただし、各パブリック IP アドレスは、独自の逆引き DNS レコードを持つことができます。

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>IPv6 パブリック IP アドレス用の逆引き DNS レコードを構成できますか?

いいえ。  現時点で、逆引き DNS レコードは、IPv4 パブリック IP アドレス用のみサポートされています。

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>DomainNameLabel を指定しないでパブリック IP アドレスの逆引き DNS レコードを構成できますか?

いいえ。 Public IP Addresses の逆引き DNS レコードを利用するには、DomainNameLabel プロパティを指定する必要があります。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Azure コンピューティング サービスから外部ドメインに電子メールを送信できますか?

いいえ。 [Azure コンピューティング サービスでは、外部ドメインへの電子メールの送信はサポートされていません](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。


<!--HONumber=Nov16_HO3-->


