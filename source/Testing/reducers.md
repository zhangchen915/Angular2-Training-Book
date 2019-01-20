# 测试 Reducers

幸运的是，测试reducers很像测试我们的同步 action 创建者，因为所有reducer操作是同步的，这使我们的全局状态容易跟踪，同时也是为什么Redux的拥有众多粉丝的原因。

我们将在 [angular2-redux-starter](https://github.com/rangle/angular2-redux-starter) 中测试减速器：

```javascript
export default function counter(state = 0, action)
  switch (action.type) {
    case INCREMENT_COUNTER:
      return state + 1;
    case DECREMENT_COUNTER:
      return state - 1;
    default:
      return state;
  }
}
```

正如你可以看到，有三种情况要测试：默认情况，增量和减量。我们想测试我们的 actions 触发的 reducer 状态变化符合预期。

```javascript
import { INCREMENT_COUNTER, DECREMENT_COUNTER } from '../actions/counter';
import counter from './counter';                                         

describe('counter reducers', () => {                                     
  it('should handle initial state', () => {                              
    expect(                                                         
      counter(undefined, {})                                             
    )                                                                    
    .toEqual(0)                                                         
  });                                                                    

  it('should handle INCREMENT_COUNTER', () => {                          
    expect(                                                         
      counter(0, {                                                       
        type: INCREMENT_COUNTER                                          
      })                                                                 
    )                                                                    
    .toEqual(1)                                                         
  });                                                                    

  it('should handle DECREMENT_COUNTER', () => {                          
    expect(                                                         
      counter(1, {                                                       
        type: DECREMENT_COUNTER                                          
      })                                                                 
    )                                                                    
    .toEqual(0)                                                         
  });                                                                    
});
```

注意，我们只是测试Redux状态的一部分，减速器负责`Counter`，而不是整体。从这些测试中我们可以看出，Redux主要基于纯函数。