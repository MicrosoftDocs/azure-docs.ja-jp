---
title: DevTest Labs 仮想マシンのカスタム アーティファクトの作成 | Microsoft Docs
description: 仮想マシンのプロビジョニング後、アプリケーションをデプロイおよび設定するために、Azure DevTest Labs で使用する成果物を作成する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270684"
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

```json
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
```

| 要素名 | 必須 | 説明 |
| --- | --- | --- |
| $schema |いいえ |JSON スキーマ ファイルの場所。 JSON スキーマ ファイルは、定義ファイルの有効性をテストする際に役立ちます。 |
| title |はい |ラボで表示されるアーティファクトの名前 |
| description |はい |ラボで表示されるアーティファクトの説明 |
| iconUri |いいえ |ラボで表示されるアイコンの URI。 |
| targetOsType |はい |アーティファクトをインストールする VM のオペレーティング システム。 サポートされているオプションは、Windows と Linux です。 |
| parameters |いいえ |マシンでアーティファクトのインストール コマンドが実行されるときに指定する値。 これは、アーティファクトをカスタマイズする際に役立ちます。 |
| runCommand |はい |VM 上で実行されるアーティファクトのインストール コマンド。 |

### <a name="artifact-parameters"></a>アーティファクトのパラメーター
定義ファイルの parameters セクションでは、アーティファクトのインストール時にユーザーが入力できる値を指定します。 アーティファクトのインストール コマンドでこれらの値を参照できます。

パラメーターを定義するには、次の構造を使用します。

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| 要素名 | 必須 | 説明 |
| --- | --- | --- |
| type |はい |パラメーター値の型。 使用できる型については、下記を参照してください。 |
| displayName |はい |ラボのユーザーに対して表示されるパラメーターの名前。 |
| description |はい |ラボで表示されるパラメーターの説明。 |

使用できる型は次のとおりです。

* string (有効な JSON 文字列)
* int (有効な JSON 整数)
* bool (有効な JSON ブール値)
* array (有効な JSON 配列)

## <a name="secrets-as-secure-strings"></a>セキュリティで保護された文字列としてのシークレット
シークレットをセキュリティで保護された文字列として宣言します。 **artifactfile.json** ファイルの `parameters` セクション内に、セキュリティで保護された文字列パラメーターを宣言するための構文を次に示します。

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

アーティファクト インストール コマンドの場合、ConvertTo-SecureString コマンドを使用して作成されるセキュリティで保護された文字列を受け取る PowerShell スクリプトを実行します。 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

artifactfile.json と artifact.ps1 (PowerShell スクリプト) の完全な例については、[GitHub のこのサンプル](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)をご覧ください。

もう 1 つの注意すべき重要ポイントは、出力はユーザーのデバッグ用にキャプチャされるため、シークレットがコンソールに記録されないことです。 

## <a name="artifact-expressions-and-functions"></a>アーティファクトの式と関数
式と関数を使用して、アーティファクトのインストール コマンドを作成できます。
式は角かっこ ([ と ]) で囲み、アーティファクトのインストール時に評価されます。 式は、JSON 文字列値内の任意の場所に配置できます。 式は常に別の JSON 値を返します。 角かっこ ([) で始まるリテラル文字列を使用する必要がある場合は、2 つの角かっこ ([[) を使用する必要があります。
通常は、関数と共に式を使用して値を構成します。 JavaScript と同じように、関数呼び出しは **functionName(arg1, arg2, arg3)** という形式になります。

次の一覧に、一般的な関数を示します。

* **parameters(parameterName)** : アーティファクト コマンドの実行時に指定するパラメーター値を返します。
* **concat(arg1, arg2, arg3,…..)** : 複数の文字列値を結合します。 この関数は、さまざまな引数を受け取ることができます。

次の例は、式と関数を使用して値を構成する方法を示しています。

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>カスタム アーティファクトの作成

1. JSON エディターをインストールします。 アーティファクト定義ファイルを操作するには、JSON エディターが必要です。 Windows、Linux、OS X で使用可能な [Visual Studio Code](https://code.visualstudio.com/) を使用することをお勧めします。
2. サンプルの artifactfile.json 定義ファイルを取得します。 [GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)で、DevTest Labs チームが作成したアーティファクトを確認します。 リポジトリには、独自のアーティファクトの作成に役立つアーティファクトの豊富なライブラリが用意されています。 アーティファクト定義ファイルをダウンロードし、変更を加えて独自のアーティファクトを作成します。
3. IntelliSense を使用します。 IntelliSense を使用して、アーティファクト定義ファイルの作成に使用できる有効な要素を確認します。 要素の値のさまざまなオプションを確認することもできます。 たとえば、**targetOsType** 要素を編集するときに、IntelliSense は Windows と Linux の 2 つの選択肢を表示します。
4. アーティファクトを、[DevTest Labs 用のパブリック Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)または[独自の Git リポジトリ](devtest-lab-add-artifact-repo.md)に格納します。 パブリック リポジトリでは、他のユーザーが共有している成果物を表示し、必要に応じてそのまま使用したり、カスタマイズしたりすることができます。
   
   1. アーティファクトごとに個別のディレクトリを作成します。 ディレクトリ名は、アーティファクト名と同じにします。
   2. 作成したディレクトリに、アーティファクト定義ファイル (artifactfile.json) を格納します。
   3. アーティファクト インストール コマンドから参照されるスクリプトを格納します。
      
      アーティファクト フォルダーの例を次に示します。
      
      ![アーティファクト フォルダーの例](./media/devtest-lab-artifact-author/git-repo.png)
5. 独自のリポジトリを使用してアーティファクトを格納する場合は、[アーティファクトとテンプレート用の Git リポジトリの追加](devtest-lab-add-artifact-repo.md)に関する記事の手順に従って、そのリポジトリをラボに追加します。

## <a name="related-articles"></a>関連記事
* [DevTest Labs でアーティファクトのエラーを診断する方法](devtest-lab-troubleshoot-artifact-failure.md)
* [DevTest Labs で Resource Manager テンプレートを使用して既存の Active Directory ドメインに VM を参加させる](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>次のステップ
* [ラボへの Git アーティファクト リポジトリの追加](devtest-lab-add-artifact-repo.md)方法を学習します。
