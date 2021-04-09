---
title: Azure Dev Spaces のしくみ
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Azure Dev Spaces を動作させるプロセスについて説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963705"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces のしくみ

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Kubernetes アプリケーションの開発には課題が生じる可能性があります。 Docker と Kubernetes の構成ファイルが必要です。 アプリケーションをローカルでテストし、その他の依存サービスとやり取りする方法を理解する必要があります。 開発者のチームを採用して、開発とテストを複数のサービスで同時に処理しなければならない場合があります。

Azure Dev Spaces には、Kubernetes アプリケーションを迅速に反復処理してデバッグし、チームと共同作業を行うための複数の方法が用意されています。 この記事では、Azure Dev Spaces の機能とそのしくみについて説明します。

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Kubernetes アプリケーションを迅速に反復処理し、デバッグする

Azure Dev Spaces を使用すると、AKS クラスターのコンテキストで Kubernetes アプリケーションを開発、テスト、反復する手間が軽減されます。 この作業の削減により、開発者は、Kubernetes で実行するようにサービスを構成することではなく、アプリケーションのビジネス ロジックに集中することができます。

### <a name="bridge-to-kubernetes"></a>Bridge to Kubernetes

Bridge to Kubernetes を使用すると、開発用コンピューターを Kubernetes クラスターに接続し、クラスター上で実行されているかのように開発用コンピューター上でコードを実行したりデバッグしたりすることができます。 Bridge to Kubernetes は、開発用コンピューターとクラスターとの間でリモート エージェントとしてトラフィックをリダイレクトするポッドをクラスター上で実行することにより、接続されたクラスターとの間でトラフィックをリダイレクトします。 このようにトラフィックをリダイレクトすることで、開発用コンピューター上のコードと、クラスターで実行されているサービスとが、同じクラスター内にあるかのように通信できます。 開発用コンピューターを Kubernetes クラスターに接続する方法の詳細については、「[Bridge to Kubernetes のしくみ][how-it-works-bridge-to-kubernetes]」を参照してください。

### <a name="run-your-code-in-aks"></a>AKS でコードを実行する

Azure Dev Spaces を使用すると、開発用コンピューターと AKS クラスターの間でトラフィックをリダイレクトするだけでなく、コードを AKS で直接構成してすぐに実行することができます。 Visual Studio、Visual Studio Code、または Azure Dev Spaces CLI を使用すると、Azure Dev Spaces によりコードがクラスターにアップロードされて、ビルドされ、実行されます。 Azure Dev spaces では、コードの変更をインテリジェントに同期し、必要に応じてサービスを再起動して変更を反映することもできます。 コードを実行している間、ビルド ログと HTTP トレースがクライアントにストリーミング バックされるため、進行状況を監視し、問題を診断できます。 また、Azure Dev Spaces を使用すると、Visual Studio や Visual Studio Code のデバッガーを、Java、Node.js、.NET Core の各サービスにアタッチすることもできます。 詳細については、「[Azure Dev Spaces のためにプロジェクトを準備する方法][how-it-works-prep]」、「[Azure Dev Spaces でのコード実行のしくみ][how-it-works-up]」、「[Azure Dev Spaces でのコードのリモート デバッグのしくみ][how-it-works-remote-debugging]」を参照してください。

## <a name="team-development"></a>チーム開発

Azure Dev Spaces を使用すると、チームは、中断することなく、同じ AKS クラスターでアプリケーションの作業を生産的に行うことができます。

### <a name="intelligent-routing-between-dev-spaces"></a>開発スペース間のインテリジェントなルーティング

Azure Dev Spaces を使用すると、チームは、クラウド ネイティブ アプリケーションが実行されている単一の AKS クラスターを共有し、他の開発者スペースに干渉することなく、チームが開発、テスト、およびデバッグできる分離された開発スペースを作成できます。 ルート開発スペースでは、アプリケーションのベースライン バージョンが実行されます。 チーム メンバーは、アプリケーションの開発、テスト、デバッグ変更のための独立した子開発スペースを、ルート スペースに基づいて作成します。 子開発スペースでは、Dev Spaces のルーティング機能により、子開発スペースと親開発スペースで実行されているサービスの間で要求をルーティングすることができます。 このルーティングにより、開発者は、親空間の依存サービスを再利用しながら、独自のバージョンのサービスを実行できます。 各子スペースには独自の一意の URL があり、コラボレーションのために他のユーザーが共有したりアクセスしたりすることができます。 Azure Dev Spaces でのルーティングの動作の詳細については、「[Azure Dev Spaces でルーティングがどのように機能するか][how-it-works-routing]」を参照してください。

### <a name="live-testing-an-open-pull-request"></a>開いている pull request のライブ テスト

また、GitHub Actions と Azure Dev Spaces を使用すると、マージ前にクラスター内の pull request で直接、アプリケーションに対する変更をテストすることもできます。 Azure Dev Spaces では、アプリケーションのレビュー バージョンをクラスターに自動的にデプロイできるので、作成者だけでなく他のチーム メンバーも、アプリケーション全体のコンテキストで変更をレビューできるようになります。 Azure Dev Spaces のルーティング機能を使用すると、アプリケーションのこのレビュー バージョンも、他の開発スペースに影響を与えることなく、クラスターにデプロイされます。 これらすべての機能を使用して、pull request を確実に承認およびマージできます。 GitHub Actions と Azure Dev Spaces の例については、「[GitHub のアクションと Azure Kubernetes Service][pr-flow]」を参照してください。

## <a name="next-steps"></a>次のステップ

実際にローカル開発用コンピューターを AKS クラスターに接続する方法については、「[開発用マシンを AKS クラスターに接続する][connect]」を参照してください。


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development