---
title: アプリケーション プロキシ エージェント コネクタのインストール時の問題
description: Azure Active Directory 用のアプリケーション プロキシ エージェント コネクタのインストール時に発生する可能性のある問題のトラブルシューティング方法。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0f76f03883746b6f4b87bb817f8adde850ed28b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253666"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>アプリケーション プロキシ エージェント コネクタのインストール時の問題

Microsoft Azure Active Directory アプリケーション プロキシ コネクタは、発信接続を使用して、クラウドで使用可能なエンドポイントから内部のドメインへの接続を確立する、内部ドメイン コンポーネントです。

## <a name="general-problem-areas-with-connector-installation"></a>コネクタのインストールに関する一般的な問題領域

コネクタのインストールに失敗する場合、根本原因は通常、次の領域のいずれかにあります。 **トラブルシューティングの前段階として、コネクタを再起動してください。**

1.  **接続** – インストールを正常に完了するには、新しいコネクタを登録し、将来の信頼プロパティを確立する必要があります。 これは、Azure Active Directory アプリケーション プロキシ クラウド サービスに接続することによって行います。

2.  **信頼の確立** – 新しいコネクタは、自己署名証明書を作成し、クラウド サービスに登録します。

3.  **管理者の認証** – コネクタのインストールを完了するために、ユーザーはインストール時に管理者の資格情報を提供する必要があります。

> [!NOTE]
> コネクタのインストール ログは %TEMP% フォルダーにあり、インストール エラーの原因に関する追加情報を提供するのに役立ちます。

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>クラウド アプリケーション プロキシ サービスと Microsoft のログイン ページへの接続を確認する

**目的:** コネクタ マシンがアプリケーション プロキシの登録エンドポイントと Microsoft のログイン ページに接続できることを確認します。

1.  [telnet](/windows-server/administration/windows-commands/telnet) またはその他のポート テスト ツールを使用して、コネクタ サーバー上でポートのテストを実行して、ポート 443 と 80 が開いているかどうかを確認します。

2.  これらのポートのいずれかが正常に実行されない場合は、ファイアウォールまたはバックエンド プロキシが、必要なドメインおよびポートにアクセスできることを確認します。「[オンプレミスの環境を準備する](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)」を参照してください。

3.  ブラウザー (別のタブ) を開き、Web ページ `https://login.microsoftonline.com` に移動し、そのページにログインできることを確認します。

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>コンピューターとバックエンド コンポーネントでアプリケーション プロキシ信頼証明書がサポートされていることを確認する

**目的:** コネクタ コンピューターおよびバックエンドのプロキシとファイアウォールが将来の信頼のためにコネクタによって作成された証明書をサポートできることと、証明書が有効であることを確認します。

>[!NOTE]
>コネクタは、TLS1.2 でサポートされる SHA512 証明書の作成を試みます。 コンピューターまたはバックエンドのファイアウォールとプロキシで TLS1.2 がサポートされていない場合、インストールに失敗します。
>
>

**必要な前提条件の確認:**

1.  コンピューターで TLS 1.2 がサポートされていることを確認します。2012 R2 より後のすべてのバージョンの Windows では、TLS1.2 をサポートしています。 コネクタ コンピューターで 2012 R2 以前のバージョンを使用している場合は、サポート技術情報 (<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>) の更新プログラムがコンピューターにインストールされていることを確認してください。

2.  ネットワーク管理者に連絡し、バックエンドのプロキシとファイアウォールで発信トラフィックについて SHA512 がブロックされていないことを確認するよう依頼します。

**クライアント証明書を確認するには:**

現在のクライアント証明書のサムプリントを確認します。 証明書ストアは `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` にあります。

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

ありえる **IsInUserStore** の値は、**true** と **false** です。 値が **true** の場合、自動的に更新された証明書は、ネットワーク サービスのユーザー証明書ストアの個人用コンテナーに格納されることを意味します。 値が **false** の場合、Register-AppProxyConnector コマンドによって開始されたインストールまたは登録の際にクライアント証明書が作成され、それがローカル マシンの証明書ストアの個人用コンテナーに格納されることを意味します。

値が **true** の場合は、以下の手順に従って証明書を検証します。
1. [PsTools.zip](/sysinternals/downloads/pstools) をダウンロードします
2. パッケージから [PsExec](/sysinternals/downloads/psexec) を抽出し、管理者特権でのコマンド プロンプトから **psexec -i -u "nt authority\network service" cmd.exe** を実行します。
3. 新しく表示されたコマンド プロンプトで **certmgr.msc** を実行します
4. 管理コンソールで、[個人用] コンテナーを展開し、[証明書] をクリックします
5. **connectorregistrationca.msappproxy.net** によって発行された証明書を見つけます

値が **false** の場合は、以下の手順に従って証明書を検証します。
1. **certlm.msc** を実行します
2. 管理コンソールで、[個人用] コンテナーを展開し、[証明書] をクリックします
3. **connectorregistrationca.msappproxy.net** によって発行された証明書を見つけます

**クライアント証明書を更新するには:**

コネクタが数か月間サービスに接続していないと、証明書の有効期限が切れることがあります。 証明書の更新に失敗すると、証明書が期限切れになります。 これにより、コネクタ サービスが動作を停止します。 イベント 1000 がコネクタの管理ログに記録されます。

"Connector re-registration failed:The Connector trust certificate expired. Run the PowerShell cmdlet Register-AppProxyConnector on the computer on which the Connector is running to re-register your Connector." (コネクタの再登録に失敗しました: コネクタの信頼証明書の有効期限が切れました。コネクタが実行されているコンピューター上で PowerShell コマンドレット Register-AppProxyConnector を実行して、コネクタを再登録してください。)

このような場合は、コネクタをアンインストールしてから再インストールして、登録をトリガーするか、次の PowerShell コマンドを実行します。

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Register-AppProxyConnector コマンドの詳細については、「[Azure AD アプリケーション プロキシ コネクタ用の無人インストール スクリプトを作成します](./application-proxy-register-connector-powershell.md)」を参照してください

## <a name="verify-admin-is-used-to-install-the-connector"></a>コネクタのインストールに管理者を使用していることを確認する

**目的:** コネクタをインストールするユーザーが適切な資格情報を持つ管理者であることを確認します。 現時点では、正常にインストールするには、そのユーザーは、少なくともアプリケーション管理者である必要があります。

**資格情報が適切であることを確認するには:**

`https://login.microsoftonline.com` に接続し、同じ資格情報を使用します。 ログインに成功したことを確認します。 **[Azure Active Directory]**  -&gt; **[ユーザーとグループ]**  -&gt; **[すべてのユーザー]** と移動すると、ユーザー ロールを確認できます。 

ユーザー アカウントを選択してから、表示されたメニューの [ディレクトリ ロール] を選択します。 選択されているロールが "アプリケーション管理者" であることを確認します。 これらの手順に従っても、どのページにもアクセスできない場合、ユーザーは必要なロールを持っていません。

## <a name="next-steps"></a>次のステップ
[Azure AD アプリケーション プロキシ コネクタについて](application-proxy-connectors.md)
