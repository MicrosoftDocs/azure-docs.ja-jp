---
title: Azure Dev Spaces と共に CI/CD を使用する
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー
ms.openlocfilehash: 01e1401c5054eb56d4e2313b5e03ce5a36d1b301
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704061"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Azure Dev Spaces と共に CI/CD を使用する

この記事では、Dev Spaces を有効にした状態で、Azure Kubernetes Service (AKS) への継続的インテグレーション/継続的配置 (CI/CD) を設定する方法について説明します。 AKS への CI/CD によって、コミットされたコードがソース リポジトリにプッシュされるたびに、アプリの更新プログラムを自動的にデプロイすることが可能になります。 Dev Spaces を有効にしたクラスターと組み合わせて CI/CD を利用すると、チームが操作するアプリケーションのベースラインを最新の状態に保つことができるので便利です。

![サンプルの CI/CD の図](../media/common/ci-cd-simple.png)

この記事では、Azure DevOps を使って説明していますが、Jenkins、TeamCity などの CI/CD システムにも同じ概念が適用されます。

## <a name="prerequisites"></a>前提条件
* [Azure Dev Spaces が有効になっている Azure Kubernetes Service (AKS) クラスター](../get-started-netcore.md)
* [インストール済みの Azure Dev Spaces CLI](upgrade-tools.md)
* [1 つのプロジェクトを含む Azure DevOps 組織](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry [管理者アカウント](../../container-registry/container-registry-authentication.md#admin-account)の詳細が利用可能
* [お使いの Azure コンテナー レジストリからプルするように AKS クラスターを承認する](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>サンプル コードのダウンロード
時間を節約するために、サンプル コードの GitHub リポジトリのフォークを作成しましょう。 [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces ) に移動して、 **[フォーク]** を選択します。 フォーク プロセスが完了すると、リポジトリのフォーク済みバージョンがローカルに**複製**されます。 既定で _master_ ブランチがチェックアウトされますが、_azds_updates_ ブランチに時間を節約するためのいくつかの変更を組み入れてあり、フォーク時にこのブランチも転送されているはずです。 _azds_updates_ ブランチには、Dev Spaces チュートリアルのセクション内で手動実行するように示されている更新プログラムと、CI/CD システムのデプロイを効率化するために事前作成されたいくつかの YAML および JSON ファイルが含まれています。 `git checkout -b azds_updates origin/azds_updates` のようなコマンドを使用して、ローカル リポジトリに _azds_updates_ ブランチをチェックアウトできます。

## <a name="dev-spaces-setup"></a>Dev Spaces の設定
`azds space select` コマンドを使用して、_dev_ という新しい空間を作成します。 _dev_ 空間は、コードの変更をプッシュするために、CI/CD パイプラインによって使用されます。 また、_dev_ に基づく "_子空間_" を作成するためにも使用されます。

```cmd
azds space select -n dev
```

親 dev 空間を選択するように要求されたら、" _\<なし\>_ " を選択します。

自分の開発スペースが作成された後は、ホストのサフィックスを確認する必要があります。 Azure Dev Spaces イングレス コントローラーのホスト サフィックスを表示するには、`azds show-context` コマンドを使用します。

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

上の例では、ホスト サフィックスは _fedcba098.eus.azds.io_ です。 この値は、後でリリース定義を作成するときに使用します。

開発者が _dev_ から "_子空間_" を作成して大規模なアプリのコンテキスト内にある分離された変更をテストできるように、_dev_ 空間には常に最新状態のリポジトリが含まれます。 この概念については、Dev Spaces のチュートリアルで詳しく説明しています。

## <a name="creating-the-build-definition"></a>ビルド定義の作成
Web ブラウザーで Azure DevOps チーム プロジェクトを開き、" _[パイプライン]_ " セクションに移動します。 最初に、Azure DevOps サイトの右上にあるプロファイル写真をクリックして、[プレビュー機能] ウィンドウを開き、" _[YAML パイプライン作成の新しいエクスペリエンス]_ " を無効にします。

![[プレビュー機能] ウィンドウを開く](../media/common/preview-feature-open.png)

無効にするオプション:

![[YAML パイプライン作成の新しいエクスペリエンス] オプション](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps の "_YAML パイプライン作成の新しいエクスペリエンス_" プレビュー機能は、この時点では、事前定義されたビルド パイプラインの作成と競合します。 事前定義されたビルド パイプラインをデプロイするために、今は無効にしておく必要があります。

_azds_updates_ ブランチには、*mywebapi* および *webfrontend* に必要なビルド手順を定義した簡単な [Azure パイプラインの YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) を組み込んでいます。

選択した言語に応じて、パイプラインの YAML は `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml` と類似のパスにチェックインされています。

このファイルからパイプラインを作成するには:
1. DevOps プロジェクトのメイン ページ上で、[パイプライン] > [ビルド] の順に移動します。
1. **新しい**ビルド パイプラインを作成するオプションを選択します。
1. ソースとして **[GitHub]** を選択し、必要に応じて自分の GitHub アカウントを使って承認して、dev 空間のサンプル アプリ リポジトリのフォーク済みバージョンから _azds_updates_ ブランチを選択します。
1. テンプレートとして、 **[コードとしての構成]** または **[YAML]** を選択します。
1. この時点で、ビルド パイプラインの構成ページが表示されています。 前述したように、 **[...]** ボタンを使用して **[YAML ファイル パス]** の言語固有パスに移動します。 たとえば、「 `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml` 」のように入力します。
1. **[変数]** タブに移動します。
1. _dockerId_ を変数として手動で追加します。[Azure Container Registry 管理者アカウント](../../container-registry/container-registry-authentication.md#admin-account)のユーザー名です (記事の「前提条件」に記載されています)。
1. _dockerPassword_ を変数として手動で追加します。[Azure Container Registry 管理者アカウント](../../container-registry/container-registry-authentication.md#admin-account)のパスワードです。 セキュリティのために、_dockerPassword_ は必ずシークレットとして指定 (鍵のアイコンを選択) します。
1. **[保存してキューに登録]** を選択します。

これで、GitHub フォークの _azds_updates_ ブランチにプッシュされた任意の更新プログラムに対して *mywebapi* および *webfrontend* を自動的にビルドする CI ソリューションが作成されました。 Azure portal に移動し、自分の Azure コンテナー レジストリを選択して、 **[リポジトリ]** タブを参照することで、Docker イメージがプッシュされたことを確認できます。イメージがビルドされて、コンテナー レジストリに表示されるまで、数分かかる場合があります。

![Azure Container Registry リポジトリ](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>リリース定義の作成

1. DevOps プロジェクトのメイン ページ上で、[パイプライン] > [リリース] の順に移動します。
1. まだリリース定義を含まないまったく新しい DevOps プロジェクトに取り組んでいる場合は、先へ進む前に、まず空のリリース定義を作成する必要があります。 既存のリリース定義ができるまで、[インポート] オプションは UI に表示されません。
1. 左側にある **[+ 新規]** ボタンをクリックして、 **[パイプラインのインポート]** をクリックします。
1. **[参照]** をクリックし、プロジェクトから `samples/release.json` を選択します。
1. Click **OK**. [パイプライン] ウィンドウがリリース定義の編集ページと共に読み込まれたことが確認できます。 また、引き続き構成する必要があるクラスター固有の詳細を示した赤い警告アイコンがいくつかあることが確認できます。
1. [パイプライン] ウィンドウの左側で、 **[成果物の追加]** バブルをクリックします。
1. **[ソース]** ドロップダウンで、前に作成したビルド パイプラインを選択します。
1. **[既定のバージョン]** では、 **[ビルド パイプライン既定のブランチ (タグ付き) からの最新バージョン]** を選択します。
1. **[タグ]** は空のままにします。
1. **[ソース エイリアス]** を `drop` に設定します。 **[ソース エイリアス]** の値は事前定義済みのリリース タスクによって使用されるため、設定しておく必要があります。
1. **[追加]** をクリックします。
1. 次に、新しく作成された `drop` アーティファクト ソース上にある、以下に示すような稲妻アイコンをクリックします。

    ![リリース アーティファクトの継続的配置の設定](../media/common/release-artifact-cd-setup.png)
1. **[継続的配置トリガー]** を有効にします。
1. **[パイプライン]** の隣の **[タスク]** タブをポイントし、_dev_ をクリックして、_dev_ ステージ タスクを編集します。
1. **[接続の種類]** で **[Azure Resource Manager]** が選択されていることを確認します。 3 つのドロップダウン コントロールが赤で強調表示されていることがわかります。![リリース定義の設定](../media/common/release-setup-tasks.png)
1. Azure Dev Spaces で使用している Azure サブスクリプションを選択します。 **[承認]** をクリックすることが必要な場合もあります。
1. Azure Dev Spaces で使用するリソース グループとクラスターを選択します。
1. **[エージェント ジョブ]** をクリックします。
1. **[エージェント プール]** で **[Hosted Ubuntu 1604]\(ホストされている Ubuntu 1604\)** を選択します。
1. 上部にある **[タスク]** セレクターをポイントし、_prod_ をクリックして _prod_ ステージ タスクを編集します。
1. **[接続の種類]** で **[Azure Resource Manager]** が選択されていることを確認します。 Azure Dev Spaces で使用している Azure サブスクリプション、リソース グループ、およびクラスターを選択します。
1. **[エージェント ジョブ]** をクリックします。
1. **[エージェント プール]** で **[Hosted Ubuntu 1604]\(ホストされている Ubuntu 1604\)** を選択します。
1. **[変数]** タブをクリックして、リリースの変数を更新します。
1. **DevSpacesHostSuffix** の値を、**UPDATE_ME** から自分のホスト サフィックスに更新します。 ホスト サフィックスは、前に `azds show-context` コマンドを実行したときに表示されたものです。
1. 右上にある **[保存]** をクリックし、 **[OK]** をクリックします。
1. **[+ リリース]** ([保存] ボタンの横にある)、 **[リリースの作成]** の順にクリックします。
1. **[アーティファクト]** で、ビルド パイプラインから最新のビルドが選択されていることを確認します。
1. **Create** をクリックしてください。

これで、自動化されたリリース プロセスが開始され、_dev_ の最上レベルの空間にある Kubernetes クラスターに、*mywebapi* および *webfrontend* のチャートがデプロイされます。 Azure DevOps の Web ポータル上で、リリースの進行状況を監視できます。

1. **[パイプライン]** の **[リリース]** セクションに移動します。
1. サンプル アプリケーションのリリース パイプラインをクリックします。
1. 最新のリリースの名前をクリックします。
1. **[ステージ]** の **dev** ボックスをポイントし、 **[ログ]** をクリックします。

すべてのタスクが完了すると、リリースが行われます。

> [!TIP]
> "*UPGRADE FAILED: timed out waiting for the condition*" のようなエラー メッセージでリリースが失敗した場合、[Kubernetes ダッシュボードを使用して](../../aks/kubernetes-dashboard.md)、ご自身のクラスター内のポッドを調べてみてください。 ポットがエラーになっており、エラー メッセージが "*Failed to pull image "azdsexample.azurecr.io/mywebapi:122": rpc error: code = Unknown desc = Error response from daemon:Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: unauthorized: authentication required*" のように始まっている場合、クラスターがお使いの Azure コンテナー レジストリからプルするための承認を受けていないことが原因になっている可能性があります。 「[お使いの Azure コンテナー レジストリからプルするように AKS クラスターを承認する](../../container-registry/container-registry-auth-aks.md)」の前提条件を完了していることを確認してください。

これで、Dev Spaces サンプル アプリの GitHub フォークに対応する CI/CD パイプラインを完全に自動化できました。 コードをコミットしてプッシュするたびに、ビルド パイプラインでは *mywebapi* および *webfrontend* イメージをビルドして、お使いのカスタム ACR インスタンスにプッシュします。 その後、リリース パイプラインでは、アプリごとの Helm チャートを Dev Spaces が有効になっているクラスター上の _dev_ 空間にデプロイします。

## <a name="accessing-your-dev-services"></a>_dev_ サービスへのアクセス
デプロイ後は、`http://dev.webfrontend.fedcba098.eus.azds.io` のようなパブリック URL を使って、*webfrontend* の _dev_ バージョンにアクセスできます。 `azds list-uri` コマンドを実行して、この URL を調べることができます。 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>運用環境へのデプロイ

このチュートリアルで作成した CI/CD システムを使用して、特定のリリースを _prod_ に手動で昇格するには:
1. **[パイプライン]** の **[リリース]** セクションに移動します。
1. サンプル アプリケーションのリリース パイプラインをクリックします。
1. 最新のリリースの名前をクリックします。
1. **[ステージ]** の **prod** ボックスをポイントし、 **[デプロイ]** をクリックします。
    ![運用環境への昇格](../media/common/prod-promote.png)
1. **[ステージ]** の **prod** ボックスを再びポイントし、 **[ログ]** をクリックします。

すべてのタスクが完了すると、リリースが行われます。

CI/CD パイプラインの _Prod_ ステージでは、Dev Spaces イングレス コントローラーではなくロード バランサーを使用して、_prod_ サービスへのアクセスが提供されます。 _prod_ ステージにデプロイされるサービスには、DNS 名の代わりに IP アドレスでアクセスできます。 運用環境では、独自のイングレス コントローラーを作成し、独自の DNS 構成に基づいてサービスをホストすることができます。

webfrontend サービスの IP アドレスを確認するには、 **[Print webfrontend public IP]\(webfrontend のパブリック IP を印刷する\)** ステップをクリックして、ログ出力を展開します。 ログ出力に表示される IP アドレスを使用して、**webfrontend** アプリケーションにアクセスします。

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>運用環境における Dev Spaces のインストルメンテーション
Dev Spaces のインストルメンテーションは、アプリケーションの通常の操作を妨害 "_しない_" ように設計されていますが、Dev Spaces が有効になっていない Kubernetes 名前空間内で運用環境のワークロードを実行することをお勧めします。 このタイプの Kubernetes 名前空間を使用する場合は、`kubectl` CLI を使用して運用環境の名前空間を作成するか、CI/CD システムによって最初の Helm デプロイ中にこれを作成できるようにしておく必要があります。 空間を "_選択する_" か、Dev Spaces ツールを使用して空間を作成すると、Dev Spaces インストルメンテーションがその名前空間に追加されます。

次に、機能のデプロイ、'dev' 環境、"_および_" 運用環境のすべてを単一の Kubernetes クラスター内でサポートする名前空間の構造のサンプルを示します。

![名前空間の構造のサンプル](../media/common/cicd-namespaces.png)

> [!Tip]
> `prod` 空間を既に作成済みであり、単純に Dev Spaces インストルメンテーションから (削除せずに) 除外したい場合、次の Dev Spaces CLI コマンドを使ってこれを実行できます。
>
> `azds space remove -n prod --no-delete`
>
> これを実行した後に `prod` 名前空間にあるすべてのポッドの削除が必要になる場合があります。そのため、Dev Spaces インストルメンテーションなしでの再作成が可能になっています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Dev Spaces を使用したチーム開発について学ぶ](../team-development-netcore.md)