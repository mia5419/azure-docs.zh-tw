---
title: 教學課程：Azure Active Directory 與 BetterWorks 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 BetterWorks 之間的單一登入。
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: jeedes

---
# 教學課程：Azure Active Directory 與 BetterWorks 整合
本教學課程旨在說明如何整合 BetterWorks 與 Azure Active Directory (Azure AD)。

BetterWorks 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 BetterWorks 的人員
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 BetterWorks (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件
若要設定 Azure AD 與 BetterWorks 的整合作業，需要下列項目：

* 一個 Azure AD 訂用帳戶
* 啟用 BetterWorks 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫加入 BetterWorks
2. 設定並測試 Azure AD 單一登入

## 從資源庫加入 BetterWorks
若要設定 BetterWorks 與 Azure AD 的整合作業，您需要從資源庫將 BetterWorks 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫加入 BetterWorks，請執行下列步驟：**

1. 在「Azure 傳統入口網站」中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory][1]
2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫加入應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **BetterWorks**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)
7. 在結果窗格中選取 [BetterWorks]，然後按一下 [完成] 以新增應用程式。
   
    ![選取資源庫中的應用程式](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

## 設定並測試 Azure AD 單一登入
本節目標是示範如何根據名為 "Britta Simon" 的測試使用者，使用 BetterWorks 設定及測試 Azure AD 單一登入功能。

若要讓單一登入作用，Azure AD 必須知道 BetterWorks 與 Azure AD 中互相對應的使用者。換句話說，必須在 Azure AD 使用者和 BetterWorks 中的相關使用者之間，建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值指定為 BetterWorks 中 **Username** 的值。

若要使用 BetterWorks 設定及測試 Azure AD 單一登入功能，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 BetterWorks 測試使用者](#creating-a-betterworks-test-user)** - 在 BetterWorks 中建立一個 Britta Simon 對應項目，其要與 Azure AD 中代表她的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入
本節目標是在 Azure 傳統入口網站中啟用 Azure AD 單一登入功能，並在您的 BetterWorks 應用程式中設定單一登入功能。

BetterWorks 應用程式會預期要有特定格式的 SAML 判斷提示。請設定此應用程式的下列宣告。您可以從應用程式的 [屬性] 索引標籤來管理這些屬性的值。以下螢幕擷取畫面顯示上述的範例。

![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**若要使用 BetterWorks 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 傳統入口網站中的 [BetterWorks] 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。
   
    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)
2. 在 [SAML Token 屬性] 對話方塊，針對下表中顯示的每一列執行下列步驟：

    | 屬性名稱 | 屬性值 |
    | --- | --- |    
    | saml\_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    a.按一下 [新增使用者屬性] 來開啟 [新增使用者屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)

    b.在 [屬性名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c.在 [屬性值] 清單中，輸入該資料列所顯示的 SAML 權杖識別碼。

    d.按一下 [完成]

1. 在頂端的功能表中，按一下 [快速啟動]。
   
    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png)
2. 在 [要如何讓使用者登入 BetterWorks] 頁面上，選取 [Azure AD 單一登入]，然後按一下 **[下一步]**。
   
    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)
3. 在 [設定應用程式設定] 對話方塊頁面上，如果您想要以「IDP 起始模式」設定應用程式，請執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)

    a.在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://app.betterworks.com/saml2/metadata/`


    b.在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://app.betterworks.com/saml2/acs/`


    c.按 [**下一步**]

1. 在 [設定應用程式設定] 對話方塊頁面上，如果您想要以「SP 起始模式」設定應用程式，請執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)
   
    a.選取 [顯示進階設定 (選擇性)]。

    b.在 [登入 URL] 文字方塊中，使用下列模式輸入使用者用來登入 BetterWorks 應用程式的 URL：`https://app.betterworks.com`

    b.按 [下一步]。

1. 在 [設定在 BetterWorks 單一登入] 頁面上，執行下列步驟，然後按一下 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)
   
    a.按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。
   
    b.按 [下一步]。
2. 若要為您的應用程式設定 SSO，請透過 <mailto:support@betterworks.com> 連絡您的 BetterWorks 支援小組。附加下載的中繼資料檔案，並與 BetterWorks 小組共用，以便在 BetterWorks 端設定 SSO。
3. 在傳統入口網站中，選取單一登入設定確認，然後按一下 [下一步]。
   
    ![Azure AD 單一登入][10]
4. 在 [單一登入確認] 頁面上，按一下 [完成]。
   
   ![Azure AD 單一登入][11]

### 建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在「Azure 傳統入口網站」中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)
2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請在頂端的功能表中，按一下 [使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)
4. 若要開啟 [加入使用者] 對話方塊，請按一下底部工具列上的 [加入使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)
5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)
   
    a.針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b.在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c.按 [下一步]。
6. 在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)
   
   a.在 [名字] 文字方塊中，輸入 **Britta**。
   
   b.在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d.在 [角色] 清單中選取 [使用者]。
   
   e.按 [下一步]。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)
8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)
   
    a.記下 [新密碼] 的值。
   
    b.按一下 [完成]。

### 建立 BetterWorks 測試使用者
在本節中，您會在 BetterWorks 中建立名為 Britta Simon 的使用者。

請透過 <mailto:support@betterworks.com> 來與 BetterWorks 支援小組合作，在 BetterWorks 平台中加入使用者。

### 指派 Azure AD 測試使用者
本節的目標是要授與 Britta Simon 對 BetterWorks 的存取權，讓她能夠使用 Azure 單一登入。

   ![指派使用者][200]

**若要將 Britta Simon 指派到 BetterWorks，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。
   
    ![指派使用者][201]
2. 在應用程式清單中，選取 [BetterWorks]。
   
    ![設定單一登入](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)
3. 在頂端的功能表中，按一下 [使用者]。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon]。
5. 在底部的工具列中，按一下 [指派]。
   
    ![指派使用者][205]

### 測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 BetterWorks 磚時，應該會自動登入 BetterWorks 應用程式。

## 其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->