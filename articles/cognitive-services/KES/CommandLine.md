---
title: コマンドライン インターフェイス - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: コマンドライン インターフェイスを使用して、構造化データからインデックス ファイルと文法ファイルをビルドし、Web サービスとしてデプロイします。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860118"
---
# <a name="command-line-interface"></a>コマンド ライン インターフェイス

Knowledge Exploration Service (KES) コマンド ライン インターフェイスには、構造化データからインデックス ファイルと文法ファイルをビルドし、Web サービスとしてデプロイする機能があります。  一般的な構文は `kes.exe <command> <required_args> [<optional_args>]` です。  コマンドの一覧を表示するには引数なしで `kes.exe` を実行します。指定したコマンドに使用できる引数の一覧を表示するには、`kes.exe <command>` を実行します。  使用できるコマンドの一覧を次に示します。

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index Command

**build_index** コマンドは、インデックス付けされるオブジェクトのスキーマ定義ファイルとデータ ファイルからバイナリ インデックス ファイルをビルドします。  結果のインデックス ファイルを使用して、構造化されたクエリ式を評価したり、コンパイル済みの文法ファイルと組み合わせて自然言語クエリの解釈を生成したりすることができます。

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| パラメーター      | 説明               |
|----------------|---------------------------|
| `<schemaFile>` | 入力スキーマのパス |
| `<dataFile>`   | 入力データベースのパス   |
| `<indexFile>`  | 出力インデックスのパス |
| `--description <description>` | 説明文字列 |
| `--remote <vmSize>`           | リモート ビルド用の VM のサイズ |

これらのファイルは、ローカル ファイルのパスまたは AzureBLOB への URL パスによって指定できます。  スキーマ ファイルには、インデックス付けされるオブジェクトの構造とサポートする操作を記述します (「[Schema Format](SchemaFormat.md)」(スキーマの形式) を参照してください)。  データ ファイルには、インデックス付けされるオブジェクトと属性値を列挙します (「[Data Format](DataFormat.md)」(データの形式) を参照してください)。  ビルドに成功すると、出力インデックス ファイルには、目的の操作をサポートする圧縮形式の入力データが含まれます。  

説明文字列は、**describe_index** コマンドを使用してその後にバイナリ インデックスを識別できるように、必要に応じて指定することができます。  

既定では、インデックスはローカル マシン上に構築されます。  Azure 環境以外の場合、ローカル ビルドは最大 10,000 個のオブジェクトを含むデータ ファイルに制限されます。  --remote フラグを指定すると、一時的に作成された、指定したサイズのAzure VM 上にインデックスが構築されます。  この方法で、より多くのメモリを持つ Azure VM を使用して、大きなインデックスを効率的に構築することができます。  ビルド プロセスが遅くなるページングを回避するには、入力データのファイル サイズの 3 倍の RAM を使用することをお勧めします。  利用可能な VM サイズの一覧については、「[Azure の Windows 仮想マシンのサイズ](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)」を参照してください。

> [!TIP] 
> より高速にビルドするには、データ ファイル内のオブジェクトを確率の降順で事前に並べ替えておきます。

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar コマンド

**build_grammar** コマンドは、XML で指定された文法をバイナリ文法ファイルにコンパイルします。  結果の文法ファイルをインデックス ファイルと組み合わせて使用して、自然言語クエリの解釈を生成することができます。

`kes.exe build_grammar <xmlFile> <grammarFile>`

| パラメーター       | 説明               |
|-----------------|---------------------------|
| `<xmlFile>`     | 入力 XML 文法仕様のパス |
| `<grammarFile>` | 出力のコンパイル済み文法のパス         |

これらのファイルは、ローカル ファイルのパスまたは AzureBLOB への URL パスによって指定できます。  文法仕様には、加重が設定された一連の自然言語表現とそのセマンティック解釈が記載されています ([「Grammar Format」(文法の形式)](GrammarFormat.md) を参照してください。)  ビルドが成功すると、出力文法ファイルには、高速デコードが可能になる文法仕様のバイナリ表現が含まれます。

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service コマンド

**host_service** コマンドは、ローカル マシン上で KES サービスのインスタンスをホストします。

`kes.exe host_service <grammarFile> <indexFile> [options]`

| パラメーター       | 説明                |
|-----------------|----------------------------|
| `<grammarFile>` | 入力バイナリ文法のパス         |
| `<indexFile>`   | 入力バイナリ インデックスのパス           |
| `--port <port>` | ローカル ポート番号。  既定値は8000 |

これらのファイルは、ローカル ファイルのパスまたは AzureBLOB への URL パスによって指定できます。  Web サービスは、 http://localhost:&lt;port&gt;/ でホストされます。  サポートされている操作の一覧については、「[Web API](WebAPI.md)」を参照してください。

Azure 環境以外の場合、ローカルでホストされるサービスは、最大 1 MB のサイズのインデックス ファイル、毎秒 10 要求、合計 1,000 回の呼び出しに制限されます。  これらの制限をなくすには、Azure VM 内で **host_service** を実行するか、**deploy_service** を使用して Azure クラウド サービスにデプロイします。

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service コマンド

**deploy_service** コマンドは、KES サービスのインスタンスを Azure クラウド サービスにデプロイします。

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| パラメーター       | 説明                  |
|-----------------|------------------------------|
| `<grammarFile>` | 入力バイナリ文法のパス           |
| `<indexFile>`   | 入力バイナリ インデックスのパス             |
| `<serviceName>` | ターゲット クラウド サービスの名前 |
| `<vmSize>`      | クラウド サービス VM のサイズ     |
| `--slot <slot>` | クラウド サービス スロット: "staging" (ステージング) (既定)、"production" (運用) |

これらのファイルは、ローカル ファイルのパスまたは AzureBLOB への URL パスによって指定できます。  サービス名には、事前構成済みの Azure クラウド サービスを指定します (「[クラウド サービスを作成してデプロイする方法](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)」を参照してください)。  このコマンドは、指定したサイズの VM を使用して、指定した Azure クラウド サービスに KES サービスを自動的にデプロイします。  パフォーマンスを大幅に低下させるページングを回避するために、入力インデックス ファイル サイズより 1 GB 多い RAM サイズの VM を使用することをお勧めします。  利用可能な VM サイズの一覧トについては、「[Cloud Services のサイズ](../../../articles/cloud-services/cloud-services-sizes-specs.md)」を参照してください。

既定では、サービスはステージング環境にデプロイされます。必要に応じて --slot パラメーターで上書きすることができます。  サポートされている操作の一覧については、「[Web API](WebAPI.md)」を参照してください。

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>describe_index コマンド

**describe_index** コマンドは、スキーマや説明など、インデックス ファイルに関する情報を出力します。

`kes.exe describe_index <indexFile>`

| パラメーター     | 説明      |
|---------------|------------------|
| `<indexFile>` | 入力インデックスのパス |

このファイルは、ローカル ファイルのパスまたは Azure BLOB への URL パスで指定できます。  出力の説明文字列は、**build_index** コマンドの --description パラメーターを使用して指定できます。

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>describe_grammar コマンド

**describe_grammar** コマンドは、バイナリ文法のビルドに使用された元の文法仕様を出力します。

`kes.exe describe_grammar <grammarFile>`

| パラメーター       | 説明      |
|-----------------|------------------|
| `<grammarFile>` | 入力文法のパス |

このファイルは、ローカル ファイルのパスまたは Azure BLOB への URL パスで指定できます。

