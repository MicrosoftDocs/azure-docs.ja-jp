---
title: 既存の ADSync データベースを使用して Azure AD Connect をインストールする | Microsoft Docs
description: このトピックでは、既存の ADSync データベースを使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8324b82a05d7e78772e0b0b6de3a9bfaa183411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91265393"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>既存の ADSync データベースを使用して Azure AD Connect をインストールする
Azure AD Connect には、データを格納する SQL Server データベースが必要です。 Azure AD Connect と共にインストールされる既定の SQL Server 2012 Express LocalDB を使用するか、所有している完全バージョンの SQL を使用することができます。 以前は、Azure AD Connect をインストールしたときに、ADSync という新しいデータベースが常に作成されました。 Azure AD Connect バージョン 1.1.613.0 (以降) では、既存の ADSync データベースを指定して、Azure AD Connect をインストールするオプションがあります。

## <a name="benefits-of-using-an-existing-adsync-database"></a>既存の ADSync データベースを使用する利点
既存の ADSync データベースを指定することで、次の利点があります。

- 資格情報情報を除き、ADSync データベースに格納されている同期構成 (カスタム同期規則、コネクタ、フィルター処理、オプションの機能構成など) が、インストール時に自動的に復元され、使用されます。 Azure AD Connect が変更をオンプレミス AD および Azure AD と同期するために使用される資格情報は暗号化され、前の Azure AD Connect サーバーからのみアクセスできます。
- ADSync データベースに格納されているすべての ID データ (コネクタ空間とメタバースに関連付けられている ID データ) と同期 Cookie も復元されます。 新しくインストールされた Azure AD Connect サーバーは、前の Azure AD Connect サーバーが最後に同期した時点から継続して同期できます。完全な同期を実行する必要はありません。

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>既存の ADSync データベースを使用する方が役に立つシナリオ
これらの利点は、次のシナリオで役立ちます。


- 既存の Azure AD Connect デプロイがあります。 既存の Azure AD Connect サーバーは動作しなくなっていますが、ADSync データベースを含む SQL サーバーはまだ動作しています。 新しい Azure AD Connect サーバーをインストールし、既存の ADSync データベースを指定します。 
- 既存の Azure AD Connect デプロイがあります。 ADSync データベースを含む SQL サーバーは動作しなくなっています。 ただし、データベースの最新のバックアップがあります。 最初に、ADSync データベースを新しい SQL サーバーに復元できます。 その後、新しい Azure AD Connect サーバーをインストールし、復元された ADSync データベースを指定します。
- LocalDB を使用している既存の Azure AD Connect デプロイがあります。 LocalDB の上限は 10 GB なので、完全な SQL に移行できます。 LocalDB から ADSync データベースをバックアップし、SQL サーバーに復元することができます。 その後、新しい Azure AD Connect サーバーを再インストールし、復元された ADSync データベースを指定することができます。
- ステージング サーバーを設定し、構成を現在のアクティブ サーバーと同じ構成にしたいと考えています。 ADSync データベースをバックアップし、別の SQL サーバーに復元できます。 その後、新しい Azure AD Connect サーバーを再インストールし、復元された ADSync データベースを指定することができます。

## <a name="prerequisite-information"></a>前提条件情報

次に進む前に気を付ける必要がある重要な注意事項があります。

- ハードウェアと必須コンポーネントに Azure AD Connect をインストールするための前提条件と、Azure AD Connect のインストールに必要なアカウントとアクセス許可を確認します。 "既存のデータベースを使用する" モードを使用して Azure AD Connect をインストールするために必要なアクセス許可は、"カスタム" インストールと同じです。
- 既存の ADSync データベースに対する Azure AD Connect のデプロイは、完全バージョンの SQL でのみサポートされています。 SQL Express LocalDB ではサポートされていません。 使用する LocalDB に既存の ADSync データベースがある場合は、まず ADSync データベース (LocalDB) をバックアップし、完全バージョンの SQL に復元する必要があります。 その後、この方法で復元されたデータベースに対して Azure AD Connect をデプロイします。
- インストールに使用される Azure AD Connect のバージョンは、次の条件を満たす必要があります。
    - 1.1.613.0 以降かつ
    - ADSync データベースで最後に使用されていた Azure AD Connect のバージョン以降。 インストールに使用された Azure AD Connect バージョンが ADSync データベースで最後に使用されていたバージョンよりも新しい場合、完全な同期が必要になる可能性があります。  2 つのバージョン間でスキーマまたは同期規則の変更があった場合、完全な同期が必要です。 
- 使用する ADSync データベースには、比較的新しい同期状態が含まれています。 既存の ADSync データベースとの最後の同期アクティビティは、過去 3 週間以内に実行されているはずです。
- "既存のデータベースを使用する" 方法で Azure AD Connect をインストールする場合、以前の Azure AD Connect サーバーに構成されているサインイン方法は維持されません。 さらに、インストール時はサインイン方法を構成できません。 インストールが完了した後にのみ、サインイン方法を構成できます。
- 複数の Azure AD Connect サーバーが同じ ADSync データベースを共有することはできません。 "既存のデータベースを使用する" 方法では、新しい Azure AD Connect サーバーに既存の ADSync データベースを再利用できます。 共有はサポートしていません。

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>"既存のデータベースを使用する" モードで Azure AD Connect をインストールする手順
1.  Azure AD Connect インストーラー (AzureADConnect.MSI) を Windows サーバーにダウンロードします。 Azure AD Connect インストーラーをダブルクリックして、Azure AD Connect のインストールを開始します。
2.  MSI のインストールが完了すると、Azure AD Connect ウィザードが簡易モードの設定で開始されます。 [終了] アイコンをクラスター リソースして画面を閉じます。
![左側のメニューの [Express 設定] が強調表示された [Azure AD Connect へようこそ] ページを示すスクリーンショット。](./media/how-to-connect-install-existing-database/db1.png)
3.  新しいコマンド プロンプトまたは PowerShell セッションを開始します。 "C:\Program Files\Microsoft Azure Active Directory Connect" フォルダーに移動します。 コマンド .\AzureADConnect.exe /useexistingdatabase を実行して、"既存のデータベースを使用する" 設定モードで Azure AD Connect ウィザードを開始します。

> [!NOTE]
> データベースに既に、以前の Azure AD Connect のインストールからのデータが含まれる場合のみ、 **/UseExistingDatabase** スイッチを使用します。 たとえば、ローカル データベースから完全な SQL Server データベースに移行しようとしている場合や、Azure AD Connect サーバーが再構築されて、Azure AD Connect の以前のインストールから ADSync データベースの SQL バックアップを復元した場合です。 データベースが空の場合、つまり以前の Azure AD Connect インストールからのデータがデータベースに含まれていない場合は、この手順をスキップしてください。

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. [Azure AD Connect へようこそ] 画面が表示されます。 ライセンス条項とプライバシーに関する声明に同意したら、 **[続行]** をクリックします。
   ![[Azure AD Connect へようこそ] ページを示すスクリーンショット](./media/how-to-connect-install-existing-database/db3.png)
1. **[必須コンポーネントのインストール]** 画面で **[既存の SQL Server を使用する]** オプションをオンにします。 ADSync データベースをホストしている SQL サーバーの名前を指定します。 ADSync データベースのホストに使用されている SQL エンジン インスタンスが SQL サーバーで既定のインスタンスではない場合、SQL エンジン インスタンス名を指定する必要があります。 さらに、SQL の参照が有効ではない場合、SQL エンジン インスタンスのポート番号も指定する必要があります。 次に例を示します。         
   ![[必須コンポーネントのインストール] ページを示すスクリーンショット。](./media/how-to-connect-install-existing-database/db4.png)           

1. **[Azure AD に接続]** 画面で、Azure AD ディレクトリのグローバル管理者の資格情報を指定する必要があります。 既定の onmicrosoft.com ドメインでアカウントを使用することをお勧めします。 このアカウントは、Azure AD のサービス アカウントを作成するためにのみ使用され、ウィザードが完了した後は使用されません。
   ![のインスタンスに接続するときには、](./media/how-to-connect-install-existing-database/db5.png)
 
1. **[ディレクトリの接続]** 画面では、ディレクトリ同期に構成されている既存の AD フォレストは、赤色の×アイコンで表示されます。 オンプレミスの AD フォレストの変更を同期するには、AD DS アカウントが必要です。 Azure AD Connect ウィザードでは、ADSync データベースに格納されている AD DS アカウントの資格情報を取得できません。これは、資格情報が暗号化され、復号には前の Azure AD Connect サーバーが必要なためです。 **[資格情報の変更]** をクリックして、AD フォレストの AD DS アカウントを指定します。
   ![Directories](./media/how-to-connect-install-existing-database/db6.png)
 
1. ポップアップ ダイアログで、(i) エンタープライズ管理者の資格情報を指定して Azure AD Connect に AD DS アカウントの作成を任せるか、(ii) AD DS アカウントを自分で作成してその資格情報を Azure AD Connect に提供することができます。 オプションを選択し、必要な資格情報を指定したら、 **[OK]** をクリックしてポップアップ ダイアログを閉じます。
   ![[新しい AD アカウントを作成] が選択されている [AD フォレスト アカウント] ポップアップ ダイアログのスクリーンショット。](./media/how-to-connect-install-existing-database/db7.png)
 
1. 資格情報を入力すると、赤色の×アイコンは緑色のチェック アイコンで置き換えられます。 **[次へ]** をクリックします。
   ![[ディレクトリの接続] ページを示すスクリーンショット。](./media/how-to-connect-install-existing-database/db8.png)
 
1. **[構成の準備完了]** 画面で、 **[インストール]** をクリックします。
   ![ようこそ](./media/how-to-connect-install-existing-database/db9.png)
 
1. インストールが完了すると、Azure AD Connect サーバーがステージング モードで自動的に有効になります。 ステージング モードを無効にする前に、予期しない変更に備えてサーバー構成と保留中のエクスポートを確認することをお勧めします。 

## <a name="post-installation-tasks"></a>インストール後のタスク
1\.2.65.0 より前のバージョンの Azure AD Connect によって作成されたデータベースのバックアップを復元するとき、 **[構成しない]** サインイン方法がステージング サーバーによって自動的に選択されます。 パスワード ハッシュ同期とパスワード ライトバックの設定は復元されますが、アクティブな同期サーバーに有効な他のポリシーに一致するようにサインイン方法を後で変更する必要があります。  これらの手順を完了しないと、このサーバーがアクティブになった場合にユーザーがサインインできなくなる可能性があります。  

次の表を使用して、必要な追加の手順を確認してください。

|機能|手順|
|-----|-----|
|パスワード ハッシュの同期| Azure AD Connect バージョン 1.2.65.0 より、パスワード ハッシュの同期とパスワード ライトバックの設定は完全に復元されます。  これより前のバージョンの Azure AD Connect を使用して復元する場合は、これらの機能の同期オプションの設定がアクティブな同期サーバーと一致することを確認してください。  他の構成手順は必要ありません。|
|AD FS とのフェデレーション|お使いのアクティブな同期サーバー用に構成された AD FS ポリシーが Azure 認証で引き続き使用されます。  Azure AD Connect を使用して AD FS ファームを管理する場合は、必要に応じて、スタンバイ サーバーがアクティブな同期インスタンスになるための準備として、サインイン方法を AD FS フェデレーションに変更することもできます。   デバイス オプションがアクティブな同期サーバーで有効な場合は、"デバイス オプションの構成" タスクを実行してこのサーバーでこれらのオプションを構成します。|
|パススルー認証およびデスクトップ シングル サインオン|お使いのアクティブな同期サーバーの構成に合わせてサインイン方法を更新します。  サーバーをプライマリに昇格させる前にこの作業を実行しないと、パススルー認証とシームレス シングル サインオンが無効になり、バックアップ サインイン オプションとしてパスワード ハッシュの同期を設定していないときにテナントがロックアウトされる可能性があります。 また、ステージング モードでパススルー認証を有効にすると、新しい認証エージェントがインストールおよび登録され、サインイン要求を受け入れる高可用性エージェントとして実行されることに注意してください。|
|PingFederate によるフェデレーション|お使いのアクティブな同期サーバー用に構成された PingFederate ポリシーが Azure 認証で引き続き使用されます。  必要に応じて、スタンバイ サーバーがアクティブな同期インスタンスになるための準備として、サインイン方法を PingFederate に変更することもできます。  この手順は、PingFederate で追加のドメインをフェデレーションする必要があるときまで延期することができます。|

## <a name="next-steps"></a>次のステップ

- Azure AD Connect がインストールされたので、[インストールを確認し、ライセンスを割り当てる](how-to-connect-post-installation.md)ことができます。
- インストールの結果有効になった機能については、[誤った削除操作を防止する機能](how-to-connect-sync-feature-prevent-accidental-deletes.md)と [Azure AD Connect Health](how-to-connect-health-sync.md) に関する各ページを参照してください。
- 一般的なトピックについては、[スケジューラの使用と同期のトリガー方法](how-to-connect-sync-feature-scheduler.md)に関するページを参照してください。
- 「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
