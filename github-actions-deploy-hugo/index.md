# 使用GitHub Actions自动部署hugo静态网站

从了解开始使用[Hugo](https://gohugo.io/)开发个人网站以来，一直都是利用[TravisCI](https://travis-ci.org/)自动构建并部署到[GitHub Pages](https://docs.github.com/cn/pages)的服务上面。[TravisCI](https://travis-ci.org/)终于迎来关闭的一天,正好趁此机会用 [GitHub Actions](https://docs.github.com/cn/actions) 来替换 Travis CI。
## 创建代码仓库
首先按照文档创建 GitHub Pages 站点。该仓库可见性必须是 Public 
另外创建一个仓库用来存放 Hugo 的源文件，名称随意，这里假设仓库名叫 pages-hugo-source。建议将仓库可见性设置成 Private 以保护好你的源代码。
创建完毕后你的账户下将存在以下两个代码仓库：  
* https://github.com/\<YourName>/\<YourName>.github.io (公开的)  
* https://github.com/\<YourName>/pages-hugo-source (私有的)
## 创建 Workflow 配置
在 pages-hugo-source 仓库下新建 .github/workflows/hugo.yml 文件。内容如下：
```YAML
name: Generate Site
on:
  push:
    branches:
      - master
jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
      - name: Check out source
        uses: actions/checkout@v2
        
      - name: Setup hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "latest" # 可以修改为你使用的 Hugo 版本
          extended: true # 设置是否需要 extended 版本
          
      - name: Build
        run: hugo --minify
        
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.DEPLOY_TOKEN }} # 另外还支持 github_token 和 personal_token
          external_repository: <YourName>/<YourName>.github.io` # 修改为你的 GitHub Pages 仓库
          publish_dir: ./public
          keep_files: false
          publish_branch: master
          # 如果使用自定义域名，还需要添加下面一行配置
          # cname: www.fournoas.com
```
该配置用到了两个第三方 Actions，分别是 [Hugo setup](https://github.com/marketplace/actions/hugo-setup) 和 [GitHub Pages action](https://github.com/marketplace/actions/github-pages-action)。前者用于安装 Hugo，后者用于部署静态站点。
## 设置 SSH Key
GitHub Pages Actions 支持三种身份验证方式：
* deploy_key
* github_token
* personal_token  

执行命令创建 SSH Key：
```Shell
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
# You will get 2 files:
#   gh-pages.pub (public key)
#   gh-pages     (private key)
```
如果在 Windows 系统下运行该命令，要将命令中的 <font color='Coral'>$(git config user.email)</font> 替换成你的邮箱地址。  
访问如下地址设置 <font color='Coral'>Public key</font>：  
https://github.com/\<YourName>/\<YourName>.github.io/settings/keys/new  
表单中的 <font color='Coral'>Title</font> 随意填写，将刚才生成的 <font color='Coral'>gh-pages.pub</font> 文件内容填入 <font color='Coral'>Key</font> 中，勾选 <font color='Coral'>Allow write access</font>，点击 <font color='Coral'>Add key</font> 按钮保存。

访问如下地址设置 <font color='Coral'>Private key</font>：

https://github.com/\<YourName>/pages-hugo-source/settings/secrets/actions/new
表单中的 <font color='Coral'>Name</font> 填入 <font color='Coral'>DEPLOY_TOKEN</font>，将刚才生成的 <font color='Coral'>gh-pages</font> 文件内容填入 <font color='Coral'>Value</font> 中，点击 <font color='Coral'>Add secret</font> 按钮保存。
## 执行 GitHub Actions
将 pages-hugo-source 仓库的代码提交并推送到 GitHub，会自动触发 GitHub Actions 执行。  

可以访问如下网址来查看 Workflows  的执行状态：  
https://github.com/\<YourName>/pages-hugo-source/actions  

等待 workflow 执行完毕，静态站点就算是发布成功了。

{{< ypblogads >}}
