---
title: Azure VMware Solution by CloudSimple - プライベート クラウド上で vRealize Automation 用の vCenter を設定する
description: CloudSimple プライベート クラウド上で VMware vRealize Automation 用のエンドポイントとして VMware vCenter サーバーを設定する方法について説明します。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b6c6a320e6299808a91214476c8c0460f9f53d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895055"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>プライベート クラウド上で VMware vRealize Automation 用の vCenter を設定する

CloudSimple プライベート クラウド上で VMware vRealize Automation 用のエンドポイントとして VMware vCenter サーバーを設定することができます。

## <a name="before-you-begin"></a>開始する前に

vCenter サーバーを構成する前に、次のタスクを完了します。

* オンプレミス環境とプライベート クラウドの間の[サイト間 VPN 接続](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)を構成します。
* プライベート クラウドの DNS サーバーに対する[オンプレミスの DNS 要求の DNS 転送を構成します](on-premises-dns-setup.md)。
* [サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信して、次の表に記載されている一連のアクセス許可を持つ vRealize Automation IaaS 管理ユーザーを作成します。

| 属性の値 | 権限 |
------------ | ------------- |  
| データストア |  領域の割り当て <br> データストアの参照 |
| データストア クラスター | データストア クラスターの構成 |
| Folder | フォルダーを作成する <br>フォルダーの削除 |
| グローバル |  カスタム属性の管理<br>カスタム属性の設定 |
| ネットワーク | ネットワークの割り当て |
| アクセス許可 | アクセス許可の変更 |
| リソース | リソースプールへの VM の割り当て<br>電源がオフの仮想マシンの移行<br>電源がオンの仮想マシンの移行 |
| 仮想マシンのインベントリ |  既存からの作成<br>Create New<br>詳細ビュー<br>[削除] | 
| 仮想マシンの操作 |  CD メディアの構成<br>コンソールの操作<br>デバイスの接続<br>電源オフ<br>電源投入<br>Reset<br>[中断]<br>ツールのインストール | 
| 仮想マシンの構成 |  既存のディスクの追加<br>新しいディスクの追加<br>追加または削除<br>ディスクの削除<br>上級<br>CPU 数の変更<br>リソースの変更<br>仮想ディスクの拡張<br>ディスク変更追跡<br>メモリ<br>デバイスの設定の変更<br>名前の変更<br>注釈の設定 (バージョン 5.0 以降)<br>設定<br>スワップファイル配置 |
| プロビジョニング |  カスタマイズ<br>テンプレートの複製<br>仮想マシンの複製<br>テンプレートのデプロイ<br>カスタマイズ仕様の読み取り |
| 仮想マシンの状態 | スナップショットの作成<br>スナップショットの削除<br>スナップショットに戻す |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>オンプレミス環境への vRealize Automation のインストール

1. CloudSimple のサポートがお客様のために作成した IaaS 管理者として、vRealize Automation IaaS サーバー アプライアンスにサインインします。
2. vRealize Automation エンドポイント用の vSphere エージェントをデプロイします。
    1. https://*vra-url*:5480/installer にアクセスします。この *vra-url* は、vRealize Automation 管理 UI にアクセスするために使用する URL です。
    2. **[IaaS Installer]\(IaaS のインストーラー\)** をクリックしてインストーラーをダウンロードします。<br>
    インストーラー ファイルの名前付け規則は setup_ *vra-url*@5480.exe です。
    3. インストーラーを実行します。 [ようこそ] 画面で、 **[次へ]** をクリックします。
    4. EULA に同意し、**[Next]\(次へ\)** をクリックします。
    5. サインイン情報を入力し、**[Accept Certificate]\(証明書を受け入れる\)** をクリックして、**[Next]\(次へ\)** をクリックします。
    ![vRA の資格情報](media/configure-vra-endpoint-login.png)
    6. **[Custom Install]\(カスタム インストール\)** と **[Proxy Agents]\(プロキシ エージェント\)** を選択し、**[Next]\(次へ\)** をクリックします。
    ![vRA のインストールの種類](media/configure-vra-endpoint-install-type.png)
    7. IaaS サーバーのサインイン情報を入力し、**[Next]\(次へ\)** をクリックします。 Active Directory を使用している場合は、**ドメイン\ユーザー** の形式でユーザー名を入力します。 それ以外の場合は、 **user@domain** の形式を使用します。
    ![vRA のログイン情報](media/configure-vra-endpoint-account.png)
    8. プロキシ設定には、**[Agent type]\(エージェントの種類\)** として「**vSphere**」と入力します。 エージェントの名前を入力します。
    9. **[Manager Service Host]\(マネージャー サービス ホスト\)** フィールドと **[Model Manager Web Service Host]\(モデル マネージャー Web サービス ホスト\)** フィールドに IaaS サーバーの FQDN を入力します。 **[Test]\(テスト\)** をクリックして、各 FQDN 値の接続をテストします。 テストが失敗する場合は、IaaS サーバーのホスト名が解決されるように DNS 設定を変更します。
    10. プライベート クラウドの vCenter サーバー エンドポイントの名前を入力します。 名前は、後で構成プロセスで使用するのでメモします。

        ![vRA のインストールのプロキシ](media/configure-vra-endpoint-proxy.png)

    11. **[次へ]** をクリックします。
    12. **[インストール]** をクリックします。

## <a name="configure-the-vsphere-agent"></a>vSphere エージェントを構成する

1. https://*vra-url*/vcac にアクセスし、**ConfigurationAdmin** としてサインインします。
2. **[Infrastructure]\(インフラストラクチャ\)**  >  **[Endpoints]\(エンドポイント\)**  >  **[Endpoints]\(エンドポイント\)** を選択します。
3. **[New]\(新規\)**  >  **[Virtual]\(仮想\)**  >  **[vSphere]** を選択します。
4. 前の手順で指定した vSphere エンドポイントの名前を入力します。
5. **[Address]\(アドレス\)** に、https://*vcenter-fqdn*/sdk という形式で Private Cloud vCenter Server の URL を入力します。この *vcenter-fqdn* は vCenter サーバーの名前です。
6. CloudSimple のサポートがお客様のために作成した vRealize Automation IaaS 管理ユーザーの資格情報を入力します。
7. **[Test Connection]\(テスト接続\)** をクリックして、ユーザーの資格情報を確認します。 テストが失敗する場合は、URL、アカウント情報、および[エンドポイント名](#verify-the-endpoint-name)を確認し、もう一度テストします。
8. テストが正常に完了したら、**[OK]** をクリックして vSphere エンドポイントを作成します。
    ![vRA エンドポイント構成のアクセス](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>エンドポイント名を確認する

正しい vCenter サーバー エンドポイント名を特定するには、次の手順を行います。

1. IaaS アプライアンス上でコマンド プロンプトを開きます。
2. ディレクトリを C:\Program Files (x86)\VMware\vCAC\Agents\agent-name に変更します。この *agent-name* は vCenter サーバー エンドポイントにお客様が割り当てた名前です。
3. 次のコマンドを実行します。

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

次のように出力されます。 `managementEndpointName` フィールドの値は、エンドポイント名です。

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
