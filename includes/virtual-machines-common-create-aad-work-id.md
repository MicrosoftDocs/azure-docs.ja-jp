
<br>

> [!NOTE]
> 管理者からユーザー名とパスワードが提供されている場合は、既に職場または学校の ID を持っている可能性があります ( *組織 ID*とも呼ばれます)。 その場合、いつでも Azure アカウントの使用を開始して、アカウントが必要な Azure リソースにアクセスできます。 これらのリソースを使用できない場合は、この記事をもう一度ご確認ください。 詳細については、「[サインインに使用できるアカウント](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts)」および「[Azure サブスクリプションと Azure AD の関連性](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir)」を参照してください。
> 
> 

手順は簡単です。 Azure クラシック ポータルで署名済み ID を見つけて、既定の Azure Active Directory ドメインを検出し、Azure 共同管理者として新しいユーザーを追加する必要があります。

## <a name="locate-your-default-directory-in-the-azure-classic-portal"></a>Azure クラシック ポータルで既定のディレクトリを見つける
まず、個人の Microsoft アカウント ID で [Azure クラシック ポータル](https://manage.windowsazure.com) にログインします。 ログインしたら、左側の青色のパネルを下にスクロールし、 **[ACTIVE DIRECTORY]**をクリックします。

![Azure Active Directory](./media/virtual-machines-common-create-aad-work-id/azureactivedirectorywidget.png)

最初に、Azure で自分の ID の一部を確認しましょう。 次に示すように、メイン ウィンドウで既定のディレクトリが 1 つあることがわかります。

![](./media/virtual-machines-common-create-aad-work-id/defaultaadlisting.png)

その他の情報を確認してみましょう。 既定のディレクトリ列をクリックすると、既定のディレクトリのプロパティが表示されます。  

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectorypage.png)

既定のドメイン名を表示するには、 **[DOMAINS]**をクリックします。

![](./media/virtual-machines-common-create-aad-work-id/domainclicktoseeyourdefaultdomain.png)

ここでは、Azure アカウントが作成されると、onmicrosoft.com のサブドメインとして使用する個人 ID のハッシュ値 (文字列から生成される数字) である個人の既定ドメインが Azure Active Directory によって作成されたことが確認できるはずです。 このドメインを使用して、新しいユーザーを追加することになります。

## <a name="creating-a-new-user-in-the-default-domain"></a>既定のドメインで新しいユーザーを作成する
**[ユーザー]** をクリックし、自分の個人アカウントを探します。 **[ソース]** 列を確認すると **Microsoft アカウント**であることがわかります。 ユーザーを既定の onmicrosoft.com Azure Active Directory ドメインで作成します。

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryuserslisting.png)

次の数ステップでは [手順](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) の説明に従いますが、特定の例を使用します。

ページ下部で **[+追加]**をクリックします。 表示されるページで、新しいユーザー名を入力し、**[ユーザーの種類]** で **[組織内の新しいユーザー]** を選択します。 この例では、新しいユーザー名は `ahmet`です。 以前に特定した既定のドメインを ahmet のメール アドレスのドメインとして選択してください。 矢印をクリックして次へ進みます。

![](./media/virtual-machines-common-create-aad-work-id/addingauserwithdirectorydropdown.png)

Ahmet の詳細を追加しますが、適切な **[ロール]** 値を選択してください。 **[グローバル管理者]** を使用すると確実ですが、使用できる場合には、下位のロールを使用することをお勧めします。 この例では、**[ユーザー]** ロールを使用します (詳細については、[ロール別の管理者権限](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1)に関するページを参照してください)。操作の各ログで Multi-Factor Authentication を使用する必要がない場合は、Multi-Factor Authentication を有効にしないでください。 終了したら、矢印をクリックします。

![](./media/virtual-machines-common-create-aad-work-id/userprofileuseradmin.png)

**[作成]** ボタンをクリックして、Ahmet の一時パスワードを生成、表示します。

![](./media/virtual-machines-common-create-aad-work-id/gettemporarypasswordforuser.png)

ユーザー名の電子メール アドレスをコピーするか、または[ **パスワードを電子メールで送信**]を使用します。 すぐにログオン時にその情報が必要になります。

![](./media/virtual-machines-common-create-aad-work-id/receivedtemporarypassworddialog.png)

Azure Active Directory から取得した新しいユーザー、 **開発者の Ahmet**が表示されます。 Azure Active Directory を使用して、職場または学校の新しい ID を作成しました。 ただし、この ID には Azure のリソースを使用する権限がまだありません。

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryusersaftercreate.png)

[ **パスワードを電子メールで送信**]を使用する場合、次のような電子メールが送信されます。

![](./media/virtual-machines-common-create-aad-work-id/emailreceivedfromnewusercreation.png)

## <a name="adding-azure-co-administrator-rights-for-subscriptions"></a>サブスクリプションに Azure 共同管理者の権限を追加する
今度は、新しいユーザーが管理ポータルにサインインできるように、サブスクリプションの共同管理者として新しいユーザーを追加する必要があります。 そのためには、左下のパネルで [ **設定**] をクリックします。

![](./media/virtual-machines-common-create-aad-work-id/thesettingswidget.png)

メインの設定領域で、上部の **[管理者]** をクリックすると、個人の Microsoft アカウント ID のみが表示されるはずです。 ページ下部で **[+追加]** をクリックし、共同管理者を指定します。 ここでは、既定のドメインと、作成した新しいユーザーの電子メール アドレスを入力します。 次のスクリーン ショットのように、既定のディレクトリのユーザーの横に緑色のチェック マークが表示されます。 このユーザーに管理を許可するすべてのサブスクリプションを選択してください。

![](./media/virtual-machines-common-create-aad-work-id/addingnewuserascoadmin.png)

完了すると、新しい共同管理者 ID を含む、2 人のユーザーが表示されます。 ポータルからログアウトします。

![](./media/virtual-machines-common-create-aad-work-id/newuseraddedascoadministrator.png)

## <a name="logging-in-and-changing-the-new-users-password"></a>新しいユーザーとしてログインしてパスワードを変更する
作成した新しいユーザーとしてログインします。

![](./media/virtual-machines-common-create-aad-work-id/signinginwithnewuser.png)

すぐに新しいパスワードを作成するように求められます。

![](./media/virtual-machines-common-create-aad-work-id/mustupdateyourpassword.png)

次のような正常に完了したことを示す内容が表示されます。

![](./media/virtual-machines-common-create-aad-work-id/successtourdialog.png)

## <a name="next-steps"></a>次のステップ
新しい Azure Active Directory の ID で [Azure リソース グループ テンプレート](../articles/xplat-cli-azure-resource-manager.md)を使用できます。

    azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


<!--HONumber=Nov16_HO3-->


