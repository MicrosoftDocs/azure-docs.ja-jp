---
title: 動的 DNS を使用して Azure にホスト名を登録する | Microsoft Docs
description: 動的 DNS を使用して独自の DNS サーバーにホスト名を登録する方法を説明します。
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: ad91eb94aedcdd0e4e715162e3ae064a1d2fb1ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220424"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>動的 DNS を使用して独自の DNS サーバーでホスト名を登録する

仮想マシン (VM) とロール インスタンスに対して [Azure は名前解決を提供します](virtual-networks-name-resolution-for-vms-and-role-instances.md)。 Azure の既定の DNS によって提供される機能では対応できない名前解決が必要な場合は、独自の DNS サーバーを用意できます。 独自の DNS サーバーを使うと、独自の特定のニーズに合わせて、DNS ソリューションをカスタマイズすることができます。 たとえば、Active Directory ドメイン コントローラーを介したオンプレミスのリソースへのアクセスが必要になる場合があります。

カスタム DNS サーバーが Azure VM としてホストされていると、同じ仮想ネットワークのホスト名のクエリを Azure に転送して、ホスト名を解決できます。 このオプションを使用しない場合は、動的 DNS (DDNS) を使用して DNS サーバーに VM のホスト名を登録できます。 Azure には、DNS サーバーに直接レコードを作成するための資格情報がないため、多くの場合に代替の準備が必要になります。 一般的なシナリオと代替手段を次に示します。

## <a name="windows-clients"></a>Windows クライアント
ドメインに参加していない Windows クライアントは、起動時や IP アドレスの変更時に、セキュリティ保護されていない DDNS の更新を試みます。 DNS 名は、ホスト名とプライマリ DNS サフィックスの組み合わせです。 Azure はプライマリ DNS サフィックスを空白のままにしますが、VM で[ユーザー インターフェイス](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794784(v=ws.10))または [PowerShell](/powershell/module/dnsclient/set-dnsclient) を使ってサフィックスを設定できます。

ドメインに参加している Windows クライアントは、セキュリティ保護された DDNS を使用し、その IP アドレスをドメイン コント ローラーに登録します。 ドメイン参加プロセスでは、クライアントにプライマリ DNS サフィックスを設定し、信頼関係を作成して管理します。

## <a name="linux-clients"></a>Linux クライアント
Linux クライアントは一般に、起動時にそれ自体を DNS サーバーに登録することはなく、DHCP サーバーがそれを行うことを前提としています。 Azure の DHCP サーバーは、DNS サーバーにレコードを登録する資格情報を持っていません。 Bind パッケージに含まれる `nsupdate` という名前のツールを利用し、DDNS 更新を送信できます。 DDNS プロトコルは標準化されているため、DNS サーバーで Bind を使用していないときにも `nsupdate` を利用できます。

DHCP クライアントが提供するフックを使用して、DNS サーバー内にホスト名エントリを作成して登録できます。 DHCP 周期の間、クライアントは */etc/dhcp/dhclient-exit-hooks.d/* のスクリプトを実行します。 `nsupdate` を使って新しい IP アドレスを登録するには、フックを使用できます。 次に例を示します。

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

`nsupdate` コマンドを利用し、セキュリティ保護された DDNS 更新を実行することもできます。 たとえば、Bind DNS サーバーを利用するとき、公開鍵/秘密鍵のペアが生成されます (`http://linux.yyz.us/nsupdate/`)。 要求の署名を検証できるように、DNS サーバーは鍵の公開部分で構成されます (`http://linux.yyz.us/dns/ddns-server.html`)。 キー ペアを `nsupdate` に提供するには、署名対象の DDNS 更新要求に対して `-k` オプションを使います。

Windows DNS サーバーを利用しているときは、`nsupdate` の `-g` パラメーターで Kerberos 認証を使用できますが、Windows 版の `nsupdate` では利用できません。 Kerberos を使用するには、`kinit` を使って資格情報を読み込んでください。 たとえば、[keytab ファイル](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)から資格情報を読み込むことができ、その後 `nsupdate -g` はキャッシュから資格情報を取得します。

必要に応じて、DNS 検索サフィックスを VM に追加できます。 DNS サフィックスは、 */etc/resolv.conf* ファイルに指定します。 ほとんどの Linux ディストリビューションはこのファイルの内容を自動的に管理するため、通常は編集できません。 ただし、HCP クライアントの `supersede` コマンドを利用し、サフィックスをオーバーライドできます。 サフィックスをオーバーライドするには、次の行を */etc/dhcp/dhclient.conf* ファイルに追加します。

```
supersede domain-name <required-dns-suffix>;
```