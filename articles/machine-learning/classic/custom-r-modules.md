---
title: ML Studio (classic):カスタム R モジュールの作成とデプロイ - Azure
description: ML Studio (クラシック) でカスタム R モジュールを作成してデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: d44f2cfa72bd53b01da073fca31ca698eb42720d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520478"
---
# <a name="define-custom-r-modules-for-machine-learning-studio-classic"></a>Machine Learning Studio (classic) 用のカスタム R モジュールを定義する

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

このトピックでは、カスタム R Studio (クラシック) を作成してデプロイする方法について説明します。 カスタム R モジュールの概要と、このモジュールの定義に使用するファイルについて説明します。 また、モジュールを定義するファイルを作成する方法と、Machine Learning ワークスペースにデプロイするためにモジュールを登録する方法も示します。 カスタム モジュールの定義で使用する要素および属性についてさらに詳しく説明します。 補助機能と補助ファイルおよび複数の出力を使用する方法についても説明します。 

**カスタム モジュール** とは、ユーザーのワークスペースにアップロードし、Azure Machine Learning Studio (クラシック) の実験の一部として実行できるユーザー定義モジュールです。 **カスタム R モジュール** とは、ユーザー定義の R 関数を実行するカスタム モジュールです。 **R** とは、アルゴリズムを実装するために統計学者やデータ科学者によって広く使用されている統計コンピューティングおよびグラフィックス用のプログラミング言語です。 現在、カスタム モジュールでサポートされている言語は R だけですが、今後のリリースで他の言語のサポートが追加される予定です。

カスタム モジュールは、他のすべてのモジュールと同様に使用できるという意味で、Azure Machine Learning Studio (クラシック) における **ファーストクラスのステータス** を持っています。 これは、公開された実験や視覚化に含まれる他のモジュールとともに実行できます。 制御できるのは、モジュールによって実装されたアルゴリズム、使用される入出力ポート、モデリング パラメーターなど、さまざまな実行時の動作です。 また、カスタム モジュールが含まれる実験を Azure AI Gallery に公開して簡単に共有することもできます。

## <a name="files-in-a-custom-r-module"></a>カスタム R モジュールのファイル
カスタム R モジュールは、少なくとも以下の 2 つのファイルが含まれる .zip ファイルによって定義されます。

* モジュールによって公開される R 関数を実装した **ソース ファイル**
* カスタム モジュール インターフェイスを記述した **XML 定義ファイル**

カスタム モジュールからアクセスできる機能を提供する追加の補助ファイルも .zip ファイルに含まれる場合があります。 このオプションについては、クイックスタートの例を紹介した後、リファレンス セクション「**XML 定義ファイルの要素**」の「**引数**」で説明します。

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>クイックスタートの例: カスタム R モジュールの定義、パッケージ化、登録
この例では、カスタム R モジュールに必要なファイルを作成し、それらのファイルを zip ファイルにパッケージ化して、Machine Learning ワークスペースにモジュールを登録する方法を示します。 この例の zip パッケージとサンプル ファイルは、 [ここ](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409)からダウンロードできます。

## <a name="the-source-file"></a>ソース ファイル
2 つのデータセット (データ フレーム) の行 (監視) を連結するために使用される **Add Rows** モジュールの標準実装を変更する **Custom Add Rows** モジュールの例について考えてみます。 標準の **Add Rows** モジュールは、`rbind` アルゴリズムを使用して、最初の入力データセットの末尾に、2 番目の入力データセットの行を追加します。 カスタマイズされた `CustomAddRows` 関数も 2 つのデータセットを同様に受け入れますが、追加の入力としてブール型スワップ パラメーターも受け取ります。 スワップ パラメーターが **FALSE** に設定されている場合は、標準実装と同じデータセットが返されますが、 **TRUE** の場合、関数は、最初の入力データセットの行を、2 番目のデータセットの末尾に追加します。 **Custom Add Rows** モジュールによって公開される R `CustomAddRows` 関数の実装が含まれる CustomAddRows.R ファイルには、次の R コードが含まれます。

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
{
    if (swap)
    {
        return (rbind(dataset2, dataset1));
    }
    else
    {
        return (rbind(dataset1, dataset2));
    } 
} 
```

### <a name="the-xml-definition-file"></a>XML 定義ファイル
この `CustomAddRows` 関数を Azure Machine Learning Studio (クラシック) モジュールとして公開するには、**Custom Add Rows** モジュールの外観や動作を指定するために XML 定義ファイルを作成する必要があります。 

```xml
<!-- Defined a module using an R Script -->
<Module name="Custom Add Rows">
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

<!-- Specify the base language, script file and R function to use for this module. -->        
    <Language name="R" 
        sourceFile="CustomAddRows.R" 
        entryPoint="CustomAddRows" />  

<!-- Define module input and output ports -->
<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
    <Ports>
        <Input id="dataset1" name="Dataset 1" type="DataTable">
            <Description>First input dataset</Description>
        </Input>
        <Input id="dataset2" name="Dataset 2" type="DataTable">
            <Description>Second input dataset</Description>
        </Input>
        <Output id="dataset" name="Dataset" type="DataTable">
            <Description>The combined dataset</Description>
        </Output>
    </Ports>

<!-- Define module parameters -->
    <Arguments>
        <Arg id="swap" name="Swap" type="bool" >
            <Description>Swap input datasets.</Description>
        </Arg>
    </Arguments>
</Module>
```

XML ファイル内の **Input** 要素と **Arg** 要素の **id** 属性の値が、CustomAddRows.R ファイルの R コードの関数パラメーター名 (この例では、*dataset1*、*dataset2*、および *swap*) と正確に一致する必要があることに注意します。 同様に、**Language** 要素の **entryPoint** 属性の値が、R スクリプトの関数名 (この例では *CustomAddRows*) と正確に一致する必要があります。 

これに対して、**Output** 要素の **id** 属性は、R スクリプトのどの変数にも対応していません。 複数の出力が必要な場合は、XML ファイルで *Outputs* 要素が宣言されている順序と " **同じ順序** " で結果が配置されたリストを R 関数から返します。

### <a name="package-and-register-the-module"></a>モジュールのパッケージ化と登録
この 2 つのファイルを *CustomAddRows.R* および *CustomAddRows.xml* として保存し、*CustomAddRows.zip* ファイルに zip 圧縮します。

Machine Learning ワークスペースでそれらを登録するには、Azure Machine Learning Studio (クラシック) でそのワークスペースに移動し、一番下にある **[+ 新規]** ボタンをクリックし、 **[モジュール] -> [zip パッケージから]** を選択して新しい **Custom Add Rows** モジュールをアップロードします。

![Zip のアップロード](./media/custom-r-modules/upload-from-zip-package.png)

これで、 **Machine Learning** の実験で Custom Add Rows モジュールにアクセスできるようになります。

## <a name="elements-in-the-xml-definition-file"></a>引数
### <a name="module-elements"></a>Module 要素
**Module** 要素は、XML ファイルでカスタム モジュールを定義する際に使用します。 1 つの XML ファイルで複数の **Module** 要素を使用することで、複数のモジュールを定義できます。 ワークスペース内の各モジュールには、一意の名前が必要です。 既存のカスタム モジュールと同じ名前でカスタム モジュールを登録すると、既存のモジュールが新しいモジュールに置き換えられます。 ただし、既存の Azure Machine Learning Studio (クラシック) モジュールと同じ名前でカスタム モジュールを登録できます。 この場合、カスタム モジュールはモジュール パレットの **[カスタム]** カテゴリに表示されます。

```xml
<Module name="Custom Add Rows" isDeterministic="false"> 
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another...</Description>/> 
```

**Module** 要素内では、2 つの追加要素をオプションで指定できます。

* 1 つは **Owner** 要素で、これはモジュールに組み込まれています  
* もう 1 つは **Description** 要素で、モジュールのクイック ヘルプに表示されるテキストが含まれています。これは、Machine Learning の UI でモジュールをポイントしたときに表示されます。

Module 要素内での文字数制限に関する規則:

* **Module** 要素内の **name** 属性の値は、64 文字以内で指定する必要があります。 
* **Description** 要素の内容は、128 文字以内で指定する必要があります。
* **Owner** 要素の内容は、32 文字以内で指定する必要があります。

モジュールの結果は確定的または非確定的です。** 既定では、すべてのモジュールが確定的であると見なされます。 つまり、一連の入力パラメーターとデータが変更されないものと想定して、モジュールは実行されるたびに同じ結果を返します。 この動作のため、パラメーターまたは入力データが変更された場合、Azure Machine Learning Studio (クラシック) は確定的としてマークされたモジュールのみを再実行します。 キャッシュされた結果が返されるので、実験の実行もかなり高速化されます。

RAND、現在の日付または時刻を返す関数など、非確定的な関数もあります。 モジュールが非確定的な関数を使用している場合は、オプションの **isDeterministic** 属性を **false** に設定することで、モジュールが非確定的であることを指定できます。 これにより、モジュールの入力やパラメーターが変更されていなくても、実験が実行されたときに必ず、モジュールが再実行されます。 

### <a name="language-definition"></a>言語定義
XML 定義ファイル内の **Language** 要素は、カスタム モジュールの言語を指定するために使用します。 現在、サポートされている言語は R だけです。 **sourceFile** 属性の値には、モジュールの実行時に呼び出す関数が含まれた R ファイルの名前を指定する必要があります。 このファイルは zip パッケージに含める必要があります。 **entryPoint** 属性の値は呼び出される関数の名前であり、ソース ファイルで定義されている有効な関数と一致する必要があります。

```xml
<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />
```

### <a name="ports"></a>Port
カスタム モジュールの入力ポートと出力ポートは、XML 定義ファイルの **Ports** セクションの子要素で指定します。 これらの要素の順序によって、ユーザーに表示されるレイアウト (UX) が決まります。 XML ファイルの **Ports** 要素内に示される最初の子 **input** または **output** は、Machine Learning の UX で一番左の入力ポートになります。
各入力ポートと出力ポートは、Machine Learning の UI でポートの上にマウス カーソルが置かれたときに表示されるテキストを指定する、オプションの **Description** 子要素を持つことができます。

**ポートの規則**:

* **入力ポートと出力ポート** の最大数はそれぞれ 8 個です。

### <a name="input-elements"></a>Input 要素
入力ポートを使用すると、R 関数とワークスペースにデータを渡すことができます。 入力ポートでサポートされている **データ型** は次のとおりです。 

**DataTable:** この型は、data.frame として R 関数に渡されます。 実際には、Machine Learning でサポートされている型および **DataTable** と互換性のある型 (CSV ファイルや ARFF ファイルなど) が data.frame に自動的に変換されます。 

```xml
<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
    <Description>Input Dataset 1</Description>
</Input>
```

各 **DataTable** 入力ポートに関連付けられた **id** 属性には一意の値が必要です。この値は、R 関数の対応する名前付きパラメーターと一致する必要があります。
実験で入力として渡されないオプションの **DataTable** ポートは、R 関数に渡される値として **NULL** を使用します。入力が接続されていない場合、オプションの Zip ポートは無視されます。 **DataTable** 型と **Zip** 型 のどちらについても、**IsOptional** 属性は省略可能です。この属性は、既定で *false* に設定されます。

**Zip:** カスタム モジュールでは、zip ファイルを入力として受け取ることができます。 この入力は、関数の R 作業ディレクトリにアンパックされます。

```xml
<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
    <Description>Zip files to be extracted to the R working directory.</Description>
</Input>
```

カスタム R モジュールでは、Zip ポートの ID は R 関数のどのパラメーターとも一致する必要はありません。 これは、zip ファイルは自動的に R 作業ディレクトリに展開されるためです。

**入力の規則:**

* **Input** 要素の **id** 属性の値には、有効な R 変数名を指定する必要があります。
* **Input** 要素の **id** 属性の値は、64 文字以内で指定する必要があります。
* **Input** 要素の **name** 属性の値は、64 文字以内で指定する必要があります。
* **Description** 要素の内容は、128 文字以内で指定する必要があります。
* **Input** 要素の **type** 属性の値には、*Zip* または *DataTable* を指定する必要があります。
* **Input** 要素の **IsOptional** 属性の値は省略可能です (指定されていない場合、既定で *false* に設定されます)。ただし、この値を指定する場合は、*true* または *false* を指定する必要があります。

### <a name="output-elements"></a>Output 要素
**標準出力ポート:** 出力ポートは R 関数の戻り値にマップされ、後続のモジュールで使用できます。 現在サポートされている標準出力ポートの型は *DataTable* だけです (*Learners* と *Transforms* がサポートされる予定です)。*DataTable* 出力は、次のように定義します。

```xml
<Output id="dataset" name="Dataset" type="DataTable">
    <Description>Combined dataset</Description>
</Output>
```

カスタム R モジュールの出力では、**id** 属性の値は R スクリプトの何かと対応する必要はありませんが、一意でなければなりません。 モジュールの出力が 1 つの場合、R 関数の戻り値は *data.frame* である必要があります。 サポート対象のデータ型の複数のオブジェクトを出力するために、適切な出力ポートを XML 定義ファイルで指定する必要があります。また、オブジェクトをリストとして返す必要があります。 返されたリストに配置されたオブジェクトの順序を反映して、左から右に、出力オブジェクトが出力ポートに割り当てられます。

たとえば、新しい結合データセット (*dataset*) に加え、元の 2 つのデータセット (*dataset1* と *dataset2*) を (*dataset*、*dataset1*、*dataset2* の順に左から右に) 出力するように **Custom Add Rows** モジュールを変更する場合、CustomAddRows.xml ファイルで出力ポートを次のように定義します。

```xml
<Ports> 
    <Output id="dataset" name="Dataset Out" type="DataTable"> 
        <Description>New Dataset</Description> 
    </Output> 
    <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
        <Description>First Dataset</Description> 
    </Output> 
    <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
        <Description>Second Dataset</Description> 
    </Output> 
    <Input id="dataset1" name="Dataset 1" type="DataTable"> 
        <Description>First Input Table</Description>
    </Input> 
    <Input id="dataset2" name="Dataset 2" type="DataTable"> 
        <Description>Second Input Table</Description> 
    </Input> 
</Ports> 
```

"CustomAddRows.R" 内のリストの正しい順序で、オブジェクトのリストを返します。

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
    if (swap) { dataset <- rbind(dataset2, dataset1)) } 
    else { dataset <- rbind(dataset1, dataset2)) 
    } 
    return (list(dataset, dataset1, dataset2)) 
} 
```

**視覚化出力** : R グラフィックス デバイスからの出力とコンソール出力を表示する *Visualization* 型の出力ポートを指定することもできます。 このポートは R 関数の出力には含まれず、他の出力ポートの型の順序に干渉しません。 カスタム モジュールに視覚化ポートを追加するには、**type** 属性の値として *Visualization* を指定した **Output** 要素を追加します。

```xml
<Output id="deviceOutput" name="View Port" type="Visualization">
    <Description>View the R console graphics device output.</Description>
</Output>
```

**出力の規則:**

* **Output** 要素の **id** 属性の値には、有効な R 変数名を指定する必要があります。
* **Output** 要素の **id** 属性の値は、32 文字以内で指定する必要があります。
* **Output** 要素の **name** 属性の値は、64 文字以内で指定する必要があります。
* **Output** 要素の **type** 属性の値には、*Visualization* を指定する必要があります。

### <a name="arguments"></a>引数
**Arguments** 要素内で定義されたモジュール パラメーターを使用して、R 関数に追加データを渡すことができます。 これらのパラメーターは、モジュールを選択したときに、Machine Learning の UI の一番右にあるプロパティ ウィンドウに表示されます。 引数はサポートされているどの型でもかまいません。また、必要に応じてカスタム列挙型を作成することもできます。 **Ports** 要素と同様に、**Arguments** 要素では、パラメーター名の上にマウスを置いたときに表示されるテキストを指定するオプションの **Description** 要素を使用できます。
モジュールの省略可能なプロパティ (defaultValue、minValue、maxValue など) を、**Properties** 要素の属性として引数に追加できます。 **Properties** 要素の有効なプロパティは引数の型によって異なります。これについては、サポートされる引数の型と合わせて次のセクションで説明します。 **isOptional** プロパティが **"true"** に設定されている引数は、ユーザーが値を入力する必要はありません。 引数に値が指定されていない場合、エントリ ポイント関数に引数は渡されません。 エントリ ポイント関数のオプション引数は、関数によって明示的に処理する必要があります (エントリ ポイント関数定義で既定値 NULL を割り当てるなど)。 ユーザーが値を指定した場合、オプション引数は、その他の引数の制約 (最小または最大) のみを適用します。
入力および出力と同様、各パラメーターで一意の ID 値をそれらに関連付けることが重要になります。 このクイックスタートの例では、関連付けられている ID とパラメーターは *swap* です。

### <a name="arg-element"></a>Arg 要素
モジュール パラメーターは、XML 定義ファイルの **Arguments** セクションの **Arg** 子要素を使用して定義します。 **Ports** セクションの子要素と同様に、**Arguments** セクションでのパラメーターの順序によって UX で発生するレイアウトが定義されます。 UI では、パラメーターは XML ファイルで定義されている順序で上から下に表示されます。 パラメーターについて Machine Learning によってサポートされるタイプを次に示します。 

**int** - 整数 (32 ビット) 型パラメーター。

```xml
<Arg id="intValue1" name="Int Param" type="int">
    <Properties min="0" max="100" default="0" />
    <Description>Integer Parameter</Description>
</Arg>
```

* *省略可能なプロパティ*: **min**、**max**、**default**、**isOptional**

**double** - 倍精度浮動小数点型パラメーター。

```xml
<Arg id="doubleValue1" name="Double Param" type="double">
    <Properties min="0.000" max="0.999" default="0.3" />
    <Description>Double Parameter</Description>
</Arg>
```

* *省略可能なプロパティ*: **min**、**max**、**default**、**isOptional**

**bool** - UX のチェック ボックスで表されるブール型パラメーター。

```xml
<Arg id="boolValue1" name="Boolean Param" type="bool">
    <Properties default="true" />
    <Description>Boolean Parameter</Description>
</Arg>
```

* *省略可能なプロパティ*: **default** - 設定されていない場合は false

**string**- 標準的な文字列。

```xml
<Arg id="stringValue1" name="My string Param" type="string">
    <Properties isOptional="true" />
    <Description>String Parameter 1</Description>
</Arg>    
```

* *省略可能なプロパティ*: **default**、**isOptional**

**ColumnPicker** - 列選択パラメーター。 この型は、列の選択として UX に表示されます。 ここでは、**Properties** 要素を使用して、列が選択されたポートの ID を指定しています。この場合、ターゲット ポートの型は *DataTable* である必要があります。 列選択の結果は、選択された列名を含む文字列のリストとして R 関数に渡されます。 

```xml
<Arg id="colset" name="Column set" type="ColumnPicker">      
    <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
    <Description>Column set</Description>
</Arg>
```

* *必須プロパティ*: **portId** - *DataTable* 型の Input 要素の ID と一致します。
* *省略可能なプロパティ*:
  
  * **allowedTypes** - 選択できる列の型をフィルター処理します。 有効な値は、次のとおりです。 
    
    * 数値
    * ブール値
    * Categorical
    * String
    * Label
    * 特徴量
    * Score
    * All
  * **default** - 列の選択の有効な既定の選択内容は次のとおりです。 
    
    * なし
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * All

**DropDown**: ユーザーが指定した列挙型 (ドロップダウン) リスト。 ドロップダウン項目は、**Properties** 要素内で **Item** 要素を使用して指定します。 各 **Item** の **id** は、一意で有効な R 変数である必要があります。 **Item** の **name** は、表示されるテキストであり、R 関数に渡される値でもあります。

```xml
<Arg id="color" name="Color" type="DropDown">
    <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
    </Properties>
    <Description>Select a color.</Description>
</Arg>    
```

* *省略可能なプロパティ*:
  * **default** - 既定のプロパティの値は、**Item** 要素のいずれかの ID 値と一致する必要があります。

### <a name="auxiliary-files"></a>補助ファイル
カスタム モジュールの ZIP ファイル内に配置されたファイルはすべて、実行時に使用できるようなります。 ディレクトリ構造がある場合は保持されます。 つまり、ファイル ソーシングは、ローカルと Azure Machine Learning Studio (クラシック) の実行で同じ動作を行います。 

> [!NOTE]
> すべてのファイルが "src" ディレクトリに展開されることに注意してください。これにより、すべてのパスに必ず "src/" プレフィックスが含まれます。
> 
> 

たとえば、CustomAddRows に出力する前に、データセットから NA を含む行を削除すると共に、重複する行も削除したい場合に、それを実行する R 関数が RemoveDupNARows.R ファイルに既に作成されているとします。

```r
RemoveDupNARows <- function(dataFrame) {
    #Remove Duplicate Rows:
    dataFrame <- unique(dataFrame)
    #Remove Rows with NAs:
    finalDataFrame <- dataFrame[complete.cases(dataFrame),]
    return(finalDataFrame)
}
```

次のように、CustomAddRows 関数で補助ファイル RemoveDupNARows.R をソースとして参照できます。

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
    source("src/RemoveDupNARows.R")
        if (swap) { 
            dataset <- rbind(dataset2, dataset1))
        } else { 
            dataset <- rbind(dataset1, dataset2)) 
        } 
    dataset <- removeDupNARows(dataset)
    return (dataset)
}
```

次に、'CustomAddRows.R'、'CustomAddRows.xml'、および 'RemoveDupNARows.R' を含む zip ファイルをカスタム R モジュールとしてアップロードします。

## <a name="execution-environment"></a>実行環境
R スクリプトの実行環境では、 **Execute R Script** モジュールと同じバージョンの R を使用し、同じ既定のパッケージを使用できます。 他の R パッケージをカスタム モジュールの zip パッケージに追加して、そのパッケージをカスタム モジュールに追加することもできます。 こうしたパッケージを、独自の R 環境の場合と同じように読み込むだけです。 

**実行環境の制限事項** は次のとおりです。

* 非永続的なファイル システム:カスタム モジュールの実行時に書き込まれるファイルは、同じモジュールの複数の実行間で保持されません。
* ネットワーク アクセスはありません。