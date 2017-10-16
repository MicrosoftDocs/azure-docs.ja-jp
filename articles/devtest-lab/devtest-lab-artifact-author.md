---
title: "DevTest Labs 仮想マシンのカスタム アーティファクトの作成 | Microsoft Docs"
description: "Azure DevTest Labs で使用する独自のアーティファクトを作成する方法を説明します。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 679819618452d65847c6163569e04945ba8a414d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>DevTest Labs 仮想マシンのカスタム アーティファクトの作成

この記事で説明する手順の概要については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>概要
"*アーティファクト*" を使用すると、VM のプロビジョニング後にアプリケーションをデプロイして設定できます。 アーティファクトは、Git リポジトリ内のフォルダーに格納されているアーティファクト定義ファイルとその他のスクリプト ファイルで構成されます。 アーティファクト定義ファイルは、VM にインストールするものを指定するのに使用できる JSON と式で構成されます。 たとえば、アーティファクトの名前、実行するコマンド、コマンドの実行時に使用可能なパラメーターを定義できます。 アーティファクト定義ファイル内では、他のスクリプト ファイルを名前で参照できます。

## <a name="artifact-definition-file-format"></a>アーティファクト定義ファイルの形式
次の例では、定義ファイルの基本構造を構成するセクションを示します。

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| 要素名 | 必須 | Description |
| --- | --- | --- |
| $schema |いいえ |JSON スキーマ ファイルの場所。 JSON スキーマ ファイルは、定義ファイルの有効性をテストする際に役立ちます。 |
| title |はい |ラボで表示されるアーティファクトの名前 |
| Description |はい |ラボで表示されるアーティファクトの説明 |
| iconUri |いいえ |ラボで表示されるアイコンの URI。 |
| targetOsType |あり |アーティファクトをインストールする VM のオペレーティング システム。 サポートされているオプションは、Windows と Linux です。 |
| パラメーター |いいえ |マシンでアーティファクトのインストール コマンドが実行されるときに指定する値。 これは、アーティファクトをカスタマイズする際に役立ちます。 |
| runCommand |はい |VM 上で実行されるアーティファクトのインストール コマンド。 |

### <a name="artifact-parameters"></a>アーティファクトのパラメーター
定義ファイルの parameters セクションでは、アーティファクトのインストール時にユーザーが入力できる値を指定します。 アーティファクトのインストール コマンドでこれらの値を参照できます。

パラメーターを定義するには、次の構造を使用します。

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| 要素名 | 必須 | Description |
| --- | --- | --- |
| type |はい |パラメーター値の型。 使用できる型については、下記を参照してください。 |
| displayName |はい |ラボのユーザーに対して表示されるパラメーターの名前。 | |
| Description |はい |ラボで表示されるパラメーターの説明。 |

使用できる型は次のとおりです。

* string (有効な JSON 文字列)
* int (有効な JSON 整数)
* bool (有効な JSON ブール値)
* array (有効な JSON 配列)

## <a name="artifact-expressions-and-functions"></a>アーティファクトの式と関数
式と関数を使用して、アーティファクトのインストール コマンドを作成できます。
式は角かっこ ([ と ]) で囲み、アーティファクトのインストール時に評価されます。 式は、JSON 文字列値内の任意の場所に配置できます。 式は常に別の JSON 値を返します。 角かっこ ([) で始まるリテラル文字列を使用する必要がある場合は、2 つの角かっこ ([[) を使用する必要があります。
通常は、関数と共に式を使用して値を構成します。 JavaScript の場合と同様に、関数呼び出しは **functionName(arg1,arg2,arg3)**という形式になります。

次の一覧に、一般的な関数を示します。

* **parameters(parameterName)**: アーティファクト コマンドの実行時に指定するパラメーター値を返します。
* **concat(arg1,arg2,arg3, …..)**: 複数の文字列値を結合します。 この関数は、さまざまな引数を受け取ることができます。

次の例は、式と関数を使用して値を構成する方法を示しています。

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>カスタム アーティファクトの作成

1. JSON エディターをインストールします。 アーティファクト定義ファイルを操作するには、JSON エディターが必要です。 Windows、Linux、OS X で使用可能な [Visual Studio Code](https://code.visualstudio.com/) を使用することをお勧めします。
2. サンプルの artifactfile.json 定義ファイルを取得します。 [GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)で、DevTest Labs チームが作成したアーティファクトを確認します。 リポジトリには、独自のアーティファクトの作成に役立つアーティファクトの豊富なライブラリが用意されています。 アーティファクト定義ファイルをダウンロードし、変更を加えて独自のアーティファクトを作成します。
3. IntelliSense を使用します。 IntelliSense を使用して、アーティファクト定義ファイルの作成に使用できる有効な要素を確認します。 要素の値のさまざまなオプションを確認することもできます。 たとえば、**targetOsType** 要素を編集するときに、IntelliSense は Windows と Linux の 2 つの選択肢を表示します。
4. [Git リポジトリ](devtest-lab-add-artifact-repo.md)にアーティファクトを格納します。
   
   1. アーティファクトごとに個別のディレクトリを作成します。 ディレクトリ名は、アーティファクト名と同じにします。
   2. 作成したディレクトリに、アーティファクト定義ファイル (artifactfile.json) を格納します。
   3. アーティファクト インストール コマンドから参照されるスクリプトを格納します。
      
      アーティファクト フォルダーの例を次に示します。
      
      ![アーティファクト フォルダーの例](./media/devtest-lab-artifact-author/git-repo.png)
5. アーティファクト リポジトリをラボに追加します。 [アーティファクトとテンプレートを格納するための Git リポジトリの追加](devtest-lab-add-artifact-repo.md)に関する記事をご覧ください。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>関連記事
* [DevTest Labs でアーティファクトのエラーを診断する方法](devtest-lab-troubleshoot-artifact-failure.md)
* [DevTest Labs で Resource Manager テンプレートを使用して既存の Active Directory ドメインに VM を参加させる](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>次のステップ
* [ラボへの Git アーティファクト リポジトリの追加](devtest-lab-add-artifact-repo.md)方法を学習します。

