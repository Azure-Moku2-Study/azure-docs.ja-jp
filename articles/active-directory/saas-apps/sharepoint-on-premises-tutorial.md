---
title: 'チュートリアル: Azure Active Directory と SharePoint オンプレミスの統合 | Microsoft Docs'
description: Azure Active Directory と SharePoint オンプレミスの間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: jeedes
ms.openlocfilehash: 25e78633e7f1bead1eaa575edc6983a59e9ffa2d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817903"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>チュートリアル: Azure Active Directory と SharePoint オンプレミスの統合

このチュートリアルでは、SharePoint オンプレミスと Azure Active Directory (Azure AD) を統合する方法について説明します。

SharePoint オンプレミスと Azure AD を統合すると、次の利点があります。

- SharePoint オンプレミスにアクセスできるユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで SharePoint オンプレミスに自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SharePoint オンプレミスの統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SharePoint オンプレミスでのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SharePoint オンプレミスの追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>ギャラリーからの SharePoint オンプレミスの追加

Azure AD への SharePoint オンプレミスの統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SharePoint オンプレミスを追加する必要があります。

**ギャラリーから SharePoint オンプレミスを追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**SharePoint オンプレミス**」と入力し、結果パネルで **SharePoint オンプレミス**を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの SharePoint オンプレミス](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SharePoint オンプレミスで Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SharePoint オンプレミス ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SharePoint オンプレミスの関連ユーザーの間で、リンク関係が確立されている必要があります。

SharePoint オンプレミスで Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SharePoint オンプレミス テスト ユーザーにアクセス権を付与する](#grant-access-to-sharePoint-on-premises-test-user)** - SharePoint オンプレミスで Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にし、SharePoint オンプレミス アプリケーションでシングル サインオンを構成します。

**SharePoint オンプレミスで Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure portal の **SharePoint オンプレミス** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. **[SharePoint オンプレミスのドメインと URL]** セクションで、次の手順を実行します。

    ![[SharePoint オンプレミスのドメインと URL] のシングル サインオン情報](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. **[サインオン URL]** ボックスに、`https://<YourSharePointServerURL>/_trust/default.aspx` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`urn:sharepoint:federation`

    c. **[応答 URL]** ボックスに、`https://<YourSharePointServerURL>/_trust/default.aspx` のパターンを使用して URL を入力します。

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

    > [!Note]
    > 証明書ファイルをダウンロードしたファイル パスは、構成時に PowerShell スクリプトで後から使用する必要があるため、書き留めておいてください。

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. **[SharePoint オンプレミス構成]** セクションで、**[SharePoint オンプレミスの構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。 **[シングル サインオン サービス URL]** に次のパターンの値を使用します。`https://login.microsoftonline.com/_my_directory_id_/wsfed` 

    > [!Note]
    > _my_directory_id_ は Azure Ad サブスクリプションのテナント ID です。

    ![SharePoint オンプレミス構成](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Sharepoint オンプレミス アプリケーションは SAML 1.1 トークンを使用するため、Azure AD では認証の後に SharePoint サーバーからの WS 取り込み要求が必要で、SAML 1.1  トークンを発行します。

7. 別の Web ブラウザー ウィンドウで、Sharepoint オンプレミスの企業サイトに管理者としてログインします。

8. **SharePoint Server 2016 で信頼できる ID プロバイダーを新しく構成する**

    SharePoint Server 2016 サーバーにサインインし、SharePoint 2016 管理シェルを開きます。 Azure Portal から $realm (Azure Portal の [SharePoint オンプレミスのドメインと URL] からの [識別子] の値)、$wsfedurl ([シングル サインオン サービス URL])、および $filepath (証明書ファイルをダウンロードしたファイル パス) の値を入力し、次のコマンドを実行して信頼済みの新しい ID プロバイダーを構成します。

    > [!TIP]
    > PowerShell を使用した経験がない場合、または PowerShell の動作の詳細については、[SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps) に関する記事をご覧ください。 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    次に、これらの手順を実行して、アプリケーションの信頼できる ID プロバイダーを有効にします。

    a. [サーバーの全体管理] で **[Web アプリケーションの管理]** に移動し、Azure AD を使用してセキュリティ保護する Web アプリケーションを選択します。

    b. リボンで、**[認証プロバイダー]** をクリックして、使用するゾーンを選択します。

    c. **[信頼できる ID プロバイダー]** を選択し、*[AzureAD]* という名前で先ほど登録した ID プロバイダーを選択します。

    d. サインイン ページの URL 設定で、**[カスタム サインイン ページ]** を選択し、値 "/_trust/" を指定します。

    e. Click **OK**.

    ![認証プロバイダーを構成する](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > 外部ユーザーの一部では、UPN が `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com` のような値を分割したために、このシングル サインオン統合を使用できません。 近いうちに、顧客がユーザーの種類に応じた UPN の処理方法をアプリ構成できるようにする予定です。 アプリ構成が可能になった後は、すべてのゲスト ユーザーが組織の従業員としてシームレスに SSO を使用できます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>SharePoint オンプレミスのテスト ユーザーにアクセス権を付与する

Azure AD にログインして SharePoint にアクセスするユーザーにはアプリケーションへのアクセス権が付与されている必要があります。次の手順に従って Web アプリケーションにアクセスするためのアクセス許可を設定します。

1. [サーバーの全体管理] で、**[アプリケーション管理]** をクリックします。

2. **[アプリケーション管理]** ページの **[Web アプリケーション]** セクションで、**[Web アプリケーションの管理]** を選択します。

3. 適切な Web アプリケーションをクリックし、**[ユーザー ポリシー]** をクリックします。

4. [Policy for Web Application]\(Web アプリケーションのポリシー\) で、**[ユーザーの追加]** をクリックします。

    ![名前要求を条件にユーザーを検索](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. **[ユーザーの追加]** ダイアログ ボックスの **[ゾーン]** で適切なゾーンをクリックして、**[次へ]** をクリックします。

6. **[Policy for Web Application]\(Web アプリケーションのポリシー\)** ダイアログ ボックスの **[ユーザーの選択]** セクションで、**[参照]** アイコンをクリックします。

7. **[検索]** テキストボックスに、Azure AD での SharePoint オンプレミス アプリケーションの構成に使用した**ユーザー プリンシパル名 (UPN)** の値を入力して、**[検索]** をクリックします。 </br>例: *brittasimon@contoso.com*。

8. リスト ビューで、AzureAD という見出しの下の名前プロパティを選択して、**[追加]** をクリックし、**[OK]** をクリックしてダイアログ ボックスを閉じます。

9. [アクセス許可] で **[フル コントロール]** をクリックします。

    ![要求ユーザーにフル コントロールを許可する](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. **[完了]** をクリックし、**[OK]** をクリックします。

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>複数の Web アプリケーションに対して 1 つの信頼できる ID プロバイダーを構成する

構成は 1 つの Web アプリケーションに機能しますが、複数の Web アプリケーションに対して同じ信頼できる ID プロバイダーを使用する場合は、追加の構成が必要です。 たとえば、URL `https://portal.contoso.local` を使用するように Web アプリケーションを拡張し、`https://sales.contoso.local` にユーザーを認証する必要があるとします。 このためには、WReply パラメーターを有効にするように ID プロバイダーを更新し、Azure AD でアプリケーションの登録を更新して応答 URL を追加する必要があります。

1. Azure Portal で Azure AD ディレクトリを開きます。 **[アプリの登録]** をクリックし、**[アプリケーションをすべて表示]** をクリックします。 以前に作成したアプリケーションをクリックします (SharePoint SAML 統合)。

2. **[設定]** をクリックします。

3. [設定] ブレードで **[応答 URL]** をクリックします。 

4. URL に `/_trust/default.aspx` を追加して追加の Web アプリケーションの URL を追加して (`https://sales.contoso.local/_trust/default.aspx` など)、**[保存]** をクリックします。

5. SharePoint サーバーで **SharePoint 2016 管理シェル**を開き、以前に使用した信頼できる ID トークンの発行者の名前を使用して、次のコマンドを実行します。

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. 全体管理では、Web アプリケーションに移動し、既存の信頼できる ID プロバイダーを有効にします。 カスタム サインイン ページ `/_trust/` としてサインイン ページ の URL を構成する必要もあります。

7. 全体管理で、Web アプリケーションをクリックして、**[ユーザー ポリシー]** を選択します。 この記事で説明済みの適切なアクセス許可を使用してユーザーを追加します。

### <a name="fixing-people-picker"></a>メンバー選択の修正

Azure AD からの ID を使用して SharePoint 2016 にユーザーがログインできるようになりましたが、ユーザー エクスペリエンスを改善する余地がまだあります。 たとえば、ユーザーを検索すると、ユーザーの選択ウィンドウに複数の検索結果が表示されます。 要求のマッピングで作成された 3 種類の要求ごとに検索結果が表示されています。 ユーザーの選択ウィンドウを使用してユーザーを選択するには、ユーザー名を正確に入力し、**名前**要求結果を選択する必要があります。

![要求検索結果](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

検索条件値の検証機能がないため、スペル ミスや、ユーザーが誤って正しくない種類の要求を選択して、たとえば **[姓]** 要求を割り当てる可能性があります。 これにより、ユーザーがリソースにアクセスできない可能性があります。

このシナリオのために、[AzureCP](https://yvand.github.io/AzureCP/) と呼ばれるオープン ソース ソリューションがあり、SharePoint 2016 のカスタム クレーム プロバイダーを提供します。 このソリューションは、Azure AD Graph を使用してユーザー入力を解決し、検証します。 詳細については [AzureCP](https://yvand.github.io/AzureCP/) を参照してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SharePoint オンプレミスへのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**SharePoint オンプレミスに Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[SharePoint オンプレミス]** を選択します。

    ![アプリケーションの一覧の SharePoint のリンク](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SharePoint オンプレミス] タイルをクリックすると、自動的に SharePoint オンプレミス アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [Azure AD を使用した SharePoint Server の認証](https://docs.microsoft.com/en-us/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media\sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media\sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media\sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media\sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media\sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media\sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media\sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media\sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media\sharepoint-on-premises-tutorial/tutorial_general_203.png