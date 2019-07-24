---
title: 例:使用開始 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API を使用して、教育パブリケーションにわたる対話形式の検索エクスペリエンスのためのエンジンを作成できます。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 00c5ed3e3ea5c083f727d06c2ed305fe35ed03db
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523326"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Knowledge Exploration Service の作業を開始する

このチュートリアルでは、Knowledge Exploration Service (KES) を使用して、教育パブリケーションを対話形式で検索するエンジンを作成します。 [Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488) から、コマンド ライン ツール [`kes.exe`](CommandLine.md) とすべてのサンプル ファイルをインストールできます。

教育パブリケーションのサンプルには、マイクロソフトの調査担当者によって発行された 1000 個の学術論文の例が含まれています。  各論文は、論文名、発行年、著者、およびキーワードに関連付けられています。 各著者は、ID、氏名、および発行時の所属組織で表されます。 各キーワードは、一連のシノニムを関連付けることができます (たとえば、キーワード「サポート ベクター マシン」をシノニム "svm" に関連付けられます)。

## <a name="define-the-schema"></a>スキーマを定義する

スキーマは、ドメイン内のオブジェクトの属性の構造を記述します。 スキーマは、JSON ファイルの形式で、各属性の名前とデータ型を指定します。 *Academic.schema* ファイルの内容は、次の例のようになります。

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

ここでは、*Title*、*Year*、および *Keyword* をそれぞれ文字列、整数および文字列属性として定義します。 著者は、ID、氏名、所属組織で表されるため、*Author* は、3 つのサブ属性の複合属性として定義します (*Author.Id*、*Author.Name*、および *Author.Affiliation*)。

既定では、属性は、*equals*、*starts_with*、および *is_between* を含む、そのデータ型で使用可能なすべての操作をサポートします。 author ID は識別子として内部でのみ使用されるため、既定値を上書きし、インデックス操作として *equals* のみを指定します。

*Keyword* 属性については、シノニムが Canonical キーワードと一致するように、属性定義に *Keyword.syn* シノニム ファイルを指定します。 このファイルには、Canonical およびシノニムの値のペアが含まれています。

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

スキーマの定義の詳細については、[スキーマ形式](SchemaFormat.md)に関する記事を参照してください。

## <a name="generate-data"></a>データを生成する

データ ファイルは、各行が [JSON 形式](https://json.org/)で論文の属性値を指定し、インデックスを作成するパブリケーションの一覧を記述します。  次の例は、読みやすいように書式を設定したデータ ファイル *Academic.data* からの 1 行です。

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

このスニペットは、論文の *Title* と *Year* 属性を それぞれ JSON 文字列と数として指定します。 JSON 配列を使用して複数の値を表します。 *Author* は複合属性であるため、各値は、サブ属性で構成される JSON オブジェクトを使用して表されます。 この場合、*Keyword* などの値が欠けている属性は、JSON 表現から除外することができます。

異なる論文の確率を区別するために、組み込み属性 *logprob* を使用して相対確率を指定します。 確率 *p* を0 から 1 の間に指定した場合、対応する対数確率は log(*p*) として計算できます。ここで、log() は自然対数関数です。

詳細については、[データ形式](DataFormat.md)に関するページを参照してください。

## <a name="build-a-compressed-binary-index"></a>圧縮バイナリ インデックスを構築する

スキーマ ファイルとデータ ファイルを作成したら、[`kes.exe build_index`](CommandLine.md#build_index-command) を使用してデータ オブジェクトの圧縮バイナリ インデックスを構築します。 この例では、インデックス ファイル *Academic.index* を入力スキーマ ファイル *Academic.schema* とデータ ファイル *Academic.data* から作成します。 次のコマンドを使用します。

`kes.exe build_index Academic.schema Academic.data Academic.index`

Azure の外でプロトタイプを簡単に作成するために、[`kes.exe build_index`](CommandLine.md#build_index-command) は、最大 10,000 個のオブジェクトを含むデータ ファイルから小型のインデックスをローカルに構築できます。 サイズが大きいデータ ファイルの場合は、[Azure の Windows 仮想マシン](../../../articles/virtual-machines/windows/quick-create-portal.md)内から実行するか、または Azure でリモート ビルドを実行します。 詳細については、スケールアップに関する記事を参照してください。

## <a name="use-an-xml-grammar-specification"></a>XML 文法仕様を使用する

文法は、サービスが解釈できる自然言語のクエリのセットと、これらの自然言語のクエリをセマンティック クエリ式に翻訳する方法を指定します。 この例では、*academic.xml* に指定された文法を使用します。

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

文法仕様構文の詳細については、[文法形式](GrammarFormat.md)に関する記事を参照してください。

## <a name="compile-the-grammar"></a>文法をコンパイルする

XML 文法仕様を指定したら、[`kes.exe build_grammar`](CommandLine.md#build_grammar-command) を使用してバイナリ文法にコンパイルできます。 文法でスキーマをインポートする場合は、文法 XML と同じパスにスキーマ ファイルを保存する必要があることに注意してください。 この例では、入力 XML 文法ファイル *Academic.xml* からバイナリ文法ファイル *Academic.grammar* を構築します。 次のコマンドを使用します。

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Web サービスで文法とインデックスをホストする

迅速なプロトタイプ作成のため、[`kes.exe host_service`](CommandLine.md#host_service-command) を使用してローカル コンピューター上の Web サービス内の文法とインデックスをホストできます。 これにより、[web API](WebAPI.md) 経由でサービスにアクセスし、データの正確性と文法の設計を検証できます。 この例では、文法ファイル *Academic.grammar* とインデックス ファイル *Academic.index* を`http://localhost:8000/` でホストします。 次のコマンドを使用します。

`kes.exe host_service Academic.grammar Academic.index --port 8000`

これにより、Web サービスのローカル インスタンスが開始されます。 ブラウザーから `http::localhost:<port>` にアクセスすることにより、対話形式でサービスをテストできます。 詳細については、サービスのテストに関する記事をご覧ください。

また、さまざまな [web API](WebAPI.md) を直接呼び出すことによって、自然言語の解釈、クエリの完了、構造化されたクエリの評価、およびヒストグラムの計算をテストできます。 サービスを停止するには、`kes.exe host_service` コマンド プロンプトに "quit" と入力するか、または Ctrl キーを押しながら C キーを押します。 次に例をいくつか示します。

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Azure の外部では、[`kes.exe host_service`](CommandLine.md#host_service-command) は最大 10,000 個のオブジェクトのインデックスに制限されます。 その他の制限には、1 秒あたり要求 10 個の API 速度や、プロセスが自動的に終了する前に、合計 1000 要求が含まれます。 これらの制限を回避するには、[Azure の Windows 仮想マシン](../../../articles/virtual-machines/windows/quick-create-portal.md)内からコマンドを実行するか、[`kes.exe deploy_service`](CommandLine.md#deploy_service-command) コマンドを使用して Azure クラウド サービスに展開します。 詳細については、サービスの展開に関する記事を参照してください。

## <a name="scale-up-to-host-larger-indices"></a>より大規模なインデックスをホストするためにスケールアップする

Azure の外で `kes.exe` を実行している場合は、インデックスは 10,000 オブジェクトに制限されます。 Azure を使用してより大規模なインデックスを構築しホストできます。 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップします。 また、Visual Studio または MSDN をサブスクライブしている場合は、[サブスクライバー特典を利用](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)できます。 これらの特典は、毎月 Azure クレジットを提供します。

`kes.exe` が Azure アカウントにアクセスできるようにするには、Azure Portal から [Azure 発行設定ファイルをダウンロード](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)します。 メッセージが表示されたら、必要な Azure アカウントにサインインします。 ファイルを、`kes.exe` を実行する作業ディレクトリに *AzurePublishSettings.xml* として保存します。

大規模なインデックスを構築しホストするには、2 つの方法があります。 1 つ目の方法では、Azure の Windows 仮想マシンにスキーマとデータ ファイルを準備します。 次に、`kes.exe build_index` を実行して VM 上にローカルに、サイズ制限なしでインデックスを構築します。 構築したインデックスは、迅速なプロトタイプ作成のために `kes.exe host_service` を使用して VM 上でローカルにホストできます。 手順の詳細については、[Azure VM のチュートリアル](../../../articles/virtual-machines/windows/quick-create-portal.md)を参照してください。

2 つ目の方法では、`--remote` パラメーターを指定した [`kes.exe build_index`](CommandLine.md#build_index-command) を使用して、リモートで Azure ビルドを実行します。 これにより、Azure VM のサイズが指定されます。 `--remote` パラメーターを指定すると、コマンドは、そのサイズで一時的な Azure VM を作成します。 次に、VM 上にインデックスが構築され、インデックスがターゲット BLOB ストレージにアップロードされ、完了時に VM が削除されます。 インデックスが構築されると、VM のコストが Azure サブスクリプションに課金されます。

このリモートの Azure ビルド機能により、[`kes.exe build_index`](CommandLine.md#build_index-command) をあらゆる環境で実行できます。 リモート ビルドを実行している場合は、入力スキーマおよびデータの引数にはローカル ファイル パス、または [Azure BLOB ストレージ](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) URL を指定できます。 出力インデックスの引数は、BLOB ストレージの URL である必要があります。 Azure ストレージ アカウントの作成方法については、「[Azure ストレージ アカウントについて](../../storage/common/storage-create-storage-account.md)」を参照してください。 BLOB ストレージから、あるいは BLOB ストレージへ効率的にファイルをコピーするには、[AzCopy](../../storage/common/storage-use-azcopy.md) ユーティリティを使用します。

この例では、次の BLOB ストレージ コンテナーが既に作成されていることが前提です: http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/。 このコンテナーには、スキーマ *Academic.schema* を含み、参照先シノニム ファイル *Keywords.syn* およびフルスケール データ ファイル *Academic.full.data* が含まれます。 次のコマンドを使用してリモートでフル インデックスを構築できます。

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

インデックスを構築するための一時 VM をプロビジョニングするには、5 分から 10 分かかる場合があります。 迅速にプロトタイプを作成するため、次のことができます。
- 任意のコンピューター上でローカルにサイズの小さいデータ セットを使用して開発します。
- 手動で [Azure VM を作成](../../../articles/virtual-machines/windows/quick-create-portal.md)し、リモート デスクトップを使用して[接続し](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)、[Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488) をインストール、VM 内 から [`kes.exe`](CommandLine.md) を実行します。

ページングを使用すると、ビルド処理の速度が遅くなります。 ページングを避けるには、インデックス構築に使用する入力データ ファイルのサイズの 3 倍の容量の RAM を備えた VM を使用します。 ホストするインデックスのサイズよりも 1 GB 多い RAM を備えた VM を使用します。 利用可能な VM サイズの一覧については、「[Azure の Windows 仮想マシンのサイズ](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)」を参照してください。

## <a name="deploy-the-service"></a>サービスをデプロイする

文法とインデックスを構築したら、Azure クラウド サービスにサービスをデプロイできます。 Azure クラウド サービスの構成の詳細については、「[クラウド サービスを作成してデプロイする方法](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)」をご覧ください。 この時点では、展開パッケージは指定しないでください。  

クラウド サービスを作成したら、[`kes.exe deploy_service`](CommandLine.md#deploy_service-command) を使用してサービスをデプロイします。 Azure クラウド サービスには、運用環境とステージングの 2 つのデプロイ スロットがあります。 ライブ ユーザー トラフィックを受信するサービスの場合は、まずステージング スロットに展開する必要があります。 サービスが起動され、初期化されるまで待ちます。 次に、いくつかの要求を送信して、デプロイを検証し、基本的なテストに合格することを確認できます。

ステージング スロットの内容を運用スロットの内容と[スワップ](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md)し、新しく展開されたサービスにライブ トラフィックを転送します。 新しいデータでサービスの更新バージョンを展開するときには、この処理を繰り返すことができます。 他のすべての Azure クラウド サービスと同様、Azure Portal を使用して[自動スケーリング](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md)を設定できます。

この例では、*\<vm_size>* の VM の既存のクラウド サービスのステージング スロットに、*Academic* インデックスを展開します。 次のコマンドを使用します。

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

利用可能な VM サイズの一覧については、「[Azure の Windows 仮想マシンのサイズ](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)」を参照してください。

サービスを展開したら、さまざまな [web API](WebAPI.md) を直接呼び出すことによって、自然言語の解釈、クエリの完了、構造化されたクエリの評価、およびヒストグラムの計算をテストできます。  

## <a name="test-the-service"></a>サービスをテストする

ライブ サービスをデバッグするには、Web ブラウザーからホスト コンピューターを参照します。 host_service 経由で展開されているローカル サービスの場合は、`http://localhost:<port>/` にアクセスします。  deploy_service 経由で展開されている Azure クラウド サービスの場合は、`http://<serviceName>.cloudapp.net/` にアクセスします。

このページには、基本的な API 呼び出しの統計情報だけでなく、このサービスでホストされている文法とインデックスに関する情報へのリンクが含まれています。 このページには、web API の使用方法を示す対話形式の検索インターフェイスも含まれています。 検索ボックスにクエリを入力し、API 呼び出しの[解釈](interpretMethod.md)、[評価](evaluateMethod.md)、および [calchistogram](calchistogramMethod.md) の結果を確認してください。 このページの基になる HTML ソースは、web API をアプリに統合し、対話型の多機能な検索エクスペリエンスを作成する方法の例としても機能します。


