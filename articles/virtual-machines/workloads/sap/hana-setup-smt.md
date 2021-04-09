---
title: Azure (Large Instances) 上に SAP HANA の SMT サーバーを設定する方法 | Microsoft Docs
description: Azure (Large Instances) 上に SAP HANA の SMT サーバーを設定する方法。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 990274f34dac0234c2c2d521ca6a260f999671bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674516"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SUSE Linux 用 SMT サーバーの設定
SAP HANA の L インスタンスは、インターネットに直接接続していません。 このようなユニットをオペレーティング システム プロバイダーに登録し、更新プログラムをダウンロードして適用するのは、簡単なプロセスではありません。 SUSE Linux の場合の解決策は、Azure 仮想マシンに SMT サーバーを設定することです。 仮想マシンは、HANA L インスタンスに接続されている Azure 仮想ネットワークでホストする必要があります。 HANA L インスタンス ユニットは、このような SMT サーバーを使用して登録や更新プログラムのダウンロードを実行できます。 

SUSE に関するその他のドキュメントについては、[Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) に関する包括的なガイドを参照してください。 

HANA L インスタンスのタスクを実行する SMT サーバーのインストールの前提条件は、次のとおりです。

- HANA L インスタンスの ExpressRoute 回線に接続された Azure 仮想ネットワーク。
- 組織に関連付けられている SUSE アカウント。 組織には、有効な SUSE サブスクリプションが必要です。

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>SMT サーバーを Azure 仮想マシンにインストールする

まず、[SUSE Customer Center](https://scc.suse.com/) にサインインします。

**[Organization]\(組織)**  >  **[Organization Credentials]\(組織の資格情報)** の順に移動します。 このセクションで、SMT サーバーを設定するために必要な資格情報を確認します。

次に、Azure 仮想ネットワークに SUSE Linux VM をインストールします。 仮想マシンをデプロイするには、Azure の SLES 12 SP2 ギャラリー イメージを取得します (BYOS SUSE イメージを選択します)。 デプロイ プロセスでは、DNS 名を定義しないでください。また、静的 IP アドレスを使用しないでください。

![SMT サーバーの仮想マシンのデプロイのスクリーンショット](./media/hana-installation/image3_vm_deployment.png)

デプロイされた仮想マシンは小さく、Azure 仮想ネットワークの内部 IP アドレス (10.34.1.4) を取得しています。 仮想マシンの名前は *smtserver* です。 インストール後、HANA L インスタンス ユニットへの接続がチェックされます。 名前解決の構成方法によっては、Azure 仮想マシンの etc/hosts で HANA L インスタンス ユニットの解決を構成することが必要な場合があります。 

ディスクを仮想マシンに追加します。 このディスクを使用して更新プログラムを保持します。ブート ディスク自体が小さすぎる可能性もあります。 次のスクリーンショットに示すように、ここでは、/srv/www/htdocs にディスクがマウントされています。 100 GB のディスクで十分です。

![[PuTTy] ウィンドウに追加されたディスクを示すスクリーンショット。](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

HANA L インスタンス ユニットにサインインして /etc/hosts を維持し、SMT サーバーを実行することになっている Azure 仮想マシンにネットワーク経由で到達できるかどうかをチェックします。

このチェックの後、SMT サーバーを実行する Azure 仮想マシンにサインインします。 PuTTY を使用して仮想マシンにサインインする場合は、Bash ウィンドウで、次の一連のコマンドを実行します。

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Bash を再起動して、設定をアクティブにします。 次に YaST を起動します。

VM (smtserver) を SUSE サイトに接続します。

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

仮想マシンが SUSE サイトに接続されたら、smt パッケージをインストールします。 smt パッケージをインストールするには、次の putty コマンドを使用します。

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


また、YAST ツールを使用して smt パッケージをインストールすることもできます。 YAST で **[Software Maintenance]** に移動し、smt を検索します。 **smt** を選択すると、yast2-smt に自動的に切り替わります。

![YAST の SMT のスクリーンショット](./media/hana-installation/image5_smt_in_yast.PNG)


smtserver にインストールするための選択内容をそのまま使用します。 インストールが完了したら、SMT サーバー構成に移動します。 SUSE Customer Center で先ほど取得した組織の資格情報を入力します。 また、SMT サーバーの URL として、Azure 仮想マシンのホスト名を入力します。 このデモでは、これは https:\//smtserver です。

![SMT サーバー構成のスクリーンショット](./media/hana-installation/image6_configuration_of_smtserver1.png)

ここで、SUSE Customer Center への接続が機能しているかどうかをテストします。 次のスクリーンショットでおわかりのように、このデモでは接続は機能しています。

![SUSE Customer Center への接続のテストのスクリーンショット](./media/hana-installation/image7_test_connect.png)

SMT セットアップが開始されたら、データベースのパスワードを入力します。 これは新規インストールであるため、次の画像のように、パスワードを定義する必要があります。

![データベースのパスワードの定義のスクリーンショット](./media/hana-installation/image8_define_db_passwd.PNG)

次の手順に従って、証明書を作成します。

![SMT サーバーの証明書の作成のスクリーンショット](./media/hana-installation/image9_certificate_creation.PNG)

構成の最後の同期チェックの実行には、数分かかることがあります。 SMT サーバーのインストールと構成が完了すると、マウント ポイント /srv/www/htdocs/ の下に repo ディレクトリが作成されていることがわかります。 repo の下にはいくつかのサブディレクトリもあります。 

次のコマンドを使用して、SMT サーバーと関連サービスを再起動します。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>SMT サーバーにパッケージをダウンロードする

すべてのサービスが再起動したら、YAST を使用して SMT Management の適切なパッケージを選択します。 パッケージの選択は、HANA L インスタンス サーバーのオペレーティング システム イメージによって異なります。 パッケージの選択は、SMT サーバーを実行している仮想マシンの SLES リリースまたはバージョンには依存しません。 次のスクリーンショットは、この選択画面の例です。

![パッケージの選択のスクリーンショット](./media/hana-installation/image10_select_packages.PNG)

次に、設定した SMT サーバーへの選択したパッケージの初期コピーを開始します。 このコピーは、シェルで smt-mirror コマンドを使用することでトリガーされます。

![SMT サーバーへのパッケージのダウンロードのスクリーンショット](./media/hana-installation/image11_download_packages.PNG)

マウント ポイント /srv/www/htdocs の下に作成されたディレクトリにパッケージがコピーされます。 このプロセスは、選択したパッケージの数によっては 1 時間以上かかる場合があります。 このプロセスが完了したら、SMT クライアントの設定に移ります。 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA L インスタンス ユニットでの SMT クライアントの設定

この例のクライアントは、HANA L インスタンス ユニットです。 SMT サーバーのセットアップによって、clientSetup4SMT.sh スクリプトが Azure 仮想マシンにコピーされています。 SMT サーバーに接続する HANA L インスタンス ユニットにこのスクリプトをコピーします。 -h オプションを使用してスクリプトを開始し、SMT サーバーの名前をパラメーターとして渡します。 この例では、*smtserver* です。

![SMT クライアントの構成のスクリーンショット](./media/hana-installation/image12_configure_client.PNG)

クライアントによるサーバーからの証明書の読み込みは成功しても、次のスクリーンショットのように登録に失敗する場合があります。

![クライアントの登録の失敗のスクリーンショット](./media/hana-installation/image13_registration_failed.PNG)

登録に失敗した場合は、[SUSE サポート ドキュメント](https://www.suse.com/de-de/support/kb/doc/?id=7006024)を参照し、そこに記載されている手順を実行します。

> [!IMPORTANT] 
> サーバー名には、完全修飾ドメイン名を指定せずに、仮想マシンの名前 (この例では *smtserver*) を指定します。 

これらの手順の実行後、HANA L インスタンス ユニットで次のコマンドを実行します。

```
SUSEConnect –cleanup
```

> [!Note] 
> この手順の後、数分待ちます。 clientSetup4SMT.sh をすぐに実行すると、エラーが発生する可能性があります。

SUSE の記事の手順に基づいて修正する必要がある問題が発生した場合は、HANA L インスタンス ユニットで clientSetup4SMT.sh をもう一度開始する必要があります。 これで、正常に完了します。

![クライアントの登録の成功のスクリーンショット](./media/hana-installation/image14_finish_client_config.PNG)

Azure 仮想マシンにインストールした SMT サーバーに接続するように、HANA L インスタンス ユニットの SMT クライアントを構成しました。 "zypper up" または "zypper in" を使用して、HANA L インスタンスへのオペレーティング システム更新プログラムのインストールや、追加パッケージのインストールを実行できるようになりました。 取得できるのは、SMT サーバーで以前にダウンロードした更新プログラムだけです。

## <a name="next-steps"></a>次のステップ
- [HLI への HANA のインストール](hana-example-installation.md)。











