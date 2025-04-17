---
lab:
  title: 在 Azure Machine Learning 中以命令作業身分執行定型指令碼
---

# 在 Azure Machine Learning 中以命令作業身分執行定型指令碼

筆記本非常適合用於實驗和開發。 一旦您開發機器學習模型並準備好進行生產環境後，即可使用指令碼來定型。 您可以執行指令碼做為命令作業。

在此練習中，您將測試指令碼，然後以命令作業的形式執行。

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
    cd azure-ml-labs/Labs/08
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
        <li>在 Azure Machine Learning 工作室 內，流覽至 <b>[計算] 頁面，然後選取 [計算</b>實例] 索引<b>卷標下的 [+</b><b>新增]。</b></li>
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

## 將筆記本轉換成指令碼

使用附加至計算執行個體的筆記本非常適合用於實驗和開發，因為其可讓您立即執行您已撰寫的程式碼並檢閱其輸出。 若要從開發移至生產環境，您將需要使用指令碼。 在第一個步驟中，您可以使用 Azure Machine Learning 工作室將筆記本轉換成指令碼。

1. 開啟 **Labs/08/src/Train classification model.ipynb** 筆記本。

    > 選取 [驗證]****，如果出現通知要求您進行驗證，請遵循必要的步驟。

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。
1. 執行所有儲存格以探索程式碼並定型模型。
1. 選取筆記本頂端的 &#9776; 圖示，以檢視**筆記本功能表**。
1. 展開 [匯出為]****，然後選取 [Python (.py)]****，將筆記本轉換成 Python 指令碼。
1. 將新檔案命名為 `train-classification-model`。
1. 建立新檔案之後，應該會自動開啟指令碼。 探索檔案並注意其包含與筆記本相同的程式碼。
1. 選取筆記本頂端的 &#9655;&#9655; 圖示，以**儲存並執行終端機中的指令碼**。
1. 指令碼是由 **python train-classification-model.py** 命令起始，且輸出應該會顯示在命令下方。

   > **注意：** 如果腳本傳回 libstdc++6 的 ImportError，請在終端機中執行下列命令，然後再再次執行腳本：
   > ```bash
   > sudo add-apt-repository ppa:ubuntu-toolchain-r/test
   > sudo apt-get update
   > sudo apt-get upgrade libstdc++6
   > ```

## 使用終端機測試指令碼

將筆記本轉換成指令碼之後，您可能需要進一步精簡。 使用指令碼時，其中一個最佳做法是使用函式。 當您的指令碼包含函式時，比較容易進行程式碼單元測試。 當您使用函式時，指令碼會由程式碼區塊組成，每個區塊都會執行特定工作。

1. 開啟 **Labs/08/src/train-model-parameters.py** 指令碼，並探索其內容。
    請注意，有一個包含其他四個函式的 main 函式：

    - 讀取資料
    - 分割資料
    - 定型模型
    - 評估模型

    在 main 函式之後，會定義每個函式。 請注意每個函式如何定義預期的輸入和輸出。

1. 選取筆記本頂端的 &#9655;&#9655; 圖示，以**儲存並執行終端機中的指令碼**。 您應該會在 [讀取資料...]**** 之後收到錯誤，告知因為檔案路徑無效，而無法取得資料。

    指令碼可讓您將程式碼參數化，以輕鬆變更輸入資料或參數。 在此情況下，指令碼會預期並未提供的資料路徑輸入參數。 您可以在 **parse_args()** 函式的指令碼結尾找到已定義及預期的參數。

    已定義兩個輸入參數：
    - **--training_data** 需要字串。
    - **--reg_rate** 需要數字，但預設值為 0.01。

    若要成功執行指令碼，您必須指定定型資料參數的值。 讓我們參考儲存在與定型指令碼相同資料夾中的 **diabetes.csv** 檔案來執行此動作。

1. 在終端中，執行下列命令：

    ```
    cd azure-ml-labs/Labs/08/src/
    python train-model-parameters.py --training_data diabetes.csv
    ```

指令碼應該會成功執行，因此輸出應該會顯示已定型模型的精確度和 AUC。

測試終端機中的指令碼很適合用來驗證指令碼是否如預期般運作。 如果程式碼發生任何問題，您將會在終端機中收到錯誤。

**或者**，編輯程式碼以強制執行錯誤，並在終端機中再次執行命令來執行指令碼。 例如，移除 **import pandas as pd** 這行，儲存並使用輸入參數執行指令碼，以檢閱錯誤訊息。

## 將指令碼當作命令作業執行

如果您知道指令碼的運作方式，可將其當做命令作業來執行。 您將能夠以命令作業的形式執行指令碼，追蹤指令碼的所有輸入和輸出。

1. 開啟 **Labs/08/Run script as command job.ipynb** 筆記本。
1. 執行筆記本中的所有儲存格。
1. 在 Azure Machine Learning 工作室中，瀏覽至 [作業]**** 頁面。
1. 瀏覽至 **diabetes-train-script** 作業，以探索您所執行命令作業的概觀。
1. 瀏覽至 [程式碼]**** 索引標籤。**src** 資料夾的所有內容 (命令作業 **code** 參數的值) 都會在這裡複製。 您可以檢閱命令作業所執行的定型指令碼。
1. 瀏覽至 [輸出 + 記錄]**** 索引標籤。
1. 開啟 **std_log.txt** 檔案並探索其內容。 此檔案的內容是命令的輸出。 請記住，當您在該處測試指令碼時，終端機中會顯示相同的輸出。 如果作業因為指令碼發生問題而失敗，則會在這裡顯示錯誤訊息。

**或者**，編輯程式碼以強制發生錯誤，並使用筆記本再次起始命令作業。 例如，從指令碼中移除 **import pandas as pd** 這行，然後儲存指令碼。 或者，編輯命令作業組態，以在作業組態本身 (而不是指令碼) 發生錯誤時探索錯誤訊息。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免產生不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
