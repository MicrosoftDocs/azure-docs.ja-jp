---
title: Azure VM の特定の RDP エラー メッセージ | Microsoft Docs
description: Azure の Windows 仮想マシンに対してリモート デスクトップ接続を使用しようとしたときに表示される可能性のある特定のエラー メッセージを確認します
keywords: リモート デスクトップ エラー,リモート デスクトップ接続エラー,VM に接続できない,リモート デスクトップのトラブルシューティング
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea8a2fa3a37815f3a7a48078e408e6607dc37eb4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709277"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Azure の Windows VM に対する特定の RDP エラー メッセージのトラブルシューティング
Azure の Windows 仮想マシン (VM) に対してリモート デスクトップ接続を使用すると、特定のエラー メッセージが表示される場合があります。 この記事では、よく発生するエラー メッセージのいくつかと、それを解決するためのトラブルシューティング手順について詳しく説明します。 RDP を使用した VM への接続で問題が発生していても、特定のエラー メッセージが表示されない場合は、[リモート デスクトップのトラブルシューティング ガイド](troubleshoot-rdp-connection.md)を参照してください。

特定のエラー メッセージについては、次を参照してください。

* [ライセンスを提供するためのリモート デスクトップ ライセンス サーバーがないため、リモート セッションは切断されました](#rdplicense)。
* [リモート デスクトップは、コンピューター "name" を見つけることができません](#rdpname)。
* [認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。](#rdpauth)
* [Windows セキュリティ エラー:お使いの資格情報は機能しませんでした](#wincred)。
* [このコンピューターはリモート コンピューターに接続できません](#rdpconnect)。

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>ライセンスを提供するためのリモート デスクトップ ライセンス サーバーがないため、リモート セッションは切断されました。
原因: リモート デスクトップ サーバー ロールの 120 日間のライセンスの猶予期間が期限切れになっているため、ライセンスをインストールする必要があります。

回避策として、ポータルから RDP ファイルのローカル コピーを保存し、PowerShell コマンド プロンプトで次のコマンドを実行して接続します。 この手順により、この接続についてのみライセンスが無効になります。

        mstsc <File name>.RDP /admin

VM に対して 2 つ以上のリモート デスクトップ接続が同時に必要でない場合、サーバー マネージャーを使用して、リモート デスクトップ サーバーのロールを削除することができます。

詳細については、 ["使用できるリモート デスクトップ ライセンス サーバーがありません" で失敗する Azure VM](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)に関するブログ投稿をご覧ください。

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Remote Desktop は、コンピューター "name" を見つけることができません。
原因: コンピューター上のリモート デスクトップ クライアントが、RDP ファイルの設定内のコンピューターの名前を解決できません。

考えられる解決策:

* 組織のイントラネットを使用している場合は、コンピューターからプロキシ サーバーにアクセスでき、そのサーバーに HTTPS トラフィックを送信できることを確認してください。
* ローカルに保存した RDP ファイルを使用している場合は、ポータルで生成されたファイルを試してください。 この手順により、仮想マシンまたはクラウド サービスの適切な DNS 名と、仮想マシンのエンドポイント ポートが使用されます。 ポータルで生成される RDP ファイルの例を次に示します。
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

この RDP ファイルのアドレス部分には、次のデータが含まれます。

* VM を含むクラウド サービスの完全修飾ドメイン名 (この例では "tailspin-azdatatier.cloudapp.net")。
* リモート デスクトップ トラフィックの外部 TCP ポート (55919)。

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>認証エラーが発生しました。 ローカル セキュリティ機関にアクセスできません。
原因: ターゲット VM が、資格情報のユーザー名の部分にセキュリティ機関を見つけることができません。

ユーザー名の形式が *SecurityAuthority*\\*UserName* (例: CORP\User1) であるとき、*SecurityAuthority* の部分は VM のコンピューター名 (ローカル セキュリティ機関の場合) または Active Directory ドメイン名のどちらかです。

考えられる解決策:

* アカウントが VM にとってローカルの場合は、VM 名のスペルが正しいことを確認します。
* アカウントが Active Directory ドメイン アカウントの場合は、ドメイン名のスペルを確認します。
* ユーザー アカウントが Active Directory ドメイン アカウントであり、ドメイン名のスペルが正しい場合は、ドメイン コント ローラーがそのドメインで利用可能であることを確認します。 ドメイン コントローラーがある Azure 仮想ネットワークで、起動していないためにドメイン コントローラーを利用できないというのは、よく起こる問題です。 回避策として、ドメイン アカウントではなく、ローカル管理者アカウントを使用できます。

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows セキュリティ エラー: 資格情報が正しくありません。
原因: ターゲット VM が、アカウント名とパスワードを検証できません。

Windows ベースのコンピューターでは、ローカル アカウントとドメイン アカウントの資格情報を認証できます。

* ローカル アカウントの場合は、*ComputerName*\\*UserName* の構文 (例: SQL1\Admin4798) を使用します。
* ドメイン アカウントの場合は、*DomainName*\\*UserName* の構文 (例: CONTOSO\peterodman) を使用します。

新しい Active Directory フォレスト内で VM がドメイン コントローラーに昇格している場合、サインイン時に使用したローカル管理者アカウントが、新しいフォレストとドメイン内で同じパスワードを持つ同等のアカウントに変換されます。 その後、ローカル アカウントは削除されます。

たとえば、ローカルアカウント DC1\DCAdmin でサインインし、新しいフォレストで仮想マシンを corp.contoso.com ドメインのドメイン コントローラーとして昇格すると、DC1\DCAdmin のローカル アカウントは削除され、新しいドメイン アカウント (CORP\DCAdmin) が同じパスワードで作成されます。

アカウント名が仮想マシンで有効なアカウントとして認証される名前であること、パスワードが正しいことを確認します。

ローカル管理者アカウントのパスワードを変更する必要がある場合は、「 [Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](reset-rdp.md)」をご覧ください。

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>このコンピューターはリモート コンピューターに接続できません。
原因: 接続するために使用されているアカウントにリモート デスクトップ サインイン権限がありません。

すべての Windows コンピューターには、リモート デスクトップ ユーザーのローカル グループがあり、このグループには、リモートでサインインできるアカウントとグループが含まれます。 ローカルの Administrators グループのメンバーもアクセスできますが、これらのアカウントは、リモート デスクトップユーザーのローカル グループのメンバーとしてリストされません。 ドメインに参加しているマシンの場合、ローカルの Administrators グループにはドメインのドメイン管理者も含まれます。

接続するために使用しているアカウントに、リモート デスクトップ サインイン権限があることを確認してください。 回避策として、ドメインまたはローカル管理者アカウントを使用して、リモート デスクトップで接続します。 目的のアカウントをリモート デスクトップ ユーザーのローカル グループに追加するには、Microsoft 管理コンソール スナップイン ( **[システム ツール]、[ローカル ユーザーとグループ]、[グループ]、[Remote Desktop Users]** の順に選択します) を使用します。

## <a name="next-steps"></a>次の手順
上記のエラーのいずれも発生せず、RDP を使用した接続で不明な問題が発生している場合は、[リモート デスクトップのトラブルシューティング ガイド](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照してください。

* VM で実行されているアプリケーションへのアクセスにおけるトラブルシューティング手順については、[Azure VM で実行されているアプリケーションへのアクセスのトラブルシューティング](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。
* Secure Shell (SSH) を使用した Azure の Linux VM への接続で問題が発生している場合は、[Azure の Linux VM への SSH 接続のトラブルシューティング](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

