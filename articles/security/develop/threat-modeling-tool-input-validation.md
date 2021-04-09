---
title: 入力の検証 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool での入力の検証について説明します。 軽減策に関する情報とコード例についても説明されています。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: c816fbad05831c83c891c70849986b38cb7fdbeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589544"
---
# <a name="security-frame-input-validation--mitigations"></a>セキュリティ フレーム:入力の検証 | 軽減策 
| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **Web アプリケーション** | <ul><li>[信頼できないスタイル シートを使用したすべての変換の XSLT スクリプトを無効にする](#disable-xslt)</li><li>[ユーザーが制御可能なコンテンツが含まれている可能性のある各ページで自動 MIME スニッフィングを必ずオプトアウトする](#out-sniffing)</li><li>[XML エンティティの解決を強化するか無効にする](#xml-resolution)</li><li>[http.sys を使用するアプリケーションで URL 正規化の検証を実行する](#app-verification)</li><li>[ユーザーからのファイルを受け入れるときは必ず適切な制御を行う](#controls-users)</li><li>[Web アプリケーションでデータ アクセスにタイプ セーフなパラメーターが使用されていることを確認する](#typesafe)</li><li>[個別のモデル バインド クラスまたはバインド フィルター リストを使用して MVC の一括割り当ての脆弱性を防ぐ](#binding-mvc)</li><li>[レンダリングの前に信頼できない Web 出力をエンコードする](#rendering)</li><li>[モデルのすべての文字列型プロパティで入力の検証とフィルター処理を実行する](#typemodel)</li><li>[すべての文字を受け入れるフォーム フィールド (リッチ テキスト エディターなど) にはサニタイズを適用する必要がある](#richtext)</li><li>[エンコードが組み込まれていないシンクに DOM 要素を割り当てない](#inbuilt-encode)</li><li>[アプリケーション内のすべてのリダイレクトが閉じられているか、安全に実行されていることを確認する](#redirect-safe)</li><li>[コントローラーのメソッドが受け入れるすべての文字列型パラメーターで入力の検証を実装する](#string-method)</li><li>[正しくない正規表現に起因する DoS を防ぐために正規表現の処理の上限タイムアウトを設定する](#dos-expression)</li><li>[Razor ビューで Html.Raw を使用しない](#html-razor)</li></ul> | 
| **[データベース]** | <ul><li>[ストアド プロシージャで動的クエリを使用しない](#stored-proc)</li></ul> |
| **Web API** | <ul><li>[Web API のメソッドでモデルの検証を必ず実行する](#validation-api)</li><li>[Web API のメソッドが受け入れるすべての文字列型パラメーターで入力の検証を実装する](#string-api)</li><li>[Web API でデータ アクセスにタイプ セーフなパラメーターが使用されていることを確認する](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Azure Cosmos DB のパラメーター化 SQL クエリを使用する](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF - スキーマ バインドを使用した入力の検証](#schema-binding)</li><li>[WCF - パラメーター インスペクターを使用した入力の検証](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>信頼できないスタイル シートを使用したすべての変換の XSLT スクリプトを無効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [XSLT のセキュリティ](/previous-versions/windows/desktop/ms763800(v=vs.85))[XsltSettings.EnableScript プロパティ](/dotnet/api/system.xml.xsl.xsltsettings.enablescript) |
| **手順** | XSLT では、`<msxml:script>` 要素を使用したスタイル シート内でのスクリプト作成をサポートしています。 これにより、XSLT 変換でカスタム関数を使用できます。 スクリプトは、変換を実行するプロセスのコンテキストで実行されます。 信頼できないコードの実行を防ぐために、信頼できない環境では XSLT スクリプトを無効にする必要があります。 " *.NET を使用している場合:* " XSLT スクリプトは既定で無効になっています。ただし、`XsltSettings.EnableScript` プロパティによって明示的に有効にされていないことを確認する必要があります。|

### <a name="example"></a>例 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>例
MSXML 6.0 を使用している場合、XSLT スクリプトは既定で無効になっています。ただし、XML DOM オブジェクトの AllowXsltScript プロパティによって明示的に有効化されていないことを確認する必要があります。 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>例
MSXML 5 以前を使用している場合、XSLT スクリプトは既定で有効になっているので、明示的に無効にする必要があります。 XML DOM オブジェクトの AllowXsltScript プロパティを false に設定します。 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>ユーザーが制御可能なコンテンツが含まれている可能性のある各ページで自動 MIME スニッフィングを必ずオプトアウトする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [IE8 のセキュリティ パート V: 包括的な保護](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **手順** | <p>ユーザーが制御可能なコンテンツが含まれている可能性のある各ページで、`X-Content-Type-Options:nosniff` HTTP ヘッダーを使用する必要があります。 この要件に従うときは、ユーザーが制御可能なコンテンツが含まれている可能性のあるページだけを対象に、この必須のヘッダーをページごとに設定することも、アプリケーションのすべてのページにグローバルに設定することもできます。</p><p>Web サーバーから提供されるファイルの各種類には、コンテンツの性質 (画像、テキスト、アプリケーションなど) を示す [MIME の種類](https://en.wikipedia.org/wiki/Mime_type) ("*コンテンツ タイプ*" とも呼ばれます) が関連付けられています。</p><p>X-Content-Type-Options ヘッダーは、コンテンツを MIME スニッフィングしないことを開発者が指定できる HTTP ヘッダーです。 このヘッダーは、MIME スニッフィング攻撃を軽減することを目的としています。 このヘッダーのサポートは、Internet Explorer 8 (IE8) で追加されました。</p><p>Internet Explorer 8 (IE8) のユーザーだけが、X-Content-Type-Options のメリットを享受できます。 現在、以前のバージョンの Internet Explorer では、X-Content-Type-Options ヘッダーは考慮されません。</p><p>Internet Explorer 8 (以降) は、MIME スニッフィングのオプトアウト機能を実装する唯一の主要ブラウザーです。 他の主要ブラウザー (Firefox、Safari、Chrome) が同様の機能を実装したら、この推奨事項を更新して、それらのブラウザー用の構文も含める予定です。</p>|

### <a name="example"></a>例
この必須のヘッダーをアプリケーションのすべてのページでグローバルに有効にするには、次のいずれかを実行します。 

* アプリケーションがインターネット インフォメーション サービス (IIS) 7 でホストされている場合は、web.config ファイルにヘッダーを追加します。 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* グローバルな Application\_BeginRequest を使用してヘッダーを追加します。 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* カスタム HTTP モジュールを実装します。 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* この必須のヘッダーを特定のページでのみ有効にするには、ヘッダーを個々の応答に追加します。 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>XML エンティティの解決を強化するか無効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [XML エンティティの展開](https://capec.mitre.org/data/definitions/197.html)、[XML サービス拒否攻撃と防御策](/archive/msdn-magazine/2009/november/xml-denial-of-service-attacks-and-defenses)、[MSXML のセキュリティの概要](/previous-versions/windows/desktop/ms754611(v=vs.85))、[MSXML コードをセキュリティで保護するためのベスト プラクティス](/previous-versions/windows/desktop/ms759188(v=vs.85))、[NSXMLParserDelegate プロトコル リファレンス](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html)、[外部参照の解決](/dotnet/standard/data/xml/resolving-external-resources) |
| **手順**| <p>広く使用されているわけではありませんが、XML パーサーが、ドキュメント内で定義された値または外部ソースの値でマクロ エンティティを展開できる XML の機能があります。 たとえば、ドキュメントで "Microsoft" という値を使用して "companyname" というエンティティが定義されている場合、ドキュメントに "&companyname;" というテキストが出現するたびに、"Microsoft" というテキストに自動的に置き換えられます。 または、Microsoft の現在の株価を取得する外部 Web サービスを参照する "MSFTStock" というエンティティがドキュメントで定義されているとします。</p><p>この場合、ドキュメントに "&MSFTStock;" が出現するたびに、現在の株価に自動的に置き換えられます。 ただし、この機能が悪用され、サービス拒否 (DoS) の状況が発生する可能性があります。 攻撃者は、複数のエンティティを入れ子にして、システムの使用可能なすべてのメモリを消費する、指数関数的展開 XML 爆弾を作成する可能性があります。 </p><p>また、膨大な量のデータをストリーミングする外部参照やスレッドを単にハングさせる外部参照を作成する可能性もあります。 そのため、アプリケーションで内部および外部 XML エンティティの解決を使用しない場合は、すべてのチームがこの機能を完全に無効にする必要があります。この機能がどうしても必要な場合は、アプリケーションがエンティティの解決に使用できるメモリの量と時間を手動で制限します。 アプリケーションでエンティティの解決が不要な場合は無効にします。 </p>|

### <a name="example"></a>例
.NET Framework コードでは、次の方法を使用できます。

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
`XmlReaderSettings` での `ProhibitDtd` の既定値は true ですが、`XmlTextReader` では false であることに注意してください。 XmlReaderSettings を使用している場合、ProhibitDtd を明示的に true に設定する必要はありませんが、安全のためにこれを行うことをお勧めします。 また、XmlDocument クラスでは、エンティティの解決を既定で使用できることにも注意してください。 

### <a name="example"></a>例
XmlDocuments のエンティティの解決を無効にするには、次のコードに示すように、Load メソッドの `XmlDocument.Load(XmlReader)` オーバーロードを使用し、XmlReader の引数に解決を無効にするための適切なプロパティを設定します。 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>例
アプリケーションでエンティティの解決を無効にすることができない場合は、アプリケーションのニーズに応じて、XmlReaderSettings.MaxCharactersFromEntities プロパティを適切な値に設定します。 これにより、発生する可能性のある指数関数的展開 DoS 攻撃の影響を抑制できます。 次のコードは、この方法の例を示しています。 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>例
インライン エンティティを解決する必要があり、外部エンティティは解決する必要がない場合は、XmlReaderSettings.XmlResolver プロパティを null に設定します。 次に例を示します。 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
MSXML6 では、ProhibitDTD は既定で true (DTD 処理の無効化) に設定されています。 Apple OSX/iOS コードでは、NSXMLParser と libXML2 の 2 つの XML パーサーを使用できます。 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>http.sys を使用するアプリケーションで URL 正規化の検証を実行する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>http.sys を使用するアプリケーションは、次のガイドラインに従う必要があります。</p><ul><li>URL の長さを 16,384 文字以下 (ASCII または Unicode) に制限します。 これは、インターネット インフォメーション サービス (IIS) 6 の既定の設定に基づく URL の絶対最大長です。 可能であれば、Web サイトでこれよりも短い長さにすることを目指します。</li><li>.NET Framework の標準のファイル I/O クラス (FileStream など) では、.NET FX の正規化規則を利用するので、これらのクラスを使用します。</li><li>既知のファイル名の許可リストを明示的に作成します。</li><li>UrlScan の拒否を適用しない既知のファイルの種類 (exe、bat、cmd、com、htw、ida、idq、htr、idc、shtm[l]、stm、printer、ini、pol、dat の各ファイル) を明示的に拒否します。</li><li>次の例外をキャッチします。<ul><li>System.ArgumentException (デバイス名)</li><li>System.NotSupportedException (データ ストリーム)</li><li>System.IO.FileNotFoundException (エスケープされた無効なファイル名)</li><li>System.IO.DirectoryNotFoundException (エスケープされた無効なディレクトリ)</li></ul></li><li>Win32 ファイル I/O API は *呼び出さない* でください。 無効な URL では、ユーザーに 400 エラーを適切に返し、実際のエラーをログに記録します。</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>ユーザーからのファイルを受け入れるときは必ず適切な制御を行う

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [無制限のファイル アップロード](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)、[ファイル シグネチャ テーブル](https://www.garykessler.net/library/file_sigs.html) |
| **手順** | <p>アップロードされたファイルは、アプリケーションにとって重大なリスクとなります。</p><p>多くの攻撃の第一段階は、攻撃対象のシステムに対するコードを取得することです。 コードを取得したら、攻撃に必要なのはそのコードを実行する方法を見つけることだけです。 ファイル アップロードを使用すると、攻撃者が第一段階を達成しやすくなります。 ファイル アップロードが制限されていない場合、システムの完全な乗っ取り、ファイル システムやデータベースの過負荷、バックエンド システムへの攻撃の転送、単純な改ざんなど、さまざな結果を招きます。</p><p>これは、アップロードされたファイルでアプリケーションが実行する内容と、特にファイルの保存場所に左右されます。 ファイル アップロードのサーバー側の検証はありません。 ファイル アップロード機能に次のセキュリティ制御を実装する必要があります。</p><ul><li>ファイル拡張子をチェックする (許可されているファイルの種類の有効なセットだけを受け入れます)。</li><li>最大ファイル サイズを制限する。</li><li>ファイルを Webroot にアップロードしないようにし、アップロードの場所をシステム ドライブ以外のドライブ上のディレクトリにする。</li><li>ファイルが上書きされないように、名前付け規則 (アップロードするファイルの名前にランダム性を持たせるなど) に従う。</li><li>ファイルをディスクに書き込む前に、ウイルス対策スキャンを実行する。</li><li>ファイル名とその他のメタデータ (ファイル パスなど) に悪意のある文字が含まれていないかどうかを必ず検証する。</li><li>ユーザーが偽装ファイルをアップロードできないように (拡張子を txt に変更して exe ファイルをアップロードするなど)、ファイル形式のシグネチャをチェックする。</li></ul>| 

### <a name="example"></a>例
ファイル形式のシグネチャの検証に関する最後の項目の詳細については、次のクラスを参照してください。 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Web アプリケーションでデータ アクセスにタイプ セーフなパラメーターが使用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>Parameters コレクションを使用している場合、SQL は実行可能コードとしてではなく、リテラル値として入力を処理します。 Parameters コレクションを使用すると、入力データに型と長さの制約を適用できます。 範囲外の値により、例外がトリガーされます。 タイプ セーフな SQL パラメーターを使用していない場合、攻撃者はフィルター処理されていない入力に埋め込まれたインジェクション攻撃を実行できる可能性があります。</p><p>フィルター処理されていない入力で発生する可能性のある SQL インジェクション攻撃を防ぐには、SQL クエリを作成するときにタイプ セーフなパラメーターを使用します。 タイプ セーフなパラメーターは、ストアド プロシージャと動的 SQL ステートメントで使用できます。 パラメーターは、実行可能コードとしてではなく、リテラル値としてデータベースで処理されます。 また、パラメーターの型と長さもチェックされます。</p>|

### <a name="example"></a>例 
次のコードは、ストアド プロシージャを呼び出すときに、SqlParameterCollection でタイプ セーフなパラメーターを使用する方法を示しています。 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
前のコード例では、入力値が 11 文字を超えることはできません。 データがパラメーターで定義された型または長さに一致しない場合、SqlParameter クラスは例外をスローします。 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>個別のモデル バインド クラスまたはバインド フィルター リストを使用して MVC の一括割り当ての脆弱性を防ぐ

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [メタデータ属性](/dotnet/api/system.componentmodel.dataannotations.metadatatypeattribute)、[公開キーのセキュリティの脆弱性と軽減策](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)、[ASP.NET MVC の一括割り当ての完全ガイド](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx)、[ MVC を使用した EF の概要](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **手順** | <ul><li>**オーバーポスティングの脆弱性を調べる必要があるのはどのような場合ですか? -** オーバーポスティングの脆弱性はユーザー入力からモデル クラスをバインドするあらゆる場所で発生する可能性があります。 MVC のようなフレームワークでは、Plain Old CLR Object (POCO) などのカスタム .NET クラスでユーザー データを表すことができます。 MVC では、要求から取得したデータが自動的にモデル クラスに設定されるので、ユーザー入力を処理する際の便利な表現が提供されます。 これらのクラスにユーザーが設定できないプロパティが含まれていると、アプリケーションはオーバーポスティング攻撃に対して脆弱になる可能性があります。オーバーポスティング攻撃では、アプリケーションが意図していなかったデータをユーザーが制御できるようになります。 MVC のモデル バインドと同様に、Entity Framework のようなオブジェクト/リレーショナル マッパーなどのデータ アクセス テクノロジも、多くの場合、POCO オブジェクトを使用したデータベース データの表現をサポートしています。 これらのデータ モデル クラスでは、データベース データを処理するときに、MVC がユーザー入力を処理するときと同様の利便性を提供します。 MVC とデータベースはどちらも POCO オブジェクトなどの類似するモデルをサポートしているため、両方の目的に同じクラスを再利用しやすいように思われます。 この方法では関心の分離を維持できません。これは、意図しないプロパティがモデル バインドに公開され、オーバーポスティング攻撃が可能になる一般的な領域の 1 つです。</li><li>**フィルター処理されていないデータベース モデル クラスを MVC アクションのパラメーターとして使用しないようにする必要があるのはなぜですか? -** MVC のモデル バインドはそのクラスのあらゆるものをバインドするからです。 データがビューに表示されなくても、悪意のあるユーザーはそのデータが含まれた HTTP 要求を送信できます。アクションによって、データベース クラスがユーザー入力を受け入れる必要があるデータ構造であることが伝えられるため、MVC はデータをバインドします。</li><li>**モデル バインドに使用する構造に注意する必要があるのはなぜですか? -** ASP.NET MVC のモデル バインドを非常に多くのモデルで使用すると、アプリケーションがオーバーポスティング攻撃にさらされます。 オーバーポスティングにより、攻撃者は開発者が意図していた範囲を超えてアプリケーション データを変更できるようになる可能性があります (商品の価格やアカウントのセキュリティ特権のオーバーライドなど)。 アプリケーションでは、アクション固有のバインド モデル (または明確な許容プロパティ フィルター リスト) を使用して、モデル バインドによって許可する信頼できない入力に関する明示的なコントラクトを提供する必要があります。</li><li>**個々のバインド モデルは複製コードを使用するのですか? -** いいえ。関心の分離の問題です。 アクション メソッドでデータベース モデルを再利用した場合、HTTP 要求でそのクラスのあらゆるプロパティ (またはサブプロパティ) をユーザーが設定できることになります。 これが望ましくない場合は、ユーザー入力から取得できるデータを MVC に示すフィルター リストまたは別のクラス構造が必要です。</li><li>**ユーザー入力用の個別のバインド モデルがある場合、すべてのデータ注釈属性を複製する必要がありますか? -** その必要はありません。 データベース モデル クラスの MetadataTypeAttribute を使用して、モデル バインド クラスのメタデータにリンクできます。 MetadataTypeAttribute が参照する型は、参照元の型のサブセットである必要があります (プロパティが減る可能性はありますが、増えることはありません)。</li><li>**ユーザー入力モデルとデータベース モデル間でデータを移動するのが面倒です。リフレクションを使用してすべてのプロパティをコピーすることはできますか? -** はい。 バインド モデルで表示されるのは、ユーザー入力でも安全であると開発者が判断したプロパティだけです。 リフレクションを使用して、この 2 つのモデル間で共通して存在するすべてのプロパティをコピーすることを妨げるセキュリティ上の理由はありません。</li><li>**[Bind(Exclude ="â&euro;¦")] はどうでしょうか。個別のバインド モデルを用意する代わりに、これを使用することはできますか? -** この方法はお勧めしません。 [Bind(Exclude ="â&euro;¦")] を使用すると、すべての新しいプロパティが既定でバインド可能になります。 新しいプロパティが追加されたときに、設計が既定でセキュリティ保護されるのではなく、セキュリティを維持するために忘れずに実行する必要がある余分な手順が発生します。 開発者によっては、プロパティが追加されるたびにこのリストを確認するのはリスクが伴います。</li><li>**Is [Bind(Include ="â&euro;¦")] は編集操作に役立ちますか? -** いいえ。 [Bind(Include ="â&euro;¦")] は挿入スタイルの操作 (新しいデータの追加) にのみ適しています。 更新スタイルの操作 (既存のデータの変更) の場合、別の方法を使用します。たとえば、個別のバインド モデルを使用するか、許容されるプロパティの明示的なリストを UpdateModel または TryUpdateModel に渡します。 編集操作に [Bind(Include ="â&euro;¦")] 属性を追加すると、MVC はオブジェクト インスタンスを作成し、リストされたプロパティのみを設定して、他のすべてのプロパティはデフォルト値のままにします。 データが永続化されている場合は、既存のエンティティが完全に置き換えられ、省略されたプロパティの値が既定値にリセットされます。 たとえば、編集操作で [Bind(Include ="â&euro;¦")] 属性から IsAdmin が省略された場合、このアクションによって名前が編集されたすべてのユーザーが IsAdmin = false にリセットされます (編集されたすべてのユーザーが管理者のステータスを失います)。 特定のプロパティが更新されないようにする場合は、前述の方法のいずれかを使用します。 MVC ツールの一部のバージョンは、編集アクションで [Bind(Include ="â&euro;¦")] を指定してコントローラー クラスを生成し、そのリストからプロパティを削除することでオーバーポスティング攻撃を防止することに注意してください。 ただし、前述のように、この方法は意図したとおりに機能するわけではなく、省略されたプロパティのデータが既定値にリセットされます。</li><li>**作成操作において、別々のバインド モデルではなく、[Bind(Include ="â&euro;¦")] を使用することに関して注意事項はありますか? -** はい。 第 1 に、この方法は、オーバーポスティングのすべての脆弱性を軽減するために 2 つの方法を維持する必要がある編集シナリオには適していません。 第 2 に、別々のバインド モデルでは、ユーザー入力に使用される構造と永続化に使用される構造の間で関心の分離が適用されます。これは [Bind(Include ="â&euro;¦")] では行われません。 第 3 に、[Bind(Include ="â&euro;¦")] が処理できるのは最上位レベルのプロパティのみであることに注意してください。属性のサブプロパティの部分 ("Details.Name" など) のみを許可することはできません。 最後に、おそらく最も重要な点として、[Bind(Include ="â&euro;¦")] を使用すると、モデル バインドにそのクラスが使用されるたびに、覚えておく必要のある手順が追加されます。 新しいアクション メソッドが直接データ クラスにバインドされ、[Bind(Include ="â&euro;¦")] 属性が含まれていない場合は、オーバーポスティング攻撃に対して脆弱になる可能性があるため、[Bind(Include ="â&euro;¦")] の方法は既定で安全性が若干低くなります。 [Bind(Include ="â&euro;¦")] を使用する場合、データ クラスがアクション メソッドのパラメーターとして使用されるときは必ず毎回指定するようにしてください。</li><li>**作成操作において、モデル クラス自体に [Bind(Include ="â&euro;¦")] 属性を配置するとどうなりますか? この方法なら、各アクション メソッドにこの属性を忘れずに配置する必要はなくなるのではありませんか? -** この方法は場合によっては有効です。 (このクラスを使用するアクション パラメーターではなく) そのモデルの型自体に [Bind(Include ="â&euro;¦")] を使用すると、すべてのアクション メソッドに [Bind(Include ="â&euro;¦")] 属性を忘れずに含める必要がなくなります。 モデル バインドのために、この属性をクラスで直接使用すると、そのクラスのセキュリティが実質的に強化されます。 ただし、この方法では、モデル クラスごとに使用できるモデル バインド構造は 1 つだけになります。 あるアクション メソッドでフィールドのモデル バインドを許可する必要があり (ユーザー ロールを更新する管理者専用のアクションなど)、その他のアクションではそのフィールドのモデル バインドを防ぐ必要がある場合、この方法は機能しません。 各クラスに指定できるモデル バインド構造は 1 つだけです。別のアクションで別のモデル バインド構造が必要な場合は、アクション メソッドで別個のモデル バインド クラスまたは別個の [Bind(Include ="â&euro;¦")] 属性を使用して、それらの別個の構造を表現する必要があります。</li><li>**バインド モデルとはなんですか?ビュー モデルと同じものですか? -** これらは 2 つの関連する概念です。 バインド モデルという用語は、アクションのパラメーター リストで使用されるモデル クラスを指します (MVC のモデル バインドからアクション メソッドに渡される構造)。 ビュー モデルという用語は、アクション メソッドからビューに渡されるモデル クラスを指します。 アクション メソッドからビューにデータを渡す一般的な方法は、ビュー固有のモデルを使用することです。 多くの場合は、この構造はモデル バインドにも適しており、ビュー モデルという用語を使用して、両方の場所で使用される同じモデルを指すことができます。 正確に言うと、この手順では、一括割り当てのために重要となる、アクションに渡される構造に重点を置いて、バインド モデルについて具体的に伝えます。</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>レンダリングの前に信頼できない Web 出力をエンコードする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、Web フォーム、MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET でクロスサイト スクリプトを防止する方法](/previous-versions/msp-n-p/ff649310(v=pandp.10))、[クロスサイト スクリプト](https://cwe.mitre.org/data/definitions/79.html)、[XSS (クロスサイト スクリプト) 防止チート シート](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **手順** | クロスサイト スクリプト (一般的な略称は XSS) は、Web からの入力を使用するオンライン サービスやアプリケーション/コンポーネントに対する攻撃ベクトルです。 XSS 脆弱性により、攻撃者が脆弱な Web アプリケーションを使用して別のユーザーのコンピューターでスクリプトを実行できるようになる場合があります。 悪意のあるスクリプトを使用して Cookie を窃取したり、JavaScript を使用して攻撃対象のコンピューターを改ざんしたりする可能性があります。 XSS を防ぐには、ユーザー入力を検証し、形式が適切であることを確認して、Web ページにレンダリングされる前にエンコードします。 入力の検証と出力エンコードは、Web Protection Library を使用して実行できます。 マネージド コード(C\#、VB.NET など) の場合、ユーザー入力が使用されるコンテキストに応じて、Web Protection (XSS 対策) Library の適切なエンコード メソッドを 1 つ以上使用します。| 

### <a name="example"></a>例

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>モデルのすべての文字列型プロパティで入力の検証とフィルター処理を実行する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [検証の追加](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation)、[MVC アプリケーションでのモデル データの検証](/previous-versions/dd410404(v=vs.90))、[ASP.NET MVC アプリケーションの基本原則](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **手順** | <p>悪意のあるユーザー入力からアプリケーションを確実に保護するには、アプリケーションで入力パラメーターが使用される前に、すべての入力パラメーターを検証する必要があります。 サーバー側で許可リスト方式の正規表現の検証を使用して入力値を検証します。 サニタイズされていないユーザー入力やパラメーターがメソッドに渡されると、コード インジェクションの脆弱性が発生する可能性があります。</p><p>Web アプリケーションの場合、エントリ ポイントにフォーム フィールド、クエリ文字列、Cookie、HTTP ヘッダー、Web サービス パラメーターが含まれている可能性もあります。</p><p>モデル バインド時に、次の入力検証チェックを実行する必要があります。</p><ul><li>許容される文字と最大許容長を受け入れるために、モデルのプロパティに注釈として RegularExpression を設定する必要があります。</li><li>コントローラーのメソッドで ModelState の有効性チェックを実行します。</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>すべての文字を受け入れるフォーム フィールド (リッチ テキスト エディターなど) にはサニタイズを適用する必要がある

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [安全でない入力のエンコード](/previous-versions/msp-n-p/ff647397(v=pandp.10)#paght000003_step3)、[HTML サニタイザー](https://github.com/mganss/HtmlSanitizer) |
| **手順** | <p>使用するすべての静的マークアップ タグを特定します。 一般的な方法は、書式設定を安全な HTML 要素 (`<b>` (太字) や `<i>` (斜体) など) に制限することです。</p><p>データを書き込む前に HTML エンコードします。 これにより、スクリプトは実行可能コードとしてではなく、テキストとして処理されるので、悪意のあるスクリプトが安全になります。</p><ol><li>ValidateRequest="false" 属性を \@ Page ディレクティブに追加して、ASP.NET 要求の検証を無効にします。</li><li>HtmlEncode メソッドを使用して文字列入力をエンコードします。</li><li>StringBuilder を使用し、Replace メソッドを呼び出して、許可する HTML 要素のエンコードを選択的に削除します。</li></ol><p>参照のページインにより、`ValidateRequest="false"` が設定され、ASP.NET 要求の検証が無効になります。 入力が HTML エンコードされ、`<b>` と `<i>` が選択的に許可されます。別の方法として、HTML サニタイズ用 .NET ライブラリを使用することもできます。</p><p>HtmlSanitizer は、XSS 攻撃につながる可能性のある構造から HTML フラグメントや HTML ドキュメントを取り除くための .NET ライブラリです。 HtmlSanitizer では、AngleSharp を使用して HTML と CSS の解析、操作、レンダリングを実行します。 HtmlSanitizer は NuGet パッケージとしてインストールできます。ユーザー入力は、サーバー側で適切な HTML または CSS サニタイズ メソッドを使用して渡すことができます (該当する場合)。 セキュリティ制御としてのサニタイズは、あくまで最後の手段と考える必要があることに注意してください。</p><p>入力の検証と出力エンコードの方が優れたセキュリティ制御と見なされます。</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>エンコードが組み込まれていないシンクに DOM 要素を割り当てない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 多くの JavaScript 関数では、既定でエンコードは実行されません。 このような関数を使用して信頼できない入力を DOM 要素に割り当てると、クロスサイト スクリプト (XSS) が実行される可能性があります。| 

### <a name="example"></a>例
安全ではない例を次に示します。 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
`innerHtml` は使用しないでください。代わりに `innerText` を使用します。 同様に、`$("#elm").html()` ではなく、`$("#elm").text()` を使用します。 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>アプリケーション内のすべてのリダイレクトが閉じられているか、安全に実行されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [OAuth 2.0 承認フレームワーク - オープン リダイレクター](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **手順** | <p>ユーザーが指定した場所へのリダイレクトを必要とするアプリケーション設計では、使用可能なリダイレクト ターゲットを、サイトまたはドメインの定義済みの "セーフ" リストに制限する必要があります。 アプリケーション内のすべてのリダイレクトが閉じられているか、安全である必要があります。</p><p>これを行うには、次の手順を実行します。</p><ul><li>すべてのリダイレクトを特定します。</li><li>リダイレクトごとに適切な軽減策を実装します。 適切な軽減策として、リダイレクトの許可リストやユーザーの確認などがあります。 オープン リダイレクトの脆弱性が存在する Web サイトやサービスで Facebook/OAuth/OpenID ID プロバイダーを使用している場合、攻撃者がユーザーのログオン トークンを窃取し、そのユーザーを偽装する可能性があります。 これは OAuth 使用時の固有のリスクであり、RFC 6749「The OAuth 2.0 Authorization Framework (OAuth 2.0 承認フレームワーク)」のセクション 10.15 「Open Redirectors (オープン リダイレクター)」に記載されています。同様に、オープン リダイレクトを使用したスピア フィッシング攻撃によってユーザーの資格情報が侵害されるおそれがあります。</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>コントローラーのメソッドが受け入れるすべての文字列型パラメーターで入力の検証を実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [MVC アプリケーションでのモデル データの検証](/previous-versions/dd410404(v=vs.90))、[ASP.NET MVC アプリケーションの基本原則](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **手順** | 引数としてプリミティブ データ型だけを受け入れ、モデルは受け入れないメソッドでは、正規表現を使用した入力の検証を実行する必要があります。 この場合、有効な regex パターンで Regex.IsMatch を使用します。 入力が指定した正規表現と一致しない場合は、制御を進めないようにし、検証エラーに関する適切な警告を表示する必要があります。| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>正しくない正規表現に起因する DoS を防ぐために正規表現の処理の上限タイムアウトを設定する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、Web フォーム、MVC5、MVC6  |
| **属性**              | 該当なし  |
| **参照**              | [DefaultRegexMatchTimeout プロパティ](/dotnet/api/system.web.configuration.httpruntimesection.defaultregexmatchtimeout) |
| **手順** | 適切に作成されていない正規表現に対するサービス拒否攻撃により、大量のバックトラッキングが発生します。このようなサービス拒否攻撃を防ぐには、グローバルな既定のタイムアウトを設定します。 処理時間が定義されている上限を超えた場合、タイムアウト例外をスローします。 何も構成されていない場合、タイムアウトが無限になります。| 

### <a name="example"></a>例
たとえば、次の構成では、処理時間が 5 秒を超えると、RegexMatchTimeoutException がスローされます。 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Razor ビューで Html.Raw を使用しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| 手順 | ASP.NET WebPages (Razor) は、自動 HTML エンコードを実行します。 埋め込まれたコード ナゲット (@ ブロック) によって出力されるすべての文字列が自動的に HTML エンコードされます。 ただし、`HtmlHelper.Raw` メソッドを呼び出すと、HTML エンコードされていないマークアップが返されます。 `Html.Raw()` ヘルパー メソッドを使用すると、このメソッドは Razor が提供する自動エンコード保護を迂回します。|

### <a name="example"></a>例
安全ではない例を次に示します。 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
マークアップを表示する必要がある場合を除き、`Html.Raw()` は使用しないでください。 このメソッドでは、出力エンコードは暗黙的に実行されません。 他の ASP.NET ヘルパー (`@Html.DisplayFor()` など) を使用してください。 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>ストアド プロシージャで動的クエリを使用しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>SQL インジェクション攻撃では、入力の検証の脆弱性を悪用して、データベースで任意のコマンドを実行します。 これは、アプリケーションがデータベースにアクセスするために入力を使用して動的 SQL ステートメントを作成する場合に発生する可能性があります。 また、コードで生のユーザー入力を含む文字列が渡されるストアド プロシージャを使用している場合にも発生する可能性があります。 攻撃者は、SQL インジェクション攻撃を使用してデータベースで任意のコマンドを実行する可能性があります。 (ストアド プロシージャ内の SQL ステートメントも含め) すべての SQL ステートメントをパラメーター化する必要があります。 パラメーター化 SQL ステートメントは、SQL に対して特殊な意味を持つ文字 (一重引用符など) を受け入れます。これらの文字は厳密に型指定されているため、問題は発生しません。 |

### <a name="example"></a>例
安全ではない動的ストアド プロシージャの例を次に示します。 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>例
安全に実装された同じストアド プロシージャを次に示します。 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Web API のメソッドでモデルの検証を必ず実行する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET Web API でのモデルの検証](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **手順** | クライアントから Web API にデータが送信されたときには、処理を実行する前にデータを検証することが必須となります。 入力としてモデルを受け入れる ASP.NET Web API では、モデルでデータ注釈を使用して、モデルのプロパティに関する検証規則を設定します。|

### <a name="example"></a>例
次のコードは同じことを示しています。 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>例
API コントローラーのアクション メソッドでは、モデルの有効性を、次のように明示的にチェックする必要があります。 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Web API のメソッドが受け入れるすべての文字列型パラメーターで入力の検証を実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、MVC 5、MVC 6 |
| **属性**              | 該当なし  |
| **参照**              | [MVC アプリケーションでのモデル データの検証](/previous-versions/dd410404(v=vs.90))、[ASP.NET MVC アプリケーションの基本原則](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **手順** | 引数としてプリミティブ データ型だけを受け入れ、モデルは受け入れないメソッドでは、正規表現を使用した入力の検証を実行する必要があります。 この場合、有効な regex パターンで Regex.IsMatch を使用します。 入力が指定した正規表現と一致しない場合は、制御を進めないようにし、検証エラーに関する適切な警告を表示する必要があります。|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Web API でデータ アクセスにタイプ セーフなパラメーターが使用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>Parameters コレクションを使用している場合、SQL は実行可能コードとしてではなく、リテラル値として入力を処理します。 Parameters コレクションを使用すると、入力データに型と長さの制約を適用できます。 範囲外の値により、例外がトリガーされます。 タイプ セーフな SQL パラメーターを使用していない場合、攻撃者はフィルター処理されていない入力に埋め込まれたインジェクション攻撃を実行できる可能性があります。</p><p>フィルター処理されていない入力で発生する可能性のある SQL インジェクション攻撃を防ぐには、SQL クエリを作成するときにタイプ セーフなパラメーターを使用します。 タイプ セーフなパラメーターは、ストアド プロシージャと動的 SQL ステートメントで使用できます。 パラメーターは、実行可能コードとしてではなく、リテラル値としてデータベースで処理されます。 また、パラメーターの型と長さもチェックされます。</p>|

### <a name="example"></a>例
次のコードは、ストアド プロシージャを呼び出すときに、SqlParameterCollection でタイプ セーフなパラメーターを使用する方法を示しています。 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
前のコード例では、入力値が 11 文字を超えることはできません。 データがパラメーターで定義された型または長さに一致しない場合、SqlParameter クラスは例外をスローします。 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Cosmos DB のパラメーター化 SQL クエリを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Document DB | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure Cosmos DB の SQL パラメーター化の発表](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **手順** | Azure Cosmos DB でサポートされているのは読み取り専用クエリだけですが、ユーザー入力と連結してクエリが作成される場合は、SQL インジェクションが可能になります。 ユーザーは、悪意のある SQL クエリを作成することで、同じコレクション内にある、アクセスすべきできないデータにアクセスできるようになる可能性があります。 クエリがユーザー入力に基づいて作成される場合は、パラメーター化 SQL クエリを使用します。 |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>WCF - スキーマ バインドを使用した入力の検証

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff647820(v=pandp.10)) |
| **手順** | <p>検証が行われていない場合、さまざまな種類のインジェクション攻撃が発生します。</p><p>メッセージの検証は、WCF アプリケーションの保護における防御手段の 1 つとなります。 このアプローチでは、悪意のあるクライアントによる攻撃から WCF サービス操作を保護するために、スキーマを使用してメッセージを検証します。 また、悪意のあるサービスによる攻撃からクライアントを保護するために、クライアントが受信したすべてのメッセージを検証します。 メッセージの検証により、操作でメッセージ コントラクトまたはデータ コントラクトが使用されているときに、メッセージを検証することが可能になります。これは、パラメーターの検証では実現できません。 メッセージの検証では、スキーマ内で検証ロジックを作成できるので、柔軟性が向上し、開発時間が短縮されます。 データ表現の標準を作成することで、組織内のさまざまなアプリケーションでスキーマを再利用できます。 さらに、メッセージの検証により、ビジネス ロジックを表すコントラクトを含む複雑なデータ型を使用する操作を保護することもできます。</p><p>メッセージの検証を実行するには、まず、サービスの操作とそれらの操作で使用されるデータ型を表すスキーマを作成します。 その後、サービスとの間で送受信されるメッセージを検証するために、カスタム クライアント メッセージ インスペクターとカスタム ディスパッチャー メッセージ インスペクターを実装した .NET クラスを作成します。 次に、クライアントとサービスの両方でメッセージの検証を有効にするカスタム エンドポイント動作を実装します。 最後に、サービスまたはクライアントの構成ファイル内で拡張されたカスタム エンドポイント動作を公開できるようにするためのカスタム構成要素をクラスに実装します。</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF - パラメーター インスペクターを使用した入力の検証

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff647875(v=pandp.10)) |
| **手順** | <p>入力とデータの検証は、WCF アプリケーションの保護における重要な防御手段の 1 つとなります。 悪意のあるクライアントによる攻撃からサービスを保護するために、WCF サービス操作で公開されているすべてのパラメーターを検証する必要があります。 逆に、悪意のあるサービスによる攻撃からクライアントを保護するために、クライアントから受信したすべての戻り値も検証する必要があります。</p><p>WCF には、カスタムの拡張機能を作成して WCF ランタイムの動作をカスタマイズできる、さまざまな機能拡張ポイントが用意されています。 メッセージ インスペクターとパラメーター インスペクターは、クライアントとサービスの間で受け渡されるデータの制御を強化するために使用する 2 つの拡張メカニズムです。 入力の検証にパラメーター インスペクターを使用し、サービスで送受信されるメッセージ全体を検査する必要がある場合にのみ、メッセージ インスペクターを使用します。</p><p>入力の検証を実行するには、サービスにおける操作でパラメーターを検証するために、.NET クラスを作成し、カスタム パラメーター インスペクターを実装します。 次に、クライアントとサービスの両方で検証を有効にするカスタム エンドポイント動作を実装します。 最後に、サービスまたはクライアントの構成ファイル内で拡張されたカスタム エンドポイント動作を公開できるようにするためのカスタム構成要素をクラスに実装します。</p>|