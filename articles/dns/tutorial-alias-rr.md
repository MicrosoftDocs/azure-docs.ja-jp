---
title: チュートリアル:ゾーン内のリソース レコードを参照するエイリアス レコードを作成する
titleSuffix: Azure DNS
description: このチュートリアルでは、ゾーン内のリソース レコードを参照する Azure DNS エイリアス レコードを構成する方法を示します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: 2b122a34cfd382a58f7680743d3a1cb1ae598fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76939246"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>チュートリアル:ゾーン内のリソース レコードを参照するエイリアス レコードを作成する

エイリアス レコードでは、同じ種類の別のレコード セットを参照できます。 たとえば、DNS の CNAME レコード セットを同じ種類の別の CNAME レコード セットのエイリアスにできます。 この機能は、動作という意味で、一部のレコード セットをエイリアスにして、一部はエイリアスにしない場合に便利です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ゾーン内のリソース レコードのエイリアス レコードを作成する。
> * エイリアス レコードをテストする。


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
テスト対象の Azure DNS でホストできる利用可能なドメイン名が必要です。 このドメインに対するフル コントロールが必要となります。 フル コントロールには、このドメインのネーム サーバー (NS) レコードを設定する権限が含まれます。

Azure DNS 内でドメインをホストする手順については、「[チュートリアル:Azure DNS でドメインをホストする](dns-delegate-domain-azure-dns.md)」を参照してください。


## <a name="create-an-alias-record"></a>エイリアス レコードを作成する

ゾーン内のリソース レコードを参照するエイリアス レコードを作成します。

### <a name="create-the-target-resource-record"></a>ターゲット リソース レコードを作成する
1. Azure DNS ゾーンを選択し、ゾーンを開きます。
2. **[レコード セット]** を選択します。
3. **[名前]** テキスト ボックスに「**server**」と入力します。
4. **[種類]** で **[A]** を選択します。
5. **[IP アドレス]** テキスト ボックスに「**10.10.10.10**」と入力します。
6. **[OK]** を選択します。

### <a name="create-the-alias-record"></a>エイリアス レコードを作成する
1. Azure DNS ゾーンを選択し、ゾーンを開きます。
2. **[レコード セット]** を選択します。
3. **[名前]** テキスト ボックスに「**test**」と入力します。
4. **[種類]** で **[A]** を選択します。
5. **[Alias Record Set]\(エイリアス レコード セット\)** チェック ボックスで **[はい]** を選択します。 続けて、 **[Zone record set]\(ゾーン レコード セット\)** オプションを選択します。
6. **[Zone record set]/(ゾーン レコード セット/)** で、 **[server]** レコードを選択します。
7. **[OK]** を選択します。

## <a name="test-the-alias-record"></a>エイリアス レコードをテストする

1. お好きな nslookup ツールを起動します。 1 つのオプションとして、[https://network-tools.com/nslook](https://network-tools.com/nslook) を参照します。
2. クエリの種類を A レコードに設定し、**test.\<your domain name\>** を検索します。 応答は **10.10.10.10** になります。
3. Azure portal で、**server** A レコードを **10.11.11.11** に変更します。
4. 数分待ってから、もう一度 nslookup を使用して **test** レコードを検索します。 応答は **10.11.11.11** になります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアル用に作成したリソースが不要になったら、**server** と **test** のリソース レコードをゾーンから削除してください。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ゾーン内のリソース レコードを参照するエイリアス レコードを作成しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
