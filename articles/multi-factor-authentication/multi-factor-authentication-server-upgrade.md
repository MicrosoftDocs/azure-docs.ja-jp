---
title: "Azure MFA Server のアップグレード | Microsoft Docs"
description: "Azure Multi-Factor Authentication Server を新しいバージョンにアップグレードする手順とガイダンスです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 6932d0da5f650121c4d0bdd0044fa696c96bcc5e
ms.lasthandoff: 03/06/2017

---


# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>最新の Azure Multi-Factor Authentication Server にアップグレードする

この記事では、Azure Multi-Factor Authentication (MFA) Server v6.0 以降をアップグレードする手順について説明します。 古いバージョンの PhoneFactor エージェントをアップグレードする必要がある場合は、「[PhoneFactor エージェントから Azure Multi-Factor Authentication Server へのアップグレード](multi-factor-authentication-get-started-server-upgrade.md)」をご覧ください。

v6.x 以前から v7.x 以降にアップグレードする場合、すべてのコンポーネントが .NET 2.0 から .NET 4.5 に変わります。 また、すべてのコンポーネントに Microsoft Visual C++ 2015 再頒布可能 Update 1 以降が必要です。 これらのコンポーネントの x86 および x64 バージョンがまだインストールされていない場合、MFA Server のインストーラーは両方のバージョンをインストールします。 ユーザー ポータルとモバイル アプリ Web サービスが異なるサーバーで動いている場合、コンポーネントをアップグレードする前にこれらのパッケージをインストールする必要があります。 最新の Microsoft Visual C++ 2015 再頒布可能 Update は [Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/)で見つかります。 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>最新バージョンの Azure MFA Server をインストールする

1. 「[Azure Multi-Factor Authentication Server のダウンロード](multi-factor-authentication-get-started-server.md#download-the-azure-multi-factor-authentication-server)」の説明に従って、Azure MFA Server の最新バージョンを入手します。
2. マスター MFA Server の C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (既定のインストール場所の場合) にある MFA Server データ ファイルのバックアップを作成します。
3. 高可用性のために複数のサーバーを実行している場合は、アップグレード中の MFA Server へのトラフィック送信を停止するように、MFA Server への認証を行うクライアント システムを変更します。 ロード バランサーを使っている場合は、アップグレード中の MFA Server をロード バランサーから削除し、アップグレードを行った後、ファームにサーバーを追加して戻します。
4. 各 MFA Server で新しいインストーラーを実行します。 下位サーバーはマスターによってレプリケートされる古いデータ ファイルを読み取ることができるため、最初に下位サーバーをアップグレードします。 

  インストーラーを実行する前に、現在の MFA Server をアンインストールする必要はありません。 インストーラーはインプレース アップグレードを実行します。 インストール パスは以前のインストールのレジストリから取得されるため、同じ場所にインストールされます (C:\Program Files\Multi-Factor Authentication Server など)。 
  
5. Microsoft Visual C++ 2015 再頒布可能 Update パッケージのインストールを求められた場合は、受け入れます。 パッケージの x86 バージョンと x64 バージョンの両方がインストールされます。
5. 前に Web サービス SDK がインストールされていた場合は、新しい Web サービス SDK のインストールを求められます。 新しい Web サービス SDK をインストールするときは、仮想ディレクトリ名が前にインストールされていた仮想ディレクトリ (たとえば、MultiFactorAuthWebServiceSdk) と一致することを確認してください。
6. すべての下位サーバーで手順を繰り返します。 下位サーバーの&1; つを新しいマスターに昇格させた後、元のマスター サーバーをアップグレードします。 

## <a name="upgrade-the-user-portal"></a>ユーザー ポータルをアップグレードする

1. ユーザー ポータルのインストール場所 (例: C:\inetpub\wwwroot\MultiFactorAuth) の仮想ディレクトリにある web.config ファイルのバックアップを作成します。 既定のテーマを変更してある場合は、App_Themes\Default フォルダーのバックアップも作成します。 既定のテーマを変更するより、Default フォルダーのコピーを作成して新しいテーマを作成することをお勧めします。
2. ユーザー ポータルが他の MFA Server コンポーネントと同じサーバーで稼働している場合、MFA Server のインストールではユーザー ポータルを更新するように求められます。 メッセージを受け入れてユーザー ポータルを更新し、仮想ディレクトリ名が以前にインストールされていた仮想ディレクトリと一致することを確認します (例: MultiFactorAuth)。
3. ユーザー ポータルが専用サーバーにインストールされている場合は、MFA Server のいずれかのインストール場所から MultiFactorAuthenticationUserPortalSetup64.msi ファイルをコピーし、ユーザー ポータル Web サーバーに置きます。 インストーラーを実行します。 

  Microsoft Visual C++ 2015 再頒布可能 Update 1 以降が必要であるというエラーが発生する場合は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/)から最新の Update パッケージをダウンロードしてインストールします。 X86 と x64 の両方のバージョンをインストールします。

4. 更新されたユーザー ポータル ソフトウェアをインストールした後、手順 1 で作成した web.config のバックアップと新しい web.config ファイルを比較します。 新しい web.config に新しい属性が存在しない場合は、バックアップの web.config ファイルを仮想ディレクトリにコピーして、新しいファイルを上書きします。 または、appSettings の値と Web サービス SDK の URL を、バックアップ ファイルからコピーして新しい web.config に貼り付けます。

複数のサーバーにユーザー ポータルがある場合は、それらすべてでインストールを繰り返します。 


## <a name="upgrade-the-mobile-app-web-service"></a>モバイル アプリ Web サービスをアップグレードする

1. モバイル アプリ Web サービスのインストール場所 (例: C:\inetpub\wwwroot\app or C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) の仮想ディレクトリにある web.config ファイルのバックアップを作成します。
2. MFA Server のいずれかのインストール場所から MultiFactorAuthenticationMobileAppWebServiceSetup64.msi ファイルをコピーし、モバイル アプリ登録 Web サーバーに置きます。
3. インストーラーを実行します。 

  Microsoft Visual C++ 2015 再頒布可能 Update 1 以降が必要であるというエラーが発生する場合は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/)から最新の Update パッケージをダウンロードしてインストールします。 X86 と x64 の両方のバージョンをインストールします。

4. 更新されたモバイル アプリ Web サービス ソフトウェアをインストールした後、手順 1 でバックアップした web.config ファイルと、インストーラーによってインストールされた新しい web.config ファイルを比較します。 新しい web.config に新しい属性が存在しない場合は、保存してあった web.config ファイルを仮想ディレクトリにコピーして戻し、インストールされた新しいファイルを上書きできます。 または、appSettings の値と Web サービス SDK の URL を、バックアップ ファイルからコピーして新しい web.config に貼り付けます。

複数のサーバーにモバイル アプリ Web サービスがある場合は、それらすべてでインストールを繰り返します。 

## <a name="upgrade-the-ad-fs-adapters"></a>AD FS アダプターをアップグレードする


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>MFA が AD FS とは異なるサーバーで実行されている場合

次の手順は、Multi-factor Authentication Server を AD FS サーバーとは別に実行している場合にのみ適用されます。 両方のサービスが同じサーバーで実行されている場合は、このセクションをスキップして、インストールの手順に進みます。 

1. AD FS で登録された既存の MultiFactorAuthenticationAdfsAdapter.config のコピーを保存するか、PowerShell コマンド `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]` を使って既存の構成をファイルにエクスポートします。 以前にインストールされていたバージョンに応じて、アダプターの名前は "WindowsAzureMultiFactorAuthentication" または "AzureMfaServerAuthentication" です。
2. MFA Server のインストール場所から AD FS サーバーに、次のファイルをコピーします。

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Register-MultiFactorAuthenticationAdfsAdapter.ps1 スクリプトを編集し、`-ConfigurationFilePath [path]` を Register-AdfsAuthenticationProvider コマンドの末尾に追加します。*[path]* は、MultiFactorAuthenticationAdfsAdapter.config ファイルまたは前の手順でエクスポートした構成ファイルへの完全なパスです。 

  新しい MultiFactorAuthenticationAdfsAdapter.config の属性を調べて、古い構成ファイルと一致するかどうかを確認します。 新しいバージョンで追加または削除された属性がある場合は、古い構成ファイルから新しい構成ファイルに属性値をコピーするか、または一致するように古い構成ファイルを変更します。

### <a name="install-new-ad-fs-adapters"></a>新しい AD FS アダプターをインストールする

> [!IMPORTANT] 
> このセクションの手順 3 から 8 を実行している間、ユーザーは 2 段階認証を実行する必要がなくなります。 AD FS を複数のクラスターで構成してある場合は、ファーム内の各クラスターを他のクラスターとは無関係に削除、アップグレード、復元して、ダウンタイムを避けることができます。

1. 一部の AD FS サーバーをファームから削除します。 他のサーバーがまだ稼働している状態で、これらのサーバーを更新します。
2. AD FS ファームから削除した各サーバーに、新しい AD FS アダプターをインストールします。 MFA Server が各 AD FS サーバーにインストールされている場合、MFA Server 管理 UX を使って更新できます。 それ以外の場合は、MultiFactorAuthenticationAdfsAdapterSetup64.msi を実行して更新します。 

  Microsoft Visual C++ 2015 再頒布可能 Update 1 以降が必要であるというエラーが発生する場合は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/)から最新の Update パッケージをダウンロードしてインストールします。 X86 と x64 の両方のバージョンをインストールします。

3. **[AD FS]** > **[認証ポリシー]** > **[グローバル多要素認証ポリシーの編集]** の順に移動します。 **WindowsAzureMultiFactorAuthentication** または **AzureMFAServerAuthentication** (現在インストールされているバージョンによって異なります) をオフにします。 

  この手順が完了した後、手順 8 が完了するまで、この AD FS クラスターでは MFA Server による 2 段階認証を使用できなくなります。

4. Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell スクリプトを実行して、古いバージョンの AD FS アダプターの登録を解除します。 スクリプトに記載されている *-Name* パラメーター ("WindowsAzureMultiFactorAuthentication" または "AzureMFAServerAuthentication") が、手順 3 で表示された名前と一致することを確認します。 集中的な構成があるため、これは同じ AD FS クラスター内のすべてのサーバーに適用されます。
5. Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell スクリプトを実行して、新しい AD FS アダプターを登録します。 集中的な構成があるため、これは同じ AD FS クラスター内のすべてのサーバーに適用されます。
6. AD FS ファームから削除した各サーバーで、AD FS サービスを再開します。
7. 更新されたサーバーを AD FS ファームに追加して戻し、他のサーバーをファームから削除します。
8. **[AD FS]** > **[認証ポリシー]** > **[グローバル多要素認証ポリシーの編集]** の順に移動します。 **AzureMfaServerAuthentication** を確認します。
9. 手順 2 を繰り返して、AD FS ファームから削除したサーバーを更新し、それらのサーバーの AD FS サービスを再開します。
10. これらのサーバーを AD FS ファームに追加して戻します。

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ](multi-factor-authentication-advanced-vpn-configurations.md)の例を入手する

- [MFA Server と Windows Server Active Directory を同期する](multi-factor-authentication-get-started-server-dirint.md)

- アプリケーションの [Windows 認証を構成する](multi-factor-authentication-get-started-server-windows.md)

