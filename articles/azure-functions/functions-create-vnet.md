---
title: Azure Functions を Azure 仮想ネットワークに統合する
description: 関数を Azure 仮想ネットワークに接続する方法を説明するステップ バイ ステップ チュートリアル
ms.topic: article
ms.date: 4/23/2020
ms.openlocfilehash: f50c923104fdfcf26f400f20f0de66a82eb3d245
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387525"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>チュートリアル: Functions を Azure 仮想ネットワークに統合する

このチュートリアルでは、Azure Functions を使用して Azure 仮想ネットワーク内のリソースに接続する方法について説明します。 インターネットと、仮想ネットワークで WordPress を実行している VM の両方へのアクセス権を持つ関数を作成します。

> [!div class="checklist"]
> * Premium プランの Function App を作成する
> * 仮想ネットワーク内の VM に WordPress サイトをデプロイする
> * Function App を仮想ネットワークに接続する
> * WordPress リソースにアクセスする関数プロキシを作成する
> * 仮想ネットワーク内から WordPress ファイルを要求する

## <a name="topology"></a>トポロジ

次の図は、作成するソリューションのアーキテクチャを示します。

 ![仮想ネットワーク統合の UI](./media/functions-create-vnet/topology.png)

Premium プランで実行されている関数には、VNet 統合機能を含む、Azure App Service の Web アプリと同じホスティング機能があります。 トラブルシューティングや高度な構成など、VNet 統合の詳細については、「[アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、IP アドレスの割り当てとサブネット化を理解することが重要です。 [アドレスの割り当てとサブネット化の基本を取り上げたこの記事](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)から開始できます。 多くの記事およびビデオをオンラインで利用できます。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-function-app-in-a-premium-plan"></a>Premium プランの Function App を作成する

まず、[Premium プラン]で Function App を作成します。 このプランはサーバーレス スケールを提供しながら、仮想ネットワーク統合をサポートします。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

右上隅のピン アイコンを選択して、ダッシュ ボードに Function App をピン留めすることができます。 ピン留めすると、VM の作成後に、この Function App に戻るのが容易になります。

## <a name="create-a-vm-inside-a-virtual-network"></a>仮想ネットワーク内に VM を作成する

次に、仮想ネットワーク内で WordPress を実行する、構成済みの VM を作成します (Jetware による [WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure))。 WordPress VM は、その低コストと利便性のために使用されます。 この同じシナリオは、REST API、App Service Environment、他の Azure サービスなど、仮想ネットワーク内のあらゆるリソースにも使用できます。 

1. ポータルで、左側のナビゲーション ウィンドウで **[+ リソースの作成]** を選択し、検索フィールドに「`WordPress LEMP7 Max Performance`」と入力して Enter キーを押します。

1. 検索結果で **[Wordpress LEMP Max Performance]** を選択します。 **ソフトウェア プラン**として **Wordpress LEMP Max Performance for CentOS** のソフトウェア プランを選択し、 **[作成]** を選択します。

1. **[基本]** タブで、画像の下の表で指定されているように VM 設定を使用します。

    ![VM を作成するための [基本] タブ](./media/functions-create-vnet/create-vm-1.png)

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **[リソース グループ](../azure-resource-manager/management/overview.md)**  | myResourceGroup | `myResourceGroup` または Function App で作成したリソース グループを選択します。 Function App、WordPress VM、およびホスティング プランに同じリソース グループを使用すれば、このチュートリアルの完了時にリソースを簡単にクリーンアップできます。 |
    | **仮想マシン名** | VNET Wordpress | VM 名は、リソース グループ内で一意である必要があります |
    | **[リージョン](https://azure.microsoft.com/regions/)** | (ヨーロッパ) 西ヨーロッパ | 近くのリージョン、または VM にアクセスする関数に近いリージョンを選択します。 |
    | **[サイズ]** | B1s | **[サイズの変更]** を選択してから、B1s 標準イメージを選択します。これには 1 つの vCPU と 1 GB のメモリがあります。 |
    | **認証の種類** | Password | パスワード認証を使用するには、 **[ユーザー名]** 、安全な **[パスワード]** 、および **[パスワードの確認]** を指定する必要があります。 このチュートリアルでは、トラブルシューティングが必要な場合を除き、VM にサインインする必要はありません。 |

1. **[ネットワーク]** タブを選択し、[仮想ネットワークの構成] の下で **[新規作成]** を選択します。

1. **[仮想ネットワークの作成]** で、画像の下にある表の設定を使用します。

    ![[VM の作成] の [ネットワーク] タブ](./media/functions-create-vnet/create-vm-2.png)

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **名前** | myResourceGroup-vnet | 仮想ネットワークに対して生成される既定の名前を使用できます。 |
    | **アドレス範囲** | 10.10.0.0/16 | 仮想ネットワークの単一のアドレス範囲を使用します。 |
    | **サブネット名** | Tutorial-Net | サブネットの名前です。 |
    | **アドレス範囲** (サブネット) | 10.10.1.0/24   | サブネット サイズは、サブネットに追加できるインターフェイスの数を定義します。 このサブネットは、WordPress サイトで使用されます。  `/24` サブネットは、254 のホスト アドレスを提供します。 |

1. **[OK]** を選択して、仮想ネットワークを作成します。

1. **[ネットワーク]** タブに戻り、 **[パブリック IP]** に **[なし]** を選択します。

1. **[管理]** タブを選択し、続いて **[診断ストレージ アカウント]** で、Function App で作成したストレージ アカウントを選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了した後、 **[作成]** を選択します。 VM 作成プロセスには数分かかります。 作成した VM は、仮想ネットワークにのみアクセスできます。

1. VM が作成された後、 **[リソースに移動]** を選択して新しい VM のページを表示し、続いて **[設定]** の下で **[ネットワーク]** を選択します。

1. **パブリック IP** がないことを確認します。 **プライベート IP** を書き留めます。これは Function App から VM に接続するために使用します。

    ![VM でのネットワーク設定](./media/functions-create-vnet/vm-networking.png)

これで、完全に仮想ネットワーク内にデプロイされた WordPress サイトが表示されます。 このサイトにはパブリック インターネットからはアクセスできません。

## <a name="connect-your-function-app-to-the-virtual-network"></a>Function App を仮想ネットワークに接続する

仮想ネットワーク内の VM で実行している WordPress サイトでは、その仮想ネットワークに Function App を接続できるようになりました。

1. 新しい Function App で、左側のメニューの **[ネットワーク]** を選択します。

1. **[VNet 統合]** の下の **[ここをクリックして構成]** を選択します。

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="Function App で [ネットワーク] を選択する":::

1. **[VNET 統合]** ページで、 **[VNet の追加]** を選択します。

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="VNet 統合プレビューを追加する":::

1. **[ネットワーク機能の状態]** で、画像の下にある表の設定を使用します。

    ![関数アプリの仮想ネットワークを定義する](./media/functions-create-vnet/networking-3.png)

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | この仮想ネットワークは前に作成したものです。 |
    | **サブネット** | 新しいサブネットを作成します | 使用する Function App 用の仮想ネットワークにサブネットを作成します。 VNet 統合は、空のサブネットを使用するように構成する必要があります。 関数が VM とは異なるサブネットを使用していても問題ありません。 仮想ネットワークは、2 つのサブネット間のトラフィックを自動的にルーティングします。 |
    | **サブネット名** | Function-Net | 新しいサブネットの名前です。 |
    | **仮想ネットワーク アドレス ブロック** | 10.10.0.0/16 | WordPress サイトで使用される、同じアドレス ブロックを選択します。 1 つのアドレス ブロックだけを定義してください。 |
    | **アドレス範囲** | 10.10.2.0/24   | サブネット サイズは、Premium プランの Function App がスケールアウトできるインスタンスの合計数を制限します。 この例では、254 のホスト アドレスが使用可能な `/24` サブネットを使用します。 このサブネットはオーバープロビジョニングされていますが、計算は簡単です。 |

1. **[OK]** を選択して、サブネットを追加します。 **[VNet 統合]** ページおよび **[ネットワーク機能の状態]** ページを閉じ、Function App のページに戻ります。

Function App から WordPress サイトが実行している仮想ネットワークにアクセスできるようになりました。 次に、[Azure Functions プロキシ](functions-proxies.md)を使用して、WordPress サイトからファイルを返します。

## <a name="create-a-proxy-to-access-vm-resources"></a>VM リソースにアクセスするためのプロキシを作成する

VNet 統合を有効にすると、仮想ネットワークで実行している VM に要求を転送するプロキシを、Function App で作成できます。

1. Function App で、左側のメニューから **[プロキシ]** を選択し、次に **[追加]** を選択します。 画像の下の表に示したプロキシ設定を使用してください。

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="プロキシ設定を定義する":::

    | 設定  | 推奨値  | 説明      |
    | -------- | ---------------- | ---------------- |
    | **名前** | Plant (植物) | 名前には任意の値を指定できます。 プロキシを識別するために使用されます。 |
    | **ルート テンプレート** | /plant | VM リソースにマップするルート。 |
    | **バックエンド URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | `<YOUR_VM_IP>` を、以前に作成した WordPress VM の IP アドレスに置き換えます。 このマッピングは、サイトから 1 つのファイルを返します。 |

1. **[作成]** を選択して、Function App にプロキシを追加します。

## <a name="try-it-out"></a>試してみる

1. ブラウザーで、**バックエンド URL** として使用した URL にアクセスしてみます。 予想どおり、要求がタイムアウトします。タイムアウトは、WordPress サイトが仮想ネットワークのみに接続され、インターネットには接続されていないために起こります。

1. 新しいプロキシから **プロキシ URL** 値をコピーして、ブラウザーのアドレス バーに貼り付けます。 返された画像は、仮想ネットワーク内部で実行する WordPress サイトからのものです。

    ![WordPress サイトから返された植物の画像ファイル](./media/functions-create-vnet/plant.png)

Function App は、インターネットと仮想ネットワークの両方に接続されています。 プロキシはパブリック インターネット経由で要求を受信し、その要求を接続された仮想ネットワークに転送するシンプルな HTTP プロキシとして機能します。 その後、プロキシによって中継された応答がインターネット経由でパブリックに返されます。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、WordPress サイトは、Function App でプロキシを使用して呼び出される API として機能します。 このシナリオは設定および視覚化が簡単なので、適切なチュートリアルになります。 仮想ネットワーク内にデプロイされた他の任意の API を使用できます。 仮想ネットワーク内にデプロイされた API を呼び出すコードで関数を作成することもできます。 より現実的なシナリオは、仮想ネットワーク内にデプロイされた SQL Server インスタンスを呼び出すためにデータ クライアント API を使用する関数です。

Premium プランで実行されている関数は、PremiumV2 プラン上の Web Apps と同じ基盤となる App Service インフラストラクチャを共有します。 [Azure App Service 内の Web アプリ](../app-service/overview.md)のドキュメントはすべて、Premium プランの関数に適用されます。

> [!div class="nextstepaction"]
> [関数のネットワーク オプションに関する詳細情報](./functions-networking-options.md)

[Premium プラン]: functions-scale.md#premium-plan
