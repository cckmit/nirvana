---
title: reactNative中sectionList的使用小结
date: 2022-05-21 21:12:40
categories: 
    - [react-native]
---
sectionList常用属性和方法
1.sections：数据源
2.ItemSeparatorComponent：行与行之间的分隔线组件。不会出现在第一行之前和最后一行之后
3.SectionSeparatorComponent：每个section之间的分隔组件
4.ListEmptyComponent：列表为空时渲染该组件。可以是React Component, 也可以是一个render函数， 或者渲染好的element。
5.ListFooterComponent：尾部组件
6.ListHeaderComponent：头部组件
7.getItemLayout：是一个可选的优化，用于避免动态测量内容尺寸的开销，不过前提是你可以提前知道内容的高度。如果你的行高是固定的，getItemLayout用起来就既高效又简单，类似下面这样：
getItemLayout={(data, index) => ( {length: 行高, offset: 行高 * index, index} )}
注意如果你指定了SeparatorComponent，请把分隔线的尺寸也考虑到offset的计算之中。
8.initialNumToRender：指定一开始渲染的元素数量，最好刚刚够填满一个屏幕，这样保证了用最短的时间给用户呈现可见的内容。注意这第一批次渲染的元素不会在滑动过程中被卸载，这样是为了保证用户执行返回顶部的操作时，不需要重新渲染首批元素。
9.keyExtractor：此函数用于为给定的item生成一个不重复的key。Key的作用是使React能够区分同类元素的不同个体，以便在刷新时能够确定其变化的位置，减少重新渲染的开销。若不指定此函数，则默认抽取item.key作为key值。若item.key也不存在，则使用数组下标。
10.legacyImplementation：设置为true则使用旧的ListView的实现
11.onEndReached：当列表被滚动到距离内容最底部不足onEndReachedThreshold的距离时调用
12.onEndReachedThreshold：决定当距离内容最底部还有多远时触发onEndReached回调。注意此参数是一个比值而非像素单位。比如，0.5表示距离内容最底部的距离为当前列表可见长度的一半时触发
13.onRefresh：如果设置了此选项，则会在列表头部添加一个标准的RefreshControl控件，以便实现“下拉刷新”的功能。同时你需要正确设置refreshing属性
14：onViewableItemsChanged：在可见行元素变化时调用。可见范围和变化频率等参数的配置请设置viewabilityconfig属性
15.refreshing：在等待加载新数据时将此属性设为true，列表就会显示出一个正在加载的符号
16.renderItem：根据行数据data渲染每一行的组件
18.scrollToLocation：将可视区内位于特定sectionIndex 或 itemIndex (section内)位置的列表项，滚动到可视区的制定位置。比如说，viewPosition 为0时将这个列表项滚动到可视区顶部 (可能会被顶部粘接的header覆盖), 为1时将它滚动到可视区底部, 为0.5时将它滚动到可视区中央。viewOffset是一个以像素为单位，到最终位置偏移距离的固定值，比如为了弥补粘接的header所占据的空间
注意: 如果没有设置getItemLayout，就不能滚动到位于外部渲染区的位置。
19.recordInteraction：主动通知列表发生了一个事件，以使列表重新计算可视区域。比如说当waitForInteractions 为 true 并且用户没有滚动列表时，就可以调用这个方法。不过一般来说，当用户点击了一个列表项，或发生了一个导航动作时，我们就可以调用这个方法。
20.flashScrollIndicators：短暂地显示滚动指示器。
