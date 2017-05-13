title: Write once Run on iOS/Android
date: 2016-06-21 9:48:13
tags: [iOS,react native,Android]
---


### 一、应用场景
  我们谁都不想重复造轮子，我们都希望write once run anywhere，react native 现在给你这个机会
  先上图：
<!-- more -->
  ![](http://7xslr9.com1.z0.glb.clouddn.com/ReactNativeiOS.png)![](http://7xslr9.com1.z0.glb.clouddn.com/ReactNativeAndroid.png)
  
### 二、简单用例
  1、简单实现一个历史记录的界面
  在这里我就不谈环境部署了，按着官方文档一步一步来就可以了
  初始化完项目后，我们可以在主目录里面看到Android和iOS的工程以及JS入口文件index.android.js和index.ios.js
  我把这两个文件里面的类容都改为
  ```bash
  require('./App/app');
  ```
  这样我们就直接可以在app.js文件里写代码应用到Android和iOS中了
  app.js里的代码如下
  ``` bash
  /**
   * Sample React Native App
   * https://github.com/facebook/react-native
   */

  import React, {
    AppRegistry,
    Platform,
    Component,
    TouchableOpacity,
    StyleSheet,
    Navigator,
    View,
    Text
  } from 'react-native';
  import Home from './home';

  const defaultRoute = {
    component: Home
  };

  class StartReact extends Component {
    _renderScene(route, navigator) {
      let Component = route.component;
      return (
        <Component {...route.params} navigator={navigator} />
      );
    }
    _renderNavBar() {
      const styles = {
        title: {
          flex: 1, alignItems: 'center', justifyContent: 'center'
        },
        button: {
          flex: 1, width: 50, alignItems: 'center', justifyContent: 'center'
        },
        buttonText: {
          fontSize: 18, color: '#FFFFFF', fontWeight: '400'
        }
      }

      var routeMapper = {
        LeftButton(route, navigator, index, navState) {
          if(index > 0) {
            return (
              <TouchableOpacity
                onPress={() => navigator.pop()}
                style={styles.button}>
                <Text style={styles.buttonText}>Back</Text>
              </TouchableOpacity>
            );
          } else {
            return (
              <TouchableOpacity
                onPress={() => navigator.pop()}
                style={styles.button}>
                <Text style={styles.buttonText}>返回</Text>
              </TouchableOpacity>
            );
          }
        },
        RightButton(route, navigator, index, navState) {
          if(index > 0 && route.rightButton) {
            return (
              <TouchableOpacity
                onPress={() => navigator.pop()}
                style={styles.button}>
                <Text style={styles.buttonText}>下一页</Text>
              </TouchableOpacity>
            );
          } else {
            return (
              <TouchableOpacity
                onPress={() => navigator.pop()}
                style={styles.button}>
                <Text style={styles.buttonText}>走你</Text>
              </TouchableOpacity>
            );
          }

        },
        Title(route, navigator, index, navState) {
          return (
            <View style={styles.title}>
              <Text style={styles.buttonText}>{route.title ? route.title : '主页'}</Text>
            </View>
          );
        }
      };

      return (
        <Navigator.NavigationBar
          style={{
            alignItems: 'center',
            backgroundColor: '#fd9526',
            shadowOffset:{
                width: 1,
                height: 0.5,
            },
            shadowColor: '#55ACEE',
            shadowOpacity: 0.8,
            }}
          routeMapper={routeMapper}
        />
      );
    }
    render() {
      return (
        <Navigator
          initialRoute={defaultRoute}
          renderScene={this._renderScene}
          sceneStyle={{paddingTop: (Platform.OS === 'android' ? 64 : 44)}}
          navigationBar={this._renderNavBar()} />
      );
    }
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#F5FCFF',
    },
    welcome: {
      fontSize: 20,
      textAlign: 'center',
      margin: 10,
    },
    instructions: {
      textAlign: 'center',
      color: '#333333',
      marginBottom: 5,
    },
  });

  AppRegistry.registerComponent('StartReact', () => StartReact);
  ```
  当然这里还依赖了home.js，Navigation.js这里就不一一罗列了
