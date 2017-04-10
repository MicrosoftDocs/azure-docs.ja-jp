---
title: "DevTest Labs VM のカスタム アーティファクトの作成 | Microsoft Docs"
description: "DevTest ラボで使用するために独自のアーティファクトを作成する方法を説明します。"
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
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e86af029422340bdfa38ba233f0ed61f6f1d8ca2
ms.lasthandoff: 03/17/2017


---
# <a name="create-custom-artifacts-for-your-devtest-labs-vm"></a>DevTest ラボ VM のカスタム アーティファクトの作成
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>概要
**アーティファクト** は、VM のプロビジョニング後にアプリケーションをデプロイして構成するために使用します。 アーティファクトは、Git リポジトリ内のフォルダーに格納されているアーティファクト定義ファイルとその他のスクリプト ファイルで構成されます。 アーティファクト定義ファイルは、VM にインストールするものを指定するのに使用できる JSON と式で構成されます。 たとえば、アーティファクトの名前、実行するコマンド、コマンド実行時に使用可能なパラメーターを定義できます。 アーティファクト定義ファイル内では、他のスクリプト ファイルを名前で参照できます。

## <a name="artifact-definition-file-format"></a>アーティファクト定義ファイルの形式
次の例では、定義ファイルの基本構造を構成するセクションを示します。

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
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
| $schema |いいえ |定義ファイルの有効性のテストに役立つ JSON スキーマ ファイルの場所 |
| title |はい |ラボで表示されるアーティファクトの名前 |
| Description |はい |ラボで表示されるアーティファクトの説明 |
| iconUri |いいえ |ラボで表示されるアイコンの URI |
| targetOsType |はい |アーティファクトをインストールする VM のオペレーティング システム。 サポートされているオプションは、Windows と Linux です。 |
| parameters |いいえ |アーティファクトのインストール コマンドがマシンで実行されるときに指定する値。 これは、アーティファクトのカスタマイズに役立ちます。 |
| runCommand |はい |VM 上で実行されるアーティファクトのインストール コマンド。 |

### <a name="artifact-parameters"></a>アーティファクトのパラメーター
定義ファイルの parameters セクションでは、アーティファクトのインストール時にユーザーが入力できる値を指定します。 アーティファクトのインストール コマンドでこれらの値を参照できます。

次の構造でパラメーターを定義します。

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| 要素名 | 必須 | Description |
| --- | --- | --- |
| type |はい |パラメーター値の型。 使用できる型については、下にある一覧を参照してください。 |
| displayName |はい |ラボのユーザーに対して表示されるパラメーターの名前。 | |
| Description |はい |ラボで表示されるパラメーターの説明。 |

使用できる型は次のとおりです。

* string - 有効な JSON 文字列
* int - 有効な JSON 整数
* bool - 有効な JSON ブール値
* array - 有効な JSON 配列

## <a name="artifact-expressions-and-functions"></a>アーティファクトの式と関数
式と関数を使用して、アーティファクトのインストール コマンドを作成できます。
式は角かっこ ([ と ]) で囲み、アーティファクトのインストール時に評価されます。 式は、JSON 文字列値内の任意の場所に配置でき、常に別の JSON 値を返します。 角かっこ [ で始まるリテラル文字列を使用する必要がある場合は、2 つの角かっこ [[ を使用する必要があります。
通常は、関数と共に式を使用して値を構成します。 JavaScript の場合と同様に、関数呼び出しは functionName(arg1,arg2,arg3) という形式になります。

次に、一般的な関数を示します。

* parameters(parameterName) - アーティファクト コマンドの実行時に指定するパラメーター値を返します。
* concat(arg1,arg2,arg3, ...) - 複数の文字列値を結合します。 この関数は、任意の数の引数を取ることができます。

次の例では、式と関数を使用して値を構成する方法を示します。

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>カスタム アーティファクトの作成
カスタム アーティファクトを作成するには、次の手順を実行します。

1. JSON エディターのインストール - アーティファクト定義ファイルを操作するには、JSON エディターが必要です。 Windows、Linux、および OS X で使用可能な [Visual Studio Code](https://code.visualstudio.com/)を使用することをお勧めします。
2. サンプルの artifactfile.json の取得 - [GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)で、Azure DevTest Labs チームが作成したアーティファクトを確認します。このリポジトリには、独自のアーティファクトの作成に役立つ豊富なアーティファクト ライブラリが用意されています。 アーティファクト定義ファイルをダウンロードし、変更を加えて独自のアーティファクトを作成します。
3. IntelliSense の利用 - IntelliSense を利用して、アーティファクト定義ファイルの作成に使用できる有効な要素を確認します。 要素の値のさまざまなオプションを確認することもできます。 たとえば、 **targetOsType** 要素を編集する際に、IntelliSense では Windows と Linux という 2 つの選択肢が表示されます。
4. Git リポジトリへのアーティファクトの格納
   
   1. アーティファクトごとに個別のディレクトリを作成します。ディレクトリ名は、アーティファクト名と同じにします。
   2. 作成したディレクトリに、アーティファクト定義ファイル (artifactfile.json) を格納します。
   3. アーティファクト インストール コマンドから参照されるスクリプトを格納します。
      
      アーティファクト フォルダーの例を次に示します。
      
      ![Artifact git repo example](./media/devtest-lab-artifact-author/git-repo.png)
5. ラボへのアーティファクト リポジトリの追加 - 「 [ラボへの Git アーティファクト リポジトリの追加](devtest-lab-add-artifact-repo.md)」を参照してください。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [How to troubleshoot failing Artifacts in AzureDevTestLabs (AzureDevTestLabs でアーティファクトの失敗をトラブルシューティングする方法)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Azure Dev Test Lab で ARM テンプレートを使用して既存の AD ドメインに VM を参加させる)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>次のステップ
* [ラボへの Git アーティファクト リポジトリの追加](devtest-lab-add-artifact-repo.md)方法を学習します。


