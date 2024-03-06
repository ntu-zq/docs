# 一、初始化git仓库
  
在GitHub上初始化一个项目，通常的步骤如下：

1. **登录 GitHub 账号：** 打开 [GitHub](https://github.com/)，确保你已经登录你的 GitHub 账号。
    
2. **创建新仓库（Repository）：** 在 GitHub 主页右上角，点击加号（+），然后选择 "New repository"。
    
3. **填写仓库信息：** 在新仓库的页面，填写以下信息：
    
    - **Repository name（仓库名称）：** 输入你项目的名称。
    - **Description（描述）：** 可选，提供对你项目的简短描述。
    - **Public/Private（公开/私有）：** 选择是公开还是私有（私有仓库需要付费账户）。
    - **Initialize this repository with a README（使用 README 初始化仓库）：** 选择是否初始化一个 README 文件。你可以选择初始化，这样你的仓库会包含一个默认的 README 文件。
    
4. **选择 .gitignore 文件和许可证（可选）：** 在创建仓库时，你可以选择添加一个 .gitignore 文件和一个许可证。这是可选的，根据你的项目需求进行选择。
    
5. **点击 "Create repository"：** 填写完信息后，点击 "Create repository" 完成创建。
    
6. **克隆仓库到本地：** 如果你已经有本地代码，可以使用 `git clone` 命令将仓库克隆到本地。在仓库的页面右上角，点击 "Code" 按钮，然后复制仓库的 URL。在本地终端中使用 `git clone` 命令：
    
    bashCopy code
    
    `git clone https://github.com/你的用户名/你的仓库名.git`
    
    将 URL 替换为你实际仓库的 URL。
    
7. **开始添加代码：** 将你的项目代码复制到克隆的本地仓库中，然后使用 `git add`、`git commit` 和 `git push` 等命令来提交更改到 GitHub。
    

这样，你就成功在 GitHub 上初始化了一个项目。请注意，上述步骤中的界面和功能可能会有一些变化，因为 GitHub 在不同的时期可能会更新其界面和功能。
# 二、ssh密钥
以下是一些步骤，指导你如何在 GitHub 上添加 SSH 密钥：

1. **生成 SSH 密钥：** 打开终端并输入以下命令（确保替换 `<your_email>` 为你的 GitHub 注册邮箱）：
    
    bashCopy code
    
    `ssh-keygen -t rsa -b 4096 -C "<your_email>"`
    
    这将生成一个 SSH 密钥对，其中包含公钥（`id_rsa.pub`）和私钥（`id_rsa`）。按照提示一路按 Enter 键（也可以自定义密码，但是每次拉代码都要输入，建议一路回车）。
    可以指定rsa名称，最后要在config文件中引用。
	    例如：
	    Host github.com
		  IdentityFile C:\Users\ZqWrold\.ssh\mygit_rsa
1. **查看并复制公钥：** 使用以下命令来查看公钥：
    
    bashCopy code
    
    `cat ~/.ssh/id_rsa.pub`
    
    复制输出的内容，它是你的 SSH 公钥。
    
3. **添加 SSH 密钥到 GitHub：** 登录到你的 GitHub 账户。在右上角点击头像，然后选择 "Settings"。
    
4. 在左侧导航栏中选择 "SSH and GPG keys"。
    
5. 点击 "New SSH key"。
    
6. 在 "Title" 字段中，为密钥起一个有描述性的名字，然后在 "Key" 字段中粘贴你复制的公钥。
    
7. 点击 "Add SSH key" 完成添加。
    

现在，你应该能够使用 SSH 克隆 GitHub 仓库或进行其他需要身份验证的操作了。在克隆时，确保使用 SSH URL 而不是 HTTPS URL。例如：

bashCopy code

`git clone git@github.com:your-username/your-repository.git`

请注意，SSH 密钥是敏感信息，应当妥善保管。