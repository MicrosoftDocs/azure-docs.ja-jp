---
title: Data Science Virtual Machine でサポートされている言語
titleSuffix: Azure
description: Data Science Virtual Machine にプレインストールされているプログラム言語および関連ツールについて説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 586f37ff972a6102da351794365f719a185857fc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877416"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンでサポートされている言語 

データ サイエンス仮想マシン (DSVM) には、AI アプリケーションを構築するための構築済み言語および開発ツールがいくつか付属します。 代表的なものは以下のとおりです。 

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 エディション)

|    |           |
| ------------- | ------------- |
| サポートされている言語バージョン | 2.7 および 3.6 |
| サポートされている DSVM エディション      | Windows Server 2016     |
| DSVM での構成/インストール方法  | 2 つのグローバル `conda` 環境が作成されます。 <br /> `/anaconda/` にある * `root` 環境は Python 3.6 です。 <br/> `/anaconda/envs/python2` にある * `python2` 環境は Python 2.7 です。       |
| サンプルへのリンク      | Python 用のサンプル Jupyter ノートブックが含まれています     |
| DSVM 上の関連ツール      | PySpark、R、Julia      |

> [!NOTE]
> 2018 年 3 月より前に作成された Windows Server 2016 には、Python 3.5 および Python 2.7 が含まれています。 また、Python 2.7 は conda **root** 環境、**py35** は Python 3.5 環境です。 

### <a name="how-to-use--run-it"></a>使用/実行方法    

* コマンド プロンプトで実行

コマンド プロンプトを開き、実行する Python のバージョンに応じて次の手順を実行します。 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* IDE での使用

Visual Studio Community エディションでインストールされる Python Tools for Visual Studio (PTVS) を使用します。 既定では、PTVS で自動的にセットアップされる環境は Python 3.6 のみです。 

> [!NOTE]
> Python 2.7 にある PTVS をポイントするには、PTVS でカスタム環境を作成する必要があります。 **[Tools] (ツール)** -> **[Python Tools]** -> **[Python Environments] (Python 環境)** の順に移動し、**[+ Custom] (+ カスタム)** をクリックして、Visual Studio Community エディションにこの環境パスを設定します。 次に、場所を `c:\anaconda\envs\python2` に設定して _[Auto Detect]_ (自動検出) をクリックします。 

* Jupyter での使用

Jupyter を開き、`New` ボタンをクリックして新しいノートブックを作成します。 現時点では、カーネル タイプとして Python 3.6 向けの _Python [Conda Root]_ と Python 2.7 環境向けの _Python [Conda env:python2]_ を選択できます。 

* Python パッケージのインストール

DSVM での既定の Python 環境は、すべてのユーザーが読み取り可能なグローバル環境です。 ただし、グローバル パッケージの書き込み/インストールは管理者のみが実行できます。 グローバル環境にパッケージをインストールするには、管理者として `activate` コマンドを使用して root または python2 環境にアクティブ化します。 その後、`conda` や `pip` などのパッケージ マネージャーを使用してパッケージをインストールまたは更新できます。 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux および Windows Server 2012 エディション)

|    |           |
| ------------- | ------------- |
| サポートされている言語バージョン | 2.7 および 3.5 |
| サポートされている DSVM エディション      | Linux、Windows Server 2012    |
| DSVM での構成/インストール方法  | 2 つのグローバル `conda` 環境が作成されます。 <br /> `/anaconda/` にある * `root` 環境は Python 2.7 です。 <br/> `/anaconda/envs/py35` にある * `py35` 環境は Python 3.5 です。       |
| サンプルへのリンク      | Python 用のサンプル Jupyter ノートブックが含まれています     |
| DSVM 上の関連ツール      | PySpark、R、Julia      |
### <a name="how-to-use--run-it"></a>使用/実行方法    

**Linux**
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

**Windows 2012**
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

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| サポートされている言語バージョン | Microsoft R Open 3.x (CRAN-R と 100% 互換)<br /> Microsoft R Server 9.x Developer エディション (スケーラブルなエンタープライズ対応 R プラットフォーム)|
| サポートされている DSVM エディション      | Linux、Windows     |
| DSVM での構成/インストール方法  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
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
| サポートされている言語バージョン | 0.6 |
| サポートされている DSVM エディション      | Linux、Windows     |
| DSVM での構成/インストール方法  | Windows:`C:\JuliaPro-VERSION` にインストール<br /> Linux:`/opt/JuliaPro-VERSION` にインストール    |
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


