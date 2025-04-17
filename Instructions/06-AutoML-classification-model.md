---
lab:
  title: 使用自動化機器學習尋找最佳分類模型
---

# 使用自動化機器學習尋找最佳分類模型

為模型定型判斷正確演算法和前置處理轉換的工作可能會牽涉到大量猜測和實驗。

在此練習中，您將使用自動化機器學習，藉由平行執行多次定型，來判斷模型的最佳演算法和前置處理步驟。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning 工作區** 會提供一個集中位置，用來管理您定型和管理模型所需的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。

您將使用 Azure CLI 來佈建工作區和必要的計算，並使用 Python SDK 使用自動化機器學習來定型分類模型。

### 建立工作區和計算資源

若要建立 Azure Machine Learning 工作區、計算執行個體和計算叢集，您將使用 Azure CLI。 所有必要的命令會在殼層指令碼中進行分組，以供您執行。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，並使用您的 Microsoft 帳戶登入。
1. 選取頁面頂端搜尋方塊右邊的 \[>_] (*Cloud Shell*) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 若系統詢問，請選取 [Bash]****。 第一次開啟 Cloud Shell 時，系統會要求您選擇要使用的殼層類型 ([Bash]** 或 [PowerShell]**)。
1. 檢查是否已指定正確的訂用帳戶，並 **選取 [不需要** 記憶體帳戶]。 選取**套用**。
1. 在終端機中，輸入下列命令來複製此存放庫:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼入 Cloud Shell 中。

1. 在複製了存放庫之後，請輸入下列命令以變更為此實驗室的資料夾，並執行其所包含的 **setup.sh** 指令碼:

    ```azurecli
    cd azure-ml-labs/Labs/06
    ./setup.sh
    ```

    > 忽略表明未安裝延伸模組的任何 (錯誤) 訊息。

1. 等候指令碼完成 - 這通常需要大約 5-10 分鐘的時間。

    <details>
    <summary><b>疑難解答提示</b>：工作區建立錯誤</summary><br>
    <p>如果您在透過 CLI 執行安裝文稿時收到錯誤，您需要手動佈建資源：</p>
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
        <li>選取 <b>[移至資源</b>]，然後在其 [概觀<b>] </b>頁面中，選取 [<b>啟動工作室</b>]。 另一個索引標籤將在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。</li>
        <li>關閉在工作室中出現的任何快顯視窗。</li>
        <li>在 Azure Machine Learning 工作室 內，流覽至 <b>[計算</b>] 頁面，然後選取+ <b>[計算實例] 索引<b>卷標下的 [</b>新增</b>]。</li>
        <li>為計算實例指定唯一的名稱，然後選取 <b>Standard_DS11_v2</b> 做為虛擬機大小。</li>
        <li>選取 [檢閱 + 建立]<b></b>，然後選取 [建立]<b></b>。</li>
        <li>接下來，選取 [ <b>計算叢集] 索引標籤</b> ，然後選取 [ <b>+ 新增</b>]。</li>
        <li>選擇與您建立工作區所在的區域相同，然後選取 <b>[Standard_DS11_v2</b> ] 作為虛擬機大小。 選取<b>下一個</b></li>
        <li>為叢集提供唯一的名稱，然後選取 [ <b>建立</b>]。</li>
    </ol>
    </details>

## 複製實驗室材料

當您建立了工作區和必要的計算資源時，您可以開啟 Azure Machine Learning 工作室，並將實驗室材料複製到工作區。

1. 在 Azure 入口網站中，瀏覽至名為 **mlw-dp100-...** 的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概觀]**** 頁面中，選取 [啟動工作室]****。 另一個索引標籤將在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在工作室中的快顯視窗。
1. 在 Azure Machine Learning 工作室中，瀏覽至 [計算]**** 頁面，並驗證您在上一節中建立的計算執行個體和叢集是否存在。 計算執行個體應該正在執行，叢集應該閒置，而且有 0 個節點正在執行。
1. 在 [計算執行個體]**** 索引標籤中，尋找您的計算執行個體，然後選取 [終端機]**** 應用程式。
1. 在終端機中，透過執行下列命令，在計算執行個體上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何 (錯誤) 訊息，指出找不到套件並解除安裝。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 在命令完成後，按一下 [檔案]**** 窗格中的 [&#8635;]**** 以重新整理檢視，並確認已建立新的 **/users/*your-user-name*/azure-ml-labs** 資料夾。

## 使用自動化機器學習來定型分類模型

現在您已擁有所有必要的資源，您可以執行筆記本來設定並提交自動化機器學習作業。

1. **使用自動化 Machine Learning.ipynb 筆記本開啟 Labs/06/Classification**。

    > 選取 [驗證]****，如果出現通知要求您進行驗證，請遵循必要的步驟。

1. 驗證筆記本是否使用 **Python 3.8 - AzureML** 核心。
1. 在筆記本中執行所有儲存格。

    新的作業將會在 Azure Machine Learning 工作區中建立。 作業會追蹤作業組態中定義的輸入、所使用的資料資產，以及用來評估模型的計量等輸出。

    請注意，自動化機器學習作業包含子工作，代表已定型的個別模型，以及執行所需的其他工作。
1. 移至 [作業]****，然後選取 **auto-ml-class-dev** 實驗。
1. 選取 [顯示名稱]**** 資料行底下的作業。
1. 等候其狀態變更為 [已完成]****。
1. 當 Automate Machine Learning 作業狀態變更為 [已完成]**** 時，請瀏覽工作室中的作業詳細資料:
    - [資料防護]**** 索引標籤會顯示您的定型資料是否有任何問題。
    - [ **模型 + 子作業** ] 索引標籤會顯示所有已定型的模型。 針對最佳模型選取 **[說明模型** ]，然後使用 **aml-cluster** 建立說明作業執行。
    - 等到 [演算法名稱 **] 資料行旁邊**出現 [說明 **] 新數據行**，然後選取 [**檢視說明**]。 您可能需要重新整理演算法清單，才能顯示此選項。
    - 檢閱建立的儀錶板，以瞭解哪些功能對目標值的影響最大。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免產生不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
