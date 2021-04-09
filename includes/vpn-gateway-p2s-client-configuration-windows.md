---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93042608"
---
バージョンがクライアントのアーキテクチャと一致する限り、各 Windows クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、「[VPN Gateway に関する FAQ](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)」のポイント対サイトに関するセクションを参照してください。

>[!NOTE]
>接続元の Windows クライアント コンピューターの管理者権限が必要です。
>

証明書認証用にネイティブ Windows VPN クライアントを構成するには、次の手順を実行してください。

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 
1. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、 **[詳細]** 、 **[実行]** の順にクリックしてください。
1. クライアント コンピューターで **[ネットワークの設定]** に移動し、 **[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。
1. 接続を試行する前に、クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。