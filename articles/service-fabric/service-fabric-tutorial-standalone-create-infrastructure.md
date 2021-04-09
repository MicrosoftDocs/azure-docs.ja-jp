---
title: AWS にクラスター用のインフラストラクチャを作成する
description: このチュートリアルでは、Service Fabric クラスターを実行するための AWS インフラストラクチャをセットアップする方法を学習します。
ms.topic: tutorial
ms.date: 05/11/2018
ms.custom: mvc
ms.openlocfilehash: c7a18b0dcdc04bdf66ac4b36ce7376ee018eb238
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842905"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>チュートリアル: Service Fabric クラスターをホストする AWS インフラストラクチャを作成する

Service Fabric で採用されている "すべての OS、すべてのクラウド" のアプローチの一環として、Service Fabric スタンドアロン クラスターには、独自の環境を選んでクラスターを作成する選択肢が用意されています。 このチュートリアル シリーズでは、AWS をホストとするスタンドアロン クラスターを作成し、そこにアプリケーションをインストールします。

このチュートリアルは、シリーズの第 1 部です。 この記事では、Service Fabric のスタンドアロン クラスターをホストするために必要な AWS リソースを生成します。 後続の記事では、Service Fabric スタンドアロン スイートをインストールし、クラスターにサンプル アプリケーションをインストールして、最後にクラスターをクリーンアップする必要があります。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * EC2 インスタンスのセットを作成する
> * セキュリティ グループを変更する
> * いずれかのインスタンスにサインインする
> * Service Fabric 用にインスタンスを準備する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、AWS アカウントが必要です。  まだアカウントを持っていない場合は、[AWS コンソール](https://aws.amazon.com/)に移動して、アカウントを作成します。

## <a name="create-ec2-instances"></a>EC2 インスタンスを作成する

AWS コンソールにサインインし、検索ボックスに「**EC2**」と入力して、**[EC2 Virtual Servers in the Cloud]\(クラウドの EC2 仮想サーバー\)** を選択します。

![AWS コンソールの検索][aws-console]

**[Launch Instance]\(インスタンスの起動\)** を選択し、次の画面で Microsoft Windows Server 2016 Base の横の **[Select]\(選択\)** を選択します。

![EC2 インスタンスの選択][aws-ec2instance]

**[t2.medium]** を選択し、**[Next: Configure Instance Details]\(次: インスタンスの詳細を構成する\)** を選択します。次の画面でインスタンスの数を `3` に変更し、**[Advanced Details]\(高度な詳細\)** を選択してそのセクションを展開します。

Service Fabric で仮想マシンも一緒に接続するには、インフラストラクチャをホストしている各 VM が同じ資格情報を持っている必要があります。  資格情報を一致させるには、2 つの一般的な方法があります。すべての仮想マシンを同じドメインに参加させる方法と、各 VM で同じ管理者パスワードを設定する方法です。  このチュートリアルでは、ユーザー データ スクリプトを使用して、すべての EC2 インスタンスが同じパスワードを持つように設定します。  運用環境では、ホストを Windows ドメインに参加させる方が安全です。

コンソールのユーザー データ フィールドに、次のスクリプトを入力します。

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

PowerShell スクリプトを入力したら、**[Review and Launch]\(確認と起動\)** を選択します。

![EC2 の確認と起動][aws-ec2configure2]

確認画面で、**[Launch]\(起動\)** を選択します。  次に、ドロップダウンで **[Proceed without a key pair]\(キー ペアなしで続行\)** を選択し、パスワードを知っていることを示すチェック ボックスをオンにします。

![AWS のキー ペアの選択][aws-keypair]

最後に、**[Launch Instances]\(インスタンスの起動\)** を選択し、**[View Instances]\(インスタンスの表示\)** を選択します。  Service Fabric クラスターの基礎が作成されました。次に、Service Fabric 構成用の準備をするために、いくつかの最終的な構成をインスタンス自体に追加する必要があります。

## <a name="modify-the-security-group"></a>セキュリティ グループを変更する

Service Fabric では、クラスター内のホスト間で多数のポートを開く必要があります。 AWS インフラストラクチャでこれらのポートを開くには、作成したインスタンスの 1 つを選択します。 次に、セキュリティ グループの名前を選択します (例: **launch-wizard-1**)。 ここで、**[Inbound]\(受信\)** タブを選択します。

これらのポートが世界中に対して開かれないようにするために、同じセキュリティ グループ内のホストだけに対してポートを開きます。 セキュリティ グループ ID を書き留めておきます。この例では、**sg-c4fb1eba** です。  次に、 **[編集]** を選択します。

次に、サービスの依存関係のためにセキュリティ グループに 4 つの規則を追加し、Service Fabric 自体のためにさらに 3 つの規則を追加します。 最初の規則は、基本的な接続チェックのために、ICMP トラフィックを許可します。 他の規則は、SMB とリモート レジストリを有効にするために必要なポートを開きます。

最初の規則のために、**[Add Rule]\(規則の追加\)** を選択し、ドロップダウン メニューの **[All ICMP - IPv4]\(すべての ICMP - IPv4\)** を選択します。 カスタムの横にある入力ボックスを選択し、上のセキュリティ グループ ID を入力します。

後の 3 つの依存関係についても、同様の操作を行う必要があります。  **[Add Rule]\(規則の追加\)** を選択し、ドロップダウンの **[Custom TCP Rule]\(カスタム TCP 規則\)** を選択します。ポートの範囲は、各規則に対して「`135`」、「`137-139`」、「`445`」のいずれかを入力します。 最後に、ソースのボックスで、セキュリティ グループ ID を入力します。

![セキュリティ グループのポート][aws-ec2securityports]

依存関係のポートが開いたので、Service Fabric 自体が通信に使用するポートについても同じ操作を行う必要があります。 **[Add Rule]\(規則の追加\)** を選択し、ドロップダウンの **[Custom TCP Rule]\(カスタム TCP 規則\)** を選択します。ポートの範囲には「`20001-20031`」と入力し、ソースのボックスにはセキュリティ グループを入力します。

次に、一時的なポート範囲の規則を追加します。  **[Add Rule]\(規則の追加\)** を選択し、ドロップダウンの **[Custom TCP Rule]\(カスタム TCP 規則\)** を選択します。ポートの範囲には、「`20606-20861`」と入力します。 最後に、ソースのボックスで、セキュリティ グループ ID を入力します。

Service Fabric の最後の 2 つの規則については、世界中に対して開いて、パーソナル コンピューターから Service Fabric クラスターを管理できるようにします。 **[Add Rule]\(規則の追加\)** を選択し、ドロップダウンの **[Custom TCP Rule]\(カスタム TCP 規則\)** を選択します。ポートの範囲には「`19000-19003`」または「`19080-19081`」と入力し、[Source]\(ソース\) ドロップダウンを [Anywhere]\(指定なし\) に変更します。

最後に、アプリケーションがデプロイされたときに表示されるように、ポート 8080 を開く必要があります。 **[Add Rule]\(規則の追加\)** を選択し、ドロップダウンの **[Custom TCP Rule]\(カスタム TCP 規則\)** を選択します。ポートの範囲には「`8080`」と入力し、[Source]\(ソース\) ドロップダウンを [Anywhere]\(指定なし\) に変更します。

すべての規則を入力し終わりました。 **[保存]** を選択します。

## <a name="connect-to-an-instance-and-validate-connectivity"></a>インスタンスに接続して接続を検証する

セキュリティ グループのタブで、左側のメニューの **[Instances]\(インスタンス\)** を選択します。  作成した各インスタンスを選択し、プライベート IP アドレスを書き留めておきます。以下の例では `172.31.21.141` と `172.31.20.163` です。

すべての IP アドレスを書き留めたら、接続するインスタンスを 1 つ選択し、インスタンスを右クリックして **[Connect]\(接続\)** を選択します。  ここで、この特定のインスタンス用の RDP ファイルをダウンロードすることができます。  **[Download Remote Desktop File]\(リモート デスクトップ ファイルのダウンロード\)** を選択し、ダウンロードされたファイルを開いて、このインスタンスへのリモート デスクトップ接続 (RDP) を確立します。  パスワードの入力を求めるメッセージが表示されたら、「`serv1ceF@bricP@ssword`」と入力します。

![リモート デスクトップ ファイルをダウンロードする][aws-rdp]

インスタンスに正常に接続したら、それらを接続してファイルを共有できることを確認します。  すべてのインスタンスの IP アドレスを収集してあるので、現在接続していないインスタンスを選択します。 **[スタート]** で「`cmd`」と入力し、**[コマンド プロンプト]** を選択します。

これらの例では、172.31.21.141 という IP アドレスへの RDP 接続が確立されました。 次に、すべての接続テストが他の IP アドレス (172.31.20.163) に対して行われます。

基本的な接続が機能することを検証するには、ping コマンドを使用します。

```
ping 172.31.20.163
```

`Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` のような出力が 4 回繰り返された場合、インスタンス間の接続は機能しています。  次に、以下のコマンドを使用して、SMB 共有が機能することを検証します。

```
net use * \\172.31.20.163\c$
```

出力として、`Drive Z: is now connected to \\172.31.20.163\c$.` が返されるはずです。

## <a name="prep-instances-for-service-fabric"></a>Service Fabric 用にインスタンスを準備する

これをゼロから作成していたならば、さらにいくつかの手順を行う必要があったでしょう。  つまり、リモート レジストリが実行されていることを検証し、SMB を有効にし、SMB およびリモート レジストリに必要なポートを開かなければなりませんでした。

作業を簡単にするために、ユーザー データ スクリプトでインスタンスをブートストラップしたときに、このすべての作業を埋め込みました。

SMB を有効にするために使用した PowerShell コマンドは、次のとおりです。

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

ファイアウォールでポートを開くための PowerShell コマンドは、次のとおりです。

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>次のステップ

シリーズのパート 1 では、3 つの EC2 インスタンスを起動し、Service Fabric インストール用に構成する方法について学習しました。

> [!div class="checklist"]
> * EC2 インスタンスのセットを作成する
> * セキュリティ グループを変更する
> * いずれかのインスタンスにサインインする
> * Service Fabric 用にインスタンスを準備する

クラスターの Service Fabric を構成するには、シリーズのパート 2 に進んでください。

> [!div class="nextstepaction"]
> [Service Fabric をインストールする](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png