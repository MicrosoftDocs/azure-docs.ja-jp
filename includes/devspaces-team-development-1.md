---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: dresteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181613"
---
# <a name="team-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したチーム開発

このチュートリアルでは、開発者のチームが Dev Spaces を使用して同じ Kubernetes クラスター内で同時に共同作業する方法を学習します。

## <a name="learn-about-team-development"></a>チーム開発について学ぶ
ここまでは、アプリケーションを扱う唯一の開発者であるかのようにアプリケーションのコードを実行しました。 このセクションでは、Azure Dev Spaces によるチーム開発の効率化について説明します。
* 共有開発空間で、または必要に応じて個別の開発空間で作業することで、同じ環境の中で開発者チームが作業できるようにします。
* 各開発者が他の開発者の妨げになることを心配せずに、単独でコードを反復処理できるようにします。
* モックを作成したり、依存関係をシミュレートしたりする必要なく、コードをコミットする前に、エンド ツー エンドでコードをテストする。

### <a name="challenges-with-developing-microservices"></a>マイクロサービスの開発に伴う課題
現時点では、サンプル アプリケーションはそれほど複雑ではありません。 しかし、実際の開発では、さらに多くのサービスが追加され、開発チームが拡大するにつれて、すぐに課題が生じます。 開発のためにすべてをローカルで実行するのは非現実的になる可能性があります。

* 開発用マシンには、必要なすべてのサービスを同時に実行するのに十分なリソースがない可能性があります。
* 一部のサービスには、パブリックに到達可能であることが必要である可能性があります。 たとえば、サービスには、Webhook に応答するエンドポイントが必要になるかもしれません。
* サービスのサブセットを実行したい場合は、すべてのサービス間の完全な依存関係階層を把握しておく必要があります。 これを判断するのは、特にサービスの数が増えるのに応じて困難になる可能性があります。
* サービスの依存関係の多くをシミュレートしたり、モックを作成したりする開発者もいます。 これは有効な方法ですが、これらのモックの管理はすぐに開発コストに影響を与える可能性があります。 さらに、この方法では開発環境が運用環境と大きく異なって見えるようになり、特定しにくいバグが知らない間に発生するおそれもあります。
* その結果、あらゆる種類の統合テストの実行が困難になります。 実際には、統合テストはコミット後にのみ行われる場合があります。つまり、問題は開発サイクルの後半に発生します。

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>共有開発空間で作業する
Azure Dev Spaces を使用して、Azure に "*共有*" 開発空間をセットアップできます。 各開発者は、アプリケーションの自分の担当部分だけに集中でき、自分のシナリオが依存する他のすべてのサービスとクラウド リソースが既に含まれている開発空間で、"*事前コミット コード*" を繰り返し開発できます。 依存関係は常に最新の状態であり、開発者は運用環境を反映した方法で作業を行います。

### <a name="work-in-your-own-space"></a>自分のスペースで作業する
サービスのコードを開発するときには、チェックインできる状態になるまで、多くの場合、コードは良好な状態ではありません。 引き続きコードの調整とテストを繰り返し、ソリューションを試します。 Azure Dev Spaces では、**スペース**という概念を導入しています。このスペースにより、チーム メンバーの妨げになることを心配せずに、単独で作業を行うことができます。

## <a name="use-dev-spaces-for-team-development"></a>チーム開発に Dev Spaces を使用する
*webfrontend* -> *mywebapi* サンプル アプリケーションを使用して、これらのアイデアを具体的な例で示します。 次のシナリオを考えてみましょう。開発者である Scott は、*mywebapi* サービス "*のみ*" を変更する必要があるとします。 *webfrontend* は、Scott が更新作業の一環として変更する必要はありません。

Dev Spaces を使用 "_しない_" 場合、Scott が更新プログラムを開発およびテストするための方法として次のいくつかの方法が考えられます。そして、そのいずれも理想的ではありません。
* すべてのコンポーネントをローカルで実行する。 これには、Docker がインストールされたより強力な開発用マシン (と場合によっては MiniKube) が必要です。
* Kubernetes クラスター上の独立した名前空間ですべてのコンポーネントを実行する。 *webfrontend* は変更されないため、これはクラスター リソースの無駄です。
* *mywebapi* のみを実行し、手動で REST 呼び出しを実行してテストする。 これでは、完全なエンド ツー エンドのフローをテストできません。
* 開発者が *mywebapi* の別のインスタンスに要求を送信できるようにする、開発に重点を置いたコードを *webfrontend* に追加する。 この場合、*webfrontend* サービスが複雑になります。

### <a name="set-up-your-baseline"></a>ベースラインを設定する
最初に、サービスのベースラインをデプロイする必要があります。 このデプロイは "前回の既知の正常な状態" を表すため、ローカル コードとチェックイン バージョンの動作を簡単に比較できます。 次に、このベースラインに基づいて子空間を作成して、より大きなアプリケーションのコンテキスト内で *mywebapi* に対する変更をテストできるようにします。

1. [Dev Spaces サンプル アプリケーション](https://github.com/Azure/dev-spaces)を複製します。`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. リモート ブランチ *azds_updates* をチェックアウトします。`git checkout -b azds_updates origin/azds_updates`
1. _dev_ 空間を選択します。`azds space select --name dev` 親 dev 空間を選択するように要求されたら、 _\<なし\>_ を選択します。
1. _mywebapi_ ディレクトリに移動し、`azds up -d` を実行します。
1. _webfrontend_ ディレクトリに移動し、`azds up -d` を実行します。
1. `azds list-uris` を実行して _webfrontend_ のパブリック エンドポイントを確認します

> [!TIP]
> 上記の手順を使用すると、手動でベースラインを設定できます。ただし、チームでは CI/CD を使用して、コミットされたコードでベースラインを自動的に最新の状態に保つことをお勧めします。
>
> [Azure DevOps を使用した CI/CD の設定に関するガイド](../articles/dev-spaces/how-to/setup-cicd.md)を参考にして、次の図のようなワークフローを作成します。
>
> ![サンプルの CI/CD の図](../articles/dev-spaces/media/common/ci-cd-complex.png)

ベースラインはこの時点で実行されています。 `azds list-up --all` コマンドを実行すると、次のような出力が表示されます。

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

[DevSpace] 列に、両方のサービスが _dev_ という名前の空間で実行されていることが示されます。 パブリック URL を開き、Web アプリに移動するすべてのユーザーは、両方のサービスを介して実行される、チェックイン済みのコード パスを呼び出します。 ここで、_mywebapi_ の開発を続行するとします。 開発環境を利用している他の開発者の邪魔にならないように、コードを変更してテストする方法はあるでしょうか? それには、独自のスペースを設定することです。

### <a name="create-a-dev-space"></a>開発スペースを作成する
_mywebapi_ の独自のバージョンを _dev_ 以外の空間で実行するために、次のコマンドを使用して独自のスペースを作成できます。

```cmd
azds space select --name scott
```

メッセージが表示されたら、**親開発空間**として _dev_ を選択します。 これは、新しい _dev/scott_ 空間が _dev_ 空間から派生することを意味します。 これがテストでどのように役立つかは、すぐわかります。

ここでは入門用の仮定に合わせて、新しい空間には _scott_ という名前を使用しました。これは、だれが作業しているのかが同僚にわかるようにするためです。 しかし、これには任意の名前を付けることができ、_sprint4_、_demo_ のように柔軟な意味を持たせることができます。 いずれにしても、_dev_ は、このアプリケーションの一部を使用するすべての開発者にとってのベースラインとなります。

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

`azds space list` コマンドを実行すると、開発環境にあるすべてのスペースが一覧表示されます。 _[Selected]\(選択\)_ 列には、現在選択している空間が示されます (true/false)。 この例では、空間の作成時に _dev/scott_ という名前の空間が自動的に選択されました。 `azds space select` コマンドを使用して、いつでも別のスペースを選択することができます。

実際の動作を見てみましょう。