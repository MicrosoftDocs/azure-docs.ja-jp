---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181614"
---
### <a name="run-the-service"></a>サービスを実行する

F5 キーを押して (またはターミナル ウィンドウで「`azds up`」と入力して)、サービスを実行します。 新たに選択した _dev/scott_ 空間で、サービスが自動的に実行されます。 `azds list-up` を実行すると、サービスが独自の空間で実行されていることを確認できます。

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

*mywebapi* のインスタンスが _dev/scott_ 空間で実行されていることがわかります。 _dev_ で実行されているバージョンもまだ実行されていますが、リストされていません。

`azds list-uris` を実行して、現在の空間の URL をリストします。

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

*webfrontend* のパブリック アクセス ポイント URL に、*scott.s* というプレフィックスが付いていることがわかります。 この URL は _dev/scott_ 空間に固有のものです。 この URL プレフィックスによって、_dev/scott_ バージョンのサービスに要求をルーティングするようイングレス コントローラーに命令が伝えられます。 この URL を使った要求が Dev Spaces によって処理されるとき、イングレス コントローラーはまず、その要求を _dev/scott_ 空間の *webfrontend* サービスにルーティングするよう試みます。 それに失敗した場合は、フォールバックとして、_dev_ 空間の *webfrontend* サービスに要求がルーティングされます。 また、localhost URL が存在することにも注目してください。Kubernetes の "*ポート フォワーディング*" 機能を使用して localhost でサービスにアクセスするためのものです。 Azure Dev Spaces における URL とルーティングの詳細については、「[How Azure Dev Spaces works and is configured (Azure Dev Spaces のしくみと構成方法)](../articles/dev-spaces/how-dev-spaces-works.md)」を参照してください。



![空間のルーティング](../articles/dev-spaces/media/common/Space-Routing.png)

Azure Dev Spaces のこの組み込み機能を使用すれば、共有空間内でコードをテストできます。その場合、各開発者が、自分の空間内にサービスをフル スタックで再作成する必要はありません。 このルーティングを行うには、このガイドの前の手順で示したように、アプリ コードで伝達ヘッダーを転送する必要があります。

### <a name="test-code-in-a-space"></a>空間内でコードをテストする
*mywebapi* の新しいバージョンと *webfrontend* をテストするには、ブラウザーで *webfrontend* のパブリック アクセス ポイントの URL を開き、About ページに移動します。 すると、新しいメッセージが表示されます。

ここで、URL の "scott.s" の部分を削除し、 ブラウザーをリフレッシュします。 (_dev_ で実行されている *mywebapi* バージョンによる) 以前の動作が表示されます。

最新の変更を常に含む _dev_ 空間があり、このチュートリアルのセクションで説明されているようにアプリケーションが DevSpace の空間ベースのルーティングを利用するように設計されていると仮定した場合、大規模なアプリケーションのコンテキスト内で新機能をテストする際に Dev Spaces がいかに役立つかは容易に想像できます。 "_すべての_" サービスをプライベート空間にデプロイする代わりに、_dev_ から派生するプライベート空間を作成し、実際に作業しているサービスのみを "アップ" することができます。 Dev Spaces ルーティング インフラストラクチャでは、_dev_ 空間で実行されている最新バージョンを既定で使用する一方で、プライベート空間で検出できる限りのサービスを利用して、残りの処理が行われます。 さらにすばらしいことに、"_複数の_" 開発者が互いに影響を与えることなく、自分の空間で同時に、異なるサービスをアクティブに開発することができます。

### <a name="well-done"></a>お疲れさまでした。
ファースト ステップ ガイドを修了しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
> * コンテナー内のコードを繰り返し開発する。
> * 2 つのサービスを個別に開発し、Kubernetes の DNS サービス検索を使用して別のサービスを呼び出す。
> * チーム環境でコードを生産的に開発してテストする。
> * Dev Spaces を使用して機能のベースラインを確立し、より大規模なマイクロサービス アプリケーションのコンテキスト内で、分離された変更を簡単にテストする

Azure Dev Spaces の概要を理解できたら、[開発空間をチーム メンバーと共有](../articles/dev-spaces/how-to/share-dev-spaces.md)し、共同作業を始めましょう。

## <a name="clean-up"></a>クリーンアップ
すべての開発スペースとその内部で実行されているサービスを含め、クラスター上の Azure Dev Spaces インスタンスを完全に削除するには、`az aks remove-dev-spaces` コマンドを使用します。 この操作は元に戻せないことに注意してください。 Azure Dev Spaces のサポートをクラスターに再度追加することはできますが、始めからやり直すようになります。 古いサービスとスペースは復元されません。

次の例では、アクティブなサブスクリプションの Azure Dev Spaces コントローラーを一覧表示し、リソース グループ "myaks-rg" の AKS クラスター "myaks" に関連付けられている Azure Dev Spaces コントローラーを削除します。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```