# 學習Redux筆記

本文整理自最下方的參考資料，原作者為Unbug。欲知詳細內容，請直接讀原作。


### Flux

使用React Native做APP時，一個component中，如果資料是不變的且不被其他component取用，應該放在`props`，而如果如果資料是會變的但不被其他component取用，則應該放在`state`，此外其他情況，應該放在`store`。

###### MVC的問題

見下圖，Model和View之間是雙向且多對多關係，數據流向複雜，更動或維護不易。

![](http://i.imgur.com/BLmJc0l.png)

###### Flux

數據是單向流動的，跟view有關的數據都在store裡，store裡有很多小store。

![](http://i.imgur.com/qN7gRWa.png)

使用者觸發action creator，action creator可能會跟伺服器溝通而產生新的action，此action會流入dispatcher，dispatcher會通知store，****store會根據action的條件決定是否要更新****，如果store有更新，view就會接到更新並更新。每個action都有一個type屬性，dispatcher會把此action派遣到所有的store裡面，無論有多少store都會收到此action，由store自行決定是否要更新state。

###### 結論：

1. components間共用的數據存在store裡。
2. 使用者發起一個actionCreator，最後透過dispatcher會被派發到store裡。
3. store會決定是否更新state（數據），如果更新的話，view也會跟著更新。

### Redux

Redux有三部份協同完成，action，reducer和store。

###### Action

事件的起點，使用者的動作觸發一個actionCreator，顧名思義，actionCreator會產生一個action。

1. type: 靜態對象，值不可變且唯一。
2. actionCreator: 為一function，function會回傳一個action對象，裡面必定帶有type屬性。
3. action: 一個JavaScript對象，裡面必定帶有type屬性。就是上面定義的type。後面可選的是帶入一些負載數據。\(payload\)

###### Reducers

一般來說，Reducer要代入一個初始值和一個callback，並回傳一個更新後的值。

`(state, action) = newState`

在React Native，我們須要自建reducer。初始值就是本來的state，而callback則改為action，我們再從reducer中依action.type判斷應該回傳怎樣的newState。這個reducer總是綁定一個store（後述），當發出store.dispatch(actionCreator())通知store更新時，actionCreator產生一個action，給store，store會自動帶進舊的state給reducer並取回newState，再比對是否更新。整個過程，我們都不會顯示的使用reducer，因為我們已經綁定給store了，store自己會去做，我們只要把reducer寫好就好了。

****reducer必須回傳新的state對象，不可以修改原本的state。****


參考[Array.reduce\(\) 累計值處理](http://www.victsao.com/blog/81-javascript/184-javascript-arr-reduce)






###### Store

整個app的所有（須要共用的）state都定義在store。確實了解前面兩個部份後，我們才能開始用redux。

假定我們已經有建立了action creator 和 reducer

```
// action creator
const ADD_TEXT = 'ADD_TEXT';
function addTextAction(text) {
  return {type: ADD_TEXT, text}
}

// reducer
function textReducer(state = [], action) {
  switch (action.type) {
    case ADD_TEXT:
      return [
        {
          text: action.text,
        },
        ...state
      ]
    default:
      return state
  }
}
```

我這邊採用與原教材不同的流程來寫。如果看不懂，還是以原教材為準。首先，我們希望使用者按下某個component後產生store變化並更新component，所以先寫component的部份。

```
...
class App extends Component {
  constructor(props) {
    super(props);

    this.state = { texts: [{text: "aa"}] }
  }
...
  handleAddText() {
    // dispatching actions
    store.dispatch( addTextAction('Say hello~') );
  }

  renderTexts() {
    return this.state.texts.map((t) => {
        return (<View><Text>{t.text}</Text></View>)
      }
    )
  }

  render() {
    return() {
      <View>
        <TouchableHighlight onPress={this.handleAddText}>
          <View>
            {this.renderTexts();}
          </View>
        </TouchableHighlight>
      </View>
    }
  }
...
}
```

使用者按下後，會觸發store的dispatch方法，該方法的參數是action對象，action對象由actionCreator產生，內容包含action type和payload。dispatch會將此action配發到store。看起來就像是使用者按下後，產生了一個action對象，此對象會配發給store。但是store接收到action後，還不確定會不會發生變化，要經過reducer之後才能決定。

在此流程中，我們尚未建立store。所以應該先在global處建立store，建立時，要把reducer放進去，表示一個store綁定一個reducer。

```
import { createStore } from 'redux';
...
// Define store
let store = createStore(textReducer)
...
class App extends Component {
...
}
```

reducer會接收一個state和action，最後回傳新的state給store，讓store決定state是否有變化。所以store是要帶入reducer作為參數的。

先前說過，store變化會使component更新。要更新的component不一定是剛剛的component，可以是其他的component，但是要如何讓component知道store的變化呢？這時我們就要在須要監聽store變化的component建立subscribe。

```
class App extends Component { constructor(props){ 
...
  componentDidMount(){
    // subscribe store
    this.unsubscribeStore = store.subscribe(() =>{...});
  }
...
}
```

取得store的變化後，就要定義store發生變化後要做的事情

```
class App extends Component { constructor(props){
...
  componentDidMount(){
    // subscribe store
    this.unsubscribeStore = store.subscribe(() =>{
      // getState
      this.setState({text: store.getState()});
    });
  }
...
}
```

如果該component被unmounted，須要unsubscribe

```
class App extends Component { constructor(props){
...
  componentWillUnmount(){
    // unsubscirbe store
    this.unsubsribeStore();
  }
...
}
```
完整版程式放在Gist~

<script src="https://gist.github.com/HFYEH/df26ae04aa44260d355a0f631525a1c9.js"></script>


![](http://i.imgur.com/9JgAhDt.png)

![](http://i.imgur.com/i5F2ZN0.png)

![](http://i.imgur.com/vHkZG1X.png)

# react-redux

在單純用redux時，要為每個component subscribe和unsubscribe store，過程太繁鎖，react-redux這個獨立的套件可以解決此問題。（react, redux, react-redux彼此都是完全獨立的）

參考資料：

作者為[Unbug Lee](https://github.com/unbug)

[Gitbook](https://unbug.gitbooks.io/react-native-training/content/4_architecture.html)

[視訊](https://www.youtube.com/watch?v=JlYwmoJyM34&list=PLC_rYRxEnwQGLQqrHR0aho33U6DCeJamC&index=4)

