---
title: "動的 DNS を使用してホスト名を登録する"
description: "このページでは、独自の DNS サーバーにホスト名を登録するために動的 DNS をセットアップする方法の詳細を示しています。"
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: 
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4fffba6d95f4c9c30ab3a1ecd9dfeb7acd9119d
ms.lasthandoff: 11/17/2016


---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>動的 DNS を使用し、独自の DNS サーバーでホスト名を登録する
[名前を解決](virtual-networks-name-resolution-for-vms-and-role-instances.md) します。 ただし、Azure によって提供される名前解決を超えるニーズがある場合は、独自の DNS サーバーを設置できます。 これにより、独自の特定のニーズに合わせて、DNS ソリューションをカスタマイズすることができます。 たとえば、Active Directory ドメイン コントローラーを介したオンプレミスのリソースへのアクセスが必要になる場合があります。

カスタム DNS サーバーが Azure VM としてホストされるときに、同じ VNET のホスト名のクエリを Azure に転送して、ホスト名を解決できます。 このルートを使用しない場合は、動的 DNS を使用して DNS サーバーに VM のホスト名を登録できます。  Azure には、DNS サーバーに直接レコードを作成する機能 (資格情報など) がないため、多くの場合に代替の準備が必要になります。 一般的なシナリオと代替手段を次に示します。

## <a name="windows-clients"></a>Windows クライアント
ドメインに参加していない Windows クライアントは、起動時や IP アドレスの変更時に、セキュリティ保護されていない動的 DNS (DDNS) の更新を試みます。 DNS 名は、ホスト名とプライマリ DNS サフィックスの組み合わせです。 Azure はプライマリ DNS サフィックスを空白のままにしますが、これは VM で [UI](https://technet.microsoft.com/library/cc794784.aspx) または[自動化](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix)によって設定できます。

ドメインに参加している Windows クライアントは、セキュリティ保護された動的 DNS を使用し、その IP アドレスをドメイン コント ローラーに登録します。 ドメイン参加プロセスでは、クライアントにプライマリ DNS サフィックスを設定し、信頼関係を作成して管理します。

## <a name="linux-clients"></a>Linux クライアント
Linux クライアントは一般に、起動時にそれ自体を DNS サーバーに登録することはなく、DHCP サーバーがそれを行うことを前提としています。 Azure の DHCP サーバーは、DNS サーバーにレコードを登録する機能も資格も備えていません。  Bind パッケージに含まれる「 *nsupdate*」という名前のツールを利用し、動的 DNS 更新を送信できます。 動的 DNS プロトコルは標準化されているため、DNS サーバーで Bind を使用していないときにも *nsupdate* を利用できます。

DHCP クライアントが提供するフックを使用して、DNS サーバー内にホスト名エントリを作成して登録できます。 DHCP 周期の間、クライアントは */etc/dhcp/dhclient-exit-hooks.d/*のスクリプトを実行します。 *nsupdate*を利用し、新しい IP アドレスを登録するためにこれを利用できます。 次に例を示します。

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
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

        
        

*nsupdate* コマンドを利用し、セキュリティ保護された動的 DNS 更新を実行することもできます。 たとえば、Bind DNS サーバーを利用するとき、公開鍵/秘密鍵のペアが [生成されます](http://linux.yyz.us/nsupdate/)。  要求の署名を検証できるように、DNS サーバーは鍵の公開部分で [構成されます](http://linux.yyz.us/dns/ddns-server.html) 。 動的 DNS 更新要求に署名するために、*-k* オプションを利用し、*nsupdate* に鍵のペアを与える必要があります。

Windows DNS サーバーを利用しているとき、Kerberos 認証を利用し、*nsupdate* に *-g* パラメーターを指定できます (Windows 版の *nsupdate* では利用できません)。 その際、*kinit* を利用し、資格情報を読み込みます ([キータブ ファイル](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)などから). その後、*nsupdate -g* がキャッシュから資格情報を取得します。

必要に応じて、DNS 検索サフィックスを VM に追加できます。 DNS サフィックスは、 */etc/resolv.conf* ファイルに指定します。 ほとんどの Linux ディストリビューションはこのファイルの内容を自動的に管理するため、通常は編集できません。 ただし、HCP クライアントの *supersede* コマンドを利用し、サフィックスを上書きできます。 その際、 */etc/dhcp/dhclient.conf*で次を追加します。

        supersede domain-name <required-dns-suffix>;


