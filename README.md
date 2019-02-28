## 说明
### 功能
根据关键词进行淘宝搜索，抓取搜索结果前三页商品详情页中的全部图片。

### 实现
#### 反爬机制
* 淘宝搜索结果界面访问频次过高或不带cookie会被重定向到人机验证界面，淘宝前端js有逻辑验证chrome是否为webdriver启动，故拖拽验证目前无法破解。
* 若爬虫cookie失效，需要手动打开淘宝搜索一个商品，将<a>https://s.taobao.com/search</a>接口request中的cookie复制粘贴到爬虫脚本中，重新启动即可。
* 商品详情页反爬会弹出登录框或拖拽验证框，但html中仍能得到图片，目前不做处理。
* 详情页图片为js加载，需要模拟用户滚动到每一张图片触发加载，否则得到的是图片可能会是假地址。
* 必要时是可以搭建selenium集群避免反爬，参考<a>https://github.com/SeleniumHQ/docker-selenium</a>。

#### 爬虫逻辑
淘宝爬虫基于selenium + chromedriver实现，并未使用scrapy框架。<br>
主要原因是爬虫需要严格限制淘宝搜索页的访问频率，scrapy框架可能会连续访问获取搜索结果，导致触发淘宝反爬机制。<br>
页面访问逻辑按照以下顺序进行：<br>
```
1. 访问淘宝首页，添加cookie
2. 关键词1的第1页搜索结果
3. 依次进入这一页的每个商品详情页，爬取全部图片
4. 关键词1的第2页搜索结果
5. 依次进入这一页的每个商品详情页，爬取全部图片
6. 关键词1的第3页搜索结果
7. 依次进入这一页的每个商品详情页，爬取全部图片
8. 关键词2的第1页搜索结果
9. 依次进入这一页的每个商品详情页，爬取全部图片<br>
......
```

这样做的好处是在保证不影响爬虫效率的前提下最大程度降低搜索页的访问频率（大约5分钟一次）。

另外，爬虫需要从txt文档读取关键词，格式如下：
```
雪碧汽水:雪碧+汽水
芬达汽水:芬达+汽水
美年达汽水:美年达+汽水
七喜汽水:七喜+汽水
百事可乐汽水:百事可乐+汽水
```