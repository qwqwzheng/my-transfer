# my-transfer
> Three level linkage of transfer

## 多选穿梭框总结

> 框架：vue 2.x，UI 组件：Element


## 省市级联动多选穿梭框

![](https://github.com/Krryxa/WORK-LEARNING/blob/master/images/f-1.jpg)

- 实现省市区三级多选联动，可任选一个省级、市级、区级，加入已选框，也可以在已选框中删除对应的区域。
- 选择对应仓库，自动勾选仓库对应的省，取消就反选
- 选择同样地区，选择省级或市级，若该对象下面的市级或区级已有选择，就自动合并为一个省级或市级：

```
例如：
已选择：广东省广州市荔湾区
点击加入：广东省广州市
最后显示：广东省广州市
广州市已被选择，对应市级的区将不显示，只显示对应的市

同理：选择广东省，也将下面已选择的所有的市或区合并成一个省级，只显示这个省级
```

## 设计
- 拆分组件：仓库和省市区框作为父组件
- 三个区域框和一个已选框相似，复用一个组件 transfer，放在省市区框父组件中。
> 省级 transfer、市级 transfer、区级 transfer

### 数据展示
- 父组件从数据中获取省级数据传递到子组件 transfer 展示出来。

- 当选中的某个省，则传递对应省级 id 到父组件，根据 id 查找对应的市级并过滤，并且使用 ref 控制市级的 transfer 组件的 father 属性，就是在市级 transfer 组件里也有对应的省级对象。
- 多选情况，只保留最后选择的父级（省级/市级）查找出对应的子级（市级/区级）
- 市级和省级组件的 father 对象是 {id:"", text:""}
- 市级组件的 father 保存着省级的 id 和名称。
- 区级组件的 father 保存着省市的 id 和名称（以-分割，如：{id:"10001-100145815", text:"广东省-河源市"}）
- 省市区都分别设置一个过滤数组，用来过滤已选的区域

### 区域搜索
- 监听搜索框的值，重新获取区域数据，再通过 filter 筛选出搜索的数据

### 点击添加进已选：
- 省级直接点击添加选中的省份，直接传递该省的对象进已选数组。并将选择的省级 id 添加进省级过滤数组。然后判断已选区域中是否有该省级一下的市级，有则删除，合并成一个省级，并在省级过滤数组删除掉这个市级 id。
- 市级点击添加选中的城市，选中的城市对象数组，遍历拼接上当前的 father 对象，最终保存的形式：{id: "10005-545132025515", text: "广东省-广州市"}，也要判断当前市级下是否有对应的区级，有则合并，并在区级过滤数组删除这个区级 id。
- 区级点击添加选中的区域，拼接上当前的 father 对象，最终的保存的形式：{id:"10004-15613610-25156165156321", text:"广东省-河源市-龙川县"},

- 子组件 transfer 中区域数据 districtList 需要放在监听器里，当点击省级或市级，自动监听更新市级或区级的列表

### 从已选中删除
- 选中已选区域的数据，传递到父组件，同样的道理，删除过滤数组对应的 id，并刷新对应的区域数据

### 监听仓库与区域对应
- 找出选中仓库的对应省级 id，已选区域有该省则过滤，无则添加，当点击取消勾选的时候，也实时将已选区域中对应的省级删除掉。


## 数据量庞大的分页穿梭框

![](https://github.com/Krryxa/WORK-LEARNING/blob/master/images/f-2.jpg)

重写穿梭框组件，使其变成分页形式加载

## 具体改变的点有

1. 从接口获取的 3000+ 的数据，放在临时变量，以分页形式展示
2. 渠道的搜索，做成在所有数据里搜索，不是在当前分页的数据里搜索，这样就不用在每个分页都搜索一次了。搜索后的结果也会自动分页。（全部数据和仅作展示的数据存放在不同变量）
3. 为了优化全选的速度，全选只在当前页里的全选
4. 穿梭框左右两个框的联动

## 关键点
1. 分页形式，关键点判断临界点
2. 搜索数据，监听 keyword 的变化，从全局数据搜索
3. 把备选的数据当做已选的过滤数组，把已选的数据当做备选的过滤数组，在所有 data 数据中进行过滤，最后进行一次搜索（备选、已选）
4. 子组件：两个框，将中间的左右箭头（加入已选和移除已选）放在父组件控制数据流动
5. 数据流动：子备选框 -> 父组件 -> 子已选框 （移除已选相反）

## 相关连接
- github：[https://github.com/Krryxa/my-transfer](https://github.com/Krryxa/my-transfer)

