<template>
	<view>
		<view class="wrap">
			<view class="title">
				<view>{{ userName }}</view>
			</view>
			<view class="content_box" id="box" ref="scrollBox">
				<view class="timer">{{ nowTime }}</view>
				<view :class="item.position == 'left' ? 'userbox2' : 'userbox'" v-for="(item, index) in chatList"
					:key="index" :id='"item"+index'>
					<view :class="item.position == 'left' ? 'nameInfo2' : 'nameInfo'">
						<view style="font-size: 14px">{{ item.position == 'left' ?item.uname:item.to_name  }}</view>
						<view :class="item.position == 'left' ? 'contentText2' : 'contentText'">
							{{ item.msn }}
						</view>
					</view>
					<view>
						<image class="touxiang" :src="item.position == 'left' ?item.uavatar:item.to_avatar" />
					</view>
				</view>
			</view>
			<view class="bottom">
				<textarea name="输入框" id="1" cols="20" rows="5" class="areaBox" v-model="inputValue"></textarea>
				<button style="height: 30px;color:#58df4d;font-size: 14px;line-height: 30px;"
					@click="sendOut">发送</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nowTime: "",
				page: 1,//聊天历史记录分页
				chatList: [{
						add_time: 1666661420,
						chat_type: "chat",
						id: 1,
						is_tourist: 1,
						mer_id: 0,
						msn: "333",
						msn_type: 0,
						position: "left",
						remind: 0,
						time_node: 0,
						to_avatar: "../../static/logo.png",
						to_name: "yajuu senpai",
						to_uid: 114514,
						type: 1,
						uavatar: "../../static/logo.png",
						uid: 1919810,
						uname: "yarimasune"
					},{
						add_time: 1666661520,
						chat_type: "reply",
						id: 2,
						is_tourist: 1,
						mer_id: 0,
						msn: "yes",
						msn_type: 0,
						position: "right",
						remind: 0,
						time_node: 0,
						to_avatar: "../../static/logo.png",
						to_name: "yajuu senpai",
						to_uid: 1919810,
						type: 1,
						uavatar: "../../static/logo.png",
						uid: 114514,
						uname: "yajuu senpai"
				}],//聊天信息
				userName: "userName",//用户名
				inputValue: "",//输入内容
				scrollTop: 0,//滚动条距离顶部距离
				infoList: [
					
				],//用户信息

				path: "wss://test.jskwsx.com/msg", //websocket链接地址
				ws: null, //建立的连接
				lockReconnect: false, //是否真正建立连接
				timeout: 10 * 1000, //30秒一次心跳
				timeoutObj: null, //心跳心跳倒计时
				serverTimeoutObj: null, //心跳倒计时
				timeoutnum: null, //断开 重连倒计时
				closeType:1,//断开判断：0代表不重连，1代表重连
			}
		},
		onShow() {
			this.getTime();//显示时间
			this.initWebpack();//初始化
			this.closeType=1;//进入改为1，代表如果断开链接自动重连
			this.getlishiList();//历史记录
			this.userName=uni.getStorageSync("userinfo").nickname;//拿到缓存中的用户信息
		},
		onLoad(options) {
			this.infoList = JSON.parse(options.urlee);//拿到上一个页面传过来的参数，内容是选中客服信息
			console.log('选中客服信息', this.infoList);
		},
		onPageScroll(e) {
			//监听滚动事件，如果滚动条等于0，代表滚动到最顶部，把分页加一，然后历史记录拉第二页数据，以此类推
			if (e.scrollTop == 0) {
				this.page++;
				this.getlishiList(1);
				console.log('到顶部了');
			}
		},
		beforeDestroy() {
			this.closeType=0 //离开页面前改为0，代表离开后断开链接不再重连
			this.ws.send({
				data: JSON.stringify({
					type: "online",
					data: {
						online: 0,
						user_type: 'user',
						is_tourist: uni.getStorageSync("userinfo").id?0:1
					}
				})
			})
			// 离开页面后关闭连接
			this.ws.close();
			// 清除时间
			clearTimeout(this.timeoutObj);
			clearTimeout(this.serverTimeoutObj);
		},
		methods: {
			getTime() {
				var a = new Date().getTime(); //获取到当前时间戳
				var b = new Date(a); //创建一个指定的日期对象
				var year = b.getFullYear(); //年份
				var month = b.getMonth() + 1; //月份（0-11）
				var date = b.getDate(); //天数（1到31）
				var hour = b.getHours(); //小时数（0到23）
				var minute = b.getMinutes(); //分钟数（0到59）
				var second = b.getSeconds(); //秒数（0到59） 
				this.nowTime = year + "-" + month + "-" + date + " " + (hour>9 ? hour : "0"+hour) 
								+ ":" + (minute>9 ? minute : "0"+minute) + ":" + (second>9 ? second : "0"+second);
				console.log("nowtime = " + this.nowTime);
			},
			//获取历史记录
			getlishiList(type) {
				uni.request({
					url: 'https://zz.api.asdwqs.com/gzh/crmebchat/chatMessageList', //仅为示例，并非真实接口地址。
					method: 'POST',
					data: {
						accept_id: this.infoList.kf_id,
						page: this.page,
						limit: 10,
					},
					header: {
						token: uni.getStorageSync('token') //拿到缓存中的token
					},
					success: (res) => {
						console.log('历史记录:', res);
						let a = res.data.data.list
						this.chatList = a.concat(this.chatList)//用拿到的数据合并现有的数据，这样当加载第二页历史记录时，顺序不会乱
						if (type == 1) {//滚动到顶部触发方法会传入1，此时不需要调用滚动到最底部的方法
							return
						}
						this.setPageScrollTo()//滚动到最底部
					}
				});
			},
			//滚动条默认滚动到最底部
			setPageScrollTo(s, c) {
				let that = this
				this.$nextTick(() => {
					const query = uni.createSelectorQuery().in(this);
					query
						.select("#box")
						.boundingClientRect((rect) => {
							let height = rect.height;//拿到聊天框的高度
							console.log("聊天信息框高度: ", height);
							wx.pageScrollTo({
								scrollTop: height,//把距离顶部距离设置成聊天框高度，以此把滚动条顶到最底部
								duration: 100 // 滑动速度
							})
						})
						.exec();
				});
			},

			//发送消息
			sendOut() {
				this.chatList.push({
					msn: this.inputValue,
					position: "right",
					to_avatar: "../../static/logo.png",
					to_name: "yajuu senpai"
				})
				let parms = {
					content: this.inputValue,
					uid: uni.getStorageSync("userinfo").id,
					uname: uni.getStorageSync("userinfo").nickname,
					uavatar: uni.getStorageSync("userinfo").avatar,
					to_uid: this.infoList.kf_id,
					to_name: this.infoList.kfname,
					to_avatar: this.infoList.kf_avatar,
					type: 'text',
					channel_type: 'wechat',
				}
				//通过websocket发送信息到后台
				this.ws.send({
					data: JSON.stringify({
						type: "chat",
						data: parms
					})
				})
				this.inputValue = ''//点击发送后清空输入框
				this.setPageScrollTo()//滚动到最底部
				console.log('发送成功', this.inputValue);
				
				this.chatList.push({
					msn: "啊对对对",
					position: "left",
					uavatar: "../../static/logo.png",
					uname: "yarimasune"
				})
				let rparms = {
					content: "啊对对对",
					uid: uni.getStorageSync("userinfo").id,
					uname: uni.getStorageSync("userinfo").nickname,
					uavatar: uni.getStorageSync("userinfo").avatar,
					to_uid: this.infoList.kf_id,
					to_name: this.infoList.kfname,
					to_avatar: this.infoList.kf_avatar,
					type: 'text',
					channel_type: 'wechat',
				}
				//通过websocket发送信息到后台
				this.ws.send({
					data: JSON.stringify({
						type: "reply",
						data: rparms
					})
				})
			},

			// 初始化websocket链接
			initWebpack() {
				//实例
				this.ws = wx.connectSocket({
					url: this.path
				})
				//链接成功
				this.ws.onOpen((res) => {
					let that = this
					console.log("连接成功", that.ws.readyState);
					if (that.ws.readyState == 1) {
						wx.sendSocketMessage({ //发送消息到后台，和send一样，这是微信的写法
							data: JSON.stringify({
								type: "login",
								data: {
									id: uni.getStorageSync("userinfo").wechatUsers.id,
									channel_type: 'wechat',
									uid: uni.getStorageSync("userinfo").id,
									openid: 'ojV4k6tnkv4_F1dddc3VwLeJ_QLs'
								}
							})
						})
						this.ws.send({
							data: JSON.stringify({
								type: "online",
								data: {
									online: 1,
									user_type: 'user',
									is_tourist: uni.getStorageSync("userinfo").id?0:1
								}
							})
						})
					}
					that.start(); //链接成功后开启心跳
				})
				//链接异常
				this.ws.onError((res) => {
					console.log("出现错误");
					this.reconnect(); //重连
				})
				//链接断开
				this.ws.onClose((res) => {
					console.log("连接关闭");
					//断开链接时判断
					if(this.closeType==0){
						return
					}
					this.reconnect(); //重连
				})
				//后台返回消息
				this.ws.onMessage((res) => {
					let type = JSON.parse(res.data)
					//后台返回消息，通过type字段判断是不是别人发送给我的消息
					if (type.type == 'chat') {
						this.chatList.push(type.data)//把消息添加到信息列表渲染
						this.setPageScrollTo() //滚动到最底部
						console.log("收到后台信息：", JSON.parse(res.data));
					}
					
					this.reset(); //收到服务器信息，心跳重置
				})

			},
			//重新连接
			reconnect() {
				var that = this;
				//防止重复链接
				if (that.lockReconnect) {
					return;
				}
				that.lockReconnect = true;
				//没连接上会一直重连，设置延迟避免请求过多
				that.timeoutnum && clearTimeout(that.timeoutnum);
				that.timeoutnum = setTimeout(function() {
					that.initWebpack(); //新连接
					that.lockReconnect = false;
				}, 5000);
			},
			//重置心跳
			reset() {
				var that = this;
				clearTimeout(that.timeoutObj); //清除心跳倒计时
				clearTimeout(that.serverTimeoutObj); //清除超时关闭倒计时
				that.start(); //重启心跳
			},
			//开启心跳
			start() {
				var self = this;
				self.timeoutObj && clearTimeout(self.timeoutObj); //心跳倒计时如果有值就清除掉，防止重复
				self.serverTimeoutObj && clearTimeout(self.serverTimeoutObj); //超时关闭倒计时如果有值就清除掉，防止重复
				self.timeoutObj = setTimeout(function() {
					if (self.ws.readyState == 1) {
						wx.sendSocketMessage({
							data: JSON.stringify({
								type: "ping"
							})
						})
					} else {
						self.reconnect();//重连
					}

					//如果超时了就关闭连接
					self.serverTimeoutObj = setTimeout(function() {
						self.ws.close();
					}, self.timeout);
				}, self.timeout);
			},
			//连接成功
		},
	}
</script>

<style scoped>
	.wrap {
		height: 100%;
		width: 100%;
		position: relative;
	}

	.touxiang {
		width: 50px;
		height: 50px;
		border-radius: 50%;
	}

	.areaBox {
		height: 40px;
	}

	.title {
		height: 40px;
		width: 100%;
		background-color: #eaeaea;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.bottom {
		min-height: 50px;
		width: 100%;
		border-top: 1px solid #eaeaea;
		background-color: #F1F1F1;
		position: fixed;
		bottom: 0;
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0 5px;
		border-radius: 10px;
	}

	.content_box {
		/* 
  中间栏计算高度，110是包含了上下固定的两个元素高度90
  这里padding：10px造成的上下够加了10，把盒子撑大了，所以一共是20要减掉
  然后不知道是边框还是组件的原因，导致多出了一些，这里再减去5px刚好。不然会出现滚动条到顶或者底部的时候再滚动的话就会报一个错，或者出现滚动条变长一下的bug
  */
		height: calc(100% - 115px);
		overflow: auto;
		padding: 10px 10px 50px 10px;
	}

	.timer {
		text-align: center;
		color: #c2c2c2;
	}

	/* 发送的信息样式 */
	/* 
右边消息思路解释：首先大盒子userbox内放两个盒子，一个放头像，一个放用户名和发送的内容，我们先用flex让他横向排列。
然后把写文字的大盒子设置flex：1。这个属性的意思就是让这个元素撑满父盒子剩余位置。然后我们再把文字盒子设置flex，并把他对齐方式设置为尾部对齐就完成了基本的结构，然后微调一下就可以了
*/
	.userbox {
		width: 100%;
		display: flex;
		margin-bottom: 10px;
	}

	.nameInfo {
		/* 用flex：1把盒子撑开 */
		flex: 1;
		margin-right: 10px;
		/* 用align-items把元素靠右对齐 */
		display: flex;
		flex-direction: column;
		align-items: flex-end;
	}

	.contentText {
		background-color: #9eea6a;
		/* 把内容部分改为行内块元素，因为盒子flex：1把盒子撑大了，所以用行内块元素让内容宽度不根据父盒子来 */
		display: inline-block;
		/* 这四句是圆角 */
		border-top-left-radius: 10px;
		border-top-right-radius: 0px;
		border-bottom-right-radius: 10px;
		border-bottom-left-radius: 10px;
		/* 最大宽度限定内容输入到百分61换行 */
		max-width: 61%;
		padding: 5px 10px;
		/* 忽略多余的空白，只保留一个空白 */
		white-space: normal;
		/* 换行显示全部字符 */
		word-break: break-all;
		margin-top: 3px;
		font-size: 14px;
	}

	/* 接收的信息样式 */
	/* 
左边消息思路解释：跟上面一样，就是换一下位置，首先通过把最外层大盒子的排列方式通过flex-direction: row-reverse;属性翻转，也就是头像和文字盒子换位置
然后删除掉尾部对齐方式，因为不写这个默认是左对齐的。我们写的左边就没必要再写了。
*/
	.userbox2 {
		width: 100%;
		display: flex;
		flex-direction: row-reverse;
		margin-bottom: 10px;
	}

	.nameInfo2 {
		/* 用flex：1把盒子撑开 */
		flex: 1;
		margin-left: 10px;
	}

	.contentText2 {
		background-color: #9eea6a;
		/* 把内容部分改为行内块元素，因为盒子flex：1把盒子撑大了，所以用行内块元素让内容宽度不根据父盒子来 */
		display: inline-block;
		/* 这四句是圆角 */
		border-top-left-radius: 0px;
		border-top-right-radius: 10px;
		border-bottom-right-radius: 10px;
		border-bottom-left-radius: 10px;
		/* 最大宽度限定内容输入到百分61换行 */
		max-width: 61%;
		padding: 5px 10px;
		/* 忽略多余的空白，只保留一个空白 */
		white-space: normal;
		/* 换行显示全部字符 */
		word-break: break-all;
		margin-top: 3px;
		font-size: 14px;
	}
</style>