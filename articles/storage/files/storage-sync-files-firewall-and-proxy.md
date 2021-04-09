---
title: Azure File Sync のオンプレミスのファイアウォール設定とプロキシ設定 | Microsoft Docs
description: オンプレミスのプロキシ設定とファイアウォール設定の Azure File Sync について理解します。 ポート、ネットワーク、Azure への特別な接続の構成の詳細を確認します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dbe7f32f14eb4da3d591811d619eb2e9bea397
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729642"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure File Sync のプロキシとファイアウォールの設定
Azure File Sync は、オンプレミスのサーバーを Azure Files に接続することで、マルチサイトの同期とクラウドの階層化の機能を実現します。 そのため、オンプレミスのサーバーがインターネットに接続されている必要があります。 サーバーから Azure Cloud Services に到達するための最適なパスは、IT 管理者が決める必要があります。

この記事では、ご利用のサーバーを Azure File Sync に正しく安全に接続するための具体的な要件と選択肢についての分析情報を提供します。

この攻略ガイドを読む前に、「[Azure File Sync のネットワークに関する考慮事項](storage-sync-files-networking-overview.md)」を読むことをお勧めします。

## <a name="overview"></a>概要
Azure File Sync は、Windows Server と Azure ファイル共有など各種 Azure サービスとの間のオーケストレーション サービスとして機能し、同期グループ内の定義に従ってデータを同期します。 Azure File Sync を正しく機能させるためには、次の Azure サービスと通信するための構成をサーバーに対して行う必要があります。

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- 認証サービス

> [!Note]  
> クラウド サービスに対する要求はすべて、Windows Server 上の Azure File Sync エージェントによって開始されます。したがってファイアウォールの観点から考慮する必要があるのは送信トラフィックだけです。 <br /> Azure File Sync エージェントへの接続が Azure サービス側から開始されることはありません。

## <a name="ports"></a>Port
Azure File Sync は HTTPS のみを使ってファイル データとメタデータを移動するため、送信方向のポート 443 を開放する必要があります。
結果的にすべてのトラフィックが暗号化されます。

## <a name="networks-and-special-connections-to-azure"></a>Azure への特別な接続とネットワーク
Azure File Sync エージェントと Azure とのチャンネルに関して特別な要件 ([ExpressRoute](../../expressroute/expressroute-introduction.md) など) はありません。

Azure File Sync は、Azure に到達さえできれば、その接続手段を問わず正しく機能します。さまざまなネットワーク特性 (帯域幅、待ち時間など) に応じた自動調整のほか、管理制御による微調整の手段も用意されています。 一部の機能については、現時点では利用することができません。 構成の対象として希望する具体的な動作がある場合は、[Azure Files の UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) でお聞かせください。

## <a name="proxy"></a>プロキシ
Azure File Sync では、アプリ固有のプロキシ設定とマシン全体のプロキシ設定がサポートされています。

**アプリ固有のプロキシ設定** を使用すると、Azure File Sync のトラフィック専用のプロキシを構成できます。 アプリ固有のプロキシ設定はエージェント バージョン 4.0.1.0 以降でサポートされ、エージェントのインストール中に、または Set-StorageSyncProxyConfiguration PowerShell コマンドレットを使用して構成できます。

アプリ固有のプロキシ設定を構成する PowerShell コマンドを次に示します。
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
たとえば、プロキシ サーバーでユーザー名とパスワードを使用した認証を必要とする場合は、次の PowerShell コマンドを実行します。

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
**マシン全体のプロキシ設定** は、Azure File Sync エージェントに対して透過的です。このプロキシではサーバーのトラフィック全体がルーティングされるためです。

マシン全体のプロキシ設定を構成するには、次の手順のようにします。 

1. .NET アプリケーションのプロキシ設定を構成します 

   - 以下の 2 つのファイルを編集します。  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - <system.net> セクションを machine.config ファイルに追加します (<system.serviceModel> セクションの下)。  127.0.01:8888 をプロキシ サーバーの IP アドレスとポートに変更します。 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. WinHTTP のプロキシ設定を設定します 

   - 管理者特権でのコマンド プロンプトまたは PowerShell から次のコマンドを実行して、既存のプロキシ設定を表示します。   

     netsh winhttp show proxy

   - 管理者特権でのコマンド プロンプトまたは PowerShell から次のコマンドを実行して、プロキシ設定を設定します (127.0.01:8888 をプロキシ サーバーの IP アドレスとポートに変更します)。  

     netsh winhttp set proxy 127.0.0.1:8888

3. 管理者特権でのコマンド プロンプトまたは PowerShell から次のコマンドを実行して、ストレージ同期エージェント サービスを開始し直します。 

      net stop filesyncsvc

      注:ストレージ同期エージェント (filesyncsvc) サービスは、停止すると自動的に開始します。

## <a name="firewall"></a>ファイアウォール
前のセクションで述べたように、送信方向のポート 443 を開放する必要があります。 さらに、ご利用のデータセンターやブランチ、リージョンのポリシーによっては、このポート上のトラフィックを特定のドメインに制限することが望ましい、または必須となる場合もあります。

次の表で、通信に必要なドメインについて説明します。

| サービス | パブリック クラウド エンドポイント | Azure Government のエンドポイント | 使用法 |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | 初回サーバー登録呼び出しを含め、すべてのユーザー呼び出し (PowerShell など) は、この URL に向かうか、この URL を経由します。 |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager の呼び出しは、認証済みのユーザーが行う必要があります。 成功するためには、この URL を使用してユーザー認証を行う必要があります。 |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Azure File Sync をデプロイする過程で、サブスクリプションの Azure Active Directory のサービス プリンシパルを作成する必要があります。 この URL は、その際に使用されます。 このプリンシパルは、最小限の権限一式を Azure File Sync サービスに委任する目的で使用されます。 Azure File Sync の初回セットアップは、サブスクリプション所有者の権限を持った認証済みユーザーが実行する必要があります。 |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | パブリック エンドポイント URL を使用します。 | この URL は、管理者のログインの際に Azure File Sync サーバー登録 UI が使用する Active Directory 認証ライブラリによってアクセスされます。 |
| **Azure ストレージ** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | サーバーがファイルをダウンロードするとき、ストレージ アカウント内の Azure ファイル共有との間で直接通信を行った方が、データの移動を効率よく実行することができます。 このサーバーには、対象のファイル共有へのアクセスのみが許可された SAS キーが与えられます。 |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | サーバーの初回登録後、そのサーバーには、特定のリージョン内の Azure File Sync サービス インスタンスに使用されるリージョン固有の URL が送信されます。 サーバーは、この URL を使って、その同期処理を行うインスタンスと直接かつ効率的に通信を行います。 |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Azure File Sync エージェントがインストールされると、PKI URL を使用して、Azure File Sync サービスと Azure ファイル共有との通信に必要な中間証明書がダウンロードされます。 OCSP URL は、証明書の状態を確認するために使用されます。 |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Azure File Sync エージェントがインストールされると、Microsoft Update URL を使用して Azure File Sync エージェントの更新プログラムがダウンロードされます。 |

> [!Important]
> &ast;.afs.azure.net へのトラフィックを許可すると、同期サービスへのトラフィックのみが可能になります。 このドメインを使用する他の Microsoft サービスはありません。
> &ast;.one.microsoft.com へのトラフィックを許可すると、同期サービスに限らず、サーバーから出て行くそれ以外のトラフィックも許可されます。 サブドメインには、その他多くの Microsoft サービスが提供されています。

&ast;.afs.azure.net または &ast;.one.microsoft.com では範囲が広すぎる場合は、通信の許可の対象となる Azure Files Sync サービスのリージョン固有のインスタンスを明示的に指定することで、サーバーの通信を制限することができます。 どのインスタンスを選ぶかは、実際にサーバーのデプロイと登録を行ったストレージ同期サービスのリージョンによって異なります。 そのリージョンを、以下の表では "プライマリ エンドポイント URL" と記述しています。

事業継続とディザスター リカバリー (BCDR) 上の理由から、グローバル冗長ストレージ (GRS) アカウント内の Azure ファイル共有が指定されていることも考えられます。 そのようなケースで、万一長時間にわたる地域的な機能不全が生じた場合には、Azure ファイル共有がペア リージョンにフェールオーバーされます。 Azure File Sync がストレージとして使用するリージョン ペアは変わりません。 そのため、GRS ストレージ アカウントを使用している場合は、サーバーが Azure File Sync のペア リージョンと通信するための URL を別途有効にする必要があります。以下の表では、これを "ペア リージョン" と記述しています。 また、Traffic Manager のプロファイルの URL も有効にする必要があります。 これにより、万一フェールオーバーが発生した場合、ネットワーク トラフィックがペア リージョンに対してシームレスに再ルーティングされます。次の表では、これを "検出 URL" と記述しています。

| クラウド  | リージョン | プライマリ エンドポイントの URL | ペアのリージョン | 探索 URL |
|--------|--------|----------------------|---------------|---------------|
| パブリック |オーストラリア東部 | https:\//australiaeast01.afs.azure.net<br>https:\//kailani-aue.one.microsoft.com | オーストラリア南東部 | https:\//tm-australiaeast01.afs.azure.net<br>https:\//tm-kailani-aue.one.microsoft.com |
| パブリック |オーストラリア南東部 | https:\//australiasoutheast01.afs.azure.net<br>https:\//kailani-aus.one.microsoft.com | オーストラリア東部 | https:\//tm-australiasoutheast01.afs.azure.net<br>https:\//tm-kailani-aus.one.microsoft.com |
| パブリック | ブラジル南部 | https:\//brazilsouth01.afs.azure.net | 米国中南部 | https:\//tm-brazilsouth01.afs.azure.net |
| パブリック | カナダ中部 | https:\//canadacentral01.afs.azure.net<br>https:\//kailani-cac.one.microsoft.com | カナダ東部 | https:\//tm-canadacentral01.afs.azure.net<br>https:\//tm-kailani-cac.one.microsoft.com |
| パブリック | カナダ東部 | https:\//canadaeast01.afs.azure.net<br>https:\//kailani-cae.one.microsoft.com | カナダ中部 | https:\//tm-canadaeast01.afs.azure.net<br>https:\//tm-kailani.cae.one.microsoft.com |
| パブリック | インド中部 | https:\//centralindia01.afs.azure.net<br>https:\//kailani-cin.one.microsoft.com | インド南部 | https:\//tm-centralindia01.afs.azure.net<br>https:\//tm-kailani-cin.one.microsoft.com |
| パブリック | 米国中部 | https:\//centralus01.afs.azure.net<br>https:\//kailani-cus.one.microsoft.com | 米国東部 2 | https:\//tm-centralus01.afs.azure.net<br>https:\//tm-kailani-cus.one.microsoft.com |
| パブリック | 東アジア | https:\//eastasia01.afs.azure.net<br>https:\//kailani11.one.microsoft.com | 東南アジア | https:\//tm-eastasia01.afs.azure.net<br>https:\//tm-kailani11.one.microsoft.com |
| パブリック | 米国東部 | https:\//eastus01.afs.azure.net<br>https:\//kailani1.one.microsoft.com | 米国西部 | https:\//tm-eastus01.afs.azure.net<br>https:\//tm-kailani1.one.microsoft.com |
| パブリック | 米国東部 2 | https:\//eastus201.afs.azure.net<br>https:\//kailani-ess.one.microsoft.com | 米国中部 | https:\//tm-eastus201.afs.azure.net<br>https:\//tm-kailani-ess.one.microsoft.com |
| パブリック | ドイツ北部 | https:\//germanynorth01.afs.azure.net | ドイツ中西部 | https:\//tm-germanywestcentral01.afs.azure.net |
| パブリック | ドイツ中西部 | https:\//germanywestcentral01.afs.azure.net | ドイツ北部 | https:\//tm-germanynorth01.afs.azure.net |
| パブリック | 東日本 | https:\//japaneast01.afs.azure.net | 西日本 | https:\//tm-japaneast01.afs.azure.net |
| パブリック | 西日本 | https:\//japanwest01.afs.azure.net | 東日本 | https:\//tm-japanwest01.afs.azure.net |
| パブリック | 韓国中部 | https:\//koreacentral01.afs.azure.net/ | 韓国南部 | https:\//tm-koreacentral01.afs.azure.net/ |
| パブリック | 韓国南部 | https:\//koreasouth01.afs.azure.net/ | 韓国中部 | https:\//tm-koreasouth01.afs.azure.net/ |
| パブリック | 米国中北部 | https:\//northcentralus01.afs.azure.net | 米国中南部 | https:\//tm-northcentralus01.afs.azure.net |
| パブリック | 北ヨーロッパ | https:\//northeurope01.afs.azure.net<br>https:\//kailani7.one.microsoft.com | 西ヨーロッパ | https:\//tm-northeurope01.afs.azure.net<br>https:\//tm-kailani7.one.microsoft.com |
| パブリック | 米国中南部 | https:\//southcentralus01.afs.azure.net | 米国中北部 | https:\//tm-southcentralus01.afs.azure.net |
| パブリック | インド南部 | https:\//southindia01.afs.azure.net<br>https:\//kailani-sin.one.microsoft.com | インド中部 | https:\//tm-southindia01.afs.azure.net<br>https:\//tm-kailani-sin.one.microsoft.com |
| パブリック | 東南アジア | https:\//southeastasia01.afs.azure.net<br>https:\//kailani10.one.microsoft.com | 東アジア | https:\//tm-southeastasia01.afs.azure.net<br>https:\//tm-kailani10.one.microsoft.com |
| パブリック | スイス北部 | https:\//switzerlandnorth01.afs.azure.net<br>https:\//tm-switzerlandnorth01.afs.azure.net | スイス西部 | https:\//switzerlandwest01.afs.azure.net<br>https:\//tm-switzerlandwest01.afs.azure.net |
| パブリック | スイス西部 | https:\//switzerlandwest01.afs.azure.net<br>https:\//tm-switzerlandwest01.afs.azure.net | スイス北部 | https:\//switzerlandnorth01.afs.azure.net<br>https:\//tm-switzerlandnorth01.afs.azure.net |
| パブリック | 英国南部 | https:\//uksouth01.afs.azure.net<br>https:\//kailani-uks.one.microsoft.com | 英国西部 | https:\//tm-uksouth01.afs.azure.net<br>https:\//tm-kailani-uks.one.microsoft.com |
| パブリック | 英国西部 | https:\//ukwest01.afs.azure.net<br>https:\//kailani-ukw.one.microsoft.com | 英国南部 | https:\//tm-ukwest01.afs.azure.net<br>https:\//tm-kailani-ukw.one.microsoft.com |
| パブリック | 米国中西部 | https:\//westcentralus01.afs.azure.net | 米国西部 2 | https:\//tm-westcentralus01.afs.azure.net |
| パブリック | 西ヨーロッパ | https:\//westeurope01.afs.azure.net<br>https:\//kailani6.one.microsoft.com | 北ヨーロッパ | https:\//tm-westeurope01.afs.azure.net<br>https:\//tm-kailani6.one.microsoft.com |
| パブリック | 米国西部 | https:\//westus01.afs.azure.net<br>https:\//kailani.one.microsoft.com | 米国東部 | https:\//tm-westus01.afs.azure.net<br>https:\//tm-kailani.one.microsoft.com |
| パブリック | 米国西部 2 | https:\//westus201.afs.azure.net | 米国中西部 | https:\//tm-westus201.afs.azure.net |
| Government | US Gov アリゾナ | https:\//usgovarizona01.afs.azure.us | US Gov テキサス | https:\//tm-usgovarizona01.afs.azure.us |
| Government | US Gov テキサス | https:\//usgovtexas01.afs.azure.us | US Gov アリゾナ | https:\//tm-usgovtexas01.afs.azure.us |

- 使用しているストレージ アカウントがローカル冗長 (LRS) またはゾーン冗長 (ZRS) の場合、有効にする必要があるのは、"プライマリ エンドポイント URL" に記載された URL だけです。

- グローバル冗長ストレージ (GRS) アカウントを使用している場合は、3 つの URL を有効にすることになります。

**例:** ストレージ同期サービスを `"West US"` にデプロイしてそこにサーバーを登録するとします。 この場合、サーバーには、次の URL との通信を許可することになります。

> - https:\//westus01.afs.azure.net (primary endpoint:米国西部)
> - https:\//eastus01.afs.azure.net (対になっているフェール オーバーリージョン: 米国東部)
> - https:\//tm-westus01.afs.azure.net (プライマリ リージョンの探索 URL)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Azure File Sync IP アドレスの許可リスト
Azure File Sync では[サービス タグ](../../virtual-network/service-tags-overview.md)の使用がサポートされています。これは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表すものです。 サービス タグを使用して、Azure File Sync サービスとの通信を可能にするファイアウォール規則を作成できます。 Azure File Sync のサービス タグは `StorageSyncService` です。

Azure 内で Azure File Sync を使用している場合は、ネットワーク セキュリティ グループでサービス タグ名を直接使用して、トラフィックを許可することができます。 詳しい方法については、「[セキュリティ グループ](../../virtual-network/network-security-groups-overview.md)」を参照してください。

オンプレミスの Azure File Sync を使用している場合は、サービス タグ API を使用して、ファイアウォールの許可リスト用の特定の IP アドレス範囲を取得できます。 この情報を取得するには、次の 2 つの方法があります。

- サービス タグをサポートするすべての Azure サービスの最新の IP アドレス範囲の一覧が、Microsoft ダウンロード センターに JSON ドキュメントの形式で毎週公開されています。 各 Azure クラウドには、そのクラウドに関連する IP アドレス範囲が記載された独自の JSON ドキュメントが存在します。
    - [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- サービス タグ検出 API (プレビュー) を使用すると、サービス タグの現在の一覧をプログラムで取得できます。 プレビューの段階では、サービス タグ検出 API によって返される情報は、Microsoft ダウンロード センターに公開されている JSON ドキュメントから返される情報よりも古い場合があります。 API サーフェスは、ご自分の自動化の設定に基づいて使用できます。
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

サービス タグ検出 API は、Microsoft ダウンロード センターに公開される JSON ドキュメントほど頻繁に更新されないため、JSON ドキュメントを使用して、オンプレミスのファイアウォールの許可リストを更新することをお勧めします。 そのためには、次の手順に従います。

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

その後、`$ipAddressRanges` 内の IP アドレス範囲を使用して、ファイアウォールを更新できます。 ファイアウォールを更新する方法については、ご利用のファイアウォールまたはネットワーク アプライアンスの Web サイトを確認してください。

## <a name="test-network-connectivity-to-service-endpoints"></a>サービス エンドポイントへのネットワーク接続をテストする
サーバーが Azure File Sync サービスに登録されたら、Test-StorageSyncNetworkConnectivity コマンドレットと ServerRegistration.exe を使用して、このサーバーに固有のすべてのエンドポイント (URL) との通信をテストできます。 このコマンドレットは、不完全な通信によってサーバーが Azure File Sync で完全に動作しない場合のトラブルシューティングに役立ちます。また、プロキシとファイアウォールの構成を微調整するために使用できます。

ネットワーク接続テストを実行するには、Azure File Sync エージェント バージョン 9.1 以降をインストールし、次の PowerShell コマンドを実行します。
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>概要とリスクの制限
このドキュメントで前述したリストは、現時点で Azure File Sync が通信する URL を記載したものです。 ファイアウォールで、これらのドメインに向かうトラフィックを許可する必要があります。 Microsoft は、このリストを最新の内容に保つよう努めます。

ドメインを制限するファイアウォール規則の設定は、セキュリティを強化するための対策になると考えられます。 そうしたファイアウォール構成を使用する場合、時間の経過に伴って URL が追加されること、また場合によっては変更される可能性もあることに留意する必要があります。 こちらの記事を定期的にご確認ください。

## <a name="next-steps"></a>次のステップ
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
- [Azure File Sync の監視](storage-sync-files-monitoring.md)
