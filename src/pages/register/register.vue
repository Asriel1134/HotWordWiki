<template>
	<view class="VBox">
		<view class="status_bar"></view>

		<view class="register">
			<text class="registerTitle">注册</text>

			<form class="registerForm" @submit="bindRegister">
				<view class="input-view">
					<input class="input mailInput" type="text" placeholder="请输入邮箱" name="mailValue" />
				</view>
				<view class="input-view">
					<input class="input usernameInput" placeholder="请输入用户名" name="usernameValue" />
				</view>
				<view class="input-view">
					<input class="input passwordInput" password placeholder="请输入密码" name="passwordValue" />
				</view>
				<view class="input-view">
					<input class="input passwordInput" password placeholder="请再次输入密码" name="passwordRepeatValue" />
				</view>
				<view class="button-view">
					<button type="primary" :loading="loading" class="registerbutton" formType="submit">注册</button>
				</view>
			</form>
		</view>
	</view>
</template>

<script>
	import {
        mapMutations,
    } from 'vuex';

	export default {
		data() {
			return {
				loading: false
			}
		},
		methods: {
			bindRegister(e) {
				this.loading = true;
				let mail = e.detail.value.mailValue,
                    password = e.detail.value.passwordValue,
					passwordRepeat = e.detail.value.passwordRepeatValue,
					username = e.detail.value.usernameValue;

				if (!/^[a-zA-Z0-9_.-]+@[a-zA-Z0-9-]+(\.[a-zA-Z0-9-]+)*\.[a-zA-Z0-9]{2,6}$/.test(mail)) {
                    this.loading = false;
					plus.nativeUI.toast("请输入正确的邮箱成功")
                    return;
                }

				if (!/^[A-Za-z0-9\u4E00-\u9FA5]{1,10}$/.test(username)) {
                    this.loading = false;
					plus.nativeUI.toast("请输入长度不大于10的用户名，不包含特殊字符")
                    return;
                }

				if (!/^[A-Za-z0-9_!/*-@#]{6,16}$/.test(password)) {
                    this.loading = false;
					plus.nativeUI.toast("请输入6-16位密码，可包含大小写字母、数字及特殊字符_!/*-@#")
                    return;
                }

				if (!(passwordRepeat == password)) {
                    this.loading = false;
					plus.nativeUI.toast("两次输入的密码不同")
                    return;
                }

				uni.request({
                    url: `${this.$serverUrl}/adduser`,
                    header: {
                        "Content-Type": "application/x-www-form-urlencoded"
                    },
                    data: {
                        "mail": mail,
						"username": username,
                        "password": password
                    },
                    method: "POST",
                    success: (e) => {
                        if (e.data.result == -1) {
							plus.nativeUI.toast("邮箱已注册，请直接登录")
                            return;
                        }
                        if (e.data.result == 0) {
                            this.login(e.data.userinfo);
                            uni.navigateBack();
                        } else {
							plus.nativeUI.toast("error" + e.data.info)
                        }
                    },
                    fail: (e) => {
						plus.nativeUI.toast("请求失败，请重试！")
                    },
                    complete: () => {
                        this.loading = false;
                    }
                })
			},
			...mapMutations(['login'])
		}
	}
</script>

<style>
	.registerbutton{
		margin-top: 25px;
		margin-bottom: 30px;
		border-radius: 9px;
		width: 80%;
		/* height: 15px; */
	}
	.usernameInput{
		background-image: url("/static/icon/username.png");
	}
	.mailInput{
		background-image: url("/static/icon/mail.png");
	}
	.passwordInput{
		background-image: url("/static/icon/password.png");
	}
	.input{
		background-color: rgba(168, 168, 168, 0.22);
		border-radius: 9px;
		margin-bottom: 8px;
		height: 40px;
		width: 100%;

		display: flex;
		text-indent: 43px;
		color: rgba(0, 0, 0, 0.733);
		font-size: 10px;
		
		background-repeat: no-repeat;
		background-size: 20px;
		background-position: 12px 10px;
	}
	.label{
		font-size: 18px;
		font-weight: 500;
	}
	.input-view{
		margin-bottom: 10px;
		display: flex;
		flex-direction: row;
		align-items: center;
	}
	.registerForm{
		display: flex;
        flex: 1;
        flex-direction: column;
        width: 520upx;
        padding-top: 30upx;
	}


	.registerTitle{
		margin-top: 15px;
		margin-bottom: 20px;
		font-size: 22px;
		font-weight: 550;
	}
	.register{
		padding: 5px 0;
		width: 90%;
		height: 420px;
		background-color: #ffffff;
		border-radius: 9px;
		margin-top: 40px;
		box-shadow: 0px 1px 3px rgba(158, 158, 158, 0.733);
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.VBox{
		background: linear-gradient(#6d91f2, #6d91f2, #6d90f200);;
		width: 100%;
		height: 250px;
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.status_bar {
		height: 5px;
		width: 100%;
	}
</style>
