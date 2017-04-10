---
title: "404 状態を返す Azure CDN エンドポイントのトラブルシューティング | Microsoft Docs"
description: "Azure CDN エンドポイントでの 404 応答コードのトラブルシューティングを行います。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53fdf1fe661167b468ef602634528e4d4173f606


---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>404 状態を返す CDN エンドポイントのトラブルシューティング
この記事は、404 エラーを返す [CDN エンドポイント](cdn-create-new-endpoint.md) に関する問題のトラブルシューティングを行う際に役立ちます。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]**をクリックします。

## <a name="symptom"></a>症状
CDN プロファイルとエンドポイントを作成しましたが、コンテンツが CDN で使用できないようです。  CDN URL を使用してコンテンツにアクセスしようとすると、HTTP 404 状態コードを受け取ります。 

## <a name="cause"></a>原因
以下のような原因が考えられます。

* ファイルの配信元が CDN で認識されない。
* エンドポイントが正しく構成されていないため、CDN が間違った場所を参照している。
* ホストが CDN からのホスト ヘッダーを拒否している。
* エンドポイントが CDN に反映される時間がなかった。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
> [!IMPORTANT]
> CDN エンドポイントが作成されてから登録内容が CDN に反映されるまでに時間がかかるため、エンドポイントはすぐには利用できません。   <b>Azure CDN from Akamai</b> プロファイルの場合、通常、反映は 1 分以内で完了します。  <b>Azure CDN from Verizon</b> プロファイルの場合、通常、反映は 90 分以内に完了しますが、もっと時間がかかる場合もあります。  このドキュメントの手順を完了しても、404 応答を受け取る場合は、サポート チケットを開く前に数時間待ってからもう一度確認することを検討してください。
> 
> 

### <a name="check-the-origin-file"></a>元のファイルを確認する
まず、キャッシュする必要があるファイルが配信元で使用可能で、パブリックにアクセスできることを確認する必要があります。  これを行うに最も簡単な方法は、InPrivate または Incognito セッションでブラウザーを開き、ファイルを直接参照することです。  単にアドレス ボックスに URL を入力するか貼り付け、それが予期したファイルかどうかを確認します。  この例では、Azure ストレージ アカウントのファイル ( `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`でアクセス可能) を使用します。  ご覧のように、テストは正常に合格しています。

![成功です。](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> これはファイルが公開されているかどうかを確認する最も早く簡単な方法ですが、組織内の一部のネットワーク構成では、このファイルが実際にはネットワークのユーザーにのみ表示される場合でも公開されているように錯覚することがあります (Azure でホストされている場合も同様)。  組織のネットワークに接続されていないモバイル デバイスや Azure の仮想マシンなど、テストできる外部ブラウザーがあれば理想的です。
> 
> 

### <a name="check-the-origin-settings"></a>配信元の設定を確認する
これで、ファイルがインターネットで公開されていることが確認できました。次は配信元の設定を確認する必要があります。  [Azure Portal](https://portal.azure.com) で、CDN プロファイルを参照し、トラブルシューティングを行うエンドポイントをクリックします。  表示された **[エンドポイント]** ブレードで、配信元をクリックします。  

![配信元が強調表示されている [エンドポイント] ブレード](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**[配信元]** ブレードが表示されます。 

![[配信元] ブレード](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>配信元の種類とホスト名
**[配信元の種類]** が正しいことを確認し、**[配信元のホスト名]** を確認します。  この例では、URL `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt` のホスト名部分は `cdndocdemo.blob.core.windows.net` です。  スクリーンショットを見れば、これが正しいことがわかります。  Azure Storage、Web アプリ、クラウド サービスが配信元の場合、 **[配信元のホスト名]** フィールドはドロップダウン リストになっているため、スペルが正しいかどうかを気にする必要はありません。  ただし、カスタムの配信元を使用する場合は、ホスト名のスペルが正しいかどうかが *きわめて重要* になります。

#### <a name="http-and-https-ports"></a>HTTP および HTTPS ポート
ここで **HTTP** および **HTTPS ポート**も確認します。  ほとんどの場合、80 と 443 は正しいポートであるため、変更する必要はありません。  ただし、配信元サーバーが別のポートでリッスンしている場合は、ここに示す必要があります。  不明な場合は、元のファイルの URL を確認してください。  HTTP および HTTPS の仕様では、既定値としてポート 80 と 443 が指定されています。 この例の URL `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`では、ポートは指定されていないため、既定値の 443 を前提としており、正しい設定になっています。  

ただし、前の手順でテストした元のファイルの URL は `http://www.contoso.com:8080/file.txt`です。  ホスト名セグメントの末尾が `:8080` であることに注意してください。  これにより、`www.contoso.com` の Web サーバーに接続する場合にポート `8080` を使用するようにブラウザーに指示されるため、**[HTTP ポート]** フィールドには 8080 を入力する必要があります。  これらのポート設定が影響するのは、配信元から情報を取得するためにエンドポイントが使用するポートのみであることに注意してください。

> [!NOTE]
> **Azure CDN from Akamai** エンドポイントでは、配信元の TCP ポート範囲全体が許可されません。  使用できない配信元ポートの一覧については、「 [Azure CDN from Akamai Allowed Origin Ports (Azure CDN from Akamai で使用できる配信元ポート)](https://msdn.microsoft.com/library/mt757337.aspx)」を参照してください。  
> 
> 

### <a name="check-the-endpoint-settings"></a>エンドポイント設定を確認する
**[エンドポイント]** ブレードに戻り、**[構成]** ボタンをクリックします。

![[構成] ボタンが強調表示されている [エンドポイント] ブレード](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

エンドポイントの **[構成]** ブレードが表示されます。

![[構成] ブレード](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>プロトコル
**[プロトコル]**では、クライアントで使用されているプロトコルが選択されていることを確認します。  クライアントで使用されているものと同じプロトコルが配信元へのアクセス時に使用されるため、前のセクションで配信元のポートを正しく構成することが重要になります。  エンドポイントは、配信元のポートに関係なく、既定の HTTP および HTTPS ポート (80 および 443) でのみリッスンします。

`http://www.contoso.com:8080/file.txt`を使用する仮想例に戻りましょう。  前述のとおり、Contoso では HTTP ポートとして `8080` が指定されていますが、HTTPS ポートとして `44300` が指定されていることも前提とします。  `contoso` という名前のエンドポイントを作成した場合、CDN エンドポイントのホスト名は `contoso.azureedge.net` になります。  `http://contoso.azureedge.net/file.txt` の要求は HTTP 要求であるため、エンドポイントはポート 8080 で HTTP を使ってファイルを配信元から取得します。  `https://contoso.azureedge.net/file.txt`のうような HTTPS 経由のセキュリティで保護された要求の場合は、配信元からファイルを取得する際にポート 44300 で HTTPS を使うことになります。

#### <a name="origin-host-header"></a>配信元のホスト ヘッダー
**[配信元のホスト ヘッダー]** は、各要求で配信元に送られるホスト ヘッダーの値です。  ほとんどの場合、これは前の手順で確認した **[配信元のホスト名]** と同じになります。  通常、このフィールドの値が正しくなくても 404 状態は表示されません。ただし、配信元の要求に応じて、他の 4xx 状態が表示される可能性があります。

#### <a name="origin-path"></a>配信元のパス
最後に、 **[配信元のパス]**を確認する必要があります。  既定では、これは空白になっています。  このフィールドは、CDN で使用できるようにする配信元でホストされているリソースの範囲を限定する場合にのみ使用する必要があります。  

たとえば、この例のエンドポイントの場合、ストレージ アカウントのすべてのリソースを使用できるようにするため、 **[配信元のパス]** は空白のままにします。  つまり、`https://cdndocdemo.azureedge.net/publicblob/lorem.txt` への要求では、エンドポイントから `/publicblob/lorem.txt` を要求する `cdndocdemo.core.windows.net` に接続されることになります。  同様に、`https://cdndocdemo.azureedge.net/donotcache/status.png` の要求では、エンドポイントは配信元からの `/donotcache/status.png` を要求します。

しかし、配信元の各パスで CDN を使用しない場合もあります。  つまり、`publicblob` パスを使用する場合に、  **[配信元のパス]** フィールドに「*/publicblob*」と入力すると、配信元へのすべての要求の前にエンドポイントが */publicblob* を挿入します。  これは、`https://cdndocdemo.azureedge.net/publicblob/lorem.txt` の要求では実際に URL の要求部分である `/publicblob/lorem.txt` を使用し、先頭に `/publicblob` が付加されるようになることを意味します。 そのため、配信元からの `/publicblob/publicblob/lorem.txt` が要求されます。  そのパスが実際のファイルに解決されない場合、配信元は 404 状態を返します。  この例の lorem.txt を取得するための正しい URL は、実際には `https://cdndocdemo.azureedge.net/lorem.txt` になります。  URL の要求部分は `/lorem.txt` であるため、*/publicblob* パスを一切指定していなくても、エンドポイントによって `/publicblob` が付加されることで、配信元に渡される要求が `/publicblob/lorem.txt` になることに注意してください。




<!--HONumber=Nov16_HO3-->


