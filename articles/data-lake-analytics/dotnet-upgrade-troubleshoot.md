---
title: .NET 4.7.2 アップグレードによる Azure Data Lake Analytics の U-SQL ジョブのエラーをトラブルシューティングする方法
description: .NET 4.7.2 へのアップグレードによる U-SQL ジョブのエラーをトラブルシューティングします。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: 2be2f50558fef41659c9a3313871b17961f6ad6d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873235"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics が .NET Framework v4.7.2 にアップグレード中

Azure Data Lake Analytics の既定のランタイムが、.NET Framework v4.5.2 から .NET Framework v4.7.2 にアップグレードされています。 この変更により、U-SQL コードでカスタム アセンブリが使用されており、これらのカスタム アセンブリで .NET ライブラリが使用されている場合に、破壊的変更のリスクがわずかながら生じます。

この .NET Framework 4.5.2 からバージョン 4.7.2 へのアップグレードでは、U-SQL ランタイム (既定のランタイム) にデプロイされる .NET Framework は常に 4.7.2 になります。 .NET Framework バージョンに関して、サイド バイ サイドのオプションはありません。

この .NET 4.7.2 へのアップグレードが完了すると、システムのマネージド コードはバージョン 4.7.2 として実行されます。U-SQL カスタム アセンブリなどのユーザーが指定したライブラリは、アセンブリが生成されたバージョンに適した下位互換性モードで実行されます。

- アセンブリ DLL がバージョン 4.5.2 に対して生成される場合、デプロイされたフレームワークではそれらは 4.5.2 ライブラリとして扱われ、4.5.2 セマンティクスが提供されます (いくつかの例外を含む)。
- .NET Framework 4.7.2 を対象とする場合、バージョン 4.7.2 の機能を使用する U-SQL カスタム アセンブリを使用できるようになりました。

この .NET 4.7.2 へのアップグレードにより、.NET カスタム アセンブリを使用する U-SQL ジョブに破壊的変更が加えられる可能性があります。 次の手順を使用して下位互換性の問題を確認することをお勧めします。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>下位互換性の問題を確認する方法

U-SQL カスタム アセンブリの .NET コードに対して .NET 互換性チェックを実行して、下位互換性に問題がある可能性があるかどうかを確認します。

> [!Note]
> このツールでは、実際の破壊的変更は検出されません。 呼び出された .NET API のうち、(特定の入力に対して) 問題の原因となる可能性のあるものが識別されるだけです。 問題の通知を受け取った場合でも、コードに問題がない可能性があるものの、詳細を確認する必要があります。

1. 次のいずれかの方法で、.NET DLL に対して下位互換性チェックを実行します。
   1. [.NET Portability Analyzer の Visual Studio 拡張機能](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)に関するページに従って、Visual Studio 拡張機能を使用する。
   1. [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport) からスタンドアロン ツールをダウンロードして使用する。 スタンドアロン ツールの実行手順については、[GitHub dotnetapiport の破壊的変更](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)に関するページを参照してください。
   1. 4\.7.2 では、 互換性、`read isRetargeting == True` は、潜在的な問題を特定します。
2. このツールにより、コードが何らかの考えられる下位非互換性 (非互換性の一般的な例を以下に示します) の影響を受ける可能性があることが示される場合は、次の方法で詳細を確認できます。
   1. コードを分析し、影響を受ける API にコードによって値が渡されるかどうかを識別する。
   1. ランタイム チェックを実行する。 ADLA では、ランタイムのデプロイはサイド バイ サイドで実行されません。 代表的なデータ セットに対して Visual Studio のローカル実行をローカルの .NET Framework 4.7.2 と共に使用して、アップグレード前にランタイム チェックを実行できます。
3. 下位非互換性の問題による影響を実際に受けている場合は、問題を修正するために必要な手順 (データやコード ロジックの修正など) を実行します。

ほとんどの場合、下位非互換性による影響を受けることはありません。

## <a name="timeline"></a>タイムライン

新しいランタイムのデプロイを確認するには、[ランタイムのトラブルシューティング](runtime-troubleshoot.md)に関するページ、および前に成功したジョブを参照してください。

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>コードを時間内に確認できない場合

以前のバージョンのランタイム (4.5.2 を対象としてビルドされた) に対してジョブを送信できます。ただし、.NET Framework のサイド バイ サイド機能がないため、4.5.2 互換モードでのみ実行されます。 この動作により、何らかの下位互換性の問題が発生する可能性があります。

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>発生する可能性のある最も一般的な下位互換性の問題

チェッカーによって識別される可能性の高い最も一般的な下位非互換性 (この一覧は、独自の内部 ADLA ジョブに対してチェッカーを実行して生成しました)、影響を受けるライブラリ (注: ライブラリは間接的にのみ呼び出すことができるため、ジョブが影響を受けるかどうかを確認するには、必要な最良のアクションを実行することが重要です)、および修正に使用できるアクションを次に示します。 注:ほとんどの破壊的変更は範囲が限られているため、独自のジョブのほぼすべてにおいて、警告は誤検出となります。

- 結果のタスクを完了するには、IAsyncResult.CompletedSynchronously プロパティが正しくなければならない
  - TaskFactory.FromAsync を呼び出した場合、結果のタスクが完了するには、IAsyncResult.CompletedSynchronously プロパティの実装が正しくなければなりません。 つまり、実装が同期的に完了した場合に限り、このプロパティによって true が返される必要があります。 以前は、このプロパティはチェックされていませんでした。
  - 影響を受けるライブラリ: mscorlib、System.Threading.Tasks
  - 推奨アクション:TaskFactory.FromAsync によって true が正しく返されることを確認する

- DataObject.GetData によってデータが UTF-8 として取得されるようになった
  - .NET Framework 4 を対象とするアプリ、または .NET Framework 4.5.1 以前のバージョンで実行されるアプリでは、DataObject.GetData によって HTML 形式のデータが ASCII 文字列として取得されます。 その結果、非 ASCII 文字 (ASCII コードが 0x7F よりも大きい文字) は、2 つのランダムな文字で表されます。.NET Framework 4.5 以降を対象とし、.NET Framework 4.5.2 で実行されるアプリでは、`DataObject.GetData` によって HTML 形式のデータが UTF-8 として取得されます。この場合、0x7F よりも大きい文字が正しく表されます。
  - 影響を受けるライブラリ:Glo
  - 推奨アクション:取得されたデータが必要な形式であることを確認する

- 無効なサロゲート ペアで XmlWriter がスローされる
  - .NET Framework 4.5.2 以前のバージョンを対象とするアプリでは、例外フォールバック処理を使用して無効なサロゲート ペアを記述しても、常に例外がスローされるとは限りません。 .NET Framework 4.6 を対象とするアプリでは、無効なサロゲート ペアを作成しようとすると、`ArgumentException` がスローされます。
  - 影響を受けるライブラリ:System.Xml、System.Xml.ReaderWriter
  - 推奨アクション:引数の例外の原因となる無効なサロゲート ペアを記述していないことを確認する

- HtmlTextWriter によって `<br/>` 要素が正しく表示されない
  - .NET Framework 4.6 以降では、`<BR />` 要素を指定して `HtmlTextWriter.RenderBeginTag()` と `HtmlTextWriter.RenderEndTag()` を呼び出すと、`<BR />` が 1 つのみ (2 つではなく) 正しく挿入されます。
  - 影響を受けるライブラリ:System.Web
  - 推奨アクション:予想される数の `<BR />` が挿入されていることを確認し、運用ジョブでランダムな動作が生じないようにする

- null 引数による CreateDefaultAuthorizationContext の呼び出しが変更された
  - null の authorizationPolicies 引数を指定して `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` への呼び出しによって返される AuthorizationContext の実装が、.NET Framework 4.6 で変更されました。
  - 影響を受けるライブラリ:System.IdentityModel
  - 推奨アクション:null の認証ポリシーが存在する場合に予期される新しい動作を処理していることを確認する
  
- RSACng で非標準のキー サイズの RSA キーが正しく読み込まれるようになった
  - 4\.6.2 より前のバージョンの .NET Framework では、RSA 証明書の非標準のキー サイズを使用しているお客様は、`GetRSAPublicKey()` および `GetRSAPrivateKey()` 拡張メソッドを使用してこれらのキーにアクセスすることはできません。 "要求されたキー サイズはサポートされていません" というメッセージと共に `CryptographicException` がスローされます。 .NET Framework 4.6.2 では、この問題は修正されています。 同様に、`RSA.ImportParameters()` と `RSACng.ImportParameters()` は非標準のキー サイズで動作するようになり、`CryptographicException` はスローされなくなりました。
  - 影響を受けるライブラリ: mscorlib、System.Core
  - 推奨アクション:RSA キーが想定どおりに動作していることを確認する

- パスのコロン チェックがより厳密になった
  - .NET Framework 4.6.2 では、以前にサポートされていなかったパス (長さと形式の両方) をサポートするためにいくつかの変更が加えられました。 ドライブの適切な区切り記号 (コロン) の構文に対するチェックが、より正確になりました。これにより、許容されていた少数の select Path API で一部の URI パスがブロックされるという副作用が生じました。
  - 影響を受けるライブラリ: mscorlib、System.Runtime.Extensions
  - 推奨アクション:

- ClaimsIdentity コンストラクターの呼び出し
  - .NET Framework 4.6.2 以降では、`T:System.Security.Principal.IIdentity` パラメーターを指定した場合に `T:System.Security.Claims.ClaimsIdentity` コンストラクターによって `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティがどのように設定されるかが変更されています。 `T:System.Security.Principal.IIdentity` 引数が `T:System.Security.Claims.ClaimsIdentity` オブジェクトで、その `T:System.Security.Claims.ClaimsIdentity` オブジェクトの `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティが `null` でない場合、`M:System.Security.Claims.ClaimsIdentity.Clone` メソッドを使用して `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティがアタッチされます。 Framework 4.6.1 以前のバージョンでは、`P:System.Security.Claims.ClaimsIdentify.Actor` プロパティは既存の参照としてアタッチされます。 この変更により、.NET Framework 4.6.2 以降では、新しい `T:System.Security.Claims.ClaimsIdentity` オブジェクトの `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティはコンストラクターの `T:System.Security.Principal.IIdentity` 引数の `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティと同じではなくなりました。 .NET Framework 4.6.1 以前のバージョンでは、これは同じになります。
  - 影響を受けるライブラリ: mscorlib
  - 推奨アクション:新しいランタイムで ClaimsIdentity が想定どおりに動作していることを確認する

- DataContractJsonSerializer による制御文字のシリアル化が、ECMAScript V6 および V8 と互換性を持つようになった
  - .NET framework 4.6.2 以前のバージョンでは、DataContractJsonSerializer によって、\b、\f、\t などの一部の特殊制御文字が、ECMAScript V6 および V8 標準と互換性のある方法でシリアル化されませんでした。 .NET Framework 4.7 以降では、これらの制御文字のシリアル化は ECMAScript V6 および V8 と互換性があります。
  - 影響を受けるライブラリ:System.Runtime.Serialization.Json
  - 推奨アクション:DataContractJsonSerializer と同じ動作を保証する
