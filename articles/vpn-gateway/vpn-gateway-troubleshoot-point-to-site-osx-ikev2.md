---
title: Mac OS X クライアントからの Azure ポイント対サイト VPN 接続のトラブルシューティング | Microsoft Docs
description: Mac OS X クライアント P2S VPN 接続のトラブルシューティング手順
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 0c058cb6547d67469d3138dc331b6181c07e6e65
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087526"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Mac OS X VPN クライアントからのポイント対サイト VPN 接続のトラブルシューティング

この記事は、ネイティブ VPN クライアントと IKEv2 を使用した、Mac OS X からのポイント対サイト接続の問題のトラブルシューティングに役立ちます。 Mac の VPN クライアントで使用される IKEv2 は、非常に簡易なものであり、許容されるカスタマイズはあまりありません。 確認する必要がある設定は次の 4 つのみです。

* サーバー アドレス
* リモート ID
* ローカル ID
* 認証設定
* OS バージョン (10.11 以降)


## <a name="VPNClient"></a>証明書ベースの認証のトラブルシューティング
1. VPN クライアントの設定を確認します。 Command キーと Shift キーを押して **[Network Setting]\(ネットワーク設定\)** に移動し、「VPN」と入力してクライアントの設定を確認します。 一覧から、調べる必要がある VPN エントリをクリックします。

   ![IKEv2 証明書ベースの認証](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. **サーバー アドレス**が完全な FQDN であり、cloudapp.net が含まれていることを確認します。
3. **リモート ID** は、サーバー アドレス (ゲートウェイの FQDN) と同じである必要があります。
4. **ローカル ID** が、クライアント証明書の**サブジェクト**と同じである必要があります。
5. **[認証設定]** をクリックして [認証設定] ページを開きます。

   ![[認証設定]](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. ドロップダウンで **[証明書]** が選択されていることを確認します。
7. **[選択]** をクリックし、適切な証明書が選択されていることを確認します。 **[OK]** をクリックして、すべての変更を保存します。

## <a name="ikev2"></a>ユーザー名とパスワードの認証のトラブルシューティング

1. VPN クライアントの設定を確認します。 Command キーと Shift キーを押して **[Network Setting]\(ネットワーク設定\)** に移動し、「VPN」と入力してクライアントの設定を確認します。 一覧から、調べる必要がある VPN エントリをクリックします。

   ![IKEv2 ユーザー名とパスワード](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. **サーバー アドレス**が完全な FQDN であり、cloudapp.net が含まれていることを確認します。
3. **リモート ID** は、サーバー アドレス (ゲートウェイの FQDN) と同じである必要があります。
4. **ローカル ID** は、空白にすることができます。
5. **[認証設定]** をクリックし、ドロップダウンで [ユーザー名] が選択されていることを確認します。

   ![[認証設定]](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. 適切な資格情報が入力されていることを確認します。

## <a name="additional"></a>追加の手順

前の手順を試してみて、すべてが正しく構成されている場合、[Wireshark](https://www.wireshark.org/#download) をダウンロードしてパケット キャプチャを実行します。

1. *isakmp* をフィルター処理し、**IKE_SA** パケットを確認します。 **[Payload:Security Association]\(ペイロード: セキュリティ アソシエーション\)** の下で SA 提案の詳細を確認することができます。 
2. クライアントとサーバーに共通のセットがあることを確認します。

   ![パケット](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. ネットワーク トレースにサーバーの応答がない場合は、Azure Portal Web サイトの Azure ゲートウェイ構成ページで IKEv2 プロトコルを有効にしたことを確認します。

## <a name="next-steps"></a>次の手順
サポートが必要な場合、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を参照してください。
