---
title: 構成に関する FAQ
description: Azure App Service の構成と管理の問題についてよく寄せられる質問への回答を取得します。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 843e11e7f2df404a6bf1866da7b383ee9d1097de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574788"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Azure の Web Apps の構成と管理に関する FAQ

この記事では、[Azure App Service の Web Apps 機能](https://azure.microsoft.com/services/app-service/web/)の構成および管理の問題に関するよく寄せられる質問 (FAQ)への回答を掲載しています。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>App Service のリソースを移動する場合の注意すべき制限はありますか?

App Service のリソースを新しいリソース グループまたはサブスクリプションに移動する場合は、注意すべきいくつかの制限があります。 詳細については、「[App Service の制限](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)」を参照してください。

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Web アプリのカスタム ドメイン名はどのように使用しますか?

Azure web アプリでのカスタム ドメイン名の使用に関するよく寄せられる質問に対する回答は、7 分間のビデオ [Add a custom domain name (カスタム ドメイン名の追加)](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) をご覧ください。 ビデオではカスタム ドメイン名を追加する方法を説明しています。 App Service Web アプリで *.azurewebsites.net URL の代わりに独自の URL を使用する方法を説明しています。 [カスタム ドメイン名をマップする方法](app-service-web-tutorial-custom-domain.md)の詳細な説明もあります。


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Web アプリの新しいカスタム ドメインはどのように購入しますか?

App Service Web アプリのカスタム ドメインを購入して設定する方法については、「[Buy and configure a custom domain name in App Service](manage-custom-dns-buy-domain.md)」(App Service でカスタム ドメイン名を購入して構成する) を参照してください。


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Web アプリに既存の TLS/SSL 証明書をアップロードして構成するにはどうすればよいですか?

既存のカスタム TLS/SSL 証明書をアップロードして設定する方法については、[使用している App Service アプリへの TLS/SSL 証明書の追加](configure-ssl-certificate.md)に関する記事を参照してください。


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>自分の Web アプリ用に Azure の新しい TLS/SSL 証明書を購入して構成するにはどうすれば良いですか?

お使いの App Service Web アプリの TLS/SSL 証明書を購入して設定する方法については、[お使いの App Service アプリへの TLS/SSL 証明書の追加](configure-ssl-certificate.md)に関するページを参照してください。


## <a name="how-do-i-move-application-insights-resources"></a>Application Insights のリソースを移動するにはどうすればよいですか?

現時点では、Azure Application Insights は移動操作をサポートしていません。 元のリソース グループに Application Insights のリソースが含まれている場合は、そのリソースを移動することはできません。 App Service アプリを移動しようとする際に Application Insights のリソースも含めると、移動操作全体が失敗します。 ただし、アプリが正常に動作するために、Application Insights と App Service プランがそのアプリと同じリソース グループ内に存在する必要はありません。

詳細については、「[App Service の制限](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)」を参照してください。

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>ガイダンス チェックリストおよびリソースの移動操作の詳細はどこにありますか?

「[App Service の制限](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)」では、リソースを新しいサブスクリプションまたは同じサブスクリプション内の新しいリソース グループに移動する方法について説明します。 リソースの移動チェックリストに関する情報を入手し、移動操作をサポートしているサービスおよびApp Service の制限事項とその他のトピックの詳細について学習できます。

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Web アプリのサーバーのタイム ゾーンはどのように設定しますか?

Web アプリのサーバーのタイム ゾーンを設定するには、次の手順を実行します。

1. Azure ポータルの App Service サブスクリプションで、 **[アプリケーション設定]** メニューに移動します。
2. **[アプリ設定]** で次の設定を追加します。
    * Key = WEBSITE_TIME_ZONE
    * Value = *目的のタイム ゾーン*
3. **[保存]** を選択します。

Windows で実行される App Service については、Windows `tzutil /L` コマンドからの出力を参照してください。 各エントリの 2 行目の値を使用します。 次に例を示します。"トンガ標準時"。 これらの値の一部は、「[既定のタイムゾーン](/windows-hardware/manufacture/desktop/default-time-zones)」の **タイムゾーン** 列にも記載されています。

Linux で実行される App Service については、[IANA TZ データベース](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)から値を設定します。 次に例を示します。"America/Adak"。

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>継続的な Web ジョブがときどき失敗する理由はなんですか?

既定では、設定された期間だけアイドル状態が続くと Web アプリはアンロードされます。 これにより、システムではリソースを節約できます。 Basic プランおよび Standard プランでは、 **[常時接続]** 設定をオンにして、常に Web アプリをロードしておくことができます。 アプリで継続的な Web ジョブを実行する場合は、 **[常時接続]** をオンにする必要があります。そうしないと、Web ジョブの実行の信頼性が低下する可能性があります。 詳細については、「[Create a continuously running WebJob](webjobs-create.md#CreateContinuous)」(継続的に実行する Web ジョブの作成) を参照してください。

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Web アプリの送信 IP アドレスはどのように取得しますか?

Web アプリの送信 IP アドレスの一覧を取得するには、次の手順を実行します。

1. Azure ポータルの Web アプリのブレードで、 **[プロパティ]** メニューに移動します。
2. **送信 IP アドレス** を検索します。

送信 IP アドレスの一覧が表示されます。

Web サイトが App Service Environment でホストされている場合、送信 IP アドレスを取得する方法については、「[発信ネットワーク アドレス](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)」をご覧ください。

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Web アプリ用に着信 IP アドレスを予約または専用にするにはどうすればよいですか?

お使いの Azure アプリ Web サイト用の専用の着信呼び出し IP アドレスを設定または予約するには、IP ベースの TLS/SSL 証明書をインストールして構成します。

着信呼び出し用の専用または予約済みの IP アドレスを使用するには、App Service プランは、Basic 以上のサービス プランである必要があります。

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>他の場所でホストされている web サイトなど、Azure の外部で使用できるように App Service 証明書をエクスポートできますか? 

はい。Azure の外部で使用する目的でエクスポートできます。 詳細については、「[App Service 証明書とカスタム ドメインの FAQ](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)」を参照してください。

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>その他の Azure クラウド サービスで使用できるように App Service 証明書をエクスポートできますか?

ポータルは、Azure Key Vault から App Service アプリに App Service 証明書をデプロイするためのファースト クラスのエクスペリエンスを提供します。 ただし、Azure 仮想マシンなど App Service プラットフォーム外部でこれらの証明書を使用するリクエストをお客様から受け取ってきました。 その他の Azure リソースで証明書を使用できるように App Service 証明書のローカル PFX コピーを作成する方法については、[App Service 証明書のローカル PFX コピーの作成](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)に関するぺージを参照してください。

詳細については、「[App Service 証明書とカスタム ドメインの FAQ](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)」を参照してください。


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Web アプリのバックアップを作成しようとするとに「部分的に成功」というメッセージを表示されます。なぜですか?

バックアップ障害の一般的な原因は、いくつかのファイルがアプリケーションで使用中であることです。 バックアップの実行中に使用されているファイルはロックされます。 このため、これらのファイルがバックアップされず、「部分的に成功」の状態になる可能性があります。 バックアップ プロセスからファイルを除外することでこの現象を回避できる可能性があります。 必要なものだけをバックアップすることを選択できます。 詳細については、「[Backup just the important parts of your site with Azure web apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)」(Azure web アプリを使用して、サイトの重要な部分だけをバックアップ) を参照してください。

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>HTTP 応答からヘッダーを削除するにはどうすればよいですか?

HTTP 応答からヘッダーを削除するには、サイトの web.config ファイルを更新します。 詳細については、「[Remove standard server headers on your Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)」(Azure Web サイトで標準的なサーバーのヘッダーを削除) を参照してください。

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>App Service は PCI 標準 3.0 と 3.1 に準拠していますか?

現時点では、Azure App Service の Web Apps の機能は PCI データ セキュリティ標準 (DSS) バージョン 3.0 レベル 1 に準拠しています。 PCI DSS バージョン 3.1 には今後準拠する予定です。 最新標準の採用をどのように進めるかについての計画は既に進行中です。

PCI DSS バージョン 3.1 の証明書は、トランスポート層セキュリティ (TLS) 1.0 を無効にする必要があります。 現時点では、TLS 1.0 を無効にすることは、ほとんどの App Service プランの選択肢ではありません。 ただし、App Service Environment を使用しているまたはワークロードを App Service Environment に移行を許容したい場合、環境の制御範囲を拡大できます。 Azure サポートに連絡して TLS 1.0 を無効にする必要があります。 近い将来、これらの設定をユーザーがアクセスできるようにする予定です。

詳細については、[PCI 標準 3.0 と 3.1 との Microsoft Azure App Service Web アプリのコンプライアンス対応](https://support.microsoft.com/help/3124528)に関するページを参照してください。

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>ステージング環境とデプロイ スロットを使用する方法を教えてください?

Standard および Premium App Service プランでは、Web アプリを App Service にデプロイする際、既定の運用スロットではなく、個別のデプロイ スロットにデプロイすることができます。 デプロイ スロットは、独自のホスト名を持つライブ Web アプリです。 Web アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つのデプロイ スロットの間でスワップすることができます。

デプロイ スロットの使用の詳細については、「[Set up a staging environment in App Service](deploy-staging-slots.md)」(App Service でのステージング環境のセットアップ) を参照してください。

## <a name="how-do-i-access-and-review-webjob-logs"></a>Web ジョブのログにアクセスして確認するにはどうすればよいですか?

Web ジョブのログを確認するには、次の手順を実行します。

1. **Kudu の Web サイト** (`https://*yourwebsitename*.scm.azurewebsites.net`) にサインインします。
2. [Web ジョブ] を選択します。
3. **[出力の切り替え]** ボタンを選択します。
4. 出力ファイルをダウンロードするには、**ダウンロード** リンクを選択します。
5. 個別実行では、 **[個別呼び出し]** を選択します。
6. **[出力の切り替え]** ボタンを選択します。
7. ダウンロード リンクを選択します。

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>SQL Server でのハイブリッド接続を使用しようとしています。 "System.OverflowException:Arithmetic operation resulted in an overflow" (System.OverflowException: 算術演算でオーバーフローが発生しました) というメッセージが表示されるのはなぜですか?

ハイブリッド接続を使用して SQL Server にアクセスしている場合、2016 年 5 月 10 日の Microsoft .NET の更新が接続の失敗の原因である可能性があります。 次のメッセージが表示される場合があります。

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>解像度

修正されたハイブリッド接続マネージャーの問題によって、例外が発生しました。 この問題を解決するために [ハイブリッド接続マネージャーを更新](https://go.microsoft.com/fwlink/?LinkID=841308)してください。

## <a name="how-do-i-add-a-url-rewrite-rule"></a>URL 書き換え規則を追加する方法を教えてください。

URL 書き換え規則を追加するには、関連する構成エントリを含む web.config ファイルを **wwwroot**　フォルダーに作成します。 詳細については、「[Azure App Services: URL 書き換えについて](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)」を参照してください。

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>App Service への受信トラフィックを制御するにはどうすればいいですか?

サイト レベルには、App Service への受信トラフィックを制御するためのオプションが 2 つあります。

* 動的 IP 制限を有効にします。 動的 IP 制限を有効にする方法については、「[IP and domain restrictions for Azure websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)」(Azure Web サイトの IP とドメインの制限) を参照してください。
* モジュールのセキュリティを有効にします。 モジュールのセキュリティを有効にする方法については、「[ModSecurity web application firewall on Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)」(Azure Web サイトの ModSecurity Web アプリケーション ファイアウォール) を参照してください。

App Service Environment を使用する場合は、[Barracuda ファイル](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)を使用できます。

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>App Service Web アプリ内のポートをブロックする方法は?

App Service 共有テナント環境では、インフラストラクチャの特性のため、特定のポートをブロックすることはできません。 TCP ポート 4020、4022、および 4024 も Visual Studio リモート デバッグに対して開いている場合があります。

App Service Environment では、受信トラフィックおよび送信トラフィックを完全に制御できます。 ネットワーク セキュリティ グループを使用して、特定のポートを制限またはブロックできます。 App Service Environment の詳細については、「[Introducing App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/)」(App Service Environment の概要) を参照してください。

## <a name="how-do-i-capture-an-f12-trace"></a>F12 トレースをキャプチャする方法は?

F12 トレースをキャプチャする方法は 2 つあります。

* F12 HTTP トレース
* F12 コンソール出力

### <a name="f12-http-trace"></a>F12 HTTP トレース

1. Internet Explorer で、Web サイトに移動します。 次の手順を実行する前にサインインする必要があります。 サインインしないと、F12 トレースは機密性の高いサインイン データをキャプチャします。
2. F12 キーを押します。
3. **[ネットワーク]** タブが選択されていることを確認し、緑色の **[再生]** ボタンをクリックします。
4. 問題を再現する手順を実行します。
5. 赤の **[停止]** ボタンをクリックします。
6. **[保存]** ボタン (ディスク アイコン) をクリックし、HAR ファイルを保存します (Internet Explorer およびで Microsoft Edge)。"*または*"、HAR ファイルを右クリックし、 **[HAR 形式ですべて保存]** を選択します (Chrome)。

### <a name="f12-console-output"></a>F12 コンソール出力

1. **[コンソール]** タブを選択します。
2. 1 つ以上の項目を含むタブごとに、タブを選択します ( **[エラー]** 、 **[警告]** 、または **[情報]** )。 タブが選択されていない場合、カーソルをタブ以外の場所に移動すると、タブ アイコンは、グレーまたは黒になります。
3. ウィンドウのメッセージ領域で右クリックし、 **[すべてコピー]** を選択します。
4. コピーしたテキストをファイルに貼り付け、ファイルを保存します。

HAR ファイルを表示するには [HAR ビューアー](http://www.softwareishard.com/har/viewer/)を使用できます。

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>ExpressRoute に接続されている仮想ネットワークに App Service Web アプリを接続しようとするとエラーが発生するのはなぜですか?

Azure ExpressRoute に接続されている仮想ネットワークに Azure Web アプリを接続しようとすると、失敗します。 "Gateway is not a VPN gateway." (ゲートウェイは VPN ゲートウェイではありません) というメッセージが表示されます。

現時点では、ExpressRoute に接続されている仮想ネットワークにポイント対サイト VPN 接続を行うことはできません。 ポイント対サイト VPN と ExpressRoute を同じ仮想ネットワークに共存させることはできません。 詳細については、「[ExpressRoute and site-to-site VPN connections limits and limitations](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)」(ExpressRoute とサイト間接続の制限事項) を参照してください。

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>静的ルーティング (ポリシー ベース) ゲートウェイを持つ仮想ネットワークに App Service Web アプリを接続するにはどうすればよいですか?

現時点では、静的ルーティング (ポリシー ベース) ゲートウェイを持つ仮想ネットワークへの App Service Web アプリの接続はサポートしていません。 ターゲットの仮想ネットワークが既に存在している場合は、そのネットワークで動的ルーティング ゲートウェイによるポイント対サイト VPN が有効になっていないと、アプリに接続できません。 ゲートウェイが静的ルーティングに設定されている場合は、ポイント対サイト VPN を有効にすることはできません。 

詳細については、「[アプリを Azure 仮想ネットワークに統合する](web-sites-integrate-with-vnet.md)」を参照してください。

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>App Service Environment で、使用できるワーカーが 2 つある場合でも、App Service プランを 1 つしか作成できないのはなぜですか?

フォールト トレランスを提供するために、App Service Environment では、ワーカー プールごとに追加のコンピューティング リソースが少なくとも 1 つ必要です。 追加のコンピューティング リソースには、ワークロードを割り当てることはできません。

詳細については、「[App Service Environment の作成方法](environment/app-service-web-how-to-create-an-app-service-environment.md)」を参照してください。

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>App Service Environment を作成するときにタイムアウトを表示されるのはなぜですか?

App Service Environment の作成はときどき失敗します。 この場合は、アクティビティ ログに次のエラーが表示されます。
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

これを解決するには、次の条件が当てはまるかどうかを確認します。
* サブネットが小さすぎる。
* サブネットが空ではない。
* ExpressRoute のために App Service Environment のネットワーク接続の要件を満たすことができない。
* ネットワーク セキュリティ グループが不適切であるために App Service Environment のネットワーク接続の要件を満たすことができない。
* 強制トンネリングがオンになっている。

詳細については、[新しい Azure App Service Environment をデプロイ (作成) するときによくある問題](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase)についてのページを参照してください。

## <a name="why-cant-i-delete-my-app-service-plan"></a>App Service プランを削除できないのはなぜですか?

App Service アプリがApp Service プランに関連付けられている場合、App Service プランを削除することはできません。 App Service プランを削除する前に、関連付けられているすべての App Service アプリを App Service プランから削除します。

## <a name="how-do-i-schedule-a-webjob"></a>Web ジョブをスケジュールする方法は?

Cron 式を使用して Web ジョブのスケジュールを作成できます。

1. settings.job ファイルを作成します。
2. Cron 式を使用して、この JSON ファイルにスケジュールのプロパティを含めます。 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Web ジョブのスケジュールの詳細については、「[Create a scheduled WebJob by using a Cron expression](webjobs-create.md#CreateScheduledCRON)」(Cron 式を使用して、Web ジョブのスケジュールを作成する) を参照してください。

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>App Service アプリの侵入テストを実行する方法は?

侵入テストを実行するには、[要求を送信](https://portal.msrc.microsoft.com/engage/pentest)します。

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Traffic Manager を使用する App Service Web アプリのカスタム ドメイン名を構成するにはどうすればいいですか?

ロード バランシングを行うために Azure Traffic Manager を使用する App Service アプリでカスタム ドメイン名を使用する方法については、「[Configure a custom domain name for an Azure web app with Traffic Manager](configure-domain-traffic-manager.md)」(Traffic Manager を使用する App Service Web アプリのカスタム ドメイン名を構成する方法) を参照してください。

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>App Service 証明書に詐欺のフラグが付いています。 解決するにはどうすればよいですか?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service 証明書購入のドメインの確認中に、次のメッセージが表示されることがあります。

"Your certificate has been flagged for possible fraud. The request is currently under review. If the certificate does not become usable within 24 hours, please contact Azure Support."(証明書に詐欺の可能性のフラグが付けられました。要求を確認中です。24 時間以内に証明書が使用可能にならない場合、Azure サポートに問い合わせてください)。

メッセージで示されているように、この詐欺の確認プロセスには最大 24 時間がかかる場合があります。 この間このメッセージは表示されたままになります。

24 時間を過ぎてもこのメッセージを表示されたままの場合、次の PowerShell スクリプトを実行してください。 このスクリプトは[証明書プロバイダー](https://www.godaddy.com/)に直接送信され、問題を解決します。

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>App Service では認証および承認はどのように動作しますか?

App Service の認証と承認の詳細なドキュメントについては、さまざまな ID プロバイダーのサインインに関するドキュメントを参照してください。
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft アカウント](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>既定の *.azurewebsites.net ドメインを Azure Web アプリケーションのカスタム ドメインにリダイレクトするにはどうすればよいですか?

Azure の Web アプリを使用して新しい Web サイトを作成するとき、既定の *サイト名*.azurewebsites.net ドメインがサイトに割り当てられます。 カスタム ホスト名をサイトに追加するが、ユーザーが既定の *. azurewebsites.net ドメインにアクセスできないようにする場合、既定の URL をリダイレクトできます。 Web サイトの既定のドメインからすべてのトラフィックをカスタム ドメインにリダイレクトする方法については、「[Redirect the default domain to your custom domain in Azure web apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/)」(Azure Web アプリで既定のドメインをカスタム ドメインにリダイレクトする) を参照してください。

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>App Service にインストールされている .NET のバージョンはどうすればわかりますか?

App Service にインストールされている Microsoft .NET のバージョンを検索する最も簡単な方法は、Kudu コンソールを使用することです。 Kudu コンソールは、ポータルから、または、App Service アプリの URL を使用してアクセスできます。 詳細については、「[Determine the installed .NET version in App Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services)」(App Service にインストールされた .NET バージョンを確認する) を参照してください。

## <a name="why-isnt-autoscale-working-as-expected"></a>自動スケールが期待どおりに動作しないのはなぜですか?

Azure の自動スケールが期待どおりに Web アプリのインスタンスをスケールインまたはスケールアウトしない場合、「フラッピング」による無限ループを避けるために意図的にスケールしないことを選択している可能性があります。 これは通常、スケール アウトとスケール インのしきい値の間に適切な余白がない場合に発生します。 「フラッピング」を回避する方法およびその他の自動スケールのベスト プラクティスについては、「[自動スケールのベスト プラクティス](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices)」を参照してください。

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>自動スケールが部分的にしか行われないことがあるのはなぜですか?

メトリックが事前に構成された境界を越えると自動スケールがトリガーされます。 キャパシティが期待値に対して部分的にのみ満たされる場合があります。 目的のインスタンス数を利用できないときに発生する場合があります。 このシナリオでは、自動スケールは、利用可能なインスタンス数に対して行われます。 次に、自動スケールは再調整ロジックを実行し、キャパシティを増やします。 残りのインスタンスが割り当てられます。 これには数分間かかります。

数分後に期待するインスタンス数が表示されない場合、部分的リフィルが境界内にメトリックを表示するのに十分であったための可能性があります。 または、下位のメトリック境界に達したために、自動スケールがスケール ダウンされた可能性があります。

これらの条件のいずれにも該当せず、問題が解決しない場合は、サポート リクエストを送信してください。

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>コンテンツの HTTP 圧縮を有効するにはどうすればよいですか?

静的コンテンツ タイプと動的コンテンツ タイプの両方に対する圧縮を有効にするには、アプリケーション レベルの web.config ファイルに次のコードを追加します。

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

圧縮する特定の動的および静的 MIME の種類を指定することもできます。 詳細については、「[httpCompression settings on a simple Azure website](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)」(単純な Azure Web サイトでの httpCompression 設定) のフォーラムで質問への回答を参照してください。

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>オンプレミス環境から App Serviceアプリ サービスへの移行方法を教えてください?

サイトを Windows および Linux の Web サーバーから App Service に移行するには、Azure App Service Migration Assistant を使用できます。 移行ツールは、必要に応じて、Azure に Web アプリとデータベースを作成し、コンテンツを発行します。 詳細については、「[Azure App Service Migration Assistant](https://appmigration.microsoft.com/)」を参照してください。

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>証明書が 1 年間ではなく 11 か月で発行されているのはなぜですか?

2020/9/1 より後に発行されたすべての証明書について、最長期間は 397 日になりました。 2020/9/1 より前に発行された証明書は、更新やキー更新を行うまで、最大 825 日間有効になります。2020/9/1 より後に更新された証明書はこの変更による影響を受けます。ユーザーは、更新された証明書の有効期限が短くなっていることに気付く場合があります。
GoDaddy により、既存の顧客の証明書を優先すると同時に新しい要件を満たすサブスクリプション サービスが実装されています。 新しく発行された証明書の有効期限が切れる 30 日前に、このサービスによって、期間を元の有効期限まで延長する 2 番目の証明書が自動的に発行されます。 App Service では、GoDaddy と連携してこの変更に対処し、お客様が証明書の完全な期間を確実に利用できるようにします。
