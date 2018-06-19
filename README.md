## hexo命令

hexo new "postName" #新建文章，缩写为：hexo n
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录,,缩写为：hexo g
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）,缩写为：hexo s
hexo s -p 8080  #本地预览的时候修改端口为8080
hexo s -debug #本地预览调试
hexo deploy #部署到GitHub，缩写为：hexo d
hexo clean  # 清除缓存文件 db.json 和已生成的静态文件 public
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
---------------以下为组合命令----------------------------------
hexo s -g  #生成并本地预览
hexo d -g  #生成并上传

## 通过脚本部署
deploy.sh