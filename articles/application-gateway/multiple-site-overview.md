---
title: Azure Application Gateway での複数のサイトのホスト
description: この記事では、Azure Application Gateway による複数サイトのサポートの概要を示します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87474327"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway の複数サイトのホスト

複数サイトのホストにより、1 つのアプリケーション ゲートウェイの同じポートで複数の Web アプリケーションを構成することができます。 最大で 100 個以上の Web サイトを 1 つのアプリケーション ゲートウェイに追加することによって、デプロイに効率的なトポロジを構成できます。 各 Web サイトは、独自のバックエンド プールに送られるようにすることができます。 たとえば、contoso.com、fabrikam.com、adatum.com という 3 つのドメインで、アプリケーション ゲートウェイの IP アドレスが示されています。 マルチサイト リスナーを 3 つ作成し、リスナーごとにポートとプロトコル設定を構成します。 

マルチサイト リスナーでワイルドカードのホスト名を定義し、リスナー 1 つにつき最大 5 つのホスト名を定義することもできます。 詳細については、[リスナーでのワイルドカードのホスト名](#wildcard-host-names-in-listener-preview)に関する記事を参照してください。

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="複数サイト アプリケーション ゲートウェイ":::

> [!IMPORTANT]
> v1 SKU では、規則はポータルに一覧表示されている順序で処理されます。 v2 SKU では、完全一致の優先順位が高くなります。 基本リスナーを構成する前に、まずマルチサイト リスナーを構成することを強くお勧めします。  そうすることで、トラフィックが確実に適切なバックエンドにルーティングされます。 基本リスナーが先に表示されていて、なおかつ受信要求と一致した場合、そのリスナーによって要求が処理されます。

`http://contoso.com` の要求は ContosoServerPool にルーティングされ、`http://fabrikam.com` は FabrikamServerPool にルーティングされます。

同様に、同じ親ドメインの複数のサブドメインを、同じアプリケーション ゲートウェイ デプロイでホストすることができます。 たとえば、`http://blog.contoso.com` および `http://app.contoso.com` を、単一のアプリケーション ゲートウェイ デプロイでホストできます。

## <a name="wildcard-host-names-in-listener-preview"></a>リスナーにおけるワイルドカードのホスト名 (プレビュー)

Application Gateway では、マルチサイト HTTP(S) リスナーを使用したホストベースのルーティングが可能です。 これで、ホスト名でアスタリスク (*) と疑問符 (?) などのワイルドカード文字を使用でき、マルチサイト HTTP(S) リスナーごとに最大 5 つのホスト名を使用できます。 たとえば、「 `*.contoso.com` 」のように入力します。

ホスト名でワイルドカード文字を使用すると、1 つのリスナー内で複数のホスト名と一致させることができます。 たとえば、`*.contoso.com` は、`ecom.contoso.com`、`b2b.contoso.com`、`customer1.b2b.contoso.com` などと一致させることができます。 ホスト名の配列を使用すると、1 つのリスナーに対して複数のホスト名を構成し、バックエンド プールに要求をルーティングすることができます。 たとえば、リスナーには、両方のホスト名に対する要求を受け入れる `contoso.com, fabrikam.com` を含めることができます。

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="ワイルドカード リスナー":::

>[!NOTE]
> この機能はプレビュー段階であり、Application Gateway の Standard_v2 および WAF_v2 SKU でのみ使用できます。 プレビューの詳細については、[使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページを参照してください。

>[!NOTE]
>現在この機能は、[Azure PowerShell](tutorial-multiple-sites-powershell.md) と [Azure CLI](tutorial-multiple-sites-cli.md) によってのみ利用できます。 ポータルでは近日中にサポートされるようになります。
> ポータルのサポートは完全に利用できるわけではないため、HostNames パラメーターのみを使用している場合、リスナーはポータルに基本リスナーとして表示され、リスナー リスト ビューのホスト名の列には構成されているホスト名が表示されないことに注意してください。 ワイルドカード リスナーを変更する場合は、ポータルでサポートされるまで、Azure PowerShell または CLI を確実に使用してください。

[Azure PowerShell](tutorial-multiple-sites-powershell.md) の場合は、`-HostName` ではなく `-HostNames` を使用する必要があります。 HostNames を使用すると、最大 5 つのホスト名をコンマ区切り値として指定でき、ワイルドカード文字を使用できます。 たとえば、`-HostNames "*.contoso.com,*.fabrikam.com"` のように指定します。

[Azure CLI](tutorial-multiple-sites-cli.md) の場合は、`--host-name` ではなく `--host-names` を使用する必要があります。 host-names を使用すると、最大 5 つのホスト名をコンマ区切り値として指定でき、ワイルドカード文字を使用できます。 たとえば、`--host-names "*.contoso.com,*.fabrikam.com"` のように指定します。

### <a name="allowed-characters-in-the-host-names-field"></a>ホスト名フィールドで使用できる文字:

* `(A-Z,a-z,0-9)` - 英数字
* `-` - ハイフンまたはマイナス
* `.` - 区切り記号としてのピリオド
*   `*` - 許可された範囲内の複数の文字と一致することができます
*   `?` - 許可された範囲内の 1 つの文字と一致することができます

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>リスナーでワイルドカード文字と複数のホスト名を使用する場合の条件:

*   1 つのリスナーでは最大で 5 つのホスト名のみを指定できます
*   アスタリスク `*` は、ドメイン スタイルの名前またはホスト名の構成要素で 1 回だけ使用できます。 たとえば、component1 *.component2*.component3 などです。 `(*.contoso-*.com)` は有効です。
*   ホスト名では、アスタリスク `*` を最大で 2 つまで使用できます。 たとえば、`*.contoso.*` は有効ですが、`*.contoso.*.*.com` は無効です。
*   ホスト名で使用できるワイルドカード文字は最大 4 つです。 たとえば、`????.contoso.com`、`w??.contoso*.edu.*` は有効ですが、`????.contoso.*` は無効です。
*   ホスト名の 1 つの構成要素でアスタリスク `*` と疑問符 `?` を一緒に使用すること (`*?` または `?*` または `**`) は無効です。 たとえば、`*?.contoso.com` や `**.contoso.com` は無効です。

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>リスナーでワイルドカードまたは複数のホスト名を使用する場合の考慮事項と制限事項:

*   [SSL ターミネーションとエンド ツー エンド SSL](ssl-overview.md) では、プロトコルを HTTPS として構成し、リスナー構成で使用される証明書をアップロードする必要があります。 マルチサイト リスナーの場合は、ホスト名も入力できます。通常、これは SSL 証明書の CN です。 リスナーで複数のホスト名を指定する場合、またはワイルドカード文字を使用する場合は、次の点を考慮する必要があります。
    *   *.contoso.com のようなワイルドカードのホスト名の場合、*.contoso.com のような CN を含むワイルドカード証明書をアップロードする必要があります
    *   同じリスナーで複数のホスト名が指定されている場合は、指定されているホスト名と一致する CN が含まれる SAN 証明書 (サブジェクトの別名) をアップロードする必要があります。
*   正規表現を使用してホスト名を指定することはできません。 ホスト名のパターンを構成するには、アスタリスク (*) や疑問符 (?) などのワイルドカード文字のみを使用できます。
*   バックエンドの正常性チェックでは、HTTP の設定ごとに複数の[カスタム プローブ](application-gateway-probe-overview.md)を関連付けることはできません。 代わりに、バックエンドで Web サイトの 1 つをプローブするか、"127.0.0.1" を使用してバックエンド サーバーの localhost をプローブすることができます。 ただし、リスナーでワイルドカードまたは複数のホスト名を使用すると、指定したすべてのドメイン パターンに対する要求が、規則の種類 (基本またはパスベース) に応じてバックエンド プールにルーティングされます。
*   プロパティ "hostname" は入力として 1 つの文字列を受け取り、ワイルドカード以外のドメイン名を 1 つだけ指定できます。"hostnames" は入力として文字列の配列を受け取り、最大 5 つのワイルドカード ドメイン名を指定できます。 ただし、両方のプロパティを同時に使用することはできません。
*   ワイルドカードまたは複数のホスト名を使用するターゲット リスナーを使用して、[リダイレクト](redirect-overview.md)規則を作成することはできません。

マルチサイト リスナーでワイルドカード ホスト名を構成する方法に関するステップ バイ ステップ ガイドについては、[Azure PowerShell を使用したマルチサイトの作成](tutorial-multiple-sites-powershell.md)または [Azure CLI の使用](tutorial-multiple-sites-cli.md)に関する記事を参照してください。

## <a name="host-headers-and-server-name-indication-sni"></a>ホスト ヘッダーと Server Name Indication (SNI)

同じインフラストラクチャで複数サイトのホストを有効にするための一般的な方法は 3 つあります。

1. 複数の Web アプリケーションをそれぞれ、一意の IP アドレスでホストする。
2. ホスト名を使用して、同じ IP アドレスで複数の Web アプリケーションをホストする。
3. さまざまなポートを使用して、同じ IP アドレスで複数の Web アプリケーションをホストする。

現在、Application Gateway は、トラフィックをリッスンする単一のパブリック IP アドレスをサポートします。 そのため、それぞれ独自の IP アドレスを持つ複数のアプリケーションは、現在サポートされていません。 

Application Gateway は、それぞれが異なるポートをリッスンしている複数のアプリケーションをサポートしていますが、このシナリオではアプリケーションが非標準ポートでトラフィックを受け入れることが必要です。 これは多くの場合、望ましい構成ではありません。

Application Gateway は、複数の Web サイトを同じパブリック IP アドレスとポートでホストするために、HTTP 1.1 ホスト ヘッダーを利用します。 アプリケーション ゲートウェイでホストされているサイトでは、Server Name Indication (SNI) TLS 拡張機能によって TLS オフロードをサポートすることもできます。 つまり、クライアント ブラウザーとバックエンド Web ファームは、HTTP/1.1 と、RFC 6066 で定義されている TLS 拡張機能をサポートする必要があります。

## <a name="next-steps"></a>次のステップ

Application Gateway で複数サイトのホスティングを構成する方法を確認します
* [Azure Portal の使用](create-multiple-sites-portal.md)
* [Azure PowerShell の使用](tutorial-multiple-sites-powershell.md) 
* [Azure CLI の使用](tutorial-multiple-sites-cli.md)

エンド ツー エンドのテンプレート ベースのデプロイについては、[複数サイトのホスティングを使用する Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)を参照してください。
