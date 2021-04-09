---
title: 'チュートリアル: 重み付けされたエンドポイントにトラフィックをルーティングする - Azure Traffic Manager'
description: このチュートリアルの記事では、Traffic Manager を使用して重み付けされたエンドポイントにトラフィックをルーティングする方法について説明します。
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: 55c316a370b9e44e906e48b4716201384567c9c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003788"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>チュートリアル:Traffic Manager を使用して重み付けされたエンドポイントを使用してトラフィックのルーティングを制御する

このチュートリアルでは、Azure Traffic Manager を使用して重み付けルーティング方法を使用してエンドポイント間のユーザー トラフィックのルーティングを制御する方法について説明します。 このルーティング方法では、Traffic Manager プロファイル構成で各エンドポイントに重みを割り当てます。 その後、各エンドポイントに割り当てられている重みに基づいてユーザー トラフィックがルーティングされます。 重みは 1 から 1,000 までの整数です。 エンドポイントに割り当てられた重み値が大きいほど優先度が高くなります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 簡単な Web サイトを IIS 上で運営する VM を 2 台作成する。
> * Traffic Manager の動作を表示するため、テスト VM を 2 台作成する。
> * IIS を稼働する VM の DNS 名を構成する。
> * Traffic Manager プロファイルを作成する。
> * Traffic Manager プロファイルに VM エンドポイントを追加する。
> * Traffic Manager の動作を確認します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Traffic Manager の動作を確認するために以下をデプロイします。

- 異なる Azure リージョン (米国東部と西ヨーロッパ) で運営される基本的な Web サイトの 2 つのインスタンス。
- Traffic Manager をテストするための 2 台の VM (1 台は米国東部内、もう 1 台は西ヨーロッパ内)。 テスト VM は、エンドポイントにより大きい重みが割り当てられた Web サイトに Traffic Manager がユーザー トラフィックをルーティングすることを示すために使用されます。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

### <a name="create-websites"></a>Web サイトを作成する

このセクションでは、2 つの Azure リージョン内に Traffic Manager プロファイル用の 2 つのサービス エンドポイントを提供する 2 つの Web サイト インスタンスを作成します。 2 つの Web サイトを作成するには、次の手順に従います。

1. 簡単な Web サイトを運営する VM を 2 台作成します (1 台は米国東部内、もう 1 台は西ヨーロッパ内)。
2. 各 VM に IIS サーバーをインストールします。 ユーザーが この Web サイトにアクセスした場合に接続される VM の名前を記述した既定の Web ページを更新します。

#### <a name="create-vms-for-running-websites"></a>Web サイトを実行するための VM を作成する

このセクションでは、米国東部と西ヨーロッパの 2 つの Azure リージョンに 2 台の VM (*myIISVMEastUS* と *myIISVMWestEurope*) を作成します。

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

#### <a name="install-iis-and-customize-the-default-webpage"></a>IIS をインストールして既定の Web ページをカスタマイズする

このセクションでは、2 台の VM (myIISVMEastUS と myIISVMWestEurope) に IIS サーバーをインストールした後、既定の Web ページを更新します。 カスタマイズした Web ページには、Web ブラウザーからその Web サイトにアクセスしたときに接続する VM の名前が表示されます。

1. 左側のメニューで、 **[すべてのリソース]** を選択します。 リソースの一覧から、**myResourceGroupTM1** リソース グループの **myIISVMEastUS** を選択します。
2. **[概要]** ページで **[接続]** を選択します。 **[仮想マシンへの接続]** で、 **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[接続]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** の順に選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告が表示されたら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
6. サーバーのデスクトップで、 **[Windows 管理ツール]**  >  **[サーバー マネージャー]** の順に移動します。
7. VM1 で Windows PowerShell を開きます。 次のコマンドを使用して、IIS サーバーのインストールと既定の htm ファイルの更新を行います。

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![IIS のインストールと Web ページのカスタマイズ](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. **myIISVMEastUS** との RDP 接続を閉じます。
9. 手順 1 から 8 を繰り返します。 **myResourceGroupTM2** リソース グループ内の VM (**myIISVMWestEurope**) への RDP 接続を作成し、IIS のインストールと既定の Web ページのカスタマイズを行います。

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS を実行する VM の DNS 名を構成する

Traffic Manager は、サービス エンドポイントの DNS 名に基づいてユーザー トラフィックをルーティングします。 このセクションでは、IIS サーバー myIISVMEastUS と myIISVMWestEurope の DNS 名を構成します。

1. 左側のメニューで、 **[すべてのリソース]** を選択します。 リソースの一覧から、**myResourceGroupTM1** リソース グループの **myIISVMEastUS** を選択します。
2. **[概要]** ページで、 **[DNS 名]** の下の **[構成]** を選択します。
3. **[構成]** ページで、DNS 名ラベルの下に一意の名前を追加します。 次に、 **[保存]** を選択します。
4. **myResourceGroupTM2** リソース グループにある **myIISVMWestEurope** という名前の VM に対して、手順 1. から 3. を繰り返します。

### <a name="create-a-test-vm"></a>テスト VM の作成

このセクションでは、VM (*myVMEastUS* と *myVMWestEurope*) を、該当する Azure リージョン (**米国東部** と **西ヨーロッパ**) 内に作成します。 これらの VM は、より大きい重み値が割り当てられた Web サイト エンドポイントに Traffic Manager がトラフィックをどのようにルーティングするかをテストするために使用されます。

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

**重み付け** ルーティング方法に基づいて Traffic Manager プロファイルを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Traffic Manager プロファイル]**  >  **[作成]** の順に選択します。
2. **[Traffic Manager プロファイルの作成]** ブレードで、次の情報を入力するか、選択します。 その他の設定は既定値のままにして、 **[作成]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | 名前                   | trafficmanager.net ゾーン内で一意の名前を入力します。 これが、Traffic Manager プロファイルにアクセスする際に使用する DNS 名、trafficmanager.net になります。                                   |
    | ルーティング方法          | **[重み付け]** ルーティング方法を選択します。                                       |
    | サブスクリプション            | サブスクリプションを選択します。                          |
    | Resource group          | **[既存のものを使用]** 、 **[myResourceGroupTM1]** の順に選択します。 |
    |        |   |

    ![Traffic Manager プロファイルの作成](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

ユーザー トラフィックのルーティング先となる、IIS サーバーを稼働する 2 台の VM (myIISVMEastUS と myIISVMWestEurope) を追加します。

1. ポータルの検索バーで、前のセクションで作成した Traffic Manager プロファイルの名前を検索します。 表示された結果で、プロファイルを選択します。
2. **[Traffic Manager プロファイル]** の **[設定]** セクションで、 **[エンドポイント]**  >  **[追加]** の順に選択します。
3. 次の情報を入力または選択します。 その他の設定は既定値のままにして、 **[OK]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | Type                    | Azure エンドポイントを入力します。                                   |
    | 名前           | 「**myEastUSEndpoint**」と入力します。                                        |
    | ターゲット リソースの種類           | **[パブリック IP アドレス]** を選択します。                          |
    | ターゲット リソース          | パブリック IP アドレスを選択して、同じサブスクリプションでパブリック IP アドレスが割り当てられているリソースの一覧を表示します。 **[リソース]** で、**myIISVMEastUS-ip** という名前のパブリック IP アドレスを選択します。 これは、米国東部内の IIS サーバー VM のパブリック IP アドレスです。|
    |  Weight      | 「**100**」と入力します。        |
    |        |           |

4. 手順 2. と 3. を繰り返して、パブリック IP アドレス **myIISVMWestEurope-ip** 用に **myWestEuropeEndpoint** という名前の別のエンドポイントを追加します。 このアドレスは、myIISVMWestEurope という名前の IIS サーバー VM に関連付けられています。 **[重み]** に「**25**」を入力します。
5. 両方のエンドポイントの追加が完了すると、どちらも Traffic Manager プロファイルに、監視ステータスが **[オンライン]** の状態で表示されます。

## <a name="test-the-traffic-manager-profile"></a>Traffic Manager プロファイルのテスト

Traffic Manager の動作を表示するには、次の手順を完了します。

1. Traffic Manager プロファイルの DNS 名を判別します。
2. Traffic Manager の動作を確認します。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager プロファイルの DNS 名を判別する

このチュートリアルでは、わかりやすくするために、Traffic Manager プロファイルの DNS 名を使用して Web サイトにアクセスします。

次のように、Traffic Manager プロファイルの DNS 名を判別できます。

1. ポータルの検索バーで、前のセクションで作成した Traffic Manager プロファイルの名前を検索します。 表示された結果で、Traffic Manager プロファイルを選択します。
2. **[概要]** を選択します。
3. Traffic Manager プロファイルに DNS 名が表示されます。 運用環境のデプロイでは、DNS CNAME レコードを使用して、Traffic Manager のドメイン名をポイントするバニティ ドメイン名を構成します。

   ![Traffic Manager の DNS 名](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager の動作確認

このセクションでは、Traffic Manager が動作していることを確認します。

1. 左側のメニューで、 **[すべてのリソース]** を選択します。 リソースの一覧から、**myResourceGroupTM1** リソース グループの **myVMEastUS** を選択します。
2. **[概要]** ページで **[接続]** を選択します。 **[仮想マシンへの接続]** で、 **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[接続]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** の順に選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告が表示されたら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
6. VM myVMEastUS の Web ブラウザーで、Traffic Manager プロファイルの DNS 名を入力して、Web サイトを表示します。 IIS サーバー myIISVMEastUS にホストされている Web サイトにルーティングされます。これは、その Web サイトの方が高い重み (**100**) を割り当てられていることによるものです。 IIS サーバー myIISVMWestEurope には、エンドポイントの重み値としてこれよりも低い **25** が割り当てられています。

   ![Traffic Manager プロファイルのテスト](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. 重み付けされた Web サイトの応答を確認するには、VM myVMWestEurope に対して手順 1. から 6. を繰り返します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソース グループは、不要になったら削除してかまいません。 これを行うには、リソース グループ (**ResourceGroupTM1** または **ResourceGroupTM2**) を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

ルーティング方法の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [トラフィック ルーティング方法](traffic-manager-routing-methods.md)
