# folia——第三方网易云美化的部署与微调

项目原仓库:[folia-major](https://github.com/chthollyphile/folia-major*)

### 部署原因
作为网易云重度使用用户,平日确实能忍受其臃肿的UI和无用的功能（毕竟是放在手机后台播放的）
但当在有全屏播放展示需求下，各个网易云第三方客户端倒是成了不错的选择。
本人之前用过[yesplaymusic](https://github.com/qier222/YesPlayMusic)以及其修改版[R3PLAY](https://github.com/Mtoly/r3play)。项目整体启动快，界面简洁清爽，歌词参考了apple music的风格。
不过多少是看腻了，在B站看到folia的歌词动画和UI后便心动，遂部署

### 初部署







先安装最基础的node.js等基础工具
``` bash
# 更新系统包列表
sudo apt update

#安装 Node.js和npm
sudo apt install -y nodejs npm git

# 安装 Node 材质是否成功安装
node -v
npm -v
```
这些搞定之后再去git网易云的api和folia的项目
``` bash
git clone https://github.com/NeteaseCloudMusicApiEnhanced/api-enhanced.git #克隆 API 项目的代码
cd api-enhanced
npm install #安装依赖
ENABLE_GENRAL-UNBLOCK=flase node app.js #根据文档要求，遂将环境变量调为flase,其他linux版本部署或者操作系统版本参照安装手册
```
此时的网易云api(api-enhanced)就跑起来了，能在http://localhost:3000上看到网易云api的界面，就算大功告成，folia中的 'VITE_NETEASE_API_BASE’就算部署到位了，如果没有，参考终端的提示端口，以及显示的错误，去看手册。
接下来就是folia的部署,先退出之前的api-enhanced目录，回到主目录，然后克隆代码，安装依赖，编辑环境变量
``` bash
cd ~
git clone https://github.com/chthollyphile/folia-major.git
cd folia-major
cpm install
nano .env.local
```
在.env.local中，编辑环境变量
不管要不要不调用ai做ai主题时，都要填写下面两个环境变量
``` bash
VITE_NETEASE_API_BASE = http://localhost:3000
# 推荐写本机的IP而非localhost,虽然多了IP改变时需要重新调整的麻烦，但是可以做到在局域网内其他设备直接访问时调用网易云的api，若使用localhost会导致用其他设备访问该网页时API无法调用（调用的使用端的IP，无法调用）
VITE_AI-PROVIDER = none
```
若要调用除此之外还支持ai做ai主题，添加如下填写，以deepseek的api为例**
(openai同理，除此之外还支持gemini,但是要另外调环境变量)
> **注意** 若要实现在本地服务器上接入AI身成AI主题，需要在folia-major文件夹内安装versel,且后续项目启动时的指令也会改变，部分文件需要修改，会在此章末尾提及
``` bash
# 1,前端识别变量
VITE_AI_PROVIDER =openai # deepseek也填openai
VITE_OPENAI_API_KEY = 你的DeepSeekKey/OpenaiKey
VITE_OPENAI_API_URL = https://api.deepseek.com 
VITE_OPENAI_API_MODEL = deepseek_flash_v4

# 2,后端severless 函数识别变量，用于vercel侧调用ai api
OPENAI_API_KEY = 你的DeepSeekKey/OpenaiKey
OPENAI_API_URL = https://api.deepseek.com 
OPENAI_API_MODEL = deepseek_flash_v4
```
配置好环境变量后，若没有配置AI api用于生成主题，则使用
``` bash
npm run dev -- --port 5173
```
来启动folia
若配置了ai api，则使用
``` bash
npm run dev:vercel -- --port 5173
```
用于启动folia
#### 若要使用AI主题，还有以下操作：
先在vercel官网上注册账号（最好以gihub形式注册），在gibhub的自己的账号下fork了folia的仓库后，在vercel内托管该项目，在vercel项目设置的enviroment variables 中按照上文中在.env.local配置的内容区配置环境变量，然后在终端安装vercel工具：
``` bash 
# 在folia-major根目录下运行
s npm i -D vercel
vercel dev
```
登陆你的账号，绑定仓库
