---
title: セキュリティで保護されたアプリケーションを Microsoft Azure 上で開発する
description: この記事では、Web アプリケーション プロジェクトの実装と検証のフェーズ中に考慮すべきベスト プラクティスについて説明します。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f4add4bf07178aa616e86f8a64b313630466824f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653267"
---
# <a name="develop-secure-applications-on-azure"></a>セキュリティで保護されたアプリケーションを Azure 上で開発する
この記事では、クラウド向けのアプリケーションを開発するときに考慮するセキュリティ アクティビティとコントロールについて説明します。 Microsoft [セキュリティ開発ライフサイクル (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) の実装と検証のフェーズ中に考慮するセキュリティの質問と概念について説明します。 目標は、より安全なアプリケーションの開発に使用できるアクティビティと Azure サービスの定義を手助けすることです。

この記事では、次の SDL フェーズについて説明します。

- 実装
- 確認

## <a name="implementation"></a>実装
実装フェーズの対象は、早期防止のためのベスト プラクティスを確立し、コードからセキュリティの問題を検出して削除することです。
アプリケーションが意図していない方法で使用されることを想定します。 これは、アプリケーションの偶発的または意図的な誤用を防ぐのに役立ちます。

### <a name="perform-code-reviews"></a>コード レビューを実行する

コードをチェックインする前に、[コード レビュー](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)を実施して、コードの全体的な品質を高め、バグが発生するリスクを小さくします。 [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) を使用して、コード レビュー プロセスを管理できます。

### <a name="perform-static-code-analysis"></a>静的コード分析を実行する

[静的コード分析](https://www.owasp.org/index.php/Static_Code_Analysis) ("*ソース コード分析*" とも呼ばれます) は、通常、コード レビューの一部として実行されます。 一般に、静的コード分析は、静的コード分析ツールを実行し、[テイント チェック](https://en.wikipedia.org/wiki/Taint_checking)や[データ フロー分析](https://en.wikipedia.org/wiki/Data-flow_analysis)などの手法を使用して、実行されていないコードの潜在的な脆弱性を見つけることを指します。

Azure Marketplace では、静的コード分析を実行してコード レビューを支援する[開発者ツール](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)が提供されています。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>アプリケーションに対するすべての入力を検証してサニタイズする

すべての入力を信頼されていないものとして扱い、最も一般的な Web アプリケーションの脆弱性からアプリケーションを保護します。 信頼されていないデータは、インジェクション攻撃の手段です。 アプリケーションに対する入力には、URL 内のパラメーター、ユーザーからの入力、データベースや API からのデータ、およびユーザーが操作できる可能性のあるすべての渡されるものが含まれます。 アプリケーションでは、何らかの方法でデータを使用する前に (ユーザーへの表示を含む)、データが構文的および意味的に有効であることを[検証する](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs)必要があります。

データ フローの早い段階で入力を検証し、適切な形式のデータのみがワークフローに入ることを確認します。 不適切なデータがデータベースに保存されたり、下流コンポーネントでの異常の原因になったりしないようにする必要があります。

ブラックリストとホワイトリストは、入力構文の検証を実行するための 2 つの一般的なアプローチです。

  - ブラックリストでは、特定のユーザー入力に "既知の悪意のある" コンテンツが含まれていないことの確認が試みられます。

  - ホワイトリストでは、特定のユーザー入力が "既知の適切な" 入力のセットと一致することの確認が試みられます。 文字ベースのホワイトリストは、ユーザー入力に "既知の適切な" 文字のみが含まれていること、または入力が既知の形式と一致することをアプリケーションで確認する、ホワイトリストの形式です。
    たとえば、これには、ユーザー名に英数字のみが含まれていることや、正確に 2 つの数値が含まれていることの確認が含まれる可能性があります。

ホワイトリストは、セキュリティで保護されたソフトウェアを構築するための推奨されるアプローチです。
ブラックリストは、不適切かもしれない入力の完全な一覧を考えることは不可能であるため、エラーが発生しやすくなります。

この処理は、クライアント側ではなくサーバー側で行います (または、サーバー側とクライアント側)。

### <a name="verify-your-applications-outputs"></a>アプリケーションの出力を確認する

視覚的に、またはドキュメント内で提示するすべての出力を、常にエンコードおよびエスケープする必要があります。 [エスケープ](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29) ("*出力エンコード*" とも呼ばれます) は、信頼されていないデータがインジェクション攻撃の手段にならないようにするために使用されます。 エスケープとデータの入力規則を組み合わせると、システム全体のセキュリティを強化する多重防御手段が提供されます。

エスケープでは、すべてのものが "*出力*" として表示されることが確認されます。 また、エスケープにより、データが実行を目的としたものではないことがインタープリターで認識され、攻撃の実行が防止されます。 これは、"*クロスサイト スクリプティング*" (XSS) と呼ばれる別の一般的な攻撃手法です。

サードパーティの Web フレームワークを使用している場合は、[OWASP XSS 防止チート シート](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)を使用して、Web サイトでの出力エンコードのオプションを確認できます。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>データベースに接続するときにパラメーター化されたクエリを使用する

コード内で "その場" でインライン データベース クエリを作成し、それをデータベースに直接送信してはいけません。 アプリケーションに挿入された悪意のあるコードにより、データベースの盗難、ワイプ、変更が発生する可能性があります。 また、アプリケーションを使用して、データベースをホストしているオペレーティング システムで悪意のあるオペレーティング システム コマンドが実行される可能性もあります。

代わりに、パラメーター化されたクエリまたはストアド プロシージャを使用します。 パラメーター化されたクエリを使用すると、コードから安全にプロシージャを呼び出し、クエリ ステートメントの一部として処理されることを心配せずに、文字列を渡すことができます。

### <a name="remove-standard-server-headers"></a>標準的なサーバー ヘッダーを削除する

Server、X-Powered-By、X-AspNet-Version などのヘッダーによって、サーバーおよび基盤のテクノロジに関する情報が公開されます。 これらのヘッダーを抑制することで、アプリケーションに指紋が残らないようにすることをお勧めします。
「[removing standard server headers on Azure websites (Azure Web サイトでの標準的なサーバー ヘッダーの削除)](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)」をご覧ください

### <a name="segregate-your-production-data"></a>運用データを分離する

運用データ (または "実" データ) を、開発、テスト、またはビジネス以外の目的に使用しないようにする必要があります。 すべての開発とテストには、マスクされた ([匿名化された](https://en.wikipedia.org/wiki/Data_anonymization)) データセットを使用する必要があります。

これは、実データにアクセスできるユーザーが少なくなり、攻撃対象領域が経ることを意味します。 また、個人データを見る従業員が減り、機密性が侵害される可能性がなくなることも意味します。

### <a name="implement-a-strong-password-policy"></a>強力なパスワード ポリシーを実装する

ブルート フォース攻撃や辞書ベース推測を防ぐには、ユーザーが必ず複雑なパスワード (例: 12 文字以上、英数字と特殊文字) を設定するように、強力なパスワード ポリシーを実装する必要があります。

ID フレームワークを使用して、パスワード ポリシーを作成し、適用できます。 Azure AD B2C では、[組み込みポリシー](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows#create-a-password-reset-user-flow)、[パスワード リセットのセルフサービス](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)、その他が提供されて、パスワード管理に役立ちます。

既定のアカウントへの攻撃を防ぐには、すべてのキーやパスワードが置換可能で、リソースをインストールした後で生成または置換されたことを確認します。

アプリケーションでパスワードを自動生成する必要がある場合は、自動生成されたパスワードがランダムで、エントロピーが高いことを確認します。

### <a name="validate-file-uploads"></a>ファイルのアップロードを検証する

アプリケーションで[ファイルのアップロード](https://www.owasp.org/index.php/Unrestricted_File_Upload)を許可する場合は、この危険なアクティビティのために実行できる予防策を検討してください。 多くの攻撃において最初のステップになるのは、攻撃対象のシステムに悪意のあるコードを取り込むことです。 ファイル アップロードを使用すると、攻撃者がこれを達成しやすくなります。 OWASP では、ファイルを検証し、アップロードされているファイルが安全であることを確認するするためのソリューションが提供されます。

マルウェア対策保護は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するのに役立ちます。 [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) または Microsoft パートナーのエンドポイント保護ソリューション ([Trend Micro](https://www.trendmicro.com/azure/)、[Symantec](https://www.symantec.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)、および[System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)) をインストールします。

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) には、リアルタイム保護、スケジュールされたスキャン、マルウェアの駆除、シグネチャの更新、エンジンの更新、サンプルのレポート、および除外イベントの収集などの機能が含まれます。 デプロイと検出の組み込み (アラートとインシデント) を容易にするために、Microsoft Antimalware とパートナー ソリューションを [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) と統合できます。

### <a name="dont-cache-sensitive-content"></a>機密性の高いコンテンツをキャッシュしない

機密性の高いコンテンツをブラウザーにキャッシュしないでください。 ブラウザーでは、キャッシュと履歴のために情報を保存できます。 キャッシュされたファイルは、フォルダーに保存されます (Internet Explorer の場合は Temporary Internet Files フォルダー)。 これらのページが再び参照されると、ブラウザーではキャッシュからページが表示されます。 機密情報 (住所、クレジット カードの詳細、社会保障番号、ユーザー名など) がユーザーに表示された場合、この情報がブラウザーのキャッシュに保存されるので、ブラウザーのキャッシュを調べることで、または単にブラウザーの **[戻る]** ボタンをクリックすることで、情報を取得できる可能性があります。

## <a name="verification"></a>確認
検証フェーズには、コードが、前のフェーズで確立されたセキュリティとプライバシーの原則を満たすことを確認する包括的な作業が含まれます。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>アプリケーションの依存関係の脆弱性を発見して修正する

アプリケーションとその依存するライブラリをスキャンし、既知の脆弱性のあるコンポーネントを識別します。 このスキャンの実行に使用できる製品としては、[OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check)、[Snyk](https://snyk.io/)、[Black Duck](https://www.blackducksoftware.com/) などがあります。

Azure App Service Web Apps では、[Tinfoil Security](https://www.tinfoilsecurity.com/) を利用する脆弱性スキャンを使用できます。 [Tinfoil Security の App Service 向けスキャン機能](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)を使用すると、開発者や管理者はコスト効率に優れた統合型の手法で迅速に脆弱性の問題を発見し、攻撃者から悪用される前に対処することができます。

> [!NOTE]
> また、[Tinfoil Security と Azure AD を統合する](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial)こともできます。 Tinfoil Security と Azure AD の統合には、次の利点があります。
>  - Azure AD で、Tinfoil Security にアクセスできるユーザーを制御できます。
>  - ユーザーは、自分の Azure AD アカウントを使用して Tinfoil Security に自動的にサインインできます (シングル サインオン)。
>  - 単一の中央サイト (Azure portal) でアカウントを管理できます。

### <a name="test-your-application-in-an-operating-state"></a>動作状態でアプリケーションをテストする

動的アプリケーション セキュリティ テスト (DAST) は、動作状態でアプリケーションをテストして、セキュリティの脆弱性を見つけるプロセスです。 DAST ツールでは、実行中のプログラムが分析されて、メモリの破損、安全でないサーバーの構成、クロスサイト スクリプティング、ユーザー特権の問題、SQL インジェクション、その他の重要なセキュリティ上の問題などのセキュリティの脆弱性が検出されます。

DAST は、静的アプリケーション セキュリティ テスト (SAST) とは異なるものです。 SAST ツールでは、セキュリティ上の欠陥を見つけるため、コードが実行されていないときに、ソース コードまたはコンパイル済みバージョンのコードが分析されます。

できればセキュリティ専門家 ([侵入テスト担当者](https://docs.microsoft.com/azure/security/azure-security-pen-testing)または脆弱性の審査機関) の支援を得て、DAST を実行してください。 セキュリティの専門家を手配できない場合は、Web プロキシ スキャナーを使用し、トレーニングした後、自分で DAST を実行してもかまいません。 早い段階で DAST スキャナーに接続し、コードに明らかなセキュリティの問題が発生しないことを確認します。 Web アプリケーション脆弱性スキャナーの一覧については、[OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) のサイトをご覧ください。

### <a name="perform-fuzz-testing"></a>ファジー テストを実行する

[ファジー テスト](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)では、正しくないデータまたはランダムなデータをアプリケーションに意図的に導入することで、プログラムの障害を誘発します。 プログラムの障害の誘発は、アプリケーションをリリースする前に、潜在的なセキュリティの問題を明らかにするのに役立ちます。

[Security Risk Detection](https://docs.microsoft.com/security-risk-detection/) は、ソフトウェアのセキュリティ クリティカルなバグを発見するための、マイクロソフト独自のファジー テスト サービスです。

### <a name="conduct-attack-surface-review"></a>攻撃対象領域レビューを実施する

コードが完成した後で攻撃対象領域をレビューすると、アプリケーションやシステムに対して設計や実装の変更が考慮されたことを確認するのに役立ちます。 脅威モデルなど、変更の結果として作成された新しい攻撃ベクトルがレビューされて軽減されたことを確認するのに役立ちます。

アプリケーションをスキャンすることで、攻撃対象領域の概要を作成できます。 Microsoft では、[Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487) と呼ばれる攻撃対象領域分析ツールが提供されています。 市販されている多くの動的テストおよび脆弱性スキャン ツールやサービスから選択できます ([OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、[Arachni](http://arachni-scanner.com/)、[Skipfish](https://code.google.com/p/skipfish/)、[w3af](http://w3af.sourceforge.net/) など)。 これらのスキャン ツールでは、アプリがクロールされて、Web 経由でアクセスできるアプリケーションの部分がマップされます。 また、Azure Marketplace で同様の[開発者ツール](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)を検索することもできます。

### <a name="perform-security-penetration-testing"></a>セキュリティ侵入テストを実施する

アプリケーションのセキュリティを確保することは、他の機能をテストすることと同じくらい大切です。 [侵入テスト](https://docs.microsoft.com/azure/security/azure-security-pen-testing)を、標準的なビルドおよびデプロイ プロセスの一環として実施してください。 デプロイしたアプリケーションに対して定期的なセキュリティ テストと脆弱性スキャンをスケジュールし、開放ポート、エンドポイント、攻撃を監視します。

### <a name="run-security-verification-tests"></a>セキュリティ確認テストを実行する

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) には、Azure プラットフォームの複数のサービス用の SVT が含まれています。 定期的にこれらの SVT を実行し、Azure サブスクリプションと、アプリケーションを構成するさまざまなリソースが、セキュリティで保護された状態であることを確認します。 また、AzSK の継続的インテグレーション/継続的デプロイ (CI/CD) 拡張機能を使用して、これらのテストを自動化することもできます。SVT を Visual Studio の拡張機能として使用できるようになります。

## <a name="next-steps"></a>次の手順
次の記事では、セキュリティで保護されたアプリケーションを設計してデプロイするのに役立つセキュリティ制御とアクティビティが推奨されています。

- [セキュリティで保護されたアプリケーションを設計する](secure-design.md)
- [セキュリティで保護されたアプリケーションをデプロイする](secure-deploy.md)
