---
title: "Linux 仮想マシンで Apache Tomcat をセットアップする | Microsoft Docs"
description: "Linux を実行している Azure 仮想マシンを使用して Apache Tomcat7 をセットアップする方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: fa30c78a5a5d458ba8845c3c10b87538427786c9
ms.lasthandoff: 03/27/2017


---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Azure で Linux 仮想マシンに Tomcat7 をセットアップする
Apache Tomcat (または単に Tomcat、旧称は Jakarta Tomcat) は、Apache Software Foundation (ASF) によって開発されたオープン ソース Web サーバーであり、サーブレット コンテナーです。 Tomcat には、Sun Microsystems の Java Servlet と JavaServer Pages (JSP) 仕様が実装されています。 Tomcat は、Java コードを実行する純粋な Java HTTP Web サーバー環境を提供します。 最も単純な構成では Tomcat は単一のオペレーティング システムのプロセスで実行されます。 このプロセスは、Java 仮想マシン (JVM) を実行します。 ブラウザーからの Tomcat に対するすべての HTTP 要求は、Tomcat プロセスで個別のスレッドとして処理されます。  

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイメント モデルの使用方法について説明します。 ほとんどの新しいデプロイでは、Resource Manager モデルを使用することをお勧めします。 Open JDK と Tomcat を使用して Ubuntu VM をデプロイするための Resource Manager テンプレートを使用するには、[こちらの記事](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/)を参照してください。

この記事では、Linux イメージ上に Tomcat7 をインストールして、Azure にデプロイします。  

学習内容:  

* Azure 上での仮想マシンの作成方法。
* Tomcat7 の仮想マシンを準備する方法。
* Tomcat7 をインストールする方法。

ここでは、既に Azure サブスクリプションを持っていることを前提としています。  持っていない場合は、[Azure Web サイト](https://azure.microsoft.com/)で無料試用版にサインアップできます。 MSDN サブスクリプションを持っている場合は、[Microsoft Azure の特別価格 (MSDN、MPN、BizSpark の特典)](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39) に関するページを参照してください。 Azure の詳細については、「 [Azure とは](https://azure.microsoft.com/overview/what-is-azure/)」を参照してください。

この記事は、Tomcat と Linux の基本的な実務知識を持つ読者を想定しています。  

## <a name="phase-1-create-an-image"></a>フェーズ 1: イメージを作成する
このフェーズでは、Azure の Linux イメージを使用して仮想マシンを作成します。  

### <a name="step-1-generate-an-ssh-authentication-key"></a>手順 1. SSH 認証キーを生成する
SSH はシステム管理者にとって重要なツールです。 ただし、人間が指定したパスワードに基づいたアクセス セキュリティを構成することはお勧めしません。 悪意のあるユーザーは、ユーザー名と脆弱なパスワードに基づいたシステムに侵入することができます。

リモート アクセスを開いたままにして、パスワードについて心配しなくてすむ方法があります。 この方法は、非対称暗号化方式による認証で構成されています。 ユーザーの秘密キーが認証を付与するキーとなります。 ユーザーのアカウントをロックして、パスワード認証を無効にすることもできます。

この方法のもう 1 つの利点は、サインインするサーバーごとに異なるパスワードが必要ないことです。 すべてのサーバーで個人の秘密キーを使用して認証できるため、パスワードをいくつも覚えておく必要がありません。



次の手順に従って、SSH 認証のキーを生成します。

1. 次の場所から PuTTYgen をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Puttygen.exe を実行します。
3. **[生成]** をクリックしてキーを生成します。 処理中にウィンドウの空白の領域にマウスを移動すると、ランダム性を高めることができます。  
   ![新しいキーの生成ボタンが表示されている PuTTY Key Generator のスクリーンショット][1]
4. 生成処理が終わると、新しい公開キーが表示されます。  
   ![新しい公開キーと [Save private key (秘密キーの保存)] ボタンが表示されている PuTTY Key Generator のスクリーンショット][2]
5. 公開キーを選択してコピーし、publicKey.pem という名前のファイルに保存します。 保存された公開キーの形式は使用する公開キーの形式と異なるため、 **[公開キーの保存]**をクリックしないでください。
6. **[Save private key (秘密キーの保存)]** をクリックし、privateKey.ppk という名前のファイルに保存します。

### <a name="step-2-create-the-image-in-the-azure-portal"></a>手順 2. Azure Portal でイメージを作成する
1. [ポータル](https://portal.azure.com/)で、タスク バーの **[新規]** をクリックしてイメージを作成します。 ニーズに基づいた Linux イメージを選択します。 次の例では、Ubuntu 14.04 イメージを使用します。
![[新規] ボタンが表示されているポータルのスクリーンショット][3]

2. **[ホスト名]** に、ユーザーとインターネット クライアントがこの仮想マシンへのアクセスに使用する URL の名前を指定します。 DNS 名の最後の部分 (tomcatdemo など) を定義すると、 tomcatdemo.cloudapp.net として URL が生成されます。  

3. **[SSH Authentication Key (SSH 認証キー)]** に、publicKey.pem ファイルのキーの値をコピーします。このファイルには、PuTTYgen によって生成された公開キーが含まれています。  
![ポータルの [SSH Authentication Key (SSH 認証キー)] ボックス][4]

4. 必要に応じて他の設定を構成し、**[作成]** をクリックします。  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>フェーズ 2: Tomcat7 の仮想マシンを準備する
このフェーズでは、Tomcat トラフィックのエンドポイントを構成し、新しい仮想マシンに接続します。

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>手順 1. Web アクセスを許可する HTTP ポートを開く
Azure のエンドポイントは、TCP または UDP プロトコルのほか、パブリック ポートとプライベート ポートで構成されます。 プライベート ポートとは、サービスが仮想マシンをリッスンするポートです。 パブリック ポートとは、Azure クラウド サービスがインターネットベースの受信トラフィックを外部でリッスンするポートです。  

TCP ポート 8080 は、Tomcat がリッスンに使用する既定のポート番号です。 このポートを Azure エンドポイントで開くと、ユーザーやその他のインターネット クライアントは Tomcat のページにアクセスできます。  

1. ポータルで、**[参照]** > **[仮想マシン]** の順にクリックし、作成した仮想マシンをクリックします。  
   ![仮想マシン ディレクトリのスクリーンショット][5]
2. 仮想マシンにエンドポイントを追加するには、 **[エンドポイント]** ボックスをクリックします。
   ![[エンドポイント] ボックスのスクリーンショット][6]
3. **[追加]**をクリックします。  

   1. エンドポイントについては、**[エンドポイント]** にエンドポイントの名前を入力し、**[パブリック ポート]** に「80」と入力します。  

      80 に設定した場合は、Tomcat にアクセスするための URL にポート番号を含める必要はありません。 たとえば、http://tomcatdemo.cloudapp.net のようになります。    

      別の値 (81 など) に設定した場合は、Tomcat にアクセスするための URL にポート番号を追加する必要があります。 たとえば、http://tomcatdemo.cloudapp.net:81/。
   2. **[プライベート ポート]** に「8080」と入力します。 既定では、Tomcat は TCP ポート 8080 でリッスンします。 Tomcat の既定のリッスン ポートを変更した場合は、Tomcat のリッスン ポートと同じになるように **[プライベート ポート]** を更新する必要があります。  
      ![[追加] コマンド、[パブリック ポート]、[プライベート ポート] が表示されている UI のスクリーンショット][7]
4. **[OK]** をクリックして、仮想マシンにエンドポイントを追加します。

### <a name="step-2-connect-to-the-image-you-created"></a>手順 2. 作成したイメージに接続する
SSH ツールを選択すると、仮想マシンに接続できます。 この例では、PuTTY を使用します。  

1. ポータルから、仮想マシンの DNS 名を取得します。
    1. **[参照]** > **[仮想マシン]** の順にクリックします。
    2. 仮想マシンの名前を選択して、**[プロパティ]** をクリックします。
    3. **[プロパティ]** タイルで、**[ドメイン名]** ボックスを探して DNS 名を取得します。  

2. **[SSH]** ボックスから、SSH 接続のポート番号を取得します。  
![SSH 接続のポート番号を示すスクリーンショット][8]

3. [PuTTY](http://www.putty.org/) をダウンロードします。  

4. ダウンロード後、実行可能ファイル Putty.exe をクリックします。 PuTTY の構成で、仮想マシンのプロパティから取得したホスト名とポート番号を使用して、基本オプションを構成します。   
![PuTTY の構成のホスト名とポートのオプションを示すスクリーンショット][9]

5. 左側のウィンドウで、**[Connection (接続)]** > **[SSH]** > **[Auth (認証)]** の順にクリックし、**[Browse (参照)]** をクリックして privateKey.ppk ファイルの場所を指定します。 privateKey.ppk ファイルには、この記事の「フェーズ 1: イメージを作成する」セクションで PuTTYgen によって生成された秘密キーが含まれています。  
![[Connection (接続)] ディレクトリ階層と [Browse (参照)] ボタンを示すスクリーンショット][10]

6. **[開く]**をクリックします。 メッセージ ボックスによるアラートが表示される場合があります。 DNS 名とポート番号を正しく構成してある場合は、 **[はい]**をクリックします。
![通知のスクリーンショット][11]

7. ユーザー名を入力するように求められます。  
![ユーザー名の入力場所を示すスクリーンショット][12]

8. この記事の「フェーズ 1: イメージを作成する」セクションで仮想マシンを作成するときに使用したユーザー名を入力します。 次のように表示されます。  
![認証の確認を示すスクリーンショット][13]

## <a name="phase-3-install-software"></a>フェーズ 3: ソフトウェアをインストールする
このフェーズでは、Java ランタイム環境、Tomcat7、その他の Tomcat7 コンポーネントをインストールします。  

### <a name="java-runtime-environment"></a>Java ランタイム環境
Tomcat は Java で記述します。 Java 開発キット (JDK) には、OpenJDK と Oracle JDK の 2 種類があります。 お好みの開発キットを選択できます。  

> [!NOTE]
> どちらの JDK でも Java API のクラスのコードはほぼ同じですが、仮想マシンのコードは異なります。 OpenJDK はオープン ライブラリを使用する傾向があるのに対し、Oracle JDK はクローズ ライブラリを使用する傾向があります。 Oracle JDK の方がクラスが多く、いくつかのバグが修正されていて、OpenJDK よりも安定しています。

#### <a name="install-openjdk"></a>OpenJDK をインストールする  

次のコマンドを使用して、OpenJDK をダウンロードします。   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* JDK のファイルを格納するディレクトリを作成するには:  

        sudo mkdir /usr/lib/jvm  
* JDK のファイルを /usr/lib/jvm/ ディレクトリに抽出するには:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Oracle JDK をインストールする


次のコマンドを使用して、Oracle の Web サイトから Oracle JDK をダウンロードします。  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* JDK のファイルを格納するディレクトリを作成するには:  

        sudo mkdir /usr/lib/jvm  
* JDK のファイルを /usr/lib/jvm/ ディレクトリに抽出するには:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Oracle JDK を既定の Java 仮想マシンとして設定するには:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Java のインストールが成功したことを確認する
次のようなコマンドを使用して Java ランタイム環境が正しくインストールされているかどうかをテストすることができます。  

    java -version  

OpenJDK をインストールすると、次のようなメッセージが表示されます。![OpenJDK のインストール成功のメッセージ][14]

Oracle JDK をインストールすると、次のようなメッセージが表示されます。![Oracle JDK のインストール成功のメッセージ][15]

### <a name="install-tomcat7"></a>Tomcat7 をインストールする
次のコマンドを使用して、Tomcat7 をインストールします。  

    sudo apt-get install tomcat7  

Tomcat7 を使用しない場合は、このコマンドを適宜変更して使用してください。  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Tomcat7 のインストールが成功したことを確認する
Tomcat7 が正常にインストールされたかどうかを確認するには、Tomcat サーバーの DNS 名を参照します。 この記事では、URL の例は http://tomcatexample.cloudapp.net/ です。 次のようなメッセージが表示された場合は、Tomcat7 は正常にインストールされています。
![Tomcat7 のインストール成功のメッセージ][16]

### <a name="install-other-tomcat7-components"></a>その他の Tomcat7 コンポーネントをインストールする
その他のオプションの Tomcat コンポーネントをインストールすることもできます。  

**sudo apt-cache search tomcat7** コマンドを使用すると、使用可能なすべてのコンポーネントが表示されます。 次のコマンドを使用して、便利なコンポーネントをいくつかインストールします。  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>フェーズ 4: Tomcat7 を構成する
このフェーズでは、Tomcat を管理します。

### <a name="start-and-stop-tomcat7"></a>Tomcat7 を起動および停止する
Tomcat7 サーバーは、インストールすると自動的に起動します。 次のコマンドで起動することもできます。   

    sudo /etc/init.d/tomcat7 start

Tomcat7 を停止するには:

    sudo /etc/init.d/tomcat7 stop

Tomcat7 の状態を表示するには:

    sudo /etc/init.d/tomcat7 status

Tomcat サービスを再起動するには: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Tomcat7 の管理
Tomcat のユーザー構成ファイルを編集して、管理者の資格情報を設定できます。 次のコマンドを使用します。  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

たとえば次のようになります。  
!["sudo vi" コマンドの出力を示すスクリーンショット][17]  

> [!NOTE]
> 管理者のユーザー名に強力なパスワードを作成します。  

このファイルの編集後に、次のコマンドで Tomcat7 サービスを再起動し、変更が適用されていることを確認する必要があります。  

    sudo /etc/init.d/tomcat7 restart  

ブラウザーを開き、URL として「**http://<your tomcat server DNS name>/manager/html**」と入力します。 たとえば、この記事では、URL は http://tomcatexample.cloudapp.net/manager/html. です。  

接続後に、次のようなものが表示されます。  
![Tomcat Web Application Manager のスクリーンショット][18]

## <a name="common-issues"></a>一般的な問題
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Tomcat や Moodle を使用してインターネットから仮想マシンにアクセスできない。
#### <a name="symptom"></a>症状  
  Tomcat は実行されているが、ブラウザーに Tomcat の既定のページが表示されない。
#### <a name="possible-root-cause"></a>考えられる根本原因   

  * Tomcat のリッスン ポートが、仮想マシンの Tomcat トラフィック用エンドポイントのプライベート ポートと異なっている。  

     パブリック ポートとプライベート ポートのエンドポイント設定を確認し、プライベート ポートが Tomcat のリッスン ポートと同じになるようにします。 仮想マシンのエンドポイントを構成する手順については、この記事の「フェーズ 1: イメージを作成する」セクションを参照してください。  

     Tomcat のリッスン ポートを確認するには、/etc/httpd/conf/httpd.conf (Red Hat リリース) または /etc/tomcat7/server.xml (Debian リリース) を開きます。 既定では、Tomcat のリッスン ポートは 8080 です。 たとえば次のようになります。  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Debian や Ubuntu などの仮想マシンを使用していて、Tomcat の既定のリッスン ポートを変更する (8081 など) 場合は、オペレーティング システムのポートも開く必要があります。 最初に、プロファイルを開きます。  

        sudo vi /etc/default/tomcat7  

     最後の行のコメントを解除し、"no" を "yes" に変更します。  

        AUTHBIND=yes
  2. ファイアウォールによって Tomcat のリッスン ポートが無効になっている。

     Tomcat の既定のページは、ローカル ホストからしか表示できません。 これは、Tomcat がリッスンしているポートがファイアウォールによってブロックされていることが問題である可能性があります。 w3m ツールを使用すると、Web ページを参照できます。 次のコマンドは w3m をインストールして、Tomcat の既定のページを参照します。  


        sudo yum  install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>解決策

  * Tomcat のリッスン ポートが、仮想マシンのトラフィック用のエンドポイントのプライベート ポートと異なっている場合は、Tomcat のリッスン ポートと同じになるようにプライベート ポートを変更する必要があります。   
  2. 問題の原因がファイアウォールや iptables の場合は、/etc/sysconfig/iptables に次の行を追加します。 2 つ目の行は、https トラフィックの場合にのみ必要です。  

      -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT

      -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

     > [!IMPORTANT]
     > 前述の行は、次のように、グローバルにアクセスを制限する行の上に配置するようにしてください。-A INPUT -j REJECT --reject-with icmp-host-prohibited



iptables を再読込するには、次のコマンドを実行します。

    service iptables restart

このテストは CentOS 6.3 で実行したものです。

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>/var/lib/tomcat7/webapps/ にプロジェクト ファイルをアップロードしようとするとアクセスが拒否される
#### <a name="symptom"></a>症状
  SFTP クライアント (FileZilla など) を使用して仮想マシンに接続し、/var/lib/tomcat7/webapps/ に移動してサイトを発行しようとすると、次のようなエラー メッセージが表示される。  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>考えられる根本原因
  /var/lib/tomcat7/webapps フォルダーへのアクセス許可が付与されていない。  
#### <a name="solution"></a>解決策  
  root アカウントからアクセス許可を取得する必要があります。 そのフォルダーの所有権を、root から、マシンをプロビジョニングしたときに使用したユーザー名に変更できます。 次は、azureuser のアカウント名の例です。  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  ディレクトリ内のすべてのファイルにアクセス許可を適用するには、-R オプションを使用します。  

  このコマンドはディレクトリにも使用できます。 -R オプションは、ディレクトリ内のすべてのファイルとディレクトリに対するアクセス許可を変更します。 たとえば次のようになります。  

     sudo chown -R username:group directory  

  このコマンドは、ディレクトリ内のすべてのファイルとディレクトリの所有権 (ユーザーとグループの両方) を変更します。  

  次のコマンドは、フォルダー ディレクトリのアクセス許可のみを変更します。 ディレクトリ内のファイルとフォルダーは変更されません。  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png

