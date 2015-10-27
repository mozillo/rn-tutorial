# Awesome-React-Native-Tutorial
一个由新手写的ReactNative新手入门的指南,站在初学者的角度去理解问题

###一.如何区分props跟state 以及防止因为理解不当出现的奇怪的报错
	
首先需要知道一个概念，就是watch，就是当你的一个对象改变的时候，对应渲染的view内容也会改变。
举个很简单的例子:
	
	[ 1 ] +
假设这是一个Web视图，[1]是一个计数器，旁边有个+按钮，当我点击+按钮的时候，我在绑定的onClick事件里执行 i = i+1 接下来发生的事情就是 [1] 变成了 [2]，然而这个view中[1]变为[2]的过程会有一个叫做state的机制帮我们完成。还是看代码吧 

	ExampleComponent = React.createClass({
		getInitialState: function() {
			return {
				i: 0
			}
		},
		comoonentDidMount: function() {
		},
		_pressButton: function() {
			var oldValue = this.state.i;
			this.setState({
				i: oldValue+1
			});
		},
		render: function() {
			return (
				<View>
					<TouchableOpacity onPress={this._pressButton}>
						<Text>{ this.state.i }</Text>
					</TouchableOpacity>
				</View>
			);
		}
	}); 
当我们每次点击这个按钮的时候，就会发现它增大1了。这就是state，系统总是会监视它的变化，随时准备更新在这个东西，当然state也可以不存在，为null，这个在视图中可以通过判断决定是否渲染这个state:
	<Text>{ this.state.i ? this.state.i : null }</Text>
state是不固定的东西，随处都可以修改更新。
如果能理解state的运作就好了，因为这个是整个app里唯一表达出动态的东西。一切视觉变化因为state而起。
	
###二.如何DEBUG

###三.如何使用Navigator
一切的SPA(单页面应用)都离不开路由系统，Navigator是RN提供的一个路由工具，不过缺陷是没有预设一个路由配置文件，所以导致整个结构类似goto语法的，你可以跳转到任意的页面。先来一个最简单的Navigator:
	
	var {
		View,
		Navigator
	} = React;
	var FirstPageComponent = require('./FirstPageComponent');
	
	var SampleComponent = React.createClass({
		render: function() {
			var defaultName = 'FirstPageComponent';
			var defaultComponent = FirstPageComponent;
			return (
	        <Navigator
	          initialRoute={{ name: defaultName, component: defaultComponent }}
	          configureScene={() => {
	            return Navigator.SceneConfigs.VerticalDownSwipeJump;
	          }}
	          renderScene={(route, navigator) => {
	            let Component = route.component;
	            if(route.component) {
	              return <Component {...route.params} navigator={navigator} />
	            }
	          }} />
			);

		}
	});
这里来逐行解释代码的功效:

第三行: 一个初始首页的component名字，比如我写了一个component叫HomeComponent，那么这个name就是这个组件的名字【HomeComponent】了。

第四行: 这个组件的Class，用来一会儿实例化成 <Component />标签
	
第七行: initialRoute={{ name: defaultName, component: defaultComponent }} 这个指定了默认的页面，也就是启动app之后会看到界面的第一屏。 需要填写两个参数: name 跟 component。

第八，九，十行:           configureScene={() => {
            return Navigator.SceneConfigs.VerticalDownSwipeJump;
          }} 这个是页面之间跳转时候的动画，具体有哪些？可以看这个目录下，有源代码的: node_modules/react-native/Libraries/CustomComponents/Navigator/NavigatorSceneConfigs.js
          
最后的几行: renderScene={(route, navigator) => {
            let Component = route.component;
            if(route.component) {
              return <Component {...route.params} navigator={navigator} />
            }
          }} />
        );
        
这里是每个人最疑惑的，我们先看到回调里的两个参数:route, navigator。通过打印我们发现route里其实就是我们传递的name,component这两个货，navigator是一个Navigator的对象，为什么呢，因为它有push pop jump...等方法，这是我们等下用来跳转页面用的那个navigator对象。   

	if(route.component) { 
		return <Component {...route.params} navigator={navigator} />
	}
这里有一个判断，也就是如果传递进来的component存在，那我们就是返回一个这个component，结合前面 initialRoute 的参数，我们就是知道，这是一个会被render出来给用户看到的component，然后navigator作为props传递给了这个component。

所以下一步，在这个FirstPageComponent里面，我们可以直接拿到这个 props.navigator:

	var {
		View,
		Text,
		TouchableOpacity
	} = React;
	
	var SecondPageComponent = require('./SecondPageComponent');

	var FirstPageComponent = React.create({
		getInitialState: function() {
			return {};
		},
		componentDidMount: function() {
		},
		_pressButton: function() {
			const { navigator } = this.props;
			//或者写成 const navigator = this.props.navigator;
			//为什么这里可以取得 props.navigator?请看上文:
			//<Component {...route.params} navigator={navigator} />
			//这里传递了navigator作为props
			if(navigator) {
				navigator.push({
					name: 'SecondPageComponent',
					component: SecondPageComponent,
				})
			}
		}
		render: function() {
			<View>
				<TouchableOpacity onPress={this._pressButton}>
					<Text>点我跳转</Text>
				</TouchableOpacity>
			</View>
		}
	});
这个里面创建了一个可以点击的区域，让我们点击可以跳到SecondPageComponent这个页面，实现页面的跳转。
现在来创建SecondPageComponent，并且让它可以再跳回FirstPageComponent:

	var {
		View,
		Text,
		TouchableOpacity,
	} = React;
	
	var FirstPageComponent = require('./FirstPageComponent');
	
	var SecondPageComponent = React.create({
		getInitialState: function() {
			return {};
		},
		componentDidMount: function() {
		},
		_pressButton: function() {
			const { navigator } = this.props;
			if(navigator) {
				//很熟悉吧，入栈出栈~
				navigator.pop();
			}
		}
		render: function() {
			<View>
				<TouchableOpacity onPress={this._pressButton}>
					<Text>点我跳回去</Text>
				</TouchableOpacity>
			</View>
		}
	});
大功告成，能进能出了。
然后下面要讨论，怎么传递参数过去，或者从对方获取参数:
###四.如何使用react-native-storage

###五.回调在RN中的应用

###六.如何使用Fetch/RESTFUL跟服务器交互

