---
title: チュートリアル - Azure Traffic Manager を使用して Web サイトの応答性を向上させる
description: このチュートリアル記事では、応答性の高い Web サイトを構築するための Traffic Manager プロファイルの作成方法について説明します。
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: d8262a80fac42f103d571523c75c5064d5d43949
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003822"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>チュートリアル:Traffic Manager を使用して Web サイトの応答性を向上させる

このチュートリアルでは、Traffic Manager を使用して、待機時間が最も短い Web サイトにユーザー トラフィックを転送することによって、応答性の高い Web サイトを作成する方法について説明します。 通常、待機時間が最も短いデータセンターは、地理的に最も近いデータセンターです。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * IIS で基本的な Web サイトを実行する 2 台の VM を作成する
> * 動作中の Traffic Manager を表示するための 2 台のテスト VM を作成する
> * IIS を実行する VM の DNS 名を構成する
> * Web サイトのパフォーマンスを向上させるための Traffic Manager プロファイルを作成する
> * Traffic Manager プロファイルに VM エンドポイントを追加する
> * Traffic Manager の動作確認

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Traffic Manager の動作を確認するために、以下をデプロイする必要があります。

- 異なる Azure リージョン (**米国東部** と **西ヨーロッパ**) で運営される基本的な Web サイトの 2 つのインスタンス。
- Traffic Manager をテストするための 2 台の VM (1 台目の VM は **米国東部** にあり、2 台目の VM は **西ヨーロッパ** にある)。 テスト VM は、同じリージョン内で実行中の Web サイトが最も短い待機時間になるときに、Traffic Manager がその Web サイトにユーザー トラフィックをルーティングすることを示するために使用されます。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

### <a name="create-websites"></a>Web サイトを作成する

このセクションでは、2 つの Azure リージョン内に Traffic Manager プロファイル用の 2 つのサービス エンドポイントを提供する 2 つの Web サイト インスタンスを作成します。 2 つの Web サイトの作成には、次の手順が含まれます。

1. 基本的な Web サイトを実行する 2 台の VM を作成します (1 台は **米国東部** 内、他方は **西ヨーロッパ** 内)。
2. 各 VM に IIS サーバーをインストールし、ユーザーが この Web サイトにアクセスした場合に接続される VM の名前を記述する既定の Web サイト ページを更新します。

#### <a name="create-vms-for-running-websites"></a>Web サイトを実行するための VM を作成する

このセクションでは、**米国東部** と **西ヨーロッパ** の 2 つの Azure リージョンに 2 台の VM (*myIISVMEastUS* と *myIISVMWestEurope*) を作成します。

1. Azure portal の左上隅で、 **[リソースの作成]**  >  **[Compute]**  >  **[Windows Server 2019 Datacenter]** の順に選択します。
2. **[仮想マシンの作成]** の **[Basic]** タブに次の値を入力するか選択します。

   - **[サブスクリプション]**  >  **[リソース グループ]** : **[新規作成]** を選択し、「**myResourceGroupTM1**」と入力します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「*myIISVMEastUS*」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** : **[米国東部]** を選択します。
   - **[管理者アカウント]**  >  **[ユーザー名]** : 任意のユーザー名を入力します。
   - **[管理者アカウント]**  >  **[パスワード]** : 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。
   - **[受信ポートの規則]**  >  **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **[受信ポートの規則]**  >  **[受信ポートの選択]** : プルダウン ボックスから **[RDP]** と **[HTTP]** を選択します。

3. **[管理]** タブを選択します。または、 **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択し、 **[次へ: 管理]** を選択します。 **[監視]** で **[ブート診断]** を **[オフ]** に設定します。
4. **[Review + create]\(レビュー + 作成\)** を選択します。
5. 設定を確認し、 **[作成]** をクリックします。  
6. 手順に従って、*myIISVMWestEurope* という名前の 2 番目の VM を作成します。 **[リソース グループ]** の名前は *myResourceGroupTM2*、 **[場所]** は "*西ヨーロッパ*"、他のすべての設定は、*myIISVMEastUS* と同じにします。
7. VM の作成には数分かかります。 両方の VM の作成が完了するまで、以降の手順に進まないでください。

   ![VM の作成](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS をインストールして既定の Web ページをカスタマイズする

このセクションでは、2 台の VM (*myIISVMEastUS* と *myIISVMWestEurope*) に IIS サーバーをインストールした後、既定の Web サイト ページを更新します。 カスタマイズする Web サイト ページには、Web ブラウザーからその Web サイトにアクセスするときに接続する VM の名前が表示されます。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *myResourceGroupTM1* リソース グループにある *myIISVMEastUS* をクリックします。
2. **[概要]** ページで **[接続]** **[仮想マシンへの接続]** の順にクリックし、 **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
6. サーバーのデスクトップで、 **[Windows 管理ツール]** > **[サーバー マネージャー]** の順に移動します。
7. VM1 で Windows PowerShell を起動し、次のコマンドを使用して IIS サーバーのインストールと既定の htm ファイルの更新を行います。

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![IIS のインストールと Web ページのカスタマイズ](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. *myIISVMEastUS* との RDP 接続を閉じます。
9. *myResourceGroupTM2* リソース グループ内の VM (*myIISVMWestEurope*) への RDP 接続を作成し、手順 1 から 8 を繰り返して、IIS のインストールと既定の Web ページのカスタマイズを行います。

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS を実行する VM の DNS 名を構成する

Traffic Manager は、サービス エンドポイントの DNS 名に基づいてユーザー トラフィックをルーティングします。 このセクションでは、IIS サーバー *myIISVMEastUS* と *myIISVMWestEurope* の DNS 名を構成します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *myResourceGroupTM1* リソース グループにある *myIISVMEastUS* を選択します。
2. **[概要]** ページで、 **[DNS 名]** の下の **[構成]** を選択します。
3. **[構成]** ページで、[DNS 名ラベル] の下に一意の名前を追加した後、 **[保存]** を選択します。
4. *myResourceGroupTM2* リソース グループにある *myIISVMWestEurope* という名前の VM に対して、手順 1. から 3. を繰り返します。

### <a name="create-test-vms"></a>テスト VM を作成する

このセクションでは、VM (*myVMEastUS* と *myVMWestEurope*) を、該当する Azure リージョン (**米国東部** と **西ヨーロッパ**) 内に作成します。 これらの VM は、Web サイトが参照されるときに、Traffic Manager が最も近い IIS サーバーにトラフィックをルーティングすることをテストするために使用します。

1. Azure portal の左上隅で、 **[リソースの作成]**  >  **[Compute]**  >  **[Windows Server 2019 Datacenter]** の順に選択します。
2. **[仮想マシンの作成]** の **[Basic]** タブに次の値を入力するか選択します。

   - **[サブスクリプション]**  >  **[リソース グループ]** : **myResourceGroupTM1** を選択します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「*myVMEastUS*」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** : **[米国東部]** を選択します。
   - **[管理者アカウント]**  >  **[ユーザー名]** : 任意のユーザー名を入力します。
   - **[管理者アカウント]**  >  **[パスワード]** : 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。
   - **[受信ポートの規則]**  >  **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **[受信ポートの規則]**  >  **[受信ポートの選択]** : プルダウン ボックスから **[RDP]** を選択します。

3. **[管理]** タブを選択します。または、 **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択し、 **[次へ: 管理]** を選択します。 **[監視]** で **[ブート診断]** を **[オフ]** に設定します。
4. **[Review + create]\(レビュー + 作成\)** を選択します。
5. 設定を確認し、 **[作成]** をクリックします。  
6. 手順に従って、*myVMWestEurope* という名前の 2 番目の VM を作成します。 **[リソース グループ]** の名前は *myResourceGroupTM2*、 **[場所]** は "*西ヨーロッパ*"、他のすべての設定は *myVMEastUS* と同じにします。
7. VM の作成には数分かかります。 両方の VM の作成が完了するまで、以降の手順に進まないでください。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

待機時間が最も短いエンドポイントに送信することでユーザー トラフィックを転送する Traffic Manager プロファイルを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Traffic Manager プロファイル]**  >  **[作成]** の順に選択します。
2. **[Traffic Manager プロファイルの作成]** で、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[作成]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | 名前                   | この名前は trafficmanager.net ゾーン内で一意である必要があります。結果的に、Traffic Manager プロファイルへのアクセスに使用される、DNS 名.trafficmanager.net になるためです。                                   |
    | ルーティング方法          | **パフォーマンス** によるルーティング方法を選択します。                                       |
    | サブスクリプション            | サブスクリプションを選択します。                          |
    | Resource group          | リソース グループ「*myResourceGroupTM1*」を選択します。 |
    | 場所                | **[米国東部]** を選択します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。                              |
    |

    ![Traffic Manager プロファイルの作成](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

ユーザーに最も近いエンドポイントにユーザー トラフィックをルーティングするために、IIS サーバーを実行する 2 台の VM (*myIISVMEastUS* & *myIISVMWestEurope*) を追加します。

1. ポータルの検索バーで、前のセクションで作成した Traffic Manager プロファイルの名前を検索し、表示された結果からそのプロファイルを選択します。
2. **[Traffic Manager プロファイル]** の **[設定]** セクションで **[エンドポイント]** をクリックし、 **[追加]** をクリックします。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[OK]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | Type                    | Azure エンドポイント                                   |
    | 名前           | myEastUSEndpoint                                        |
    | ターゲット リソースの種類           | パブリック IP アドレス                          |
    | ターゲット リソース          | **パブリック IP アドレスを選択** して、同じサブスクリプションでパブリック IP アドレスを持つリソースの一覧を表示します。 **[リソース]** で、*myIISVMEastUS-ip* という名前のパブリック IP アドレスを選択します。 これは、米国東部内の IIS サーバー VM のパブリック IP アドレスです。|
    |        |           |

4. 手順 2 と 3 を繰り返して、*myIISVMWestEurope* という名前の IIS サーバー VM に関連付けられたパブリック IP アドレス *myIISVMWestEurope-ip* 用の *myWestEuropeEndpoint* という名前の別のエンドポイントを追加します。
5. 両方のエンドポイントは、追加が完了すると、 **[Traffic Manager プロファイル]** に、監視ステータスが **[オンライン]** の状態で表示されます。

    ![Traffic Manager エンドポイントの追加](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager プロファイルのテスト

このセクションでは、Traffic Manager が待機時間が最も短い Web サイトを実行している最も近い VM にユーザー トラフィックをルーティングすることをテストします。 動作中の Traffic Manager を表示するには、次の手順を完了します。

1. Traffic Manager プロファイルの DNS 名を判別します。
2. 次のように、動作中の Traffic Manager を表示します。
    - **米国東部** リージョン内のテスト VM (*myVMEastUS*) から、Web ブラウザーで、Traffic Manager プロファイルの DNS 名を参照します。
    - **西ヨーロッパ** リージョン内のテスト VM (*myVMWestEurope*) から、Web ブラウザーで、Traffic Manager プロファイルの DNS 名を参照します。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager プロファイルの DNS 名を判別する

このチュートリアルでは、わかりやすくするために、Traffic Manager プロファイルの DNS 名を使用して Web サイトにアクセスします。

次のように、Traffic Manager プロファイルの DNS 名を判別できます。

1. ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索します。 表示された結果で、Traffic Manager プロファイルをクリックします。
2. **[Overview]** をクリックします。
3. **[Traffic Manager プロファイル]** に、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 運用環境のデプロイでは、DNS CNAME レコードを使用して、Traffic Manager のドメイン名をポイントするバニティ ドメイン名を構成します。

   ![Traffic Manager の DNS 名](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager の動作確認

このセクションでは、Traffic Manager が動作していることを確認します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *[myResourceGroupTM1]* リソース グループにある *[myVMEastUS]* をクリックします。
2. **[概要]** ページで **[接続]** **[仮想マシンへの接続]** の順にクリックし、 **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
1. VM *myVMEastUS* の Web ブラウザーで、Traffic Manager プロファイルの DNS 名を入力して、Web サイトを表示します。 この VM は **米国東部** にあるため、**米国東部** にある最も近い IIS サーバー *myIISVMEastUS* でホストされている、最も近い Web サイトにルーティングされます。

   ![Web ブラウザーで "Traffic Manager" プロファイルを表示する画面のスクリーンショット。](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. 次に、手順 1 から 5 を使用して **西ヨーロッパ** にある VM *myVMWestEurope* に接続し、その VM から Traffic Manager プロファイルのドメイン名を参照します。 この VM は **西ヨーロッパ** にあるため、**西ヨーロッパ** にある最も近い IIS サーバー *myIISVMWestEurope* でホストされている Web サイトにルーティングされます。

   ![Traffic Manager プロファイルのテスト](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループ (**ResourceGroupTM1** と **ResourceGroupTM2**) を削除します。 これを行うには、リソース グループ (**ResourceGroupTM1** または **ResourceGroupTM2**) を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [一定のエンドポイントにトラフィックを分散させる](traffic-manager-configure-weighted-routing-method.md)
