---
title: "Azure Application Gateway での複数のサイトのホスト | Microsoft Docs"
description: "このページでは、Azure Portal を使って同じゲートウェイで複数の Web アプリケーションをホストするために既存の Azure アプリケーション ゲートウェイを構成する手順について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 90b7e2f7f5327684f173bd7e10f21e65bea8fbe7


---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>複数の Web アプリケーションをホストするために既存のアプリケーション ゲートウェイを構成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

複数サイトのホストにより、複数の Web アプリケーションを同じアプリケーション ゲートウェイにデプロイすることができます。 どのリスナーがトラフィックを受信するかを決定するには、受信 HTTP 要求にホスト ヘッダーがあるかどうかに依存します。 リスナーは、ゲートウェイのルール定義の構成に従って、適切なバックエンド プールにトラフィックを送ります。 SSL 対応の Web アプリケーションの場合、アプリケーション ゲートウェイは Server Name Indication (SNI) 拡張機能を使用して Web トラフィックに適切なリスナーを選択します。 複数サイトのホストの一般的な用途は、さまざまな Web ドメインに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。 同様に、同じルート ドメインの複数のサブドメインも、同じアプリケーション ゲートウェイでホストすることができます。

## <a name="scenario"></a>シナリオ

次の例では、アプリケーション ゲートウェイは、2 つのバックエンド サーバー プール (contoso サーバー プールと fabrikam サーバー プール) を使用して contoso.com および fabrikam.com のトラフィックを処理します。 同様の設定は、app.contoso.com や blog.contoso.com などのサブドメインのホストにも使用できます。

![複数サイトのシナリオ][multisite]

## <a name="before-you-begin"></a>開始する前に

このシナリオでは、既存のアプリケーション ゲートウェイに複数サイトのサポートを追加します。 このシナリオを完了するには、既存のアプリケーション ゲートウェイを構成できる必要があります。 ポータルで基本的なアプリケーション ゲートウェイを作成する方法については、「[ポータルを使ってアプリケーション ゲートウェイを作成する](application-gateway-create-gateway-portal.md)」をご覧ください。

アプリケーション ゲートウェイを更新するために必要な手順を次に示します。

1. 各サイトに使うバックエンド プールを作成します。
2. アプリケーション ゲートウェイがサポートする各サイトにリスナーを作成します。
3. 各リスナーと適切なバックエンドをマップするルールを作成します。

## <a name="requirements"></a>必要条件

* **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。 一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。 FQDN を使用することもできます。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。 複数サイト対応のアプリケーション ゲートウェイでは、ホスト名と SNI インジケーターも追加されます。
* **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。
* **証明書:** 各リスナーに固有の証明書が必要です。この例では、複数サイト用に 2 つのリスナーを作成します。 したがって、2 つの .pfx 証明書とパスワードを作成する必要があります。

## <a name="create-back-end-pools-for-each-site"></a>各サイトのバックエンド プールを作成する

アプリケーション ゲートウェイがサポートする各サイトにバックエンド プールが必要です。この例では、contoso11.com 用と fabrikam11.com 用に 2 つのプールを作成します。

### <a name="step-1"></a>手順 1

Azure Portal (https://portal.azure.com) で既存のアプリケーション ゲートウェイに移動します。 **[バックエンド プール]** を選び、**[追加]** をクリックします

![バックエンド プールを追加する][7]

### <a name="step-2"></a>手順 2.

バックエンド プール **pool1** の情報を入力し、バックエンド サーバーの IP アドレスまたは FQDN を追加して、**[OK]** をクリックします

![バックエンド プール pool1 の設定][8]

### <a name="step-3"></a>手順 3.

バックエンド プール ブレードで **[追加]** をクリックして新しいバックエンド プール **pool2** を追加し、バックエンド サーバーの IP アドレスまたは FQDN を追加して、**[OK]** をクリックします

![バックエンド プール pool2 の設定][9]

## <a name="create-listeners-for-each-back-end"></a>各バックエンド用のリスナーを作成する

Application Gateway は、複数の Web サイトを同じパブリック IP アドレスとポートでホストするために、HTTP 1.1 ホスト ヘッダーを利用します。 ポータルで作成される基本的なリスナーには、このプロパティは含まれません。

### <a name="step-1"></a>手順 1

既存のアプリケーション ゲートウェイで **[リスナー]** をクリックし、**[Multi-site]** (マルチサイト) をクリックして最初のリスナーを追加します。

![リスナー概要ブレード][1]

### <a name="step-2"></a>手順 2.

リスナーの情報を入力します。 SSL 終了が設定されているこの例では、新しいフロント エンド ポートを作成します。 SSL 終了に使う .pfx 証明書をアップロードします。 このブレードと標準的な基本リスナー ブレードとの唯一の違いは、ホスト名です。

![リスナー プロパティ ブレード][2]

### <a name="step-3"></a>手順 3.

**[Multi-site]** (マルチサイト) をクリックし、前の手順の説明に従って&2; 番目のサイト用に別のリスナーを作成します。 第&2; のリスナーには別の証明書を使います。 このブレードと標準的な基本リスナー ブレードとの唯一の違いは、ホスト名です。 リスナーの情報を入力して **[OK]** をクリックします。

![リスナー プロパティ ブレード][3]

> [!NOTE]
> Azure Portal でのアプリケーション ゲートウェイ用リスナーの作成は長時間のタスクであり、このシナリオで&2; つのリスナーを作成するには時間がかかる場合があります。 完了すると、次の図のようにポータルにリスナーが表示されます。

![リスナーの概要][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>リスナーをバックエンド プールにマップするルールを作成する

### <a name="step-1"></a>手順 1

Azure Portal (https://portal.azure.com) で既存のアプリケーション ゲートウェイに移動します。 **[ルール]** を選び、既存の既定のルール **rule1** を選んで、**[編集]** をクリックします。

### <a name="step-2"></a>手順 2.

次の図に示すように、ルール ブレードに入力します。 1 番目のリスナーと&1; 番目のプールを選び、完了したら **[保存]** をクリックします。

![既存のルールを編集する][6]

### <a name="step-3"></a>手順 3.

**[Basic rule (基本ルール)]** をクリックして、2 番目のルールを作ります。 2 番目のリスナーと&2; 番目のバックエンド プールの情報をフォームに入力し、**[OK]** をクリックして保存します。

![基本ルールの追加ブレード][10]

以上のシナリオで、Azure Portal での複数サイトのサポートを含む既存アプリケーション ゲートウェイの構成は終了です。

## <a name="next-steps"></a>次のステップ

[Application Gateway - Web アプリケーション ファイアウォール](application-gateway-webapplicationfirewall-overview.md)を使用して Web サイトを保護する方法について学びます。

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png



<!--HONumber=Jan17_HO4-->


