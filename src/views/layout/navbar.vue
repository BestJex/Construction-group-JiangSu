<template>
	<nav class="site-navbar" :class="navbarClasses">
		<div class="bacolo" style="width: 100%;height: 82.5px;background: -webkit-gradient(linear, 0 0, 100% 100%, from(#7BB3F3), to(#20335D));position: absolute;">
			<div class="jianzhu" style=""></div>
			<div class="site-navbar__header">
				<div class="site-navbar__brand" @click="$router.push({ name: 'home' })">
					<div class="logo"><img src="~@/assets/img/project_logo.png" alt="logo"></div>
					<a class="site-navbar__brand-lg" href="javascript:;">{{appName}}</a>
					<a class="site-navbar__brand-mini" href="javascript:;">{{appShortName}}</a>
				</div>
			</div>
			<el-menu class="site-navbar__menu site-navbar__menu--right" style="z-index: 10;border-right: none;display: flex;align-items: center;height: 62.5px;margin:  10px; font-size: 16px;">
				<el-menu-item index="2" id="base_notification">
					<div class="el-badge" >
						<a href="#" target="_blank" v-on:click.stop.prevent="notificationHandle()">
							<icon-svg name="messages" class="site-sidebar__menu-icon"></icon-svg>
							
						</a>
						<sup class="el-badge__content is-fixed">{{notificationNum}}</sup>
					</div>
				</el-menu-item>
				<el-menu-item index="3" id="base_user">
					<el-dropdown :show-timeout="0" placement="bottom">
						<span class="el-dropdown-link">
							<el-avatar shape ="circle" size="medium" src="https://cube.elemecdn.com/3/7c/3ea6beec64369c2642b92c6726f1epng.png"></el-avatar>
							<span style="margin-left: 12px;font-size: 15px;color: #fff;">{{user.userDisplayName}}</span>
							<i class="el-icon-arrow-down el-icon--right"></i>
						</span>
						<el-dropdown-menu slot="dropdown">
							<el-dropdown-item @click.native="updatePasswordHandle()">修改密码</el-dropdown-item>
							<el-dropdown-item @click.native="profileHandle()">个人信息</el-dropdown-item>
							<el-dropdown-item @click.native="setTaskAgentHandle()">任务授权</el-dropdown-item>
							<el-dropdown-item @click.native="logoutHandle()">注销退出</el-dropdown-item>
							<el-dropdown-item @click.native="settingHandle()">风格设置</el-dropdown-item>
						</el-dropdown-menu>
					</el-dropdown>
				</el-menu-item>
			</el-menu>
		</div>
		<div class="site-navbar__body clearfix" >
			<!-- <el-menu class="site-navbar__menu" mode="horizontal">
	  <el-menu-item class="site-navbar__switch" index="0" @click="switchSidebarCollapseHandle()">
	     <icon-svg :name="$store.state.ui.sidebarCollapse?'zhedie_r':'zhedie_l'"></icon-svg>
	  </el-menu-item>
	</el-menu> -->
			<div class="div-loading" v-show="isLoading">
				<div class="div-loading-inner"><i class="el-icon-loading"></i><span class="icon-name">系统加载中...</span>
				</div>
			</div>
	
		</div>
		<!-- 弹窗, 修改密码 -->
		<update-password v-if="updatePassowrdVisible" ref="updatePassowrd"></update-password>
		<profile v-if="profileVisible" ref="profile" ></profile>
		<stomp-client ref="stompClient" ></stomp-client>
	</nav>
</template>

<script>
	import UpdatePassword from '../publicsubsystem/user/changePassword'
	import Profile from '../publicsubsystem/user/profile'
	import StompClient from './stompClient'

	import {
		mapMutations,
		mapState,
	} from 'vuex'
	export default {
		data() {
			return {
				appName: window.SITE_CONFIG['appName'],
				appShortName: window.SITE_CONFIG['appShortName'],
				updatePassowrdVisible: false,
				profileVisible: false,
			}
		},
		components: {
			UpdatePassword,
			Profile,
			StompClient,
		},
		created() {

		},
		computed: {
			navbarClasses() {
				let type = this.$store.state.ui.navbarLayoutType
				return [!/\S/.test(type) || type === 'default' ? '' : `site-navbar--${type}`]
			},
			...mapState({
				path: state => state.route.path,
				user: state => state.app.user,
				notificationNum: state => state.ui.notificationNum,
				isLoading: state => state.ui.isLoading,
			}),
		},
		mounted() {},
		methods: {
			// 切换侧边栏, 水平折叠收起状态
			switchSidebarCollapseHandle() {
				this.SWITCH_SIDEBAR_COLLAPSE({
					collapse: !this.$store.state.ui.sidebarCollapse
				})
			},

			// 修改密码
			updatePasswordHandle() {
				this.updatePassowrdVisible = true
				this.$nextTick(() => {
					this.$refs.updatePassowrd.init()
				})
			},
			profileHandle() {
				this.profileVisible = true
				this.$nextTick(() => {
					this.$refs.profile.init()
				})
			},
			// 任务授权
			setTaskAgentHandle() {
				this.$router.push({
					name: 'wf_taskagent'
				})
			},
			settingHandle() {
				this.$router.push({
					name: 'gl_setting'
				})
			},
			notificationHandle() {
				this.$router.push({
					name: 'gl_notification'
				})
			},
			// 退出
			logoutHandle() {
				this.$router.push({
					name: 'loginout'
				})
			},
			...mapMutations(['SWITCH_SIDEBAR_COLLAPSE', 'DELETE_CONTENT_TABS'])
		},
	}
</script>
<style lang="scss" scoped>
	.site-navbar__brand-lg {
		font-size: 21px;
		margin: 25px 0 0 4px;
	}
	.site-navbar__brand {
		line-height: 70px;
		height: 70px;
		width: 450px;
		display: flex;
		align-items: center;
	}
	.site-navbar__header {
		width: 500px;
		height: 70px;
		z-index: 10;
	}
	.div-loading {
		width: 200px;
		height: 50px;

		position: absolute;
		left: 50%;
		top: 50%;
		margin-left: -100px;
		margin-top: -25px;
	}

	.bacolo {
		&:before {
			position: fixed;
			top: 0;
			left: 200px;
			z-index: 7;
			width: 1200px;
			height: 82.5px;
			content: "";
			background-size: cover;
		}

	}
	.logo {
		width: 60px;
		height: 60px;
		margin-left: 25px;
		img {
			width: 60px;
		}
	}
	.jianzhu {
		width: 90%;
		height: 82.5px;
		z-index: 1;
		position: absolute;
		// left: 17%;
		margin: 0 0% 0 10%;
		background-image: url("~@/assets/img/back.png");
    background-size: contain;
		background-repeat-y: no-repeat;
	}

	.div-loading-inner {
		margin-left: 50px;
		margin-top: 18px;
	}
	.site-sidebar__menu-icon {
		width: 30px;
		height: 33px;
		color: #fff !important;
	}
	#base_notification:hover, #base_user:hover {
		background: transparent;
	}
</style>
