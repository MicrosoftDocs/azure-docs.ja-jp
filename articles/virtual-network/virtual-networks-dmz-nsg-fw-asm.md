---
title: 境界ネットワークの例 – 境界ネットワークを構築し、ファイアウォールと NSG を使用してアプリケーションを保護する | Microsoft Docs
description: ファイアウォールとネットワーク セキュリティ グループ (NSG) を使用して境界ネットワークを構築する
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997466"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>例 2:境界ネットワークを構築し、ファイアウォールと NSG を使用してアプリケーションを保護する
[「Microsoft クラウド サービスとネットワーク セキュリティ」ページに戻る][HOME]

この例では、ファイアウォール、4 台の Windows Server コンピューター、およびネットワーク セキュリティ グループ (NSG) を使用して境界ネットワーク (*DMZ*、"*非武装地帯*"、"*スクリーン サブネット*" とも呼ばれます) を作成する方法を示します。 また、各手順をより深く理解できるように、関連する各コマンドについても詳しく説明します。 「トラフィックのシナリオ」セクションでは、トラフィックが境界ネットワークの防御層を通過する過程を順を追って説明します。 最後の「参照」セクションでは、さまざまなシナリオでテストおよび実験ができるように、この環境を構築するための完全なコードと手順を紹介します。

![NVA、NSG を含む受信境界ネットワーク][1]

## <a name="environment"></a>環境 
この例は、以下の項目を含む Azure サブスクリプションを使用するシナリオに基づいています。

* 2 つのクラウド サービス: FrontEnd001 と BackEnd001。
* 次の 2 つのサブネットを持つ CorpNetwork という名前の仮想ネットワーク:FrontEnd と BackEnd。
* 両方のサブネットに適用される単一の NSG。
* ネットワーク仮想アプライアンス:FrontEnd サブネットに接続されている Barracuda NextGen ファイアウォール。
* アプリケーション Web サーバーを表す Windows Server コンピューター:IIS01。
* アプリケーション バックエンド サーバーを表す 2 つの Windows Server コンピューター:AppVM01 と AppVM02。
* DNS サーバーを表す Windows Server コンピューター:DNS01。

> [!NOTE]
> この例では Barracuda NextGen ファイアウォールを使用していますが、他にも多くのネットワーク仮想アプライアンスを使用できます。
> 
> 

この例で示した環境の大部分は、この記事の「参照」セクションで紹介している PowerShell スクリプトで構築します。 VM と仮想ネットワークはスクリプトで構築しますが、そのプロセスについての詳しい説明はこのドキュメントにはありません。

環境を構築するには

1. 「参照」セクションに示しているネットワーク構成用 XML ファイルを保存します (ご自身のシナリオに一致するように、名前、ロケーション情報、IP アドレスを更新します)。
2. スクリプトを実行する環境に合わせて PowerShell スクリプト内のユーザー定義変数を更新します (サブスクリプション、サービス名など)。
3. PowerShell でスクリプトを実行します。

> [!NOTE]
> PowerShell スクリプトで指定されたリージョンと、ネットワーク構成用 XML ファイルで指定されたリージョンが一致している必要があります。
>
>

スクリプトが正常に実行したら、次の手順を実行できます。

1. ファイアウォール ルールを設定します。 この記事の「ファイアウォール ルール」のセクションを参照してください。
2. 必要に応じて、簡単な Web アプリケーションを含む Web サーバーとアプリ サーバーをセットアップして、この境界ネットワーク構成でのテストを行うことができます。 「参照」セクションに示している 2 つのアプリケーション スクリプトを使用できます。

次のセクションでは、NSG に関連するスクリプトのステートメントの大部分について説明します。

## <a name="nsgs"></a>NSG
この例では、NSG グループを作成し、そこに 6 つのルールを設定します。

> [!TIP]
> 一般的には、具体的な "Allow" ルールを作成してから、包括的な "Deny" ルールを作成してください。 どのルールが先に評価されるかは、割り当てる優先度によって決まります。 特定のルールに該当することが判明したトラフィックに対しては、他のルールは評価されません。 NSG ルールは、(サブネットから見て) 受信方向または送信方向のどちらか一方に適用できます。
> 
> 

ここでは、受信トラフィックに対して次の内容のルールを作成しています。

1. 内部的な DNS トラフィック (ポート 53) を許可する。
2. インターネットから任意の VM への RDP トラフィック (ポート 3389) を許可する。
3. インターネットから NVA (ファイアウォール) への HTTP トラフィック (ポート 80) を許可する。
4. IIS01 から AppVM01 へのすべてのトラフィック (すべてのポート) を許可する。
5. インターネットから仮想ネットワーク全体 (両方のサブネット) へのすべてのトラフィック (すべてのポート) を拒否する。
6. FrontEnd サブネットから BackEnd サブネットへのすべてのトラフィック (すべてのポート) を拒否する。

これらのルールが各サブネットにバインドされている状態で、インターネットから Web サーバーへの HTTP 要求が受信された場合、ルール 3 (Allow) とルール 5 (Deny) の両方が該当するように思われますが、ルール 3 の方が優先度が高いので、ルール 3 のみが適用されます。 ルール 5 は作用しません。 したがって、ファイアウォールへの HTTP 要求は許可されます。

同じトラフィックが DNS01 サーバーに到達しようとしている場合は、ルール 5 (Deny) が最初に適用されるので、トラフィックはサーバーに到達できません。 FrontEnd サブネットから BackEnd サブネットへの通信は、ルール 6 (Deny) によってブロックされます (ルール 1 とルール 4 で許可されたトラフィックを除く)。 これによって、FrontEnd にある Web アプリケーションのセキュリティが攻撃者によって侵害されたとしても BackEnd ネットワークは保護されます。 この場合、BackEnd の "保護された" ネットワークに対する攻撃者のアクセスは制限されます。 (攻撃者のアクセスは AppVM01 サーバー上で公開されているリソースのみに限定されます。)

インターネットへ向かうトラフィックは、既定の送信ルールによって許可されています。 この例では、送信トラフィックを許可していますが、送信ルールに対する変更は一切行っていません。 両方向のトラフィックをロック ダウンするには、ユーザー定義ルーティングが必要です。 この手法については、[メインのセキュリティ境界ドキュメント][HOME]の別の例で説明しています。

ここで説明した NSG ルールは、「[例 1 - NSG を使用して単純な DMZ を構築する][Example1]」で使用した NSG ルールと似ています。 個々の NSG ルールとその属性について詳しくは、その記事で NSG の説明を再確認してください。

## <a name="firewall-rules"></a>ファイアウォール規則
ファイアウォールを管理し、必要な構成を作成するには、コンピューターに管理クライアントをインストールする必要があります。 デバイスの管理方法については、ご利用のファイアウォール (または他の NVA) ベンダーのドキュメントを参照してください。 以降、このセクションでは、ファイアウォールそのものの構成について説明しています。ファイアウォールの構成は、(Azure portal や PowerShell ではなく) ベンダーの管理クライアントを使って行います。

この例で使用するクライアントをダウンロードして Barracuda ファイアウォールに接続する手順については、「[Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)」を参照してください。

ファイアウォールには、転送ルールを作成する必要があります。 この例のシナリオでルーティングするのは、インターネットからファイアウォールを介して Web サーバーに到達する受信トラフィックだけであるため、必要な転送 NAT ルールは 1 つだけです。 この例に使用されている Barracuda ファイアウォールでそれに該当するのが、そのトラフィックを許可する Destination NAT ルール (Dst NAT) です。

次のルールを作成するには (または既にある既定のルールを確認するには)、次の手順を実行します。
1. Barracuda NG Admin クライアント ダッシュボードで、[configuration]\(構成\) タブの **[Operational Configuration]\(操作の構成\)** セクションで **[Ruleset]\(ルールセット\)** を選択します。 

   ファイアウォールに対して既にアクティブになっているルールと非アクティブのルールとが **[Main Rules]\(メイン ルール\)** というグリッドに表示されます。

2. 新しいルールを作成するには、このグリッドの右上隅にある小さな緑色の **+** ボタンを選択します。 (ファイアウォールがロックされている場合があります。 ボタンに "**ロック**" と表示されていてルールの作成や編集ができない場合は、このボタンを選択してルールセットのロックを解除し、編集できる状態にしてください。)
  
3. 既存のルールを編集するには、そのルールを選択して右クリックし、**[Edit Rule]\(ルールの編集\)** を選択します。

新しいルールを作成して名前を付けます (**WebTraffic** など)。 

Destination NAT ルールのアイコンは ![Destination NAT アイコン][2]

ルール自体は次のように表示されます。

![ファイアウォール ルール][3]

HTTP のポート 80 または HTTPS のポート 443 を宛先としてファイアウォールに到達したすべての受信トラフィックは、ファイアウォールの DHCP1 Local IP インターフェイスから送出され、IP アドレス 10.0.1.5 の Web サーバーにリダイレクトされます。 トラフィックはポート 80 に着信し、Web サーバーのポート 80 に向かうため、ポートの変更は必要ありません。 しかし仮に Web サーバーの待機ポートが 8080 であった場合、ファイアウォールの受信ポートである 80 を Web サーバーの受信ポートである 8080 に変換する必要があります。その場合は、[Target List]\(ターゲット リスト\) に「10.0.1.5:8080」と入力することになります。

接続方法も指定する必要があります。 インターネットからの宛先ルールとしては、Dynamic SNAT が最適な方法です。

これまでに作成したルールは 1 つだけですが、優先度を正しく設定することが重要です。 ファイアウォールに対して設定されたすべてのルールの中で、この新しいルールがグリッドの一番下 (BLOCKALL ルールの下) に設定されている場合、新しいルールがまったく機能しません。 Web トラフィック用に新しく作成したルールが BLOCKALL ルールよりも上に来るようにしてください。

作成したルールは、ファイアウォールにプッシュしたうえでアクティブにする必要があります その作業を行わないと、ルールの変更が反映されません。 プッシュとアクティブ化のプロセスについては、次のセクションで説明します。

## <a name="rule-activation"></a>ルールのアクティブ化
ルールをルールセットに追加したので、そのルールセットをファイアウォールにアップロードしてアクティブ化する必要があります。

![ファイアウォール ルールのアクティブ化][4]

管理クライアントの右上隅にボタンのグループがあります。 **[Send Changes]\(変更の送信\)** を選択して変更したルールセットをファイアウォールに送信したら、**[Activate]\(アクティブ化\)** を選択します。

ファイアウォールのルールセットをアクティブ化したので、環境の構築は完了です。 必要に応じて、「参照」セクションにあるサンプル アプリケーション スクリプトを実行して、環境にアプリケーションを追加できます。 アプリケーションを追加したら、次のセクションで説明されているトラフィックのシナリオをテストできます。

> [!IMPORTANT]
> Web サーバーに直接到達するわけではないことに注意してください。 FrontEnd001.CloudApp.Net にある HTTP ページがブラウザーから要求されたとき、HTTP エンドポイント (ポート 80) はまず、そのトラフィックを Web サーバーにではなくファイアウォールに渡します。 その後ファイアウォールが、先ほど作成したルールに従って、NAT を使用して Web サーバーに要求をマップします。
> 
> 

## <a name="traffic-scenarios"></a>トラフィックのシナリオ
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(許可) Web からファイアウォールを介して Web サーバーにアクセス
1. インターネット ユーザーが、FrontEnd001.CloudApp.Net (インターネットに接続されたクラウド サービス) にある HTTP ページを要求します。
2. クラウド サービスは、ポート 80 で開放されているエンドポイントを介して、ファイアウォールのローカル インターフェイス 10.0.1.4:80 にトラフィックを渡します。
3. FrontEnd サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。 次のルールに進みます。
   2. NSG ルール 2 (RDP) は該当しません。 次のルールに進みます。
   3. NSG ルール 3 (インターネットからファイアウォール) が該当します。 トラフィックが許可されます。 ルールの処理はここで終了します。
4. トラフィックがファイアウォールの内部 IP アドレス (10.0.1.4) に到達します。
5. ファイアウォール転送ルールは、これをポート 80 のトラフィックと見なし、Web サーバー IIS01 にこのトラフィックをリダイレクトします。
6. Web トラフィックをリッスンしている IIS01 が、この要求を受け取って、その処理を開始します。
7. IIS01 が AppVM01 上の SQL Server インスタンスに情報を要求します。
8. FrontEnd サブネットに送信ルールはないので、トラフィックは許可されます。
9. BackEnd サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。 次のルールに進みます。
   2. NSG ルール 2 (RDP) は該当しません。 次のルールに進みます。
   3. NSG ルール 3 (インターネットからファイアウォール) は該当しません。 次のルールに進みます。
   4. NSG ルール 4 (IIS01 から AppVM01) が該当します。 トラフィックが許可されます。 ルールの処理はここで終了します。
10. AppVM01 上の SQL Server インスタンスが要求を受信し、応答します。
11. BackEnd サブネットに送信ルールは存在しないので、応答は許可されます。
12. FrontEnd サブネットが、以下に示す受信ルールの処理を開始します。
    1. BackEnd サブネットから FrontEnd サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
    2. サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
13. IIS01 が AppVM01 からの応答を受信し、HTTP 応答を完成させて要求元に送信します。
14. これはファイアウォールからの NAT セッションであるため、応答の最初の宛先はファイアウォールです。
15. ファイアウォールが Web サーバーから応答を受信し、インターネット ユーザーに返します。
16. FrontEnd サブネットに対する送信ルールは存在しないので、応答は許可され、インターネット ユーザーは Web ページを受信します。

#### <a name="allowed-rdp-to-backend"></a>(許可) BackEnd への RDP
1. インターネット上のサーバー管理者が BackEnd001.CloudApp.Net:*xxxxx* 上の AppVM01 に対する RDP セッションを要求します。ここで、*xxxxx* は AppVM01 に対する RDP のポート番号で、ランダムに割り当てられます。 (割り当てられたポートは、Azure portal 上で、または PowerShell を使用して見つけることができます。)
2. ファイアウォールがリッスンするのは FrontEnd001.CloudApp.Net のアドレスのみであるため、このトラフィック フローにファイアウォールは関係しません。
3. BackEnd サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。 次のルールに進みます。
   2. NSG ルール 2 (RDP) が該当します。 トラフィックが許可されます。 ルールの処理はここで終了します。
4. 送信ルールがないので、既定のルールが適用され、戻りトラフィックが許可されます。
5. RDP セッションが可能な状態になります。
6. AppVM01 はユーザー名とパスワードを求めるメッセージを表示します。

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(許可) DNS サーバーに対する Web サーバーの DNS 参照
1. Web サーバーである IIS01 が、www.data.gov にあるデータ フィードを必要としています。そのためにはアドレスを解決する必要があります。
2. 仮想ネットワーク用のネットワーク構成にはプライマリ DNS サーバーとして、DNS01 (BackEnd サブネット上の 10.0.2.4) が示されています。 IIS01 は DNS 要求を DNS01 に送信します。
3. FrontEnd サブネットに送信ルールはないので、トラフィックは許可されます。
4. BackEnd サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) が該当します。 トラフィックが許可されます。 ルールの処理はここで終了します。
5. DNS サーバーが要求を受信します。
6. DNS サーバーは、アドレスがキャッシュされていないことがわかると、インターネット上のルート DNS サーバーを照会します。
7. BackEnd サブネットに送信ルールはないので、トラフィックは許可されます。
8. インターネット DNS サーバーが応答します。 このセッションは内部から開始されたため、応答は許可されます。
9. DNS サーバーは応答をキャッシュし、IIS01 からの要求に応答します。
10. BackEnd サブネットに送信ルールはないので、トラフィックは許可されます。
11. FrontEnd サブネットが、以下に示す受信ルールの処理を開始します。
    1. BackEnd サブネットから FrontEnd サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
    2. サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
12. IIS01 が DNS01 から応答を受信します。

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(許可) Web サーバーによる AppVM01 上のファイルへのアクセス
1. IIS01 が AppVM01 上のファイルを要求します。
2. FrontEnd サブネットに送信ルールはないので、トラフィックは許可されます。
3. BackEnd サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。 次のルールに進みます。
   2. NSG ルール 2 (RDP) は該当しません。 次のルールに進みます。
   3. NSG ルール 3 (インターネットからファイアウォール) は該当しません。 次のルールに進みます。
   4. NSG ルール 4 (IIS01 から AppVM01) が該当します。 トラフィックが許可されます。 ルールの処理はここで終了します。
4. AppVM01 が要求を受信し、ファイルを返します (アクセスが許可されている場合)。
5. BackEnd サブネットに送信ルールは存在しないので、応答は許可されます。
6. FrontEnd サブネットが、以下に示す受信ルールの処理を開始します。
   1. BackEnd サブネットから FrontEnd サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
   2. サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
7. IIS01 がファイルを受信します。

#### <a name="denied-web-direct-to-web-server"></a>(拒否) Web サーバーへの直接 Web アクセス
Web サーバー IIS01 とファイアウォールは同じクラウド サービス内に置かれているため、外部に公開された同じ IP アドレスを共有しています。 そのため HTTP トラフィックはすべてファイアウォールにリダイレクトされます。 要求は正常に処理されますが、Web サーバーに直接到達しているわけではありません。 指定されたとおりに、最初にファイアウォールを経由します。 トラフィック フローについては、このセクションの 1 つ目のシナリオを参照してください。

#### <a name="denied-web-to-backend-server"></a>(拒否) Web から BackEnd サーバー
1. インターネット ユーザーが、BackEnd001.CloudApp.Net サービスを介して AppVM01 上のファイルにアクセスを試みます。
2. ファイル共有用に開放されたエンドポイントがないので、このアクセスはクラウド サービスを通過できず、サーバーに到達しません。
3. 何らかの理由でエンドポイントが開放されていたとしても、NSG ルール 5 (インターネットから仮想ネットワーク) によってこのトラフィックはブロックされます。

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(拒否) DNS サーバーに対する Web DNS 参照
1. インターネット ユーザーが、BackEnd001.CloudApp.Net サービスを介して DNS01 上の内部 DNS レコードを参照しようとしています。
2. DNS 用に開放されたエンドポイントがないので、このアクセスはクラウド サービスを通過できず、サーバーに到達しません。
3. 何らかの理由でエンドポイントが開放されていたとしても、NSG ルール 5 (インターネットから仮想ネットワーク) によってこのトラフィックはブロックされます。 (ルール 1 [DNS] は次の 2 つの理由で該当しません。 1 つ目は発信元のアドレスがインターネットであるということ。このルールが該当するのは、発信元がローカル仮想ネットワークの場合のみです。 2 つ目は、Allow ルールでトラフィックが拒否されることは決してないということです。)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(拒否) ファイアウォールを経由した Web から SQL へのアクセス
1. インターネット ユーザーが、FrontEnd001.CloudApp.Net (インターネットに接続されたクラウド サービス) にある SQL データを要求します。
2. SQL 用に開放されているエンドポイントがないので、このアクセスはクラウド サービスを通過できず、ファイアウォールに到達しません。
3. 何らかの理由でエンドポイントが開放されていた場合、FrontEnd サブネットは、受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。 次のルールに進みます。
   2. NSG ルール 2 (RDP) は該当しません。 次のルールに進みます。
   3. NSG ルール 3 (インターネットからファイアウォール) が該当します。 トラフィックが許可されます。 ルールの処理はここで終了します。
4. トラフィックがファイアウォールの内部 IP アドレス (10.0.1.4) に到達します。
5. ファイアウォールは SQL 用の転送ルールを持たず、トラフィックを破棄します。

## <a name="conclusion"></a>まとめ
この例は、アプリケーションをファイアウォールで保護したり、バックエンド サブネットを受信トラフィックから切り離したりする方法としては比較的わかりやすい部類に入ります。

その他の例およびネットワーク セキュリティ境界の概要については、[こちら][HOME]を参照してください。

## <a name="references"></a>参照
### <a name="full-script-and-network-config"></a>完全なスクリプトとネットワーク構成
PowerShell スクリプト ファイルに完全なスクリプトを保存します。 NetworkConf2.xml という名前のファイルにネットワーク構成スクリプトを保存します。
必要に応じて、ユーザー定義の変数を変更します。 スクリプトを実行し、この記事の「ファイアウォール ルール」のセクションの手順に従ってください。

#### <a name="full-script"></a>完全なスクリプト
このスクリプトは、ユーザー定義の変数に基づいて、次の手順を実行します。

1. Azure サブスクリプションに接続します。
2. ストレージ アカウントを作成します。
3. ネットワーク構成ファイルの定義に従って、仮想ネットワークを 1 つ、サブネットを 2 つ作成します。
4. 4 つの Windows Server VM を構築します。
5. NSG を構成します。 構成では、次の手順を実行します。
   * NSG を作成します。
   * NSG にルールを設定します。
   * 適切なサブネットに NSG をバインドします。

この PowerShell スクリプトは、インターネットに接続されているコンピューターまたはサーバー上でローカルに実行する必要があります。

> [!IMPORTANT]
> このスクリプトを実行すると、PowerShell に警告またはその他の情報メッセージが表示される場合があります。 考慮する必要があるのは、赤色のエラー メッセージのみです。
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>ネットワーク構成ファイル
この XML ファイルに実際のロケーション情報を反映して保存し、このファイルへのリンクを上記スクリプト内の $NetworkConfigFile 変数に追加します。

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>サンプル アプリケーション スクリプト
この例や他の境界ネットワークの例のサンプル アプリケーションをインストールする場合は、[サンプル アプリケーション スクリプト][SampleApp]を参照してください。

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "NSG での受信 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "送信先 NAT アイコン"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "ファイアウォール ルール"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "ファイアウォール ルールのアクティブ化"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
