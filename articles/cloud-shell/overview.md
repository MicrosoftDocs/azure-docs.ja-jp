---
title: "Azure Cloud Shell (プレビュー) の概要 | Microsoft Docs"
description: "Azure Cloud Shell について概説します。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Azure Cloud Shell (プレビュー) の概要
Azure Cloud Shell は、Azure リソースを管理するための、ブラウザーでアクセスできるインタラクティブなシェルです。
Azure Cloud Shell には、業務に最適のシェル操作を選択できる柔軟性があります。
Linux ユーザーは Bash を、Windows ユーザーは PowerShell を選ぶことができます。

![Cloud Shell の Bash](media/overview/overview-bash-pic.png)

![Cloud Shell の PowerShell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Features (機能)
### <a name="browser-based-shell-experience"></a>ブラウザーベースのシェル環境
Cloud Shell は、Azure の管理タスクを視野に入れて開発されたブラウザーベースのコマンド ライン環境です。
Cloud Shell を利用することで、ローカル コンピューターに拘束されることなくクラウドならではの作業が可能となります。

### <a name="choice-of-preferred-shell-experience"></a>最適なシェル エクスペリエンスの選択
Azure Cloud Shell には、業務に最適のシェル操作を選択できる柔軟性があります。
Linux ユーザーは Bash を、Windows ユーザーは PowerShell を選ぶことができます。

### <a name="pre-configured-azure-workstation"></a>事前構成済みの Azure ワークステーション
Cloud Shell は、作業のスピードを高めるために、広く使われているコマンド ライン ツールや言語サポートと共にプレインストールされます。

すべてのツールの一覧については、[Bash エクスペリエンス](features.md#tools)および [PowerShell エクスペリエンス](features-powershell.md#tools)をご覧ください。

### <a name="automatic-authentication"></a>自動認証
Azure CLI 2.0 ですばやくリソースにアクセスできるよう、Cloud Shell では各セッションで安全に自動認証が行われます。

### <a name="connect-your-azure-file-storage"></a>Azure File Storage の接続
Cloud Shell マシンは一時的なものであるため、お使いの $Home ディレクトリを永続化するためには、Azure ファイル共有を `clouddrive` としてマウントする必要があります。
Cloud Shell の初回起動時に、リソース グループとストレージ アカウント、ファイル共有を自動的に作成するよう促されます。 これは 1 回限りの作業であり、それ以降はすべてのセッションで自動的に接続されます。 1 つのファイル共有をマップすると、Cloud Shell の Bash と PowerShell の両方で使われます。

#### <a name="create-new-storage"></a>新しいストレージの作成
![](media/overview/basic-storage.png)

ローカル冗長ストレージ (LRS) アカウントと Azure ファイル共有が自動的に作成されます。 Bash 環境と PowerShell 環境の両方を選ぶと、Azure ファイル共有は両方の環境で使われます。 ストレージのコストは通常どおりに適用されます。

以下の 3 つのリソースが自動的に作成されます。
1. `cloud-shell-storage-<region>` という名前のリソース グループ
2. `cs<uniqueGuid>` という名前のストレージ アカウント
3. `cs-<user>-<domain>-com-<uniqueGuid>` という名前のファイル共有

> [!Note]
> Cloud Shell の Bash は、`$Home` を永続化するための既定の 5 GB ディスク イメージも作成します。 SSH キーなど、$Home ディレクトリ内のすべてのファイルが、マウントされたファイル共有に格納されたユーザー ディスク イメージに永続化されます。 $Home ディレクトリおよびマウントされたファイル共有へのファイルの保存時に、ベスト プラクティスを適用します。

#### <a name="use-existing-resources"></a>既存のリソースの使用
![](media/overview/advanced-storage.png)

既にあるリソースを Cloud Shell に関連付けるため、詳細オプションが用意されています。
ストレージのセットアップを促す画面が表示されたら、[詳細設定の表示] をクリックして、追加オプションを表示します。
ドロップダウン ボックスには、割り当てられている Cloud Shell リージョンとローカル/グローバル冗長ストレージ アカウントが一覧表示されます。

[Cloud Shell ストレージ、ファイル共有の更新、ファイルのアップロード/ダウンロードについてはこちらを参照してください。](persisting-shell-storage.md)

## <a name="concepts"></a>概念
* Cloud Shell は、ユーザーごとにセッション単位で一時的に提供されるマシン上で実行されます。
* Cloud Shell は、無操作状態で 20 分経過するとタイムアウトとなります。
* Cloud Shell は、ファイル共有を接続した状態でのみアクセスできます。
* Cloud Shell は、Bash と PowerShell の両方に同じファイル共有を使います。
* Cloud Shell には、ユーザー アカウントごとに 1 台のマシンが割り当てられます。
* アクセス許可は、標準の Linux ユーザーとして設定されます (Bash)。

[Cloud Shell の Bash](features.md) と [Cloud Shell の PowerShell](features-powershell.md) の機能に関する詳細をご覧ください。

## <a name="examples"></a>例
* スクリプトを使って Azure の管理タスクを自動化します
* Azure Portal と Azure コマンドライン ツールを使って同時にリソースを管理します
* Azure CLI 2.0 または Azure PowerShell コマンドレットを試用します

[Cloud Shell の Bash](quickstart.md) および [Cloud Shell の PowerShell](quickstart-powershell.md) のクイックスタートで、これらの例を試してください。

## <a name="pricing"></a>価格
Cloud Shell のホストとなるマシンは無料です。ただし、前提条件として Azure ファイル共有をマウントする必要があります。 ストレージのコストは通常どおりに適用されます。

## <a name="supported-browsers"></a>サポートされているブラウザー
Cloud Shell は、Chrome、Edge、Safari での使用をお勧めします。
Cloud Shell は Chrome、Firefox、Safari、IE、Edge でサポートされますが、ブラウザー固有の設定の影響を受けます。