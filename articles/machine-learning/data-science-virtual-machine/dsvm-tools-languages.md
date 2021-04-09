---
title: サポートされている言語
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine にプレインストールされているサポート対象のプログラム言語および関連ツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516432"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンでサポートされている言語 

Data Science Virtual Machine (DSVM) には、人工知能 (AI) アプリケーションを構築するための事前構築済み言語および開発ツールがいくつか付属します。 重要なものをいくつか紹介します。

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 エディション)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされている言語バージョン | Python 2.7 および 3.7 |
| サポートされている DSVM エディション      | Windows Server 2016     |
| DSVM での構成/インストール方法  | 次の 2 つのグローバル `conda` 環境が作成されます: <br /> * `/anaconda/` にある `root` 環境は、Python 3.7 です。 <br/> * `/anaconda/envs/python2` にある `python2` 環境は、Python 2.7 です。       |
| サンプルへのリンク      | Python 用のサンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | PySpark、R、Julia。      |

> [!NOTE]
> 2018 年 3 月より前に作成された Windows Server 2016 のビルドには、Python 3.5 と Python 2.7 が含まれています。 Python 2.7 は、conda **root** 環境であり、**py37** は Python 3.7 環境です。

### <a name="how-to-use-and-run-it"></a>使用と実行方法    

* コマンド プロンプトでの実行:

  コマンド プロンプトを開き、実行する Python のバージョンに応じて次のいずれかの方法を使用します。

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* IDE で使用する:

  Visual Studio Community エディションでインストールされる、Python Tools for Visual Studio (PTVS) を使用します。 既定では、PTVS で自動的に設定される唯一の環境は Python 3.6 です。 

    > [!NOTE]
    > Python 2.7 にある PTVS をポイントするには、PTVS でカスタム環境を作成する必要があります。 Visual Studio Community エディションでこの環境パスを設定するには、 **[ツール]**  ->  **[Python ツール]**  ->  **[Python 環境]** の順に移動し、 **[+ カスタム]** を選択します。 その後、場所を **c:\anaconda\envs\python2** に設定し、 **[自動検出]** を選択します。

* Jupyter で使用する:

  Jupyter を開き、 **[新規]** を選択して新しいノートブックを作成します。 カーネルの種類を、Python 3.7 向けの _Python [Conda Root]_ および Python 2.7 向けの _Python [Conda env:python2]_ として設定できます。

* Python パッケージをインストールする:

  DSVM での既定の Python 環境は、すべてのユーザーが読み取り可能なグローバル環境です。 しかし、グローバル パッケージの書き込みとインストールを行うことができるのは管理者のみです。 グローバル環境にパッケージをインストールするには、管理者として `activate` コマンドを使用して、root または python2 環境に対してアクティブ化します。 その後、`conda` や `pip` などのパッケージ マネージャーを使用して、パッケージをインストールまたは更新できます。

## <a name="python-linux-edition"></a>Python (Linux エディション)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされている言語バージョン | Python 2.7 および 3.5 |
| サポートされている DSVM エディション      | Linux   |
| DSVM での構成/インストール方法  | 次の 2 つのグローバル `conda` 環境が作成されます: <br /> * `/anaconda/` にある `root` 環境は Python 2.7 です。 <br/> * `/anaconda/envs/py35` にある `py35` 環境は Python 3.5 です。       |
| サンプルへのリンク      | Python 用のサンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | PySpark、R、Julia      |

### <a name="how-to-use-and-run-it"></a>使用と実行方法    

* ターミナルで実行する:

  ターミナルを開き、実行する Python のバージョンに応じて、次のいずれかを実行します。

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* IDE で使用する:

  Visual Studio Community エディションでインストールされる、PyCharm を使用します。 

* Jupyter で使用する:

  Jupyter を開き、 **[新規]** を選択して新しいノートブックを作成します。 カーネルの種類を、Python 2.7 向けの **Python [Conda Root]** および Python 3.5 環境向けの **Python [Conda env:py35]** として設定できます。 

* Python パッケージをインストールする:

  DSVM での既定の Python 環境は、すべてのユーザーが読み取り可能なグローバル環境です。 しかし、グローバル パッケージの書き込みとインストールを行うことができるのは管理者のみです。 グローバル環境にパッケージをインストールするには、管理者または sudo 権限のあるユーザーとして `source activate` コマンドを使用して、root または py35 環境に対してアクティブ化します。 その後、`conda` や `pip` などのパッケージ マネージャーを使用して、パッケージをインストールまたは更新できます。


## <a name="r"></a>R

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされている言語バージョン | Microsoft R Open 3.x (CRAN-R と 100% 互換)<br /> Microsoft R Server 9.x Developer エディション (スケーラブルなエンタープライズ対応 R プラットフォーム)|
| サポートされている DSVM エディション      | Linux、Windows     |
| DSVM での構成/インストール方法  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| サンプルへのリンク      | R 用のサンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | SparkR、Python、Julia      |

### <a name="how-to-use-and-run-it"></a>使用と実行方法    

**Windows**:

* コマンド プロンプトでの実行:

  コマンド プロンプトを開き、「`R`」と入力します。

* IDE で使用する:

  Visual Studio Community エディションまたは RStudio でインストールされる RTools for Visual Studio (RTVS) を使用します。 これらは [スタート] メニューで、またはデスクトップ アイコンとして使用できます。 

* Jupyter で使用する

  Jupyter を開き、 **[新規]** を選択して新しいノートブックを作成します。 Jupyter R カーネル (IRKernel) を使用するために、カーネルの種類を **R** として設定できます。

* R パッケージをインストールする:

  R は、すべてのユーザーが読み取り可能なグローバル環境で DSVM にインストールされます。 しかし、グローバル パッケージの書き込みとインストールを行うことができるのは管理者のみです。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して R を実行します。 その後、R パッケージ マネージャー `install.packages()` を実行して、パッケージをインストールまたは更新できます。

**Linux**:

* ターミナルで実行する:

  ターミナルを開き、`R` を実行します。  

* IDE で使用する:

  Linux DSVM にインストールされている、RStudio を使用します。  

* Jupyter で使用する:

  Jupyter を開き、 **[新規]** を選択して新しいノートブックを作成します。 Jupyter R カーネル (IRKernel) を使用するために、カーネルの種類を **R** として設定できます。 

* R パッケージをインストールする:

  R は、すべてのユーザーが読み取り可能なグローバル環境で DSVM にインストールされます。 しかし、グローバル パッケージの書き込みとインストールを行うことができるのは管理者のみです。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して R を実行します。 その後、R パッケージ マネージャー `install.packages()` を実行して、パッケージをインストールまたは更新できます。


## <a name="julia"></a>Julia

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされている言語バージョン | 0.6 |
| サポートされている DSVM エディション      | Linux、Windows     |
| DSVM での構成/インストール方法  | Windows: `C:\JuliaPro-VERSION` にインストール<br /> Linux: `/opt/JuliaPro-VERSION` にインストール    |
| サンプルへのリンク      | Julia 用のサンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | Python、R      |

### <a name="how-to-use-and-run-it"></a>使用と実行方法    

**Windows**:

* コマンド プロンプトで実行する

  コマンド プロンプトを開き、`julia` を実行します。
* IDE で使用する:

  DSVM にインストールされ、デスクトップ ショートカットとして使用可能な Julia IDE と共に `Juno` を使用します。

* Jupyter で使用する:

  Jupyter を開き、 **[新規]** を選択して新しいノートブックを作成します。 カーネルの種類を **Julia VERSION** として設定できます。

* Julia パッケージをインストールする:

  既定の Julia の場所は、すべてのユーザーが読み取り可能なグローバル環境です。 しかし、グローバル パッケージの書き込みとインストールを行うことができるのは管理者のみです。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して Julia を実行します。 その後、`Pkg.add()` などの Julia パッケージ マネージャー コマンドを実行して、パッケージをインストールまたは更新できます。


**Linux**:
* ターミナルで実行する:

  ターミナルを開き、`julia` を実行します。
* IDE で使用する:

  DSVM にインストールされ、 **[アプリケーション]** メニューのショートカットとして使用可能な Julia IDE と共に、`Juno` を使用します。

* Jupyter で使用する:

  Jupyter を開き、 **[新規]** を選択して新しいノートブックを作成します。 カーネルの種類を **Julia VERSION** として設定できます。

* Julia パッケージをインストールする:

  既定の Julia の場所は、すべてのユーザーが読み取り可能なグローバル環境です。 しかし、グローバル パッケージの書き込みとインストールを行うことができるのは管理者のみです。 グローバル環境にパッケージをインストールするには、上記のいずれかの方法を使用して Julia を実行します。 その後、`Pkg.add()` などの Julia パッケージ マネージャー コマンドを実行して、パッケージをインストールまたは更新できます。

## <a name="other-languages"></a>その他の言語

**C#** : Windows で使用可能であり、Visual Studio Community エディションから、または `Developer Command Prompt for Visual Studio` でアクセスできます (`csc` コマンドを実行できる場合)。

**Java**: OpenJDK は DSVM の Linux エディションと Windows エディションの両方で使用可能であり、パスに設定されています。 Java を使用するには、Windows のコマンド プロンプトまたは Linux の bash シェルで `javac` または `java` コマンドを入力します。

**Node.js**:Node.js は DSVM の Linux エディションと Windows エディションの両方で使用可能であり、パスに設定されています。 Node.js にアクセスするには、Windows のコマンド プロンプトまたは Linux の bash シェルで `node` または `npm` コマンドを入力します。 Windows では、Node.js アプリケーション開発用のグラフィカル IDE を提供するために、Node.js ツール用の Visual Studio 拡張機能がインストールされます。

**F#** : Windows で使用可能であり、Visual Studio Community エディションから、または `Developer Command Prompt for Visual Studio` でアクセスできます (`fsc` コマンドを実行できる場合)。
