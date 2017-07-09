# vane

基于`mobx & mobx-react`的React store管理框架，提供简单快捷开发方式。

## 特点

`三个API`搞定问题！

## 如何使用

> vane提供了一键初始化的`start`方法，入口文件可以像下面这样开始：

```js
import React from 'react';
import App from './App';

import {
    start,
} from 'vane';

// model
import user from './models/User';
import todos from './models/Todos';

start({
    component: App,
    container: '#root',
    models: {
        user,
        todos
    }
});
```

> 基于vane的开发范式的`container Component`也是UI Component，UI Component像下面这样：

```js
import React, {Component, PropTypes} from 'react';

// load middlewares
import './middlewares';

// components
import UserLogin from './components/UserLogin';
import UserDetail from './components/UserDetail';
import Todos from './components/Todos';

import {
    inject,
    observer,
} from 'vane';

// 注意先observer，后inject
@inject('user')
@observer
export default class App extends Component {
    render() {
        // const user = this.props.user.toJSON();
        console.log(this.props.user.toJSON());
        const {user} = this.props;

        console.log('user.isLogin:', user.isLogin);

        if (user.isLogin !== true) {
            return <UserLogin />;
        }

        return (
            <div>
                <UserDetail />
                <Todos />
            </div>
        );
    }
}
```

## model

代码类似于下面这样：

```js
import TodoItem from './TodoItem';
import * as api from '../api';

export default {
    name: 'Todos',
    data: {
        list: [],
    },
    syncs: {
        add(text, userId) {
            // 类似于Vue，对数组的操作会触发UI的重新渲染
            this.list.push(new TodoItem({
                text,
                userId
            }));
        },
    },
    effects: {
        async getByUserId(userId) {
            let todos = await api.getTodosByUserId(userId);
            todos = todos.map(todo => new TodoItem(todo));
            // 类似于Vue，对数组的操作会触发UI的重新渲染
            this.list = this.list.concat(todos);
        },
    }
};
```

model由以下几个部分组成：

- 1、name: 当前model的命名空间；
- 2、constants: 不可变常量；
- 3、data: 可操作数据部分；
- 4、syncs: 同步操作数据部分；
- 5、effects: 异步处理部分；
- 6、init: 初始化model后的回调方法；
- 7、autorun: 每次对数据进行操作后都会自动执行的方法。

## 特点

- 简单易上手，开发效率高；
- MVVM：Vane实现了基于React的MVVM开发范式，简单直接，开发效率高；
- 更改store数据：直接赋值；
- 触发action：直接执行store的action；
- 性能优化：自动做掉。

具体例子参考[example](https://github.com/abell123456/vane/tree/master/example).