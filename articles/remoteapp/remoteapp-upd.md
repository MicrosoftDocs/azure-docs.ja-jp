---
title: "Azure RemoteApp によるユーザー データと設定の保存方法 | Microsoft Docs"
description: "Azure RemoteApp によりユーザー プロファイル ディスクを使用してユーザー データを保存する方法を説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: e125af62-5c1a-4186-a238-52f537f7bb34
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dd281ead7d2c06af98ea0baea31d897b5be0d9d7
ms.openlocfilehash: 04d8031a4c5f06d56afc1d08d4a4fa5fb50f8a25
ms.lasthandoff: 02/16/2017


---
# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Azure RemoteApp によるユーザー データと設定の保存方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp では、デバイスとセッションにわたってユーザー ID とカスタマイズが保存されます。 このユーザー データは、ユーザー プロファイル ディスク (UPD) と呼ばれる、ユーザー/コレクションごとのディスクに格納されます。 このディスクはユーザーを監視し、ユーザーがサインインしている場所に関係なく、一貫したユーザー エクスペリエンスを実現します。

ユーザー プロファイル ディスクはユーザーに対して透過的です。ユーザーは、ドキュメントを自身の [ドキュメント] フォルダー (ローカル ドライブのように見える場所にあります) に保存し、アプリの設定を通常どおりに変更できます。 同時に、任意のデバイスから Azure RemoteApp に接続するとき、すべての個人設定が保持されます。 ユーザーには、自身のデータすべてが同じ場所に表示されます。

各 UPD には 50 GB の永続的なストレージがあり、ユーザー データとアプリケーションの両方の設定が格納されています。 

ユーザー プロファイル データの詳細については、次をお読みください。

> [!NOTE]
> UPD を無効にする必要がありますか。 UPD を無効にできるようになりました。詳細については、[Azure RemoteApp でのユーザー プロファイル ディスク (UPD) の無効化](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)に関するページを参照してください。
> 
> 

## <a name="how-can-an-admin-get-to-the-data"></a>管理者がデータにアクセスするには、どうすればよいですか。
いずれかのユーザーのデータにアクセスする必要がある場合は (障害復旧やユーザーの退職時)、サポートに連絡して、コレクションとユーザー ID のサブスクリプション情報を伝えます。 Azure RemoteApp チームによって、VHD の URL が提供されます。 VHD をダウンロードし、必要なドキュメントやファイルを取得します。 VHD は 50 GB あるため、ダウンロードに少し時間がかかることに注意してください。

## <a name="is-the-data-backed-up"></a>データはバックアップされていますか。
はい。ユーザー データのバックアップは、地理的な場所ごとに保存されています。 バックアップ データは読み取り専用です。プライマリ データ センターがダウンしている場合、このデータにアクセスするには、通常のデータと同じ方法を使用します (Azure RemoteApp を使用)。 データはリアルタイムでバックアップ場所にコピーされ、複数のバージョンが保存されることはありません。 そのため、データの破損時に正しいことがわかっている以前のバージョンを復元することはできませんが、プライマリ データ センターがダウンした場合は他の場所からユーザー データを取得できます。

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>サーバーではユーザーの UPD はどのようにユーザーに表示されますか。
サーバーにはユーザーごとにユーザー自身のディレクトリが作成され、そのユーザーの UPD (c:\Users\username) にマップされます。

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>Outlook と UPD を最善の方法で使用するには、どうすればよいですか。
Azure RemoteApp では、セッション間で Outlook の状態 (メールボックス、PST) が保存されます。 これを有効にするには、PST をユーザー プロファイル データ (c:\users\<username>) に格納する必要があります。 これはデータの既定の場所なので、この場所を変更しない限り、データはセッション間で保持されます。

また、Outlook では "キャッシュ" モードを使用し、検索には "サーバー/オンライン" モードを使用することをお勧めします。

Outlook および Azure RemoteApp の使用方法の詳細については、 [この記事](remoteapp-outlook.md) を参照してください。

## <a name="what-about-redirection"></a>リダイレクトはいかがですか。
[リダイレクト](remoteapp-redirection.md)を設定することで、ユーザーがローカル デバイスにアクセスできるように Azure RemoteApp を構成できます。 これにより、ローカル デバイスが UPD のデータにアクセスできるようになります。

## <a name="can-i-use-my-upd-as-a-network-share"></a>UPD をネットワーク共有として使用できますか。
いいえ。 UPD は、ネットワーク共有として使用できません。 UPD は、ユーザーが Azure RemoteApp にアクティブに接続されている場合にのみ使用できます。

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>コレクションからユーザーを削除すると、そのユーザーの UPD も削除されますか。
いいえ。ユーザーを削除しても、UPD が自動的に削除されることはありません。データは、コレクションを削除するまで保存されます。 コレクションを削除してから 90 日が経過すると、すべての UPD が削除されます。 

コレクションから UPD を削除する必要がある場合は、Azure RemoteApp にご連絡ください。UPD は、こちらで削除いたします。

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>ユーザー (現在のユーザーまたは削除されたユーザー) の UPD にアクセスできますか。
はい、できます。[Azure RemoteApp](mailto:remoteappforum@microsoft.com) にご連絡いただければ、データにアクセスするための URL を設定します。 約 10 時間後にアクセスの有効期限が切れるので、その前に UPD からデータまたはファイルをダウンロードしてください。

## <a name="are-upds-available-offline"></a>オフラインで UPD を利用できますか。
現時点では、上記で説明した 10 時間を超えて UPD にオフライン アクセスすることはできません。 つまり、現在アクセスできる時間では、より複雑なタスクを実行できません。たとえば、UPD でウイルス対策ソフトウェアを実行したり、監査のためにデータにアクセスしたりすることはできません。

## <a name="do-registry-key-settings-persist"></a>レジストリ キーの設定は永続化されますか。
はい。HKEY_Current_User に書き込まれたものはすべて UPD の一部になります。

## <a name="can-i-disable-upds-for-a-collection"></a>コレクションに対して UPD を無効にできますか。
はい、サブスクリプションの UPD を無効にするように Azure RemoteApp に依頼できます。ただし、これをご自身で行うことはできません。 これは、サブスクリプション内のすべてのコレクションに対して UPD が無効になることを意味します。

UPD を無効にする必要があるのは、次のような場合です。 

* ユーザー データへの完全なアクセスと制御が必要な場合 (金融機関など、監査やレビューの目的のため)。
* サードパーティのユーザー プロファイル管理ソリューションがオンプレミスにあり、ドメインに参加している Azure RemoteApp デプロイでその使用を続ける必要がある場合。 この場合、プロファイル エージェントがゴールド イメージに読み込まれている必要があります。 
* 任意のローカル データ ストレージが不要な場合、またはクラウドにすべてのデータがあったり、ファイル共有のため、Azure RemoteApp を使用してデータのローカルへの保存を制御する必要がある場合。

詳細については、[Azure RemoteApp でのユーザー プロファイル ディスク (UPD) の無効化](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)に関するページを参照してください。

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>ユーザーによるシステム ドライブへのデータ保存を制限できますか。
はい。ただし、これは、コレクションを作成する前に、テンプレート イメージで設定する必要があります。 システム ドライブへのアクセスをブロックするには、次の手順を実行します。

1. テンプレート イメージで **gpedit.msc** を実行します。
2. **[ユーザーの構成]、[管理用テンプレート]、[Windows コンポーネント]、[エクスプローラー]** の順に移動します。
3. 次のオプションを選択します。
   * **指定したドライブを [マイ コンピューター] 内で非表示にする**
   * **[マイ コンピューター] からドライブにアクセスできないようにする**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>UPD をシードできますか。 ユーザーが初めてログインするときに使用できる UPD にデータを配置する必要があります。
はい。テンプレート イメージを作成するときに、既定のプロファイルに情報を追加できます。 その情報は、その後 UPD に追加されます。

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>格納するデータの量に応じて、UPD のサイズを変更できますか。
いいえ。すべての UPD に 50 GB のストレージが用意されています。 異なる量のデータを格納する必要がある場合は、次の手順を実行します。

1. コレクションの UPD を無効にします。
2. ユーザーがアクセスするファイル共有を設定します。
3. スタートアップ スクリプトを使用して、ファイル共有を読み込みます。 Azure RemoteApp のスタートアップ スクリプトの詳細については、以下を参照してください。
4. すべてのデータをファイル共有に保存するようユーザーに指示します。

## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>Azure RemoteApp でスタートアップ スクリプトを実行するには、どうすればよいですか。
スタートアップ スクリプトを実行するには、まず、コレクションに使用するテンプレート イメージで、スケジュールされたタスクを作成します (この操作は、sysprep を実行する前に行います)。 ** 

![システム タスクの作成](./media/remoteapp-upd/upd1.png)

![ユーザーがログオンするときに実行されるシステム タスクを作成します](./media/remoteapp-upd/upd2.png)

**[全般]** タブで、[セキュリティ] の下の **[ユーザー アカウント]** を "BUILTIN\Users" に必ず変更してください。

![ユーザー アカウントのグループへの変更](./media/remoteapp-upd/upd4.png)

スケジュールされたタスクでは、ユーザーの資格情報を使用してスタートアップ スクリプトが実行されます。 ユーザーがログオンするたびに実行されるように、タスクのスケジュールを設定します。

![タスクのトリガーを "ログオン時" として設定します](./media/remoteapp-upd/upd3.png)

[グループ ポリシー ベースのスタートアップ スクリプト](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx)を使用することもできます。 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>スタートアップ スクリプトを [スタート] メニューに配置するのはいかがでしょうか。 動作しますか。
つまり、構成ウィンドウ スクリプトを実行する .bat ファイルを作成し、c:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp フォルダーに保存して、ユーザーが RemoteApp セッションを開始するたびに、そのスクリプトが実行されるようにできますか。

いいえ。その操作は、RDSH を使用する Azure RemoteApp ではサポートされていません。RDSH でも、[スタート] メニューのスタートアップ スクリプトはサポートされていません。

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>mstsc.exe (リモート デスクトップ プログラム) を使用して、ログオン スクリプトを構成できますか。
いいえ。Azure RemoteApp ではサポートされません。

## <a name="can-i-store-data-on-the-vm-locally"></a>データを VM にローカルに保存することはできますか。
いいえ、VM 上の UPD 以外の場所に保存されたデータは失われます。 ユーザーが Azure RemoteApp に次回にサインインしたとき、同じ VM が取得されない可能性が高まります。 ユーザーと VM の永続性は維持されないため、ユーザーは同じ VM にサインインせず、データも失われます。 さらに、コレクションの更新時に、既存の VM は VM の新しいセットに置き換えられます。これは、VM 自体に格納されたすべてのデータが失われることを意味します。 UPD や共有ストレージ (Azure Files など)、VNET 内のファイル サーバー、クラウド ストレージ システム (DropBox など) に保存しておくことをお勧めします。

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>PowerShell を使用して VM に Azure File 共有をマウントする方法
以下のように、Net-PSDrive コマンドレットを使用して、ドライブをマウントできます。

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


また、以下を実行して、資格情報を保存することもできます。

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


これにより、New-PSDrive コマンドレットの -Credential パラメーターを省略できます。


