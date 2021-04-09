---
title: Azure CDN エンドポイントのトラブルシューティング - 404 状態コード
description: 404 HTTP 応答状態コードを返す Content Delivery Network エンドポイントに関する問題のトラブルシューティングを行う方法について説明します。
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192493"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>404 状態コードを返す Azure CDN エンドポイントのトラブルシューティング
この記事により、404 HTTP 応答状態コードを返す Content Delivery Network (CDN) エンドポイントに関する問題のトラブルシューティングを行うことができます。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートの要求]** をクリックしてください。

## <a name="symptom"></a>症状
CDN プロファイルとエンドポイントを作成しましたが、コンテンツが CDN で使用できないようです。 CDN URL を使用してコンテンツにアクセスしようとすると、HTTP 404 状態コードが返されます。 

## <a name="cause"></a>原因
以下のような原因が考えられます。

* ファイルの配信元が CDN で認識されない。
* エンドポイントが正しく構成されていないため、CDN が間違った場所を参照している。
* ホストが CDN からのホスト ヘッダーを拒否している。
* エンドポイントが CDN に反映される時間がなかった。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
> [!IMPORTANT]
> CDN エンドポイントが作成されてから登録内容が CDN に反映されるまでに時間がかかるため、エンドポイントはすぐには利用できません。
> - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
> - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
> - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 90 分以内で完了します。 
> 
> このドキュメントの手順を完了しても、404 応答を受け取る場合は、サポート チケットを開く前に数時間待ってからもう一度確認することを検討してください。
> 
> 

### <a name="check-the-origin-file"></a>元のファイルを確認する
まず、キャッシュするファイルが配信元サーバーで使用できること、およびインターネットでパブリックにアクセスできることを確認します。 これを行う最も簡単な方法は、private または Incognito セッションでブラウザーを開き、ファイルを直接参照することです。 アドレス ボックスに URL を入力するか貼り付けて、それが想定どおりのファイルであることを確認します。 たとえば、Azure ストレージ アカウントにファイルがあり、https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt でアクセスできるとします。 このファイルの内容を適切に読み込むことができれば、テストは合格です。

![Success!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> これはファイルが公開されているかどうかを確認する最も早く簡単な方法ですが、組織内の一部のネットワーク構成では、ファイルが実際にはネットワークのユーザーにのみ表示される場合でも、公開されているように表示されることがあります (Azure でホストされている場合も同様)。 そうでないことを確認するには、組織のネットワークに接続されていないモバイル デバイスや Azure の仮想マシンなど、外部のブラウザーでファイルをテストします。
> 
> 

### <a name="check-the-origin-settings"></a>配信元の設定を確認する
ファイルがインターネットで公開されていることを確認したら、配信元の設定を確認します。 [Azure Portal](https://portal.azure.com) で、CDN プロファイルを参照し、トラブルシューティングを行うエンドポイントを選択します。 表示された **[エンドポイント]** ページで、配信元を選択します。  

![配信元が強調表示されている [エンドポイント] ページ](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**[配信元]** ページが表示されます。 

![[配信元] ページ](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>配信元の種類とホスト名
**[配信元の種類]** と **[配信元のホスト名]** の値が正しいことを確認します。 この例の https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt という URL のホスト名部分は *cdndocdemo.blob.core.windows.net* で、これは正しい値です。 Azure Storage、Web アプリ、クラウド サービスの配信元として、 **[配信元のホスト名]** フィールドではドロップダウン リスト値が使用されているため、スペル ミスを気にする必要はありません。 ただし、カスタムの配信元を使用する場合は、ホスト名のスペルが正しいことを確認してください。

#### <a name="http-and-https-ports"></a>HTTP および HTTPS ポート
お使いの **HTTP ポート** および **HTTPS ポート** を確認してください。 ほとんどの場合、80 と 443 は正しいポートであるため、変更する必要はありません。  ただし、配信元サーバーが別のポートでリッスンしている場合は、ここに示す必要があります。 不明な場合は、配信元のファイルの URL を表示します。 HTTP および HTTPS の仕様では、既定値としてポート 80 と 443 が使用されます。 この例の URL https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt では、ポートは指定されていないため、既定値の 443 が想定され、正しい設定になっています。  

ただし、前にテストした配信元のファイルの URL は http:\//www.contoso.com:8080/file.txt です。 ホスト名セグメントの末尾の *:8080* の部分に注意してください。 この数値は、ブラウザーに対して、ポート 8080 を使用して www\.contoso.com の Web サーバーに接続するよう指示します。したがって、**[HTTP ポート]** フィールドには「*8080*」と入力する必要があります。 これらのポート設定が影響するのは、配信元から情報を取得するためにエンドポイントが使用するポートのみであることに注意してください。

> [!NOTE]
> **Azure CDN Standard from Akamai** エンドポイントでは、配信元の TCP ポート範囲全体が許可されません。  使用できない配信元ポートの一覧については、「 [Azure CDN from Akamai Allowed Origin Ports (Azure CDN from Akamai で使用できる配信元ポート)](/previous-versions/azure/mt757337(v=azure.100))」を参照してください。  
> 
> 

### <a name="check-the-endpoint-settings"></a>エンドポイント設定を確認する
**[エンドポイント]** ページで、 **[構成]** ボタンを選択します。

![[構成] ボタンが強調表示されている [エンドポイント] ページ](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

CDN エンドポイントの **[構成]** ページが表示されます。

![[構成] ページ](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>プロトコル
**[プロトコル]** では、クライアントで使用されているプロトコルが選択されていることを確認します。 クライアントで使用されているものと同じプロトコルが配信元へのアクセス時に使用されるため、前のセクションで配信元のポートが正しく構成されていることが重要です。 CDN エンドポイントは、配信元のポートに関係なく、既定の HTTP および HTTPS ポート (80 および 443) でのみリッスンします。

http:\//www.contoso.com:8080/file.txt を使用する仮説例に戻りましょう。  前述のとおり、Contoso では HTTP ポートとして *8080* が指定されていますが、HTTPS ポートとして *44300* が指定されていることも前提とします。  *contoso* という名前のエンドポイントを作成した場合、CDN エンドポイントのホスト名は *contoso.azureedge.net* です。  http:\//contoso.azureedge.net/file.txt の要求は HTTP 要求であるため、エンドポイントはポート 8080 で HTTP を使ってファイルを配信元から取得します。  https:\//contoso.azureedge.net/file.txt のような HTTPS 経由のセキュリティで保護された要求の場合は、配信元からファイルを取得する際にポート 44300 で HTTPS を使うことになります。

#### <a name="origin-host-header"></a>配信元のホスト ヘッダー
**[配信元のホスト ヘッダー]** は、各要求で配信元に送られるホスト ヘッダーの値です。  ほとんどの場合、これは前の手順で確認した **[配信元のホスト名]** と同じになります。  通常、このフィールドの値が正しくなくても 404 状態は表示されません。ただし、配信元の要求に応じて、他の 4xx 状態が表示される可能性があります。

#### <a name="origin-path"></a>配信元のパス
最後に、 **[配信元のパス]** を確認する必要があります。  既定では、これは空白になっています。  このフィールドは、CDN で使用できるようにする配信元でホストされているリソースの範囲を限定する場合にのみ使用する必要があります。  

この例のエンドポイントでは、ストレージ アカウントのすべてのリソースを使用可能にする必要があったため、 **[配信元のパス]** は空白のままにしました。  つまり、https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt への要求では、エンドポイントから、 */publicblob/lorem.txt* を要求する cdndocdemo.core.windows.net に接続されることになります。  同様に、https:\//cdndocdemo.azureedge.net/donotcache/status.png の要求では、エンドポイントは配信元から */donotcache/status.png* を要求します。

しかし、配信元に CDN を使用しないパスがある場合は、どうすればよいのでしょうか。  たとえば、*publicblob* パスだけを公開したいときなどです。  **[配信元のパス]** フィールドに「*/publicblob*」と入力すると、エンドポイントによって、配信元へのすべての要求の前に */publicblob* が挿入されます。  つまり、https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt の要求では、実際には URL の要求部分として */publicblob/lorem.txt* が使用され、この先頭に */publicblob* が付加されます。 そのため、配信元に対して */publicblob/publicblob/lorem.txt* が要求されます。  そのパスが実際のファイルに解決されない場合、配信元は 404 状態を返します。  この例で lorem.txt を取得するための正しい URL は、実際には https:\//cdndocdemo.azureedge.net/lorem.txt です。  */publicblob* パスが一切含まれていないことに注意してください。これは、URL の要求部分が */lorem.txt* であり、エンドポイントによって */publicblob* が付加されることで、配信元に渡される要求が */publicblob/lorem.txt* になるためです。

