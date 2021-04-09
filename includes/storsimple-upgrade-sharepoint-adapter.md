---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3467a5d5daa300f82c7b81641ab7e262259d9285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95556430"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>SharePoint 2010 を SharePoint 2013 にアップグレードして SharePoint 用 StorSimple アダプターをインストールする
> [!IMPORTANT]
> 以前に RBS 経由で外部ストレージに移動されたファイルは、アップグレードが完了し、RBS 機能を再び有効にするまでは使用できません。 ユーザーへの影響を限定するために、計画されたメンテナンス期間にアップグレードまたは再インストールを実行してください。
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>SharePoint 2010 を SharePoint 2013 にアップグレードしてアダプターをインストールするには
1. SharePoint 2010 ファームで、外部化された BLOB の BLOB ストア パスと RBS が有効にされているコンテンツ データベースをメモします。 
2. 新しい SharePoint 2013 ファームをインストールして構成します。 
3. データベース、アプリケーション、およびサイト コレクションを SharePoint 2010 ファームから新しい SharePoint 2013 ファームに移動します。 手順については、「 [SharePoint 2013 へのアップグレード プロセスの概要](/SharePoint/upgrade-and-update/overview-of-the-upgrade-process)」を参照してください。
4. 新しいファームに SharePoint 用 StorSimple アダプターをインストールします。 手順については、「 [SharePoint 用 StorSimple アダプターをインストールする](#install-the-storsimple-adapter-for-sharepoint) 」を参照してください。
5. 手順 1. でメモした情報を使用して、同じコンテンツ データベース セットの RBS を有効にし、SharePoint 2010 のインストール時に使用したのと同じ BLOB ストア パスを指定します。 手順については、「[RBS の構成](#configure-rbs)」を参照してください。 この手順を完了すると、あらかじめ外部化されているファイルが新しいファームからアクセス可能になります。 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>SharePoint 用 StorSimple アダプターをアップグレードする
> [!IMPORTANT]
> このアップグレードは、以下の理由で、計画的なメンテナンス期間中に実行されるようスケジュールする必要があります。
> 
> * あらかじめ外部化されているコンテンツは、アダプターを再インストールするまで使用できません。
> * SharePoint 用 StorSimple アダプターの以前のバージョンをアンインストールしてから新しいバージョンをインストールするまでにサイトにアップロードされたコンテンツは、コンテンツ データベースに格納されます。 そのコンテンツは、新しいアダプターをインストールした後、StorSimple デバイスに移動する必要があります。 コンテンツの移行は、SharePoint に用意されている Microsoft `RBS Migrate()` PowerShell コマンドレットを使用して行うことができます。 詳細については、「 [コンテンツをリモート BLOB ストレージ (RBS) 内または RBS 外に移行する (SharePoint Foundation 2010)](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))」を参照してください。 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>SharePoint 用 StorSimple アダプターをアップグレードするには
1. SharePoint 用 StorSimple アダプターの以前のバージョンをアンインストールします。
   
   > [!NOTE]
   > これにより、コンテンツ データベースの RBS が自動的に無効になります。 ただし、既存の BLOB は StorSimple デバイスに残ります。 RBS は無効になり、BLOB はコンテンツ データベースに移行されていないため、これらの BLOB に対する要求はいずれも失敗します。 
   > 
   > 
2. 新しい SharePoint 用 StorSimple アダプターをインストールします。 新しいアダプターは、以前 RBS に対して有効または無効にされたコンテンツ データベースを自動的に認識し、以前の設定を使用します。