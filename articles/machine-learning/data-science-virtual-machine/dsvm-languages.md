---
title: "Azure でのデータ サイエンス仮想マシン用言語 | Microsoft Docs"
description: "Azure でのデータ サイエンス仮想マシン用言語"
keywords: "データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 2f2125e739b738847e03ce429d65801969611685
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンでサポートされている言語 

データ サイエンス仮想マシン (DSVM) には、AI アプリケーションを構築するための構築済み言語および開発ツールがいくつか付属します。 代表的なものは以下のとおりです。 

## <a name="python"></a>Python

|    |           |
| ------------- | ------------- |
| サポートされている言語バージョン | 2.7 および 3.5 |
| サポートされている DSVM エディション      | Linux、Windows     |
| DSVM での構成/インストール方法  | 2 つのグローバル `conda` 環境が作成されます。 <br /> `/anaconda/` にある * `root` 環境は Python 2.7 です。 <br/> `/anaconda/envs/py35` にある * `py35` 環境は Python 3.5 です。       |
| サンプルへのリンク      | Python 用のサンプル Jupyter ノートブックが含まれています     |
| DSVM 上の関連ツール      | PySpark、R、Julia      |
### <a name="how-to-use--run-it"></a>使用/実行方法    

**Windows**:

* コマンド プロンプトで実行

コマンド プロンプトを開き、実行する Python のバージョンに応じて次の手順を実行します。 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* IDE での使用

Visual Studio Community エディションでインストールされる Python Tools for Visual Studio (PTVS) を使用します。 Python 2.7 では、PTVS で自動的にセットアップされる唯一の環境です。 
> [!NOTE]
> Python 3.5 にある PTVS をポイントするには、PTVS でカスタム環境を作成する必要があります。 **[Tools] (ツール)** -> **[Python Tools]** -> **[Python Environments] (Python 環境)** の順に移動し、**[+ Custom] (+ カスタム)** をクリックして、Visual Studio Community エディションにこの環境パスを設定します。 次に、場所を `c:\anaconda\envs\py35` に設定して _[Auto Detect]_ (自動検出) をクリックします。 

* Jupyter での使用

Jupyter を開き、`New` ボタンをクリックして新しいノートブックを作成します。 現時点では、カーネル タイプとして Python 2.7 向けの _Python [Conda Root]_ と Python 3.5 環境向けの _Python [Conda env:py35]_ を選択できます。 

* Python パッケージのインストール

DSVM での既定の Python 環境は、すべてのユーザーが読み取り可能なグローバル環境です。 ただし、グローバル パッケージの書き込み/インストールは管理者のみが実行できます。 グローバル環境にパッケージをインストールするには、管理者として `activate` コマンドを使用して root または py35 環境にアクティブ化します。 その後、`conda` や `pip` などのパッケージ マネージャーを使用してパッケージをインストールまたは更新できます。 


**Linux**:

* ターミナルで実行

ターミナルを開き、実行する Python のバージョンに応じて次の手順を実行します。 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* IDE での使用

Visual Studio Community エディションでインストールされる PyCharm を使用します。 

* Jupyter での使用

Jupyter を開き、`New` ボタンをクリックして新しいノートブックを作成します。 現時点では、カーネル タイプとして Python 2.7 向けの _Python [Conda Root]_ と Python 3.5 環境向けの _Python [Conda env:py35]_ を選択できます。 

* Python パッケージのインストール

DSVM での既定の Python 環境は、すべてのユーザーが読み取り可能なグローバル環境です。 ただし、グローバル パッケージの書き込み/インストールは管理者のみが実行できます。 グローバル環境にパッケージをインストールするには、管理者または sudo 権限のあるユーザーとして `source activate` コマンドを使用して root または py35 環境にアクティブ化します。 その後、`conda` や `pip` などのパッケージ マネージャーを使用してパッケージをインストールまたは更新できます。 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| サポートされている言語バージョン | Microsoft R Open 3.x (CRAN-R と 100% 互換)<br /> Microsoft R Server 9.x Developer エディション (スケーラブルなエンタープライズ対応 R プラットフォーム)|
| サポートされている DSVM エディション      | Linux、Windows     |
| DSVM での構成/インストール方法  | Windows: `C:\Program Files\Microsoft\R Server\R_SERVER` <br />Linux: ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| サンプルへのリンク      | R 用のサンプル Jupyter ノートブックが含まれています     |
| DSVM 上の関連ツール      | SparkR、Python、Julia      |
### <a name="how-to-use--run-it"></a>使用/実行方法    

**Windows**:

* コマンド プロンプトで実行

コマンド プロンプトを開いて `R` を入力します。

* IDE での使用

Visual Studio Community エディションまたは RStudio でインストールされる RTools for Visual Studio (RTVS) を使用します。 これらは [スタート] メニューまたはデスクトップのアイコンから使用できます。 

* Jupyter での使用

Jupyter を開き、`New` ボタンをクリックして新しいノートブックを作成します。 現時点では、Jupyter R カーネル (IRKernel) を使用するためにカーネル タイプを _R_ として選択できます。 

* R パッケージのインストール

R は、すべてのユーザーが読み取り可能なグローバル環境で DSVM にインストールされます。 ただし、グローバル パッケージの書き込み/インストールは管理者のみが実行できます。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して R を実行します。 その後、R パッケージマネージャー `install.packages()` を使用してパッケージをインストールまたは更新できます。 

**Linux**:

* ターミナルで実行

ターミナルを開いて `R` を実行します。  

* IDE での使用

Linux DSVM にインストールされている RStudio を使用します。  

* Jupyter での使用

Jupyter を開き、`New` ボタンをクリックして新しいノートブックを作成します。 現時点では、Jupyter R カーネル (IRKernel) を使用するためにカーネル タイプを _R_ として選択できます。 

* R パッケージのインストール

R は、すべてのユーザーが読み取り可能なグローバル環境で DSVM にインストールされます。 ただし、グローバル パッケージの書き込み/インストールは管理者のみが実行できます。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して R を実行します。 その後、R パッケージマネージャー `install.packages()` を使用してパッケージをインストールまたは更新できます。 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| サポートされている言語バージョン | 0.5 |
| サポートされている DSVM エディション      | Linux、Windows     |
| DSVM での構成/インストール方法  | Windows: `C:\JuliaPro-VERSION` にインストールされます<br /> Linux: `/opt/JuliaPro-VERSION` にインストールされます    |
| サンプルへのリンク      | Julia 用のサンプル Jupyter ノートブックが含まれています     |
| DSVM 上の関連ツール      | Python、R      |
### <a name="how-to-use--run-it"></a>使用/実行方法    

**Windows**:

* コマンド プロンプトで実行

コマンド プロンプトを開いて `julia` を実行します。 
* IDE での使用

DSVM にインストールされ、デスクトップのショートカットから実行できる `Juno` Julia IDE を使用します。

* Jupyter での使用

Jupyter を開き、`New` ボタンをクリックして新しいノートブックを作成します。 現時点では、カーネル タイプを `Julia VERSION` として選択できます 

* Julia パッケージのインストール

既定の Julia の位置は、すべてのユーザーが読み取り可能なグローバル環境です。 ただし、グローバル パッケージの書き込み/インストールは管理者のみが実行できます。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して Julia を実行します。 その後、`Pkg.add()` などの Julia パッケージ マネージャー コマンドを実行してパッケージをインストールまたは更新できます。 


**Linux**:
* ターミナルで実行。

ターミナルを開いて `julia` を実行します。 
* IDE での使用

DSVM にインストールされ、アプリケーション メニューのショートカットから実行できる `Juno` Julia IDE を使用します。

* Jupyter での使用

Jupyter を開き、`New` ボタンをクリックして新しいノートブックを作成します。 現時点では、カーネル タイプを `Julia VERSION` として選択できます 

* Julia パッケージのインストール

既定の Julia の位置は、すべてのユーザーが読み取り可能なグローバル環境です。 ただし、グローバル パッケージの書き込み/インストールは管理者のみが実行できます。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して Julia を実行します。 その後、`Pkg.add()` などの Julia パッケージ マネージャー コマンドを実行してパッケージをインストールまたは更新できます。 

## <a name="other-languages"></a>その他の言語

**C#**: Windows で使用可能であり、Visual Studio Community エディションから、または `Developer Command Prompt for Visual Studio` でアクセスできます (`csc` コマンドの実行のみ可能)。 

**Java**: OpenJDK は DSVM の Linux エディションと Windows エディションの両方で使用可能であり、パスに設定されています。 Windows のコマンド プロンプトまたは Linux の bash シェルで `javac` または `java` コマンドを入力することにより、Java を使用できます。 

**node.js**: node.js は DSVM の Linux エディションと Windows エディションの両方で使用可能であり、パスに設定されています。 Windows のコマンド プロンプトまたは Linux の bash シェルで `node` または `npm` コマンドを入力することにより、node.js にアクセスできます。 Windows では、node.js アプリケーション開発用のグラフィカル IDE を提供するために、Node.js Tools for Visual Studio 拡張機能がインストールされます。 

**F#**: Windows で使用可能であり、Visual Studio Community エディションから、または `Developer Command Prompt for Visual Studio` でアクセスできます (`fsc` コマンドの実行のみ可能)。 


