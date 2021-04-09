---
title: リモート デスクトップ クライアントの Windows Virtual Desktop のトラブルシューティング - Azure
description: Windows Virtual Desktop テナント環境でクライアント接続を設定するときの問題を解決する方法です。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 097c97d16cf62793d03ac42662267e0553383bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539619"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>リモート デスクトップ クライアントのトラブルシューティング

この記事では、リモート デスクトップ クライアントに関する一般的な問題とその修正方法について説明します。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 または Windows 10 用のリモート デスクトップ クライアントが応答を停止する、または開くことができない

バージョン 1.2.790 以降では、[バージョン情報] ページから、またはコマンドを使用してユーザー データをリセットすることができます。

ユーザー データを削除し、既定の設定を復元して、すべてのワークスペースの登録を解除するには、次のコマンドを使用します。

```cmd
msrdcw.exe /reset [/f]
```

以前のバージョンのリモート デスクトップ クライアントを使用している場合は、クライアントをアンインストールしてから再インストールすることをお勧めします。

## <a name="web-client-wont-open"></a>Web クライアントが開かない

まず、ブラウザーで別の Web サイト ([www.bing.com](https://www.bing.com) など) を開き、インターネット接続をテストします。

**nslookup** を使って、DNS で FQDN を解決できることを確認します。

```cmd
nslookup rdweb.wvd.microsoft.com
```

別のクライアント (Windows 7 または Windows 10 用のリモート デスクトップ クライアントなど) で接続してみて、Web クライアントを開くことができるかどうかを確認します。

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>Web クライアントに接続中に他の Web サイトを開けない

Web クライアントに接続中に他の Web サイトを開けない場合は、ネットワーク接続に問題があるか、ネットワークが停止している可能性があります。 ネットワーク サポートに問い合わせることをお勧めします。

### <a name="nslookup-cant-resolve-the-name"></a>Nslookup で名前を解決できない

Nslookup で名前を解決できない場合は、ネットワーク接続に問題があるか、ネットワークが停止している可能性があります。 ネットワーク サポートに問い合わせることをお勧めします。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>自分のクライアントは接続できないが、ネットワーク上の他のクライアントは接続できる

Web クライアントを使用しているときにブラウザーの動作が開始されたり動作が停止したりした場合は、次の手順に従ってトラブルシューティングを行います。

1. ブラウザーを再起動します。
2. ブラウザーの Cookie をクリアします。 「[Internet Explorer で cookie ファイルを削除する方法](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)」をご覧ください。
3. ブラウザーのキャッシュをクリアします。 [お使いのブラウザーのブラウザー キャッシュをクリアする方法](https://binged.it/2RKyfdU)をご覧ください。
4. プライベート モードでブラウザーを開きます。

## <a name="client-doesnt-show-my-resources"></a>クライアントにリソースが表示されない

最初に、使用している Azure Active Directory アカウントを確認します。 Windows Virtual Desktop に使用するアカウントとは別の Azure Active Directory アカウントで既にサインインしている場合は、サインアウトするか、プライベート ブラウザー ウィンドウを使用する必要があります。

Windows Virtual Desktop (クラシック) を使用している場合は、[この記事](./virtual-desktop-fall-2019/connect-web-2019.md)の Web クライアント リンクを使用して、リソースに接続します。

それでもうまくいかない場合は、アプリ グループがワークスペースに関連付けられていることを確認してください。

## <a name="web-client-stops-responding-or-disconnects"></a>Web クライアントが応答を停止するか切断する

別のブラウザーまたはクライアントを使って接続してみてください。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>他のブラウザーやクライアントも正常に機能しないか、開けない

ブラウザーを切り替えた後も問題が解決しない場合は、ブラウザーではなく、ネットワークで問題が発生している可能性があります。 ネットワーク サポートに問い合わせることをお勧めします。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web クライアントが資格情報の入力を求め続ける

Web クライアントで資格情報の入力の要求が出続ける場合は、次の手順に従います。

1. Web クライアントの URL が正しいことを確認します。
2. 使用している資格情報が、URL に関連付けられた Windows Virtual Desktop 環境に対するものであることを確認します。
3. ブラウザーの Cookie をクリアします。 詳細については、「[Internet Explorer で cookie ファイルを削除する方法](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)」を参照してください。
4. ブラウザーのキャッシュをクリアします。 詳細については、[お使いのブラウザーのブラウザー キャッシュをクリアする方法](https://binged.it/2RKyfdU)を参照してください。
5. プライベート モードでブラウザーを開きます。

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>Windows クライアントによって Windows Virtual Desktop (クラシック) フィードがブロックされる

Windows クライアント フィードに Windows Virtual Desktop (クラシック) アプリが表示されない場合は、次の手順に従います。

1. Windows Virtual Desktop (クラシック) に関連付けられているアプリ ID が、条件付きアクセス ポリシーに含まれているかどうかを確認します。
2. 条件付きアクセス ポリシーにより、Windows Virtual Desktop (クラシック) のアプリ ID 以外のすべてのアクセスがブロックされているかどうかを確認します。 その場合、クライアントがフィードを検出できるようにするために、アプリ ID **9cdead84-a844-4324-93f2-b2e6bb768d07** をポリシーに追加する必要があります。

一覧でアプリ ID 9cdead84-a844-4324-93f2-b2e6bb768d07 が見つからない場合は、Windows Virtual Desktop リソース プロバイダーを登録する必要があります。 リソース プロバイダーを登録するには:

1. Azure portal にサインインします。
2. **[サブスクリプション]** に移動し、ご使用のサブスクリプションを選択します。
3. ページの左側にあるメニューで、 **[リソース プロバイダー]** を選択します。
4. **[Microsoft.DesktopVirtualization]** を見つけて選択し、 **[再登録]** を選択します。

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境を作成しているときや、Windows Virtual Desktop 環境でホスト プールを作成しているときに発生した問題を解決するには、[環境とホスト プールの作成](troubleshoot-set-up-issues.md)に関するページを参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop エージェントまたはセッション接続に関連する問題のトラブルシューティングについては、「[Windows Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする](troubleshoot-agent.md)」を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
