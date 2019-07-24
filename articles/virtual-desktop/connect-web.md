---
title: Windows Virtual Desktop プレビュー Web クライアントに接続する - Azure
description: Windows Virtual Desktop プレビュー HTML5 Web クライアントに接続する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 231dc2b777ce7fa37f445215863a55e93562e411
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403750"
---
# <a name="connect-with-the-web-client"></a>Web クライアントに接続する

Web クライアントでは、時間のかかるインストール プロセスなしで Web ブラウザーから Windows Virtual Desktop プレビュー リソースにアクセスすることができます。

>[!NOTE]
>現在、Web クライアントにはモバイル OS のサポートがありません。

## <a name="supported-operating-systems-and-browsers"></a>サポートされているオペレーティング システムとブラウザー

任意の HTML5 対応ブラウザーが動作しますが、正式にサポートしているオペレーティング システムとブラウザーは次のとおりです。

| ブラウザー           | サポート対象 OS                     | メモ               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | バージョン 55 以降 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>リモート リソース フィードにアクセスする

ブラウザーで、[Windows Virtual Desktop Web クライアント](https://rdweb.wvd.microsoft.com/webclient)に移動し、ユーザー アカウントを使用してサインインします。

>[!NOTE]
>Windows Virtual Desktop に使用するものとは異なる Azure AD アカウントを使用して既にサインインしている場合は、サインアウトするか、プライベート ブラウザー ウィンドウを使用する必要があります。

サインインすると、リソースの一覧が表示されます。 リソースを起動するには、**[すべてのリソース]** タブで通常のアプリと同様にそのリソースを選択します。
