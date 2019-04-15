---
title: Windows 7 および Windows 10 による Windows Virtual Desktop プレビューへの接続 - Azure
description: Windows 7 または Windows 10 により Windows Virtual Desktop プレビュー サービスに接続する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/27/2019
ms.author: helohr
ms.openlocfilehash: 8b5a649d767dae9ae9c0fb077f550451a7f83601
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630376"
---
# <a name="connect-with-windows-7-and-windows-10"></a>Windows 7 および Windows 10 に接続する

> 適用対象:Windows 7 および Windows 10

Windows 7 と Windows 10 を実行するデバイスから Windows Virtual Desktop プレビューのリソースへのアクセスを提供するダウンロード可能なクライアントを利用できます。

> [!IMPORTANT]
> Windows Virtual Desktop リソースへのアクセスには **RemoteApp とデスクトップ接続 (RADC)** または**リモート デスクトップ接続 (MSTSC)** を使用しないでください。Windows Virtual Desktop ではどちらのクライアントもサポートされていないためです。

## <a name="install-the-client"></a>クライアントをインストールします。

クライアントを[ダウンロード](https://go.microsoft.com/fwlink/?linkid=2068602) して、ローカル PC にインストールします。 これには、管理者権限が必要です。

## <a name="subscribe-to-a-feed"></a>フィードのサブスクライブ

管理者によって提供されるフィードをサブスクライブして、ローカル コンピューターから使用可能なリソースの一覧を取得します。

フィードをサブスクライブするには:

1. すべてのアプリ リストからクライアントを起動し、**[リモート デスクトップ]** を探します。
1. サービスに接続して自分のリソースを取得するために、メイン ページで **[サブスクライブ]** を選択します。
1. メッセージが表示されたら、自分のユーザー アカウントで**サインイン**します。

正常に認証すると、利用できるリソースの一覧が表示されるようになります。

リソースは、次の 2 つの方法のいずれかで起動できます。

- クライアントのメイン ページで、リソースをダブルクリックして起動します。
- [スタート] メニューから他のアプリを通常の方法で起動するようにリソースを起動します。
  - 検索バーでアプリを検索することもできます。

## <a name="update-the-client"></a>クライアントの更新

新しいバージョンのクライアントが利用できる場合は、クライアントと Windows アクション センターから通知されます。 通知を選択して、更新プロセスを開始します。
