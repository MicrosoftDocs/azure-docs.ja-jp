---
title: Azure AD B2C カスタム ポリシーの概要 |Microsoft Docs
description: Azure Active Directory B2C のカスタム ポリシーと Identity Experience Framework に関するトピック。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 514ce0a43904048952f38edd6a9d38713f6ef8f3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936667"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Azure AD B2C カスタム ポリシーの概要

カスタム ポリシーは、Azure Active Directory B2C (Azure AD B2C) テナントの動作を定義する構成ファイルです。 [ユーザー フロー](user-flow-overview.md)が、最も一般的な ID タスク用に Azure AD B2C ポータルで事前に定義されているのに対して、カスタム ポリシーは、さまざまなタスクを実行するために ID 開発者が編集できます。

カスタム ポリシーは、完全に構成可能で、ポリシーに基づきます。 カスタム ポリシーにより、標準のプロトコル形式 (OpenID Connect、OAuth、SAML など) といくつかの標準以外の形式 (REST API ベースのシステム間の要求の交換など) のエンティティ間で信頼が調整されます。 このフレームワークにより、ホワイトラベルが付けられたわかりやすいエクスペリエンスが作成されます。

カスタム ポリシーは、階層型チェーンで互いを参照する 1 つ以上の XML 形式ファイルとして表されます。 XML 要素により、構成要素、ユーザーおよび他の利用者との対話、ビジネス ロジックが定義されます。 

## <a name="custom-policy-starter-pack"></a>カスタム ポリシー スターター パック

Azure AD B2C カスタム ポリシー [スターター パック](custom-policy-get-started.md#get-the-starter-pack)には、すぐに使用できるように事前に構築されたいくつかのポリシーが用意されています。 これらの各スターター パックには、説明したシナリオを実現するために必要な最小数の技術プロファイルとユーザー体験が含まれています。

- **LocalAccounts** - ローカル アカウントのみの使用を可能にします。
- **SocialAccounts** - ソーシャル (フェデレーション) アカウントのみの使用を可能にします。
- **SocialAndLocalAccounts** - ローカル アカウントとソーシャル アカウントの両方の使用を可能にします。 ほとんどのサンプルは、このポリシーを参照しています。
- **SocialAndLocalAccountsWithMFA** - ソーシャル、ローカル、および多要素認証オプションの使用を可能にします。

## <a name="understanding-the-basics"></a>基本情報 

### <a name="claims"></a>Claims

要求は、Azure AD B2C ポリシーの実行時に、データの一時的なストレージとなります。 ユーザーに関する情報 (名、姓など) や、ユーザーまたは他のシステムから取得したその他の要求 (要求交換) を格納できます。 [要求スキーマ](claimsschema.md)は、要求を宣言する場所です。 

ポリシーを実行すると、Azure AD B2C により、内部および外部の利用者との間で要求が送受信され、それらの要求のサブセットがトークンの一部として証明書利用者アプリケーションに送信されます。 要求は次のように使用されます。 

- 要求は、ディレクトリ ユーザー オブジェクトに対して保存、読み取り、または更新されます。
- 要求は、外部 ID プロバイダーから受信されます。
- 要求は、カスタム REST API サービスを使用して送信または受信されます。
- データは、サインアップまたはプロファイルの編集のフロー中に、ユーザーからの要求として収集されます。

### <a name="manipulating-your-claims"></a>要求の操作

[要求変換](claimstransformations.md)は、特定の要求を別の要求に変換したり、要求を評価したり、要求値を設定したりするために使用できる定義済みの関数です。 たとえば、文字列コレクションへの項目の追加、文字列の大文字と小文字の変更、日付と時刻の要求の評価などを行うことができます。 要求変換により、変換メソッドが指定されます。 

### <a name="customize-and-localize-your-ui"></a>UI のカスタマイズとローカライズ

Web ブラウザーにページを表示してユーザーから情報を収集する場合は、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)を使用します。 セルフアサート技術プロファイルを編集して、[要求を追加し、ユーザー入力をカスタマイズする](./configure-user-input.md)ことができます。

セルフアサート技術プロファイルの[ユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)には、カスタマイズされた HTML コンテンツを使用して[コンテンツ定義](contentdefinitions.md)要素に URL を指定します。 セルフアサート技術プロファイルで、このコンテンツ定義 ID をポイントします。

言語固有の文字列をカスタマイズするには、[localization](localization.md) 要素を使用します。 コンテンツ定義には、読み込み対象のローカライズされたリソースのリストを指定する [localization](localization.md) 参照を含めることができます。 Azure AD B2C により、ユーザー インターフェイス要素が URL から読み込まれた HTML コンテンツとマージされ、ユーザーにページが表示されます。 

## <a name="relying-party-policy-overview"></a>証明書利用者ポリシーの概要

証明書利用者アプリケーション (SAML プロトコルでは、サービス プロバイダーと呼ばれます) では、特定のユーザー体験を実行するために[証明書利用者ポリシー](relyingparty.md)が呼び出されます。 証明書利用者ポリシーにより、実行するユーザー体験と、トークンに含まれる要求のリストが指定されます。 

![ポリシー実行フローを示している図](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

同じポリシーを使用するすべての証明書利用者アプリケーションは、同じトークン要求を受け取り、ユーザーは同じユーザー体験を経験します。

### <a name="user-journeys"></a>ユーザー体験

[ユーザー体験](userjourneys.md)では、ユーザーがアプリケーションにアクセスするためにたどるパスを使用して、ビジネス ロジックを定義できます。 ユーザーは、ユーザー体験を通じて、アプリケーションに提示される要求を取得します。 ユーザー体験は、[一連のオーケストレーション ステップ](userjourneys.md#orchestrationsteps)から構築されます。 ユーザーは、トークンを取得するために、最後の手順に進む必要があります。 

次の手順では、[ソーシャルおよびローカル アカウント スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) ポリシーにオーケストレーション ステップを追加する方法について説明します。 追加された REST API 呼び出しの例を次に示します。

![カスタマイズされたユーザー体験](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>オーケストレーション ステップ

オーケストレーション ステップは、その目的または意図する機能を実装するメソッドを参照します。 このメソッドは、[技術プロファイル](technicalprofiles.md)と呼ばれます。 ビジネス ロジックをより適切に表すためにユーザー体験に分岐が必要な場合、オーケストレーション ステップは[サブ体験](subjourneys.md)を参照します。 サブ体験には、独自のオーケストレーション ステップのセットが含まれています。

ユーザーは、トークンを取得するために、ユーザー体験の最後のオーケストレーション ステップに進む必要があります。 ただし、ユーザーはすべてのオーケストレーション ステップを通過する必要がない場合があります。 オーケストレーション ステップは、オーケストレーション ステップで定義されている[前提条件](userjourneys.md#preconditions)に基づいて、条件付きで実行できます。 

オーケストレーション ステップが完了すると、Azure AD B2C では、出力された要求が **要求バッグ** に格納されます。 要求バッグ内の要求は、ユーザー体験のその後のオーケストレーション ステップで利用できます。

次の図は、ユーザー体験のオーケストレーション ステップが要求バッグにアクセスする方法を示しています。

![Azure AD B2C のユーザー体験](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>技術プロファイル

技術プロファイルには、さまざまな種類の利用者と通信するためのインターフェイスが用意されています。 ユーザー体験により、オーケストレーション ステップを通じて呼び出し元の技術プロファイルが組み合わされて、ビジネス ロジックが定義されます。

技術プロファイルのすべての種類で同じ概念を共有します。 入力要求を送信し、要求の変換を実行して、構成された利用者と通信します。 プロセスが完了すると、技術プロファイルから要求バッグに出力要求が返されます。 詳細については、[技術プロファイルの概要](technicalprofiles.md)を参照してください。

### <a name="validation-technical-profile"></a>検証技術プロファイル

ユーザーによるユーザー インターフェイスの操作中に、収集されたデータを検証することもできます。 ユーザーと対話するには、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)を使用する必要があります。

ユーザー入力を検証する場合は、セルフアサート技術プロファイルから[検証技術プロファイル](validation-technical-profile.md)が呼び出されます。 検証技術プロファイルは、非対話型の技術プロファイルを呼び出す方法です。 この場合、技術プロファイルによって出力要求またはエラー メッセージを返すことができます。 画面上でエラー メッセージがユーザーに表示され、ユーザーは再試行することができます。

次の図は、Azure AD B2C で検証技術プロファイルを使用してユーザーの資格情報を検証する方法を示しています。

![検証技術プロファイルの図](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>継承モデル

各スターター パックには、次のファイルが含まれています。

- **Base ファイル**。ほとんどの定義が含まれています。 ポリシーのトラブルシューティングおよび長期間の保守に役立つように、このファイルへの変更は最小限に抑えるようにしてください。
- **Extensions** ファイル。テナントの固有の構成変更を保持しています このポリシー ファイルは、Base ファイルから派生したものです。 このファイルを使用して、新しい機能を追加するか、既存の機能をオーバーライドします。 たとえば、このファイルを使用して、新しい ID プロバイダーとフェデレーションします。
- **証明書利用者 (RP)** ファイル。証明書利用者アプリケーション (Web、モバイル、またはデスクトップ アプリケーションなど) から直接呼び出される、単一タスクに焦点を置いています。 サインアップやサインイン、パスワードのリセット、プロファイルの編集などの各自のタスクには、それぞれ独自の証明書利用者ポリシー ファイルが必要です。 このポリシー ファイルは、Extensions ファイルから派生したものです。

継承モデルは次のとおりです。

- どのレベルの子ポリシーでも、親ポリシーから継承することができ、新しい要素を追加することによって拡張することができます。
- より複雑なシナリオでは、継承レベルをさらに追加できます (合計 5 個まで)。
- 証明書利用者ポリシーをさらに追加できます。 たとえば、アカウントの削除、電話番号の変更、SAML 証明書利用者ポリシーなどです。

次の図は、ポリシー ファイルと証明書利用者アプリケーションの関係を示しています。

![信頼フレームワーク ポリシーの継承モデルを示している図](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>ガイダンスとベスト プラクティス

### <a name="best-practices"></a>ベスト プラクティス

Azure AD B2C カスタム ポリシー内で、独自のビジネス ロジックを統合して、必要なユーザー エクスペリエンスを構築し、サービスの機能を拡張できます。 作業を開始するためのベスト プラクティスと推奨事項のセットが用意されています。

- **拡張ポリシー** または **証明書利用者ポリシー** 内にロジックを作成します。 新しい要素を追加できます。その場合、同じ ID を参照することによって基本ポリシーがオーバーライドされます。 これにより、Microsoft が新しいスターター パックをリリースした場合に、後で基本ポリシーを簡単にアップグレードできるようにしながら、プロジェクトをスケールアウトすることができます。
- **基本ポリシー** 内では、変更を行わないことを強くお勧めします。 必要に応じて、変更が加えられた場所にコメントを追加します。
- 技術プロファイルのメタデータなどの要素をオーバーライドする場合は、基本ポリシーから技術プロファイル全体をコピーしないようにしてください。 代わりに、要素の必須セクションだけをコピーします。 変更を行う方法の例については、[電子メールの確認の無効化](./disable-email-verification.md)に関するページを参照してください。
- コア機能が共有されている技術プロファイルの複製を減らすには、[技術プロファイルを含める方法](technicalprofiles.md#include-technical-profile)に従います。
- サインイン時に Azure AD ディレクトリに書き込まないようにしてください。書き込むと、調整の問題が発生する可能性があります。
- ポリシーに REST API などの外部依存関係がある場合は、それらの可用性が高いことを確認します。
- より優れたユーザー エクスペリエンスを実現するには、[オンライン コンテンツ配信](../cdn/index.yml)を使用してカスタム HTML テンプレートがグローバルにデプロイされていることを確認します。 Azure Content Delivery Network (CDN) を使用すると、読み込み時間を短縮し、帯域幅を節約して、応答速度を向上させることができます。
- ユーザー体験に変更を加える場合、基本ポリシーから拡張ポリシーにユーザー体験全体をコピーします。 コピーしたユーザー体験に対して一意のユーザー体験 ID を指定します。 次に、[証明書利用者ポリシー](relyingparty.md)で、新しいユーザー体験を指すように[既定のユーザー体験](relyingparty.md#defaultuserjourney)要素を変更します。

## <a name="troubleshooting"></a>トラブルシューティング

Azure AD B2C ポリシーを使用して開発を行う場合は、ユーザー体験の実行中にエラーまたは例外が発生することがあります。 これは、Application Insights を使用して調査できます。

- Application Insights を Azure AD B2C と統合して、[例外を診断します](troubleshoot-with-application-insights.md)。
- [Visual Studio Code の Azure AD B2C 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)を使用すると、ポリシーの名前と時刻に基づいて、[ログにアクセスして視覚化する](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md)ことができます。
- カスタム ポリシーの設定における最も一般的なエラーが、不適切な形式の XML です。 XML ファイルをアップロードする前にエラーを特定するには、[XML スキーマ検証](troubleshoot-custom-policies.md)を使用します。

## <a name="continuous-integration"></a>継続的インテグレーション

Azure Pipelines で設定した継続的インテグレーションとデリバリー (CI/CD) パイプラインを使用して、[ソフトウェア デリバリーとコード管理の自動化に Azure AD B2C カスタム ポリシーを含める](deploy-custom-policies-devops.md)ことができます。 開発、テスト、運用などのさまざまな Azure AD B2C 環境にデプロイするときは、手動プロセスを削除し、Azure Pipelines を使用して自動テストを実行することをお勧めします。

## <a name="prepare-your-environment"></a>環境を準備する

Azure AD B2C カスタム ポリシーの使用を開始します。

1. [Azure AD B2C テナントを作成する](tutorial-create-tenant.md)
1. ポリシーをテストできるようにするために、Azure portal を使用して [Web アプリケーションを登録します](tutorial-register-applications.md)。
1. 必要な[ポリシー キー](custom-policy-get-started.md#add-signing-and-encryption-keys)を追加し、[Identity Experience Framework アプリケーションを登録します](custom-policy-get-started.md#register-identity-experience-framework-applications)。
1. [Azure AD B2C ポリシー スターター パックを取得し](custom-policy-get-started.md#get-the-starter-pack)、テナントにアップロードします。 
1. スターター パックをアップロードした後、[サインアップまたはサインイン ポリシーをテストします](custom-policy-get-started.md#test-the-custom-policy)。
1. [Visual Studio Code](https://code.visualstudio.com/) (VS Code) をダウンロードしてインストールすることをお勧めします。 Visual Studio Code は、軽量でありながら強力なソース コード エディターです。これはデスクトップで使用でき、Windows、macOS、Linux に対応しています。 VS Code では、[VS Code 用の Azure AD B2C 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)をインストールすることにより、Azure AD B2C カスタム ポリシーの XML ファイルをすばやく操作および編集できます。
 
## <a name="next-steps"></a>次の手順

Azure AD B2C ポリシーを設定してテストしたら、ポリシーのカスタマイズを開始できます。 方法の詳細については、次の記事を参照してください。

- カスタム ポリシーを使用して[要求を追加し、ユーザー入力をカスタマイズする](./configure-user-input.md)。 一部のスターター パック技術プロファイルをカスタマイズして要求を定義する方法、およびユーザー インターフェイスに要求を追加する方法について説明します。
- カスタム ポリシーを使用して、アプリケーションの[ユーザー インターフェイスをカスタマイズします](customize-ui-with-html.md)。 独自の HTML コンテンツを作成し、コンテンツ定義をカスタマイズする方法について説明します。
- カスタム ポリシーを使用して、アプリケーションの[ユーザー インターフェイスをローカライズします](./language-customization.md)。 ローカライズされたリソース要素を追加して、サポートされている言語のリストを設定し、言語固有のラベルを指定する方法について説明します。
- ポリシーの開発およびテスト中に、[電子メールの確認を無効にできます](./disable-email-verification.md)。 技術プロファイルのメタデータを上書きする方法について説明します。
- カスタム ポリシーを使用して、[Google アカウントでのサインインを設定します](./identity-provider-google.md)。 OAuth2 技術プロファイルを使用して新しい要求プロバイダーを作成する方法について説明します。 次に、Google のサインイン オプションを含めるようにユーザー体験をカスタマイズします。
- カスタム ポリシーに関する問題を診断するには、[Application Insights を使用して Azure Active Directory B2C ログを収集します](troubleshoot-with-application-insights.md)。 新しい技術プロファイルを追加する方法と、証明書利用者ポリシーを構成する方法について説明します。
