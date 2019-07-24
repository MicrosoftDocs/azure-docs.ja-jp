---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323727"
---
接続に問題がある場合は、次の点を確認してください。

- **証明書のエクスポート ウィザード**を使用してクライアント証明書をエクスポートした場合は、**[証明のパスにある証明書を可能であればすべて含む]** を選択してクライアント証明書を .pfx ファイルとしてエクスポートしたことを確認してください。 この値を使用してクライアント証明書をエクスポートすると、ルート証明書情報もエクスポートされます。 クライアント コンピューターに証明書をインストールした後、.pfx ファイルのルート証明書もインストールされます。 ルート証明書がインストールされていることを確認するには、**[ユーザー証明書の管理]** を開いて **[Trusted Root Certification Authorities\Certificates]** を選択します。 ルート証明書が一覧にあることを確認します。認証が正しく機能するためには、ルート証明書が必要です。

- エンタープライズ CA ソリューションによって発行された証明書を使用し、認証できない場合は、クライアント証明書の認証の順序を確認します。 認証の一覧の順序を確認するには、クライアント証明書をダブルクリックし、**[詳細]** タブ、**[拡張キー使用法]** の順に選択します。 一覧の最初の項目が "*クライアント認証*" であることを確認します。 そうでない場合は、一覧の最初の項目が "*クライアント認証*" であるユーザー テンプレートに基づいたクライアント証明書を発行します。

- P2S のトラブルシューティングの補足情報については、[P2S 接続のトラブルシューティング](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。