---
title: Bridge to Kubernetes への移行
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Azure Dev Spaces から Bridge to Kubernetes への移行プロセスについて説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Bridge to Kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888523"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Bridge to Kubernetes への移行

> [!IMPORTANT]
> Azure Dev Spaces は 2023 年 10 月 31 日に廃止されます。 お客様は、クライアント開発者ツールである Bridge to Kubernetes を使用するように移行する必要があります。
>
> Azure Dev Spaces の目的は、ユーザーに Kubernetes での開発に徐々に慣れてもらうことでした。 Azure Dev Spaces のアプローチにおける大きなトレードオフは、Docker と Kubernetes の構成、および Kubernetes のデプロイの概念を理解するために、ユーザーに余計な負担がかかることでした。 時間の経過と共に、Azure Dev Spaces のアプローチでは、Kubernetes での内部ループ開発の速度が効果的に低下しないことも明らかになりました。 Bridge to Kubernetes では、内部ループ開発の速度が効果的に低下し、ユーザーにとって余計な負担が回避されます。
>
> 核となる目標は変わりません。大規模アプリケーションのコンテキストでマイクロサービス コードの開発、テスト、デバッグを行うための最適なエクスペリエンスを構築することです。

Bridge to Kubernetes は、Azure Dev Spaces で動作する多くの開発シナリオの代わりに使用できるより軽量の手段を提供します。 Bridge to Kubernetes は、[Visual Studio][vs]  と  [Visual Studio Code][vsc] の拡張機能を使用したクライアント側のみのエクスペリエンスです。  

Bridge to Kubernetes を使用すると、確立された Kubernetes アプリケーションにローカル開発ワークステーション上で実行されているサービスを含めることができるため、開発エクスペリエンスに役立ちます。 Dev Spaces とは異なり、Bridge to Kubernetes では、Docker や Kubernetes の構成を作成する必要がなくなり内部ループの複雑さが軽減されるため、開発者は純粋にマイクロサービス コードのビジネス ロジックに集中することができます。

Bridge to Kubernetes は、Kubernetes 環境の依存関係や既存の構成を消費しながら、開発用コンピューター上で実行されているコードで反復処理するのに役立ちます。 これに対して、Azure Dev Spaces では、サービスをリモートでデバッグしたりコードで反復処理したりできるようになる前に、マイクロサービスを Kubernetes 環境にデプロイします。

この記事では、Azure Dev Spaces と Bridge to Kubernetes の比較のほか、Azure Dev Spaces から Bridge to Kubernetes に移行するための手順を示します。

## <a name="development-approaches"></a>開発方法

![開発のアプローチ](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces では、Kubernetes 開発者が AKS クラスター内で直接実行されているコードを操作できるようになったため、デプロイされた環境に似ていないローカル環境の必要性はなくなりました。 このアプローチによって開発の一定の側面は改善されましたが、Azure Dev Spaces の使用を開始できるようになるまでに、Docker、Kubernetes、Helm などの追加の概念を学習して維持するという前提条件も導入されました。

Bridge to Kubernetes を使用すると、開発者は開発用コンピューター上で直接作業しながら、クラスターの残りの部分を操作することができます。 このアプローチでは、開発用コンピューター上で直接実行されているコードの親しみやすさと速度を利用する一方、クラスターによって提供される依存関係と環境を共有しています。 このアプローチではまた、Kubernetes での実行から来る忠実性とスケーリングも利用しています。

## <a name="feature-comparison"></a>機能の比較

Azure Dev Spaces と Bridge to Kubernetes には同様の機能がありますが、異なる領域もいくつかあります。

| 要件  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | 15 の Azure リージョン内 | 任意の AKS サービス リージョン    |
| **Security** |
| クラスターで必要なセキュリティ アクセス  | AKS クラスターの共同作成者  | Kubernetes RBAC - デプロイの更新   |
| 開発用コンピューターで必要なセキュリティ アクセス  | N/A  | ローカル管理者/sudo   |
| **使いやすさ** |
| Kubernetes および Docker アーティファクトからの独立  | いいえ  | はい   |
| 変更の自動ロールバック、事後デバッグ  | いいえ  | はい   |
| **サポートされているクライアント ツール** |
| Visual Studio 2019 での動作  | はい  | はい   |
| Visual Studio Code での動作  | はい  | はい   |
| CLI での動作  | はい  | いいえ   |
| **オペレーティング システムの互換性** |
| Windows 10 での動作  | はい  | はい  |
| Linux での動作  | はい  | はい  |
| macOS での動作  | はい  | はい  |
| **Capabilities** |
| 開発者の分離またはチーム開発  | はい  | はい  |
| 環境変数の選択的な上書き  | いいえ  | はい  |
| Dockerfile および Helm グラフの作成  | はい  | いいえ  |
| Kubernetes へのコードの永続的なデプロイ  | はい  | いいえ  |
| Kubernetes ポッドでのリモート デバッグ  | はい  | いいえ  |
| Kubernetes に接続されたローカル デバッグ  | いいえ  | はい  |
| 同じワークステーションでの複数のサービスの同時デバッグ  | はい  | はい  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes 内部ループの開発

Azure Dev Spaces と Bridge to Kubernetes の最も大きな違いは、コードを実行する場所です。 Azure Dev Spaces は、マイクロサービス コードの開発およびデバッグに役立ちますが、そのコードをクラスター内で実行する必要があります。 Bridge to Kubernetes を使用すると、Kubernetes で実行されているより大規模なアプリケーションのコンテキストを維持しながら、開発用コンピューター上で直接マイクロサービス コードを開発およびデバッグできます。 Bridge to Kubernetes によって Kubernetes クラスターの境界が拡張され、ローカル プロセスで Kubernetes から構成を継承できるようになります。

![内部ループの開発](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Bridge to Kubernetes を使用するときは、開発用コンピューターとクラスターの間にネットワーク接続が確立されます。 この接続の有効期間中は、サービスへの要求を開発用コンピューターにリダイレクトするプロキシが Kubernetes のデプロイの代わりにクラスターに追加されます。 接続を解除すると、アプリケーションのデプロイは、クラスター上で実行されているそのデプロイの元のバージョンの使用に戻ります。 このアプローチは、コードがクラスターに同期され、ビルドされてから実行される Azure Dev Spaces の動作方法とは異なります。 また、Azure Dev Spaces ではコードがロールバックされません。

Bridge to Kubernetes には、それぞれのデプロイ方法には関係なく、Kubernetes で実行されているアプリケーションを操作できる柔軟性があります。 CI/CD を使用してアプリケーションをビルドして実行する場合も、確立されたツールまたはカスタム スクリプトのどちらを使用しているかには関係なく、引き続き Bridge to Kubernetes を使用してコードを開発およびデバッグできます。

> [!TIP]
>  [Microsoft Kubernetes 拡張機能][kubernetes-extension]を使用すると、IntelliSense で Kubernetes マニフェストを迅速に開発できるため、Helm グラフのスキャフォールディングに役立ちます。  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Azure Dev Spaces から Bridge to Kubernetes に移行する

1. Visual Studio を使用している場合は、Visual Studio IDE をバージョン 16.7 以上に更新し、[Visual Studio Marketplace][vs-marketplace] から Bridge to Kubernetes 拡張機能をインストールします。 Visual Studio Code を使用している場合は、[Bridge to Kubernetes 拡張機能][vsc-marketplace]をインストールします。
1. Azure portal または [Azure Dev Spaces CLI][azds-delete] を使用して Azure Dev Spaces コントローラーを無効にします。
1. [Azure Cloud Shell](https://shell.azure.com) を使用します。 または、bash がインストールされている Mac、Linux、または Windows では、bash シェル プロンプトを開きます。 コマンド ライン環境で次のツールが使用可能であることを確認します。Azure CLI、docker、kubectl、curl、tar、gunzip。
1. コンテナー レジストリを作成するか、既存のものを使用します。 Azure でコンテナー レジストリを作成するには、[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) を使用するか、[Docker Hub](https://hub.docker.com/) を使用します。 Azure Cloud Shell を使用する場合、Docker イメージをホストするために使用できるのは Azure Container Registry だけです。
1. 移行スクリプトを実行して、Azure Dev Spaces アセットを Bridge to Kubernetes アセットに変換します。 このスクリプトは、Bridge to Kubernetes と互換性のある新しいイメージを作成し、指定されたレジストリにアップロードした後、[Helm](https://helm.sh) を使用して、そのイメージでクラスターを更新します。 リソース グループ、AKS クラスターの名前、およびコンテナー レジストリを指定する必要があります。 次に示すように、他のコマンド ライン オプションもあります。

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   このスクリプトでは、次のフラグがサポートされています。

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. *azds.yaml* 内のカスタマイズ (環境変数の設定など) を、プロジェクトの *values.yml* ファイルに手動で移行します。
1. (省略可能) プロジェクトから `azds.yaml` ファイルを削除します。
1. デプロイされたアプリケーションで Bridge to Kubernetes を構成します。 Visual Studio での Bridge to Kubernetes の使用の詳細については、[Visual Studio での Bridge to Kubernetes の使用][use-btk-vs]に関するページを参照してください。 VS Code の場合は、[VS Code での Bridge to Kubernetes の使用][use-btk-vsc]に関するページを参照してください。
1. 新しく作成された Bridge to Kubernetes デバッグ/起動プロファイルを使用してデバッグを開始します。
1. 必要に応じてスクリプトを再実行して、クラスターに再デプロイすることができます。

## <a name="team-development-in-a-shared-cluster"></a>共有クラスターでのチーム開発

Bridge to Kubernetes では、開発者固有のルーティングを使用することもできます。 Azure Dev Spaces のチーム開発シナリオでは、複数の Kubernetes 名前空間を使用し、親名前空間と子名前空間の概念を使用してサービスをアプリケーションの残りの部分から分離します。 Bridge to Kubernetes でも同じ機能が提供されますが、パフォーマンス特性の向上が、同じアプリケーション名前空間内で得られます。

Bridge to Kubernetes と Azure Dev Spaces ではどちらも、HTTP ヘッダーが存在し、かつそれをアプリケーション全体にわたって伝達する必要があります。 既に Azure Dev Spaces でのヘッダーの伝達を処理するようにアプリケーションを構成している場合は、そのヘッダーを更新する必要があります。 Azure Dev Spaces から Bridge to Kubernetes に移行するには、構成されているヘッダーを *azds-route-as* から *kubernetes-route-as* に更新します。

## <a name="evaluate-bridge-to-kubernetes"></a>Bridge to Kubernetes を評価する

クラスターで Azure Dev Spaces を無効にする前に Bridge to Kubernetes を実験したい場合、最も簡単な方法は新しいクラスターの使用です。 Azure Dev Spaces と Bridge to Kubernetes を同じクラスターで同時に使用しようとすると、その両方でルーティング機能を使用しているときに問題が発生します。

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Visual Studio を使用して Bridge to Kubernetes を評価する

1. Visual Studio IDE をバージョン 16.7 以上に更新し、[Visual Studio Marketplace][vs-marketplace] から Bridge to Kubernetes 拡張機能をインストールします。
1. 新しい AKS クラスターを作成し、アプリケーションをデプロイします。 また、[サンプル アプリケーション][btk-sample-app]を使用することもできます。
1. デプロイされたアプリケーションで Bridge to Kubernetes を構成します。 Visual Studio での Bridge to Kubernetes の使用の詳細については、[Bridge to Kubernetes の使用][use-btk-vs]に関するページを参照してください。
1. 新しく作成された Bridge to Kubernetes デバッグ プロファイルを使用して Visual Studio でのデバッグを開始します。

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Visual Studio Code を使用して Bridge to Kubernetes を評価する

1. [Bridge to Kubernetes 拡張機能][vsc-marketplace]をインストールします。
1. 新しい AKS クラスターを作成し、アプリケーションをデプロイします。 また、[サンプル アプリケーション][btk-sample-app]を使用することもできます。
1. デプロイされたアプリケーションで Bridge to Kubernetes を構成します。 Visual Studio Code での Bridge to Kubernetes の使用の詳細については、[Bridge to Kubernetes の使用][use-btk-vsc]に関するページを参照してください。
1. 新しく作成された Bridge to Kubernetes 起動プロファイルを使用して Visual Studio でのデバッグを開始します。

## <a name="next-steps"></a>次のステップ

Bridge to Kubernetes のしくみの詳細について学習します。

> [!div class="nextstepaction"]
> [Bridge to Kubernetes のしくみ][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
