---
title: Azure DNS エイリアス レコードと Azure App Service のカスタム ドメイン検証でサブドメインの乗っ取りを防ぐ
description: サブドメインの乗っ取りに関する重大度の高い一般的な脅威を回避する方法について説明します
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: c3a821156074727d02ab36cf88f3e998756b8cc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389452"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する

この記事では、サブドメインの乗っ取りに関する一般的なセキュリティの脅威と、その軽減手順について説明します。


## <a name="what-is-a-subdomain-takeover"></a>サブドメインの乗っ取りとは

サブドメインの乗っ取りは、多くのリソースを定期的に作成したり削除したりする組織にとって、重大度の高い一般的な脅威です。 サブドメインの乗っ取りは、プロビジョニング解除された Azure リソースを参照する [DNS レコード](../../dns/dns-zones-records.md#dns-records)がある場合に発生する可能性があります。 このような DNS レコードは、"未解決の DNS" エントリとも呼ばれます。 CNAME レコードは、この脅威に対して特に脆弱です。 サブドメインの乗っ取りが発生すると、悪意のあるアクターが悪意のあるアクティビティを実行しているサイトに、組織のドメイン向けのトラフィックをリダイレクトできるようになります。

サブドメインの乗っ取りの一般的なシナリオは次のとおりです。

1. **作成:**

    1. `app-contogreat-dev-001.azurewebsites.net` の完全修飾ドメイン名 (FQDN) を使用して、Azure リソースをプロビジョニングします。

    1. Azure リソースにトラフィックをルーティングするサブドメイン `greatapp.contoso.com` を使用して、DNS ゾーンに CNAME レコードを割り当てます。

1. **プロビジョニング解除:**

    1. Azure リソースは、不要になるとプロビジョニング解除または削除されます。 
    
        この時点で、CNAME レコード `greatapp.contoso.com` を DNS ゾーンから削除する *必要があります*。 CNAME レコードが削除されていない場合でも、アクティブなドメインとして公開されますが、トラフィックはアクティブな Azure リソースにルーティングされません。 これは、"未解決の" DNS レコードの定義です。

    1. 未解決のサブドメイン `greatapp.contoso.com` が現在脆弱であり、別の Azure サブスクリプションのリソースに割り当てられることで乗っ取られる可能性があります。

1. **乗っ取り:**

    1. 脅威アクターが一般的に使用可能な方法とツールを使用して、未解決のサブドメインを検出します。  

    1. 脅威アクターは、以前に管理していたリソースと同じ FQDN を使用して Azure リソースをプロビジョニングします。 この例では、 `app-contogreat-dev-001.azurewebsites.net`です。

    1. この時点で、サブドメイン `greatapp.contoso.com` に送信されるトラフィックは、コンテンツが制御される悪意のあるアクターのリソースにルーティングされます。



![プロビジョニング解除された Web サイトからのサブドメインの乗っ取り](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>サブドメインの乗っ取りのリスク

DNS レコードが使用できないリソースへポイントしている場合、レコード自体は DNS ゾーンから削除されている必要があります。 削除されていない場合、これは "未解決の DNS" レコードになり、サブドメインの乗っ取りが行われる可能性があります。

未解決の DNS エントリが存在していると、脅威アクターは、関連付けられている DNS 名を制御して、悪意のある Web サイトやサービスをホストできるようになります。 組織のサブドメインに悪意のあるページやサービスがあると、次のような結果になる可能性があります。

- **サブドメインのコンテンツに対する制御の喪失** - ブランドの毀損や信頼の損失だけでなく、組織のコンテンツをセキュリティで保護できないことに関する否定的な報道など。

- **疑いを持たないサイト閲覧者からの Cookie 収集** - Web アプリでは、セッション Cookie をサブドメイン (*.contoso.com) に公開するのが一般的です。そのため、どのサブドメインからでもアクセスできるようになっています。 脅威アクターは、サブドメインの乗っ取りを使用して、本物に似せた検索ページを作成し、疑いを持たないユーザーを騙してアクセスさせることで Cookie (セキュリティで保護されているものも含む) を収集することができます。 よくある誤解は、SSL 証明書を使用することで、サイトとユーザーの Cookie を乗っ取りから保護できるというものです。 しかし、脅威アクターはハイジャックしたサブドメインを使用して、有効な SSL 証明書を適用して受け取ることができます。 有効な SSL 証明書により、セキュリティで保護された Cookie へのアクセスが許可され、悪意のあるサイトの見かけの正当性をさらに向上させることになります。

- **フィッシング キャンペーン** - 本物に見せかけたサブドメインは、フィッシング キャンペーンで使用される可能性があります。 これは、悪意のあるサイトや、脅威アクターが既知の安全なブランドの正当なサブドメイン宛ての電子メールを受信できるようにする MX レコードに当てはまります。

- **さらなるリスク** - 悪意のあるサイトは、XSS、CSRF、CORS バイパスなどの他の古典的な攻撃にエスカレートさせるために使用される可能性があります。



## <a name="identify-dangling-dns-entries"></a>未解決の DNS エントリを特定する

未解決の可能性がある組織内の DNS エントリを特定するには、Microsoft の GitHub でホストされた PowerShell ツール ["DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains) を使用します。

このツールを使用すると、Azure の顧客がサブスクリプションまたはテナントで作成された既存の Azure リソースに CNAME が関連付けられているすべてのドメインを一覧表示できます。

CNAME が他の DNS サービス内にあり、Azure リソースを示している場合は、入力ファイルの CNAME をツールに指定します。

このツールでは、以下の表に示す Azure リソースがサポートされています。 このツールでは、すべてのテナントの CNAME が抽出されるか、または入力として処理されます。


| サービス                   | 種類                                        | FQDNproperty                               | 例                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | microsoft.network/frontdoors                | properties.cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | microsoft.storage/storageaccounts           | properties.primaryEndpoints.blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | microsoft.cdn/profiles/endpoints            | properties.hostName                        | `abc.azureedge.net`             |
| パブリック IP アドレス       | microsoft.network/publicipaddresses         | properties.dnsSettings.fqdn                | `abc.EastUs.cloudapp.azure.com` |
| Azure の Traffic Manager     | microsoft.network/trafficmanagerprofiles    | properties.dnsConfig.fqdn                  | `abc.trafficmanager.net`        |
| Azure Container Instances  | microsoft.containerinstance/containergroups | properties.ipAddress.fqdn                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | microsoft.apimanagement/service             | properties.hostnameConfigurations.hostName | `abc.azure-api.net`             |
| Azure App Service         | microsoft.web/sites                         | properties.defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service - Slots | microsoft.web/sites/slots                   | properties.defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>前提条件

次のものを持つユーザーとして、クエリを実行します。

- 少なくとも Azure サブスクリプションへの閲覧者レベルのアクセス権
- Azure Resource Graph への読み取りアクセス権

組織のテナントのグローバル管理者である場合は、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権を昇格させる](../../role-based-access-control/elevate-access-global-admin.md)」のガイダンスを使用して、組織のすべてのサブスクリプションへのアクセス権が付与されるようにアカウントを昇格させます。


> [!TIP]
> Azure Resource Graph には、大規模な Azure 環境を使用している場合に考慮する必要がある調整およびページングの制限があります。 
> 
> 大規模な Azure リソース データセットを処理する方法について[参照してください](../../governance/resource-graph/concepts/work-with-data.md)。
> 
> このツールでは、サブスクリプションのバッチ処理を使用してこれらの制限を回避しています。

### <a name="run-the-script"></a>スクリプトを実行する

PowerShell スクリプト (**Get-DanglingDnsRecords.ps1**) の詳細を確認し、GitHub (https://aka.ms/Get-DanglingDnsRecords ) からダウンロードしてください。

## <a name="remediate-dangling-dns-entries"></a>未解決の DNS エントリを修復する 

DNS ゾーンをレビューし、未解決の CNAME レコードまたは乗っ取られている CNAME レコードを特定します。 サブドメインが未解決であるか、または乗っ取られていることが判明した場合は、次の手順に従って、脆弱なサブドメインを削除して、リスクを軽減します。

1. DNS ゾーンから、プロビジョニングされなくなったリソースの FQDN を示す CNAME レコードをすべて削除します。

1. コントロール内のリソースにトラフィックをルーティングできるようにするには、未解決のサブドメインの CNAME レコードに指定されている FQDN を使用して、追加のリソースをプロビジョニングします。

1. アプリケーション コードで特定のサブドメインへの参照をレビューし、誤ったサブドメイン参照や古いサブドメイン参照を更新します。

1. 侵害が発生しているかどうかを調査し、組織のインシデント対応手順に従って対処します。 この問題を調査するためのヒントとベストプラクティスについては、以下をご覧ください。

    アプリケーション ロジックで OAuth 資格情報などのシークレットが未解決のサブドメインに送信された場合や、機密性の高い情報が未解決のサブドメインに送信された場合は、そのデータがサードパーティに公開されている可能性があります。

1. リソースのプロビジョニングが解除されたときに、CNAME レコードが DNS ゾーンから削除されなかった理由について理解し、今後は Azure リソースのプロビジョニングが解除されたときに、DNS レコードが適切に更新されるようにするための手順を実行します。


## <a name="prevent-dangling-dns-entries"></a>未解決の DNS エントリを防止する

未解決の DNS エントリと、その結果として生じるサブドメインの乗っ取りを防止するためのプロセスを組織で導入していることを確認することは、セキュリティ プログラムの重要な部分です。

一部の Azure サービスでは、予防策の作成に役立つ機能が提供されています。詳細については、以下で説明します。 この問題を防止するための他の方法は、組織のベスト プラクティスまたは標準の操作手順に従って確立する必要があります。

### <a name="enable-azure-defender-for-app-service"></a>Azure Defender for App Service を有効にする

Azure Security Center の統合クラウド ワークロード保護プラットフォーム (CWPP) である Azure Defender には、Azure、ハイブリッド、およびマルチクラウドのリソースとワークロードを保護するためのさまざまなプランが用意されています。

**Azure Defender for App Service** プランには、未解決の DNS の検出が含まれています。 このプランを有効にすると、App Service Web サイトを使用停止にしても、そのカスタム ドメインを DNS レジストラーから削除しない場合にセキュリティ アラートを受け取ります。

Azure Defender による未解決の DNS の保護は、ドメイン管理に Azure DNS を使用しているか、外部のドメイン レジストラーを使用しているかに関係なく利用でき、Windows 上の App Service と Linux 上の App Service の両方に適用されます。

この Azure Defender プランのその他の特典については、「[Azure Defender for App Service の概要](../../security-center/defender-for-app-service-introduction.md)」を参照してください。

### <a name="use-azure-dns-alias-records"></a>Azure DNS エイリアス レコードの使用

Azure DNS の[エイリアス レコード](../../dns/dns-alias.md#scenarios)では、DNS レコードと Azure リソースのライフサイクルを結合することで、未解決の参照が防止できるようになります。 たとえば、パブリック IP アドレスまたは Traffic Manager プロファイルをポイントするエイリアス レコードとして修飾されている DNS レコードについて考えます。 これらの基になるリソースを削除すると、DNS エイリアス レコードが空のレコード セットになります。 削除されたリソースは参照されなくなります。 エイリアス レコードを使用して保護できるものには制限があることに注意してください。 現在の、この一覧の制限は次のとおりです。

- Azure Front Door
- Traffic Manager プロファイル
- Azure Content Delivery Network (CDN) エンドポイント
- パブリック IP

現時点で提供されているサービス内容は限られていますが、サブドメインの乗っ取りを防ぐため、可能な限りエイリアス レコードを使用することをお勧めします。

Azure DNS のエイリアス レコードの機能に関する詳細については、[こちら](../../dns/dns-alias.md#capabilities)を参照してください。



### <a name="use-azure-app-services-custom-domain-verification"></a>Azure App Service のカスタム ドメインの検証を使用する

Azure App Service の DNS エントリを作成する場合は、Domain Verification ID を持つ asuid.{subdomain} TXT レコードを作成します。 このような TXT レコードが存在する場合、他の Azure サブスクリプションによってカスタム ドメインの検証を行うこと、つまり乗っ取ることはできません。 

これらのレコードによって、他の誰かが CNAME エントリにある同じ名前の Azure App Service を作成することを阻止するわけではありません。 ドメイン名の所有権を証明することができない脅威アクターが、トラフィックを受信したりコンテンツを制御したりすることはできません。

既存のカスタム DNS 名を Azure App Service にマップする方法については、[こちら](../../app-service/app-service-web-tutorial-custom-domain.md)を参照してください。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>脅威を軽減するためのプロセスを構築して自動化する

多くの場合、未解決の DNS による脅威を回避するためのクリーンアップ プロセスの実行は、開発者や運用チームの担当になっています。 以下のプラクティスは、組織がこの脅威による問題を回避するのに役立ちます。 

- **防止手順を作成する:**

    - リソースを削除するたびにアドレスを再ルーティングするようにアプリケーション開発者に指導します。

    - サービスを停止するときに、必要なチェック事項の一覧に "DNS エントリの削除" を含めます。

    - カスタム DNS エントリがあるすべてのリソースに対して[削除ロック](../../azure-resource-manager/management/lock-resources.md)をかけます。 削除ロックは、リソースがプロビジョニング解除される前にマッピングを削除する必要があることを示すインジケーターとして機能します。 このような対策は、社内の教育プログラムと組み合わせて初めて機能します。

- **検出手順を作成する:**

    - DNS レコードを定期的に確認して、サブドメインがすべて以下の Azure リソースにマップされていることを確認します。

        - 存在しているかどうか - DNS ゾーンに対して、*.azurewebsites.net や *.cloudapp.azure.com などの Azure サブドメインを指すリソースのクエリを実行します (詳しくは、[Azure ドメインのリファレンス リスト](azure-domains.md)を参照してください)。
        - 所有者が自分であるかどうか - DNS サブドメインが対象としているすべてのリソースを所有していることを確認します。

    - Azure の完全修飾ドメイン名 (FQDN) エンドポイントとアプリケーション所有者のサービス カタログを維持します。 サービス カタログをビルドするには、次の Azure Resource Graph クエリ スクリプトを実行します。 このスクリプトからは、アクセスできるリソースの FQDN エンドポイント情報が表示され、それらが CSV ファイルに出力されます。 テナントのあらゆるサブスクリプションにアクセスできる場合、このスクリプトでは、次のサンプル スクリプトで確認できるように、それらすべてのサブスクリプションが考慮されます。 特定のサブスクリプション セットに結果を制限するには、画像のようにスクリプトを編集します。


- **修復手順を作成する:**
    - 未解決の DNS エントリが見つかった場合、チームは侵害が発生したかどうかを調査する必要があります。
    - リソースが使用停止になったときにアドレスが再ルーティングされなかった理由を調査します。
    - 使用されなくなった DNS レコードは削除するか、組織が所有する正しい Azure リソース (FQDN) へポイントされるようにします。
 

## <a name="next-steps"></a>次のステップ

サブドメインの乗っ取りを防ぐために使用できる関連のサービスと Azure の機能の詳細については、以下のページを参照してください。

- [Azure Defender for App Service を有効にする](../../security-center/defender-for-app-service-introduction.md) - 未解決の DNS エントリを検出すると、アラートを受け取ります

- [Azure DNS で未解決の DNS レコードを防ぐ](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Azure App Service でカスタム ドメインを追加するときにドメイン検証 ID を使用する](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [クイック スタート: Azure PowerShell を使用して最初の Resource Graph クエリを実行します](../../governance/resource-graph/first-query-powershell.md)
