---
title: UICollectionView
date: 2016-07-07 10:31:23
tags:
categories: UI
---

* 基础要点
	1. UICollectionView的dataSource、delegate
	2. UICollectionView多组数据和单组数据的展示
	3. UICollectionView、UICollectionViewFlowLayout的常见属性
	4. UICollectionViewCell三种注册方式（Class、Nib、storyboard）
	5. 自定义UICollectionViewFlowLayout布局
	
## 一、数据源和代理方法
#### 1、UICollectionViewDataSource（数据源）
* 有多少组
	```objc
	- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView
	```
* 有多少行（@required）
	```objc
	- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section;
	```
* cell（@required）
	```objc
	- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath;
	```
* 返回collecctionView的头尾部视图
	```objc
	- (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath;
		
		- (nullable UICollectionViewLayoutAttributes *)layoutAttributesForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath;
	当自定义loyout布局对象时，需重写方法2设置布局属性，否则无法显示
	```
* Move
	```objc
	- (BOOL)collectionView:(UICollectionView *)collectionView canMoveItemAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(9_0);
	
	- (void)collectionView:(UICollectionView *)collectionView moveItemAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath*)destinationIndexPath NS_AVAILABLE_IOS(9_0);
	```
	
#### 2、UICollectionViewDelegate（代理）
* Selected
	```objc
	- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath;
	```
		
#### 3、UICollectionViewFlowLayout
###### 见案例 

## 二、UICollectionViewController
#### 要点把握
```objc
self.view 和 self.collectionView 代表的是不同类型的, self.collectionView 才是  collectionView

self.collectionViewLayout  只读的
self.collectionView.collectionViewLayout 如果使用需要进行一次强转
```
* 组头组尾的时候, 必须通过数据源方法
* 如果在storyboard 中拖拽 组头的话,  必须设置重用标识符, 不然数据源方法不会执行

* 必须通过这个方法返回 
	```objc
	- (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath
	```
* kind类型
	```objc
	UICollectionElementKindSectionHeader  页眉
	UICollectionElementKindSectionFooter  页脚
	```
* 悬浮效果
	```objc
	flowLayout.sectionHeadersPinToVisibleBounds = YES;
	flowLayout.sectionFootersPinToVisibleBounds = YES;
	```
* 设置组头/尾 的size
	```objc
	flowLayout.headerReferenceSize = CGSizeMake(10, 10);
	flowLayout.footerReferenceSize = CGSizeMake(10, 20);
	```
## 三、案例：自定义collectionViewFlowLayout多组数据，自定义头尾部视图
#### FlowLayout
```objc
#import "ZCFlowLayout.h"
/** 列数 */
#define KColumnCount 5
/** 当item为图片是，一张图片行列方法正好盖住几个文字item */
#define KChangeCount 2

@interface ZCFlowLayout ()
/** 保存所有item属性的数组 */
@property (nonatomic, strong) NSMutableArray *itemsArray;
//保存每次计算的最大Y值
@property (nonatomic, assign) CGFloat maxY;
@end

@implementation ZCFlowLayout

- (void)prepareLayout {
    [super prepareLayout];
    UIEdgeInsets insets = UIEdgeInsetsMake(0, ZCFoodSectionInset, ZCFoodSectionInset, ZCFoodSectionInset);
    self.sectionInset = insets;
    //起始头部View有个间距
    CGFloat viewH = ZCFoodHeaderViewH;
    CGFloat viewW = ZC_SCREEN_WIDTH;
    CGFloat bottomViewH = ZCFoodBottomViewH;
    CGFloat viewX = 0;
    CGFloat viewY = 0;
    CGFloat startY = viewH;
    //行列间距
    CGFloat rowMargin = self.minimumLineSpacing;
    CGFloat colMargin = self.minimumInteritemSpacing;
    //计算item的宽高
    CGFloat itemW = (ZC_SCREEN_WIDTH - insets.left - insets.right - colMargin * (KColumnCount - 1)) / KColumnCount;
    CGFloat itemH = ZCFoodHeaderViewH;
    //计算图片宽高
    CGFloat imageW = KChangeCount * itemW + rowMargin * (KChangeCount - 1);
    CGFloat imageH = KChangeCount * itemH + colMargin * (KChangeCount - 1);
    NSInteger sectionCount = [self.collectionView numberOfSections];
    for (int i = 0; i < sectionCount; i++) {
        //组头部View属性
        NSIndexPath *indexPath = [NSIndexPath indexPathForItem:0 inSection:i];
        UICollectionViewLayoutAttributes *viewAttrs = [UICollectionViewLayoutAttributes layoutAttributesForSupplementaryViewOfKind:UICollectionElementKindSectionHeader withIndexPath:indexPath];
        viewAttrs.frame = CGRectMake(viewX, viewY, viewW, viewH);
        [self.itemsArray addObject:viewAttrs];
        
        NSInteger itemCount = [self.collectionView numberOfItemsInSection:i];
        for (int j = 0; j < itemCount; j++) {
            NSIndexPath *indexPath = [NSIndexPath indexPathForItem:j inSection:i];
            UICollectionViewLayoutAttributes *itemAttribute =[UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexPath];
            //计算行列索引
            NSInteger num = (KColumnCount - KChangeCount) * KChangeCount;
            NSInteger row = (j - num - 1) / KColumnCount + KChangeCount;
            NSInteger col = (j - num - 1) % KColumnCount;
            //计算item的X/Y值
            CGFloat itemX = insets.left + (itemW + rowMargin) * col;
            CGFloat itemY = startY + insets.top + (itemH + colMargin) * row;
            //图片
            if (j == 0) {
                itemX = insets.left;
                itemY = startY + insets.top;
                itemAttribute.frame = CGRectMake(itemX, itemY, imageW, imageH);
                [self.itemsArray addObject:itemAttribute];
                continue;
            }
            if (j <= num) {
                //重新计算行列索引
                row = (j - 1) / (KColumnCount - KChangeCount);
                col = (j - 1) % (KColumnCount - KChangeCount);
                //计算item的X/Y值
                itemX = insets.left + imageW + rowMargin + (itemW + rowMargin) * col;
                itemY = startY + insets.top + (itemH + colMargin) * row;
            }
            itemAttribute.frame = CGRectMake(itemX, itemY, itemW , itemH);
            [self.itemsArray addObject:itemAttribute];
            _maxY = CGRectGetMaxY(itemAttribute.frame);
        }
        indexPath = [NSIndexPath indexPathForItem:itemCount inSection:i];
        viewAttrs = [UICollectionViewLayoutAttributes layoutAttributesForSupplementaryViewOfKind:UICollectionElementKindSectionFooter withIndexPath:indexPath];
        viewAttrs.frame = CGRectMake(0, _maxY + insets.bottom, viewW, bottomViewH);
        [self.itemsArray addObject:viewAttrs];
        _maxY = _maxY + insets.bottom + bottomViewH;
        viewY = _maxY;
        startY = _maxY + viewH + insets.top;
    }
    self.collectionView.contentSize = CGSizeMake(0, _maxY);
}

- (CGSize)collectionViewContentSize {
    return self.collectionView.contentSize;
}

- (NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect {
    return self.itemsArray;
}

- (NSMutableArray *)itemsArray {
    if (!_itemsArray) {
        _itemsArray = [NSMutableArray array];
    }
    return _itemsArray;
}
@end
```

#### ViewController
```objc
#import "ZCFoodViewController.h"
#import "ZCFlowLayout.h"
#import "ZCCategoryModel.h"
#import "ZCCollectionHeaderView.h"
#import "ZCImageCell.h"
#import "ZCWordCell.h"
#import "ZCMaterialDescriptionViewController.h"
#import "ZCFavoriteViewController.h"
#import "ZCFoodTherapyViewController.h"

#import "ZCCategoryModel.h"
#import "ZCFoodModel.h"

@interface ZCFoodViewController ()<UICollectionViewDelegateFlowLayout>
@property (nonatomic, weak) ZCFlowLayout *flowLayout;
@property (nonatomic, strong) NSArray *dataArray;
@end
static NSString *const headerIdentifier = @"header";
static NSString *const footerIdentifier = @"footer";
static NSString *imageId = @"imageCell";
static NSString *wordId = @"wordCell";
@implementation ZCFoodViewController
// 初始化自动设置流水布局
- (instancetype)init
{
    ZCFlowLayout *flowLayout = [[ZCFlowLayout alloc] init];
    self.flowLayout = flowLayout;
    self = [super initWithCollectionViewLayout:flowLayout];
    if (self) {}
    return self;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    // 设置CollectionView
    [self setupCollectionView];
    //添加刷新控件
    [self addRefresh];
}
// 设置CollectionView
- (void)setupCollectionView {
    self.collectionView.backgroundColor = [UIColor whiteColor];
    // 注册cell
    [self.collectionView registerNib:[UINib nibWithNibName:NSStringFromClass([ZCImageCell class]) bundle:nil] forCellWithReuseIdentifier:imageId];
    [self.collectionView registerNib:[UINib nibWithNibName:NSStringFromClass([ZCWordCell class]) bundle:nil] forCellWithReuseIdentifier:wordId];
    // 注册section头／尾
    [self.collectionView registerClass:[ZCCollectionHeaderView class] forSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:headerIdentifier];
    [self.collectionView registerClass:[UICollectionReusableView class] forSupplementaryViewOfKind:UICollectionElementKindSectionFooter withReuseIdentifier:footerIdentifier];
    self.collectionView.contentInset = UIEdgeInsetsMake(64, 0, 49, 0);
    self.collectionView.scrollIndicatorInsets = UIEdgeInsetsMake(64, 0, 49, 0);
    self.flowLayout.minimumInteritemSpacing = 10;
    self.flowLayout.minimumLineSpacing = 10;
}
//添加刷新控件
- (void)addRefresh {
    self.collectionView.mj_header = [MJRefreshNormalHeader headerWithRefreshingTarget:self refreshingAction:@selector(loadNewData)];
    //加载数据
    [self.collectionView.mj_header beginRefreshing];
}
// 获取数据
- (void)loadNewData {
    // 设置请求参数
    NSMutableDictionary *parameters = [NSMutableDictionary dictionary];
    parameters[@"methodName"] = self.methodName;
    parameters[@"version"] = @4.4;
    [[ZCNetWorking sharedInstance] postWithUrlString:@"http://api.izhangchu.com/?appVersion=4.4&sysVersion=9.3.2&devModel=iPhone" withParameter:parameters withComplection:^(NSDictionary *responseObject) {
        self.dataArray = [ZCCategoryModel mj_objectArrayWithKeyValuesArray:responseObject[@"data"][@"data"]];
        [self.collectionView reloadData];
        [self.collectionView.mj_header endRefreshing];
    } withFailure:^(NSError *error) {
        [self.collectionView.mj_header endRefreshing];
        [SVProgressHUD showErrorWithStatus:@"加载失败"];
    }];
}
#pragma mark - UICollectionViewDataSource
- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView {
    return self.dataArray.count;
}

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    NSInteger count = [[self.dataArray[section] foodArray] count];
    return  count ? count + 1 : 0 ;
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    if (indexPath.item) {
        ZCWordCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:wordId forIndexPath:indexPath];
        ZCFoodModel *model = [self.dataArray[indexPath.section] foodArray][indexPath.item - 1];
        cell.textLabel.text = model.text;
        return cell;
    }
    ZCImageCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:imageId forIndexPath:indexPath];
    ZCCategoryModel *model = self.dataArray[indexPath.section];
    [cell.iconImageView sd_setImageWithURL:[NSURL URLWithString:model.image]];
    return cell;
}

- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    if (indexPath.item) {
        ZCFoodModel *model = [self.dataArray[indexPath.section] foodArray][indexPath.item - 1];
        if ([self.methodName  isEqual: @"MaterialSubtype"]) {
            //食疗
            ZCMaterialDescriptionViewController *descVc = [[ZCMaterialDescriptionViewController alloc] init];
            descVc.title = model.text;
            descVc.ID = model.ID;
            [self.navigationController pushViewController:descVc animated:YES];
            return;
        }
        if ([self.methodName  isEqual: @"CategoryIndex"]) {
            //分类
            if (model.type == 1) {
                ZCFavoriteViewController *favoriteVc = [[ZCFavoriteViewController alloc] init];
                favoriteVc.title = model.text;
                NSMutableDictionary *parameters = [NSMutableDictionary dictionary];
                parameters[@"size"] = @20;
                parameters[@"page"] = @1;
                parameters[@"methodName"] = @"CategorySearch";
                parameters[@"cat_id"] = model.ID;
                parameters[@"type"] = @1;
                parameters[@"version"] = @4.4;
                favoriteVc.parameters = parameters;
                [self.navigationController pushViewController:favoriteVc animated:YES];
                return;
            }
            if (model.type == 2) {
                //食疗
                ZCFoodTherapyViewController *thrapyVc = [[ZCFoodTherapyViewController alloc] init];
                thrapyVc.department_id = model.ID;
                [self.navigationController pushViewController:thrapyVc animated:YES];
            }
        }
    }
}

//设置头部/尾部视图
- (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath {
    if ([kind isEqualToString:UICollectionElementKindSectionHeader]) {
        ZCCollectionHeaderView *headerView = [collectionView dequeueReusableSupplementaryViewOfKind:kind withReuseIdentifier:headerIdentifier forIndexPath:indexPath];
        ZCCategoryModel *model = self.dataArray[indexPath.section];
        headerView.titleLabel.text = model.text;
        return headerView;
    } else {
        UICollectionReusableView *footerView = [collectionView dequeueReusableSupplementaryViewOfKind:kind withReuseIdentifier:footerIdentifier forIndexPath:indexPath];
        footerView.backgroundColor = ZC_GLOBAL_COLOR;
        return footerView;
    }
}

#pragma mark - UICollectionViewDelegateFlowLayout
//sectionHeaderSize
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout referenceSizeForHeaderInSection:(NSInteger)section {
    return CGSizeMake(0, ZCFoodHeaderViewH);
}
//sectionFooterSize
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout referenceSizeForFooterInSection:(NSInteger)section {
    return CGSizeMake(0, ZCFoodBottomViewH);
}

@end
```