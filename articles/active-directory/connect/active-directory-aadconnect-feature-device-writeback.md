---
title: "Azure AD Connect: デバイスの書き戻しの有効化 | Microsoft Docs"
description: "Azure AD Connect を使用するデバイスの書き戻しを有効にする方法について詳しく説明します"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: bf7dd55be2b0e6ae43fc148ff36d8d2141a2f12b
ms.lasthandoff: 12/20/2016


---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: デバイスの書き戻しの有効化
> [!NOTE]
> デバイスの書き戻しには、Azure AD Premium に対するサブスクリプションが必要です。
> 
> 

ここでは、Azure AD Connect においてデバイスの書き戻し機能を有効にする方法について説明します。 デバイスの書き戻しは、次のシナリオで使用されます。

* AD FS (2012 R2 以降) で保護されたアプリケーション (証明書利用者の信頼) へのデバイスに基づく条件付きアクセスを有効にします。

これにより、セキュリティが強化され、アプリケーションへのアクセスが信頼されたデバイスに対してのみ許可されることが保証されます。 条件付きアクセスの詳細については、「[条件付きアクセス ポリシーを使用したリスクの管理](../active-directory-conditional-access.md)」および「[Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)」をご覧ください。

> [!IMPORTANT]
> <li>デバイスは、ユーザーと同じフォレスト内にある必要があります。 デバイスは単一のフォレストに書き戻される必要があるため、この機能では現在、複数のユーザー フォレストでのデプロイはサポートされていません。</li>
> <li>オンプレミスの Active Directory フォレストに追加できるのは、1 つのデバイス登録構成オブジェクトのみです。 この機能は、オンプレミスの Active Directory が複数の Azure AD ディレクトリに同期されるトポロジと互換性がありません。</li>> 

## <a name="part-1-install-azure-ad-connect"></a>パート 1: Azure AD Connect のインストール
1. カスタム設定または簡単設定を使用して Azure AD Connect をインストールします。 すべてのユーザーとグループの同期に成功してから、デバイスの書き戻しを有効にすることをお勧めします。

## <a name="part-2-prepare-active-directory"></a>パート 2: Active Directory の準備
デバイスの書き戻しの使用を準備するには、次の手順を使用します。

1. Azure AD Connect がインストールされているコンピューターから、管理者特権モードで PowerShell を起動します。
2. Active Directory PowerShell モジュールがインストールされていない場合は、次のコマンドを使用してインストールします。
   
   `Add-WindowsFeature RSAT-AD-PowerShell`
3. Azure Active Directory PowerShell モジュールがインストールされていない場合、 [Windows PowerShell 用 Azure Active Directory モジュール (64 ビット版)](http://go.microsoft.com/fwlink/p/?linkid=236297)からそれをダウンロードしてインストールします。 このコンポーネントには、Azure AD Connect と一緒にインストールされるサインイン アシスタントへの依存関係があります。
4. エンタープライズ管理者の資格情報で次のコマンドを実行した後、PowerShell を終了します。
   
   `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`
   
   `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

構成名前空間の変更が必要なため、エンタープライズ管理者の資格情報が必要です。 ドメイン管理者には、十分なアクセス許可がありません。

![デバイスの書き戻しを有効にするための Powershell](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

説明:

* CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn] の下にコンテナーとオブジェクトが存在しない場合は、新しく作成されて構成されます。
* CN=RegisteredDevices,[domain-dn] の下にコンテナーとオブジェクトが存在しない場合は、新しく作成されて構成されます。 このコンテナーにデバイス オブジェクトが作成されます。
* Active Directory でデバイスを管理するために必要なアクセス許可を Azure AD コネクタ アカウントに設定します。
* Azure AD Connect が複数のフォレストにインストールされている場合であっても、1 つのフォレストで実行すれば十分です。

パラメーター:

* DomainName: デバイス オブジェクトが作成される Active Directory ドメイン。 注: 特定の Active Directory フォレストのデバイスはすべて、1 つのドメインに作成されます。
* AdConnectorAccount: ディレクトリ内のオブジェクトを管理するために Azure AD Connect によって使用される Active Directory アカウント。 Azure AD Connect Sync が AD に接続するために使用するアカウントです。 簡単設定を使用してインストールした場合、アカウントには MSOL_ というプレフィックスが付きます。

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>パート 3: Azure AD Connect でのデバイスの書き戻しを有効にする
Azure AD Connect でデバイスの書き戻しを有効にするには、次の手順を使用します。

1. インストール ウィザードをもう一度実行します。 [追加のタスク] ページで **[同期オプションのカスタマイズ]** を選択し、**[次へ]** をクリックします。
   ![カスタム インストール [同期オプションのカスタマイズ]](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. [オプション機能] ページで、デバイスの書き戻しを変更できるようになります。 Azure AD Connect の準備手順が完了していない場合は、[オプション機能] ページでデバイスの書き戻しの設定を変更できないことに注意してください。 [デバイスの書き戻し] チェックボックスをオンにして、 **[次へ]**をクリックします。 チェックボックスがまだオフの場合は、 [トラブルシューティングのセクション](#the-writeback-checkbox-is-still-disabled)を参照してください。
   ![カスタム インストール デバイスの書き戻しオプション機能](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. [書き戻し] ページでは、指定したドメインが既定の [デバイスの書き戻しフォレスト] として表示されます。
   ![カスタム インストール デバイスの書き戻し先フォレスト](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. その他の構成は変更しないで、ウィザードのインストールを完了します。 必要に応じて、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」をご覧ください。

## <a name="enable-conditional-access"></a>条件付きアクセスを有効にする
このシナリオを有効にする詳細な手順については、「 [Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)」をご覧ください。

## <a name="verify-devices-are-synchronized-to-active-directory"></a>デバイスが Active Directory に同期されていることを確認する
デバイスの書き戻しは正常に動作するようになっています。 デバイス オブジェクトを AD に書き戻すには、最大 3 時間かかる可能性があります。  デバイスが正しく同期されていることを確認するには、同期規則が完了した後で次のようにします。

1. Active Directory 管理センターを起動します。
2. フェデレーションされているドメイン内の RegisteredDevices を展開します。
   ![Active Directory 管理センター登録済みのデバイス](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3. 現在登録されているデバイスが一覧表示されます。
   ![Active Directory 管理センター登録済みのデバイス一覧](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="the-writeback-checkbox-is-still-disabled"></a>書き戻しのチェックボックスがオフのままです。
前述の手順を実行しても、デバイスの書き戻しのチェックボックスがオンにならない場合は、次の手順を実行し、ボックスがオンになる前にインストール ウィザードで検証が実行されるようにします。

まず次の手順を実行します。

* 少なくとも 1 つのフォレストに Windows Server 2012R2 があることを確認します。 このデバイス オブジェクトの種類が必要です。
* インストール ウィザードが既に実行中の場合、変更があっても検出されません。 この場合は、インストール ウィザードを完了してから、再実行してください。
* 初期化スクリプトで指定したアカウントが、Active Directory Connector に使用されている正しいユーザーであることを確認します。 確認する手順は次のとおりです。
  * [スタート] メニューから **[同期サービス]**を開きます。
  * **[コネクタ]** タブを開きます。
  * 種類が Active Directory ドメイン サービスのコネクタを探して選択します。
  * **[アクション]** の **[プロパティ]** を選択します。
  * **[Active Directory フォレストに接続]**を選択します。 この画面で指定されているドメインとユーザー名と、スクリプトに指定したアカウントが一致することを確認します。
    ![Synchronization Service Manager のコネクタ アカウント](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Active Directory の構成を確認します。

* つまり、configuration 名前付けコンテキストの (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) の下に Device Registration Service があることを確認します。

![トラブルシューティング、configuration 名前空間の DeviceRegistrationService](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* configuration 名前空間を検索して、configuration オブジェクトが 1 つのみであることを確認します。 複数ある場合は、重複するオブジェクトを削除します。

![トラブルシューティング、重複するオブジェクトの検索](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Device Registration Service オブジェクトに、属性 msDS-DeviceLocation が存在し、値が指定されていることを確認します。 その場所を参照し、その場所と objectType msDS-DeviceContainer が存在することを確認します。

![トラブルシューティング、msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![トラブルシューティング、RegisteredDevices オブジェクト クラス](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Active Directory Connector には、前の手順で検索した Registered Devices コンテナーに対するアクセス許可が必要です。 このコンテナーには、次のようなアクセス許可があります。

![トラブルシューティング、コンテナーに対するアクセス許可の確認](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Active Directory アカウントに、CN=Device Registration Configuration,CN=Services,CN=Configuration オブジェクトに対するアクセス許可があることを確認します。

![トラブルシューティング、デバイス登録構成に対するアクセス許可の確認](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>追加情報
* [条件付きアクセス ポリシーを使用したリスクの管理](../active-directory-conditional-access.md)
* [Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。


