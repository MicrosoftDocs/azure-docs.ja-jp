---
title: Azure Functions へのプライベート サイト アクセスを有効にする
description: Azure Functions 用に Azure 仮想ネットワークのプライベート サイト アクセスを設定する方法について説明します。
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937044"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>チュートリアル:Azure Functions のプライベート サイト アクセスを設定する

このチュートリアルでは、Azure Functions の[プライベート サイト アクセス](./functions-networking-options.md#private-endpoint-connections)を有効にする方法について説明します。 プライベート サイト アクセスを使用すると、自分の関数コードを特定の仮想ネットワークからしかトリガーできないように設定できます。

プライベート サイト アクセスは、Function App へのアクセスを特定の仮想ネットワークに限定する必要があるような状況で役立ちます。 たとえば、Function App の対象を特定の組織の従業員や、特定の仮想ネットワーク内のサービス (別の Azure 関数、Azure 仮想マシン、AKS クラスターなど) に限定することができます。

Function App が仮想ネットワーク内の Azure リソースにアクセスする必要がある (つまり[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)経由で接続されている) 場合、[仮想ネットワークの統合](./functions-create-vnet.md)が必要となります。

このチュートリアルでは、Function App のプライベート サイト アクセスを構成する方法について説明します。

> [!div class="checklist"]
> * 仮想マシンの作成
> * Azure Bastion サービスを作成する
> * Azure Functions アプリを作成する
> * 仮想ネットワーク サービス エンドポイントを構成する
> * Azure 関数を作成してデプロイする
> * 仮想ネットワークの内外から関数を呼び出す

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="topology"></a>トポロジ

次の図は、作成するソリューションのアーキテクチャを示します。

![プライベート サイト アクセス ソリューションのアーキテクチャの概要図](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、IP アドレスの割り当てとサブネット化を理解することが重要です。 [アドレスの割り当てとサブネット化の基本を取り上げたこの記事](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)から開始できます。 多くの記事およびビデオをオンラインで利用できます。

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

このチュートリアルの最初の手順として、仮想ネットワーク内に新しい仮想マシンを作成します。  特定の仮想ネットワーク内からしか関数を利用できないよう制限した後は、この仮想マシンが関数へのアクセスに使用されます。

1. **[リソースの作成]** ボタンを選択します。

1. 検索フィールドに「**Windows Server**」と入力し、検索結果から **[Windows Server]** を選択します。

1. Windows Server オプションの一覧から **[Windows Server 2019 Datacenter]** を選択し、 **[作成]** ボタンを押します。

1. _[基本]_ タブで、画像の下の表で指定されているように VM 設定を使用します。

    >[!div class="mx-imgBorder"]
    >![新しい Windows VM の [基本] タブ](./media/functions-create-private-site-access/create-vm-3.png)

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | _サブスクリプション_ | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 |
    | [_リソース グループ_](../azure-resource-manager/management/overview.md) | myResourceGroup | このチュートリアルのすべてのリソースを含めるためのリソース グループを選択します。  同じリソース グループを使用することで、このチュートリアルの完了時にリソースをクリーンアップしやすくなります。 |
    | _仮想マシン名_ | myVM | VM 名は、リソース グループ内で一意である必要があります |
    | [_リージョン_](https://azure.microsoft.com/regions/) | (米国) 米国中北部 | 近くの (アクセスする関数に近い) リージョンを選択します。 |
    | _パブリック インバウンド ポート_ | なし | インターネットから VM へのインバウンド接続を排除するために **[なし]** を選択します。 VM へのリモート アクセスは、Azure Bastion サービス経由で構成します。 |

1. _[ネットワーク]_ タブを選択し、 **[新規作成]** を選択して新しい仮想ネットワークを構成します。

    >[!div class="mx-imgBorder"]
    >![[仮想ネットワーク] セクションの [新規作成] アクションが強調表示されている [ネットワーク] タブを示すスクリーンショット。](./media/functions-create-private-site-access/create-vm-networking.png)

1. _[仮想ネットワークの作成]_ で、画像の下にある表の設定を使用します。

    >[!div class="mx-imgBorder"]
    >![新しい VM 用の仮想ネットワークを新規作成する](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | _名前_ | myResourceGroup-vnet | 仮想ネットワークに対して生成される既定の名前を使用できます。 |
    | _アドレス範囲_ | 10.10.0.0/16 | 仮想ネットワークの単一のアドレス範囲を使用します。 |
    | _サブネット名_ | チュートリアル | サブネットの名前です。 |
    | _アドレス範囲_ (サブネット) | 10.10.1.0/24 | サブネット サイズは、サブネットに追加できるインターフェイスの数を定義します。 このサブネットは VM によって使用されます。 /24 サブネットは、254 のホスト アドレスを提供します。 |

1. **[OK]** を選択して、仮想ネットワークを作成します。
1. _[ネットワーク]_ タブに戻り、 _[パブリック IP]_ に **[なし]** が選択されていることを確認します。
1. _[管理]_ タブを選択し、 _[Diagnostic storage account]\(診断ストレージ アカウント\)_ で **[新規作成]** を選択して、新しいストレージ アカウントを作成します。
1. _[ID]_ 、 _[自動シャットダウン]_ 、 _[バックアップ]_ の各セクションは既定値のままにします。
1. _[Review + create]\(レビュー + 作成\)_ を選択します。 検証が完了した後、 **[作成]** を選択します。 VM 作成プロセスには数分かかります。

## <a name="configure-azure-bastion"></a>Azure Bastion を構成する

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) は、安全な RDP アクセスおよび SSH アクセスを Azure portal から直接仮想マシンに提供するフル マネージドの Azure サービスです。 Azure Bastion サービスを使用すれば、RDP アクセスに関連したネットワーク設定の構成が不要になります。

1. ポータルで、リソース グループ ビューの上部にある **[追加]** を選択します。
1. 検索フィールドに「**Bastion**」と入力します。
1. 検索結果にある **[Bastion]** を選択します。
1. **[作成]** を選択すると、新しい Azure Bastion リソースを作成する処理が開始されます。 まだ AzureBastionSubnet サブネットが存在しないため、 _[仮想ネットワーク]_ セクションにエラー メッセージが表示されていることがわかります。 サブネットは次の手順で作成します。 画像の下の表に示した設定を使用してください。

    >[!div class="mx-imgBorder"]
    >![Azure Bastion の作成を開始](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | _名前_ | myBastion | 新しい Bastion リソースの名前です。 |
    | _リージョン_ | 米国中北部 | ユーザーに近い[リージョン](https://azure.microsoft.com/regions/)、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |
    | _Virtual Network_ | myResourceGroup-vnet | Bastion リソースが作成される仮想ネットワークです。 |
    | _サブネット_ | AzureBastionSubnet | 新しい Bastion ホスト リソースがデプロイされるご利用の仮想ネットワークのサブネットです。 名前の値の **AzureBastionSubnet** を使用して、サブネットを作成する必要があります。 この値によって、Azure でリソースをデプロイするサブネットを把握できます。 **/27** かそれより大きいサブネットを使用する必要があります (/27、/26 など)。 |

    > [!NOTE]
    > Azure Bastion リソースを作成するための詳細なステップ バイ ステップ ガイドについては、[Azure Bastion ホストの作成](../bastion/tutorial-create-host-portal.md)に関するチュートリアルを参照してください。

1. Azure Bastion ホストのプロビジョニング先として使用できるサブネットを作成します。 **[サブネット構成の管理]** を選択すると新しいペインが表示され、そこで新しいサブネットを定義することができます。  新しいサブネットを作成するには、 **[+ サブネット]** を選択します。
1. サブネットの名前は **AzureBastionSubnet** とし、サブネットのプレフィックスは **/27** 以上にしてください。  **[OK]** を選択してサブネットを作成します。

    >[!div class="mx-imgBorder"]
    >![Azure Bastion ホストのサブネットを作成する](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. _[Bastion の作成]_ ページの利用可能なサブネットの一覧で、新たに作成した **AzureBastionSubnet** を選択します。

    >[!div class="mx-imgBorder"]
    >![特定のサブネットを使用して Azure Bastion ホストを作成する](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. **[確認と作成]** を選択します。 検証が完了した後、 **[作成]** を選択します。 Azure Bastion リソースが作成されるまでには数分かかります。

## <a name="create-an-azure-functions-app"></a>Azure Functions アプリを作成する

次の手順では、[従量課金プラン](consumption-plan.md)を使用して Azure にFunction App を作成します。 後からこのチュートリアルの中で、このリソースに自分の関数コードをデプロイします。

1. ポータルで、リソース グループ ビューの上部にある **[追加]** を選択します。
1. **[Compute] > [Function App]** の順に選択します。
1. _[基本]_ セクションで、下の表で指定されているFunction App の設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | _リソース グループ_ | myResourceGroup | このチュートリアルのすべてのリソースを含めるためのリソース グループを選択します。  Function App と VM に同じリソース グループを使用すれば、このチュートリアルの完了時にリソースを簡単にクリーンアップできます。 |
    | _関数アプリ名_ | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、a から z (大文字と小文字は区別されない)、0 から 9、および - です。 |
    | _発行_ | コード | コード ファイルまたは Docker コンテナーの発行オプション。 |
    | _ランタイム スタック_ | 優先言語 | お気に入りの関数プログラミング言語をサポートするランタイムを選択します。 |
    | _リージョン_ | 米国中北部 | ユーザーに近い[リージョン](https://azure.microsoft.com/regions/)、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |

    ページの下部にある **[次へ: Hosting >]\(次へ: ホスティング>\)** ボタンを選択します。
1. _[ホスティング]_ セクションの _[ストレージ アカウント]_ 、 _[オペレーティング システム]_ 、 _[プラン]_ で、次の表に従って適切な値を選択します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | _ストレージ アカウント_ | グローバルに一意の名前 | Function App で使用されるストレージ アカウントを作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](storage-considerations.md#storage-account-requirements)を満たしている必要があります。 |
    | _オペレーティング システム_ | 優先オペレーティング システム | オペレーティング システムは、ランタイム スタックの選択に基づいてあらかじめ選択されますが、必要に応じて設定を変更できます。 |
    | _プラン_ | 従量課金 | [ホスティング プラン](./functions-scale.md)は、Function App のスケーリング方法と各インスタンスが利用できるリソースを規定するものです。 |
1. **[確認および作成]** を選択して、アプリ構成の選択内容を確認します。
1. **[作成]** を選択して、Function App をプロビジョニングし、デプロイします。

## <a name="configure-access-restrictions"></a>アクセス制限を構成する

次の手順では、仮想ネットワーク上のリソースだけが関数を呼び出せるように[アクセス制限](../app-service/app-service-ip-restrictions.md)を構成します。

[プライベート サイト](functions-networking-options.md#private-endpoint-connections) アクセスは、Function App と特定の仮想ネットワークとの間に Azure 仮想ネットワークの[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を作成することによって実現します。 アクセス制限は、サービス エンドポイントを用いて導入されます。 特定の仮想ネットワーク内から発信されたトラフィックしか、指定されたリソースにアクセスできないようサービス エンドポイントによって制限されます。 指定されたリソースとは、このケースで言えば Azure 関数が該当します。

1. Function App 内で、 _[設定]_ セクションのヘッダーの下にある **[ネットワーク]** リンクを選択します。
1. _[ネットワーク]_ ページは、Azure Front Door、Azure CDN、アクセス制限を構成するための出発点となります。
1. プライベート サイト アクセスを構成するには、 **[アクセス制限を構成する]** を選択します。
1. _[アクセス制限]_ ページに表示されるのは既定の制限のみです。 既定では、Function App へのアクセスが一切制限されません。  プライベート サイト アクセス制限の構成を作成するには、 **[規則の追加]** を選択します。
1. _[Add Access Restriction]\(アクセス制限の追加\)_ ウィンドウで、新しい規則の _[名前]_ 、 _[優先度]_ 、 _[説明]_ を入力します。
1. _[種類]_ ボックスの一覧から **[仮想ネットワーク]** を選択し、先ほど作成した仮想ネットワークを選択してから、**Tutorial** サブネットを選択します。 
    > [!NOTE]
    > サービス エンドポイントの有効化には数分かかる場合があります。
1. _[アクセス制限]_ ページを見ると、新しい制限が存在することがわかります。 _[エンドポイントの状態]_ が Disabled から Provisioning になり、最終的に Enabled になるまでに数秒かかる場合があります。

    >[!IMPORTANT]
    > Function App にはそれぞれ、Function App のデプロイを管理するための [[高度なツール (Kudu)] サイト](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site)があります。 このサイトには、`<FUNCTION_APP_NAME>.scm.azurewebsites.net` 形式の URL からアクセスします。 Kudu サイトでアクセス制限を有効にすると、ローカルの開発者ワークステーションからプロジェクト コードがデプロイされなくなり、デプロイを実行するには仮想ネットワーク内にエージェントが必要になります。

## <a name="access-the-functions-app"></a>Function App にアクセスする

1. 先ほど作成したFunction App に戻ります。  _[概要]_ セクションで URL をコピーします。

    >[!div class="mx-imgBorder"]
    >![Function App の URL を取得する](./media/functions-create-private-site-access/access-function-overview.png)

    次に、仮想ネットワークの外部にあるコンピューターからFunction App にアクセスしようとすると、アクセスが禁止されていることを示す HTTP 403 ページが返されます。
1. リソース グループに戻り、以前に作成した仮想マシンを選択します。 VM からサイトにアクセスするためには、Azure Bastion サービス経由で VM に接続する必要があります。
1. **[接続]** を選択し、 **[Bastion]** を選択します。
1. 適切なユーザー名とパスワードを入力して仮想マシンにログインします。
1. **[接続]** を選択します。 新しいブラウザー ウィンドウがポップアップ表示されて、仮想マシンを対話的に操作できるようになります。
この VM は仮想ネットワーク経由でサイトにアクセスしているため、VM 上の Web ブラウザーからサイトにアクセスできます。  サイトには指定された仮想ネットワーク内からしかアクセスできませんが、パブリック DNS エントリは依然として存在します。

## <a name="create-a-function"></a>関数を作成する

このチュートリアルの次の手順では、HTTP によってトリガーされる Azure 関数を作成します。 HTTP GET または POST 経由で関数を呼び出したときに、"Hello, {name}" という応答が返されるようにします。  

1. 次のいずれかのクイックスタートに従って、Azure Functions アプリを作成、デプロイします。

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [コマンド ライン](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Azure Functions プロジェクトを発行するときに、このチュートリアルの中で作成したFunction App  リソースを選択します。
1. 関数がデプロイされていることを確認します。

    >[!div class="mx-imgBorder"]
    >![デプロイされている関数 (関数一覧)](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>関数を直接呼び出す

1. 関数へのアクセスをテストするには、関数の URL をコピーする必要があります。 デプロイされた関数を選択し、 **[Get Function Url]\(関数の URL の取得\)** を選択します。 次に、 **[コピー]** ボタンをクリックして、クリップボードに URL をコピーします。

    >[!div class="mx-imgBorder"]
    >![関数の URL をコピーする](./media/functions-create-private-site-access/get-function-url.png)

1. URL を Web ブラウザーに貼り付けます。 次に、仮想ネットワークの外部にあるコンピューターからFunction App にアクセスしようとすると、アプリへのアクセスが禁止されていることを示す HTTP 403 応答が返されます。

## <a name="invoke-the-function-from-the-virtual-network"></a>仮想ネットワークから関数を呼び出す

仮想ネットワーク上に構成されている VM の Web ブラウザーからは、(Azure Bastion サービスを使用して) 関数にアクセスすることができます。

>[!div class="mx-imgBorder"]
>![Azure Bastion 経由で Azure 関数にアクセスする](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ


> [!div class="nextstepaction"]
> [関数のネットワーク オプションに関する詳細情報](./functions-networking-options.md)