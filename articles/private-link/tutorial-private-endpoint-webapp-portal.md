---
title: チュートリアル:Azure プライベート エンドポイントを使用して Web アプリに接続する
titleSuffix: Azure Private Link
description: Azure プライベート エンドポイントを使用して Web アプリに非公開で接続するこのチュートリアルで作業を開始します。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896975"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>チュートリアル:Azure プライベート エンドポイントを使用して Web アプリに接続する

Azure プライベート エンドポイントは、Azure における Private Link の基本的な構成要素です。 仮想マシン (VM) などの Azure リソースと Private Link リソースとの非公開での通信が可能になります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークと bastion ホストを作成します。
> * 仮想マシンを作成します。
> * Web アプリを作成します。
> * プライベート エンドポイントを作成します。
> * Web アプリのプライベート エンドポイントへの接続をテストします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!Note]
> プライベート エンドポイントは、PremiumV2 レベル、PremiumV3 レベルの Windows Web アプリ、Linux Web アプリ、Azure Functions Premium プラン (エラスティック Premium プランとも呼ばれます) に対するパブリック リージョンで利用できます。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-virtual-network-and-bastion-host"></a>仮想ネットワークと bastion ホストの作成

このセクションでは、仮想ネットワーク、サブネット、bastion ホストを作成します。 

bastion ホストは、プライベート エンドポイントをテストする目的で、仮想マシンに安全に接続するために使用されます。

1. 画面の左上で、 **[リソースの作成] > [ネットワーク] > [仮想ネットワーク]** の順に選択するか、検索ボックスで **Virtual network** を検索します。

2. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | Azure サブスクリプションを選択します。                                  |
    | リソース グループ   | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 「**myVNet**」と入力します                                    |
    | リージョン           | **[西ヨーロッパ]** を選択します |

3. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

4. **[IP アドレス]** タブで、次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | IPv4 アドレス空間 | 「**10.1.0.0/16**」と入力します。 |

5. **[サブネット名]** で、 **[default]\(既定\)** という単語を選択します。

6. **[サブネットの編集]** に次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | サブネット名 | 「**mySubnet**」と入力します。 |
    | サブネットのアドレス範囲 | 「**10.1.0.0/24**」と入力します。 |

7. **[保存]** を選択します。

8. **[セキュリティ]** タブをクリックします。

9. **[BastionHost]** で **[有効にする]** を選択します。 この情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | 要塞名 | 「**myBastionHost**」と入力します |
    | AzureBastionSubnet のアドレス空間 | 「**10.1.1.0/24**」と入力します |
    | パブリック IP アドレス | **[新規作成]** を選択します。 </br> **[名前]** に「**myBastionIP**」と入力します。 </br> **[OK]** を選択します。 |


8. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

9. **［作成］** を選択します

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

このセクションでは、プライベート エンドポイントのテストに使用する仮想マシンを作成します。


1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** の順に選択するか、検索ボックスで「**仮想マシン**」を検索します。
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVM**」と入力します |
    | リージョン | **[西ヨーロッパ]** を選択します |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **[Windows Server 2019 Datacenter - Gen1]** を選択します |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | **myVNet** |
    | Subnet | **mySubnet** |
    | パブリック IP | **[なし]** を選択します。 |
    | NIC ネットワーク セキュリティ グループ | **Basic**|
    | パブリック受信ポート | **[なし]** を選択します。 |
   
5. **[Review + create]\(レビュー + 作成\)** を選択します。 
  
6. 設定を確認し、 **[作成]** を選択します。

## <a name="create-web-app"></a>Web アプリの作成

このセクションでは、Web アプリを作成します。

1. 左側のメニューで、 **[リソースの作成]**  >  **[ストレージ]**  >  **[Web アプリ]** の順に選択するか、検索ボックスで「**Web アプリ**」を検索します。

2. **[Web アプリの作成]** の **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |  |
    | 名前 | 「**mywebapp**」と入力します。 この名前が使用できない場合は、一意の名前を入力してください。 |
    | 発行 | **[コード]** を選択します。 |
    | ランタイム スタック | **[.NET Core 3.1 (LTS)]** を選択します。 |
    | オペレーティング システム | **[Windows]** を選択します。 |
    | リージョン | **[西ヨーロッパ]** を選択します |
    | **App Service プラン** |  |
    | Windows プラン (西ヨーロッパ) | **[新規作成]** を選択します。 </br> **[名前]** に「**myServicePlan**」と入力します。 |
    | SKU とサイズ | **[サイズの変更]** を選択します。 </br> **[Spec Picker]\(スペックの選択\)** 画面で **[P2V2]** を選択します。 </br> **[適用]** を選択します。 |
   
3. **[Review + create]\(レビュー + 作成\)** を選択します。

4. **［作成］** を選択します

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Azure portal の [Web アプリの作成] の [基本] タブ。" border="true":::

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

1. 左側のメニューで、 **[すべてのリソース]**  >  **[mywebapp]** または作成時に選択した名前を選択します。

2. Web アプリの概要で、 **[設定]**  >  **[Networking]\(ネットワーク\)** を選択します。

3. **[Networking]\(ネットワーク\)** で、 **[Configure your private endpoint connections]\(プライベート エンドポイント接続の構成\)** を選択します。

4. **[プライベート エンドポイント接続]** 画面で、 **[+ 追加]** を選択します。

5. **[プライベート エンドポイントの追加]** 画面で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 「**mywebappendpoint**」と入力します。 |
    | サブスクリプション | サブスクリプションを選択します。 |
    | 仮想ネットワーク | **[myVNet]** を選択します。 |
    | Subnet | **[mySubnet]** を選択します。 |
    | プライベート DNS ゾーンとの統合 | **[はい]** を選択します。 |

6. **[OK]** を選択します。
    

## <a name="test-connectivity-to-private-endpoint"></a>プライベート エンドポイントへの接続のテスト

このセクションでは、前の手順で作成した仮想マシンを使用し、プライベート エンドポイントを通じて Web アプリに接続します。

1. 左側のナビゲーション ペインで **[リソース グループ]** を選択します。

2. **[myResourceGroup]** を選択します。

3. **[myVM]** を選択します。

4. **myVM** の [概要] ページで **[接続]** 、 **[Bastion]** の順に選択します。

5. 青色の **[Bastion を使用する]** ボタンを選択します。

6. 仮想マシンの作成時に入力したユーザー名とパスワードを入力します。

7. 接続後にサーバーで Windows PowerShell を開きます。

8. 「`nslookup <webapp-name>.azurewebsites.net`」と入力します。 **\<webapp-name>** を、前の手順で作成した Web アプリの名前に置き換えます。  以下に表示されるようなメッセージが返されます。

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Web アプリ名に対応する **10.1.0.5** というプライベート IP アドレスが返されます。  このアドレスは、先ほど作成した仮想ネットワークのサブネット内に存在します。

9. お使いのローカル コンピューターで Web ブラウザーを開き、Web アプリの外部 URL **https://\<webapp-name>.azurewebsites.net** を入力します。

10. **403** ページが表示されることを確認します。 このページは、Web アプリに外部からアクセスできないことを示しています。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="外部 Web アプリ アドレスに対する 403 ページ。" border="true":::

11. **myVM** への bastion 接続で、Internet Explorer を開きます。

12. Web アプリの URL **https://\<webapp-name>.azurewebsites.net** を入力します。

13. 既定の Web アプリ ページが表示されることを確認します。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="既定の Web アプリ ページ。" border="true":::

18. **myVM** への接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使う予定がなければ、次の手順を使用して、仮想ネットワーク、仮想マシン、および Web アプリを削除します。

1. 左側のメニューから、 **[リソース グループ]** を選択します。

2. **[myResourceGroup]** を選択します。

3. **[リソース グループの削除]** を選択します。

4. **[リソース グループ名を入力してください]** に「**myResourceGroup**」と入力します。

5. **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

Private Link サービスの作成方法を確認します。
> [!div class="nextstepaction"]
> [Private Link サービスを作成する](create-private-link-service-portal.md)
