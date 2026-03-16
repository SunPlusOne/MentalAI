<template>
    <div class="container">
        <div class="title">
            <div class="back-home">
                <el-icon><Back /></el-icon>
                <span>返回首页</span>
            </div>
            <div class="title-text">
                <h2>登录</h2>
                <p>请输入您的账号和密码进行登录</p>
            </div>
        </div>
        <div class="form-container">
            <el-form
                ref="ruleFormRef"
                :model="formData"
                :rules="rules"
                label-position="top"
            >
                <el-form-item label="用户名或邮箱" prop="username">
                    <el-input v-model="formData.username" size="large" placeholder="请输入用户名" />
                </el-form-item>
                <el-form-item label="密码" prop="password">
                    <el-input v-model="formData.password" size="large" type="password" placeholder="请输入密码" show-password />
                </el-form-item>
                <el-form-item>
                    <el-button class="btn" size="large" type="primary" @click="submitForm(ruleFormRef)">登录</el-button>
                </el-form-item>
            </el-form>
            <div class="footer">
                <p>还没有账号？<router-link to="/auth/register">去注册</router-link></p>
            </div>
        </div>
    </div>
</template>
<script setup>
import { reactive, ref } from 'vue';
import { ElMessage } from 'element-plus';
import { login } from '@/api/admin';
import { useRouter } from 'vue-router';

const router = useRouter();

const ruleFormRef = ref()

const formData = reactive({
    username: '',
    password: ''
})

const rules = reactive({
    username: [
        { required: true, message: '请输入用户名', trigger: 'blur' }
    ],
    password: [
        { required: true, message: '请输入密码', trigger: 'blur' }
    ]
})

//提交表单
const submitForm = async (formEL) => {
    if (!formEL) return
    const valid = await formEL.validate().catch(() => false)
    if (!valid) return

    try {
        const data = await login(formData)
        if (!data?.token) {
            ElMessage.error('登录接口返回格式异常，未找到 token')
            return
        }

        //登录成功，保存token和用户信息到localStorage
        localStorage.setItem('token', data.token)
        localStorage.setItem('userInfo', JSON.stringify(data.userInfo || {}))
        ElMessage.success('登录成功')
        //根据用户角色跳转到不同页面
        if (data.userInfo.userType === 2) {
            router.push('/back/dashboard')
        } else {}
            
    } catch (error) {
        console.error('登录失败', error)
    }
}

const handleLogin = () => {
    submitForm(ruleFormRef.value)
}
</script>
<style lang="scss" scoped>
.container {
    width: 384px;
    .title {
        .back-home {
            margin-bottom: 60px;
        }
        .title-text {
            text-align: center;
            h2 {
                font-size: 36px;
                margin-bottom: 10px;
            }
            p {
                font-size: 14px;
                color: #666;
            }
        }
    }
    .form-container {
        margin-top: 30px;
        .btn {
            width: 100%;
            margin-top: 40px;
        }
        .footer {
            padding: 30px;
            text-align: center;
        }
    }
}
</style>