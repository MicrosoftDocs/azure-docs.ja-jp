---
title: Windows Virtual Desktop の環境 - Azure
description: Windows Virtual Desktop 環境のホスト プールやアプリ グループなどの基本的な要素について説明します。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a07dd1791b539ea44fcbab250aa9c227ee1705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88002619"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Virtual Desktop の環境

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/environment-setup-2019.md)を参照してください。

Windows Virtual Desktop は、仮想デスクトップと RemoteApp への簡単で安全なアクセスをユーザーに提供するサービスです。 このトピックでは、Windows Virtual Desktop 環境の一般構造について少し詳しく説明します。

## <a name="host-pools"></a>ホスト プール

ホスト プールとは、Windows Virtual Desktop エージェントを実行するときに Windows Virtual Desktop にセッション ホストとして登録する Azure 仮想マシンのコレクションです。 ホスト プール内のすべてのセッション ホスト仮想マシンは、一貫したユーザー エクスペリエンスを実現するために同じイメージから供給される必要があります。

ホスト プールには以下の 2 種類があります。

- 個人用。各セッション ホストが個々のユーザーに割り当てられています。
- プール。セッション ホストは、ホスト プール内のアプリ グループに承認されたユーザーからの接続を受け入れることができます。

ホスト プールに追加のプロパティを設定して、負荷分散の動作、各セッション ホストが取得できるセッション数、およびユーザーが Windows Virtual Desktop セッションへのサインイン時にホスト プール内のセッション ホストに対して実行できる操作を変更できます。 ユーザーに公開されるリソースは、アプリ グループを通じて制御します。

## <a name="app-groups"></a>アプリ グループ

アプリ グループは、ホスト プール内のセッション ホストにインストールされているアプリケーションの論理グループです。 アプリ グループには以下の 2 種類があります。

- RemoteApp。ユーザーは、個別に選択されてアプリ グループに公開された RemoteApp にアクセスします
- デスクトップ。ユーザーは完全なデスクトップにアクセスします

既定では、ホスト プールを作成するたびに、デスクトップ アプリ グループ ("デスクトップ アプリケーション グループ" という名前) が自動的に作成されます。 このアプリ グループはいつでも削除できます。 ただし、デスクトップ アプリ グループが存在する間は、ホスト プールに別のデスクトップ アプリ グループを作成できません。 RemoteApp を公開するには、RemoteApp アプリ グループを作成する必要があります。 複数の RemoteApp アプリ グループを作成してさまざまな worker シナリオに対応することができます。 別の RemoteApp アプリ グループに重複する RemoteApp を含めることもできます。

ユーザーにリソースを公開するには、それらをアプリ グループに割り当てる必要があります。 ユーザーをアプリ グループに割り当てるときは、以下の事項を検討してください。

- 1 人のユーザーを同じホスト プール内のデスクトップ アプリ グループと RemoteApp アプリ グループの両方に割り当てることができます。 ただし、ユーザーは、セッションごとに 1 種類のアプリ グループしか起動できません。 ユーザーは、1 つのセッションで両方の種類のアプリ グループを同時に起動することはできません。
- ユーザーは、同じホスト プール内の複数のアプリ グループに割り当てることができ、そのフィードは両方のアプリ グループの累積になります。

## <a name="workspaces"></a>Workspaces

ワークスペースは、Windows Virtual Desktop 内のアプリケーション グループを論理的にグループ化したものです。 ユーザーに公開されたリモート アプリとデスクトップがそのユーザーに表示されるようにするには、各 Windows Virtual Desktop アプリケーション グループがワークスペースに関連付けられている必要があります。

## <a name="end-users"></a>エンド ユーザー

アプリ グループにユーザーを割り当てた後、いずれかの Windows Virtual Desktop クライアントを使用して Windows Virtual Desktop 展開に接続できます。

## <a name="next-steps"></a>次のステップ

委任されたアクセスとユーザーにロールを割り当てる方法について詳しくは、「[Delegated Access in Windows Virtual Desktop Preview (Windows Virtual Desktop での委任されたアクセス)](delegated-access-virtual-desktop.md)」をご覧ください。

Windows Virtual Desktop ホスト プールを設定する方法については、「[Azure portal を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)」を参照してください。

Windows Virtual Desktop に接続する方法については、次のいずれかの記事をご覧ください。

- [Windows 10 または Windows 7 を使用して接続する](connect-windows-7-10.md)
- [Web ブラウザーを使用して接続する](connect-web.md)
- [Android クライアントに接続する](connect-android.md)
- [macOS クライアントに接続する](connect-macos.md)
- [iOS クライアントに接続する](connect-ios.md)