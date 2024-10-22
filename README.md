# algolia 搜索设置
要为您的站点启用和配置搜索功能，请按照下列步骤操作：

1. 在[https://www.algolia.com/](https://www.algolia.com/)注册免费的 Algolia 搜索帐户。  
2. 添加 `New Application` 。您可以选择 `COMMUNITY` 计划。  
3. 切换到 `Indices` 并创建一个新索引。  
4. 切换到API Keys并将您的 `Application ID` 、 `Search-Only API Key` 和选择的 `Index name` 复制到您的 `hugo.toml` 文件中。
5. 确保 `hugo.toml` 中的 `algolia_search` 参数设置为 **true** 。
6. 在站点根目录下执行 `hugo` 命令，生成 `index.json` 文件。
	- 手动上传  
		转到 `public/index.json` 文件并复制其内容。  
		登录您的 Algolia 帐户，打开索引，然后单击 `Add records manually` 。  
		粘贴从 `index.json` 文件复制的文本。  
		在索引的Browse选项卡中验证索引记录是否已正确上传。  
		如果您有多语言设置，请确保对所有 `public/{LANG}/index.json` 文件重复上述步骤。  
		
	- 自动上传  
		切换到 `algolia` 目录并通过执行以下命令安装所需的依赖项：  
		```
		cd algolia
		npm install
		```
		从algolia目录运行data-upload.js 
		```
		npm run data-upload -- -f ../public/index.json -a <algolia-app-id> -k <algolia-admin-api-key> -n <algolia-index-name>
		```
		algolia-admin-api-key参数，即您的 Algolia 帐户的 `Admin API Key` ，用于创建、更新和删除索引，应保密。  
		如果您想在开始新上传之前清除相应的 Algolia 索引，请添加 `-c` 或 `--clear-index` 选项。  
		登录您的 Algolia 帐户，并在索引的Browse选项卡中验证索引记录是否已正确上传。  
		如果您有多语言设置，请确保对所有public/{LANG}/index.json文件重复上述步骤。  
		
7. 要完成初始设置，请转到新创建的索引的 `Configuration` 选项卡，在 `FILTERING AND FACETING` 部分中选择 `Facets` ，然后在 `Attributes for faceting` 属性选项中添加带有 `filter only` 修饰符的 `language` 属性。如果添加language属性后，显示Unknown attribute错误，请忽略它。
