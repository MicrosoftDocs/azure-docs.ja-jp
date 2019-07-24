---
title: .NET Core と Visual Studio を使用した Azure Dev Spaces でのチーム開発
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 58807aa5a540de6eaa9a337caa0c34fee9408296
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361439"
---
# <a name="team-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したチーム開発

このチュートリアルでは、開発者のチームが Dev Spaces を使用して同じ Kubernetes クラスター内で同時に共同作業する方法を学習します。

## <a name="learn-about-team-development"></a>チーム開発について学ぶ

ここまでは、アプリの開発に取り組んでいる開発者が 1 人だけであるかのようにアプリケーションのコードを実行していました。 このセクションでは、Azure Dev Spaces によるチーム開発の効率化について説明します。
* 共有開発空間で、または必要に応じて個別の開発空間で作業することで、同じ環境の中で開発者チームが作業できるようにします。
* 各開発者が他の開発者の妨げになることを心配せずに、単独でコードを反復処理できるようにします。
* モックを作成したり、依存関係をシミュレートしたりする必要なく、コードをコミットする前に、エンド ツー エンドでコードをテストする。

### <a name="challenges-with-developing-microservices"></a>マイクロサービスの開発に伴う課題
現時点では、サンプル アプリケーションは複雑ではありません。 しかし、実際の開発では、さらに多くのサービスが追加され、開発チームが拡大するにつれて、すぐに課題が生じます。

* 開発用マシンには、必要なすべてのサービスを同時に実行するのに十分なリソースがない可能性があります。
* 一部のサービスには、パブリックに到達可能であることが必要である可能性があります。 たとえば、サービスには、Webhook に応答するエンドポイントが必要になるかもしれません。
* サービスのサブセットを実行したい場合は、すべてのサービス間の完全な依存関係階層を把握しておく必要があります。 この階層を判断するのは、特にサービスの数が増えるのに応じて困難になる可能性があります。
* サービスの依存関係の多くをシミュレートしたり、モックを作成したりする開発者もいます。 これは有効な方法ですが、これらのモックの管理はすぐに開発コストに影響を与える可能性があります。 さらに、この方法では開発環境が運用環境と異なって見えるようになり、特定しにくいバグが発生するおそれもあります。
* その結果、あらゆる種類の統合テストの実行が困難になります。 実際には、統合テストはコミット後にのみ行われる場合があります。つまり、問題は開発サイクルの後半に発生します。

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>共有開発空間で作業する
Azure Dev Spaces を使用して、Azure に "*共有*" 開発空間をセットアップできます。 各開発者は、アプリケーションの自分の担当部分だけに集中でき、自分のシナリオが依存する他のすべてのサービスとクラウド リソースが既に含まれている開発空間で、"*事前コミット コード*" を繰り返し開発できます。 依存関係は常に最新の状態であり、開発者は運用環境を反映した方法で作業を行います。

### <a name="work-in-your-own-space"></a>自分のスペースで作業する
サービスのコードを開発するときには、チェックインできる状態になるまで、多くの場合、コードは良好な状態ではありません。 引き続きコードの調整とテストを繰り返し、ソリューションを試します。 Azure Dev Spaces では、**スペース**という概念を導入しています。このスペースにより、チーム メンバーの妨げになることを心配せずに、単独で作業を行うことができます。

## <a name="use-dev-spaces-for-team-development"></a>チーム開発に Dev Spaces を使用する
*webfrontend* -> *mywebapi* サンプル アプリケーションを使用して、これらのアイデアを具体的な例で示します。 次のシナリオを考えてみましょう。開発者である Scott は、*mywebapi* サービス "*のみ*" を変更する必要があるとします。 *webfrontend* は、Scott が更新作業の一環として変更する必要はありません。

Dev Spaces を使用 "_しない_" 場合、Scott が更新プログラムを開発およびテストするための方法として次のいくつかの方法が考えられます。そして、そのいずれも理想的ではありません。
* すべてのコンポーネントをローカルで実行する。これには、Docker がインストールされたより強力な開発用マシン (と場合によっては MiniKube) が必要です。
* Kubernetes クラスター上の独立した名前空間ですべてのコンポーネントを実行する。 *webfrontend* は変更されないため、独立した名前空間を使用するのはクラスター リソースの無駄です。
* *mywebapi* のみを実行し、手動で REST 呼び出しを実行してテストする。 この種のテストでは、完全なエンド ツー エンドのフローはテストされません。
* 開発者が *mywebapi* の別のインスタンスに要求を送信できるようにする、開発に重点を置いたコードを *webfrontend* に追加する。 このコードを追加すると、*webfrontend* サービスが複雑になります。

### <a name="set-up-your-baseline"></a>ベースラインを設定する
最初に、サービスのベースラインをデプロイする必要があります。 このデプロイは "前回の既知の正常な状態" を表すため、ローカル コードとチェックイン バージョンの動作を簡単に比較できます。 次に、このベースラインに基づいて子空間を作成して、より大きなアプリケーションのコンテキスト内で *mywebapi* に対する変更をテストできるようにします。

1. [Dev Spaces サンプル アプリケーション](https://github.com/Azure/dev-spaces)を複製します。`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. リモート ブランチ *azds_updates* をチェックアウトします。`git checkout -b azds_updates origin/azds_updates`
1. 両方のサービスの F5/デバッグ セッションをすべて閉じます。ただし、Visual Studio ウィンドウでプロジェクトを開いたままにしておきます。
1. _mywebapi_ プロジェクトがある Visual Studio ウィンドウに切り替えます。
1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[プロパティ]** を選択します。
1. 左側の **[デバッグ]** タブを選択して、Azure Dev Spaces の設定を表示します。
1. サービスに対して F5 キーまたは Ctrl + F5 キーを押したときに使用される空間を作成するために、**[変更]** を選択します。
1. [スペース] ボックスの一覧の **[\<Create New Space…\>]\(<新しいスペースの作成...>\)** を選択します。
1. 親空間が **\<なし\>** に設定されていることを確認し、空間名「**dev**」を入力します。 [OK] をクリックします。
1. Ctrl + F5 キーを押して、デバッガーをアタッチせずに _mywebapi_ を実行します。
1. _webfrontend_ プロジェクトがある Visual Studio ウィンドウに切り替え、Ctrl + F5 キーを押してこれも同様に実行します。

> [!Note]
> Ctrl + F5 キーを押して Web ページが最初に表示された後に、ブラウザーを更新することが必要な場合があります。

> [!TIP]
> 上記の手順を使用すると、手動でベースラインを設定できます。ただし、チームでは CI/CD を使用して、コミットされたコードでベースラインを自動的に最新の状態に保つことをお勧めします。
>
> [Azure DevOps を使用した CI/CD の設定に関するガイド](how-to/setup-cicd.md)を参考にして、次の図のようなワークフローを作成します。
>
> ![サンプルの CI/CD の図](media/common/ci-cd-complex.png)

パブリック URL を開き、Web アプリに移動するすべてのユーザーは、既定の _dev_ 空間を使用して両方のサービスを介して実行される、作成済みのコード パスを呼び出すことになります。 ここでは、*mywebapi* の開発を続けるとします。開発空間を使用している他の開発者の作業を妨げずに続行するにはどうすればよいのでしょうか。 それには、独自のスペースを設定することです。

### <a name="create-a-new-dev-space"></a>新しい開発空間を作成する
Visual Studio 内から、F5 キーまたは Ctrl + F5 キーを押してサービスを実行するときに使用される追加のスペースを作成できます。 スペースには任意の名前を付けることができます。内容に合わせて自由に名前を付けることができます (例:  _sprint4_ または _demo_)。

新しいスペースを作成するには、次の手順を実行します。
1. *mywebapi* プロジェクトがある Visual Studio ウィンドウに切り替えます。
2. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[プロパティ]** を選択します。
3. 左側の **[デバッグ]** タブを選択して、Azure Dev Spaces の設定を表示します。
4. ここから、F5 キーまたは Ctrl + F5 キーを押したときに使用されるクラスターやスペースを変更または作成できます。 *以前に作成した Azure Dev Space が選択されていることを確認します。*
5. [スペース] ボックスの一覧の **[\<Create New Space…\>]\(<新しいスペースの作成...>\)** を選択します。

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. **[空間の追加]** ダイアログで、親の空間を **dev** に設定し、新しい空間の名前を入力します。 誰が作業しているスペースであるかを同僚がわかるように、新しいスペースに自分の名前 (例: "scott") を使用できます。 Click **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. これで、AKS クラスターと、プロジェクトのプロパティ ページで選択した新しいスペースが表示されます。

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>*mywebapi* のコードを更新する

1. *mywebapi* プロジェクトで、`Controllers/ValuesController.cs` ファイル内の `string Get(int id)` メソッドのコードを次のように変更します。
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. この更新されたコード ブロックにブレークポイントを設定します (既に設定されている場合があります)。
3. F5 キーを押して _mywebapi_ サービスを開始します。これにより、選択した空間を使用してクラスターでサービスが開始されます。 この場合の選択した空間は _scott_ です。

さまざまなスペースのしくみを理解するうえで役立つ図を次に示します。 紫色のパスは、_dev_ 空間を使用した要求を示しています。これは、URL の先頭に空間が追加されていない場合に使用される既定のパスです。 ピンク色のパスは、_dev/scott_ 空間を使用した要求を示しています。

![](media/common/Space-Routing.png)

Azure Dev Spaces のこの組み込み機能を使用すると、共有環境でコードをエンド ツー エンドでテストできます。各開発者が自分のスペースにサービスの完全なスタックを再作成する必要はありません。 このガイドの以前の手順で示したように、このルーティングではアプリ コードで伝達ヘッダーを転送する必要があります。

### <a name="test-code-running-in-the-devscott-space"></a>_dev/scott_ 空間で実行されているコードをテストする
*mywebapi* の新しいバージョンと *webfrontend* をテストするには、ブラウザーで *webfrontend* のパブリック アクセス ポイントの URL (たとえば、 http://dev.webfrontend.123456abcdef.eus.azds.io) を開き、About ページに移動します。 "Hello from webfrontend and Hello from mywebapi" という元のメッセージが表示されます。

URL に "scott.s." の部分を追加して、 http://scott.s.dev.webfrontend.123456abcdef.eus.azds.io になるようにし、ブラウザーを更新します。 *mywebapi* プロジェクトで設定したブレークポイントに到達します。 F5 キーを押して続行すると、"Hello from webfrontend and mywebapi now says something new" という新しいメッセージがブラウザーに表示されます。 これは、*mywebapi* で更新したコードのパスが _dev/scott_ 空間で実行されているためです。

最新の変更を常に含む _dev_ 空間があり、このチュートリアルのセクションで説明されているようにアプリケーションが DevSpace の空間ベースのルーティングを利用するように設計されていると仮定した場合、大規模なアプリケーションのコンテキスト内で新機能をテストする際に Dev Spaces がいかに役立つかは容易に想像できます。 "_すべての_" サービスをプライベート空間にデプロイする代わりに、_dev_ から派生するプライベート空間を作成し、実際に作業しているサービスのみを "アップ" することができます。 Dev Spaces ルーティング インフラストラクチャでは、_dev_ 空間で実行されている最新バージョンを既定で使用する一方で、プライベート空間で検出できる限りのサービスを利用して、残りの処理が行われます。 さらにすばらしいことに、"_複数の_" 開発者が互いに影響を与えることなく、自分の空間で同時に、異なるサービスをアクティブに開発することができます。

### <a name="well-done"></a>お疲れさまでした。
ファースト ステップ ガイドを修了しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
> * コンテナー内のコードを繰り返し開発する。
> * 2 つのサービスを個別に開発し、Kubernetes の DNS サービス検索を使用して別のサービスを呼び出す。
> * チーム環境でコードを生産的に開発してテストする。
> * Dev Spaces を使用して機能のベースラインを確立し、より大規模なマイクロサービス アプリケーションのコンテキスト内で、分離された変更を簡単にテストする

Azure Dev Spaces の概要を理解できたら、[開発空間をチーム メンバーと共有](how-to/share-dev-spaces.md)し、共同作業の容易さについてチームの理解を深めましょう。

## <a name="clean-up"></a>クリーンアップ
すべての開発スペースとその内部で実行されているサービスを含め、クラスター上の Azure Dev Spaces インスタンスを完全に削除するには、`az aks remove-dev-spaces` コマンドを使用します。 この操作は元に戻せないことに注意してください。 Azure Dev Spaces のサポートをクラスターに再度追加することはできますが、始めからやり直すようになります。 古いサービスとスペースは復元されません。

次の例では、アクティブなサブスクリプションの Azure Dev Spaces コントローラーを一覧表示し、リソース グループ "myaks-rg" の AKS クラスター "myaks" に関連付けられている Azure Dev Spaces コントローラーを削除します。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
