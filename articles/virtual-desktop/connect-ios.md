---
title: iOS から Windows Virtual Desktop に接続する - Azure
description: iOS クライアントを使用して Windows Virtual Desktop に接続する方法。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: 764ed4fefd1a3aba1f0b7812fa2965505aa34161
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338685"
---
# <a name="connect-with-the-ios-client"></a>iOS クライアントに接続する

> 適用対象: iOS 8.0 以降。 iPhone、iPad、iPod touch と互換性があります。

>[!NOTE]
> iOS クライアントは現在まだプレビュー段階です。

ダウンロード可能なクライアントを使用して、ご使用の iOS デバイスから Windows Virtual Desktop リソースにアクセスできます。 このガイドでは、iOS クライアントを設定する方法について説明します。

## <a name="install-the-ios-beta-client"></a>iOS Beta クライアントをインストールする
iOS Beta クライアントをインストールするには:

1. ご使用の iOS デバイスに [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) アプリをインストールします。
2. iOS デバイスでブラウザーを開き、[aka.ms/rdiosbeta](https://aka.ms/rdiosbeta) に移動します。
3. ラベル **[Step 2:Join the Beta]** \(手順 2: ベータ版に参加\) の下で、 **[テストを開始]** を選択します。
4. TestFlight アプリにリダイレクトされたら、 **[同意する]** を選択してから **[インストール]** を選択します。

## <a name="subscribe-to-a-feed"></a>フィードのサブスクライブ

管理者から提供されたフィードをサブスクライブして、ご使用の iOS デバイスでアクセスできる管理対象リソースの一覧を取得します。

フィードをサブスクライブするには:

1. 接続センターで、 **+** をタップし、次に **[Add Workspace]\(ワークスペースを追加\)** をタップします。
2. **[Feed URL]\(フィード URL\)** フィールドにフィード URL を入力します。 フィード URL には、URL またはメール アドレスのいずれかを指定できます。
   - URL を使用する場合は、管理者によって付与されたものを使用します。 通常、URL は <https://rdweb.wvd.microsoft.com> です。
   - 電子メールを使用するには、メール アドレスを入力します。 これにより、メール アドレスに関連付けられている URL を検索するようにクライアントに指示されます (管理者がそのようにサーバーを構成した場合)。
3. **[Next]\(次へ\)** をタップします。
4. メッセージが表示されたら、資格情報を入力します。
   - **[User name]\(ユーザー名\)** 。リソースへのアクセス許可を持つユーザー名を指定します。
   - **[Password]\(パスワード\)** 。ユーザー名に関連付けられているパスワードを指定します。
   - 管理者が追加の要素を指定するように認証を構成した場合、そのように求められることもあります。
5. **[保存]** をタップします。

その後、接続センターにリモート リソースが表示されます。

フィードをサブスクライブすると、フィードのコンテンツが自動で定期的に更新されます。 管理者によって行われる変更に基づいて、リソースが追加、変更、または削除されることがあります。

## <a name="next-steps"></a>次の手順

iOS Beta クライアントの使用方法の詳細については、「[iOS クライアントの概要](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios)」のドキュメントで確認してください。
