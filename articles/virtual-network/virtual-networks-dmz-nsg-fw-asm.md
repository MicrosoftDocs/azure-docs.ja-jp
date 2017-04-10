---
title: "DMZ の例 - ファイアウォールと NSG から成る DMZ を構築してアプリケーションを保護する | Microsoft Docs"
description: "ファイアウォールとネットワーク セキュリティ グループ (NSG) から成る DMZ を構築する"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0


---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>例 2 - ファイアウォールと NSG から成る DMZ を構築してアプリケーションを保護する
[セキュリティ境界のベスト プラクティス ページに戻る][HOME]

この例では、ファイアウォールと 4 台の Windows Server、ネットワーク セキュリティ グループから成る DMZ を作成します。 また、各手順をより深く理解できるように、関連するコマンドを順に説明します。 さらに、「トラフィックに関するシナリオ」セクションでは、DMZ の防御層におけるトラフィックの進行過程を詳しく説明しています。 最後の「参照」セクションでは、さまざまなシナリオでテストおよび実験ができるように、この環境を構築するための完全なコードと手順を紹介します。 

![Inbound DMZ with NVA and NSG][1]

## <a name="environment-description"></a>環境の説明
この例で使用するサブスクリプションには、以下のものが含まれています。

* 2 つのクラウド サービス: "FrontEnd001" と "BackEnd001"
* 2 つのサブネット ("FrontEnd" と "BackEnd") を含む仮想ネットワーク "CorpNetwork"
* 両方のサブネットに適用される単一のネットワーク セキュリティ グループ
* フロントエンド サブネットに接続されたネットワーク仮想アプライアンス (この例では Barracuda NextGen Firewall)
* アプリケーション Web サーバーを表す Windows サーバー ("IIS01")
* アプリケーション バックエンド サーバーを表す 2 つの Windows サーバー ("AppVM01"、"AppVM02")
* DNS サーバーを表す Windows サーバー ("DNS01")

> [!NOTE]
> この例では Barracuda NextGen ファイアウォールを使用していますが、他にもさまざまなネットワーク仮想アプライアンスを使用できます。
> 
> 

以上に示した環境の大部分は、このページの「参照」セクションで紹介している PowerShell スクリプトで構築します。 VM と Virtual Network の構築については、スクリプト例には含まれていますが、このドキュメントでは詳細な説明を省略します。

環境を構築するには

1. 「参照」セクションに示しているネットワーク構成用 xml ファイルを保存します (特定のシナリオに一致するように、名前、ロケーション情報、IP アドレスを更新します)。
2. スクリプトを実行する環境に合わせてスクリプト内のユーザー変数を更新します (サブスクリプション、サービス名など)。
3. PowerShell でスクリプトを実行します。

**注**: PowerShell スクリプトで示されたリージョンは、ネットワーク構成用 xml ファイルで示されたリージョンと一致する必要があります。

スクリプトが正常に実行された後、必要に応じて次の手順を別途実行します。

1. ファイアウォール ルールを設定します。この点については、以降の「ファイアウォール ルール」というセクションで取り上げます。
2. この DMZ 構成を使ったテストを実行するために、必要に応じて「参照」セクションにある 2 つのスクリプトを実行します。簡単な Web アプリケーションを含む Web サーバーとアプリケーション サーバーがこれらのスクリプトによってセットアップされます。

次のセクションでは、ネットワーク セキュリティ グループに関連してスクリプトに含まれる大部分のステートメントを説明しています。

## <a name="network-security-groups-nsg"></a>ネットワーク セキュリティ グループ (NSG)
この例では、NSG グループを作成し、そこに 6 つのルールを設定します。 

> [!TIP]
> 一般的には、具体的な "Allow" ルールを作成してから、包括的な "Deny" ルールを作成してください。 どのルールが先に評価されるかは、割り当てる優先度によって決まります。 具体的なルールが一度適用されたトラフィックに対しては、他のルールは評価されません。 NSG ルールは、(サブネットから見て) 受信方向または送信方向のどちらか一方に適用できます。
> 
> 

ここでは、受信トラフィックに対して次の内容のルールを作成しています。

1. 内部的な DNS トラフィック (ポート 53) を許可する。
2. インターネットから任意の VM への RDP トラフィック (ポート 3389) を許可する。
3. インターネットから NVA (ファイアウォール) への HTTP トラフィック (ポート 80) を許可する。
4. IIS01 から AppVM1 への任意のトラフィック (すべてのポート) を許可する。
5. インターネットから VNet 全体 (両方のサブネット) への任意のトラフィック (すべてのポート) を拒否する。
6. フロントエンド サブネットからバックエンド サブネットへの任意のトラフィック (すべてのポート) を拒否する。

これらのルールが各サブネットにバインドされている状態で、インターネットから Web サーバーへの HTTP 要求が受信された場合、ルール 3 (Allow) とルール 5 (Deny) の両方が該当しますが、ルール 3 の方が優先度が高いので、ルール 3 のみが適用され、ルール 5 は作用しません。 したがって、ファイアウォールへの HTTP 要求は許可されます。 同じトラフィックが DNS01 サーバーに到達しようとしている場合は、ルール 5 (Deny) が最初に適用されるので、トラフィックはサーバーに到達できません。 フロントエンド サブネットからバックエンド サブネットへの通信は、ルール 6 (Deny) によってブロックされます (ルール 1 とルール 4 で許可されたトラフィックを除く)。これによって、フロントエンドにある Web アプリケーションのセキュリティが攻撃者によって侵害されたとしてもバックエンド ネットワークは保護されるため、バックエンドの "保護された" ネットワークに対する攻撃者のアクセスは (AppVM01 サーバー上で公開されているリソースに) 限定されます。

インターネットへ向かうトラフィックは、既定の送信ルールによって許可されています。 この例では、送信トラフィックを許可していますが、送信ルールに対する変更は一切行っていません。 トラフィックを双方向でロックダウンするには、ユーザー定義ルーティングが必要です。ユーザー定義ルーティングについては、別の例で詳しく説明しています。[メインのセキュリティ境界ドキュメント][HOME]を参照してください。

上に挙げた NSG ルールは、「[例 1 - NSG を使用して単純な DMZ を構築する][Example1]」で使用した NSG ルールとよく似ています。 個々の NSG ルールとその属性について詳しくは、そのドキュメントで NSG の説明を再確認してください。

## <a name="firewall-rules"></a>ファイアウォール ルール
ファイアウォールを管理したり、必要な構成を作成したりするには、管理クライアントを PC にインストールする必要があります。 デバイスの管理方法については、ご利用のファイアウォール (または他の NVA) ベンダーのドキュメントを参照してください。 以降、このセクションでは、ファイアウォールそのものの構成について説明しています。ファイアウォールの構成は、(Azure ポータルや PowerShell ではなく) ベンダーの管理クライアントを使って行います。

この例で使用するクライアントをダウンロードして Barracuda に接続する手順については、 [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

ファイアウォールには、転送ルールを作成する必要があります。 この例でルーティングするのは、インターネットからファイアウォールを介して Web サーバーに到達する受信トラフィックだけであるため、必要な転送 NAT ルールは 1 つだけです。 この例に使用されている Barracuda NextGen ファイアウォールでそれに該当するのが、そのトラフィックを許可する Destination NAT ルール ("Dst NAT") です。

次のルールを作成するには (または既にある既定のルールを確認するには)、Barracuda NG Admin クライアント ダッシュボードの [configuration] タブに移動し、[Operational Configuration] セクションの [Ruleset] をクリックします。 ファイアウォールに対して既にアクティブになっているルールと非アクティブのルールとが "Main Rules" というグリッドに表示されます。 このグリッドの右上隅に小さな緑色の "+" ボタンがあります。新しいルールはこのボタンをクリックして作成します (注: ファイアウォールが変更できないように "ロック" されている場合があります。ボタンに "Lock" と表示されていてルールを作成したり編集したりできない場合は、このボタンをクリックしてルールセットの "ロックを解除" し、編集できる状態にしてください)。 既存のルールを編集するには、そのルールを選択して右クリックし、[Edit Rule] を選択します。

新しいルールを作成して名前を付けます ("WebTraffic" など)。 

Destination NAT ルールのアイコンは  ![Destination NAT Icon][2]

ルール自体は次のように表示されます。

![Firewall Rule][3]

この場合、HTTP のポート 80 または HTTPS のポート 443 を宛先としてファイアウォールに到達したすべての受信トラフィックは、ファイアウォールの "DHCP1 Local IP" インターフェイスから送出され、IP アドレス 10.0.1.5 の Web サーバーにリダイレクトされます。 トラフィックはポート 80 に着信し、Web サーバーのポート 80 に向かうため、ポートの変更は必要ありません。 しかし仮に Web サーバーの待機ポートが 8080 であった場合、ファイアウォールの受信ポートである 80 を Web サーバーの受信ポートである 8080 に変換する必要があります。その場合は、[Target List] に「10.0.1.5:8080」と入力することになります。

[Connection Method] も指定する必要があります。インターネットからの宛先ルールには、"Dynamic SNAT" が最適です。 

これまでに作成したルールは 1 つだけですが、優先度は正しく設定することが大切です。 ファイアウォールに対して設定されたすべてのルールの中で、この新しいルールがグリッドの一番下 ("BLOCKALL" ルールの下) に設定されている場合、新しいルールがまったく機能しません。 Web トラフィック用に新しく作成したルールが BLOCKALL ルールよりも上に来るようにしてください。

作成したルールは、ファイアウォールにプッシュしたうえでアクティブにする必要があります。その作業を行わないと、ルールの変更が反映されません。 プッシュとアクティブ化のプロセスについては、次のセクションで説明します。

## <a name="rule-activation"></a>ルールのアクティブ化
このルールを追加したら、そのルールセットをファイアウォールにアップロードしてアクティブ化する必要があります。

![Firewall Rule Activation][4]

管理クライアントの右上隅に一連のボタンがあります。 [Send Changes] ボタンをクリックして、変更済みのルールをファイアウォールに送信し、そのうえで [Activate] ボタンをクリックしてください。

ファイアウォールのルールセットをアクティブ化すれば、この例における環境の構築は完了です。 必要に応じて「参照」セクションにある Post Build スクリプトを実行し、以降のトラフィックのシナリオをテストするためのアプリケーションをこの環境に追加してください。

> [!IMPORTANT]
> Web サーバーに直接到達するわけではないことに注意してください。 FrontEnd001.CloudApp.Net にある HTTP ページがブラウザーから要求されたとき、HTTP エンドポイント (ポート 80) はまず、そのトラフィックを Web サーバーにではなくファイアウォールに渡します。 その後ファイアウォールが、先ほど作成したルールに従って、Web サーバーへのネットワーク アドレス変換をその要求に対して実行します。
> 
> 

## <a name="traffic-scenarios"></a>トラフィックのシナリオ
#### <a name="allowed-web-to-web-server-through-firewall"></a>(許可) Web からファイアウォールを介して Web サーバーにアクセス
1. インターネット ユーザーが、FrontEnd001.CloudApp.Net (インターネットに接続されたクラウド サービス) にある HTTP ページを要求します。
2. クラウド サービスは、ポート 80 で開放されているエンドポイントを介して、ファイアウォールのローカル インターフェイス 10.0.1.4:80 にトラフィックを渡します。
3. フロントエンド サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
   2. NSG ルール 2 (RDP) は該当しません。次のルールに進みます。
   3. NSG ルール 3 (インターネットからファイアウォール) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
4. トラフィックがファイアウォールの内部 IP アドレス (10.0.1.4) に到達します。
5. ファイアウォール転送ルールは、これをポート 80 のトラフィックと見なし、Web サーバー IIS01 にこのトラフィックをリダイレクトします。
6. Web トラフィックをリッスンしている IIS01 が、この要求を受け取って、その処理を開始します。
7. IIS01 が AppVM01 上の SQL Server に情報を求めます。
8. フロントエンド サブネットに送信ルールはないので、トラフィックは許可されます。
9. バックエンド サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
   2. NSG ルール 2 (RDP) は適用されず、次のルールに進みます。
   3. NSG ルール 3 (インターネットからファイアウォール) は該当しません。次のルールに進みます。
   4. NSG ルール 4 (IIS01 から AppVM01) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
10. AppVM01 は SQL クエリを受信し、応答します。
11. バックエンド サブネットに送信ルールは存在しないので、応答は許可されます。
12. フロントエンド サブネットが、以下に示す受信ルールの処理を開始します。
    1. バックエンド サブネットからフロントエンド サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
    2. サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
13. IIS サーバーが SQL 応答を受信し、HTTP 応答を完成させて要求元に送信します。
14. これはファイアウォールからの NAT セッションであるため、応答の (最初の) 宛先はファイアウォールです。
15. ファイアウォールが Web サーバーから応答を受信し、インターネット ユーザーに返します。
16. フロント エンド サブネットに送信ルールは存在しないので、この応答は許可され、インターネット ユーザーは要求した Web ページを受信します。

#### <a name="allowed-rdp-to-backend"></a>(許可) バックエンドへの RDP
1. インターネット上のサーバー管理者が BackEnd001.CloudApp.Net:xxxxx 上の AppVM01 に対する RDP セッションを要求します。ここで、xxxxx は AppVM01 に対する RDP のポート番号で、ランダムに割り当てられます (割り当てられたポートは、Azure ポータル上で、または PowerShell を使用して見つけることができます)。
2. ファイアウォールが待機するのは FrontEnd001.CloudApp.Net のアドレスのみであるため、このトラフィック フローにファイアウォールは関係しません。
3. バックエンド サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
   2. NSG ルール 2 (RDP) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
4. 送信ルールがない場合は、既定のルールが適用され、戻りトラフィックが許可されます。
5. RDP セッションが可能な状態になります。
6. AppVM01 はユーザー名とパスワードを求めるメッセージを表示します。

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(許可) DNS サーバーに対する Web サーバーの DNS 参照
1. Web サーバーである IIS01 が、www.data.gov にあるデータ フィードを必要としています。そのためにはアドレスを解決する必要があります。
2. VNet 用のネットワーク構成にはプライマリ DNS サーバーとして、DNS01 (バックエンド サブネット上の 10.0.2.4) がリストされており、IIS01 は DNS 要求を DNS01 に送信します。
3. フロントエンド サブネットに送信ルールはないので、トラフィックは許可されます。
4. バックエンド サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
5. DNS サーバーは要求を受信します。
6. DNS サーバーは、アドレスがキャッシュされていないことがわかると、インターネット上のルート DNS サーバーに要求します。
7. バックエンド サブネットに送信ルールはないので、トラフィックは許可されます。
8. インターネット DNS サーバーが応答します。このセッションは内部から開始されたため、応答は許可されます。
9. DNS サーバーは応答をキャッシュし、最初の要求に応答して IIS01 に返します。
10. バックエンド サブネットに送信ルールはないので、トラフィックは許可されます。
11. フロントエンド サブネットが、以下に示す受信ルールの処理を開始します。
    1. バックエンド サブネットからフロントエンド サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
    2. サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
12. IIS01 が DNS01 から応答を受信します。

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(許可) Web サーバーが AppVM01 上のファイルにアクセスする
1. IIS01 が AppVM01 上のファイルを要求します。
2. フロントエンド サブネットに送信ルールはないので、トラフィックは許可されます。
3. バックエンド サブネットが、以下に示す受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
   2. NSG ルール 2 (RDP) は適用されず、次のルールに進みます。
   3. NSG ルール 3 (インターネットからファイアウォール) は該当しません。次のルールに進みます。
   4. NSG ルール 4 (IIS01 から AppVM01) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
4. AppVM01 が要求を受信し、ファイルを返します (アクセスが許可されている場合)。
5. バックエンド サブネットに送信ルールは存在しないので、応答は許可されます。
6. フロントエンド サブネットが、以下に示す受信ルールの処理を開始します。
   1. バックエンド サブネットからフロントエンド サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
   2. サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
7. IIS サーバーがファイルを受信します。

#### <a name="denied-web-direct-to-web-server"></a>(拒否) Web サーバーへの直接 Web アクセス
Web サーバー (IIS01) とファイアウォールは同じクラウド サービス内に置かれているため、外部に公開された同じ IP アドレスを共有しています。 つまり HTTP トラフィックはすべてファイアウォールにリダイレクトされます。 要求は正常に処理されますが、Web サーバーに直接到達しているわけではありません。必ずファイアウォールを経由して Web サーバーに到達します。 トラフィック フローについては、このセクションの 1 つ目のシナリオを参照してください。

#### <a name="denied-web-to-backend-server"></a>(拒否) Web からバックエンド サーバー
1. インターネット ユーザーが、BackEnd001.CloudApp.Net サービスを介して AppVM01 上のファイルにアクセスを試みます。
2. ファイル共有用に開放されたエンドポイントがないので、このアクセスはクラウド サービスを通過できず、サーバーに到達しません。
3. 何らかの理由でエンドポイントが開放されていたとしても、NSG ルール 5 (インターネットから VNet) によってこのトラフィックはブロックされます。

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(拒否) DNS サーバーに対する Web DNS 参照
1. インターネット ユーザーが、BackEnd001.CloudApp.Net サービスを介して DNS01 上の内部 DNS レコードを参照しようとしています。
2. DNS 用に開放されたエンドポイントがないので、このアクセスはクラウド サービスを通過できず、サーバーに到達しません。
3. 何らかの理由でエンドポイントが開放されていたとしても、NSG ルール 5 (インターネットから VNet) によってこのトラフィックはブロックされます (注: ルール 1 (DNS) は次の 2 つの理由で該当しません。1 つ目は発信元のアドレスがインターネットであるということ。このルールが該当するのは発信元としてのローカル VNet のみです。2 つ目は、Allow ルールでトラフィックが拒否されることは決してないということです)。

#### <a name="denied-web-to-sql-access-through-firewall"></a>(拒否) ファイアウォールを経由した Web から SQL へのアクセス
1. インターネット ユーザーが、FrontEnd001.CloudApp.Net (インターネットに接続されたクラウド サービス) にある SQL データを要求します。
2. SQL 用に開放されているエンドポイントがないので、このアクセスはクラウド サービスを通過できず、ファイアウォールに到達しません。
3. 何らかの理由でエンドポイントが開放されていた場合、フロントエンド サブネットは、受信ルールの処理を開始します。
   1. NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
   2. NSG ルール 2 (RDP) は適用されず、次のルールに進みます。
   3. NSG ルール 2 (インターネットからファイアウォール) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
4. トラフィックがファイアウォールの内部 IP アドレス (10.0.1.4) に到達します。
5. ファイアウォールは SQL 用の転送ルールを持たず、トラフィックを破棄します。

## <a name="conclusion"></a>まとめ
アプリケーションをファイアウォールで保護したり、バックエンド サブネットを受信トラフィックから切り離したりする方法として、これは比較的わかりやすい部類に入ります。

その他の例およびネットワーク セキュリティ境界の概要については、[こちら][HOME]を参照してください。

## <a name="references"></a>参照
### <a name="main-script-and-network-config"></a>主要なスクリプトとネットワーク構成
PowerShell スクリプト ファイルに完全なスクリプトを保存します。 "NetworkConf2.xml" という名前のファイルにネットワーク構成を保存します。
必要に応じて、ユーザー定義の変数を変更します。 スクリプトを実行し、前述のファイアウォール ルールのセットアップ手順に従ってください。

#### <a name="full-script"></a>完全なスクリプト
このスクリプトは、ユーザー定義の変数に基づいています。

1. Azure サブスクリプションに接続する
2. 新しいストレージ アカウントの作成
3. ネットワーク構成ファイルの定義に従って VNet を 1 つ、サブネットを 2 つ新規に作成する
4. Windows サーバーの VM を 4 つ作成する
5. NSG を構成する
   * NSG を作成
   * NSG にルールを設定
   * 適切なサブネットに NSG をバインド

この PowerShell スクリプトは、インターネットに接続されている PC またはサーバー上でローカルに実行する必要があります。

> [!IMPORTANT]
> このスクリプトを実行すると、PowerShell に警告またはその他の情報メッセージが表示される場合があります。 赤色のエラー メッセージのみが問題の原因となります。
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.

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

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

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

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
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

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
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
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
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
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
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

      # Add NSG Rules
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

        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>ネットワーク構成ファイル
この xml ファイルに実際のロケーション情報に反映して保存し、このファイルへのリンクを上記スクリプト内の $NetworkConfigFile 変数に追加します。

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

#### <a name="sample-application-scripts"></a>サンプル アプリケーション スクリプト
これに対応するサンプル アプリケーション、およびその他の DMZ の例をインストールしたい場合は、[サンプル アプリケーション スクリプト][SampleApp]をご利用ください。

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "NSG での受信 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "送信先 NAT アイコン"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "ファイアウォール ルール"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "ファイアウォール ルールのアクティブ化"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md



<!--HONumber=Dec16_HO2-->


