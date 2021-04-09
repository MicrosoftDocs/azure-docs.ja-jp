---
title: チュートリアル - Azure Traffic Manager を使用してサブネットのトラフィック ルーティングを構成する
description: このチュートリアルでは、ユーザーのサブネットから特定のエンドポイントにトラフィックをルーティングするように Traffic Manager を構成する方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 9b916f9942b0459b41d98b952fad072ae48318b3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505437"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>チュートリアル:Traffic Manager を使用してユーザーのサブネットに基づいて特定のエンドポイントにトラフィックを転送する

この記事では、サブネット トラフィックのルーティング方法を構成する方法について説明します。 **サブネット** トラフィックのルーティング方法を使用すると、一連の IP アドレス範囲を特定のエンドポイントにマップできます。 Traffic Manager で要求が受信されると、要求の送信元 IP アドレスが検査されて、それに関連付けられているエンドポイントが返されます。

このチュートリアルでは、サブネットのルーティングを使用し、ユーザーのクエリの IP アドレスに応じて、トラフィックを内部 Web サイトまたは運用 Web サイトにルーティングします。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * IIS で基本的な Web サイトを実行する 2 台の VM を作成する
> * 動作中の Traffic Manager を表示するための 2 台のテスト VM を作成する
> * IIS を実行する VM の DNS 名を構成する
> * ユーザーのサブネットに基づいてトラフィックをルーティングするための Traffic Manager プロファイルを作成する
> * Traffic Manager プロファイルに VM エンドポイントを追加する
> * Traffic Manager の動作確認

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Traffic Manager の動作を確認するために、以下をデプロイする必要があります。

- 異なる Azure リージョン (**米国東部** (内部 Web サイトとして機能) と **西ヨーロッパ** (運用 Web サイトとして機能)) で実行している 2 つの基本的な Web サイト。
- Traffic Manager をテストするための 2 台の VM (1 台は **米国東部** 内、2 台目は **西ヨーロッパ** 内)。

テスト VM を使用して、ユーザー クエリの送信元サブネットに基づいて、Traffic Manager でユーザー トラフィックが内部 Web サイトまたは運用 Web サイトにルーティングされる方法を示します。

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
5. 設定を確認し、 **[作成]** を選択します。  
6. 手順に従って、*myIISVMWestEurope* という名前の 2 番目の VM を作成します。 **[リソース グループ]** の名前は *myResourceGroupTM2*、 **[場所]** は "*西ヨーロッパ*"、他のすべての設定は、*myIISVMEastUS* と同じにします。
7. VM の作成には数分かかります。 両方の VM の作成が完了するまで、以降の手順に進まないでください。

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS をインストールして既定の Web ページをカスタマイズする

このセクションでは、2 台の VM (*myIISVMEastUS* と *myIISVMWestEurope*) に IIS サーバーをインストールした後、既定の Web サイト ページを更新します。 カスタマイズする Web サイト ページには、Web ブラウザーからその Web サイトにアクセスするときに接続する VM の名前が表示されます。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *myResourceGroupTM1* リソース グループにある *myIISVMEastUS* を選択します。
2. **[概要]** ページで **[接続]** を選択し、 **[仮想マシンへの接続]** の **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告が表示されたら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
6. サーバーのデスクトップで、 **[Windows 管理ツール]** > **[サーバー マネージャー]** の順に移動します。
7. VM *myIISVMEastUS* で Windows PowerShell を起動し、次のコマンドを使用して IIS サーバーのインストールと既定の htm ファイルの更新を行います。

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. *myIISVMEastUS* VM との RDP 接続を閉じます。
9. *myResourceGroupTM2* リソース グループ内の VM (*myIISVMWestEurope*) への RDP 接続を作成し、手順 1. から 6. を繰り返して、IIS のインストールと既定の Web ページのカスタマイズを実行します。
10. *myIISVMWestEurope* VM で Windows PowerShell を起動し、次のコマンドを使用して IIS サーバーのインストールと既定の htm ファイルの更新を行います。

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS を実行する VM の DNS 名を構成する

Traffic Manager は、サービス エンドポイントの DNS 名に基づいてユーザー トラフィックをルーティングします。 このセクションでは、IIS サーバー *myIISVMEastUS* と *myIISVMWestEurope* の DNS 名を構成します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *myResourceGroupTM1* リソース グループにある *myIISVMEastUS* を選択します。
2. **[概要]** ページで、 **[DNS 名]** の下の **[構成]** を選択します。
3. **[構成]** ページで、[DNS 名ラベル] の下に一意の名前を追加した後、 **[保存]** を選択します。
4. *myResourceGroupTM2* リソース グループにある *myIISVMWestEurope* という名前の VM に対して、手順 1. から 3. を繰り返します。

### <a name="create-test-vms"></a>テスト VM を作成する

このセクションでは、VM (*myVMEastUS* と *myVMWestEurope*) を、該当する Azure リージョン (**米国東部** と **西ヨーロッパ**) 内に作成します。 これらの VM は、ユーザーのクエリのサブネットに基づいて Traffic Manager がユーザー トラフィックをどのようにルーティングするかをテストするために使用します。

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
5. 設定を確認し、 **[作成]** を選択します。  
6. 手順に従って、*myVMWestEurope* という名前の 2 番目の VM を作成します。 **[リソース グループ]** の名前は *myResourceGroupTM2*、 **[場所]** は "*西ヨーロッパ*"、他のすべての設定は *myVMEastUS* と同じにします。
7. VM の作成には数分かかります。 両方の VM の作成が完了するまで、以降の手順に進まないでください。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

要求の送信元 IP アドレスに基づいて特定のエンドポイントを返すことができる Traffic Manager プロファイルを作成します。

1. 画面の左上で、 **[リソースの作成]** を選択します。 「*Traffic Manager プロファイル*」を検索し、 **[作成]** を選択します。
2. **[Traffic Manager プロファイルの作成]** で、次の情報を入力するか、選択します。 残りの設定は既定値をそのまま使用し、 **[作成]** を選択します。

    ![Traffic Manager プロファイルの作成](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | 名前                   | この名前は trafficmanager.net ゾーン内で一意である必要があります。結果的に、Traffic Manager プロファイルへのアクセスに使用される、DNS 名.trafficmanager.net になるためです。                                   |
    | ルーティング方法          | **[サブネット]** ルーティング方式を選択します。                                       |
    | サブスクリプション            | サブスクリプションを選択します。                          |
    | Resource group          | **[既存]** を選択し、「*myResourceGroupTM1*」と入力します。 |

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

ユーザーのクエリのサブネットに基づいてユーザー トラフィックをルーティングするために、IIS サーバーを実行する 2 つの VM *myIISVMEastUS* と *myIISVMWestEurope* を追加します。

1. ポータルの検索バーで、前のセクションで作成した Traffic Manager プロファイルの名前を検索し、表示された結果からそのプロファイルを選択します。
2. **[Traffic Manager プロファイル]** の **[設定]** セクションで、**[エンドポイント]** を選択し、**[追加]** を選択します。
3. 次の情報を入力または選択します。 残りの設定は既定値をそのまま使用し、 **[OK]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | Type                    | Azure エンドポイント                                   |
    | 名前           | myInternalWebSiteEndpoint                                        |
    | ターゲット リソースの種類           | パブリック IP アドレス                          |
    | ターゲット リソース          | **パブリック IP アドレスを選択** して、同じサブスクリプションでパブリック IP アドレスを持つリソースの一覧を表示します。 **[リソース]** で、*myIISVMEastUS-ip* という名前のパブリック IP アドレスを選択します。 これは、米国東部内の IIS サーバー VM のパブリック IP アドレスです。|
    |  サブネット ルーティングの設定    |   *myVMEastUS* テスト VM の IP アドレスを追加します。 この VM から送信されたすべてのユーザー クエリは、*myInternalWebSiteEndpoint* に転送されます。    |

4. 手順 2. と 3. を繰り返して、*myIISVMWestEurope* という名前の IIS サーバー VM に関連付けられたパブリック IP アドレス *myIISVMWestEurope-ip* 用の *myProdWebsiteEndpoint* という名前の別のエンドポイントを追加します。 **[Subnet routing settings]\(サブネット ルーティングの設定\)** で、テスト VM *myVMWestEurope* の IP アドレスを追加します。 このテスト VM からのすべてのユーザー クエリは、エンドポイント *myProdWebsiteEndpoint* にルーティングされます。
5. 両方のエンドポイントが追加されると、 **[Traffic Manager プロファイル]** に表示され、監視ステータスが **[オンライン]** になります。

## <a name="test-traffic-manager-profile"></a>Traffic Manager プロファイルのテスト

このセクションでは、Traffic Manager によって特定のサブネットからのユーザー トラフィックが特定のエンドポイントにどのようにルーティングされるかをテストします。 動作中の Traffic Manager を表示するには、次の手順を完了します。

1. Traffic Manager プロファイルの DNS 名を判別します。
2. 次のように、動作中の Traffic Manager を表示します。
    - **米国東部** リージョン内のテスト VM (*myVMEastUS*) から、Web ブラウザーで、Traffic Manager プロファイルの DNS 名を参照します。
    - **西ヨーロッパ** リージョン内のテスト VM (*myVMWestEurope*) から、Web ブラウザーで、Traffic Manager プロファイルの DNS 名を参照します。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager プロファイルの DNS 名を判別する

このチュートリアルでは、わかりやすくするために、Traffic Manager プロファイルの DNS 名を使用して Web サイトにアクセスします。

次のように、Traffic Manager プロファイルの DNS 名を判別できます。

1. ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索します。 表示された結果で、Traffic Manager プロファイルを選択します。
2. **[概要]** を選択します。
3. **[Traffic Manager プロファイル]** に、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 運用環境のデプロイでは、DNS CNAME レコードを使用して、Traffic Manager のドメイン名をポイントするバニティ ドメイン名を構成します。

### <a name="view-traffic-manager-in-action"></a>Traffic Manager の動作確認

このセクションでは、Traffic Manager が動作していることを確認します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *[myResourceGroupTM1]* リソース グループにある *[myVMEastUS]* を選択します。
2. **[概要]** ページで **[接続]** を選択し、 **[仮想マシンへの接続]** の **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告が表示されたら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
6. VM *myVMEastUS* の Web ブラウザーで、Traffic Manager プロファイルの DNS 名を入力して、Web サイトを表示します。 VM *myVMEastUS* の IP アドレスはエンドポイント *myInternalWebsiteEndpoint* と関連付けられているので、Web ブラウザーではテスト Web サイト サーバー *myIISVMEastUS* が起動されます。

7. 次に、手順 1 から 5 を使用して **西ヨーロッパ** にある VM *myVMWestEurope* に接続し、その VM から Traffic Manager プロファイルのドメイン名を参照します。 VM *myVMWestEurope* の IP アドレスはエンドポイント *myProductionWebsiteEndpoint* と関連付けられているので、Web ブラウザーではテスト Web サイト サーバー *myIISVMWestEurope* が起動されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループ (**ResourceGroupTM1** と **ResourceGroupTM2**) を削除します。 これを行うには、リソース グループ (**ResourceGroupTM1** または **ResourceGroupTM2**) を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

サブネットのルーティング方法の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [サブネット トラフィック ルーティング方法](traffic-manager-routing-methods.md#subnet)
