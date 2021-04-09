---
title: コア IO 操作 | Microsoft Azure Maps
description: 空間 IO モジュールのコア ライブラリを使用して、XML および区切られたデータの読み取りと書き込みを効率的に行う方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 08966549d2c4d2bf701ee0dbfc5ed24f202396fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891378"
---
# <a name="core-io-operations"></a>コア IO 操作

空間 IO モジュールは、空間データ ファイルを読み取るツールを提供するだけでなく、XML および区切られたデータの読み取りと書き込みをすばやく効率的に行うための、基になるコア ライブラリを公開します。

`atlas.io.core` 名前空間には、CSV および XML のデータをすばやく読み書きできる 2 つの下位クラスが含まれています。 これらの基底クラスは、空間 IO モジュールの空間データ リーダーとライターに役立ちます。 これらを自由に使用して、CSV または XML ファイルの読み取りと書き込みのサポートを追加してください。
 
## <a name="read-delimited-files"></a>区切りファイルを読み取る

`atlas.io.core.CsvReader` クラスは、区切られたデータ セットを含む文字列を読み取ります。 このクラスには、データを読み取るための 2 つのメソッドが用意されています。

- `read` 関数は、完全なデータ セットを読み取り、区切られたデータ セットのすべてのセルを表す文字列の 2 次元配列を返します。
- `getNextRow` 関数は、区切られたデータ セット内の各テキスト行を読み取り、そのデータ セット行のすべてのセルを表す文字列の配列を返します。 ユーザーは、行を処理し、その行の不要なメモリをすべて破棄してから、次の行を処理できます。 そのため、関数の方がメモリ効率が高くなります。

既定では、リーダーは区切り記号としてコンマ文字を使用します。 ただし、区切り記号は、任意の 1 文字に変更することも、`'auto'` に設定することもできます。 `'auto'` に設定すると、リーダーは文字列の最初のテキスト行を分析します。 次に、区切り記号として使用する最も一般的な文字を以下の表から選択します。

| 区切り記号 | 文字 |
| :-- | :-- |
| コンマ | `,` |
| タブ | `\t` |
| Pipe | `|` |

また、このリーダーでは、区切り文字を含むセルの処理に使用されるテキスト修飾子もサポートされます。 引用符 (`'"'`) 文字が既定のテキスト修飾子ですが、これは任意の 1 文字に変更できます。

## <a name="write-delimited-files"></a>区切りファイルを書き込む

`atlas.io.core.CsvWriter` は、オブジェクトの配列を区切られた文字列として書き込みます。 任意の 1 文字を区切り記号またはテキスト修飾子として使用できます。 既定の区切り記号はコンマ (`','`) で、既定のテキスト修飾子は引用符 (`'"'`) 文字です。

このクラスを使用するには、次の手順に従います。

- クラスのインスタンスを作成し、必要に応じてカスタムの区切り記号またはテキスト修飾子を設定します。
- `write` 関数または `writeRow` 関数を使用して、クラスにデータを書き込みます。 `write` 関数の場合は、複数の行とセルを表すオブジェクトの 2 次元配列を渡します。 `writeRow` 関数を使用するには、複数の列を含むデータ行を表すオブジェクトの配列を渡します。
- `toString` 関数を呼び出して、区切られた文字列を取得します。 
- 必要に応じて、`clear` メソッドを呼び出して、ライターを再利用できるようにしてそのリソース割り当てを減らすか、`delete` メソッドを呼び出してライター インスタンスを破棄します。

> [!Note]
> 書き込まれる列の数は、ライターに渡されるデータの最初の行に含まれるセルの数に制限されます。

## <a name="read-xml-files"></a>XML ファイルを読み取る

`atlas.io.core.SimpleXmlReader` クラスは、`DOMParser` よりもすばやく XML ファイルを解析できます。 ただし、`atlas.io.core.SimpleXmlReader` クラスでは、XML ファイルを正しい形式にする必要があります。 XML ファイルが正しい形式でない (たとえば、終了タグがない) 場合は、エラーが発生する可能性があります。

次のコードは、`SimpleXmlReader` クラスを使用して XML 文字列を解析して JSON オブジェクトにし、それを目的の形式にシリアル化する方法を示しています。

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>XML ファイルを書き込む

`atlas.io.core.SimpleXmlWriter` クラスは、正しい形式の XML をメモリ効率の高い方法で書き込みます。

次のコードは、`SimpleXmlWriter` クラスを使用して正しい形式の XML 文字列を生成する方法を示しています。

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

上記のコードから生成された XML は、次のようになります。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

[CsvReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

[サポートされるデータ形式の詳細](spatial-io-supported-data-format-details.md)