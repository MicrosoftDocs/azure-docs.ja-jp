---
title: オンプレミスの Azure AD パスワード保護に関する FAQ
description: オンプレミスの Active Directory Domain Services 環境での Azure AD パスワード保護に関するよく寄せられる質問について確認します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625130"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>オンプレミスでの Azure AD パスワード保護に関するよく寄せられる質問

このセクションでは、Azure AD パスワード保護に関してよく寄せられる質問への回答を示します。

## <a name="general-questions"></a>一般的な質問

**Q:安全なパスワードを選択する方法に関して、ユーザーにどのようなガイダンスが与えられますか?**

このトピックに関する Microsoft の現在のガイダンスについては、次のリンクを参照してください。

[Microsoft のパスワードのガイダンス](https://www.microsoft.com/research/publication/password-guidance)

**Q:オンプレミスの Azure AD パスワード保護はパブリックでないクラウドでサポートされますか?**

オンプレミスの Azure AD パスワード保護は、パブリック クラウドと Arlington クラウドでサポートされます。 その他のクラウドの提供開始についての日付は発表されていません。

Azure AD ポータルでは、サポートされているクラウド以外の場合でもオンプレミス固有の [Windows Server Active Directory のパスワード保護] 構成を変更できます。このような変更は保存されますが、そうでなければ実行されません。 サポートされていないクラウドでは、オンプレミスのプロキシ エージェントまたはフォレストの登録はサポートされません。そのような登録の試行は常に失敗します。

**Q:どのようにして Azure AD パスワード保護の利点を自分のオンプレミスのユーザーのサブセットに適用できますか?**

サポートされていません。 デプロイして有効にされると、Azure AD パスワード保護では識別を行いません。すべてのユーザーがセキュリティの利点を等しく受け取ります。

**Q:パスワードの変更とパスワードの設定 (またはリセット) の違いは何ですか?**

パスワードの変更は、ユーザーが古いパスワードを知っていることを証明した後に新しいパスワードを選択する場合のアクションです。 パスワードの変更は、たとえば、ユーザーが Windows にログインした後に新しいパスワードを選択するように求められたときに発生します。

パスワードの設定 (パスワードのリセットとも呼ばれます) は、たとえば Active Directory ユーザーとコンピューターの管理ツールを使用して、管理者がアカウントのパスワードを新しいパスワードに置き換える場合のアクションです。 この操作には高いレベルの特権 (通常はドメイン管理者) が必要であり、通常、操作を実行する担当者は古いパスワードを知りません。 ヘルプ デスクのシナリオで、パスワードの設定がよく実行されます。たとえば、パスワードを忘れたユーザーを支援する場合などです。 また、パスワードを指定して新しいユーザー アカウントを初めて作成するときにもパスワードの設定イベントが発生します。

パスワード検証ポリシーは、実行されているのがパスワードの変更か設定かに関係なく同じように動作します。 Azure AD パスワード保護 DC エージェント サービスは、パスワードの変更または設定操作が行われたかどうかをユーザーに通知するために、さまざまなイベントをログに記録します。  「[Azure AD パスワード保護の監視とログ記録](./howto-password-ban-bad-on-premises-monitor.md)」をご覧ください。

**Q:Azure AD のパスワード保護では、インストール後に既存のパスワードが検証されますか?**

いいえ。Azure AD のパスワード保護では、パスワードの変更または設定操作中に、クリア テキストのパスワードに対してのみパスワード ポリシーを適用できます。 Active Directory によってパスワードが受け入れられると、そのパスワードの認証プロトコル固有のハッシュのみが保持されます。 クリア テキストのパスワードは保存されないため、Azure AD のパスワード保護で既存のパスワードを検証することはできません。

Azure AD のパスワード保護を最初にデプロイした後、すべてのユーザーとアカウントでは、時間の経過に伴い通常どおり既存のパスワードの有効期限が切れると、Azure AD のパスワード保護で検証されたパスワードを使用し始めます。 必要に応じて、ユーザー アカウント パスワードを 1 回だけ手動で期限切れにすることによって、このプロセスを高速化できます。

[パスワードを無期限にする] を使用して構成されたアカウントの場合は、手動で期限切れにしない限りパスワードの変更が強制されることはありません。

**Q:Active Directory ユーザーとコンピューター管理スナップインを利用して弱いパスワードを設定しようとすると、重複パスワード拒否イベントがログに記録されるのはなぜですか。**

Active Directory ユーザーとコンピューター管理スナップインではまず、Kerberos プロトコルを利用して新しいパスワードを設定しようとします。 エラーが発生すると、このスナップインでは次に、レガシ (SAM RPC) プロトコル (使用される特定のプロトコルは重要ではありません) を利用してパスワードを設定しようとします。 新しいパスワードが Azure AD パスワード保護で弱いと見なされると、このスナップイン動作の結果、2 セットのパスワード リセット拒否イベントがログに記録されます。

**Q:Azure AD パスワード保護のパスワード検証イベントが空のユーザー名でログに記録されるのはなぜですか。**

Active Directory では、パスワードをテストして、ドメインの現在のパスワード複雑さ要件が満たされているかどうかを確認する機能がサポートされています (たとえば、[NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API を使用して)。 この方法でパスワードが検証されるとき、テストには、Azure AD パスワード保護などのパスワード フィルター DLL ベースの製品による検証も含まれます。ただし、特定のパスワード フィルター DLL に渡されるユーザー名は空になります。 このシナリオの Azure AD パスワード保護でも、現在有効なパスワード ポリシーを使用してパスワードが検証され、結果をキャプチャするためのイベント ログ メッセージが発行されますが、イベント ログ メッセージのユーザー名フィールドは空になります。

**Q:その他のパスワード フィルター ベースの製品とサイド バイ サイドで Azure AD パスワード保護をインストールすることはサポートされていますか?**

はい。 複数の登録されたパスワード フィルター DLL に対するサポートは、コア Windows 機能であり、Azure AD パスワード保護に固有のものではありません。 登録されたパスワード フィルター DLL はすべて、パスワードを受け入れる前に一致している必要があります。

**Q:Azure を使用せずに、Active Directory 環境に Azure AD パスワード保護をデプロイし、構成するにはどうすればよいですか?**

サポートされていません。 Azure AD パスワード保護は、オンプレミスの Active Directory 環境への拡張をサポートする Azure の機能です。

**Q:Active Directory レベルでポリシーの内容を変更するにはどうすればよいですか?**

サポートされていません。 ポリシーを管理するには、Azure AD ポータルを使用する必要があります。 前の質問も参照してください。

**Q:sysvol レプリケーションに DFSR が必要なのはなぜですか?**

FRS (DFSR に対する先行テクノロジ) には、多くの既知の問題があり、より新しいバージョンの Windows Server Active Directory ではまったくサポートされていません。 Azure AD パスワード保護のゼロ テストは、FRS で構成されたドメインで行われます。

詳細については、次の記事を参照してください。

[sysvol レプリケーションを DFSR に移行するケース](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[FRS の終了が近づいています](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

ご利用のドメインでまだ DFSR を使用していない場合、Azure AD パスワード保護をインストールする前に DFSR 使用にドメインを移行する必要があります。 詳細については、次のリンクを参照してください。

[SYSVOL レプリケーション移行ガイド: FRS レプリケーションから DFS レプリケーションに移行する](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD パスワード保護 DC エージェント ソフトウェアは、現在、sysvol レプリケーションにまだ FRS を使用しているドメインのドメイン コントローラーにインストールされますが、この環境ではソフトウェアは正しく機能しません。 その他のマイナスの副作用としては、個々のファイルを複製できない、sysvol 復元処理が成功したように見えたが、一部のファイルの複製に失敗しており、何のエラーも表示されない、などがあります。 できるだけ早く、DFSR の使用にドメインを移行してください。DFSR に固有のメリットがあるだけでなく、Azure AD パスワード保護のデプロイのブロックを解除します。 今後のバージョンでは、ドメインで依然として FRS を使用している場合、このソフトウェアは自動的に無効になります。

**Q:機能には、ドメイン sysvol 共有にどのくらいのディスク領域が必要ですか?**

正確な領域の使用量は、Microsoft グローバル禁止リストとテナントごとのカスタム リストで禁止されているトークンの数と長さ、および暗号化オーバーヘッドなどの要素に依存するため、さまざまに異なります。 これらのリストの内容は、将来拡大する可能性があります。 このことを念頭に、妥当な予想として、機能には、ドメイン sysvol 共有に最低 5 メガバイトの領域が必要になります。

**Q:DC エージェント ソフトウェアのインストールまたはアップグレードに再起動が必要なのはなぜですか?**

これはコア Windows 動作によって必要になります。

**Q:特定のプロキシ サーバーを使用するように DC エージェントを構成する方法はありますか?**

いいえ。 プロキシ サーバーはステートレスであるため、特定のどのプロキシ サーバーを使用するかは重要ではありません。

**Q:Azure AD Connect などの他のサービスと並行して Azure AD パスワード保護プロキシ サービスをデプロイしても大丈夫ですか?**

はい。 Azure AD パスワード保護プロキシ サービスと Azure AD Connect は、互いに直接競合することはありません。

残念ながら、Azure AD パスワード保護プロキシ ソフトウェアによってインストールされた Microsoft Azure AD Connect エージェント アップデーター サービスのバージョンと、[Azure Active Directory アプリケーション プロキシ](../manage-apps/application-proxy.md) ソフトウェアによってインストールされたサービスのバージョンとの間には互換性がありません。 この非互換性が原因で、エージェント アップデーター サービスがソフトウェアの更新のために Azure に接続できなくなっている可能性があります。 Azure AD パスワード保護プロキシと Azure Active Directory アプリケーション プロキシを同じマシンにインストールすることはお勧めしません。

**Q:DC エージェントとプロキシはどのような順序でインストールして登録する必要がありますか?**

プロキシ エージェントのインストール、DC エージェントのインストール、フォレストの登録、プロキシの登録は、任意の順序での実行がサポートされています。

**Q:この機能のデプロイによって、ドメイン コントローラーに対するパフォーマンスの打撃を懸念する必要がありますか?**

Azure AD パスワード保護 DC エージェント サービスは、既存の正常な Active Directory デプロイにおいてドメイン コントローラーのパフォーマンスに著しく影響を及ぼすことはないはずです。

ほとんどの Active Directory デプロイで、パスワード変更操作は、特定のどのドメイン コントローラーでもワークロード全体のごく一部になります。 たとえば、10000 ユーザー アカウントと 30 日に設定された MaxPasswordAge ポリシーがある Active Directory ドメインを想像してください。 平均で、このドメインでは毎日 10000/30 = 333 回までのパスワード変更操作が発生しますが、これは 1 つのドメイン コントローラーの場合でも少ない操作数です。 可能性のある最悪のケースのシナリオを考えてみます。1 時間に 1 つの DC でこの 333 回までのパスワードの変更が行われたとします。 たとえば、月曜日の朝に多数の従業員が全員出勤してくる場合に、このシナリオが発生する可能性があります。 この場合でも、333 回まで/60 分 = 1 分あたり 6 回のパスワードの変更が発生することになり、これも同様に重大な負荷ではありません。

ただし現在のドメイン コントローラーが既にパフォーマンス制限付きレベルで実行している (たとえば、CPU、ディスク領域、ディスク I/O に関して最大限に達しているなど) 場合は、この機能をデプロイする前に、ドメイン コン ローラーを追加するか、または使用可能なディスク領域を拡張することをお勧めします。 sysvol ディスク領域の使用量に関する上記の質問も参照してください。

**Q:ドメイン内のほんの一部の DC で Azure AD パスワード保護をテストしたいと思います。ユーザー パスワードの変更で、これらの特定の DC を強制的に使用させることはできますか?**

いいえ。 Windows クライアント OS は、ユーザーがパスワードを変更するときに、どのドメイン コントローラーを使用するかを制御します。 ドメイン コントローラーは、Active Directory サイトとサブネット割り当て、環境固有のネットワーク構成などの要素に基づいて選択されます。Azure AD パスワード保護は、これらの要素を制御せず、ユーザーのパスワードを変更するためにどのドメイン コントローラーが選択されるかに影響を与えることはできません。

この目標に部分的に到達する 1 つの方法は、特定の Active Directory サイト内のすべてのドメイン コントローラーに Azure AD パスワード保護をデプロイすることです。 このアプローチは、そのサイトに割り当てられる Windows クライアントに対し、したがってそれらのクライアントにログインし、パスワードを変更するユーザーに対しても、妥当なカバレッジを提供します。

**Q:プライマリ ドメイン コントローラー (PDC) にだけ Azure AD パスワード保護 DC エージェント サービスをインストールした場合、ドメイン内の他のすべてのドメイン コントローラーも保護されますか?**

いいえ。 PDC 以外の特定のドメイン コントローラーで、ユーザーのパスワードが変更された場合、クリアテキスト パスワードは PDC に送信されません (この考えは一般的な誤解です)。 特定の DC で、新しいパスワードが受け入れられると、その DC はそのパスワードを使用して、そのパスワードのさまざまな認証プロトコル固有のハッシュを作成し、ディレクトリでそれらのハッシュを保持します。 クリア テキスト パスワードは保持されません。 更新されたハッシュが、PDC にレプリケートされます。 場合により、ユーザー パスワードは、ネットワーク トポロジや Active Directory サイトの設計などのさまざまな要素に応じて、PDC で直接変更されることがあります。 (前の質問を参照してください。)

まとめると、PDC への Azure AD パスワード保護 DC エージェント サービスのデプロイでは、ドメイン全体で 100% の機能のセキュリティ カバレッジに到達する必要があります。 PDC のみへの機能のデプロイでは、ドメイン内の他の DC に対して、Azure AD パスワード保護のセキュリティ上の利点を提供しません。

**Q:オンプレミスの Active Directory 環境にエージェントをインストールした後でも、カスタム スマート ロックアウトが機能しないのはなぜですか?**

カスタム スマート ロックアウトは Azure AD でのみサポートされています。 エージェントがインストールされていても、オンプレミスの Active Directory 環境では、Azure AD ポータルでカスタム スマート ロックアウトの設定を変更しても環境への影響はありません。

**Q:Azure AD パスワード保護で、System Center Operations Manager 管理パックは使用できますか?**

いいえ。

**Q:ポリシーが監査モードになるよう構成した場合でも Azure AD が依然として脆弱なパスワードを拒否しているのはなぜですか?**

監査モードがサポートされるのは、オンプレミスの Active Directory 環境でのみです。 Azure AD は、パスワードを評価する際、暗黙的に常に "強制" モードになります。

**Q:パスワードが Azure AD パスワード保護によって拒否された場合、ユーザーには従来の Windows エラー メッセージが表示されます。このエラー メッセージをカスタマイズして、実際に発生したことをユーザーに知らせることはできますか?**

いいえ。 ドメイン コントローラーによってパスワードが拒否されたときにユーザーに表示されるエラー メッセージは、ドメイン コントローラーではなく、クライアント コンピューターによって制御されています。 この動作は、パスワードが既定の Active Directory パスワード ポリシーによって拒否されたか、または Azure AD パスワード保護などのパスワード フィルター ベースのソリューションによって拒否されたかにかかわらず、発生します。

## <a name="password-testing-procedures"></a>パスワードのテスト手順

ソフトウェアの適切な動作を検証するため、および[パスワード評価アルゴリズム](concept-password-ban-bad.md#how-are-passwords-evaluated)について理解を深めるために、さまざまなパスワードの基本的なテストを実行することが望ましい場合があります。 このセクションでは、反復可能な結果を生成することを目的とした、このようなテストの方法について概説します。

このような手順に従う必要があるのはなぜでしょうか? オンプレミスの Active Directory 環境には、パスワードの制御された反復可能なテストを行うことが困難になる要因がいくつかあります。

* パスワード ポリシーが Azure で構成および永続化され、ポリシーのコピーが、ポーリング メカニズムを使用して、オンプレミスの DC エージェントによって定期的に同期されます。 このポーリング サイクルに固有の待機時間が原因で混乱が発生する可能性があります。 たとえば、Azure でポリシーを構成しても、それを DC エージェントに同期することを忘れた場合、テストで期待どおりの結果が得られないことがあります。 ポーリング間隔は、1 時間に 1 回になるように現在ハードコーディングされていますが、ポリシーの変更間に 1 時間待機することは、対話型のテスト シナリオでは理想的ではありません。
* 新しいパスワード ポリシーがドメイン コントローラーに同期された後、それが他のドメイン コントローラーにレプリケートされるまで、さらに待機時間が発生します。 最新バージョンのポリシーをまだ受け取っていないドメイン コントローラーに対してパスワードの変更をテストした場合、これらの遅延が原因で、予期しない結果が発生する可能性があります。
* ユーザー インターフェイスを使用してパスワードの変更をテストした場合、結果の信頼性を確保することが難しくなります。 たとえば、ユーザー インターフェイスに無効なパスワードを誤入力することは容易に起こります。これは特に、ほとんどのパスワード ユーザー インターフェイスでユーザー入力が非表示になっているためです (たとえば、Windows の Ctrl + Alt + Delete -> [パスワードの変更] UI など)。
* ドメインに参加しているクライアントからパスワードの変更をテストするときに、どのドメイン コントローラーを使用するかを厳密に制御することはできません。 Windows クライアント OS は、Active Directory サイトとサブネットの割り当て、環境固有のネットワーク構成などの要因に基づいてドメイン コントローラーを選択します。

これらの問題を回避するために、下の手順は、ドメイン コントローラーにログインしているときのパスワード リセットのコマンドライン テストに基づいています。

> [!WARNING]
> これらの手順はテスト環境でのみ使用する必要があります。これは、DC エージェント サービスが停止している間、受信するすべてのパスワードの変更とリセットが検証されずに受け入れられてしまうため、およびドメイン コントローラーへのログインに固有のリスクが増加するのを避けるためです。

次の手順は、DC エージェントが少なくとも 1 つのドメイン コントローラーにインストールされていること、少なくとも 1 つのプロキシがインストールされていること、およびプロキシとフォレストの両方が登録されていることを前提としています。

1. DC エージェント ソフトウェアがインストールされ、再起動されたドメイン コントローラーに、ドメイン管理者の資格情報 (または、テスト ユーザー アカウントを作成し、パスワードをリセットするための十分な特権を持つその他の資格情報) を使用してログオンします。
1. イベント ビューアーを開いて、[DC エージェント管理イベント ログ](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log)に移動します。
1. 昇格した [コマンド プロンプト] ウィンドウを開きます。
1. パスワード テストを実行するためのテスト アカウントを作成します。

   ユーザー アカウントを作成する方法は多数ありますが、反復的なテスト サイクル中にこれを簡単に行う方法として、コマンドライン オプションが提供されています。

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   下の説明のために、"ContosoUser" という名前のテスト アカウントを作成したと仮定します。次に例を示します。

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Web ブラウザーを開き (ドメイン コントローラーではなく、別のデバイスを使用する必要がある場合があります)、[Azure portal](https://portal.azure.com) にサインインし、[Azure Active Directory] > [セキュリティ] > [認証方法] > [パスワード保護] を参照します。
1. 実行するテストに対して、必要に応じて Azure AD パスワード保護ポリシーを変更します。  たとえば、強制または監査のモードを構成する場合や、カスタムの禁止パスワード リストの禁止語句のリストを変更する場合があります。
1. DC エージェント サービスを停止および再起動して、新しいポリシーを同期します。

   この手順は、さまざまな方法で実行できます。 1 つの方法は、[Azure AD パスワード保護 DC エージェント サービス] を右クリックし、[再起動] を選択することで、サービス管理の管理コンソールを使用することです。 もう 1 つの方法では、次のようにコマンド プロンプト ウィンドウから実行できます。

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. イベント ビューアーを確認して、新しいポリシーがダウンロードされていることを確認します。

   DC エージェント サービスが停止して開始されるたびに、連続して発行された 2 つの 30006 イベントが表示されます。 1 番目の 30006 イベントには、sysvol 共有のディスクにキャッシュされたポリシーが反映されます。 2 番目の 30006 イベント (存在する場合) では、テナント ポリシーの日付が更新されており、その場合、Azure からダウンロードされたポリシーが反映されます。 テナント ポリシーの日付値は、ポリシーが Azure からダウンロードされたおおよそのタイムスタンプを示すように現在コーディングされています。
   
   2 番目の 30006 イベントが表示されない場合は、続行する前に問題をトラブルシューティングする必要があります。
   
   30006 イベントは、この例のようになります。
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   たとえば、強制と監査のモードを切り替えると、AuditOnly フラグが変更されます (AuditOnly=0 が設定されている上のポリシーは強制モードです)。カスタムの禁止パスワード リストに対する変更は、上の 30006 イベントには直接的には反映されません (また、セキュリティ上の理由から、他のどこにもログされません)。 このような変更の後に Azure からポリシーを正常にダウンロードした場合は、変更されたカスタムの禁止パスワード リストも含まれます。

1. テスト ユーザー アカウントの新しいパスワードのリセットを試行することで、テストを実行します。

   この手順は、コマンド プロンプト ウィンドウから次のように実行できます。

   ```text
   net.exe user ContosoUser <password>
   ```

   コマンドを実行した後、イベント ビューアーを確認することで、コマンドの結果に関する詳細情報を得ることができます。 パスワード検証結果イベントについては、「[DC エージェント管理イベント ログ](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log)」というトピックを参照してください。net.exe コマンドからの対話型出力に加えて、このようなイベントを使用して、テストの結果を検証します。

   例を試してみましょう。Microsoft グローバル リストで禁止されているパスワードを設定しようとしています (このリストは[ドキュメント化されていません](concept-password-ban-bad.md#global-banned-password-list)が、ここでは既知の禁止語句に対してテストすることができます)。 この例では、ポリシーを強制モードに構成し、カスタムの禁止パスワード リストに 0 個の語句を追加したことを前提としています。

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   ドキュメントによると、このテストはパスワード リセット操作であったため、ContosoUser ユーザーに対して 10017 および 30005 イベントが表示されます。

   10017 イベントは、この例のようになります。

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30005 イベントは、この例のようになります。

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   以上です。別の例を試してみましょう。 今回は、ポリシーが監査モードのときに、カスタムの禁止リストによって禁止されているパスワードを設定してみます。 この例は、次の手順を実行したことを前提としています。ポリシーを監査モードに構成し、カスタムの禁止パスワード リストに "lachrymose" という語句を追加し、上記のように、DC エージェント サービスを停止して開始することで、結果の新しいポリシーをドメイン コントローラーに同期しました。

   次に、禁止パスワードのバリエーションを設定します。

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   今回は、ポリシーが監査モードであるために成功したということを覚えておいてください。 ContosoUser ユーザーに対して 10025 および 30007 イベントが表示されます。

   10025 イベントは、この例のようになります。
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30007 イベントは、この例のようになります。

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. 前のステップで説明した手順を使用して、引き続き任意のさまざまなパスワードをテストし、イベント ビューアーで結果を確認します。 Azure portal でポリシーを変更する必要がある場合は、前に説明したように、新しいポリシーを DC エージェントに同期することを忘れないでください。

Azure AD パスワード保護のパスワード検証動作の制御されたテストを実行できるようにする手順について説明しました。 ドメイン コントローラーでコマンド ラインからユーザー パスワードを直接リセットすることは、このようなテストを実行するための特殊な手段のように思われるかもしれませんが、前述のように、この目的は反復可能な結果を生成することです。 さまざまなパスワードをテストする際には、[パスワード評価アルゴリズム](concept-password-ban-bad.md#how-are-passwords-evaluated)を念頭に置いてください。これは、予期しない結果を説明するのに役立つ場合があるためです。

> [!WARNING]
> すべてのテストが完了したら、テストのために作成したユーザー アカウントを忘れずに削除してください。

## <a name="additional-content"></a>その他のコンテンツ

次のリンクは、主要な Azure AD パスワード保護ドキュメントに含まれませんが、機能の有用な追加の情報源になる場合があります。

[Azure AD Password Protection is now generally available! (Azure AD パスワード保護の一般提供が開始されました)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15:Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/) (電子メールのフィッシング保護ガイド – パート 15: Microsoft Azure AD パスワード保護サービス (オンプレミスの場合も))

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529) (Azure AD パスワード保護およびスマート ロックアウトがパブリック プレビュー段階になりました)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified サポート トレーニングの提供開始

Azure AD パスワード保護の詳細と環境へのデプロイに興味がある場合は、Premier または Unified サポート契約のお客様が利用可能なマイクロソフト事前対応型サービスを利用できます。 サービスは、Azure Active Directory:パスワード保護と呼ばれます。 詳細については、テクニカル アカウント マネージャーにお問い合わせください。

## <a name="next-steps"></a>次のステップ

ここでは回答されていないオンプレミスの Azure AD パスワード保護に関するご質問がある場合、以下のフィードバック項目を送信してください。

[Azure AD のパスワード保護をデプロイする](howto-password-ban-bad-on-premises-deploy.md)