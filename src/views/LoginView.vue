<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const studentId = ref('')
const password = ref('')
const showPassword = ref(false)
const loginError = ref('')

const togglePasswordVisibility = () => {
  showPassword.value = !showPassword.value
}

const handleLogin = () => {
  // 这里模拟登录验证
  // 验证学号是否为8位纯数字
  const studentIdRegex = /^\d{8}$/
  if (!studentIdRegex.test(studentId.value)) {
    loginError.value = '请输入8位纯数字学号'
    return
  }

  if (studentId.value && password.value) {
    loginError.value = ''
    // 登录成功后跳转到管理员页面
    router.push('/event')
  } else {
    loginError.value = '请输入学号和密码'
  }
}

const handleForgotPassword = () => {
  // 跳转到密码找回页面（暂时简单提示）
  alert('密码找回功能暂未实现')
}
</script>

<template>
  <div class="login-container">
    <div class="login-header">
      <h1>赛事数据采集系统</h1>
      <p>采集员端</p>
    </div>

    <div class="login-form">
      <div class="form-group">
        <label for="studentId">学号</label>
        <input
          id="studentId"
          v-model="studentId"
          type="text"
          placeholder="请输入8位纯数字学号"
          class="form-input"
        />
      </div>

      <div class="form-group">
        <label for="password">密码</label>
        <div class="password-input-container">
          <input
            id="password"
            v-model="password"
            :type="showPassword ? 'text' : 'password'"
            placeholder="请输入密码"
            class="form-input"
          />
          <button type="button" class="password-toggle" @click="togglePasswordVisibility">
            {{ showPassword ? '隐藏' : '显示' }}
          </button>
        </div>
      </div>

      <div v-if="loginError" class="error-message">
        {{ loginError }}
      </div>

      <button type="button" class="login-button" @click="handleLogin">登录</button>

      <div class="login-footer">
        <button type="button" class="forgot-password" @click="handleForgotPassword">
          忘记密码？
        </button>
        <p class="auth-hint">仅授权采集员可登录</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.login-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 20px;
  background-color: #f5f5f5;
}

.login-header {
  text-align: center;
  margin-bottom: 40px;
}

.login-header h1 {
  font-size: 24px;
  color: #333;
  margin-bottom: 8px;
}

.login-header p {
  font-size: 16px;
  color: #666;
}

.login-form {
  width: 100%;
  max-width: 400px;
  background-color: #fff;
  border-radius: 12px;
  padding: 30px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-size: 14px;
  color: #333;
  font-weight: 500;
}

.form-input {
  width: 100%;
  height: 44px;
  padding: 0 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  box-sizing: border-box;
}

.form-input:focus {
  outline: none;
  border-color: #1890ff;
  box-shadow: 0 0 0 2px rgba(24, 144, 255, 0.2);
}

.password-input-container {
  position: relative;
}

.password-toggle {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  color: #1890ff;
  font-size: 14px;
  padding: 4px 8px;
  cursor: pointer;
}

.error-message {
  color: #f5222d;
  font-size: 14px;
  margin-bottom: 16px;
}

.login-button {
  width: 100%;
  height: 44px;
  background-color: #1890ff;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 0.3s;
}

.login-button:hover {
  background-color: #40a9ff;
}

.login-footer {
  margin-top: 20px;
  text-align: center;
}

.forgot-password {
  background: none;
  border: none;
  color: #1890ff;
  font-size: 14px;
  cursor: pointer;
  margin-bottom: 12px;
}

.auth-hint {
  font-size: 12px;
  color: #999;
  margin: 0;
}

/* 移动端适配 */
@media (max-width: 768px) {
  .login-form {
    padding: 20px;
  }

  .login-header h1 {
    font-size: 20px;
  }
}
</style>
