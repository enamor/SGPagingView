
## 前沿

* 新闻、电商、视频等 app 经常会看到这种 SegmentedControl 布局样式

* 很遗憾 SGPageView 不能进行 pods 管理，只能手动添加（已有开发者在 pods 中进行管理 SGPageView）


## 效果图

![](https://github.com/kingsic/SGPageView/raw/master/Gif/sorgle.gif) 


## 主要内容的介绍

* `多种指示器长度样式`<br>

* `标题按钮文字渐显效果`<br>

* `标题按钮文字缩放效果`<br>


## 代码介绍（详细使用, 请参考 Demo）

* 1、将项目中 SGPageView 文件夹拖入工程

* 2、导入 #import "SGPageView.h" 头文件
  
* 3、SGPageView 的使用步骤（在父视图的 viewDidLoad 中加入下面代码）

```Objective-C
    /// 说明：一定要加上这句代码，否则 pageContentView 会存在偏移量下移问题（v1.1.5之后不需要这句代码）
    self.automaticallyAdjustsScrollViewInsets = NO; 

    /// 子控制器及 pageContentView 的创建
    ChildVCOne *oneVC = [[ChildVCOne alloc] init];
    ChildVCTwo *twoVC = [[ChildVCTwo alloc] init];
    ChildVCThree *threeVC = [[ChildVCThree alloc] init];
    ChildVCFour *fourVC = [[ChildVCFour alloc] init];
    /// 子控制器数组
    NSArray *childVCArr = @[oneVC, twoVC, threeVC, fourVC];
    
    CGFloat contentViewHeight = self.view.frame.size.height - 108;
    self.pageContentView = [[SGPageContentView alloc] initWithFrame:CGRectMake(0, 108, self.view.frame.size.width, contentViewHeight) parentVC:self childVCs:childVCArr];
    _pageContentView.delegatePageContentView = self;
    [self.view addSubview:_pageContentView];
    
    
    /// 子标题及 pageTitleView 的创建
    NSArray *titleArr = @[@"精选", @"电影", @"电视剧", @"综艺"];
    
    self.pageTitleView = [SGPageTitleView pageTitleViewWithFrame:CGRectMake(0, 64, self.view.frame.size.width, 44) delegate:self titleNames:titleArr];
    [self.view addSubview:_pageTitleView];
```

* * 滚动内容视图的代理方法

```Objective-C
- (void)SGPageContentView:(SGPageContentView *)SGPageContentView progress:(CGFloat)progress originalIndex:(NSInteger)originalIndex targetIndex:(NSInteger)targetIndex {
    [self.pageTitleView setPageTitleViewWithProgress:progress originalIndex:originalIndex targetIndex:targetIndex];
}
```

* * 滚动标题视图的代理方法

```Objective-C
- (void)SGPageTitleView:(SGPageTitleView *)SGPageTitleView selectedIndex:(NSInteger)selectedIndex {
    [self.pageContentView setPageCententViewCurrentIndex:selectedIndex];
}
```


## 问题及解决方案（v1.1.5 之后不需要做处理）

#### 说明

* 父控制器中一定要加上 self.automaticallyAdjustsScrollViewInsets = NO; 这句代码；否则 pageContentView 会存在偏移量下移问题

* 子控制中使用纯代码创建 tableView 时，会存在内容区域显示问题

* 纯代码在 viewDidLoad 方法中创建 tableView 时，高度一定要等于 SGPageContentView 的高度或使用 Masonry 进行约束；

* XIB 创建 tableView 时，不会出现这种问题，是因为 XIB 加载完成之后会调用 viewDidLayoutSubviews 这个方法，所以 XIB 中创建 tableVIew 不会出现约束问题

#### 下面提供三种解决方案（仅供参考）

```Objective-C
- (void)viewDidLoad {
    [super viewDidLoad];
    
    /// 解决方案一
    self.tableView = [[UITableView alloc] initWithFrame:CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height - 108) style:UITableViewStylePlain];
    _tableView.dataSource = self;
    [self.view addSubview:self.tableView];

    /// 解决方案二
    self.tableView = [[UITableView alloc] initWithFrame:CGRectZero style:UITableViewStylePlain];
    _tableView.dataSource = self;
    [self.view addSubview:self.tableView];
    [self.tableView mas_makeConstraints:^(MASConstraintMaker *make) {
         make.edges.equalTo(self.view);
    }];
}
```

```Objective-C
- (void)viewDidLayoutSubviews {
    [super viewDidLayoutSubviews];

    /// 解决方案三
    [self.view addSubview:self.tableView];
}
```

```Objective-C
- (UITableView *)tableView {
    if (!_tableView) {
        _tableView = [[UITableView alloc] initWithFrame:self.view.frame style:UITableViewStylePlain];
        _tableView.dataSource = self;
    }
    return _tableView;
}
```


## 版本介绍

* 2016-10-7 --> 初始版本的创建

* 2017-4-13 --> 版本升级（根据标题内容自动适配 SGPageTitleView 是静止还是滚动）

* 2017-4-18 --> 新增标题文字颜色属性以及指示器颜色属性

* 2017-5-12 --> SGPageContentView 新增是否需要滚动属性

* 2017-6-1 --> v1.1.0 性能优化以及 Bug 修复（主要解决 SGPageTitleView 不可滚动时，标题中含有中、英文时出现的 Bug 问题）

* 2017-6-15 --> v1.1.5 新增新浪微博模块以及代码的优化


## Concluding remarks

* 如在使用中, 遇到什么问题或有更好建议者, 请记得 [Issues me](https://github.com/kingsic/SGPageView/issues) 或 kingsic@126.com 邮箱联系我

