---
lab:
  title: 探索用於工作區互動的開發人員工具
---

# 探索用於工作區互動的開發人員工具

您可使用各種工具來與 Azure Machine Learning 工作區互動。 根據您需要執行的工作，以及開發人員工具的喜好設定，您可選擇要何時使用哪個工具。 此實驗室旨在介紹常用於工作區互動的開發人員工具。 如果您想要深入了解如何使用特定工具，還有其他實驗室可供探索。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

與 Azure Machine Learning 工作區互動的常用開發人員工具如下：

- 使用 Azure Machine Learning 延伸模組的 **Azure CLI**：這個命令列方法很適合用於基礎結構自動化。
- **Azure Machine Learning 工作室**：使用方便使用的 UI 來探索工作區及其所有功能。
- 適用於 Azure Machine Learning 的 **Python SDK**：用於從 Jupyter 筆記本提交作業和管理模型，非常適合資料科學家。

您將探索這些工具中的每一個工具，以了解通常使用該工具完成的工作。

## 使用 Azure CLI 佈建基礎結構

若要讓資料科學家使用 Azure Machine Learning 來定型機器學習模型，您必須設定必要的基礎結構。 您可使用 Azure CLI 搭配 Azure Machine Learning 延伸模組來建立 Azure Machine Learning 工作區和資源，例如計算執行個體。

若要開始，請開啟 Azure Cloud Shell，安裝 Azure Machine Learning 延伸模組並複製 Git 存放庫。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，並使用您的 Microsoft 帳戶登入。
1. 選取頁面頂端搜尋方塊右邊的 \[>_] (*Cloud Shell*) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 若系統詢問，請選取 [Bash]****。 第一次開啟 Cloud Shell 時，系統會要求您選擇要使用的殼層類型 ([Bash]** 或 [PowerShell]**)。
1. 檢查是否已指定正確的訂用帳戶，並 **選取 [不需要** 記憶體帳戶]。 選取**套用**。
1. 使用以下命令，移除任何 ML CLI 延伸模組 (第 1 版和 2 版)，以避免與舊版發生任何衝突：
    
    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼到 Cloud Shell 中。

    > 忽略表示未安裝延伸模組的任何 (錯誤) 訊息。

1. 使用下列命令安裝 Azure Machine Learning (v2) 延伸模組:
    
    ```azurecli
    az extension add -n ml -y
    ```

1. 建立資源群組。 選擇接近您的位置。
    
    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. 若要建立工作區：
    
    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. 等候工作區及其相關聯的資源建立完成 - 這通常需要大約 5 分鐘的時間。

    <details>  
    <summary><b>疑難解答提示</b>：工作區建立錯誤</summary><br>
    <p>如果您在透過 CLI 建立工作區時收到錯誤，您必須手動佈建資源：</p>
    <ol>
        <li>在 Azure 入口網站 首頁中，選取 [<b>+建立資源</b>]。</li>
        <li>搜尋<i>機器學習</i>服務，然後選取 <b>[Azure 機器學習</b>]。 選取 <b>建立</b>。</li>
        <li>使用下列設定建立新的 Azure Machine Learning 資源： <ul>
                <li><b>訂用帳戶</b>：您的 Azure 訂用帳戶<i></i></li>
                <li><b>資源群組</b>：rg-dp100-labs</li>
                <li><b>工作區名稱</b>：mlw-dp100-labs</li>
                <li><b>區域</b>：<i>選取最接近您所在位置的地理區域</i></li>
                <li><b>儲存體帳戶</b>：記下將為您的工作區建立的預設新儲存體帳戶<i></i></li>
                <li><b>金鑰保存庫</b>：記下將為您的工作區建立的預設新金鑰保存庫<i></i></li>
                <li><b>Application Insights</b>：記下將為您的工作區建立的預設新 Application Insights 資源<i></i></li>
                <li><b>容器登錄</b>：無 (在您第一次將模型部署到容器時，系統將會自動建立一個<i></i>)</li>
            </ul>
        <li>選取 <b>[檢閱 + 建立</b> ]，並等候工作區及其相關聯的資源建立 - 這通常需要大約 5 分鐘的時間。</li>
    </ol>
    </details>

## 使用 Azure CLI 建立計算執行個體

定型機器學習模型所需基礎結構的另一個重要部分是**計算**。 雖然您可以在本機定型模型，但使用雲端計算會更具可調性和成本效益。

當資料科學家在 Azure Machine Learning 工作區中開發機器學習模型時，他們想要使用可執行 Jupyter Notebook 的虛擬機器。 針對開發，**計算執行個體**非常適合。

建立 Azure Machine Learning 工作區之後，您也可以使用 Azure CLI 建立計算執行個體。

在此練習中，您將使用下列設定來建立計算執行個體：

- **計算名稱**：*計算執行個體的名稱。必須是唯一且少於 24 個字元。*
- **虛擬機器大小**：STANDARD_DS11_V2
- **計算類型** (執行個體或叢集)：ComputeInstance
- **Azure Machine Learning 工作區名稱**：mlw-dp100-labs
- **資源群組**：rg-dp100-labs

1. 使用下列命令在工作區中建立計算執行個體。 如果計算執行個體名稱包含 "XXXX"，請以隨機數字取代它，以建立唯一的名稱。

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    如果您收到錯誤訊息，指出計算執行個體的名稱已經存在，請變更名稱，然後重試命令。

    <details>  
    <summary><b>疑難解答提示</b>：計算建立錯誤</summary><br>
    <p>如果您在透過 CLI 建立計算實體時收到錯誤，您必須手動佈建資源：</p>
    <ol>
        <li>在 Azure 入口網站中，瀏覽至名為 <b>mlw-dp100-labs</b> 的 Azure Machine Learning 工作區。</li>
        <li>選取 Azure Machine Learning 工作區，然後在其 [概觀]<b></b> 頁面中，選取 [啟動工作室]<b></b>。 另一個索引標籤將在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。</li>
        <li>關閉在工作室中出現的任何快顯視窗。</li>
        <li>在 Azure Machine Learning 工作室 內，流覽至 <b>[計算] 頁面，然後選取 [計算</b>實例] 索引<b>卷標下的 [+</b><b>新增]。</b></li>
        <li>為計算實例指定唯一的名稱，然後選取 <b>Standard_DS11_v2</b> 做為虛擬機大小。</li>
        <li>選取 [檢閱 + 建立]<b></b>，然後選取 [建立]<b></b>。</li>
    </ol>
    </details>

## 使用 Azure CLI 建立計算叢集

雖然計算執行個體很適合用於開發，但是當我們想要定型機器學習模型時，計算叢集更適合。 只有在提交作業以使用計算叢集時，才會調整為 0 個以上的節點並執行作業。 一旦不再需要計算叢集，其會自動調整大小回 0 個節點，以將成本降到最低。 

若要建立計算叢集，您可以使用 Azure CLI，類似於建立計算執行個體。

您將使用下列設定來建立計算叢集：

- **計算名稱**：aml-cluster
- **虛擬機器大小**：STANDARD_DS11_V2
- **計算類型**：AmlCompute * (建立計算叢集)*
- **執行個體上限**：*節點數目上限*
- **Azure Machine Learning 工作區名稱**：mlw-dp100-labs
- **資源群組**：rg-dp100-labs

1. 使用下列命令在工作區中建立計算叢集。
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

    <details>  
    <summary><b>疑難解答提示</b>：計算建立錯誤</summary><br>
    <p>如果您在透過 CLI 建立計算叢集時收到錯誤，您必須手動佈建資源：</p>
    <ol>
        <li>在 Azure 入口網站中，瀏覽至名為 <b>mlw-dp100-labs</b> 的 Azure Machine Learning 工作區。</li>
        <li>選取 Azure Machine Learning 工作區，然後在其 [概觀]<b></b> 頁面中，選取 [啟動工作室]<b></b>。 另一個索引標籤將在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。</li>
        <li>關閉在工作室中出現的任何快顯視窗。</li>
        <li>在 Azure Machine Learning 工作室 中，流覽至 <b>[計算</b>] 頁面，然後選取 <b>+ [計算叢集] 索引<b>卷標下的 [</b>新增</b>]。</li>
        <li>選擇與您建立工作區所在的區域相同，然後選取 <b>[Standard_DS11_v2</b> ] 作為虛擬機大小。 選取<b>下一個</b></li>
        <li>為叢集提供唯一的名稱，然後選取 [ <b>建立</b>]。</li>
    </ol>
    </details>

## 使用 Azure Machine Learning 工作室設定工作站

雖然 Azure CLI 很適合自動化，但您可能想檢閱您所執行命令的輸出。 您可以使用 Azure Machine Learning 工作室來檢查是否已建立資源和資產，以及檢查是否成功執行作業，或檢閱作業失敗的原因。 

1. 在 Azure 入口網站中，瀏覽至名為 **mlw-dp100-labs** 的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概觀]**** 頁面中，選取 [啟動工作室]****。 另一個索引標籤將在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在工作室中的快顯視窗。
1. 在 Azure Machine Learning 工作室中，瀏覽至 [計算]**** 頁面，並驗證您在上一節中建立的計算執行個體和叢集是否存在。 計算實例應該正在執行，叢集應處於成功狀態，且有0個節點正在執行。

## 使用 Python SDK 來定型模型

既然您已確認已建立必要的計算，您可以使用 Python SDK 來執行定型指令碼。 您將在計算執行個體上安裝並使用 Python SDK，並在計算叢集上定型機器學習模型。

1. 在您的 **計算實例**中 **，[應用程式** ] 字段中有許多選項。 選取 [ **終端** 機] 應用程式以啟動終端機（您可能需要單擊省略號以展開選取範圍）。
1. 在終端機中執行下列命令，以在終端機中的計算執行個體上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 略過表示未安裝套件的任何 (錯誤) 訊息。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 在命令完成後，選取 [檔案]**** 窗格中的 [&#8635;]**** 以重新整理檢視，並確認已建立新的 **/users/*your-user-name*/azure-ml-labs** 資料夾。
1. 開啟 **Labs/02/Run training script.ipynb** 筆記本。

    > 選取 [驗證]****，如果出現通知要求您進行驗證，請遵循必要的步驟。

1. 確認筆記本使用 **筆記本環境右上角的 Python 3.8 - AzureML** 核心。 每個核心都有自己的映像，並預先安裝了自己的套件集。
1. 在筆記本中執行所有儲存格。

新的作業將會在 Azure Machine Learning 工作區中建立。 作業會追蹤作業組態中定義的輸入、所使用的程式碼，以及用來評估模型的計量等輸出。

## 在 Azure Machine Learning 工作室中檢閱您的作業歷程記錄

當您將作業提交至 Azure Machine Learning 工作區時，您可以在 Azure Machine Learning 工作室中檢閱其狀態。

1. 選取筆記本中提供做為輸出的作業 URL，或瀏覽至 Azure Machine Learning 工作室中的 [作業]**** 頁面。
1. 列出一項名為 **diabetes-training** 的新實驗。 選取最新的作業 **diabetes-pythonv2-train**。
1. 檢閱作業的**屬性**。 記下作業 [狀態]****：
    - **已加入佇列**：作業正在等候計算可供使用。
    - **準備中**：計算叢集正在調整大小，或正在計算目標上安裝環境。
    - **執行中**：正在執行定型指令碼。
    - **正在完成**：訓練指令碼已執行，且作業會以所有最終資訊進行更新。
    - **已完成**：作業已成功完成並終止。
    - **失敗**：作業失敗並終止。
1. 在 [輸出 + 記錄]**** 之下，您可以在 **user_logs/std_log.txt** 中找到指令碼的輸出。 指令碼中 **列印** 陳述式的輸出會顯示在這裡。 如果因為指令碼有問題而出現錯誤，您也會在這裡找到錯誤訊息。
1. 在 **Code** 之下，您會發現您在作業組態中指定的資料夾。 此資料夾包含定型指令碼和資料集。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免產生不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-labs** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。 
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
