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

## 博客搭建及维护

#### 一、关于搭建的流程
1. 创建仓库，http://CrazyMilk.github.io；
2. 创建两个分支：master 与 hexo；
3. 设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）；
4. 使用git clone git@github.com:CrazyMilk/CrazyMilk.github.io.git拷贝仓库；
5. 在本地http://CrazyMilk.github.io文件夹下通过Git bash依次执行npm install hexo、hexo init、npm install 和 npm install hexo-deployer-git（此时当前分支应显示为hexo）;
6. 修改_config.yml中的deploy参数，分支应为master；
7. 依次执行git add .、git commit -m "..."、git push origin hexo提交网站相关的文件；
8. 执行hexo g -d生成网站并部署到GitHub上。

这样一来，在GitHub上的http://CrazyMilk.github.io仓库就有两个分支，一个hexo分支用来存放网站的原始文件，一个master分支用来存放生成的静态网页。完美( •̀ ω •́ )y！

#### 二、关于日常的改动流程
在本地对博客进行修改（添加新博文、修改样式等等）后，通过下面的流程进行管理。
1. 依次执行git add .、git commit -m "..."、git push origin hexo指令将改动推送到GitHub（此时当前分支应为hexo）；
2. 然后才执行hexo g -d发布网站到master分支上。虽然两个过程顺序调转一般不会有问题，不过逻辑上这样的顺序是绝对没问题的（例如突然死机要重装了，悲催....的情况，调转顺序就有问题了）。

#### 三、本地资料丢失后的流程
当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：
1. 使用git clone git@github.com:CrazyMilk/CrazyMilk.github.io.git拷贝仓库（默认分支为hexo）；
2. 在本地新拷贝的http://CrazyMilk.github.io文件夹下通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。


![](http://omoq9b9a3.bkt.clouddn.com/18-6-19/34128044.jpg)
