---
lab:
  title: 使用 MLflow 記錄和登錄模型
---

# 使用 MLflow 記錄和登錄模型

MLflow 是一個開放原始碼平台，可供您管理端對端機器學習生命週期。 當您使用 MLflow 記錄模型時，可以輕鬆地跨平台和工作負載移動模型。

在此練習中，您將使用 MLflow 來記錄機器學習模型。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning 工作區** 會提供一個集中位置，用來管理您定型和管理模型所需的所有資源和資產。 您可以透過工作室、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。

您將使用 Azure CLI 來佈建工作區和必要的計算，以及將使用 Python SDK 來執行命令作業。

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
    cd azure-ml-labs/Labs/10
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
        <li>在 Azure Machine Learning 工作室 中，流覽至 <b>[計算</b>] 頁面，然後選取 [計算實例] 索引<b>卷標下的 [+</b><b>新增</b>]。</li>
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

## 從筆記本提交 MLflow 作業

現在您已擁有所有必要的資源，您可以執行筆記本，以使用 MLflow 來定型和記錄模型。

1. **使用 MLflow.ipynb 筆記本開啟 Labs\10\Log 模型**。

    > 選取 [驗證]****，如果出現通知要求您進行驗證，請遵循必要的步驟。

1. 確認筆記本使用 **Python 3.10 - AzureML** 核心。
1. 執行筆記本中的所有儲存格。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免產生不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
