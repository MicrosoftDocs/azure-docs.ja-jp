---
title: Windows Virtual Desktop の MSIX アプリ アタッチに関するよくあるご質問 - Azure
description: Windows Virtual Desktop の MSIX アプリ アタッチに関するよくあるご質問。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 395c274630131c2ae5f451443913e1e69c7c422a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738703"
---
# <a name="msix-app-attach-faq"></a>MSIX アプリ アタッチに関するごよくある質問

この記事では、Windows Virtual Desktop の MSIX アプリ アタッチに関するよくあるご質問に回答します。

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>MSIX アプリと MSIX アプリ アタッチの違いは何ですか。

MSIX はアプリのパッケージ形式であり、MSIX アプリ アタッチは、デプロイに MSIX パッケージを配信する機能です。

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX アプリ アタッチでは FSLogix は使用されますか?

MSIX アプリ アタッチでは、FSLogix は使用されません。 ただし、MSIX アプリ アタッチと FSLogix は、ユーザーがシームレスなエクスペリエンスを得られるよう連携して動作するように設計されています。

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>MSIX アプリ アタッチは Windows Virtual Desktop 外で使用できますか?

MSIX アプリ アタッチを強化する API が Windows 10 Enterprise 向けに用意されています。 これらの API は、Windows Virtual Desktop 外で使用できます。 ただし、Windows Virtual Desktop 外には MSIX アプリ アタッチの管理プレーンはありません。

## <a name="how-do-i-get-an-msix-package"></a>MSIX パッケージはどのように入手できますか?

MSIX パッケージは、ご自分のソフトウェア ベンダーから提供されます。 非 MSIX パッケージを MSIX に変換することもできます。 詳細については、「[既存のお使いのインストーラーを MSIX に移行する方法](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)」を参照してください。

## <a name="which-operating-systems-support-msix-app-attach"></a>MSIX アプリ アタッチはどのオペレーティング システムでサポートされていますか?

Windows 10 Enterprise および Windows 10 Enterprise マルチセッション、バージョン 2004 以降でサポートされています。

## <a name="is-msix-app-attach-currently-generally-available"></a>現在、MSIX アプリ アタッチは一般公開されていますか。

MSIX アプリ アタッチは、Windows 10 Enterprise および Windows 10 Enterprise マルチセッション、バージョン 2004 以降に含まれています。 現在、両方のオペレーティング システムが一般公開されています。 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>MSIX アプリ アタッチは Windows Virtual Desktop 外で使用できますか?

MSIX と MSIX アプリ アタッチ API は、Windows 10 Enterprise および Windows 10 Enterprise マルチセッション、バージョン 2004 以降に含まれています。 現在、Windows Virtual Desktop 外で MSIX アプリ アタッチの管理ソフトウェアは提供されていません。

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>同じアプリケーションの 2 つのバージョンを同時に実行できますか。

同じ MSIX アプリケーションの 2 つのバージョンを同時に実行するには、appxmanifest.xml ファイルで定義されている MSIX パッケージ ファミリが、アプリごとに異なっている必要があります。

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>MSIX アプリ アタッチを使用する場合は、自動更新を無効にする必要がありますか。

はい。 MSIX アプリ アタッチでは、MSIX アプリケーションの自動更新はサポートされていません。

## <a name="how-do-permissions-work-with-msix-app-attach"></a>MSIX アプリ アタッチでアクセス許可はどのように機能しますか。

MSIX アプリ アタッチを使用するホスト プール内のすべての仮想マシン (VM) に、MSIX イメージが格納されているファイル共有に対する読み取りアクセス許可が必要です。 Azure Files も使用している場合は、ロールベースのアクセス制御 (RBAC) と New Technology File System (NTFS) の両方のアクセス許可が付与されている必要があります。

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>HTTP または HTTPs で MSIX アプリ アタッチを使用できますか。

HTTP または HTTPs で MSIX アプリ アタッチを使用することは、現在サポートされていません。

## <a name="can-i-restage-the-same-msix-application"></a>同じ MSIX アプリケーションを再ステージングすることはできますか。

はい。 既に再ステージングされているアプリケーションを再ステージングできます。これによってエラーが発生することはありません。

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX アプリ アタッチで自己署名証明書はサポートされていますか。

はい。 MSIX アプリのアタッチを使用して自己署名アプリケーションをホストするすべてのセッション ホスト VM に、自己署名証明書をインストールする必要があります。


## <a name="next-steps"></a>次のステップ

MSIX アプリ アタッチの詳細については、[概要](what-is-app-attach.md)と[用語集](app-attach-glossary.md)に関するページを参照してください。 それ以外の場合、[アプリのアタッチの設定](app-attach.md)を開始してください。
