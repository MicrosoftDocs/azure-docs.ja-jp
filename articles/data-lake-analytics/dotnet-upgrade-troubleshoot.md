---
title: .NET Framework 4.7.2 アップグレードによる Azure Data Lake Analytics の U-SQL ジョブのエラーをトラブルシューティングする方法
description: .NET Framework 4.7.2 へのアップグレードによる U-SQL ジョブのエラーをトラブルシューティングします。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217679"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics が .NET Framework v4.7.2 にアップグレード中

Azure Data Lake Analytics の既定のランタイムが、.NET Framework v4.5.2 から .NET Framework v4.7.2 にアップグレードされています。 この変更により、U-SQL コードでカスタム アセンブリが使用されており、これらのカスタム アセンブリで .NET ライブラリが使用されている場合に、破壊的変更のリスクがわずかながら生じます。

この .NET Framework 4.5.2 からバージョン 4.7.2 へのアップグレードでは、U-SQL ランタイム (既定のランタイム) にデプロイされる .NET Framework は常に 4.7.2 になります。 .NET Framework バージョンに関して、サイド バイ サイドのオプションはありません。

この .NET Framework 4.7.2 へのアップグレードが完了すると、システムのマネージド コードはバージョン 4.7.2 として実行されます。U-SQL カスタム アセンブリなどのユーザーが指定したライブラリは、アセンブリが生成されたバージョンに適した下位互換性モードで実行されます。

- アセンブリ DLL がバージョン 4.5.2 に対して生成される場合、デプロイされたフレームワークではそれらは 4.5.2 ライブラリとして扱われ、4.5.2 セマンティクスが提供されます (いくつかの例外を含む)。
- .NET Framework 4.7.2 を対象とする場合、バージョン 4.7.2 の機能を使用する U-SQL カスタム アセンブリを使用できるようになりました。

この .NET Framework 4.7.2 へのアップグレードにより、.NET カスタム アセンブリを使用する U-SQL ジョブに破壊的変更が加えられる可能性があります。 次の手順を使用して下位互換性の問題を確認することをお勧めします。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>下位互換性の問題を確認する方法

U-SQL カスタム アセンブリの .NET コードに対して .NET 互換性チェックを実行して、下位互換性に問題がある可能性があるかどうかを確認します。

> [!Note]
> このツールでは、実際の破壊的変更は検出されません。 呼び出された .NET API のうち、(特定の入力に対して) 問題の原因となる可能性のあるものが識別されるだけです。 問題の通知を受け取った場合でも、コードに問題がない可能性があるものの、詳細を確認する必要があります。

1. 次のいずれかの方法で、.NET DLL に対して下位互換性チェックを実行します。
   1. [.NET Portability Analyzer の Visual Studio 拡張機能](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)に関するページに従って、Visual Studio 拡張機能を使用する。
   1. [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport) からスタンドアロン ツールをダウンロードして使用する。 スタンドアロン ツールの実行手順については、[GitHub dotnetapiport の破壊的変更](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)に関するページを参照してください。
   1. 4.7.2 では、 互換性、`read isRetargeting == True` は、潜在的な問題を特定します。
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

チェッカーによって識別される可能性の高い最も一般的な下位非互換性 (この一覧は、独自の内部 ADLA ジョブに対してチェッカーを実行して生成しました)、影響を受けるライブラリ (注: ライブラリは間接的にのみ呼び出すことができるため、ジョブが影響を受けるかどうかを確認するには、必要な最良のアクションを実行することが重要です)、および修正に使用できるアクションを次に示します。 注: ほとんどの破壊的変更は範囲が限られているため、独自のジョブのほぼすべてにおいて、警告は誤検出となります。

- IAsyncResult.CompletedSynchronously プロパティが正しくなければ、結果のタスクは完了しません
  - TaskFactory.FromAsync を呼び出した場合、結果のタスクが完了するには、IAsyncResult.CompletedSynchronously プロパティの実装が正しくなければなりません。 つまり、実装が同期的に完了した場合にのみ、このプロパティは true を返す必要があります。 以前は、このプロパティは確認されていませんでした。
  - 影響を受けるライブラリ: mscorlib、System.Threading.Tasks
  - 推奨されるアクション: TaskFactory.FromAsync によって true が正しく返されることを確認する

- DataObject.GetData は、データを UTF-8 として取得するようになりました
  - .NET Framework 4 を対象とするアプリ、または .NET Framework 4.5.1 以前のバージョンで実行されるアプリでは、DataObject.GetData によって HTML 形式のデータが ASCII 文字列として取得されます。 その結果、非 ASCII 文字 (ASCII コードが 0x7F よりも大きい文字) は、2 つのランダムな文字で表されます。.NET Framework 4.5 以降を対象とし、.NET Framework 4.5.2 で実行されるアプリでは、`DataObject.GetData` によって HTML 形式のデータが UTF-8 として取得されます。この場合、0x7F よりも大きい文字が正しく表されます。
  - 影響を受けるライブラリ: Glo
  - 推奨されるアクション: 取得されたデータが必要な形式であることを確認する

- 無効なサロゲート ペアで XmlWriter がスローされる
  - .NET Framework 4.5.2 またはそれ以前のバージョンをターゲットとするアプリケーションの場合、例外フォールバック処理を使用した無効なサロゲート ペアを作成しても、必ず例外がスローされるとは限りません。 .NET Framework 4.6 を対象とするアプリでは、無効なサロゲート ペアを作成しようとすると、`ArgumentException` がスローされます。
  - 影響を受けるライブラリ: System.Xml、System.Xml.ReaderWriter
  - 推奨されるアクション: 引数の例外の原因となる無効なサロゲート ペアを記述していないことを確認する

- HtmlTextWriter によって `<br/>` 要素が正しく表示されない
  - .NET Framework 4.6 以降では、`<BR />` 要素を指定して `HtmlTextWriter.RenderBeginTag()` と `HtmlTextWriter.RenderEndTag()` を呼び出すと、`<BR />` が 1 つのみ (2 つではなく) 正しく挿入されます。
  - 影響を受けるライブラリ: System.Web
  - 推奨されるアクション: 予想される数の `<BR />` が挿入されていることを確認し、運用ジョブでランダムな動作が生じないようにする

- null 引数を指定した CreateDefaultAuthorizationContext の呼び出しが変更されました
  - null の authorizationPolicies 引数を指定して `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` への呼び出しによって返される AuthorizationContext の実装が、.NET Framework 4.6 で変更されました。
  - 影響を受けるライブラリ: System.IdentityModel
  - 推奨されるアクション: null の認証ポリシーが存在する場合に予期される新しい動作を処理していることを確認する
  
- RSACng でキー サイズが非標準の RSA キーが正しく読み込まれるようになりました
  - 4\.6.2 より前の .NET Framework バージョンでは、RSA 証明書のキー サイズが標準ではない顧客は、拡張メソッドの `GetRSAPublicKey()` や `GetRSAPrivateKey()` でそのキーにアクセスできません。 "要求されたキー サイズはサポートされていません" というメッセージと共に `CryptographicException` がスローされます。 .NET Framework 4.6.2 では、この問題は修正されています。 同様に、`RSA.ImportParameters()` と `RSACng.ImportParameters()` は非標準のキー サイズで動作するようになり、`CryptographicException` はスローされなくなりました。
  - 影響を受けるライブラリ: mscorlib、System.Core
  - 推奨されるアクション: RSA キーが想定どおりに動作していることを確認する

- パスのコロン確認が厳密化
  - .NET Framework 4.6.2 では、以前はサポートされていなかったパスをサポートするために (長さと形式の両方で) 数多くの変更が加えられました。 適切なドライブ区切り (コロン) 構文の確認がより正確に行われました。その結果、それ以前は許容されていた、ごく少数のパス API の一部の URI パスがブロックされました。
  - 影響を受けるライブラリ: mscorlib、System.Runtime.Extensions
  - 推奨アクション:

- ClaimsIdentity コンストラクターを呼び出す
  - .NET Framework 4.6.2 以降、`T:System.Security.Claims.ClaimsIdentity` コンストラクターと `T:System.Security.Principal.IIdentity` パラメーターの組み合わせで `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティが設定されるしくみに変更があります。 `T:System.Security.Principal.IIdentity` 引数が `T:System.Security.Claims.ClaimsIdentity` オブジェクトで、その `T:System.Security.Claims.ClaimsIdentity` オブジェクトの `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティが `null` ではない場合、`M:System.Security.Claims.ClaimsIdentity.Clone` メソッドを使用して `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティがアタッチされます。 Framework 4.6.1 以前のバージョンでは、`P:System.Security.Claims.ClaimsIdentify.Actor` プロパティは既存の参照としてアタッチされます。 この変更により、.NET Framework 4.6.2 以降では、新しい `T:System.Security.Claims.ClaimsIdentity` オブジェクトの `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティはコンストラクターの `T:System.Security.Principal.IIdentity` 引数の `P:System.Security.Claims.ClaimsIdentify.Actor` プロパティと同じではなくなりました。 .NET Framework 4.6.1 以前のバージョンでは、等しくなります。
  - 影響を受けるライブラリ: mscorlib
  - 推奨されるアクション: 新しいランタイムで ClaimsIdentity が想定どおりに動作していることを確認する

- DataContractJsonSerializer による制御文字のシリアル化が ECMAScript V6 および V8 対応に
  - .NET framework 4.6.2 以前のバージョンでは、DataContractJsonSerializer によって、\b、\f、\t などの一部の特殊制御文字が、ECMAScript V6 および V8 標準と互換性のある方法でシリアル化されませんでした。 .NET Framework 4.7 以降では、これらの制御文字のシリアル化は ECMAScript V6 および V8 と互換性があります。
  - 影響を受けるライブラリ: System.Runtime.Serialization.Json
  - 推奨されるアクション: DataContractJsonSerializer と同じ動作を保証する
