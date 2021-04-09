---
title: 算術演算の適用
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の Apply Math Operation (算術演算の適用) モジュールを使用して、パイプラインの列値に算術演算を適用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 3b2925a532d722598ccf16c001c9e2591aed1f2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584992"
---
# <a name="apply-math-operation"></a>算術演算の適用

この記事では、Azure Machine Learning デザイナーのモジュールについて説明します。

Apply Math Operation を使用して、入力データセットの数値列に適用される計算を作成します。 

算術演算には、数学関数、三角関数、丸め関数、およびガンマ関数や誤差関数など、データ サイエンスで使用される特殊な関数があります。  

演算を定義してパイプラインを実行すると、データセットに値が追加されます。 モジュールの構成方法に応じて、次の操作を実行できます。

+ 結果をデータセットに追加する (演算の結果を確認するときに便利です)。
+ 列値を、計算された新しい値に置き換える。
+ 結果の新しい列を生成し、元のデータを表示しない。 

次のカテゴリから必要な操作を探してください。  

- [Basic](#basic-math-operations)  
  
     **基本** カテゴリの関数は、単一の値または値の列を操作するために使用できます。 たとえば、列内のすべての数値の絶対値を取得したり、列内の各値の平方根を計算したりすることができます。  
  
-   [比較](#comparison-operations)  
  
      **比較** カテゴリの関数は、すべて比較に使用されます。2 つの列内の値の一対比較を行ったり、列内の各値を指定された定数と比較したりすることができます。 たとえば、列を比較して、2 つのデータセットで値が同じかどうかを判断することができます。 または、最大許容値などの定数を使用して、数値列の外れ値を検出することもできます。  
  
-   [操作](#arithmetic-operations)  
  
     **演算** カテゴリには、加算、減算、乗算、除算という基本の数学関数が含まれます。 列または定数のいずれかを操作できます。 たとえば、列 A の値を列 B の値に加算できます。または、以前に計算した平均値などの定数を列 A の各値から減算することもできます。  
  
-   [丸め](#rounding-operations)  
  
     **丸め** カテゴリには、丸め、切り上げ、切り捨て、ゼロ方向への丸めなどの演算をさまざまな精度で実行するためのさまざまな関数が含まれます。 精度は、10 進数と整数の両方に対して指定できます。  
  
-   [Special](#special-math-functions)  
  
     **特殊** カテゴリには、楕円積分やガウスの誤差関数など、特にデータ サイエンスで使用される数学関数が含まれます。  
  
-   [三角](#trigonometric-functions)  
  
     **三角** カテゴリには、すべての標準の三角関数が含まれます。 たとえば、ラジアンを角度に変換したり、ラジアンまたは角度で正接などの関数を計算したりすることができます。
     これらの関数は単項です。つまり、1 つの列の値を入力として受け取り、三角関数を適用し、結果として列の値を返します。 入力列が適切な型であり、指定された演算にとって正しい型の値が含まれていることを確認します。   

## <a name="how-to-configure-apply-math-operation"></a>Apply Math Operation の構成方法  

**Apply Math Operation** モジュールには、数値のみを含む列が少なくとも 1 つ含まれたデータセットが必要です。 数値は、不連続または連続のいずれも可能ですが、文字列ではなく数値データ型である必要があります。

複数の数値列に同じ演算を適用できますが、すべての列が同じデータセットに含まれている必要があります。 

このモジュールの各インスタンスが実行できる演算は、一度に 1 種類のみです。 複雑な算術演算を実行するには、**Apply Math Operation** モジュールの複数のインスタンスを連結する必要がある場合があります。  
  
1.  **Apply Math Operation** モジュールをパイプラインに追加します。

1. 少なくとも 1 つの数値列を含むデータセットを接続します。  

1.  計算の実行に使用するソース列を 1 つ以上選択します。   
  
    - 選択する列は、すべて数値データ型である必要があります。 
    - データの範囲は、選択された算術演算に有効である必要があります。 そうでない場合、エラーまたは NaN (非数) の結果が発生する可能性があります。 たとえば、Ln(-1.0) は無効な演算のため、値 `NaN` の結果になります。
  
1.  **[カテゴリ]** をクリックして、実行する算術演算の **種類** を選択します。
    
1. そのカテゴリの一覧から特定の演算を選択します。
  
1.  各種類の演算で必要な追加のパラメーターを設定します。  
  
1.  **[Output mode]\(出力モード\)** オプションを使用して、算術演算を生成する方法を指定します。 

    - **Append**: 入力として使用されるすべての列が出力データセットに含まれ、さらに、算術演算の結果を含む追加列が 1 つ追加されます。
    - **Inplace**。 入力として使用される列の値が、新しい計算値に置き換えられます。 
    - **ResultOnly**。 算術演算の結果を含む単一の列が返されます。
  
1.  パイプラインを送信します。  
  
## <a name="results"></a>結果

**Append** または **ResultOnly** のオプションを使用して結果を生成する場合、返されるデータセットの列見出しには、演算と、使用された列が示されます。 たとえば、**Equals** 演算子を使用して 2 つの列を比較した場合、結果は次のようになります。  
  
-   **Equals(Col2_Col1)**。Col1 に対して Col2 をテストしたことを示しています。  
-   **Equals(Col2_$10)**。列 2 と定数 10 を比較したことを示しています。  

**Inplace** オプションを使用しても、ソース データは削除も変更もされず、元のデータセットの列が引き続きデザイナーに表示されます。 元のデータを表示するには、[Add Columns](add-columns.md) (列の追加) モジュールを接続し、それを **Apply Math Operation** の出力に結合します。  
    
## <a name="basic-math-operations"></a>基本の算術演算 

**基本** カテゴリの関数は、通常、列から 1 つの値を受け取り、定義済みの演算を実行して、1 つの値を返します。 一部の関数では、2 番目の引数として定数または列セットを指定できます。  
  
 Azure Machine Learning では、**基本** カテゴリの次の関数がサポートされます。  

### <a name="abs"></a>Abs

選択した列の絶対値を返します。  
  
### <a name="atan2"></a>Atan2

4 象限逆正接を返します。  

ポイント座標を含む列を選択します。 X 座標に対応する 2 番目の引数については、定数を指定することもできます。  

MATLAB の ATAN2 関数に対応します。  

### <a name="conj"></a>Conj

選択された列の値の共役を返します。  

### <a name="cuberoot"></a>CubeRoot

選択された列の値の立方根を計算します。  

### <a name="doublefactorial"></a>DoubleFactorial  
 選択された列の値の二重階乗を計算します。 二重階乗は、通常の階乗関数を拡張したものであり、`x!!` として表示されます。  

### <a name="eps"></a>Eps

現在の値と次に高い倍精度数の差のサイズを返します。 MATLAB の EPS 関数に対応します。  
  
### <a name="exp"></a>Exp

選択された列の値にべき乗された e を返します。 この関数は、Excel の EXP 関数と同じです。  

### <a name="exp2"></a>Exp2

y = x * 2<sup>t</sup> の値を求め、2 を底とする引数の指数を返します。ここで、t は指数を含む値の列です。  

**[Column set]\(列セット\)** で、指数値 t を含む列を選択します。

**Exp2** では、2 番目の引数 x を指定できます。これは、定数または別の列の値のいずれかになります。 **[Second argument type]\(2 番目の引数の型\)** で、乗数の x を定数として指定するか、列内の値として指定するかを示します。  

たとえば、乗数と指数の両方に対して値 {0,1,2,3,4,5} を持つ列を選択した場合、関数は {0, 2, 8, 24, 64 160) を返します。  

### <a name="expminus1"></a>ExpMinus1 

選択された列の値の負の指数を返します。  

### <a name="factorial"></a>階乗
選択された列の値の階乗を返します。  

### <a name="hypotenuse"></a>Hypotenuse
1 つの辺の長さが値の列として指定され、第 2 の辺の長さが定数または 2 つの列として指定された三角形の斜辺を計算します。  

### <a name="ln"></a>Ln

選択された列内の値の自然対数を返します。  

### <a name="lnplus1"></a>LnPlus1

選択された列内の値の自然対数 + 1 を返します。  

### <a name="log"></a>ログ

指定された底を使用して、選択された列の値の対数を返します。  

底 (2 番目の引数) を定数として、または別の値の列を選択して指定することができます。  

### <a name="log10"></a>Log10

選択した列の 10 を底とする対数の値を返します。  

### <a name="log2"></a>Log2

選択した列の 2 を底とする対数の値を返します。  

### <a name="nthroot"></a>NthRoot
指定した n を使用して、値の n 乗根を返します。  

**ColumnSet** オプションを使用して、累乗根を計算する列を選択します。  

**[Second argument type]\(2 番目の引数の型\)** で、累乗根を含む別の列を選択するか、累乗根として使用する定数を指定します。  

2 番目の引数が列の場合は、列内の各値が、対応する行の n の値として使用されます。 2 番目の引数が定数の場合は、**[Second argument]\(2 番目の引数\)** テキスト ボックスに n の値を入力します。
### <a name="pow"></a>Pow

選択された列内の各値について、X の Y 乗を計算します。  

まず、**[ColumnSet]** オプションを使用して、**底** を含む列 (float である必要がある) を選択します。  

**[Second argument type]\(2 番目の引数の型\)** で、指数を含む列を選択するか、指数として使用する定数を指定します。  

2 番目の引数が列の場合は、列内の各値が、対応する行の指数として使用されます。 2 番目の引数が定数の場合は、**[Second argument]\(2 番目の引数\)** テキスト ボックスに指数の値を入力します。  

### <a name="sqrt"></a>Sqrt

選択された列の値の平方根を返します。  

### <a name="sqrtpi"></a>SqrtPi

選択された列の各値について、値を pi で乗算し、結果の平方根を返します。  

### <a name="square"></a>Square

選択された列の値を 2 乗します。  

## <a name="comparison-operations"></a>比較演算  

2 つの値セットを互いに比較してテストする必要がある場合は常に、Azure Machine Learning デザイナーの比較関数を使用します。 たとえば、パイプラインでは、次の比較演算を実行する必要がある場合があります。  

- 確率スコア モデルの列をしきい値と比較して評価する。
- 2 つの結果セットが同一かどうかを判断する。 異なる行ごとに FALSE フラグを追加します。これは、追加の処理やフィルター処理に使用できます。  

### <a name="equalto"></a>EqualTo

値が同じ場合に True を返します。  

### <a name="greaterthan"></a>GreaterThan

**[Column set]\(列セット\)** の値が、指定された定数より大きい場合、または比較列の対応する値より大きい場合に True を返します。  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

**[Column set]\(列セット\)** の値が、指定された定数以上の場合、または比較列の対応する値以上の場合に True を返します。  

### <a name="lessthan"></a>LessThan

**[Column set]\(列セット\)** の値が、指定された定数より小さい場合、または比較列の対応する値より小さい場合に True を返します。  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

**[Column set]\(列セット\)** の値が、指定された定数以下の場合、または比較列の対応する値以下の場合に True を返します。  

### <a name="notequalto"></a>NotEqualTo

[**列セット**] の値が定数または比較列と等しくない場合は True を返し、等しい場合は False を返します。  

### <a name="pairmax"></a>PairMax

大きい値 ([**列設定**] の値か、定数または比較列の値) を返します。  

### <a name="pairmin"></a>PairMin

**[Column set]\(列セット\)** 内の値と、定数または比較列の値のうち、小さい方の値を返します。  
  
##  <a name="arithmetic-operations"></a>算術演算   

基本の算術演算 (加算、減算、除算、乗算) が含まれます。  ほとんどの演算はバイナリで、2 つの数値が必要であるため、最初に演算を選択してから、1 番目と2 番目の引数で使用する列または数値を選択します。

除算と減算の順序は次のとおりです。 
- Subtract(Arg1_Arg2) = Arg1 - Arg 2
- Divide(Arg1_Arg2) =  Arg1 / Arg 2

いくつかの例を次の表に示します
操作|Num1|Num2|結果列|結果の値|
----|----|----|----|----
|加算|1|5|Add(Num2_Num1)| 6|
|乗算|1|5|Multiple(Num2_Num1)|5|
|減算|5|1|Subtract(Num2_Num1)|4|
|減算|0|1|Subtract(Num2_Num1)|-1|
|除算|5|1|Divide(Num2_Num1)|5|
|事業部|1|0|Divide(Num2_Num1)|Infinity|

### <a name="add"></a>追加

**[Column set]\(列セット\)** を使用してソース列を指定し、**[Second argument]\(2 番目の引数\)** に指定されている数値をそれらの値に加算します。  

2 つの列の値を加算するには、**[Column set]\(列セット\)** を使用して 1 つ以上の列を選択してから、**[Second argument]\(2 番目の引数\)** を使用して 2 番目の列を選択します。  

### <a name="divide"></a>除算

**[Column set]\(列セット\)** の値を定数、または **[Second argument]\(2 番目の引数\)** に定義された列値で除算します。  言い換えると、最初に除数を選択し、次に被除数を選択します。 出力値は商です。

### <a name="multiply"></a>乗算

指定された定数または列の値で [**列セット**] の値を乗算します。  

### <a name="subtract"></a>減算

**[Column set]\(列セット\)** オプションを使って別の列を選択することにより、演算対象の値の列 (*被減数*) を指定します。 次に、**[Second argument]\(2 番目の引数\)** ドロップダウン リストを使用して、減算する数値 (*減数*) を指定します。 定数、または値の列のいずれかを選択できます。

##  <a name="rounding-operations"></a>丸めの演算 

Azure Machine Learning デザイナーでは、さまざまな丸めの演算がサポートされます。 多くの演算では、丸めを行うときに使用する精度を指定する必要があります。 定数として指定される静的な精度を指定することも、値の列から取得した動的な精度の値を適用することもできます。  

- 定数を使用する場合は、**[Precision Type]\(精度の型\)** を **[Constant]\(定数\)** に設定し、**[Constant Precision]\(定数の精度\)** テキスト ボックスに整数で桁数を入力します。 整数以外の値を入力してもモジュールはエラーを生成しませんが、予期しない結果になる可能性があります。  

- データセットの行ごとに異なる精度値を使用するには、**[Precision Type]\(精度の型\)** を **[ColumnSet]** に設定してから、適切な精度値を含む列を選択します。  

### <a name="ceiling"></a>Ceiling

[**列セット**] の最高値を返します。  

### <a name="ceilingpower2"></a>CeilingPower2

[**列セット**] の最高値の 2 乗を返します。  

### <a name="floor"></a>床

指定された有効桁数に対して、[**列セット**] の最低値を返します。  

### <a name="mod"></a>Mod

指定された有効桁数に対して、[**列セット**] の値の端数部分を返します。  

### <a name="quotient"></a>商

指定された有効桁数に対して、[**列セット**] の値の端数部分を返します。  

### <a name="remainder"></a>剰余

[**列セット**] の値の剰余を返します。  

### <a name="rounddigits"></a>RoundDigits

指定した桁数で四捨五入して丸められた [**列セット**] の値を返します。  

### <a name="rounddown"></a>RoundDown

指定した桁数で切り捨てられた [**列セット**] の値を返します。  

### <a name="roundup"></a>RoundUp

指定した桁数で切り上げられた [**列セット**] の値を返します。  

### <a name="toeven"></a>ToEven

最も近い偶数の整数に丸められた [**列セット**] の値を返します。  

### <a name="toodd"></a>ToOdd

最も近い奇数の整数に丸められた [**列セット**] の値を返します。  

### <a name="truncate"></a>Truncate

指定された有効桁数を超えるすべての桁を削除して、[**列セット**] の値を切り捨てます。  
  
## <a name="special-math-functions"></a>特殊な数学関数

このカテゴリには、データ サイエンスでよく使用される特殊な数学関数が含まれます。 特に明記されていない限り、関数は単項で、選択された 1 つ以上の列の各値に対して指定された計算を返します。  

### <a name="beta"></a>Beta

オイラーのベータ関数の値を返します。  

### <a name="ellipticintegrale"></a>EllipticIntegralE
不完全な楕円整数の値を返します。  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

完全な楕円整数 (K) の値を返します。  

### <a name="erf"></a>Erf

誤差関数の値を返します。  

誤差関数 (ガウスの誤差関数とも呼ばれる) は、拡散を示すために確率で使用されるシグモイド形状の特殊関数です。  

### <a name="erfc"></a>Erfc

補完的な誤差関数の値を返します。  

`Erfc` は 1 - erf(x) と定義されます。  

### <a name="erfscaled"></a>ErfScaled

スケーリングされた誤差関数の値を返します。  

誤差関数のスケーリングされたバージョンは、算術アンダーフローを回避するために使用できます。  

### <a name="erfinverse"></a>ErfInverse

逆 `erf` 関数の値を返します。  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

指数積分 Ei の値を返します。  

### <a name="gamma"></a>Gamma

ガンマ関数の値を返します。  

### <a name="gammaln"></a>GammaLn

ガンマ関数の自然対数を返します。  

### <a name="gammaregularizedp"></a>GammaRegularizedP

正規化不完全ガンマ関数の値を返します。  

この関数は、定数または値の列として指定できる第 2 の引数を取ります。  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

正規化逆不完全ガンマ関数の値を返します。  

この関数は、定数または値の列として指定できる第 2 の引数を取ります。  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

正規化不完全ガンマ関数の値を返します。  

この関数は、定数または値の列として指定できる第 2 の引数を取ります。  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

一般化された正規化逆不完全ガンマ関数の値を返します。

この関数は、定数または値の列として指定できる第 2 の引数を取ります。  

### <a name="polygamma"></a>PolyGamma

ポリガンマ関数の値を返します。  

この関数は、定数または値の列として指定できる第 2 の引数を取ります。

##  <a name="trigonometric-functions"></a>三角関数 

このカテゴリには、重要な三角関数と逆三角関数の大部分が含まれます。 すべての三角関数は単項であり、追加の引数は必要ありません。  

### <a name="acos"></a>Acos

列値の逆余弦を計算します。  

### <a name="acosdegree"></a>AcosDegree

列値の逆余弦を計算します (角度単位)。  

### <a name="acosh"></a>Acosh

列値の双曲線逆余弦を計算します。  

### <a name="acot"></a>Acot

列の値の逆余接を計算します。  

### <a name="acotdegrees"></a>AcotDegrees

列値の逆余接を計算します (角度単位)。  

### <a name="acoth"></a>Acoth

列値の双曲線逆余接を計算します。  

### <a name="acsc"></a>Acsc

列の値の逆余割を計算します。  

### <a name="acscdegrees"></a>AcscDegrees

列値の逆余割を計算します (角度単位)。  
### <a name="asec"></a>Asec

列値の逆正割を計算します。  

### <a name="asecdegrees"></a>AsecDegrees

列値の逆正割を計算します (角度単位)。  

### <a name="asech"></a>Asech

列値の双曲線逆正割を計算します。  

### <a name="asin"></a>Asin

列値の逆正弦を計算します。  

### <a name="asindegrees"></a>AsinDegrees

列値の逆正弦を計算します (角度単位)。  

### <a name="asinh"></a>Asinh

列値の双曲線逆正弦を計算します。  

### <a name="atan"></a>Atan

列値の逆正接を計算します。  

### <a name="atandegrees"></a>AtanDegrees

列値の逆正接を計算します (角度単位)。  

### <a name="atanh"></a>Atanh

列値の双曲線逆正接を計算します。  

### <a name="cos"></a>Cos

列値の余弦を計算します。  

### <a name="cosdegrees"></a>CosDegrees

列値の余弦を計算します (角度単位)。  

### <a name="cosh"></a>Cosh

列値の双曲線余弦を計算します。  

### <a name="cot"></a>Cot

列値の双曲線余接を計算します。  

### <a name="cotdegrees"></a>CotDegrees

列値の双曲線余接を計算します (角度単位)。  

### <a name="coth"></a>Coth
列値の双曲線余接を計算します。  

### <a name="csc"></a>Csc

列値の余割を計算します。  

### <a name="cscdegrees"></a>CscDegrees

列値の余割を計算します (角度単位)。  

### <a name="csch"></a>Csch

列値の双曲線余割を計算します。  

### <a name="degreestoradians"></a>DegreesToRadians

度をラジアンに変換します。  

### <a name="sec"></a>Sec

列値の正割を計算します。  

### <a name="asecdegrees"></a>aSecDegrees

列値の正割を計算します (角度単位)。  

### <a name="asech"></a>aSech

列値の双曲線正割を計算します。  

### <a name="sign"></a>署名

列値の正弦を返します。  

### <a name="sin"></a>Sin

列値の正弦を計算します。  

### <a name="sinc"></a>Sinc

列値の正弦余弦の値を計算します。  

### <a name="sindegrees"></a>SinDegrees

列値の正弦を計算します (角度単位)。  

### <a name="sinh"></a>Sinh

列値の双曲線正弦を計算します。  

### <a name="tan"></a>Tan

列値の正接を計算します。  

### <a name="tandegrees"></a>TanDegrees

引数の正接を計算します (角度単位)。  

### <a name="tanh"></a>Tanh

列値の双曲線正接を計算します。  
  
## <a name="technical-notes"></a>テクニカル ノート

2 番目の演算子として複数の列を選択する場合は注意してください。 すべての列に定数を加算するなど、演算が単純な場合、結果は理解しやすくなります。 

データセットに複数の列があり、データセットをそれ自体に加算すると仮定します。 結果では、次のように各列がそれ自体に加算されます。  
  
|Num1|Num2|Num3|Add(Num1_Num1)|Add(Num2_Num2)|Add(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

もっと複雑な計算を実行する必要がある場合は、**Apply Math Operation** の複数のインスタンスを連結することができます。 たとえば、**算術演算の適用** の 1 つのインスタンスを使用して 2 つの列を追加し、**算術演算の適用** の別のインスタンスを使用して合計を定数で除算し、平均値を取得することができます。  
  
または、次のいずれかのモジュールを使用して、SQL、R、または Python のスクリプトを使ってすべての計算を一度に実行します。
 
+ [R スクリプトの実行](execute-r-script.md)
+ [Python スクリプトの実行](execute-python-script.md)
+ [SQL 変換の適用](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
