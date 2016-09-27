React DOM tree 是由單一個最上層組件，包含數目不等的組件，每個組件都可以包含數目不等的組件，最後展成一個組件樹。

props是當前組件對外溝通的唯一管道，當props被更改，其下的DOM就會被更新。

state是當前組件內部的狀態，一旦變動，當前組件也會被更新。

#### React 組件生命週期和對時期對應的方法：

[參考連結](https://segmentfault.com/a/1190000003691119)

###### 初始化

componentWillMount()：用來在render前最後一次更改state，而且不會觸發重新render

render()

componentDidMount()：對DOM的異步操作放在這裡

初始化一個組件和其子組件的順序依序是：
parent.componentWillMount()
parent.render()
child.componentWillMount()
child.render()
child.componentDidMount()
parent.componentDidMount()

###### 更新

componentWillReceiveProps(nextProps, nextContext)：組件接收到新的props會調用，當前組件的state發生變化不會執行。從當前組件的父組件改變當前組件的props意指從外部更新此組件，而state改變造成的更新，不在此處。此函數可以在更新前，最後一次修改state而不會觸發重新render。官方建議最外層的組件改變state與伺服器和用戶互動，而子組件只負責通過props更新。

componentShouldUpdate()：決定組件收到新的props和state後是否更新，預設回傳true。組件會將render()的返回值與當前的DOM比較，只更新有變動的部份。在此階段不能使用this.setState()。

componentWillUpdate(), componentDidUpdate()：只能作非狀態的計算，同樣不能使用this.setState()。


###### 移除

componentWillMount()：解除事件監聽，清除記憶體等放在這裡。



## 理解 Navigator 的好文章

[react-native组件生命周期在navigator上的表现](http://www.lynull.com/2016/03/13/react-native-navigator-lifecycle/)

[react-native组件生命周期在navigator上的表现续](http://www.lynull.com/2016/03/22/react-native-navigator-lifecycle-2/)


[浅析react-native的Navigator组件01之最简实例](http://www.lynull.com/2016/03/26/navigator-simplest-01/)

[浅析react-native的Navigator组件02之数据传送](http://www.lynull.com/2016/03/27/navigator-passdata-02/)

[浅析react-native的Navigator组件03之动画切换](http://www.lynull.com/2016/03/28/%E7%BB%86%E8%AF%B4react-native%E7%9A%84navigator%E7%BB%84%E4%BB%B603%E4%B9%8B%E5%8A%A8%E7%94%BB%E5%88%87%E6%8D%A2/)

## 理解 ListView 的好文章

[浅析Listview组件01之最简实例](http://www.lynull.com/2016/04/02/%E6%B5%85%E6%9E%90listview%E7%BB%84%E4%BB%B601%E4%B9%8B%E6%9C%80%E7%AE%80%E5%AE%9E%E4%BE%8B/)




[React Native 从入门到原理](http://www.jianshu.com/p/978c4bd3a759)







[第七卷 - Fetch 簡介以及 React Native 之 Fetch 的練習](http://winwu.github.io/react-native-note/2016/05/02/fetch-exampe/)





[React Native通信机制详解](http://blog.cnbang.net/tech/2698/)


[深入浅出React（四）：虚拟DOM Diff算法解析](http://www.infoq.com/cn/articles/react-dom-diff)


[React Native 调研报告](http://blog.csdn.net/lihuiqwertyuiop/article/details/45241909)





