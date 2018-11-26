---
title: UITableView
date: 2016-07-06 10:31:23
tags:
categories: UI
---

## 一、数据源和代理方法

**UITableView有两种风格：UITableViewStylePlain和UITableViewStyleGrouped**
#### 1、UITableViewDataSource(数据源)
* 1、有多少组
	```objc
	- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView;
	```
* 2、有多少行（required）
	```objc
	- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
	```
* 3、cell（required）
	```objc
	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
	```
	
* 4、SectionHeaderView/SectionFooterView Title
	```objc
	- (nullable NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section;
		
	- (nullable NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section;
	```
* 5、Editing（对应indexPath的cell是否可编辑）
	```objc
	[TableView setEditing:YES animated:YES];

	- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath;
	
	- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath;
	```
* 6、Moving/reordering
	```objc	
	- (BOOL)tableView:(UITableView *)tableView canMoveRowAtIndexPath:(NSIndexPath *)indexPath;
	
	- (void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath *)destinationIndexPath;
	By default, the reorder control will be shown only if the datasource implements -tableView:moveRowAtIndexPath:toIndexPath:
	```
* 7、Index
	```objc
	返回tableView的SectionTitle的数组
	- (nullable NSArray<NSString *> *)sectionIndexTitlesForTableView:(UITableView *)tableView 
	return list of section titles to display in section index view (e.g. "ABCD...Z#")
	
	返回具体title在tableView的哪组
	- (NSInteger)tableView:(UITableView *)tableView sectionForSectionIndexTitle:(NSString *)title atIndex:(NSInteger)inde
	```
	
#### 2、UITableViewDelegate（代理）
* 1、height
	```objc
	- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath;
	- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section;
	- (CGFloat)tableView:(UITableView *)tableView heightForFooterInSection:(NSInteger)section;
	
	预估高度
	- (CGFloat)tableView:(UITableView *)tableView estimatedHeightForRowAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(7_0);
	- (CGFloat)tableView:(UITableView *)tableView estimatedHeightForHeaderInSection:(NSInteger)section NS_AVAILABLE_IOS(7_0);
	- (CGFloat)tableView:(UITableView *)tableView estimatedHeightForFooterInSection:(NSInteger)section NS_AVAILABLE_IOS(7_0);
	```
* 2、SectionHeaderView/SectionFooterView
	```objc
	- (nullable UIView *)tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section;
	
	- (nullable UIView *)tableView:(UITableView *)tableView viewForFooterInSection:(NSInteger)section;
	
	UITableViewMethod（不是代理方法，当需要重用时使用以下方法设置）
	- (nullable UITableViewHeaderFooterView *)headerViewForSection:(NSInteger)section NS_AVAILABLE_IOS(6_0);
	
	- (nullable UITableViewHeaderFooterView *)footerViewForSection:(NSInteger)section NS_AVAILABLE_IOS(6_0);
	```
* 3、selected
	```objc
	- (nullable NSIndexPath *)tableView:(UITableView *)tableView willSelectRowAtIndexPath:(NSIndexPath *)indexPath;
	
	- (nullable NSIndexPath *)tableView:(UITableView *)tableView willDeselectRowAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(3_0);
	
	- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
	
	- (void)tableView:(UITableView *)tableView didDeselectRowAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(3_0);
	```
* 4、用户点击cell 右边的 箭头
	```objc
	 - (void)tableView:(UITableView *)tableView accessoryButtonTappedForRowWithIndexPath:(NSIndexPath *)indexPath
	```

#### 3、UITableViewCell
* 1、UITableViewCellStyle
	```objc
	typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
    UITableViewCellStyleDefault,    // 左侧显示textLabel（不显示detailTextLabel），imageView可选（显示在最左边）
    
    UITableViewCellStyleValue1,     // 左侧显示textLabel、右侧显示detailTextLabel（默认蓝色），imageView可选（显示在最左边）
    
    UITableViewCellStyleValue2,     // 左侧依次显示textLabel(默认蓝色)和detailTextLabel，imageView可选（显示在最左边）
    
    UITableViewCellStyleSubtitle    // 左上方显示textLabel，左下方显示detailTextLabel（默认灰色）,imageView可选（显示在最左边）
	};
	```
* 2、UITableViewCell的accesoryTyp
	```objc
	typedef NS_ENUM(NSInteger, UITableViewCellAccessoryType) {
    UITableViewCellAccessoryNone,                   // 不显示任何图标
    UITableViewCellAccessoryDisclosureIndicator,    // 跳转指示图标
    UITableViewCellAccessoryDetailDisclosureButton, // 内容详情图标和跳转指示图标
    UITableViewCellAccessoryCheckmark,              // 勾选图标
    UITableViewCellAccessoryDetailButton    // 内容详情图标
	};
	````
* 3、如果不希望响应select，那么就可以用下面的代码设置属性
	```objc
	TableView.allowsSelection = NO;
	```
* 4/如何让cell 能够响应 select，但是选中后的颜色又不发生改变呢，那么就设置
	```objc
	cell.selectionStyle = UITableViewCellSelectionStyleNone;
	```
* 5、如何获得 某一行的CELL对象
	```objc
	- (UITableViewCell *)cellForRowAtIndexPath:(NSIndexPath *)indexPath;
	```


#### 4、UITableView的刷新方法
* 1、刷新整个表格
	```objc
	- (void)reloadData;
	```
* 2、刷新指定的分组和行
	```objc
	- (void)reloadRowsAtIndexPaths:(NSArray *)indexPaths withRowAnimation:(UITableViewRowAnimation)animation NS_AVAILABLE_IOS(3_0);
	```
* 3、刷新指定的分组
	```objc
	- (void)reloadSections:(NSIndexSet *)sections withRowAnimation:(UITableViewRowAnimation)animation NS_AVAILABLE_IOS(3_0);
	```
* 4、删除时刷新指定的行数据
	```objc
	- (void)deleteRowsAtIndexPaths:(NSArray *)indexPaths withRowAnimation:(UITableViewRowAnimation)animation;
	```
* 5、添加时刷新指定的行数据。
	```objc
	- (void)insertRowsAtIndexPaths:(NSArray *)indexPaths withRowAnimation:(UITableViewRowAnimation)animation;
	```
		
#### 5、TableView的SectionHeader/SectionFooterView高度设置
```objc
//当需要设置TableView的SectionHeaderView和FooterView的高度为0时
	
//方式一：通过代理方法设置（注：返回值为0表示使用属性sectionHeaderHeight的默认值）
//优先使用代理方法设置高度，若未重写或返回值为0则通过属性设置

- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section {
    return section == CGFLOAT_MIN;
}

- (CGFloat)tableView:(UITableView *)tableView heightForFooterInSection:(NSInteger)section {
    return CGFLOAT_MIN;
}

//方式二：通过属性赋值(可以直接设置为0，但当各组头尾的高度不一致时，需使用代理方法)

self.tableView.sectionHeaderHeight = CGFLOAT_MIN;

self.tableView.sectionFooterHeight = CGFLOAT_MIN;
```

#### 6、隐藏TableView的tableHeaderView/tableFooterView设置
```objc
//不能直接设置tableHeaderView/tableFooterView的frame为0（无效，此时系统会使用tableHeaderView/tableFooterView的默认frame）

self.tableView.tableHeaderView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 0, CGFLOAT_MIN)];

self.tableView.tableFooterView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 0, CGFLOAT_MIN)];
```

#### 7、预估高度使用
```objc
self.tableView.estimatedRowHeight = 60;
//设置值行高只适应
self.tableView.rowHeight = UITableViewAutomaticDimension;
//可不实现下面方法，xib只适应高度
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath;
```

#### 8、折叠所有section header
```objc
numberOfRowsInSection: 如果反回的行数为0 那么 cellForRowAtIndexPath： 是不会调用的

为 **Model** 添加一个**bool**属性， 用来记录该组是否折叠 ， 默认是 NO
@property (nonatomic, assign,getter=isExplain) BOOL explain;
```