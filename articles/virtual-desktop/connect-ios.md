---
title: iOS から Windows Virtual Desktop に接続する - Azure
description: iOS クライアントを使用して Windows Virtual Desktop に接続する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 87bb2cc53ce056552e8f44aac4ade96e603a8787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230043"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>iOS クライアントを使用して Windows Virtual Desktop に接続する

> 適用対象: iOS 13.0 以降。 iPhone、iPad、iPod touch と互換性があります。

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/connect-ios-2019.md)を参照してください。

ダウンロード可能なクライアントを使用して、ご使用の iOS デバイスから Windows Virtual Desktop リソースにアクセスできます。 このガイドでは、iOS クライアントを設定する方法について説明します。

## <a name="install-the-ios-client"></a>iOS クライアントをインストールする

開始するには、クライアントを[ダウンロード](https://aka.ms/rdios)して、ご利用の iOS デバイスにインストールします。

## <a name="subscribe-to-a-feed"></a>フィードのサブスクライブ

管理者から提供されたフィードをサブスクライブして、ご使用の iOS デバイスでアクセスできる管理対象リソースの一覧を取得します。

フィードをサブスクライブするには:

1. 接続センターで、 **+** をタップし、次に **[Add Workspace]\(ワークスペースを追加\)** をタップします。
2. **[Feed URL]\(フィード URL\)** フィールドにフィード URL を入力します。 フィード URL には、URL またはメール アドレスのいずれかを指定できます。
   - URL を使用する場合は、管理者によって付与されたものを使用します。 通常、URL は <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> です。
   - 電子メールを使用するには、電子メール アドレスを入力します。 これにより、メール アドレスに関連付けられている URL を検索するようにクライアントに指示されます (管理者がそのようにサーバーを構成した場合)。
   - US Gov ポータルから接続するには、<https://rdweb.wvd.azure.us/api/arm/feeddiscovery> を使用します。
3. **[Next]** \(次へ\) をタップします。
4. メッセージが表示されたら、資格情報を入力します。
   - **[User name]\(ユーザー名\)** 。リソースへのアクセス許可を持つユーザー名を指定します。
   - **[パスワード]** には、ユーザー名に関連付けられているパスワードを指定します。
   - 管理者が追加の要素を指定するように認証を構成した場合、そのように求められることもあります。
5. **[保存]** をタップします。

その後、接続センターにリモート リソースが表示されます。

フィードをサブスクライブすると、フィードのコンテンツが定期的に自動的に更新されます。 管理者によって行われる変更に基づいて、リソースが追加、変更、または削除されることがあります。

## <a name="next-steps"></a>次のステップ

iOS クライアントの使用方法の詳細については、「[iOS クライアントの概要](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)」のドキュメントで確認してください。
