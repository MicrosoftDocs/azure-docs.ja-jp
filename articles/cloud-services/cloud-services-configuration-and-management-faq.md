---
title: 構成と管理に関する問題についてよくあるご質問
description: この記事では、Microsoft Azure Cloud Services の構成と管理についてよくあるご質問を紹介します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 31659f4e8e4f9e25a997be54223b8856edfa8abe
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612985"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Azure Cloud Services (クラシック) の構成と管理の問題: よく寄せられる質問 (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

この記事では、[Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) の構成と管理の問題についてよくあるご質問を紹介します。 サイズについては、 [Cloud Services VM サイズのページ](cloud-services-sizes-specs.md) を参照してください。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**証明書**

- [私のクラウド サービスの TLS または SSL 証明書の証明書チェーンが不完全なのはなぜですか。](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- ["Windows Azure Tools Encryption Certificate for Extensions" の目的は何ですか。](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [インスタンスに "RDP 接続" せずに証明書署名要求 (CSR) を生成する方法を教えてください。](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [クラウド サービス管理証明書の期限が切れました。更新する方法を教えてください。](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [メイン TLS または SSL 証明書 (.pfx) と中間証明書 (.p7b) のインストールを自動化する方法を教えてください。](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- ["Microsoft Azure Service Management for MachineKey" 証明書の目的は何ですか。](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**監視およびログ記録**

- [アプリケーションの管理と監視に利用できる Azure Portal の今後の Cloud Service 機能について教えてください。](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [IIS によるログ ディレクトリへの書き込みが停止するのはなぜですか。](#why-does-iis-stop-writing-to-the-log-directory)
- [Cloud Services の WAD ログ記録を有効にする方法を教えてください。](#how-do-i-enable-wad-logging-for-cloud-services)

**ネットワーク構成**

- [Azure Load Balancer のアイドル タイムアウトを設定する方法を教えてください。](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [私のクラウド サービスに静的 IP アドレスを関連付ける方法を教えてください。](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Azure の基本的な IPS/IDS と DDoS が提供する機能には何がありますか。](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Cloud Services VM で HTTP/2 を有効にする方法を教えてください。](#how-to-enable-http2-on-cloud-services-vm)

**アクセス許可**

- [Microsoft 内部のエンジニアは、許可なくクラウド サービスのインスタンスにリモート デスクトップ接続できますか。](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [RDP ファイルを使ってクラウド サービス VM にリモート デスクトップ接続できません。"認証エラーが発生しました (コード: 0x80004005)" というエラーが発生します。](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**スケーリング**

- [X 個のインスタンスを超えて拡張できません。](#i-cannot-scale-beyond-x-instances)
- [メモリ メトリックに基づく自動スケールを構成する方法を教えてください。](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**全般**

- [Web サイトに `nosniff` を追加する方法を教えてください。](#how-do-i-add-nosniff-to-my-website)
- [IIS の Web ロールをカスタマイズする方法を教えてください。](#how-do-i-customize-iis-for-a-web-role)
- [クラウド サービスのクォータ制限とは何ですか。](#what-is-the-quota-limit-for-my-cloud-service)
- [クラウド サービス VM 上のドライブで表示される空きディスク領域が非常に小さいのはなぜですか。](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Cloud Services にマルウェア対策拡張機能を自動的に追加する方法を教えてください。](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Cloud Services で Server Name Indication (SNI) を有効にする方法を教えてください。](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Azure Cloud Service にタグを追加する方法を教えてください。](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure Portal に使っているクラウド サービスの SDK のバージョンが表示されません。表示する方法を教えてください。](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [数か月間、クラウド サービスをシャットダウンしようと思います。IP アドレスを失うことなくクラウド サービスの料金を減らす方法を教えてください。](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>証明書

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>私のクラウド サービスの TLS または SSL 証明書の証明書チェーンが不完全なのはなぜですか。
    
Microsoft では、リーフ証明書だけではなく、完全な証明書チェーン (リーフ証明書、中間証明書、およびルート証明書) をインストールすることをお客様にお勧めしています。 お客様がリーフ証明書だけをインストールした場合、CTL を探索して証明書チェーンを構築することについては Windows の処理に頼ることになります。 Windows が証明書を検証しようとしたときに、Azure または Windows Update で断続的なネットワークの問題や DNS の問題が発生すると、証明書が無効と見なされる可能性があります。 完全な証明書チェーンをインストールすると、この問題を回避できます。 これを行う方法については、ブログ記事「[How to install a chained SSL certificate](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) (チェーンされた SSL 証明書をインストールする方法)」をご覧ください。

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>"Windows Azure Tools Encryption Certificate for Extensions" の目的は何ですか。

拡張機能がクラウド サービスに追加されると、必ず、これらの証明書が自動的に作成されます。 ほとんどの場合、これは WAD 拡張機能または RDP 拡張機能ですが、その他のマルウェア対策やログ コレクター拡張機能であることもあります。 これらの証明書は、拡張機能のプライベート構成を暗号化および復号化するためだけに使用されます。 有効期限の日付は確認されないため、証明書の有効期限が切れていてもかまいません。 

これらの証明書を無視することができます。 証明書をクリーンアップする場合、それらの証明書すべての削除を試行することができます。 使用中の証明書を削除しようとすると、Azure からエラーがスローされます。

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>インスタンスに "RDP 接続" せずに証明書署名要求 (CSR) を生成する方法を教えてください。

次のガイダンス ドキュメントをご覧ください。

[Obtaining a certificate for use with Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/) (Windows Azure Web サイト (WAWS) で使用する証明書の取得)

CSR は単なるテキスト ファイルです。 必ずしも、最終的に証明書が使用されるマシンから作成しないでもかまいません。 このドキュメントは App Service 用に書かれるものですが、CSR の作成は汎用的で、Cloud Services にも適用されます。

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>クラウド サービス管理証明書の期限が切れました。 更新する方法を教えてください。

次の PowerShell コマンドを使って、管理証明書を更新できます。

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

**Get-AzurePublishSettingsFile** は、Azure Portal の **[サブスクリプション]**  >  **[管理証明書]** に新しい管理証明書を作成します。 新しい証明書の名前は、"<サブスクリプション名>-<今日の日付>-credentials" のようになります。

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>メイン TLS または SSL 証明書 (.pfx) と中間証明書 (.p7b) のインストールを自動化する方法を教えてください。

このタスクはスタートアップ スクリプト (batch/cmd/PowerShell) を使って自動化することができ、サービス定義ファイルでそのスタートアップ スクリプトを登録します。 スタートアップ スクリプトと証明書 (.p7b ファイル) の両方を、スタートアップ スクリプトの同じディレクトリのプロジェクト フォルダーに追加します。

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>"Microsoft Azure Service Management for MachineKey" 証明書の目的は何ですか。

この証明書は、Azure Web ロールでマシン キーを暗号化するために使用されます。 詳しくは、[こちらのアドバイザリ](/security-updates/securityadvisories/2018/4092731)をご覧ください。

詳細については、次の記事を参照してください。
- [クラウド サービスのスタートアップ タスクを構成して実行する方法](./cloud-services-startup-tasks.md)
- [クラウド サービス共通のスタートアップ タスク](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>監視およびログ記録

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>アプリケーションの管理と監視に利用できる Azure Portal の今後の Cloud Service 機能について教えてください。

リモート デスクトップ プロトコル (RDP) の新しい証明書を生成する機能が、リリースされる予定です。 代わりに、次のスクリプトを実行してもかまいません。

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
csdef および cscfg のアップロード先に blob またはローカルを選択する機能が、リリースされる予定です。 [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment) を使用して、各場所の値を設定できます。

インスタンス レベルでメトリックを監視する機能。 追加の監視機能は、「[クラウド サービスの監視方法](cloud-services-how-to-monitor.md)」に従って利用できます。

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>IIS によるログ ディレクトリへの書き込みが停止するのはなぜですか。
ログ ディレクトリに書き込むためのローカル ストレージ クォータを使い果たしたからです。 これを修正するには、次の 3 つのいずれかの操作を行います。
* IIS の診断を有効にし、診断を定期的に BLOB ストレージに移動する。
* ログ ディレクトリからログ ファイルを手動で削除する。
* ローカル リソースのクォータ制限を大きくする。

詳細については、以下のドキュメントをご覧ください。
* [Azure Storage への診断データの保存と表示](../storage/common/storage-introduction.md)
* [IIS Logs stop writing in Cloud Service](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service) (IIS ログがクラウド サービスで書き込みを停止する)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Cloud Services の WAD ログ記録を有効にする方法を教えてください。
Windows Azure Diagnostics (WAD) ログ記録は、次のオプションを使用して有効にできます。
1. [Visual Studio から有効にする](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [.NET コードを使用して有効にする](./cloud-services-dotnet-diagnostics.md)
3. [PowerShell を使用して有効にする](./cloud-services-diagnostics-powershell.md)

Cloud Services の現在の WAD 設定を取得するには、[Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) PowerShell コマンドを使用できます。または、ポータルの [クラウド サービス] --> [拡張機能] ブレードにそれを表示できます。


## <a name="network-configuration"></a>ネットワーク構成

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer のアイドル タイムアウトを設定する方法を教えてください。
サービス定義 (csdef) ファイルで、次のようにしてタイムアウトを指定できます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
詳細については、「[New: Configurable Idle Timeout for Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) (Azure Load Balancer の構成可能なアイドル タイムアウト)」を参照してください。

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>私のクラウド サービスに静的 IP アドレスを関連付ける方法を教えてください。
静的 IP アドレスを設定するには、予約済み IP を作成する必要があります。 この予約済み IP は、新しいクラウド サービスに、または既存のデプロイに関連付けることができます。 詳細については、次のドキュメントをご覧ください。
* [予約済み IP アドレスを作成する方法](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [既存のクラウド サービスの IP アドレスを予約する](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [予約済み IP を新しいクラウド サービスに関連付ける](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [予約済み IP を実行中のデプロイに関連付ける](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [サービス構成ファイルを使用してクラウド サービスに予約済み IP を関連付ける](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure の基本的な IPS/IDS と DDoS が提供する機能には何がありますか。
Azure では、脅威に対する防御のため、データセンターの物理サーバーに IPS/IDS を備えています。 それに加えて、お客様は、Web アプリケーション ファイアウォール、ネットワーク ファイアウォール、マルウェア対策、侵入検出/防御システム (IDS/IPS) などのサード パーティ製のセキュリティ ソリューションをデプロイすることができます。 詳細については、「[データと資産を保護し、国際的なセキュリティ基準に準拠](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)」をご覧ください。

Microsoft では、脅威を検出するためにサーバー、ネットワーク、アプリケーションを継続的に監視しています。 Azure の多面的な脅威管理アプローチでは、侵入の検出、分散型サービス拒否 (DDoS) 攻撃の防止、侵入テスト、行動分析、異常検出、機械学習を利用して、常に防御を強化し、リスク軽減に努めています。 Azure 向けの Microsoft マルウェア対策は、Azure Cloud Services および Virtual Machines を保護します。 お客様は、Web アプリケーション ファイアウォール、ネットワーク ファイアウォール、マルウェア対策、侵入検出/防御システム (IDS/IPS)、その他、サード パーティ製のセキュリティ ソリューションを必要に応じてデプロイすることができます。

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Cloud Services VM で HTTP/2 を有効にする方法を教えてください。

Windows 10 と Windows Server 2016 は、クライアントとサーバー側の両方で HTTP/2 に対応しています。 クライアント (ブラウザー) が、TLS 拡張機能で HTTP/2 とネゴシエートする TLS 上の IIS サーバーに接続している場合、サーバー側で変更を加える必要はありません。 これは、HTTP/2 の使用を指定した h2-14 ヘッダーが、既定で TLS 上で送信されるためです。 一方、クライアントが HTTP/2 にアップグレードするために Upgrade ヘッダーを送信する場合は、サーバー側で次の変更を加えることで、Upgrade が機能し、最終的に HTTP/2 接続できるようにする必要があります。 

1. regedit.exe を実行します。
2. レジストリ キー HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters を参照します。
3. **DuoEnabled** という新しいダブルワード値を作成します。
4. 値を 1 に設定します。
5. サーバーを再起動します。
6. **[既定の Web サイト]** に移動し、 **[バインド]** で、作成した自己署名証明書を使用して新しい TLS バインドを作成します。 

詳細については、次を参照してください。

- [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (IIS 上の HTTP/2)
- [ビデオ: HTTP/2 in Windows 10: Browser, Apps and Web Server](https://channel9.msdn.com/Events/Build/2015/3-88) (Windows 10 の HTTP/2: ブラウザー、アプリ、Web サーバー)
         

上記の手順は、スタートアップ タスクで、新しい PaaS インスタンスを作成するたびに、システム レジストリで上記の変更を実行するように自動化することができます。 詳しくは、「[クラウド サービスのスタートアップ タスクを構成して実行する方法](cloud-services-startup-tasks.md)」をご覧ください。

 
この処理を完了すると、次のいずれかの方法を使用して、HTTP/2 が有効かどうかを確認できるようになります。

- IIS ログでプロトコルのバージョンを有効にして、IIS ログを確認します。 ログには HTTP/2 が表示されます。 
- Internet Explorer または Microsoft Edge で F12 Developer Tool を有効にして、[ネットワーク] タブに切り替えてプロトコルを確認します。 

詳細については、「[HTTP/2 on IIS](https://blogs.iis.net/davidso/http2)」(IIS 上の HTTP/2) を参照してください。

## <a name="permissions"></a>アクセス許可

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Cloud Services のロールベースのアクセスを実装するにはどうすればよいですか。
Cloud Services は、Azure Resource Manager ベースのサービスではないため、Azure ロールベースのアクセス制御 (Azure RBAC) モデルをサポートしていません。

「[Azure での各種ロールについて](../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

## <a name="remote-desktop"></a>リモート デスクトップ

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Microsoft 内部のエンジニアは、許可なくクラウド サービスのインスタンスにリモート デスクトップ接続できますか。
Microsoft では、所有者、またはその指名を受けた担当者からの書面 (電子メールまたはその他の書面による通信) による許可なしで、内部のエンジニアがお客様のクラウド サービスにリモート デスクトップ接続することがないように、厳格な手続きに従っております。

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>RDP ファイルを使ってクラウド サービス VM にリモート デスクトップ接続できません。 "認証エラーが発生しました (コード: 0x80004005)" というエラーが発生します。

このエラーは、Azure Active Directory に参加しているコンピューターから RDP ファイルを使った場合に発生する可能性があります。 この問題を解決するには、次の手順に従ってください。

1. ダウンロードした RDP ファイルを右クリックして、 **[編集]** を選びます。
2. ユーザー名の前にプレフィックスとして "&#92;" を追加します。 たとえば、**username** の代わりに **.\username** を使います。

## <a name="scaling"></a>Scaling

### <a name="i-cannot-scale-beyond-x-instances"></a>X 個のインスタンスを超えて拡張できない
Azure サブスクリプションには、使用できるコアの数に制限があります。 使用可能なすべてのコアを既に使用している場合、スケーリングは機能しません。 たとえば、コア数が 100 に制限されている場合、これは、クラウド サービスでは A1 サイズの仮想マシン インスタンスを 100 個、または A2 サイズの仮想マシン インスタンスを 50 個使用できることを意味します。

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>メモリ メトリックに基づく自動スケールを構成する方法を教えてください。

Cloud Services のメモリ メトリックに基づく自動スケールは、現在サポートされていません。 

この問題を回避するには、Application Insights を使うことができます。 自動スケールは、メトリック ソースとして Application Insights をサポートし、"Memory" のようなゲスト メトリックに基づいてロール インスタンスの数をスケーリングできます。  クラウド サービスのプロジェクト パッケージ ファイル (*.cspkg) で Application Insights を構成し、サービスで Azure Diagnostics 拡張機能を有効にしてこの機能を実装する必要があります。

Application Insights でカスタム メトリックを利用してクラウド サービスに自動スケールを構成する方法について詳しくは、「[Azure でのカスタム メトリックによる自動スケールの概要](../azure-monitor/autoscale/autoscale-custom-metric.md)」をご覧ください

クラウド サービスの Application Insights に Azure Diagnostics を統合する方法について詳しくは、「[Cloud Services、Virtual Machines、または Service Fabric の診断データを Application Insights に送信する](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)」をご覧ください

Cloud Services 用に Application Insights を有効にする方法について詳しくは、「[Azure Cloud Services 向けの Application Insights](../azure-monitor/app/cloudservices.md)」をご覧ください

Cloud Services 用に Azure Diagnostics ログを有効にする方法について詳しくは、「[Azure クラウド サービスと仮想マシンに対する診断を設定する](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)」をご覧ください

## <a name="generic"></a>ジェネリック

### <a name="how-do-i-add-nosniff-to-my-website"></a>Web サイトに `nosniff` を追加する方法を教えてください。
クライアントが MIME の種類をスニッフィングできないように、*web.config* ファイルに設定を追加します。

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

これは、IIS に設定として追加することもできます。 [一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)に関する記事を参照して、次のコマンドを使用します。

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>IIS の Web ロールをカスタマイズする方法を教えてください。
[一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)に関する記事にある IIS スタートアップ スクリプトを使用します。

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>クラウド サービスのクォータ制限とは何ですか。
[サービス固有の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)のページをご覧ください。

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>クラウド サービス VM 上のドライブで表示される空きディスク領域が非常に小さいのはなぜですか。
これは想定される動作であり、アプリケーションに問題が発生することはないはずです。 Azure PaaS VM の %approot% ドライブでジャーナルがオンになっているため、実質的に、ファイルが通常占有する領域の 2 倍の容量が消費されます。 ただし、この件で問題が起きないようにするには、いくつかの事柄に注意する必要があります。

%approot% ドライブのサイズは、<.cspkg のサイズ + ジャーナルの最大サイズ + 空き領域のマージン> の計算値と、1.5 GB のうち、どちらか大きいほうになります。 VM のサイズは、この計算に影響を与えません。 (VM のサイズは、一時的な C: ドライブのサイズにのみ影響を与えます。) 

%approot% ドライブへの書き込みはサポートされていません。 Azure VM に対して書き込む場合は、一時的な LocalStorage リソース (または、BLOB ストレージ、Azure Files など、その他のオプション) に対して書き込む必要があります。 したがって、%approot% フォルダー上の空き領域の量は重要ではありません。 アプリケーションが %approot% ドライブに対して書き込んでいるかどうか不明の場合は、サービスを数日間実行し、"実行前" と "実行後" のサイズを比較することができます。 

Azure は、%approot% ドライブに対して何も書き込みません。 `.cspkg` から VHD が作成され、Azure VM にマウントされた後、このドライブに書き込むことがある唯一の機能はアプリケーションです。 

ジャーナルの設定は構成不可であるため、オフにすることはできません。

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Cloud Services にマルウェア対策拡張機能を自動的に追加する方法を教えてください。

スタートアップ タスクで PowerShell スクリプトを使用してマルウェア対策拡張機能を有効にすることができます。 次の記事の手順に従って実装してください。 
 
- [PowerShell のスタートアップ タスクを作成する](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension)

マルウェア対策デプロイ シナリオの詳細とポータルから有効にする方法については、「[マルウェア対策のデプロイ シナリオ](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)」を参照してください。

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Cloud Services で Server Name Indication (SNI) を有効にする方法を教えてください。

Cloud Services で SNI を有効にするには、次のいずれかの方法を使用します。

**方法 1: PowerShell を使用する**

SNI バインドは、以下のようにクラウド サービス ロール インスタンスのスタートアップ タスクで PowerShell コマンドレット **New-WebBinding** を使って構成できます。

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

[こちら](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10))の説明のように、$sslFlags には次の値のいずれかを使用できます。

|値|意味|
------|------
|0|SNI なし|
|1|SNI が有効|
|2|中央証明書ストアを使用する SNI 以外のバインド|
|3|中央証明書ストアを使用する SNI バインド|
 
**方法 2: コードを使用する**

SNI バインドは、こちらの[ブログ投稿](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service)のように、ロール スタートアップでコードを使用して構成することもできます。

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
SNI バインドを有効にするには、上記のいずれかのアプローチを使用して、まずスタートアップ タスクまたはコードを使用して、各ホスト名に対応する証明書 (*.pfx) をロール インスタンスにインストールする必要があります。

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Azure Cloud Service にタグを追加する方法を教えてください。 

クラウド サービスはクラシック リソースです。 Azure Resource Manager で作成したリソースだけがタグに対応しています。 クラウド サービスなど、クラシック リソースにタグを適用することはできません。 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure Portal に使っているクラウド サービスの SDK のバージョンが表示されません。 表示する方法を教えてください。

現在、Azure Portal にこの機能を組み込む作業を行っています。 その間は、次の PowerShell コマンドを使って SDK のバージョンを取得できます。

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>数か月間、クラウド サービスをシャットダウンしようと思います。 IP アドレスを失うことなくクラウド サービスの料金を減らす方法を教えてください。

既にデプロイ済みのクラウド サービスについては、コンピューティングとストレージの使用量に対して課金されます。 そのため、Azure VM をシャットダウンした場合でも、ストレージについては引き続き料金がかかります。 

サービスの IP アドレスを失うことがなく請求額を減らすには次のような方法があります。

1. デプロイを削除する前に、[IP アドレスを予約](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)します。  この IP アドレスに対してのみ課金されます。 IP アドレスの課金について詳しくは、「[IP アドレスの価格](https://azure.microsoft.com/pricing/details/ip-addresses/)」をご覧ください。
2. デプロイを削除します。 後で使えるように、xxx.cloudapp.net は削除しないでください。
3. サブスクリプションで予約したものと同じ予約 IP を使ってクラウド サービスを再デプロイする場合、「[Reserved IP addresses for Cloud Services and Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/)」(クラウド サービスおよび仮想マシンに対する予約済み IP アドレス) をご覧ください。