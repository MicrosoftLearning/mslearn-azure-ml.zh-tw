---
lab:
  title: 探索 Azure Machine Learning 工作區
---

# 探索 Azure Machine Learning 工作區

Azure Machine Learning 提供資料科學平台來定型和管理機器學習模型。 在此實驗室中，您將建立 Azure Machine Learning 工作區，並探索各種使用工作區的方式。 實驗室旨在介紹 Azure Machine Learning 和開發人員工具的各種核心功能。 如果您想要更深入地了解功能，還有其他實驗室可供探索。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning **工作區** 提供一個集中位置，可用來管理您需要定型和管理模型的所有資源和資產。 您可以在 Azure 入口網站中使用互動式介面來佈建工作區，或使用 Azure CLI 搭配 Azure Machine Learning 延伸模組。 在大部分的生產案例中，最好使用 CLI 將佈建自動化，讓您可將資源部署併入可重複的開發與作業 (*DevOps*) 程序。 

在此練習中，您將使用 Azure 入口網站佈建 Azure Machine Learning 以探索所有選項。

1. 登入 `https://portal.azure.com/`。
2. 使用下列設定建立新的 **Azure Machine Learning** 資源：
    - **訂用帳戶**：您的 Azure 訂用帳戶**
    - **資源群組**：`rg-dp100-labs`
    - **工作區名稱**：`mlw-dp100-labs`
    - **區域**：*選取最接近您所在位置的地理區域*
    - **儲存體帳戶**：記下將為您的工作區建立的預設新儲存體帳戶**
    - **金鑰保存庫**：記下將為您的工作區建立的預設新金鑰保存庫**
    - **Application Insights**：記下將為您的工作區建立的預設新 Application Insights 資源**
    - **容器登錄**：無 (在您第一次將模型部署到容器時，系統將會自動建立一個**)
3. 等候工作區及其相關聯的資源建立完成 - 這通常需要大約 5 分鐘的時間。

> **注意**：在建立 Azure Machine Learning 工作區後，您可以使用一些進階選項來限制透過*私人端點*的存取，並指定資料加密的自訂金鑰。 我們不會在此練習中使用這些選項，但請您記得並留意這些選項！

## 探索 Azure Machine Learning 工作室

*Azure Machine Learning 工作室*是一個 Web 型入口網站，您可透過它來存取 Azure Machine Learning 工作區。 您可使用 Azure Machine Learning Studio 來管理工作區內的所有資產和資源。

1. 移至名為 **rg-dp100-labs** 的資源群組。
1. 確認資源群組包含您的 Azure Machine Learning 工作區、Application Insights、Key Vault 和儲存體帳戶。
1. 選取您的 Azure Machine Learning 工作區。
1. 從 [概觀]**** 頁面選取 [啟動工作室]****。 另一個索引標籤會在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在工作室中的快顯視窗。
1. 請注意工作室左側顯示的不同頁面。 如果功能表中只顯示符號，請選取 &#9776; 圖示以展開功能表，並探索頁面的名稱。
1. 請注意 [撰寫]**** 區段，其中包含 [筆記本]****、[自動化 ML]**** 和 [設計工具]****。 這些是您可以在 Azure Machine Learning 工作室中建立自己的機器學習模型的三種方式。
1. 請注意 [資產]**** 區段，其中包含 [資料]****、[作業]**** 及 [模型]****。 在定型或評分模型時，會取用或建立資產。 資產用於定型、部署及管理您的模型，並可設定版本以追蹤您的歷程記錄。
1. 請注意 [管理]**** 區段，其中包含 [計算]****。 這些是定型或部署機器學習模型所需的基礎結構資源。

## 撰寫訓練管線

若要探索 Azure Machine Learning 工作區中的資產和資源使用方式，讓我們嘗試並定型模型。

撰寫模型定型管線的快速方法是使用**設計工具**。

> **注意**：整個過程中可能會出現快顯項目來引導您使用工作室。 您可以關閉並忽略所有快顯項目，並將焦點放在此實驗室的指示。

1. 從工作室左側的功能表中選取 [設計工具]**** 頁面。
1. 選取 [迴歸 - 汽車價格預測 (基本)]**** 範例。

    新的管線隨即出現。 管線頂端會顯示元件，以載入 [汽車價格資料 (原始)]****。 管線會處理資料並定型線性迴歸模型，以預測每輛汽車的價格。
1. 選取頁面頂端的 [設定和提交]****，以開啟 [設定管線作業]**** 對話方塊
1. 在 [基本]**** 頁面上，選取 [建立新的]****，並將實驗的名稱設定為 `train-regression-designer`，然後選取 [下一步]****。
1. 在 [輸入與輸出]**** 頁面，選取 [下一步]****，而不進行任何變更。
1. [執行時間設定]**** 頁面會出現錯誤，因為您無執行管線的預設計算。

讓我們建立計算目標。

## 建立計算目標

若要在 Azure Machine Learning 工作區內執行任何工作負載，您需要計算資源。 Azure Machine Learning 的其中一個優點是能夠建立雲端式計算，讓您能大規模執行實驗和定型指令碼。

1. 在 Azure Machine Learning 工作室中，從左側功能表選取 [計算]**** 頁面。 您可以使用四種計算資源：
    - **計算執行個體**：由 Azure Machine Learning 管理的虛擬機器。 當您探索資料並反覆實驗機器學習模型時，適合用於開發。
    - **計算叢集**：可調整的虛擬機器叢集，可視需要處理實驗程式碼。 適合用於執行生產程式碼或自動化作業。
    - **Kubernetes 叢集**：用於定型和評分的 Kubernetes 叢集。 適用於大規模即時模型部署。
    - **連結的計算**：將虛擬機器或 Azure Databricks 叢集等現有 Azure 計算資源連結至工作區。

    若要使用設計工具來定型您撰寫的機器學習模型，您可使用計算執行個體或計算叢集。

2. 在 [計算執行個體]**** 索引標籤上，使用下列設定新增新的計算執行個體。 
    - **計算名稱**：*輸入唯一名稱*
    - **位置**：*自動與您的工作區位置相同*
    - **虛擬機器類型**：`CPU`
    - **虛擬機器大小**：`Standard_DS11_v2`
    - **可用配額**：此會顯示可用的專用核心。
    - **顯示進階設定**：請注意下列設定，但不選取這些設定：
        - **啟用 SSH 存取**：`Unselected` *(您可使用此選項來啟用利用 SSH 用戶端來對虛擬機器進行直接存取)*
        - **啟用虛擬網路**：`Unselected` *(您通常會在企業環境下使用此選項來增強網路安全性)*
        - **指派給其他使用者**：`Unselected` *(您可以使用此選項將計算執行個體指派給資料科學家)*
        - **使用安裝指令碼進行佈建**：`Unselected` *(您可以使用此選項來新增當遠端執行個體建立時要在其上執行的指令碼)*
        - **指派受控識別**：`Unselected` *(您可以連結系統指派或使用者指派的受控識別，以授與存取權給資源)*

3. 選取 [建立]**** 並等候遠端執行個體啟動且其狀態變更為 [執行中]****。

> **注意**：計算執行個體和叢集是以標準 Azure 虛擬機器映像為基礎。 針對此練習，建議使用 *Standard_DS11_v2* 映像，以達到最佳的成本與效能平衡。 如果您的訂用帳戶具有不包含此映像的配額，請選擇替代映像；但請記得，較大的映像可能會產生較高的成本，而較小的映像可能不足以完成工作。 或者，請要求您的 Azure 系統管理員擴大您的配額。

## 執行定型管線

您已建立計算目標，現在可以在設計工具中執行範例定型管線。

1. 瀏覽至 [設計工具]**** 頁面。
1. 選取 [迴歸 - 汽車價格預測 (基本)]**** 管線草稿。
1. 選取頁面頂端的 [設定和提交]****，以開啟 [設定管線作業]**** 對話方塊
1. 在 [基本]**** 頁面上，選取 [建立新的]****，並將實驗的名稱設定為 `train-regression-designer`，然後選取 [下一步]****。
1. 在 [輸入與輸出]**** 頁面，選取 [下一步]****，而不進行任何變更。
1. 在 [執行階段設定]**** 的 [選取計算類型]**** 下拉式清單中，選取 [計算執行個體]**，然後在 [選取 Azure ML 計算執行個體]**** 下拉式清單中選取新建立的計算執行個體。
1. 選取 [檢閱 + 提交]**** 即可檢閱管線作業，然後選取 [提交]**** 以便執行訓練管線。

定型管線現在會提交至計算執行個體。 大約需要 10 分鐘的時間，管線才會完成。 讓我們同時探索其他一些頁面。

## 使用作業來檢視您的歷程記錄

每當您在 Azure Machine Learning 工作區中執行指令碼或管線時，它就會記錄為 [作業]****。 作業可讓您追蹤您執行的工作負載，且彼此比較。 作業屬於**實驗**，可讓您將作業執行分組在一起。

1. 使用 Azure Machine Learning 工作室左側的功能表，瀏覽至 [作業]**** 頁面。
1. 選取 **train-regression-designer** 實驗，以檢視其作業執行。 在此，您會看到屬於此實驗之所有作業的概觀。 如果您執行了多個定型管線，此檢視可讓您比較管線並找出最佳管線。
1. 選取 **train-regression-designer** 實驗中的最後一個作業。
1. 請注意，定型管線會顯示，您可以在其中檢視哪些元件成功或失敗。 如果作業仍在執行中，您也可識別目前正在執行的項目。
1. 若要檢視管線作業詳細資料，請選取右上方的 [作業概觀]****，以展開 [管線作業概觀]****。
1. 請注意，在 [概觀]**** 參數中，您可以找到作業的狀態、建立管線的人員、建立管線的時間，以及執行完整管線所花費的時間(以及其他事項)。

    當您以作業形式執行指令碼或管線時，您可定義任何輸入並記載任何輸出。 Azure Machine Learning 也會自動追蹤作業的屬性。 藉由使用作業，您可以輕鬆地檢視歷程記錄，以瞭解您或同事已完成的工作。

    在實驗期間，作業可協助追蹤您定型的不同模型，以比較和識別最佳模型。 在生產期間，作業可讓您檢查自動化工作負載是否如預期般執行。

1. 當您的工作完成時，您也可以檢視每個個別元件執行的詳細資料，包括輸出。 您可以隨意探索管線，以瞭解模型的定型方式。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免產生不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-labs** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
