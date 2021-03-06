---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93061631"
---
クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。

1. *.pfx* ファイルを見つけ、クライアント コンピューターにコピーします。 クライアント コンピューターで、 *.pfx* ファイルをダブルクリックしてインストールします。 **[ストアの場所]** は **[現在のユーザー]** のままにしておき、 **[次へ]** を選択します。
1. **[インポートするファイル]** ページでは、何も変更しないでください。 **[次へ]** を選択します。
1. **[秘密キーの保護]** ページで、証明書のパスワードを入力するか、セキュリティ プリンシパルが正しいことを確認し、 **[次へ]** を選択します。
1. **[証明書ストア]** ページで、既定の場所をそのまま使用し、 **[次へ]** を選択します。
1. **[完了]** を選択します。 証明書のインストールの **[セキュリティ警告]** で **[はい]** を選択します。 証明書を生成したため、このセキュリティ警告では安心して [はい] を選択できます。
1. これで証明書がインポートされます。
