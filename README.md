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

###四.如何使用react-native-storage

###五.回调在RN中的应用

###六.如何使用Fetch/RESTFUL跟服务器交互

