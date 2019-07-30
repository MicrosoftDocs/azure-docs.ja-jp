---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608251"
---
### <a name="preview"></a>パブリック プレビューに参加するにはどうすればよいですか?

パブリック プレビューに参加するには、オンボードする必要があります。 [この記事](../articles/bastion/bastion-create-host-portal.md)内の手順を使用して、新しい Azure Bastion リソースを作成します。 現時点で、このサービスにアクセスして使用するには、通常の Azure portal ではなく、[Azure portal - プレビュー](https://aka.ms/BastionHost)を使用する必要があります。

### <a name="regions"></a>プレビュー期間に利用できるのはどのリージョンですか?

Bastion リソースは、[Azure portal - プレビュー リンク](https://aka.ms/BastionHost)経由でこれらのプレビュー リージョンのいずれかにデプロイして使用することができます。

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Azure portal 内で Bastion リソースが見つかりません。 どうすればよいですか。

通常の Azure portal ではなく、[Azure portal - プレビュー リンク](https://aka.ms/BastionHost)を使用していることを確認してください。

### <a name="publicip"></a>自分の仮想マシンにはパブリック IP が必要ですか?

Azure Bastion サービスを使用して接続する Azure 仮想マシンにはパブリック IP は必要ありません。 Bastion サービスは、ご使用の仮想ネットワーク内で、お客様の仮想マシンのプライベート IP 経由でお客様の仮想マシンへの RDP または SSH セッションや接続を開きます。

### <a name="rdpssh"></a>RDP または SSH クライアントは必要ですか?

ご使用の Azure portal 内でお客様の Azure 仮想マシンに RDP または SSH アクセスするために RDP または SSH クライアントは必要ありません。 ポータルのプレビュー フライトにアクセスするには、[Azure portal - プレビュー リンク](https://aka.ms/BastionHost)を使用します。 これにより、ブラウザー内で直接お客様の仮想マシンに RDP または SSH アクセスすることができます。

### <a name="agent"></a>Azure 仮想マシン内で実行するエージェントは必要ですか?

ご使用のブラウザーやお客様の Azure 仮想マシンにエージェントやソフトウェアをインストールする必要はありません。 Bastion サービスはエージェントレスのため、RDP や SSH 用の追加のソフトウェアを必要としません。

### <a name="browsers"></a>どのブラウザーがサポートされていますか?

パブリック プレビュー期間は、Windows 上で Microsoft Edge ブラウザーまたは Google Chrome をご使用ください。 Apple Mac では、Google Chrome ブラウザーをご使用ください。 Microsoft Edge Chromium も Windows と Mac の両方でサポートされます。

### <a name="roles"></a>仮想マシンにアクセスするには、なんらかのロールが必要ですか?

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="previewbill"></a>価格 - このプレビューへの参加に対しては課金されますか?

パブリック プレビュー期間中は、一部のみ課金されます。 しかし、お客様のデプロイに適用される SLA はありません。 詳細については、 [価格に関するページ](https://aka.ms/BastionHostPricing)を参照してください。

### <a name="previewbill"></a>Bastion セッションの開始前に "セッションの有効期限が切れています" というエラー メッセージを受け取るのはなぜですか?

セッションは Azure portal からのみ開始される必要があります。 もう一度 Azure portal にサインインして、自分のセッションを開始してください。 別のブラウザー セッションまたはタブから直接 URL に移動する場合に、このエラーが発生します。 これは、自分のセッションがより安全で、セッションが Azure portal からのみアクセスできることを確保するのに役立ちます。
