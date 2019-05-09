---
title: AppSource パッケージの準備 | Microsoft Docs
description: AppSource パッケージを準備しビルドする方法の説明。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc06ddf2fce93bef27e7aaee85b47179608e192b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837033"
---
# <a name="appsource-package-preparation"></a>AppSource パッケージの準備

solution.zip ファイルに加え、**AppSource パッケージ**が必要になります。 これは、顧客の CRM 環境にソリューションをデプロイするプロセスを自動化するために必要なすべてのアセットを含んだ .zip ファイルです。 **AppSource パッケージ**には次のコンポーネントがあります

* Package Deployer 用のパッケージ
* 使用するアセットを含む **Content_Types.xml** ファイル
* アプリ固有のデータを含む xml ファイル
* 管理センターで一覧に表示される、32 x 32 のロゴ
* ライセンス条項、プライバシー ポリシー

次の手順が、AppSource パッケージの作成に役立ちます。

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Package Deployer 用のパッケージを作成する

Package Deployer 用のパッケージは AppSource パッケージの一部になります。

Package Deployer 用パッケージを作成するには、次の手順を使用します: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)。 完了すると、パッケージは次のアセットから構成されます。

1. パッケージ フォルダー: すべてのソリューション、構成データ、フラット ファイル、およびパッケージの内容が含まれています。 _注:以下の例では、パッケージ フォルダーを "PkgFolder" と想定しています。_
2. dll:アセンブリには、ご自分のパッケージ用のカスタム コードが含まれています。 _注:以下の例では、このファイルを "MicrosoftSample.dll" と想定しています。_

ここで、"**Content_Types.xml**" というファイルを作成する必要があります。このファイルには、パッケージの一部であるアセット拡張子のすべてが一覧表示されます。 ファイルのコード例を次に示します。

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

最後の手順で、以下の内容を 1 つのファイルに zip 圧縮します。 このファイルを **package.zip** と呼びます。 次の内容を含みます

1. PkgFolder (フォルダー内のすべてを含む)
2. dll
3. **Content_Types.xml**

package.zip を作成する手順:

1. パッケージ フォルダー、**Content_Types.xml** ファイル、および PackageName.dll を 1 つのディレクトリに配置します。

![CRMScreenShot2](media/CRMScreenShot2.png)

1. フォルダー内のすべての項目を選択し、右クリックして、[送信先]、[Compressed (zip) folder]\(圧縮 (zip) フォルダー) の順に選択します

![CRMScreenShot3](media/CRMScreenShot3.png)

1. package.zip に名前を変更します

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. AppSource パッケージを作成する

AppSource パッケージには、いくつかの追加ファイルが必要です。

1. jpg (32 x 32 の解像度)
2. html (HTML 形式のファイル)
3. **Content_Types.xml** (上記と同じ)
4. xml

input.xml のコード例を次に示します。 下の表の定義を参照してください。

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**各値の説明:**

|フィールド|詳細|
|---|---|
|ProviderName|ソリューションの取得元。 Microsoft チームの場合、これは Microsoft になります。|
|PackageFile |content\_types.xml ファイルと一緒に zip 圧縮された Package Deployer アセット。 この zip ファイルには、Package Deployer アセンブリと、Package Deployer アセットを持つフォルダーが含まれます。 つまり、package.zip です|
|SolutionAnchorName |ソリューション アセットの表示名と説明に使用される Package Deployer におけるソリューション zip ファイルの名前。|
| StartDate| これは、ソリューション パッケージが利用可能になる日付です。 形式は、MM/DD/YYYY です|
|EndDate|これは、ソリューション パッケージが利用できなくなる日付です。 形式は、MM/DD/YYYY です |
|SupportedCountries |これは、このパッケージが表示される国のコンマ区切りのリストです。 現在のすべての国番号の一覧については、オンライン サービスにお問い合わせください。 この記事の作成時点では、一覧は次のとおりでした。AE、AL、AM、AO、AR、AT、AU、AZ、BA、BB、BD、BE、BG、BH、BM、BN、BO、BR、BY、CA、CH、CI、CL、CM、CO、CR、CV、CW、CY、CZ、DE、DK、DO、DZ、EC、EE、EG、ES、FI、FR、GB、GE、GH、GR、GT、HK、HN、HR、HU、ID、IE、IL、IN、IQ、IS、IT、JM、JO、JP、KE、KG、KN、KR、KW、KY、KZ、LB、LK、LT、LU、LV、LY、MA、MC、MD、ME、MK、MN、MO、MT、MU、MX、MY、NG、NI、NL、NO、NZ、OM、PA、PE、PH、PK、PL、PR、PS、PT、PY、QA、RO、RS、RU、RW、SA、SE、SG、SI、SK、SN、SV、TH、TM、TN、TR、TT、TW、UA、US、UY、UZ、VE、VI、VN、ZA、ZW |
|LearnMoreLink | このパッケージの詳細情報ページの URL。 |
|Locales|優先ソリューション UX でサポートする UX 言語ごとの、このノードの 1 つのインスタンス。 このノードには、各言語のロケール、ロゴ、および用語について説明する子ノードが含まれます|
|Locales:PackageLocale.Code|このノードの言語の LCID。 例:英語 (米国) は 1033|
|Locales:PackageLocale.IsDefault|これが既定の言語であることを示します。 顧客が選択した UX 言語が利用できない場合、これは、フォールバック言語として使用されます。|
|Locales:ロゴ|これは、このパッケージに使用するログです。 アイコンのサイズは 32 x 32 です。 許可されている形式は PNG と JPG です|
|Locales:Terms:PackageTerm.File|これは、ライセンス条項を含む HTML ドキュメントのファイル名です。|

ログが表示される位置は次のとおりです。

![CRMScreenShot5](media/CRMScreenShot5.png)

最後の手順で、以下の内容を 1 つのファイルに zip 圧縮します。

1. zip (先ほど作成)
2. **Content_Types.xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

ファイルの名前をアプリに適したものに変更します。 会社名とアプリ名を含めることをお勧めします。 例: **_Microsoft_SamplePackage.zip**。
