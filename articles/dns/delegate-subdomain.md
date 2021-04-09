---
title: サブドメインを委任する - Azure DNS
description: このラーニング パスでは、Azure DNS サブドメインの委任について説明します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011542"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Azure DNS サブドメインを委任する

Azure portal を使用して DNS サブドメインを委任することができます。 たとえば、contoso.com ドメインを所有している場合は、*engineering* というサブドメインを contoso.com ゾーンとは別に管理できる別のゾーンに委任できます。

必要に応じて、[Azure PowerShell](delegate-subdomain-ps.md) を使用して、サブドメインを委任できます。

## <a name="prerequisites"></a>前提条件

Azure DNS サブドメインを委任するには、まずパブリック ドメインを Azure DNS に委任する必要があります。 ネーム サーバーを委任のために構成する方法については、[Azure DNS へのドメインの委任](./dns-delegate-domain-azure-dns.md)に関するページを参照してください。 ドメインが Azure DNS ゾーンに委任された後は、サブドメインを委任できるようになります。

> [!NOTE]
> この記事では、contoso.com を例として使用します。 contoso.com を独自のドメイン名に置き換えてください。

## <a name="create-a-zone-for-your-subdomain"></a>サブドメインのゾーンを作成する

まず **engineering** サブドメインのゾーンを作成します。

1. Azure portal から **[リソースの作成]** を選択します。
2. 検索ボックスに、「**DNS**」と入力し、**[DNS ゾーン]** を選択します。
3. **［作成］** を選択します
4. **[DNS ゾーンの作成]** ウィンドウで、**[名前]** ボックスに「**engineering.contoso.com**」と入力します。
5. ゾーンのリソース グループを選択します。 親ゾーンと同じリソース グループを使用して、似たリソースをまとめておくことができます。
6. **Create** をクリックしてください。
7. デプロイが成功したら、新しいゾーンに移動します。

## <a name="note-the-name-servers"></a>ネーム サーバーをメモする

次に、engineering サブドメインの 4 つのネーム サーバーをメモします。

**engineering** ゾーン ウィンドウで、ゾーンの 4 つのネーム サーバーをメモします。 これらのネーム サーバーは後で使用します。

## <a name="create-a-test-record"></a>テスト レコードを作成する

テストに使用する **A** レコードを作成します。 たとえば、**www** A レコードを作成し、**10.10.10.10** IP アドレスを使用してそれを構成します。

## <a name="create-an-ns-record"></a>NS レコードの作成

次に、**engineering** ゾーンのネーム サーバー (NS) レコードを作成します。

1. 親ドメインのゾーンに移動します。
2. **[+ レコード セット]** を選択します。
3. **[レコード セットの追加]** ウィンドウで、**[名前]** ボックスに「**engineering**」と入力します。
4. **[種類]** には **[NS]** を選択します。
5. **[ネーム サーバー]** で、以前にメモした **engineering** ゾーンの 4 つのネーム サーバーを入力します。
6. **[OK]** をクリックします。

## <a name="test-the-delegation"></a>委任をテストする

nslookup を使用して委任をテストします。

1. PowerShell ウィンドウを開きます。
2. コマンド プロンプトに「`nslookup www.engineering.contoso.com.`」と入力します。
3. アドレス **10.10.10.10** を示す権限のない回答を受け取ります。

## <a name="next-steps"></a>次のステップ

[Azure でホストされているサービスの逆引き DNS を構成する](dns-reverse-dns-for-azure-services.md)方法について説明します。