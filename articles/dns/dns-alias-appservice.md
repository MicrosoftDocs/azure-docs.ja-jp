---
title: ゾーンの頂点で負荷分散された Azure Web アプリをホストする
description: Azure DNS エイリアス レコードを使用して、ゾーンの頂点で負荷分散された Web アプリをホストします
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 72adb2732eb0832589cbc25fb7e4288eb1899214
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954513"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>ゾーンの頂点で負荷分散された Azure Web アプリをホストする

DNS プロトコルでは、ゾーンの頂点で A または AAAA レコード以外のものを割り当てることはできません。 ゾーンの頂点の例として、contoso.com があります。 Traffic Manager の背後でアプリケーションの負荷分散を行っているアプリケーションの所有者にとっては、この制限によって問題が生じます。 ゾーンの頂点レコードから Traffic Manager プロファイルをポイントすることはできません。 そのため、アプリケーションの所有者は回避策を使用する必要があります。 アプリケーション レイヤーでのリダイレクトでは、ゾーンの頂点から別のドメインにリダイレクトする必要があります。 たとえば、contoso.com から www\.contoso.com へのリダイレクトです。 このようにすると、リダイレクト機能に単一障害点が発生します。

エイリアス レコードを使用すると、この問題がなくなります。 アプリケーションの所有者は、ゾーンの頂点のレコードで、外部エンドポイントを持つ Traffic Manager プロファイルをポイントできます。 アプリケーションの所有者は、DNS ゾーン内の他のドメインで使用されているのと同じ Traffic Manager プロファイルをポイントできます。

たとえば、contoso.com と www\.contoso.com で、同じ Traffic Manager プロファイルをポイントできます。 これは、Traffic Manager に外部エンドポイントのみが構成されている場合に限ります。

この記事では、ドメインの頂点に対するエイリアス レコードを作成し、Web アプリ用に Traffic Manager プロファイルのエンドポイントを構成する方法について説明します。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

テスト対象の Azure DNS でホストできる利用可能なドメイン名が必要です。 このドメインに対するフル コントロールが必要となります。 フル コントロールには、このドメインのネーム サーバー (NS) レコードを設定する権限が含まれます。

Azure DNS 内でドメインをホストする手順については、「[チュートリアル:Azure DNS でドメインをホストする](dns-delegate-domain-azure-dns.md)」を参照してください。

このチュートリアルで使用するドメインの例は contoso.com ですが、独自のドメイン名を使用してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

この記事で使用するすべてのリソースを保持するリソース グループを作成します。

## <a name="create-app-service-plans"></a>App Service プランを作成する

次の表で示す構成情報を使用して、リソース グループに 2 つの Web App Service プランを作成します。 App Service プランの作成の詳細については、「[Azure で App Service プランを管理する](../app-service/app-service-plan-manage.md)」をご覧ください。


|名前  |オペレーティング システム  |場所  |価格レベル  |
|---------|---------|---------|---------|
|ASP-01     |Windows|米国東部|Dev/Test D1-Shared|
|ASP-02     |Windows|米国中部|Dev/Test D1-Shared|

## <a name="create-app-services"></a>App Services を作成する

App Service プランごとに 1 つずつ、2 つの Web アプリを作成します。

1. Azure portal ページの左上隅にある **[リソースの作成]** を選択します。
2. 検索バーに「**Web app**」と入力し、Enter キーを押します。
3. **Web アプリ** を選択します。
4. **［作成］** を選択します
5. 既定値のままにし、次の表を使用して 2 つの Web アプリを構成します。

   |名前<br>(.azurewebsites.net 内で一意になっている必要があります)|リソース グループ |ランタイム スタック|リージョン|App Service プラン/場所
   |---------|---------|-|-|-------|
   |App-01|既存のものを使用します<br>リソース グループを選択します|.NET Core 2.2|米国東部|ASP-01(D1)|
   |App-02|既存のものを使用します<br>リソース グループを選択します|.NET Core 2.2|米国中部|ASP-02(D1)|

### <a name="gather-some-details"></a>詳細情報を収集する

Web アプリの IP アドレスとホスト名を書き留めておく必要があります。

1. リソース グループを開き、最初の Web アプリを選択します (この例では **App-01**)。
2. 左側の列で、 **[プロパティ]** を選択します。
3. **[URL]** のアドレスと、 **[送信 IP アドレス]** で一覧の最初の IP アドレスを書き留めます。 この情報は、後で Traffic Manager エンドポイントを構成するときに使います。
4. **App-02** についても同じようにします。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

リソース グループで Traffic Manager プロファイルを作成します。 既定値を使用し、trafficmanager.net 名前空間内で一意の名前を入力します。

Traffic Manager プロファイルの作成方法の詳細については、[Web アプリケーションに高可用性を実現する Traffic Manager プロファイルの作成に関するクイック スタート](../traffic-manager/quickstart-create-traffic-manager-profile.md)を参照してください。

### <a name="create-endpoints"></a>エンドポイントを作成する

これで、2 つの Web アプリに対するエンドポイントを作成できます。

1. リソース グループを開き、Traffic Manager プロファイルを選択します。
2. 左側の列で、 **[エンドポイント]** を選択します。
3. **[追加]** を選択します。
4. 次の表を使用して、エンドポイントを構成します。

   |Type  |名前  |移行先  |場所  |カスタム ヘッダーの設定|
   |---------|---------|---------|---------|---------|
   |外部エンドポイント     |End-01|App-01 について記録した IP アドレス|米国東部|ホスト: \<the URL you recorded for App-01\><br>例: **host:app-01.azurewebsites.net**|
   |外部エンドポイント     |End-02|App-02 について記録した IP アドレス|米国中部|ホスト: \<the URL you recorded for App-02\><br>例: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS ゾーンの作成

既存の DNS ゾーンをテスト用に使用することも、新しいゾーンを作成することもできます。 Azure で新しい DNS ゾーンを作成して委任する方法については、「[チュートリアル:Azure DNS でドメインをホストする](dns-delegate-domain-azure-dns.md)」を参照してください。

## <a name="add-a-txt-record-for-custom-domain-validation"></a>カスタム　ドメイン検証用の TXT レコードを追加する

Web アプリにカスタム ホスト名を追加すると、ドメインを検証するための特定の TXT レコードが検索されます。

1. リソース グループを開き、DNS ゾーンを選択します。
2. **[レコード セット]** を選択します。
3. 次の表を使用して、レコード セットを追加します。 値には、前に記録した実際の Web アプリ URL を使用します。

   |名前  |Type  |値|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>カスタム ドメインの追加

両方の Web アプリのカスタム ドメインを追加します。

1. リソース グループを開き、最初の Web アプリを選択します。
2. 左側の列で、 **[カスタム ドメイン]** を選択します。
3. **[カスタム ドメイン]** で、 **[カスタム ドメインの追加]** を選択します。
4. **[カスタム ドメイン]** で、カスタム ドメイン名を入力します。 たとえば、contoso.com です。
5. **[検証]** を選択します。

   ドメインは検証に合格し、 **[ホスト名の利用可否]** と **[ドメイン所有権]** の横に緑のチェック マークが表示される必要があります。
5. **[カスタム ドメインの追加]** を選択します。
6. **[サイトに割り当てられるホスト名]** で新しいホスト名を確認するには、ブラウザーの表示を更新します。 ページを更新しても、すぐに変更が表示されないことがあります。
7. 2 番目の Web アプリでこの手順を繰り返します。

## <a name="add-the-alias-record-set"></a>エイリアス レコード セットを追加する

ここで、ゾーンの頂点に対するエイリアス レコードを追加します。

1. リソース グループを開き、DNS ゾーンを選択します。
2. **[レコード セット]** を選択します。
3. 次の表を使用して、レコード セットを追加します。

   |名前  |Type  |エイリアス レコード セット  |エイリアスの種類  |Azure リソース|
   |---------|---------|---------|---------|-----|
   |@     |A|はい|Azure リソース|Traffic Manager - お使いのプロファイル|


## <a name="test-your-web-apps"></a>Web アプリをテストする

テストを行って、Web アプリに到達できること、および負荷分散されていることを確認できる状態になりました。

1. Web ブラウザーを開き、ドメインを参照します。 たとえば、contoso.com です。 既定の Web アプリ ページが表示されるはずです。
2. 最初の Web アプリを停止します。
3. Web ブラウザーを閉じて、数分待ちます。
4. Web ブラウザーを起動し、ドメインを参照します。 まだ、既定の Web アプリ ページが表示されるはずです。
5. 2 番目の Web アプリを停止します。
6. Web ブラウザーを閉じて、数分待ちます。
7. Web ブラウザーを起動し、ドメインを参照します。 Web アプリが停止されていることを示すエラー 403 が表示されます。
8. 2 番目の Web アプリを開始します。
9. Web ブラウザーを閉じて、数分待ちます。
10. Web ブラウザーを起動し、ドメインを参照します。 既定の Web アプリ ページが再び表示されるはずです。

## <a name="next-steps"></a>次のステップ

エイリアス レコードの詳細については、次の記事を参照してください。

- [チュートリアル:Azure パブリック IP アドレスを参照するエイリアス レコードを構成する](tutorial-alias-pip.md)
- [チュートリアル:Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する](tutorial-alias-tm.md)
- [DNS に関する FAQ](./dns-faq.md#alias-records)

アクティブな DNS 名を移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](../app-service/manage-custom-dns-migrate-domain.md)」を参照してください。