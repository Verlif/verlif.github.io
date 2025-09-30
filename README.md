## 站点配置纪要

本站点使用的是[Hexo](https://hexo.io/zh-cn/)工具，[Stellar](https://xaoxuu.com/wiki/stellar/#start)主题

### 站点图标

站点图标配置在`source`目录下，名为`favicon.ico`。

### 站点信息

站点信息放置于`_config.yml`文件中。

### 文章配置

文章文件放置于`source/_post`目录下，按照**category**做文件夹区分。

例如：

- `source/_post/tech`存放**category**为`tech`的文章。

包括专栏也是以**category**作为文件夹区分。

### 专栏

专栏信息放在`source/_data/topic`下，以`yml`后缀结尾。

其中的每一个文件都是一个专栏配置，其文件名就是对应专栏文章的`topic`参数。

例如：

- `notes.yml`的配置会对**topic**为`notes`的文章生效。

### 类型文章首页

新定义的地址添加首页需要在`source/_post`下进行其对应的文件夹，并在其文件夹中存放一个`index.md`文件作为首页内容。

例如：

- `source/_post/toys/index.md`文件存在时，可以通过访问`{{域名}}/toys`来显示这个文件内容。

### 文章图片

文章图片手动处理，这里不使用插件。将所有图片放于`source/image`目录下，并按照类型或文章进行目录分类（**必要**）。