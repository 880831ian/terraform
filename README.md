# terraform

## 什麼是 IaC ?

IaC 全名是 Infrastructure as Code (基礎設施即代碼)，從字面意思就可以略知一二，也就是把基礎設施變成程式碼，在還沒有這些 IaC 工具之前，大家都是開啟 WEB UI 畫面來進行建置或設定，雖然使用 UI 點一點就建好了，但這些步驟都沒有被紀錄下來 (git)，也沒有辦法透過其他人一起 Review 的方式來避免人為操作錯誤。因此有了 IaC 這些工具，可以將實際的操作流程，轉換成程式碼或是其他像是 JSON、Yaml 的方式給紀錄下來，以下是導入 IaC 帶來的好處：

<br>

1. 建置 CI/CD 自動化 (不需要再仰賴 UI 進行操作)
2. 版本控制  (大家可以透過 MR 規定 code review，避免出現人為錯誤)
3. 重複使用 (可以將常用的變成參數代入，減少建置時間)
4. 環境一致性 

<br>

(以上 IaC 說明來自 [小惡魔 - 初探 Infrastructure as Code 工具 Terraform vs Pulumi ](https://blog.wu-boy.com/2021/02/introduction-to-infrastructure-as-code-terraform-vs-pulumi/)文章，寫得真的很好，推推)

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/iac.png)

<br>

## Terraform 又是什麼？ 

IaC 的工具有很多種，接著我們就來介紹其中一個工具 - Terraform，Terraform 是什麼呢？根據官網的說明可以知道，Terraform 是 [HashiCorp](https://www.hashicorp.com/) 所開發的基礎設施即代碼工具。它可以使用人類方便讀的配置文件來定義資源和基礎設施，以下有使用 Terraform 幾個優點：

<br>

1. Terraform 可以管理多個雲平台上的基礎架構
2. 使用人類可讀的配置語言來幫助我們快速編寫基礎設施代碼
3. 可以將配置提交給版本控制，安全地在基礎架構上進行協作

<br>

### 管理任何基礎設施

Terraform 提供插件讓 Terraform 可以通過其 API 與雲平台和其他服務進行交互。HashiCorp 和 Terraform 社區編寫了 3193 多個提供商來管理像 AWS、Azure、GCP、Kubernetes、Helm、GitHub 等資源，可以到 [Terraform Registry](https://registry.terraform.io/browse/providers?_gl=1*a2tlfj*_ga*MTc0NDA4ODQ5MC4xNjYzOTI0ODI5*_ga_P7S46ZYEKW*MTY2NDE4MjM4NC40LjEuMTY2NDE4Mjc5Ny4wLjAuMA..) 查看更多平台或服務的提供者，當然如果沒有找到想要的提供者，也可以自己編寫自己的套件。

<br>

### 標準化部署工作流程

提供商會將基礎設施的每個單元 (例如建立 VM 或是 VPC) 定義為資源。你可以將來自不同提供者的資源組合，變成模組，讓我們可以用一致的語言和工作流程去管理他們。

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/terraform.jpg)

<br>

## 安裝 Terraform

安裝 Terraform 的方式有很多種，我就以我在使用的 Mac OS 為例，其他可以參考 [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started#install-terraform)：

<br>

### 安裝步驟

1. 先安裝 HashiCorp tap，這是 HashiCorp 在 Homebrew 的儲存庫：

```
brew tap hashicorp/tap
```

<br>

2. 使用 hashicorp/tap/terraform

```
brew install hashicorp/tap/terraform
```

<br>

### 如何驗證是否安裝成功

打開一個新的 Terminal，使用 `terraform -help` 檢查是否有安裝成功，也可以在 `-help` 後面加入參數來查看該參數的功能與更多訊息

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/1.png)

<br>

### 自動補全指令

可以啟動終端機上的 Tab 自動補全功能，執行以下指令，再重開終端機，就會出現了：

```
terraform -install-autocomplete
```

<br>

想要解除自動補全 (雖然應該不會拉)，執行以下指令：

```
terraform -uninstall-autocomplete
```

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/8.png)





<br>

### 快速入門

當我們安裝好，想要最快的了解 Terraform ，當然是自己動手做一次，我們依照官網的教學，可以在一分鐘內使用 Docker 配置好 NGINX 伺服器，那我們開始囉！

<br>

1. 首先，我們必須要先安裝好 Docker，下載 [Mac 版 Docker 桌面](https://docs.docker.com/desktop/install/mac-install/)
2. 建立一個資料夾，並進入該資料夾內
3. 將以下 Terraform 配置文件貼到檔案中，並取名 `main.tf`：([同步到 GitHub 需要程式碼的可以前往查看](https://github.com/880831ian/terraform/tree/master/docker_container))

```
provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:1.23"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.name
  name  = "nginx"
  ports {
    internal = 80
    external = 8000
  }
}
```

<br>

4. 再開一個檔案取名為 `provider.tf`，將以下配置文件貼到檔案中：

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.13.0"
    }
  }
}
```
(以上程式碼來自官網 [安裝 Terraform#快速入門](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started#quick-start-tutorial) 加上小修改)

<br>

先來簡單說明一下 Terraform 程式碼格式，Terraform 的檔案副檔名是 `*.tf`，採用名為 HCL (HashiCorp Configuration Language) 的組態語言來描述基礎架構。

(補充說明：只要是同一個目錄下有 .tf 檔案結尾的，Terraform 都會去執行，所以檔案名稱可以自己取名，但為了方便管理都會使用 main、provider、backend、output 的檔案來放置對應的內容)

HCL 是一種宣告式的語言，讓你直接寫下期望的基礎架構，而不是寫下過程的每一個步驟。

<br>

#### 檔案介紹


我們先看 `provider.tf` 檔案，檔案內會先寫好需要的供應商來源以及版本 (版本有點像是對應供應商提供的 api 版本)

```
terraform {
  required_providers {
    供應商名稱 = {
      source  = "供應商來源"
      version = "~> 所使用的版本"
    }
  }
}
```

<br>

`main.tf` 檔案內的 `provider` 區塊會寫供應商的相關設定，假設我們使用 google 就會在裡面先設定好 project id 等。

`resource` 區塊會需要寫雲端資源名稱以及自定義的名稱，雲端資源名稱這項是不可以更改的，假設我們要使用 docker 的 container 服務，這邊就需要填寫 docker_container。自定義的名稱可以是你想要為使用這個雲端資源去定義的名稱。

```
provider "供應商名稱" {}

resource "雲端資源名稱" "自定義的名稱" {
 	 屬性 = 值
}
```

<br>

所以我們已上面的 Docker 配置好 NGINX 伺服器為例，provider 我們這次使用的是 `docker`， resource 我們可以拆開來寫，

像是第一個 resource `docker_image` 我們幫他取叫 nginx，裡面就是放有關 image 的設定，詳細的 image 設定可以參考 [Resource (docker_image)](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/image)，

第二個 resource `docker_container` 一樣叫 nginx，裡面用的 image 是拿前面的 docker_image resource name 來使用，一樣詳細可以參考 [Resource (docker_container)](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container)。

<br>

小提醒，如果不知道要怎麼寫 provider 供應商設定，可以打開 [terraform 官網找到該供應商](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container)，點選右邊的 USE PROVIDER 

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/11.png)

<br>

可以看到官方教學要怎麼使用這個供應商。

<br>


#### 指令說明

接著有幾個指令要帶大家認識：


* `terraform init`：初始化項目，下載 tf 檔案中所需要的外掛套件
* `terraform plan`：會產生一份執行計劃。上面會寫著它將會做哪些事，你可以驗證是否符合你預期的設計
* `terraform apply`：實際運作，把基礎架構建置完成。在完成之後，會把目前的狀態儲存到一份檔案中 (*.tfstate)
* `terraform destroy`：會銷毀用 Terraform 起的服務
* `terraform fmt`：幫你整理好 tf 文件
* `terraform validate`：靜態檢查 tf 文件


附上懶人指令
```
alias ti='terraform init'
alias ta='terraform apply'
alias tp='terraform plan'
alias td='terraform destroy'
```

<br>

由於在 apply 的時候會跳出詢問視窗，如果是要寫成腳本，可以把指令改成 `terraform apply -auto-approve` 就不需要輸入 yes 了！

<br>

#### 實際操作

有上面的指令後，我們來實際操作看看：

<br>

1. 首先到該 `main.tf` 檔案目錄下，先使用 `terraform apply` 來測試看看：

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/2.png)

<br>

會發現沒有辦法直接用 `terraform apply` 指令來建置服務，我們看一下他提示的說明，他說他找不到 lock file，需要先進行初始化才可以執行，所以我們的建置流程是先 init --> apply

<br>

##### terraform init

2. 我們先執行 `terraform init`，可以看到他會下載 tf 檔案中所需要的外掛套件 (docker)

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/3.png)

當我們初始化後，資料夾會多一個檔案 (.terraform.lock.hcl) 以及資料夾 (.terraform)

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/9.png)

<br>

`.terraform.lock.hcl`：是 Terraform 中用於鎖定和管理外部提供者（providers）版本的檔案。它的主要功能是確保在不同的環境中使用相同的外部提供者版本，以避免在團隊合作或不同環境中引入不一致性和問題。

`.terraform/`：資料夾主要用於存儲初始化和管理基礎架構相關的臨時文件。

<br>

##### terraform plan

3. 接著我們使用 `terraform plan` 來查看我們的計劃，可以看到他會列出我們所寫的 tf 裡面有用到的 resource，除了我們有設定的屬性，其他的屬性也會顯示出來，可以更方便地讓我們知道這個 resource 有哪些屬性可以設定

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/4.png)

<br>

##### terraform apply

4. 最後我們檢查都沒有問題，就可以使用 `terraform apply` 來建置，apply 其實跟 plan 一樣都會先讓我們看一下計劃，但會跳出詢問是否要執行，除非你輸入 `yes`，否則就跟 plan 單純顯示計劃內容，最後我們就可以看到他成功在 docker 上面建立 nginx 服務

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/5.png)
![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/6.png)

<br>

當我們 apply 完，服務也建立後，查看一下資料夾後會發現，又多了一個檔案 `terraform.tfstate`：

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/10.png)

<br>

`terraform.tfstate`： 是 Terraform 的狀態檔案，它包含了基礎架構的狀態和資源的詳細信息。預設情況下，這個檔案是本地的並且只存在於 Terraform 初始化和操作的目錄中。(但要如何實現共同維護同一個 IaC 呢，敬待後續分享 🤣)

<br>

##### terraform destory

5. 另外，當你想要移除服務時，可以使用 `terraform destroy` 來將服務給移除

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/7.png)

<br>

##### terraform import

6. 最後還有一個蠻重要的，就是我們已經有很多服務都是使用 WEB UI 方式建立的服務，那我們要怎麼把它變成 tf 檔案呢？

跟我們剛剛說的 `terraform.tfstate` 檔案有關，他會儲存我們 IaC 的狀態，所以我們才可以透過他知道現在是對資源做新增、修改、刪除哪個操作

那當這個檔案不見時，如果再重新下 `terraform apply`，他會認為你是新增狀態，但實際上 docker 服務還是啟動的狀態，所以就會錯誤，會跟你說他已經存在。

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/12.png)

<br>

所以這時候我們要把線上服務的資源轉成 tf ，第一步要先把 resource 的框架給寫出來，其他可以先留空白，如下 `main.tf`：

```
provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:1.23"
  keep_locally = false
}

resource "docker_container" "nginx" {

}
```

<br>

接著使用 `terraform import` 來將線上服務的資源套用到我們 main.tf 裡面的 resource，所以會長得像：

```
terraform import docker_container.nginx 7f363ea3f6a64b5432ae3627f490b3e297abf80f196bce9c028ec2eb82706f12
```

import 後面會加上 main.tf resource 名稱 docker_container，以及我們取名的 nginx，最後帶 container id (`docker ps` 查詢)，就可以匯出 terraform.tfstate 檔案囉，詳細的 import 可以參考[每個供應商資源的網頁(這邊以 docker 為例)](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container#import)

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/13.png)

<br>

##### terraform show

當我們匯出後，可以看一下 terraform.tfstate，它會是一個 json 格式，如果要轉成 tf 格式，還需要使用 `terraform show` 來將 terraform.tfstate 檔案轉成 tf 格式，如下：

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/14.png)

<br>

如果透過上述的方式來轉成 tf，會發現在重新 apply 時，會出現錯誤，這邊以 gce 的當範例，轉完的 tf 設定，有些是 tf 不支援的參數，只會顯示在 tfstate，所以還需要手動刪除。

<br>

![圖片](https://raw.githubusercontent.com/880831ian/terraform/master/images/15.png)

<br>

## 後續文章

經過上面的步驟，了解 IaC Infrastructure as Code (基礎設施即代碼) 以及實際去使用 Terraform 去建置 docker 的 nginx 服務，之後還會更新有關 Terraform 的文章，會不定時更新此篇文章的後續文章連結區，大家有興趣可以多留意歐！

[使用 Terraform 建立 Google Compute Engine](https://blog.pin-yi.me/terraform-gce/)

[使用 Terraform 建立 Google Kubernetes Engine](https://blog.pin-yi.me/terraform-gke/)


<br>

## 參考資料

[初探 Infrastructure as Code 工具 Terraform vs Pulumi](https://blog.wu-boy.com/2021/02/introduction-to-infrastructure-as-code-terraform-vs-pulumi/)

[今晚我想認識 Terraform](https://ithelp.ithome.com.tw/articles/10233759)